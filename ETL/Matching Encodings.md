## Matching Encodings



```python
# import the chardet library
import chardet 

# use the detect method to find the encoding
# 'rb' means read in the file as binary
with open("mystery.csv", 'rb') as file:
    print(chardet.detect(file.read()))
```

```
{'encoding': 'UTF-16', 'confidence': 1.0, 'language': ''}
```



```python
from encodings.aliases import aliases

alias_values = set(aliases.values())
#alias_values
# TODO: iterate through the alias_values list trying out the different encodings to see which one or ones work
# HINT: Use a try - except statement. Otherwise your code will produce an error when reading in the csv file
#       with the wrong encoding.
# HINT: In the try statement, print out the encoding name so that you know which one(s) worked.
for encoding in alias_values:
    try:
        df = pd.read_csv('mystery.csv', encoding = encoding)
        print('sucessful', encoding)
    except:
        pass
  
```

```
sucessful utf_16
sucessful utf_16_le
sucessful utf_16_be
```

