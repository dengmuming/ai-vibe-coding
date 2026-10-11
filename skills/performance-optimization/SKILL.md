---
description: Systematically analyze bottlenecks, improve execution efficiency, and reduce memory/resource consumption.
---

# Performance Optimization Skill

When asked to optimize code performance, follow a systematic, evidence-based approach:

1.  **Understand the Bottleneck**: Identify whether the issue is CPU-bound, memory-bound, or I/O-bound. Ask for or use profiling data before guessing.
2.  **Algorithmic Efficiency**: 
    - Check for inefficient loops (e.g., $O(N^2)$ instead of $O(N \log N)$ or $O(N)$).
    - Ensure the right data structures are used (e.g., Hash Maps/Sets for fast lookups instead of Arrays).
3.  **Database & I/O Optimization**:
    - Identify N+1 query problems.
    - Check for missing indexes or unoptimized database queries.
    - Batch I/O operations where possible.
4.  **Caching Strategy**:
    - Determine if repeated computations or queries can be cached (e.g., memoization, Redis).
5.  **Concurrency & Asynchronous Processing**:
    - Use async/await for I/O waits.
    - Leverage multi-threading or parallel processing for heavy CPU tasks if the environment permits.
6.  **Verify via Measurement**: Always ensure that the optimized code yields identical results to the original, and summarize the expected time/space complexity improvements.
