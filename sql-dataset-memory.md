# Dealing with larger SQL datasets and limited memory

> Originally Written: 2015-08-20

There may come a time when you have a very large table to query that results in a very large result set. If you use 
pyodbc for connecting to SQL Server or mysql.connector, when you issue the query it loads all of the results into 
memory. This is great for speed, not so when your query may return 15GB worth of email data.

The code provided below is what I use for progressively querying out results without trying to pull all rows in a 
single shot. The method used for SQL Server is not overly effecient but it gets the job done. MySQL thankfully 
already provides the tools necessary to do exactly what we want.

## MySQL

In this example I am using a mysql.connector cursor to query data from a table, I make use of MySQL's ability to 
limit and offset query results to only query out a chunk of data at a time.

You may change the SELECT statement as you see fit, but for this to work you need to leave the `LIMIT {},{}` at 
the very end of the query.

```sql
# Offset and limit control how many records to query for at a single time.
offset = 0
limit = 10000
total_counter = 0

table_name = 'EMAIL_BODIES'

# Continuously query until we break the looping.
while True:

    # This is a standard query, you can easily add a WHERE clause just before LIMIT.
    cursor.execute("SELECT * FROM {} LIMIT {},{}".format(table_name, offset, limit))

    # If no rows were returned, stop the while loop
    if not cursor.rowcount:
        break

    # In this example, I am writing the entire rows data into a CSV file.
    for row in cursor.fetchall():
        csv_file.writerow(row)

    # Nothing more than a counter
    total_counter += cursor.rowcount

    # Leave this alone, it is what increments the offset for the next iteration
    offset += limit
```

## SQL Server

This one is a very different animal compared to MySQL. Unfortunately SQL Server does not provide any native way to 
offset result sets, so what we have to do is make use of CTE's and forcefully only grab a specific number of rows 
between two row numbers.

This method requires something extra, it requires you to have a unique column in the table. The way it works is it 
pulls your original query (on the server's side) and then orders by the column that contains unique data. If you 
don't have a uniquely identifiable column this method will not output values correctly. Typically when you build a 
database you'll add a column with something like IDENTIFY(1,1) which is the same thing as MySQL's AUTO_INCREMENT, 
you would want to use that column.

If you need to modify the SELECT statement that is pulling the data you want to export, you will need to 
modify: `WITH Results_CTE AS (SELECT *, ROW_NUMBER() OVER (ORDER BY {3}) AS RowNum FROM {0})` you may change the 
asterisk to any column names you wish and you can add a WHERE statement after the `FROM {0}` part, but do not 
modify the rest.


```sql
# Offset and limit control how many records to query for at a single time.
offset = 0
limit = 10000
total_counter = 0

table_name = 'EMAIL_BODIES'
unique_table_column = 'email_id'

# Query 1 row for column titles. This this alone.
cursor.execute("SELECT TOP 1 * FROM {}".format(table_name))

# Obtaining the column titles
columns = [row.column_name for row in cursor.columns(table=table_name)]

# Continuously query until we break the looping.
while True:

    cursor.execute("""
    WITH Results_CTE AS (SELECT *, ROW_NUMBER() OVER (ORDER BY {3}) AS RowNum FROM {0})
    SELECT * FROM Results_CTE WHERE RowNum &gt;= {1} AND RowNum &lt; {1} + {2}
    """.format(table_name, offset, limit, unique_table_column))

    counter = 0
    for row in cursor.fetchall():
        counter += 1

        row_data = [getattr(row, column) for column in columns]

        # In this example we are writing the rows data to a csv file.
        csv_file.writerow(row_data)

    # If the counter is still 0 it means our query returned no results.
    if not counter:
        break

    total_counter += counter

    offset += limit
```

## SQL Server Updated 2016-09-10
This newer method does not require a CTE or limiting as it leaves the data on the SQL Server and obtains the 
rows individually. So far in my testing this has been faster than the CTE.

```sql
table_name = 'EMAIL_BODIES'

# Query 1 row for column titles. This this alone.
cursor.execute("SELECT TOP 1 * FROM {}".format(table_name))

# Obtaining the column titles
columns = [row.column_name for row in cursor.columns(table=table_name)]

cursor.execute("SELECT * FROM {}".format(table_name))

while True:
    row = cursor.fetchone()
    if not row:
        break

    row_data = [getattr(row, column) for column in columns]

    # In this example we are writing the rows data to a csv file.
    csv_file.writerow(row_data)
```
