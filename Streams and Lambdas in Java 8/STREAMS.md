# Streams
A stream is “a **sequence of elements** from a **source** that supports **aggregate operations**”
- Sequence of elements: A stream provides an interface to a sequenced set of values of a specific element type. 
    However, _streams don’t actually store elements; they are computed on demand_.
- Source: Streams conamounte from a data-providing source such as collections, arrays, or I/O resources. 
- Aggregate operations: Streams support SQL-like operations and common operations from functional programing languages, 
    such as filter, map, reduce, find, match, sorted, and so on.”

### Streams : 

* are not data structure
* Do not contain storage for data
* are “pipelines” for streams of data (i.e. of objects) 
* while in the pipeline data undergo transformation (without changing the data structure holding it) 
* wrap collections (lists, set, maps)
* read data from it
* work on copies

### How do data get into streams?

Streams are mainly generated based on collections:
```
List<String> names = Arrays.asList("John", "George", "Sue");   
Stream<String> stream1 = names.stream();  
Stream<String> stream2 = Stream.of("Tom", "Rita", "Mae");  
Stream<String> stream3; 
stream2 = Arrays.stream( new String[]{"Lisa", "Max", "Anna"} ); 
```

Or with builder pattern:
```
Stream<String> stream4 = Stream.<String>builder()                                
          .add("Mike")
          .add("Sandra")
          .build(); 
```

**How do data get out of streams?**
The Streaming API provides so called “finalizing” methods (i.e. methods that do not return stream objects) : 
``forEach, toArray, collect, reduce, min, max, count, anyMatch, noneMatch, findFirst, findAny...``

# Lambdas or Closures

“Lambda” = “closure” = record storing a function (functionality, method) and its environment (but without a class or method name) 
Roughly: anonymous method 
Lambdas represent source code - not data and not object state!

**Syntax:**  ``( parameter list ) -> { expression(s) }``
**Examples:**
````
(int x, int y) -> { return x + y; } 
(long x) -> { return x * 2; } 
() -> { String msg = "Lambda"; System.out.println(msg); }
````

