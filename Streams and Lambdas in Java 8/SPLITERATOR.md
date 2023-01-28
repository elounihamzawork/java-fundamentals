Besides traversing sequences of data, like an Iterator<T>, a Spliterator<T> can also partition it:
> Iterator + Splitting => Spliterator

Spliterator itself does not provide the parallel programming behavior. However, it provides some methods to support it. <br>
Developers should utilize Spliterator interface methods and implement parallel programming by using Fork/Join Framework (one good approach).<br>
We can use it for both Collection API and Stream API classes.<br>
It uses `tryAdvance()` method to iterate elements individually in multiple Threads to support Parallel Processing.<br>
It uses `forEachRemaining()` method to iterate elements sequentially in a single Thread.
It uses `trySplit()` method `to divide itself into Sub-Spliterators` to support Parallel Processing.
**Java Spliterator Methods**

- **default void forEachRemaining(Consumer action):** Performs the given action for each remaining element, 
  sequentially in the current thread, until all elements have been processed or the action throws an exception.
- **trySplit():** To split the source, so that two threads can work separately on different sections of the input, Spliterator provides a trySplit() method


##### Spliterator trySplit() example
```
ArrayList<String> list = new ArrayList<>();

list.add("A");
list.add("B");
list.add("C");
list.add("D");
list.add("E");
list.add("F");

Spliterator<String> spliterator1 = list.spliterator();
Spliterator<String> spliterator2 = spliterator1.trySplit();

spliterator1.forEachRemaining(System.out::println);

System.out.println("========");

spliterator2.forEachRemaining(System.out::println);

OUTPUT:

D
E
F
========
A
B
C
```

##### Spliterator forEachRemaining() example
Java example to perform hasNext() and next() operations in single statement using forEachRemaining() method.

```
ArrayList<String> list = new ArrayList<>();

list.add("A");
list.add("B");
list.add("C");
list.add("D");

Spliterator<String> spliterator = list.spliterator();

spliterator.forEachRemaining(System.out::println);

Program Output.

A
B
C
D
```


For instance, IntStream.range(inclusive, exclusive) creates an **RangeIntSpliterator** 
with the characteristics ORDERED | SIZED | SUBSIZED | IMMUTABLE | NONNULL | DISTINCT | SORTED .
This means that this stream has no duplicates, no nulls, is already sorted in natural order, the size is known, 
and it will be chunked deterministically. The data and the iteration order never change, and if we split it, we will always get the same first chunk.<br>
So these code snippets should have virtually the same performance:
```
    @Benchmark
    public long countRange() {
    return IntStream.range(0, size).count();
    }

    @Benchmark
    public long countRangeDistinct() {
        return IntStream.range(0, size).distinct().count();
    }
```

### Examples

**tryAdvance()** : Performs the given action on next element. It is used to traverse the elements one by one.
```
List<String> languageList = Arrays.asList("Java", "C", "C++", "Python", "Scala", "Kotlin");
Spliterator<String> languageSpliterator = languageList.spliterator();
languageSpliterator.tryAdvance(System.out::println);
languageSpliterator.tryAdvance(System.out::println);
         
//      Output : 
//      Java
//      C
```

**forEachRemaining()** : Performs the given action on each of the remaining elements. It is used to traverse the elements in bulk.
```
List<String> languageList = Arrays.asList("Java", "C", "C++", "Python", "Scala", "Kotlin");
Spliterator<String> languageSpliterator = languageList.spliterator();
languageSpliterator.tryAdvance(System.out::println);
languageSpliterator.tryAdvance(System.out::println);
languageSpliterator.forEachRemaining(System.out::println);
         
//      Output : 
//      Java
//      C
//      C++
//      Python
//      Scala
//      Kotlin
```

**trySplit()** : This method splits current Spliterator into another Spliterator. It is helpful in parallel programming.
```
List<String> languageList = Arrays.asList("Java", "C", "C++", "Python", "Scala", "Kotlin");
Spliterator<String> languageSpliterator = languageList.spliterator();
Spliterator<String> anotherLanguageSpliterator = languageSpliterator.trySplit();
 
System.out.println("From 1st Spliterator");
System.out.println("------------------");
languageSpliterator.forEachRemaining(System.out::println);
         
System.out.println("------------------");
         
System.out.println("From 2nd Spliterator");
System.out.println("------------------");
anotherLanguageSpliterator.forEachRemaining(System.out::println);
                 
//      From 1st Spliterator
//      ------------------
//      Python
//      Scala
//      Kotlin
//      ------------------
//      From 2nd Spliterator
//      ------------------
//      Java
//      C
//      C++
```

**estimateSize()** : It returns the estimate of number of elements yet to be traversed by the Spliterator
```
List<String> languageList = Arrays.asList("Java", "C", "C++", "Python", "Scala", "Kotlin");
Spliterator<String> languageSpliterator = languageList.spliterator();
System.out.println(languageSpliterator.estimateSize());       //Output : 6
languageSpliterator.tryAdvance(System.out::println);
System.out.println(languageSpliterator.estimateSize());       //Output : 5
```

**getExactSizeIfKnown()** : It returns exact size if known, otherwise returns -1.
```
List<String> languageList = Arrays.asList("Java", "C", "C++", "Python", "Scala", "Kotlin");
Spliterator<String> languageSpliterator = languageList.spliterator();
System.out.println(languageSpliterator.getExactSizeIfKnown());       //Output : 6
languageSpliterator.tryAdvance(System.out::println);
System.out.println(languageSpliterator.getExactSizeIfKnown());       //Output : 5
```

**characteristics()** : Returns set of characteristics of elements in an integer form.
```
List<String> languageList = Arrays.asList("Java", "C", "C++", "Python", "Scala", "Kotlin");
Spliterator<String> languageSpliterator = languageList.spliterator();
System.out.println(languageSpliterator.characteristics());
         
//Output : 16464
```

**hasCharacteristics()** : Checks whether this Spliterator has specified characteristics or not.
```
List<String> languageList = Arrays.asList("Java", "C", "C++", "Python", "Scala", "Kotlin");
Spliterator<String> languageSpliterator = languageList.spliterator();
System.out.println(languageSpliterator.hasCharacteristics(16464));
         
//Output : true
```

**getComparator()** : It returns Comparator of the source if it is sorted, otherwise throws IllegalStateException. 
If the source is sorted in natural order, it returns null.
```
List<String> languageList = Arrays.asList("Java", "C", "C++", "Python", "Scala", "Kotlin");
Spliterator<String> languageSpliterator = languageList.spliterator();
System.out.println(languageSpliterator.getComparator());
         
//Output : Exception in thread "main" java.lang.IllegalStateException
```
