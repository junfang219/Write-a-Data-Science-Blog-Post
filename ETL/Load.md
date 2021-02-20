## Read CSV

```python
import pandas as pd
df_projects = pd.read_csv('../data/projects_data.csv', sep=',', dtype=str)
df_population = pd.read_csv('../data/population_data.csv', skiprows=4)
df_projects.isnull().sum() #by column
df_population.isnull().sum(axis=1) #by row
df_projects.shape
df_population = df_population.drop('Unnamed: 62', axis=1) # drop by column
df_population[df_population.isnull().any(axis=1)] # any row contains a null

# Output the df_merged data frame as a csv file
# If you do not want the data frame indices in your result, use index=False
df_merged.to_csv('countrydata.csv', index=False)
```





```python
f = open('population_data.csv')
for i in range(10):
    line = f.readline()
    print('line: ', i,  line)
f.close() 


```



## Read JSON

```python
import pandas as pd
df_json = pd.read_json('population_data.json', orient='records')

# Output the df data frame as a json file
df_merged.to_json('countrydata.json', orient='records')

```

```latex
'split' : dict like {index -> [index], columns -> [columns], data -> [values]}

'records' : list like [{column -> value}, ... , {column -> value}]

'index' : dict like {index -> {column -> value}}

'columns' : dict like {column -> {index -> value}}

'values' : just the values array
```

```python
import json

# read in the JSON file
with open('population_data.json') as f:
    json_data = json.load(f)

# print the first record in the JSON file
print(json_data[0])
print('\n')
```



## Read XML

```xml
<record>
  <field name="Country or Area" key="ABW">Aruba</field>
  <field name="Item" key="SP.POP.TOTL">Population, total</field>
  <field name="Year">1960</field>
  <field name="Value">54211</field>
</record>
```



```python
# import the BeautifulSoup library
from bs4 import BeautifulSoup

# open the population_data.xml file and load into Beautiful Soup
with open("population_data.xml") as fp:
    soup = BeautifulSoup(fp, "lxml") # lxml is the Parser type
    
 
# output the first 5 records in the xml file
# this is an example of how to navigate the XML document with BeautifulSoup

i = 0
# use the find_all method to get all record tags in the document
for record in soup.find_all('record'):
    # use the find_all method to get all fields in each record
    i += 1
    for record in record.find_all('field'):
        print(record['name'], ': ' , record.text)
    print()
    if i == 5:
        break
```



```python
data_dictionary = {'Country or Area':[], 'Year':[], 'Item':[], 'Value':[]}
all_records = soup.find_all('record')

for record in all_records:
    for field in record.find_all('field'):
        data_dictionary[field['name']].append(field.text)

df = pd.DataFrame.from_dict(data_dictionary)
df = df.pivot(index='Country or Area', columns='Year', values='Value')
df.reset_index(level=0, inplace=True)
```



## Extract Data from SQL Databases

SQLite

```python
import sqlite3
import pandas as pd

# connect to the database
conn = sqlite3.connect('population_data.db')

# run a query
pd.read_sql('SELECT * FROM population_data', conn)
pd.read_sql('SELECT "Country_Name", "Country_Code", "1960" FROM population_data', conn)

```



MySQL or PostgreSQL

```python
import pandas as pd
from sqlalchemy import create_engine

### 
# create a database engine 
# to find the correct file path, use the python os library:
#import os
#print(os.getcwd())
#
###

engine = create_engine('sqlite:////home/workspace/3_sql_exercise/population_data.db')
pd.read_sql("SELECT * FROM population_data", engine)
```



## Download from APIs 

## (Application programming interface)

```python
import requests
import pandas as pd

url = 'http://api.worldbank.org/v2/countries/br;cn;us;de/indicators/SP.POP.TOTL/?format=json&per_page=1000'
r = requests.get(url)
r.json()

```



## Output the df dataframe as a sqlite database file.

```python
import sqlite3

# connect to the database
# the database file will be worldbank.db
# note that sqlite3 will create this database file if it does not exist already
conn = sqlite3.connect('worldbank.db')

# TODO: output the df_merged dataframe to a SQL table called 'merged'.
# HINT: Use the to_sql() method
# HINT: Use the conn variable for the connection parameter
# HINT: You can use the if_exists parameter like if_exists='replace' to replace a table if it already exists

df_merged.to_sql('merged', con = conn, if_exists='replace', index=False)

# commit any changes to the database and close the database
conn.commit()
conn.close()

```



SQLite, as its name would suggest, is somewhat limited in its functionality. For example, the Alter Table command only allows you to change a table name or to add a new column to a table. You can't, for example, add a primary key to a table once the table is already created.

If you want more control over a sqlite3 database, it's better to use the sqlite3 library directly. Here is an example of how to use the sqlite3 library to create a table in the database, insert a value, and then run a SQL query on the database. Run the code cells below to see the example.

```python
import sqlite3
# connect to the data base
conn = sqlite3.connect('worldbank.db')

# get a cursor
cur = conn.cursor()

# drop the test table in case it already exists
cur.execute("DROP TABLE IF EXISTS test")

# create the test table including project_id as a primary key
cur.execute("CREATE TABLE test (project_id TEXT PRIMARY KEY, countryname TEXT, countrycode TEXT, totalamt REAL, year INTEGER);")

# insert a value into the test table
cur.execute("INSERT INTO test (project_id, countryname, countrycode, totalamt, year) VALUES ('a', 'Brazil', 'BRA', '100,000', 1970);")

# commit changes made to the database
conn.commit()

# select all from the test table
cur.execute("SELECT * FROM test")
cur.fetchall()

# commit any changes and close the data base
conn.close()
```

```
[('a', 'Brazil', 'BRA', '100,000', 1970)]
```



```python
# connect to the data base
conn = sqlite3.connect('worldbank.db')

# get a cursor
cur = conn.cursor()

# drop tables created previously to start fresh
cur.execute("DROP TABLE IF EXISTS test")
cur.execute("DROP TABLE IF EXISTS indicator")
cur.execute("DROP TABLE IF EXISTS projects")
cur.execute("DROP TABLE IF EXISTS gdp")
cur.execute("DROP TABLE IF EXISTS population")

# TODO create the projects table including project_id as a primary key
# HINT: Use cur.execute("SQL Query")
cur.execute("CREATE TABLE projects (project_id TEXT PRIMARY KEY, countryname TEXT, countrycode TEXT, totalamt REAL, year INTEGER);")

# TODO: create the gdp table including (countrycode, year) as primary key
# HINT: To create a primary key on multiple columns, you can do this:
# CREATE TABLE tablename (columna datatype, columnb datatype, columnc dataype, PRIMARY KEY (columna, columnb));
cur.execute("CREATE TABLE gdp (countryname TEXT, countrycode TEXT, year INTEGER, gdp REAL, PRIMARY KEY (countrycode, year));")

# TODO: create the gdp table including (countrycode, year) as primary key
cur.execute("CREATE TABLE population (countryname TEXT, countrycode TEXT, year INTEGER, population REAL, PRIMARY KEY (countrycode, year));")

# commit changes to the database. Do this whenever modifying a database
conn.commit()
```



```python
# TODO:insert project values into the projects table
# HINT: Use a for loop with the pandas iterrows() method
# HINT: The iterrows() method returns two values: an index for each row and a tuple of values
# HINT: Some of the values for totalamt and year are NaN. Because you've defined
# year and totalamt as numbers, you cannot insert NaN as a value into those columns.
# When totaamt or year equal NaN, you'll need to change the value to something numeric
# like, for example, zero

for index, values in df_projects.iterrows():
    project_id, countryname, countrycode, totalamt, year = values
    
    if totalamt == 'nan':
        totalamt = 0
    if year == 'nan':
        year = 0
    
    sql_string = 'INSERT INTO projects (project_id, countryname, countrycode, totalamt, year) VALUES ("{}", "{}", "{}", {}, {});'.format(project_id, countryname, countrycode, totalamt, year)
    cur.execute(sql_string)

conn.commit()
```



```python
# run this command to see if your tables were loaded as expected
sqlquery = "SELECT * FROM projects JOIN gdp JOIN population ON projects.year = gdp.year AND projects.countrycode = gdp.countrycode AND projects.countrycode = population.countrycode AND projects.year=population.year;"
result = pd.read_sql(sqlquery, con=conn)
result.shape

# commit any changes and close the database
conn.commit()
conn.close()
```

