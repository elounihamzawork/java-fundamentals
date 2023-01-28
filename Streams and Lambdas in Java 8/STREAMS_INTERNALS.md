# Building a stream pipeline

A stream pipeline is built by constructing a linked-list representation of the stream source and its intermediate operations.
In the internal representation, each stage of the pipeline is described by a bitmap of stream flags that describe 
what's known about the elements at this stage of the stream pipeline. Streams uses these flags to optimize both 
the construction and execution of the stream.
##### Stream flags
- **_SIZED_:**	            The size of the stream is known.<br>
- **_DISTINCT_:**	        The elements of the stream are distinct, according to Object.equals() for object streams, or according to == for primitive streams.<br>
- **_SORTED_:**	        The elements of the stream are sorted in the natural order.<br>
- **_ORDERED_:**	        The stream has a meaningful encounter order (see the " Encounter order" section).<br>

The stream flags for the source stage are derived from the characteristics bitmap of the spliterator (spliterators support a larger set of flags than do streams). 

A high-quality spliterator implementation not only provides efficient element access and splitting but also describes the characteristics of the elements. (For example, the spliterator for a HashSet reports the DISTINCT characteristic, since the elements of a Set are known to be distinct.)

Each intermediate operation has a known effect on the stream flags; an operation can set, clear, or preserve the setting for each flag. 
For example, the filter() operation preserves the SORTED and DISTINCT flags but clears the SIZED flag; 
the map() operation clears the SORTED and DISTINCT flags but preserves the SIZED flag; 
and the sorted() operation preserves the SIZED and DISTINCT flags and injects the SORTED flag. 
As the linked-list representation of stages is constructed, the flags for the previous stage are combined with the behavior 
of the current stage to arrive at a new set of flags for the current stage.

In some cases, the flags make it possible to elide(cancel) an operation entirely, as in this stream pipeline:
```
Stream pipeline in which operations can be automatically elided

TreeSet<String> ts = ...
String[] sortedAWords = ts.stream()
                          .filter(s ‑> s.startsWith("a"))
                          .sorted()
                          .toArray();

The stream flags for the source stage include SORTED, because the source is a TreeSet. 
The filter() method preserves the SORTED flag, so the stream flags for the filtering stage also include the SORTED flag. 
Normally, the result of the sorted() method would be to construct a new pipeline stage, add it to the end of the pipeline, 
and return the new stage. However, because it's known that the elements are already sorted in natural order, 
the sorted() method is a no-op — it just returns the previous stage (the filtering stage), since sorting would be redundant. 
(Similarly, if the elements are known to be DISTINCT, the distinct() operation can be eliminated entirely.)              
```

# Executing a stream pipeline

When the terminal operation is initiated, the stream implementation picks an execution plan. 
Intermediate operations are divided into stateless (filter(), map(), flatMap()) and stateful (sorted(), limit(), distinct()) operations. 
A stateless operation is one that can be performed on an element without knowledge of any of the other elements. For example, 
a filtering operation only needs to examine the current element to determine whether to include or eliminate it, 
but a sorting operation must see all the elements before it knows which element to emit first.

If the pipeline is executing sequentially, or is executing in parallel but consists of all stateless operations, 
it can be computed in a single pass. Otherwise, the pipeline is divided into sections (at stateful operation boundaries) 
and is computed in multiple passes.

Terminal operations are either short-circuiting (allMatch(), findFirst()) or non–short-circuiting (reduce(), collect(), forEach()). 
If the terminal operation is non–short-circuiting, the data can be processed in bulk (using the forEachRemaining() method of the source spliterator, 
further reducing the overhead of accessing each element); if it's short-circuiting, it must be processed one element at a time (using tryAdvance()).