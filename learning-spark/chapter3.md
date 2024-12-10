#### chapter 3. Apache Spark’s Structured APIs

###### intro:
- In this chapter, we will explore the principal motivations behind adding structure to
Apache Spark, how those motivations led to the creation of high-level APIs (Data‐
Frames and Datasets), and their unification in Spark 2.x across its components. We’ll
also look at the Spark SQL engine that underpins these structured high-level APIs.

- Inspired by pandas DataFrames in structure, format, and a few specific operations,
Spark DataFrames are like distributed in-memory tables with named columns and
schemas, where each column has a specific data type: integer, string, array, map, real,
date, timestamp, etc. To a human’s eye, a Spark DataFrame is like a table. DataFrames are
immutable and Spark keeps a lineage of all transformations. 

- In Spark's supported languages, columns are objects with public methods (represented by the
`Column` type).

- `Column` is the name of the object, while `col()` is a standard built-in function that returns a Column.

- `DataFrameReader` and `DataFrameWriter` interfaces.

- If the DataFrame is written as Parquet, the
schema is preserved as part of the Parquet metadata. 

- The DataFrame API also offers the collect() method, but for
extremely large DataFrames this is resource-heavy (expensive) and
dangerous, as it can cause out-of-memory (OOM) exceptions.
Unlike count(), which returns a single number to the driver, col
lect() returns a collection of all the Row objects in the entire DataFrame 
or Dataset. If you want to take a peek at some Row records
you're better off with `take(n)`, which will return only the first n
Row objects of the DataFrame.

- a strongly typed collection of domain-specific objects that can be transformed in paral‐
lel using functional or relational operations. Each Dataset [in Scala] also has an unty‐
ped view called a DataFrame, which is a Dataset of `Row`.

- internal `Catalog`, a programmatic interface to Spark SQL that holds
a list of names of columns, data types, functions, tables, databases, etc.

- 