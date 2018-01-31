Just some notes from StanfordOnline's "Introduction to Databases."

# Introduction to Databases
## Intro
Why should one use a database as opposed to simply storing data in a bunch of files?  If one has only a little bit of data and will be the only one accessing, a file system could work fine.  However, if multiple users need to access the data, manipulate it, and even access the same data simultaneously, a file system can become inefficient, unsecure, or even dangerous (e.g., one user can decide to overwrite some data that another user needs, etc). 

Databases are designed to be efficient, reliable, convenient, and safe, providing access to massive amounts data for use by many users.  DBs do not require data to be stored in memory, but allow for storage, management, and access to occur on disk.  Furthermore, the data is persistent (not changed when a user or users are performing transformations on it). The access is efficient --- quick! It is reliable (accessible all the time).

### 4 Key Concepts
* *Data Model*:  How is the data structured? (e.g., set of records, XML files, graph models, etc)
* *Schema vs Data*:  Analogous to "types" and "variables"  (or "classes" and "objects") in programming languages;  you create a schema, then store data in the prescribed schema
* *Data Definition Language (DDL)*:  the language used to construct schema for a particular DB
* *Data Manipulation (Query) Language (DML)*:  used to query and modify the data in a DB

### 4 Key People
* *DBMS Implementer*:  builds the system (not focused on in this course)
* *DB Designer*:  establishes the schema for DB by looking at what data a company has and wants to store and attempting to design an intuitive, efficient structuring of that data
* *DB Application Developer*:  develops the interfaces and programs that operate on top of the database and called on by the user
* *DB Administrator*:  loads data into the database, gets everything running and working, and maintains the database, ensuring that it is always operating smoothly and efficiently (very important role at a company and is often highly paid)


## The Relational Model
The relational model is powerful because its language is simple, but expressive (i.e., it is human-readable, intuitive).  One need not write and re-write tons of code to access and transform data in various ways: relations DBs support high-level language that allows a user to write simple queries and let the DB do the hard work behind the scenes.  Furthermore, it has been around a long time and has very efficient implementations.

### Terminology
* *Database*:  a set of named relations (i.e., a set of named tables)
* *Relation = Table*:  a set of named attributes (i.e., a set of named columns)
* *Tuple = Row*:  a set of associated values for each attribute (i.e., a set of associated values across each column)
* *Type (aka Domain)*:  the data type of an attribute
  - e.g., atomic data types such as string, float, integer (commonly supported)
  - e.g., structured types such as jpg files (supported by some databases)
* *Schema*:  the structural description of a relation (i.e., the structural description of a table)
* *Instance*:  the actual contents of a relation/table in a DB
  - so the schema is set up in advance, while instances change over time
* *NULL*:  a special value that represents an unknown/undefined value and can be used for any data type
  - caveat*: NULLs are not returned when queried (e.g., if you specify to return all students w/ GPA > 3.5 OR GPA <= 3.5, any student w/ a NULL GPA will not be returned)
* *Key*:  an attribute whose value is unique (or a set of attributes whose combined values are unique) for each tuple/row in a relation/table
  - i.e., this an index-like thing that uniquely identifies each row, but is not constrained to being an index  (though, behind the scenes the DB converts keys into indices for efficient access and storage)
  - a key that is common between two tables/relations represents how those tables/relations are linked, or "point" to each other (this is like a pointer, but not officially a pointer)
* *Query Language*:  Synonymous with Data Manipulation Language (DML)
* *Closure*:  Queries return relations/tables from the database; closure refers to the fact that the database returns a relation/table of the same type that is queried (i.e., you can request data tables to be returned in specific sub- and joined-table forms, not necessarily in the form data is stored in)
* *Composition*:  Queries on top of queries (you can create a query to query your query :-p)

### Querying Relational/Tabulated Databases
#### 1. Design the database schema with the Data Definition Language (DDL)
* cylinder usually used to represent database
* "rowless" document used to represent schema

```
--------------------------
|        ________      |
|        |    |  |  |    |     |
|        |    |  |  |    |     |
|        |__|_|_|__|     |
|                              |
|_______________|
```

#### 2. "Bulk load" initial data into database
* column/row structured document represents instance of a relation/table
```
--------------------------
|        ________      |     Some Files
|        |__|_|_|__|     |           ___
|        |__|_|_|__|     |  <--   /--- /
|        |__|_|_|__|     |         /__/
|                              |
|_______________|
```

#### 3. Repeat: execute queries (Q) and modifications (M)
```
--------------------------
|        ________      |     Some Files
|        |__|_|_|__|     |                  O
|        |__|_|_|__|     |   <--Q--    --|--
|        |__|_|_|__|     |   ---A-->     /\
|                              |   <--Q2--
|_______________|   ---A2--> 
           /\       |
  O       |  M  | "Ok"
--|--  __|       |
  /\     <--------
```
