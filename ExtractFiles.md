**CSV**

```python
import pandas as pd
df_projects = pd.read_csv('../data/projects_data.csv', sep=',', dtype=str)
```



Pandas tries to figure out programatically the data type of each column (integer, float, boolean, string). In this case, pandas could not automatically figure out the data type. That is because some columns have more than one possible data types. In other words, this data is messy. 

Therefore, dtype=str specifies the data type of each column.

**Print a few lines of the dataset**

```python
f = open('population_data.csv')
for i in range(10):
    line = f.readline()
    print('line: ', i,  line)
f.close() 
```

**Read CSV and skip a few lines**

```python
df_population = pd.read_csv('../data/population_data.csv', skiprows=4)
```



**JSON**

```python
import pandas as pd
df_json = pd.read_json('population_data.json', orient='records')
```

Orient can be 'split', 'index','records', and 'table' depending on the format of JSON file.

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_json.html

or read JSON as a dictionary

```python
import json

# read in the JSON file
with open('population_data.json') as f:
    json_data = json.load(f)
```



**XML**

Pandas cannot read XML directly because tag names are user defined.

```xml
<field name="Year">1961</field>
```

BeautifulSoup library read XML



```python
# use the find_all method to get all record tags in the document
data_dictionary = {'Country or Area':[], 'Year':[], 'Item':[], 'Value':[]}

for record in soup.find_all('record'):
    for record in record.find_all('field'):
        data_dictionary[record['name']].append(record.text)

df = pd.DataFrame.from_dict(data_dictionary)
df = df.pivot(index='Country or Area', columns='Year', values='Value')
df.reset_index(level=0, inplace=True)
```



**Sql** 

SQLite

```python
import sqlite3
import pandas as pd

# connect to the database
conn = sqlite3.connect('population_data.db')

# run a query
pd.read_sql('SELECT * FROM population_data', conn)
```

MySQL

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





**API**		application programming interface

```python
import requests
import pandas as pd

url = 'http://api.worldbank.org/v2/countries/br;cn;us;de/indicators/SP.POP.TOTL/?format=json&per_page=1000'
r = requests.get(url)
r.json()
```

