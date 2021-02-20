## Combining data

```python
# TODO: import the pandas library
import pandas as pd
# TODO: read in each csv file into a separate variable
df_rural = pd.read_csv('rural_population_percent.csv', skiprows=4)
df_electricity = pd.read_csv('electricity_access_percent.csv', skiprows=4)

# TODO: remove the 'Unnamed:62' column from each data set
df_rural.drop('Unnamed: 62', axis=1, inplace=True)
df_electricity.drop('Unnamed: 62', axis=1, inplace=True)

# TODO: combine the two data sets together using the concat method
# In other words, all of the rows of df_rural will come first
# followed by all the rows in df_electricity. This is possible to do
# because they both have the same column names.
pd.concat([df_rural, df_electricity])

df_merged = df_projects.merge(df_indicator, how='left', on=['countrycode', 'year'])
```

```python
# TODO: merge the data sets together according to the instructions. First, use the 
# melt method to change the formatting of each data frame so that it looks like this:
# Country Name, Country Code, Year, Rural Value
# Country Name, Country Code, Year, Electricity Value
df_rural_melt = pd.melt(df_rural,\
                        id_vars=['Country Name', 'Country Code', 'Indicator Name', 'Indicator Code'],\
                       var_name = 'Year', value_name='Electricity_Value')

df_electricity_melt = pd.melt(df_electricity,\
                              id_vars=['Country Name', 'Country Code', 'Indicator Name', 'Indicator Code'],\
                             var_name='Year', value_name='Rural_Value')

# TODO: drop any columns from the data frames that aren't needed
df_rural_melt.drop(['Indicator Name', 'Indicator Code'], axis=1, inplace=True)
df_electricity_melt.drop(['Indicator Name', 'Indicator Code'], axis=1, inplace=True)

# TODO: merge the data frames together based on their common columns
# in this case, the common columns are Country Name, Country Code, and Year
df_merge = df_rural_melt.merge(df_electricity_melt, how='outer',\
                               on=['Country Name', 'Country Code', 'Year'])

# TODO: sort the results by country and then by year

df_combined = df_merge.sort_values(['Country Name', 'Year'])
```

