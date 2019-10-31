# Creating tables using DSLContext.ddl() converts VARBINARY columns to TEXT in MySQL ([#9473](https://github.com/jOOQ/jOOQ/issues/9473))

Repro:
1. Run codegen (uses `DDLDatabase` extension with `src/main/resources/schema.sql`)
2. Change `connection` instantiation in `MCVETest` to connect to a valid MySQL DB
3. Run MCVETest.mcveTest() and observe the exception thrown when inserting `byte[]` values into `TestTable`:
```
org.jooq.exception.DataAccessException: SQL [insert into `TestTable` (`Foo`, `Bar`) values (?, ?)]; Incorrect string value: '\x81\x82\x83\x84\x85\x86...' for column 'Foo' at row 1
```
4. Observe that the types of the columns in the created table don't match what's in `schema.sql`:
```
mysql> describe TestTable;
   +-------+------+------+-----+---------+-------+
   | Field | Type | Null | Key | Default | Extra |
   +-------+------+------+-----+---------+-------+
   | Foo   | text | YES  |     | NULL    |       |
   | Bar   | text | YES  |     | NULL    |       |
   +-------+------+------+-----+---------+-------+
   2 rows in set (0.00 sec)
```