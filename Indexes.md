A database index is a data structure that helps speed up data retrieval in a database table.

An index acts as a lookup table that points to the location of requested data in a database file. It typically consists of two columns: a search key and a data pointer. The search key stores the value to search for, and the data pointer points to the block where the data is located.

- **Benefits**

	 Indexes can significantly improve the performance of SQL statements, especially for large tables. 

- **Drawbacks**
    
	 Indexes can consume additional disk space and slow down the performance of some inserts, updates, and deletes. 


- **Examples**
    
     B-trees are a common type of index used in database engines. B-trees are self-balancing, ordered, and disk-friendly, which makes them well-suited for databases.
     



Clustered vs non clustered indexes

	Cluster index doesn't require additional disk space whereas the Non-clustered index requires additional disk space. Cluster index offers faster data accessing, on the other hand, Non-clustered index is slower

	If you need to retrieve a large number of rows from the table, then you should consider creating a clustered index. If you need to retrieve a small number of rows from the table, then you should consider creating a non-clustered index.

	A clustered index physically stores the data rows in a table in a specific order, while a non-clustered index creates a separate structure that references the original data.
	
	A clustered index is used to sort and store data rows in a table, while a non-clustered index is used to improve query performance or locate rows in a table
	
	A clustered index can slow down when rows are updated because other rows may need to be moved to maintain the correct order. A non-clustered index can consume extra disk space, especially for large tables with many columns or rows