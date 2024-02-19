# PG Library

A small class for interacting with Postgres databases. Developed for the CS 495 TV Manager project.

## Initialize

Pass a dictionary with connection credentials when instantiating the PGDB object.
```
conn_dict: dict = {
    "user" : "",        # default $USER
    "password" : "",    # default None
    "host" : "",        # default localhost
    "port" : "",        # default 5432
    "dbname" : ""       # default $USER
}
```
All of the connection params are optional, and default to the regular Postgres defaults (commented above).

Optional args are read_only connection (default True) and an SQL directory where the object will check for queries stored as as readable files (default None).


## Usage

You can execute a query from a file, from a raw string, from a decorated function, or run a very simple select query. As with the actual Postgres database, schema is always an optional argument, and always defaults to "public" if not provided. Results of a query are always returned as a list of dictionaries, in which the column name maps to the value.

execute_file_query(): If no SQL directory is passed when the object is created, then it will assume you're passing with an absolute file path. If there is no file extension included in the method call, then it will assume it's looking for a .sql file. If you did pass an SQL directory at instantiation, and you're using .sql file extensions, you can just pass the name of the file as a string without path or extension. For example, if you initialized the PGDB object with sql_dir = '/home/app/SQL', you can execute the query in '/home/app/SQL/my_query.sql' with execute_file_query('my_query').

execute_str_query(): Execute a raw string query.

str_to_query(): Use this to decorate a function that returns a string. Could be useful for mildly dynamic queries. For example (assuming you have already created a PGDB object called "pg_interface"):

```
@pg_interface.str_to_query
def foo(cols: list[str], identifier: str) -> str:
    columns = ', st.'.join(cols)

    query = f"""select st.{columns}
            from schema.table_name st
            where st.id = '{identifier}'"""
    
    return query
```

Using the decorator, foo() will return the result of the query.

get_columns(): Not a query function. Will return a list of {column_name : python_type} for the specified schema/table.

get_rows(): Will return the results of a query that selects the specified columns from the specified schema/table.


There are no methods for doing joins or window functions- I want to keep any logic that's even remotely complex in a version controlled .sql file, or let the user wrap the logic in a decorated function. There's also nothing for creates/updates/deletes or anything like that- I want to leave the real ELT to another application or library or framework better suited for it. You can use the get_rows() method for very simple, unqualified selects from a table (or, preferably, a view or function).