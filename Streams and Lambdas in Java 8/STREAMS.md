# Streams
A stream is “a **sequence of elements** from a **source** that supports **aggregate operations**”.
**The Stream API provides a functional style of programming**

- Sequence of elements: A stream provides an interface to a sequenced set of values of a specific element type. 
    However, _streams don’t actually store elements; they are computed on demand_.
- Source: Streams conamounte from a data-providing source such as collections, arrays, or I/O resources. 
- Aggregate operations: Streams support SQL-like operations and common operations from functional programing languages, 
    such as filter, map, reduce, find, match, sorted, and so on.”
- A **stream pipeline** consists of three types of things (A source, Zero or more intermediate operations, and A terminal operation that
  Producing a `result` or a `side-effect` )
- The pipeline is **only evaluated** when the **terminal operation is called**
- The operations which return another stream as a result are called intermediate operations and the operations 
  which return non-stream values like primitive or object or collection or return nothing are called terminal operations.
- Intermediate operations can be chained together
- Terminal operations can not be chained together.
- All operations can execute sequentially or in parallel
- Intermediate operations can be merged
- **Intermediate operations are lazily loaded.** (When you call intermediate operations, they are actually not executed. 
  They are just stored in the memory and executed when the terminal operation is called on the stream.)
- **For functional programming you should not modify state**
- Streams only for object ref types, int, long, and double (Minor primitive types are missing like char and byte)
- **Streams are processed lazily**
- Data is “pulled” by terminal operation, not pushed by source
- Intermediate results typically not stored (But there are exceptions (e.g., sorted))
- **we cannot reuse the streams or traverse the streams multiple times.
    Any attempt to do so will result in error : Stream has already been operated on or closed.**

**Other definition for Streams**

Java Streams are basically a pipeline of `aggregate operations` that can be applied to process a sequence of elements.

**An aggregate operation:** is a higher-order function that receives a behaviour in a form of a function or lambda, 
and that behaviour is what gets applied to our sequence.

**higher-order function:** is a function that does at least one of the following(returns a function as its result, takes one or more functions as arguments)
### Characteristics of a stream : 

* **Declarative paradigm:** Streams are written specifying what has to be done, but not how.)
* **Lazily evaluated:** This basically means that until we call a terminal operation, our stream won’t be doing anything, 
  we will just have declared what our pipeline will be doing.
* **Can be parallelised:** Java Streams are sequential by default, but they can be very easily parallelised.
* **It can be consumed only once:** Once we call a terminal operation, a new stream would have to be generated 
  in order to apply the same series of java streams operations.
* are not data structure
* Do not contain storage for data 
* wrap collections (lists, set, maps)
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

with builder pattern:
```
Stream<String> stream4 = Stream.<String>builder()                                
          .add("Mike")
          .add("Sandra")
          .build(); 
```

Static factories:
```
– IntStream.range()
– Files.walk()
```

**How do data get out of streams?**
The Streaming API provides so called “finalizing” methods (i.e. methods that do not return stream objects) : 
``forEach, toArray, collect, reduce, min, max, count, anyMatch, noneMatch, findFirst, findAny...``

# Phases of a stream

A Java Stream is composed by three main phases:
- **_Split:_** Data is collected from a collection, a channel or a generator function for example. 
  In this step we convert a datasource to a Stream in order to process our data, we usually call it stream source. 
- **_Apply:_** Every operation in the pipeline is applied to each element in the sequence. 
  Operations in this phase are called intermediate operations.
- **_Combine:_** Completion with a terminal operation where the stream gets materialised.

There are two interfaces in Java which are very important for the SPLIT and COMBINE phases; these interfaces are 
**Spliterator** and **Collector**.

The Spliterator interface allows two behaviours that are quite important in the split phase: ``iterating`` and the ``potential splitting of elements``.
**Splitting will take a big importance when running parallel streams**, as it’ll be the one responsible for splitting 
the stream to give an independent “piece of work” to each thread.

Spliterator provides two methods for accessing elements:
```
boolean tryAdvance(Consumer<? super T> action);
void forEachRemaining(Consumer<? super T> action);
```
And one method for splitting our stream source:
``Spliterator<T> trySplit();``

Since JDK 8, a spliterator method has been included in every collection, so streams use the Spliterator internally 
to iterate through the elements of a Stream.



# Lambdas

Term comes from λ-Calculus | Everything is a function | anonymous function (A function without a corresponding identifier (name))


Lambda expressions are lexically scoped. This means that they **do not inherit any names from a supertype or introduce a new level of scoping**. 
Declarations in a lambda expression are interpreted just as they are in the enclosing environment.

“Lambda” = “closure” = record storing a function (functionality, method) and its environment (but without a class or method name) 
Roughly: anonymous method 
Lambdas represent source code - not data and not object state!

**Syntax:**  ``( parameter list ) -> { expression(s) }``
**Examples:**
````
(int x, int y) -> { return x + y; } 
(long x) -> { return x * 2; } 
() -> { String msg = "Lambda"; System.out.println(msg); }
for single return statements keyword return together with {}-pair can be dropped:  (int x, int y) -> { return x * y; } =>  (x, y) -> x * y 
( )-pair can be dropped with only one parameter: (long x) -> { return x * 2; }  => x -> x * 2 
````

# Type inference
- Compiler can often infer parameter types in a lambda expression (Inferrence based on target functional interface’s method signature )
- Lambda expressions allow for minimal syntax if compiler can deduct type information (so called type inference)

E.g: 
```
static T void sort(List<T>l, Comparator<?superT>c);
List<String>list=getList(); 
Collections.sort(list, (Stringx,Stringy)->x.length()>y.length()); 
                  🠳
Collections.sort(list, (x,y)->x.length()-y.length());
```

# Method references (“function pointers”)


**Syntax:** ``Classname::methodName``  ``objectReferenceName::methodName``
Lambdas can be replaced by method references whenever there would not further actions within the the lambda

|   Reference   | Method reference |   replacing Lambda   |
|:-------------:|    :----:   |:--------------------:|
| static method | String::valueOf       |    obj -> String.valueOf(obj)     |
|   instance method (via class)    | String::compareTo         |       (s1, s2) -> s1.comapreTo(s2)       |
| instance method (via object ref)  | person::getName        |   () -> person.getName()     |
| Constructor  | ArrayList::new        |    () -> new ArrayList<>()     |

#### The Art Of Reduction
Optional<T>reduce(BinaryOperator<T>accumulator):

- BinaryOperator is a subclass of BiFunction ``R apply(T t,U u)``, For BinaryOperator all types are the same ``T apply(T x,T y)``
- The key is to find the right accumulator : The accumulator takes a partial result and the next element, and returns a new partial result.


