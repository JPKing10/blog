+++
title = "Java Garbage Collection"
description = "Notes on Java GC and memory management. Hints for tuning HotSpot."
date = 2025-05-11
[taxonomies]
tags = ["notes", "java"]
+++

- Some simple notes on JVM heap memory and garbage collector (HotSpot)
- Todo: Z1

---

## JVM Heap & Garbage Collection Basics

- All objects live in a memory region called the **Java heap**  
- A garbage collector automates memory management on the heap  
  - It allocates and deallocates memory  
  - **Note:** The term "garbage collector" might be misleading; "memory manager" could be more accurate  
- The heap size can grow during execution up to a specified maximum size  
- The heap is organized into different regions to make GC more efficient  
- When regions become full, a GC cycle is triggered to clear out unused objects  

---

## The GC Cycle

1. **Mark**  
   - Unused objects (those without live references) are identified  
   - *Simple approach:* start from root objects, follow all references, and mark every reached object as live  
2. **Sweep/Delete**  
   - Marked (dead) objects are removed  
   - The allocator tracks the freed spaces for future allocations  
3. **Compaction (optional)**  
   - Free spaces can become fragmented between live objects  
   - The JVM may compact memory by shifting live objects to create one contiguous free region  
4. **Reuse or Return**  
   - Freed memory is either returned to the OS or reused for new objects  

---

## GC Pause Modes

- **Stop-the-World**  
  - Application threads are suspended while GC runs  
  - Simpler (no concurrent mutations)  
  - Suitable for **throughput-oriented** workloads (e.g., batch servers)  
- **Concurrent / Pause-Free**  
  - GC runs alongside the application  
  - More complex (must handle object mutations in real-time)  
  - Occasional short pauses may still occur  
  - Suitable for **latency-sensitive** applications  
- **HotSpot** uses a mix of both approaches  

---

## Generational Garbage Collection

- Based on the observation that objects tend to have different lifetimes:  
  1. **Young** (short-lived)  
  2. **Tenured/Old** (long-lived)  
- **Partitioning** the heap by age limits how much the GC must scan each cycle  
- **Minor GC**: triggers when the **Young** generation fills  
  - Targets only Young gen → faster, since most objects are dead  
- **Major GC** (or Full GC): triggers when the **Old** generation fills  
  - Scans the entire heap, but runs much less often  
- Live objects surviving Young-gen GCs get **promoted** to the Old generation  

### Young Generation Layout

- **Eden** + **Two Survivor Spaces**  
- On each Minor GC:  
  1. Eden + one Survivor space are emptied  
  2. Live objects from Eden and the other Survivor are copied into the empty Survivor  
  3. Objects that survive enough cycles (or if Survivor fills) are promoted to Old  

---

## HotSpot GC Tuning

Key goals: **pause-time**, **throughput**, and **heap size**. HotSpot may adjust heap and GC frequency at runtime to meet targets.

### Logging

```bash
-Xlog:gc*    # Log all GC-related messages
```

### Pause-Time Goal

```bash
-XX:MaxGCPauseMillis=<ms>
```

- Sets a target for the maximum pause time in milliseconds  
- The JVM will attempt to meet this goal, but it's not guaranteed  
- **Note:** Setting this value too low may lead to frequent GC cycles and reduced throughput  

### Throughput Goal

```bash
-XX:GCTimeRatio=nnn
```

- Defines the desirable ratio of **application time** to **GC time**  
- JVM aims to keep GC time ≤ `1/(1 + nnn)` of total runtime  
- For example, `-XX:GCTimeRatio=19` sets a goal of 5% GC time and 95% application time  

### Heap Size

```bash
-Xms=<size>    # Initial (minimum) heap size  
-Xmx=<size>    # Maximum heap size (larger heaps ⇒ fewer GCs but longer pauses)
```

#### Generation Sizes

```bash
-XX:NewRatio=nnn    # Ratio of Old to Young gen size (default: 2)
```

- **Note:** When using G1 GC, avoid setting the Young generation size explicitly (e.g., via `-Xmn` or `-XX:NewRatio`) as it may interfere with G1's ability to meet pause-time goals.  
  - Source: [Simple & effective Java G1 GC tuning tips](https://blog.gceasy.io/simple-effective-g1-gc-tuning-tips/)

---

## Further Reading

- [Java Memory Management White Paper](https://www.oracle.com/technetwork/java/javase/memorymanagement-whitepaper-150215.pdf)  
- [Garbage Collection Tutorial](https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html)  
- [Memory Segments and Arenas in Java](https://docs.oracle.com/en/java/javase/21/core/memory-segments-and-arenas.html)  
- [Java Garbage Collection Tuning](https://docs.oracle.com/en/java/javase/17/gctuning)  
- [Java Virtual Machine Options](https://www.oracle.com/java/technologies/javase/vmoptions-jsp.html)  
