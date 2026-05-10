# JVM (Java Virtual Machine) Reference

**Focus**: JVM architecture, memory management, garbage collection, performance tuning, troubleshooting, and JVM internals.

## Table of Contents

- [1. JVM Architecture](#1-jvm-architecture)
- [2. Memory Management](#2-memory-management)
- [3. Garbage Collection](#3-garbage-collection)
- [4. JVM Options](#4-jvm-options)
- [5. Performance Tuning](#5-performance-tuning)
- [6. Monitoring & Troubleshooting](#6-monitoring--troubleshooting)
- [7. JVM Tools](#7-jvm-tools)

---

## 1. JVM Architecture

**JVM components**: Class Loader, Runtime Data Areas, Execution Engine, Native Method Interface

**Runtime Data Areas**:
- **Method Area** (Metaspace in Java 8+): Class metadata, static variables, method bytecode
- **Heap**: Object instances (shared across threads)
- **Stack**: Per-thread stack frames (local variables, method calls)
- **PC Register**: Per-thread program counter
- **Native Method Stack**: Native method calls

**Execution Engine**: Interpreter, JIT Compiler (C1, C2), Garbage Collector

**Class loading**: Bootstrap → Extension → Application class loaders

**Bytecode execution**: Java source → `.class` files (bytecode) → JVM interprets/compiles → Native code

---

## 2. Memory Management

**Heap structure**:
- **Young Generation**: Eden, Survivor 0, Survivor 1 (new objects)
- **Old Generation** (Tenured): Long-lived objects
- **Metaspace** (Java 8+): Class metadata (replaces PermGen)

**Object lifecycle**: Created in Eden → Minor GC → Survivor spaces → Major GC → Old Generation

**Stack memory**: Per-thread, stores local variables, method parameters, return addresses

**Memory allocation**: Objects allocated on heap, references on stack

**OutOfMemoryError types**: `Heap space`, `Metaspace`, `Direct buffer memory`, `Unable to create native thread`

---

## 3. Garbage Collection

**GC algorithms**:
- **Serial GC**: Single-threaded, `-XX:+UseSerialGC`
- **Parallel GC**: Multi-threaded, `-XX:+UseParallelGC`
- **G1 GC**: Low-latency, `-XX:+UseG1GC`
- **ZGC**: Ultra-low latency (Java 11+), `-XX:+UseZGC`
- **Shenandoah**: Low-latency (Java 12+), `-XX:+UseShenandoahGC`

**GC types**:
- **Minor GC**: Young generation collection
- **Major GC**: Old generation collection
- **Full GC**: Entire heap collection

**GC phases** (G1):
- **Young Collection**: Eden + Survivor spaces
- **Mixed Collection**: Young + some old regions
- **Concurrent Marking**: Mark live objects concurrently

**GC tuning flags**:
```bash
-XX:MaxGCPauseMillis=200        # Target pause time (G1)
-XX:G1HeapRegionSize=16m        # Region size (G1)
-XX:NewRatio=2                   # Old:Young ratio (1:2)
-XX:SurvivorRatio=8              # Eden:Survivor ratio (8:1:1)
```

---

## 4. JVM Options

**Memory options**:
```bash
-Xms512m                         # Initial heap size
-Xmx2g                           # Maximum heap size
-Xmn256m                         # Young generation size
-XX:MetaspaceSize=256m           # Initial metaspace size
-XX:MaxMetaspaceSize=512m        # Maximum metaspace size
```

**GC options**:
```bash
-XX:+UseG1GC                     # Use G1 garbage collector
-XX:MaxGCPauseMillis=200          # Target pause time
-XX:+PrintGCDetails               # Print GC details
-XX:+PrintGCDateStamps            # Print GC timestamps
-Xlog:gc*:file=gc.log            # GC logging (Java 9+)
```

**Performance options**:
```bash
-XX:+UseStringDeduplication      # Deduplicate strings (G1)
-XX:+UseCompressedOops           # Compress object pointers (64-bit)
-XX:+UseCompressedClassPointers  # Compress class pointers
-XX:+TieredCompilation           # Enable tiered compilation
-XX:CompileThreshold=10000       # JIT compilation threshold
```

**Debugging options**:
```bash
-XX:+HeapDumpOnOutOfMemoryError  # Dump heap on OOM
-XX:HeapDumpPath=/path/to/dump   # Heap dump path
-XX:+PrintFlagsFinal              # Print all JVM flags
-XX:+UnlockDiagnosticVMOptions   # Unlock diagnostic options
```

---

## 5. Performance Tuning

**Heap sizing**:
- Set `-Xms` = `-Xmx` to avoid heap resizing
- Young generation: 25-50% of heap
- Old generation: 50-75% of heap

**GC selection**:
- **Throughput**: Parallel GC (batch processing)
- **Low latency**: G1 GC (web applications)
- **Ultra-low latency**: ZGC/Shenandoah (real-time systems)

**Common tuning**:
```bash
# Web application (G1)
-Xms2g -Xmx2g
-XX:+UseG1GC
-XX:MaxGCPauseMillis=200
-XX:G1HeapRegionSize=16m

# Batch processing (Parallel)
-Xms4g -Xmx4g
-XX:+UseParallelGC
-XX:ParallelGCThreads=8
```

**JIT compilation**: Hot methods compiled to native code after threshold (default 10,000 invocations)

**String deduplication**: `-XX:+UseStringDeduplication` (G1) - Reduces memory for duplicate strings

---

## 6. Monitoring & Troubleshooting

**JVM monitoring**:
```bash
jps                              # List JVM processes
jstat -gc <pid> 1000             # GC statistics every second
jstat -gccapacity <pid>          # GC capacity
jmap -heap <pid>                 # Heap summary
jmap -histo <pid>                # Object histogram
```

**Thread analysis**:
```bash
jstack <pid>                     # Thread dump
jstack -l <pid>                  # Thread dump with locks
```

**Memory analysis**:
```bash
jmap -dump:format=b,file=heap.hprof <pid>  # Heap dump
jhat heap.hprof                   # Analyze heap dump (deprecated)
# Use Eclipse MAT or VisualVM instead
```

**GC logging** (Java 9+):
```bash
-Xlog:gc*:file=gc.log:time,level,tags
-Xlog:gc+heap:file=heap.log
```

**Common issues**:
- **OutOfMemoryError**: Increase heap, check memory leaks, analyze heap dump
- **High GC pause**: Tune GC algorithm, reduce heap size, optimize object allocation
- **CPU spikes**: Thread dump analysis, check infinite loops, JIT compilation issues
- **Memory leaks**: Heap dump analysis, check static collections, unclosed resources

---

## 7. JVM Tools

**JDK tools**:
- `jps`: List JVM processes
- `jstat`: JVM statistics monitoring
- `jmap`: Memory map and heap dump
- `jstack`: Thread stack trace
- `jinfo`: JVM configuration info
- `jcmd`: Multi-tool command (Java 7+)

**VisualVM**: GUI tool for monitoring, profiling, thread analysis, heap dump analysis

**Eclipse MAT**: Memory Analyzer Tool for heap dump analysis

**JProfiler**: Commercial profiler for CPU, memory, thread analysis

**Flight Recorder** (Java 11+):
```bash
-XX:+FlightRecorder
-XX:StartFlightRecording=duration=60s,filename=recording.jfr
jfr print recording.jfr          # Print recording
```

**JMX monitoring**:
```bash
-Dcom.sun.management.jmxremote
-Dcom.sun.management.jmxremote.port=9999
-Dcom.sun.management.jmxremote.authenticate=false
-Dcom.sun.management.jmxremote.ssl=false
```

**Common commands**:
```bash
# GC statistics
jstat -gc <pid> 1000

# Thread dump
jstack <pid> > threaddump.txt

# Heap dump
jmap -dump:live,format=b,file=heap.hprof <pid>

# JVM info
jinfo -flags <pid>
```

---

> **Note**: JVM manages memory through heap (objects) and stack (method calls). Garbage collection reclaims unused heap memory. Choose GC algorithm based on latency/throughput requirements: G1 for low latency, Parallel for throughput, ZGC for ultra-low latency. Monitor with `jstat`, `jmap`, `jstack`. Tune heap size, GC algorithm, and JIT compilation for optimal performance. Use heap dumps and thread dumps for troubleshooting memory leaks and performance issues.

