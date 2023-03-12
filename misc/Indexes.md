### Clustered Index and Covering Index

The key in an index is the thing that queries search for, but the value can be one of
two things:

1. It could be the actual row (document, vertex) in question, or
2. It could be a reference to the row stored elsewhere

When a reference is stored, this reference can be a place on a heap file. A heap file is a storage structure that contains data in no particular order.

> What is a heap file ?

- In a database, a heap file is a storage structure that contains data in no particular order.

- Since the data is not organized in any particular order, new rows can simply be appended to the end of the file, and deleted rows can be marked as deleted without actually removing them from the file which can be overwritten with new data later.

- However they can be slow when retrieving data based on indexed columns. Since the data is not organized in any particular order, the database engine must scan the entire file to find the required rows.

The heap file approach is common because it avoids duplicating data when multiple secondary indexes are present: each index just references a location in the heap file, and the actual data is kept in one place.

Now assume when updating a new value without changing the key, the value is bigger in size than the old data value. In this case a new location needs to be found where the new value will be stored.

In the heap file, either the location of new value can be updated for that key, or a forwarding pointer is left behind in the old heap location.

If the extra hop from the index to the heap file is too much of a performance
penalty for reads, so it can be desirable to store the indexed row directly
within an index. This is known as a **clustered index**.

A **covering index** is an in-between index that stores only few of the columns within the index. This form a sort of bridge between a clustered index (that stores all data) and non-clustered index (that only stores a pointer).

This approach allows some queries to be answered by using the
index alone (in which case, the index is said to cover the query).

|                  | Clustered Index                           | Covering Index                          |
| ---------------- | ----------------------------------------- | --------------------------------------- |
| Primary Function | Stores all data in the index              | Stores some data in the index           |
| Index Columns    | Contains all columns for the entire table | Contains some columns for the table     |
| Disk Space       | Requires more disk space than covering    | Requires less disk space than clustered |

---

Ref:

1. Designing Data-Intensive Applications by Martin Kleppman - Chapter 3 - Storage and Retrieval
