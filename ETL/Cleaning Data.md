## Cleaning Data

```python
df_indicator[['Country Name', 'Country Code']].drop_duplicates()

df_projects['countryname'].unique()

df_projects['Official Country Name'] = df_projects['countryname'].str.split(";").str.get(0)


```



count # of occurance of duplicates

```python
# TODO: print out the dates that appeared twice in the results
unique_dates, count = np.unique(dates, return_counts=True)
#print(unique_dates, count)
for i in range(len(unique_dates)):
    if count[i] == 2:
        print(unique_dates[i])
```



```python
# TODO: In the sector1 variable, replace the string '!$10' with nan
#       Put the results back into the sector1 variable
# HINT: you can use the pandas replace() method and numpy.nan
sector['sector1']=sector['sector1'].replace('!$!0', np.nan)

sector['sector1'] = sector['sector1'].replace('!.+','',regex=True)

```

The replace method can take a regular expression. So df['data'].replace('?.+', regex=True) where '?.+' means find a set of characters that starts with a question mark is then followed by one or more characters. You can see a [regular expression cheat sheet](https://medium.com/factory-mind/regex-tutorial-a-simple-cheatsheet-by-examples-649dc1c3f285) here.

