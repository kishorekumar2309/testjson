# 4. INTRODUCING STREAMS

### 1. WHAT ARE STREAMS?

- an update to the Java API that let you manipulate collections of data in a declarative way
- chain together several building-block operations to express a complicated data-processing pipeline

***Summary***:

- ***Declarative*—** More concise and readable
- ***Composable*—** Greater flexibility
- ***Parallelizable*—** Better performance



### 2. GETTING STARTED WITH STREAMS

***Definition:*** a sequence of elements from a source that supports data-processing operations

Break down:

- ***Sequence of elements*—** Collections are about data; streams are about computations
- ***Source*—** data-providing source such as collections, arrays, or I/O resources
- ***Data-processing operations*—** supports database-like / functional programming operations (filter, map, reduce, find, match, sort, and so on)

2 important characteristics of stream operations:

1. ***Pipelining*—** Many stream operations return a stream themselves, allowing operations to be chained to form a larger pipeline (enables *laziness* and *short-circuiting*)
2. ***Internal iteration*—** In contrast to collections, which are iterated explicitly using an iterator, stream operations do the iteration behind the scenes



### 3. STREAMS VS. COLLECTIONS

- both provide interfaces to data structures representing a sequenced set of values of the element type
- collection is an in-memory data structure that holds *all* the values the data structure currently has—every element in the collection has to be computed before it can be added to the collection (can add or remove elements) (DVD)
- a stream is a conceptually fixed data structure (can’t add or remove elements) whose elements are *computed on demand* (Video streaming)
- a collection is eagerly constructed whereas a stream is like a lazily constructed collection: values are computed when they’re solicited by a consumer (in management speak this is demand-driven, or even just-in-time, manufacturing)

#### 3.1. Traversable only once

- a stream can be consumed only once

#### 3.2. External vs. internal iteration

- *external iteration:* Collection interface requires iteration to be done by the user (for example, using for-each)
- *internal iteration:* Streams does the iteration for you and takes care of storing the resulting stream value somewhere



### 4. STREAM OPERATIONS

#### 4.1. Intermediate operations

- return another stream as the return type
- can be connected to form a query
- *lazy* - don’t perform any processing until a terminal operation is invoked on the stream pipeline
- *optimized* - intermediate operations can usually be merged and processed into a single pass by the terminal operation
- *short circuiting* - can be used for short circuiting (e.g. *limit*)

#### 4.2. Terminal operations

- operations that close a stream and produce a result from a stream pipeline

#### 4.3. Working with streams

3 essential items for building a stream pipeline:

1. *data source* 
2. *intermediate operations*
3. *terminal operation*



### SUMMARY

- A stream is a sequence of elements from a source that supports data-processing operations.
- Streams make use of internal iteration: the iteration is abstracted away through operations such as filter, map, and sorted.
- There are two types of stream operations: intermediate and terminal operations.
- Intermediate operations such as filter and map return a stream and can be chained together. They’re used to set up a pipeline of operations but don’t produce any result.
- Terminal operations such as forEach and count return a non-stream value and process a stream pipeline to return a result.
- The elements of a stream are computed on demand (“lazily”).





