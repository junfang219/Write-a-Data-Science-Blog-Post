## Dummy variables



```python
# TODO: Create dummy variables from the sector1 data. Put the results into a dataframe called dummies
# Hint: Use the get_dummies method
dummies = pd.DataFrame(pd.get_dummies(sector['sector1']))

df_final = pd.concat([df, dummies], axis=1)
```

