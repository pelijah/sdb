```
+------+
| r.db | Simple and performance-friendly relational database
+------+
```

The database of r.db is based on index tables of key-size depth.
The key is the index of the key-bucket array which opens the search
space to a single key.

Items are directly sorted

  [255] key buckets
    |
    `-> [0..255]           -----.
         |     \                |
         |      \               |
     [0..255]  [0..255]         |
         |        |             |-- size of key (depth)
     [0..255]    ...            |
         |                      |
     [0..255]              -----'

OPERATIONS
==========

   ADD

   DELETE

     r_db_delete_by_key(db, K_ID, &tmp) # specify more than one key?

   ITERATE
   
+----------+
| r.db.sql | The SQL frontend for r.db
+----------+

 - Compiles simple SQL-like sentences into a set of r.db queries

void **iter = r_db_sql_query("select * from flags");
while(r_db_ptr(iter)) {
	struct foo_t *foo = r_db_ptr(iter);
	iter = r_db_ptr_next(iter);
}
