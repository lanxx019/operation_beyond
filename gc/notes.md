# JVM Garbage Collection Study Notes

## Basic Definitions and Algorithms

- Generation

- Concurrent

- Parallel

- What is GC root?

	The so-called GC (Garbage Collector) roots are objects special for garbage collector. Garbage collector collects those objects that are not GC roots and are not accessible by references from GC roots. ([GC Roots](https://www.yourkit.com/docs/java/help/gc_roots.jsp), [Garbage Collection Roots](https://www-01.ibm.com/support/knowledgecenter/SS3KLZ/com.ibm.java.diagnostics.memory.analyzer.doc/gcroots.html))

- What is GC safepoint?
	
	A point during program execution at which all GC roots are known and all heap object contents are consistent. From a global point of view, all threads must block at a safepoint before the GC can run. (As a special case, threads running JNI code can continue to run, because they use only handles. During a safepoint they must block instead of loading the contents of the handle.) From a local point of view, a safepoint is a distinguished point in a block of code where the executing thread may block for the GC. Most call sites qualify as safepoints. There are strong invariants which hold true at every safepoint, which may be disregarded at non-safepoints. Both compiled Java code and C/C++ code be optimized between safepoints, but less so across safepoints. The JIT compiler emits a GC map at each safepoint. C/C++ code in the VM uses stylized macro-based conventions (e.g., TRAPS) to mark potential safepoints. ([HotSpot Glossary of Terms](http://openjdk.java.net/groups/hotspot/docs/HotSpotGlossary.html#safepoint), [Safety First: Safepoints](http://jpbempel.blogspot.com/2013/03/safety-first-safepoints.html), [Safepoints in HotSpot JVM](http://blog.ragozin.info/2012/10/safepoints-in-hotspot-jvm.html))

- What is Free List?

	A storage management technique in which unused parts of the Java object heap are chained one to the next, rather than having all of the unused part of the heap in a single block.([HotSpot Glossary of Terms](http://openjdk.java.net/groups/hotspot/docs/HotSpotGlossary.html#freeList), [Free List](https://en.wikipedia.org/wiki/Free_list))
	
- What is Card Marking?

	The Sun JDKs use an optimized variant of an algorithm called card marking to identify modifications to pointers held in fields of old-generation objects. In this approach, the heap is divided into a set of cards, each of which is usually smaller than a memory page. The JVM maintains a card map, with one bit (or byte, in some implementations) corresponding to each card in the heap. Each time a pointer field in an object in the heap is modified, the corresponding bit in the card map for that card is set. At garbage collection time, the mark bits associated with cards in the old generation are examined, and dirty cards are scanned for objects containing references into the younger generation. Then the mark bits are cleared. ([Garbage collection in the HotSpot JVM](http://www.ibm.com/developerworks/library/j-jtp11253/))

- What is thread local allocation buffer(TLAB)?

	Thread-local allocation buffer. Used to allocate heap space quickly without synchronization. Compiled code has a "fast path" of a few instructions which tries to bump a high-water mark in the current thread's TLAB, successfully allocating an object if the bumped mark falls before a TLAB-specific limit address.([HotSpot Glossary of Terms](http://openjdk.java.net/groups/hotspot/docs/HotSpotGlossary.html#TLAB), [The Real Thing](https://blogs.oracle.com/jonthecollector/entry/the_real_thing), [What Are Thread Local Allocation Buffers](http://robsjava.blogspot.com/2013/03/what-are-thread-local-allocation-buffers.html))

- What is read/write barrier?

	Memory Barrier: A barrier is a block on reading from or writing to certain memory locations by certain threads or processes.
Barriers can be implemented in either software or hardware. **Software barriers involve additional instructions around load or store operations, which would typically be added by a cooperative compiler**. Hardware barriers don’t require compiler support, and may be implemented on common operating systems by using memory protection. ([Memory Management](http://www.memorymanagement.org/glossary/b.html#term-barrier-1))
	- Write Barrier: A write barrier is a block on writing to certain memory locations by certain threads or processes. Write barriers are used for incremental or concurrent garbage collection. They are also used to maintain remembered sets for generational collectors.([Memory Management](http://www.memorymanagement.org/glossary/w.html#term-write-barrier))
	- Read Barrier: A read barrier is a block on reading from certain memory locations by certain threads or processes. Read barriers are used for incremental or concurrent garbage collection. ([Memory Management](http://www.memorymanagement.org/glossary/r.html#term-read-barrier))

- What is tri-color marking?
- What is incremental update?
- What is snapshot at the beginning(SATB)?
- What is handler in Java?
- What is compare and swap(CAS)?
- What is compiled code?
- What is interpreted code?
- What are phantom/weak/soft reference in Java?

## Common Garbage Collection Phases/Operations

### Mark

### Sweep

### Compact

### Cpoy

## HotSpot Garbage Collectors

### Serial Collector

### Parallel Collector

### Current Mark and Sweep Collector

### Garbare First(G1) Collector

### Shenandoah Collector

## Reference

1. [Java Garbage Collection Handbook](https://plumbr.eu/java-garbage-collection-handbook)
2. [Adopt OpenJDK](https://adoptopenjdk.gitbooks.io/adoptopenjdk-getting-started-kit/content/en/index.html) 
3. [Memory Management Reference](http://www.memorymanagement.org/) 
4. [The Java Garbage Collection Mini Book](https://github.com/lanxx019/operation_beyond/blob/master/gc/The-Java-Garbage-Collection-Mini-book-final.pdf)
5. [The JVM Write Barrier - Card Marking](http://psy-lob-saw.blogspot.com/2014/10/the-jvm-write-barrier-card-marking.html)
6. [Garbage Collector in Java](http://www.brpreiss.com/books/opus5/html/page424.html#SECTION0014300000000000000000)
7. [HotSpot Glossary of Terms](http://openjdk.java.net/groups/hotspot/docs/HotSpotGlossary.html)
8. [Tracing garbage collection](https://en.wikipedia.org/wiki/Tracing_garbage_collection#Tri-color_marking)
9. [G1 Garbage Collector: Details and Tuning](http://www.slideshare.net/SimoneBordet/g1-garbage-collector-details-and-tuning)
10. [Understanding GC pauses in HotSpot's minor GC](http://blog.ragozin.info/2011/06/understanding-gc-pauses-in-jvm-hotspots.html)