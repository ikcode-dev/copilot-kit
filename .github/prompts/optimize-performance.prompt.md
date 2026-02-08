---
name: optimize-performance
description: "Identify and fix performance issues using pattern recognition and language-idiomatic best practices"
agent: agent
argument-hint: "Optional: specify performance target (latency, throughput, memory) or constraints"
tools: ['execute/testFailure', 'execute/getTerminalOutput', 'execute/awaitTerminal', 'execute/runInTerminal', 'execute/runTests', 'read/problems', 'read/readFile', 'read/terminalSelection', 'read/terminalLastCommand', 'agent', 'edit/createDirectory', 'edit/createFile', 'edit/editFiles', 'edit/editNotebook', 'search', 'web/fetch', 'sequentialthinking/*', 'sequential-thinking/*', 'todo']
---

## Goal

Systematically analyze code for performance issues using pattern recognition and static analysis. Identify known anti-patterns, algorithmic inefficiencies, and opportunities for language-idiomatic optimizations. Suggests improvements following best practices for the detected language/framework, and implements changes only after user approval. Explicitly identifies breaking vs. non-breaking changes and validates that optimizations improve performance through testing.

## Inputs & Context Gathering

- Accept target code from `${selection}`, `${file}`, or `${input:target:Describe performance goals or specify target code (optional)}`.
- **Optional profiling data**: If user provides profiling output, benchmark results, or performance metrics as context, prioritize optimizations based on that data.
- **Proactively gather project context**:
  - Language/framework detection: file extensions, manifests (package.json, Cargo.toml, go.mod, requirements.txt, etc.)
  - Performance constraints: `README.md`, `CONTRIBUTING.md`, `.github/**/*`, `docs/**/*`, SLA documentation
  - Existing benchmarks/tests: performance test files, benchmark configurations, CI/CD performance gates
  - Runtime environment: deployment target (server, browser, mobile, embedded), resource constraints
  - Dependencies: framework versions, available performance libraries, optimization tools
  - Architecture patterns: caching strategies, database query patterns, async/concurrency usage
- **Determine optimization scope**:
  - If user specifies targets (e.g., "reduce latency," "optimize memory") → focus on those metrics
  - If profiling data provided → prioritize issues identified in profiling results
  - If no target specified → perform comprehensive static analysis for known anti-patterns
  - If unclear → ask about performance goals, acceptable tradeoffs, and critical constraints

## Performance Optimization Protocol

### 1. Initial Performance Assessment

Use sequential thinking (MCP tool: `sequentialthinking`) for complex performance analysis:
- **Understand the code's purpose**: What does this code do? What's its expected usage pattern?
- **Identify performance context**: Is this hot path or cold path? What's the likely execution frequency?
- **Detect language/framework**: What language is this? What are the idiomatic performance patterns?
- **Check for profiling data**: Did user provide profiling output, benchmarks, or metrics?
- **Analyze code structure**: What algorithms, data structures, and patterns are used?

**Critical decision point**: Does this code have a performance problem?

#### If performance is acceptable:
```
✅ **No optimization needed**

Code follows performance best practices:
- [Observation 1: e.g., using efficient algorithms (O(n log n))]
- [Observation 2: e.g., proper data structures for the use case]
- [Observation 3: e.g., idiomatic patterns for the language/framework]

**Why optimization would be premature or counterproductive**:
- [Reason 1: e.g., not a hot path based on code structure]
- [Reason 2: e.g., algorithmic complexity already optimal]
- [Reason 3: e.g., further optimization would sacrifice readability without clear benefit]

**Recommendation**: [If performance becomes a concern, suggest profiling to identify actual bottlenecks]
```
Stop here. Don't optimize code that doesn't need it.

#### If optimization is warranted:
Proceed to step 2.

### 2. Language-Idiomatic Performance Patterns

Detect language and identify common performance anti-patterns and optimization opportunities:

#### **JavaScript/TypeScript** (Node.js/Browser)
- **Common anti-patterns**: Event loop blocking, inefficient object creation, closure overhead, DOM manipulation in loops, excessive re-renders, using `delete` operator, unnecessary `forEach` in hot paths
- **Optimization patterns**: Memoization, debouncing/throttling, virtual scrolling, Web Workers, lazy loading, tree shaking, `Object.freeze` for immutability, `WeakMap` for metadata, `for-of` over `forEach` in hot loops
- **Static checks**: Look for synchronous I/O, missing pagination, unbounded loops, inefficient string concatenation

#### **Python**
- **Common anti-patterns**: String concatenation with `+` in loops, list comprehensions when generators suffice, global variable lookups, unnecessary object copies, missing `__slots__`, quadratic nested loops
- **Optimization patterns**: Use built-ins (`map`, `filter`, `any`, `all`), `''.join()` for strings, generators for large datasets, NumPy/Pandas for numerical data, `functools.lru_cache`, `__slots__` for classes, list/dict comprehensions
- **Static checks**: Look for N+1 database queries, missing indexes on iterations, inefficient data structures (list search instead of set/dict)

#### **Go**
- **Common anti-patterns**: String concatenation with `+`, not preallocating slices, `defer` in tight loops, unnecessary interface{} usage, missing buffer reuse, goroutine leaks (no context cancellation)
- **Optimization patterns**: `strings.Builder` for string building, `make([]T, 0, capacity)` for preallocation, `sync.Pool` for object reuse, value receivers for small types, avoid `interface{}` in hot paths
- **Static checks**: Look for slice growth in loops, missing capacity hints, unnecessary heap allocations, inefficient map operations

#### **Rust**
- **Common anti-patterns**: Unnecessary `.clone()` calls, missing `Vec::with_capacity`, using `Box`/`Rc` when stack allocation works, inefficient iterator chains, dynamic dispatch (`dyn Trait`) in hot paths
- **Optimization patterns**: Borrowing over cloning, `Vec::with_capacity` for known sizes, `Cow` for conditional ownership, iterator adapters (`filter`, `map`, `fold`), `#[inline]` for small functions, zero-copy parsing
- **Static checks**: Look for `.clone()` that could be borrows, missing capacity hints, unnecessary allocations, suboptimal iterator usage

#### **Java/Kotlin**
- **Common anti-patterns**: Autoboxing in loops, using `LinkedList` instead of `ArrayList`, inefficient string concatenation, excessive reflection, unnecessary synchronization, creating streams for small collections
- **Optimization patterns**: Primitive arrays for numerical data, `StringBuilder` for string building, `ArrayList` by default, `EnumSet`/`EnumMap` for enums, stream optimization, parallel streams for large datasets
- **Static checks**: Look for boxing/unboxing, inefficient collection usage, string concatenation with `+`, unnecessary object creation

#### **C/C++**
- **Common anti-patterns**: Unnecessary heap allocations, missing move semantics, copying large objects, inefficient memory layout, virtual calls in tight loops, missing `const` and `constexpr`
- **Optimization patterns**: Move semantics (`std::move`), `reserve()` for vectors, `emplace_back` over `push_back`, RAII, `constexpr` for compile-time computation, value semantics for small types
- **Static checks**: Look for unnecessary copies, missing moves, heap allocations that could be stack, inefficient container operations

#### **C#/.NET**
- **Common anti-patterns**: Boxing value types, excessive LINQ on hot paths, string concatenation with `+`, misusing async/await (async void, unnecessary Task), creating arrays for temporary data
- **Optimization patterns**: `Span<T>` for memory slicing, `ArrayPool` for buffer reuse, `ValueTask` for hot async paths, `struct` for small value types, `stackalloc` for small buffers, `StringBuilder`
- **Static checks**: Look for boxing, LINQ in loops, string allocations, unnecessary async, array allocations that could use pooling

#### **Ruby**
- **Common anti-patterns**: String allocations (not freezing strings), inefficient Hash lookups, using `#map` for side effects only, excessive object creation, missing memoization
- **Optimization patterns**: Frozen string literals, symbol keys for hashes, `#fetch` with default block, `#each` vs `#map` appropriately, precompute constants, memoization with `||=`
- **Static checks**: Look for unfrozen strings in loops, `#map` without using results, inefficient enumerable methods

**If language not listed**: Research language-specific performance patterns and common anti-patterns before proceeding.

### 3. Static Analysis & Pattern Recognition

Analyze code structure and identify optimization opportunities:
1. **Use profiling data if available**: Prioritize issues identified in user-provided profiling output or benchmarks
2. **Analyze algorithmic complexity**: What's the Big O? Can we use better algorithms/data structures? Look for O(n²) or worse in loops
3. **Identify anti-patterns**: Match code against language-specific anti-patterns from section 2
4. **Check I/O patterns**: Unnecessary network/disk I/O? Missing batching/caching? N+1 query problems?
5. **Inspect memory patterns**: Excessive allocations visible in code? Unnecessary object creation? Missing object reuse?
6. **Review concurrency usage**: Blocking operations? Missing async/parallelization opportunities? Obvious race conditions?

**Common performance anti-patterns**:
- **Premature pessimization**: Using slow approach when fast one is equally simple
- **N+1 queries**: Database/API calls in loops
- **Unbounded operations**: No pagination, limit, or timeout
- **Inefficient data structures**: Array search when hash map is better
- **Repeated computation**: Same calculation multiple times without caching
- **Synchronous blocking**: Not using async/concurrency when beneficial
- **Large payloads**: Transferring/parsing more data than needed
- **Poor cache locality**: Random memory access patterns

### 4. Optimization Strategy Design

Prioritize optimizations by **impact × likelihood × safety**:

#### **High-Impact, Low-Risk Optimizations** (Do first)
- Algorithm/data structure improvements (O(n²) → O(n log n))
- Removing redundant work (duplicate calculations, unnecessary allocations)
- Adding caching for expensive pure functions
- Database query optimization (indexes, query consolidation, connection pooling)
- Using language built-ins over manual implementations
- Preallocation of collections with known size

#### **Medium-Impact, Medium-Risk Optimizations** (Do with testing)
- Concurrency/parallelization (verify thread safety, avoid race conditions)
- Lazy loading/eager loading tradeoffs
- Memory layout optimization (struct packing, cache alignment)
- Micro-optimizations in hot loops (loop unrolling, reduced branching)

#### **High-Impact, High-Risk Optimizations** (Do only if necessary)
- Breaking API changes (signature changes, return type changes)
- Architecture changes (sync → async, blocking → non-blocking)
- Unsafe code (manual memory management, unchecked operations)
- Platform-specific optimizations (SIMD, assembly, compiler intrinsics)

**Tradeoff considerations**:
- **Performance vs. Readability**: Prefer clear code unless profiling justifies complexity
- **Performance vs. Maintainability**: Avoid clever optimizations that obscure intent
- **Performance vs. Portability**: Document platform-specific assumptions
- **Performance vs. Safety**: Never sacrifice correctness for speed
- **Time complexity vs. Space complexity**: Consider memory constraints
- **Optimization effort vs. Actual gain**: Focus on bottlenecks, not marginal improvements

### 5. Breaking Change Analysis

Before implementing optimizations, classify changes:

#### **Non-Breaking Changes** ✅ (Safe to implement)
- Internal algorithm changes maintaining same output
- Adding optional parameters with defaults
- Performance improvements without API changes
- Adding caching/memoization transparently
- Optimizing internal data structures

#### **Breaking Changes** ⚠️ (Require explicit approval)
- **Signature changes**: Parameter types, return types, parameter order
- **Behavior changes**: Different output format, error handling, side effects
- **Semantic changes**: Async → sync or vice versa, blocking behavior
- **Contract changes**: Precondition/postcondition modifications
- **Dependency changes**: Minimum version bumps, removed dependencies

**For breaking changes, document**:
- **What breaks**: Specific API contracts that change
- **Migration path**: How users update their code
- **Alternatives**: Can we achieve performance without breaking changes?
- **Justification**: Is the performance gain worth the disruption?

### 6. Implementation Plan

**Before writing code**:
1. **Document optimization rationale**: "By doing X, we address Y anti-pattern and expect improvement in Z"
2. **Suggest benchmarking**: Recommend creating/running benchmarks to validate the improvement
3. **Present plan to user**: Show proposed changes, breaking/non-breaking classification, expected impact
4. **Get explicit approval**: Especially for breaking changes or complex optimizations

**Implementation principles**:
- **One optimization at a time**: Easier to measure impact and rollback if needed
- **Preserve correctness**: All tests must pass (or be updated appropriately)
- **Maintain code quality**: Optimized code should still be readable and maintainable
- **Add comments**: Explain non-obvious optimizations (why, not just what)
- **Follow language idioms**: Use the idiomatic "fast path" for the language
- **Keep escape hatches**: Allow users to opt into slower but safer behavior if needed

### 7. Validation & Testing

After implementing optimizations:
1. **Run existing tests**: Verify correctness - all tests pass, behavior unchanged (or intentionally changed)
2. **Run/create benchmarks**: If benchmarks exist, compare before/after. If not, suggest creating them to validate improvement
3. **Check edge cases**: Test with various input sizes (small, medium, large), error conditions
4. **Verify algorithmic improvement**: Confirm the complexity change (e.g., O(n²) → O(n log n)) is reflected in practice
5. **Suggest performance validation**: Recommend how to measure the improvement in the target environment

**Success criteria**:
- All tests passing (correctness preserved)
- Code quality maintained or improved
- Anti-patterns eliminated
- Breaking changes documented and approved
- Benchmarks show improvement (if run), or clear recommendation for benchmarking provided

## Expected Output Format

### Phase 1: Analysis & Proposal (Before Implementation)

```markdown
## Performance Analysis

**Language/Framework**: [Detected language and version]

**Analysis Method**: [Static code analysis / Pattern recognition / Based on provided profiling data]

**Profiling Data** (if provided by user):
- [Metric from profiling: e.g., Function X takes 450ms (65% of total time)]
- [Or: "No profiling data provided - analysis based on code patterns"]

**Identified Issues**:
1. **[Issue 1]** (`file.ext:L42-L58`) - [Severity: Critical/High/Medium/Low]
   - **Anti-pattern**: [Specific anti-pattern, e.g., "O(n²) nested loop for deduplication"]
   - **Evidence**: [Code structure analysis or profiling data if available]
   - **Impact**: [Expected performance impact based on algorithmic analysis]

2. **[Issue 2]** (`file.ext:L89`) - [Severity]
   - **Anti-pattern**: [Specific issue]
   - **Evidence**: [Code analysis or profiling data]
   - **Impact**: [Expected impact]

## Proposed Optimizations

### Optimization 1: [Name, e.g., "Replace nested loop with HashSet"]
**Location**: `file.ext:L42-L58`

**Change Type**: ✅ Non-Breaking | ⚠️ Breaking

**Approach**: [Detailed explanation of the optimization]

**Expected Improvement**: [Estimated improvement based on algorithmic analysis, e.g., "Reduces complexity from O(n²) to O(n), significant improvement for large inputs (n > 1000)"]

**Tradeoffs**:
- ✅ Pro: [Benefit]
- ✅ Pro: [Benefit]
- ⚠️ Con: [Drawback, e.g., "Increased memory usage by O(n)"]

**Code Preview**:
```[language]
// Before (simplified)
[relevant original code snippet]

// After (proposed)
[optimized code snippet]
```

### Optimization 2: [Name]
[Repeat structure]

---

## Breaking Changes Summary

[If any breaking changes exist, list them clearly:]

| Change | Location | Impact | Migration |
|--------|----------|--------|-----------|
| [Change description] | `file:line` | [What breaks] | [How to fix] |

**OR**

✅ **All proposed optimizations are non-breaking changes.**

---

## Recommendation

**Proceed with optimizations?**

- **Low-risk changes** (non-breaking): [List them]
- **High-risk changes** (breaking or complex): [List them]
- **Suggested approach**: [Recommend which to implement first, or if all should be applied]

**Awaiting your approval to implement these changes.**
```

### Phase 2: Implementation Results (After Approval & Implementation)

```markdown
## Performance Optimization Results

### Changes Implemented

**[Optimization 1 Name]** (`file.ext:L42-L58`)
- ✅ Non-Breaking | ⚠️ Breaking
- [Brief description of what was changed]

**[Optimization 2 Name]** (`file.ext:L89-L95`)
- ✅ Non-Breaking | ⚠️ Breaking
- [Brief description of what was changed]

### Performance Improvements

**Benchmarks** (if run):

| Metric | Before | After | Improvement |
|--------|--------|-------|-------------|
| [Metric 1] | [Value] | [Value] | [% improvement] ✅ |
| [Metric 2] | [Value] | [Value] | [% improvement] ✅ |

**OR if benchmarks not run:**

**Expected Improvements**:
- [Algorithmic improvement 1: e.g., Reduced complexity from O(n²) to O(n)]
- [Anti-pattern fixed 1: e.g., Eliminated N+1 query problem]
- [Language-specific improvement: e.g., Replaced string concatenation in loop with StringBuilder]

**Validation Method**: [Describe how improvement was verified: existing tests, new benchmarks created, or recommended benchmarking approach]

### Code Changes Explained

#### [Optimization 1: Detailed Explanation]
**Before**:
```[language]
[Original code with context]
```

**After**:
```[language]
[Optimized code with context]
```

**Why This Improves Performance**:
- [Reason 1: Algorithmic improvement, reduced complexity from O(n²) to O(n)]
- [Reason 2: Eliminated known anti-pattern (e.g., unnecessary allocations)]
- [Reason 3: Uses language-idiomatic pattern (e.g., "Leverages Go's map implementation which is optimized for this use case")]
- [Reason 4: Expected impact based on code analysis]

#### [Optimization 2: Detailed Explanation]
[Repeat structure]

### Test Results

- ✅ All existing tests pass
- ✅ Edge cases verified (empty input, large input, error conditions)
- ✅ Performance benchmarks [run and show improvement / created for future validation / recommended]
- [Additional test notes if relevant]

### Breaking Changes Documentation

[If breaking changes were approved and implemented:]

#### Migration Guide

**Change**: [What changed]

**Before** (old API):
```[language]
[Old usage example]
```

**After** (new API):
```[language]
[New usage example]
```

**Why**: [Performance justification]

---

**OR if no breaking changes:**

✅ **No breaking changes.** All optimizations maintain backward compatibility.

---

### Follow-up Recommendations

- [Optional: Next optimization opportunities, e.g., "Consider adding caching layer for database queries"]
- [Optional: Monitoring suggestions, e.g., "Add metrics for endpoint latency to track performance in production"]
- [Optional: Further profiling, e.g., "Profile under production load to identify additional bottlenecks"]
```

### If no optimization is needed:

```markdown
✅ **Code follows performance best practices**

**Analysis Results**:
- [Finding 1: e.g., Using optimal algorithm for the use case (O(n log n) for sorting)]
- [Finding 2: e.g., Proper data structures (HashSet for lookups)]
- [Finding 3: e.g., Language-idiomatic patterns applied]

**What was checked**:
- [Check 1: Algorithmic complexity analysis]
- [Check 2: Language-specific anti-patterns]
- [Check 3: Memory allocation patterns]

**Why optimization would be premature or counterproductive**:
- [Reason 1: e.g., "Already using optimal algorithm"]
- [Reason 2: e.g., "No obvious anti-patterns detected"]
- [Reason 3: e.g., "Further optimization would sacrifice readability without clear benefit"]

**Next Steps** (if performance becomes a concern):
- [Recommendation: e.g., "Profile with realistic workload to identify actual bottlenecks"]
- [Recommendation: e.g., "Add benchmarks to track performance over time"]
```

## Guidance

### When to Optimize
✅ **DO optimize when**:
- Code uses known anti-patterns (e.g., N+1 queries, O(n²) algorithms on unbounded input, language-specific anti-patterns)
- Profiling data (if provided) shows clear bottlenecks
- Performance fails to meet documented SLAs or user expectations
- User explicitly requests optimization with clear targets
- Algorithmic complexity is clearly suboptimal for the use case
- Language-idiomatic patterns are not being used

❌ **DON'T optimize when**:
- No obvious anti-patterns or inefficiencies detected
- Algorithmic complexity is already optimal
- Code follows language-idiomatic best practices
- Optimization would sacrifice correctness or security
- Effort outweighs benefit (micro-optimizations for cold paths without data)
- The likely bottleneck is external (network, database, third-party API) based on code structure

### Quality Standards
- **Pattern-based**: Identify and eliminate known anti-patterns and inefficiencies
- **Idiomatic**: Use language-specific best practices and optimization patterns
- **Algorithmic**: Prioritize algorithmic improvements (better Big O) over micro-optimizations
- **Safe**: Preserve correctness, maintain test coverage
- **Pragmatic**: Focus on issues with clear impact, avoid premature optimization
- **Documented**: Explain why optimizations work and what anti-patterns were addressed
- **Testable**: Ensure changes can be validated through tests and benchmarks

### Edge Cases
- **Micro-benchmarks unreliable**: Test with realistic workloads and data
- **Different performance on different platforms**: Profile on target environment
- **Performance regression in edge cases**: Test with various input sizes and types
- **Concurrency issues**: Verify thread safety, avoid race conditions
- **Resource constraints**: Consider memory-limited, CPU-limited environments separately

### Collaboration Cues
- **Uncertain about impact?**: Recommend profiling or benchmarking to validate the optimization
- **Multiple optimization approaches?**: Present tradeoffs with pros/cons
- **Breaking changes necessary?**: Explain why and provide migration path
- **User's performance goals unclear?**: Ask about SLAs, acceptable latency, throughput targets, or typical workload
- **No obvious anti-patterns?**: State findings clearly and suggest profiling if performance is still a concern
- **Optimization implemented?**: Recommend benchmarking to measure actual impact

### Anti-Patterns to Avoid
- **Premature optimization**: Optimizing code that doesn't have clear issues or anti-patterns
- **Micro-optimization**: Focusing on insignificant gains when algorithmic improvements are available
- **Clever code**: Sacrificing readability for marginal performance without data to support it
- **Ignoring algorithmic complexity**: Focusing on low-level optimizations when algorithm is suboptimal
- **Assuming impact**: Claiming specific performance gains without benchmarking data
- **Breaking correctness**: Trading bugs for speed
- **Over-engineering**: Adding complexity without clear benefit

## Advanced Techniques

### Language-Specific Performance Patterns

**When optimizing, leverage these idiomatic patterns**:

- **JavaScript/TypeScript**: `Object.freeze` for immutability, `WeakMap` for metadata, avoid `delete` operator, use `for-of` over `forEach` in hot loops
- **Python**: List/dict comprehensions, `itertools`, `functools.lru_cache`, `__slots__`, NumPy for numerical ops
- **Go**: Preallocate slices with `make([]T, 0, capacity)`, use `strings.Builder`, prefer value receivers for small types, avoid `interface{}` in hot paths
- **Rust**: Iterator adapters (`filter`, `map`, `fold`), `Vec::with_capacity`, avoid `Box`/`Rc` when stack allocation works, use `#[inline]` judiciously
- **Java**: `ArrayList` over `LinkedList` by default, `EnumSet`/`EnumMap` for enums, `StringBuilder`, primitive streams
- **C++**: Move semantics, `reserve()` for vectors, `emplace_back` over `push_back`, RAII, `constexpr`

### Benchmarking Recommendations
When recommending benchmarks to validate optimizations:
1. **Realistic workloads**: Test with production-like data and scenarios
2. **Warm up period**: Account for JIT compilation, cache warming (especially for JVM, .NET)
3. **Multiple iterations**: Run benchmarks multiple times for statistical significance
4. **Isolate variables**: Change one optimization at a time to measure impact
5. **Target environment**: Benchmark in production-like environment when possible
6. **Benchmark tools**: Suggest language-appropriate tools (e.g., Criterion for Rust, JMH for Java, pytest-benchmark for Python)

### Memory Optimization Strategies
- **Reduce allocations**: Preallocate, reuse objects, object pooling
- **Optimize data layout**: Struct packing, cache-friendly access patterns
- **Use value types**: Avoid heap allocations for small types (stack allocation)
- **Lazy loading**: Defer expensive initialization until needed
- **Streaming**: Process data incrementally instead of loading all at once

### Database/I/O Optimization
- **Query optimization**: Indexes, query consolidation, pagination
- **Connection pooling**: Reuse database connections
- **Batching**: Combine multiple operations
- **Caching**: Memoize expensive queries
- **Async I/O**: Non-blocking operations for I/O-bound code

## Meta-Validation

Before delivering optimization results:
- [ ] Identified issues are based on recognized anti-patterns or profiling data (if provided)
- [ ] Language-idiomatic patterns and best practices were applied
- [ ] Algorithmic improvements are sound (Big O analysis is correct)
- [ ] Tests pass (correctness preserved)
- [ ] Benchmarks run (if available) or benchmarking approach recommended
- [ ] Breaking changes are clearly documented
- [ ] User approval obtained before implementing breaking changes
- [ ] Code quality maintained (readable, maintainable optimizations)
- [ ] Edge cases tested (various input sizes, error conditions)
- [ ] Optimization aligns with user's stated or implied performance goals

---

**Philosophy**: Focus on eliminating known anti-patterns and algorithmic inefficiencies first. The best optimizations are based on sound analysis, respect language idioms, maintain code clarity, and can be validated through testing. When profiling data is available, use it to prioritize; otherwise, rely on pattern recognition and complexity analysis. Never sacrifice correctness for performance.
