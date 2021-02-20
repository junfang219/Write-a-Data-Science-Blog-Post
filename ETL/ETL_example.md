```python
# Generator for reading in one line at a time
# generators are useful for data sets that are too large to fit in RAM

def extract_lines(file):
    while True:
        line = file.readline()
        if not line:
            break
        yield line
```



```python

# transform the indicator data
def transform_indicator_data(data, colnames):
    
    # get rid of quote marks
    for i, datum in enumerate(data):
        data[i] = datum.replace('"','')
    
    country = data[0]
    
    # filter out values that are not countries
    non_countries = ['World',
     'High income',
     'OECD members',
     'Post-demographic dividend',
     'IDA & IBRD total',
     'Low & middle income',
     'Middle income',
     'IBRD only',
     'East Asia & Pacific',
     'Europe & Central Asia',
     'North America',
     'Upper middle income',
     'Late-demographic dividend',
     'European Union',
     'East Asia & Pacific (excluding high income)',
     'East Asia & Pacific (IDA & IBRD countries)',
     'Euro area',
     'Early-demographic dividend',
     'Lower middle income',
     'Latin America & Caribbean',
     'Latin America & the Caribbean (IDA & IBRD countries)',
     'Latin America & Caribbean (excluding high income)',
     'Europe & Central Asia (IDA & IBRD countries)',
     'Middle East & North Africa',
     'Europe & Central Asia (excluding high income)',
     'South Asia (IDA & IBRD)',
     'South Asia',
     'Arab World',
     'IDA total',
     'Sub-Saharan Africa',
     'Sub-Saharan Africa (IDA & IBRD countries)',
     'Sub-Saharan Africa (excluding high income)',
     'Middle East & North Africa (excluding high income)',
     'Middle East & North Africa (IDA & IBRD countries)',
     'Central Europe and the Baltics',
     'Pre-demographic dividend',
     'IDA only',
     'Least developed countries: UN classification',
     'IDA blend',
     'Fragile and conflict affected situations',
     'Heavily indebted poor countries (HIPC)',
     'Low income',
     'Small states',
     'Other small states',
     'Not classified',
     'Caribbean small states',
     'Pacific island small states']
    
    if country not in non_countries:
        data_array = np.array(data, ndmin=2)
        data_array.reshape(1, 63)
        df = pd.DataFrame(data_array, columns=colnames).replace('', np.nan)
        df.drop(['\n', 'Indicator Name', 'Indicator Code'], inplace=True, axis=1)

        # Reshape the data sets so that they are in long format
        df_melt = df.melt(id_vars=['Country Name', 'Country Code'], 
                            var_name='year', 
                            value_name='gdp')
        
        results = []
        for index, row in df_melt.iterrows():
            country, countrycode, year, gdp = row
            if str(gdp) != 'nan':
                results.append([country, countrycode, year, gdp])
        return results
    

```



```python
# TODO: fill out the code wherever you find a TODO in this cell

def load_indicator_data(results):
    conn = sqlite3.connect('worldbank.db')
    cur = conn.cursor()
    if results:
        for result in results:
            countryname, countrycode, year, gdp = result

            sql_string = 'INSERT INTO gdp (countryname, countrycode, year, gdp) VALUES ("{}", "{}", {}, {});'.format(countryname, countrycode, year, gdp)

            # connect to database and execute query
            try:
                cur.execute(sql_string)
            except Exception as e:
                print('error occurred:', e, result)
            
    conn.commit()
    conn.close()
    
    return None
```

```python
# Execute this code cell to run the ETL pipeline
# You do not need to change anything in this cell
with open('../data/gdp_data.csv') as f:
    for line in extract_lines(f):
        data = line.split(',')
        if len(data) == 63:
            if data[0] == '"Country Name"':
                colnames = []
                # get rid of quote marks
                for i, datum in enumerate(data):
                    colnames.append(datum.replace('"',''))
            else:
                # transform and load the line of indicator data
                results = transform_indicator_data(data, colnames)
                load_indicator_data(results)
```

