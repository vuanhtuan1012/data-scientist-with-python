# Data Scientist with Python
Career track "Data Scientist with Python" at DataCamp.

## I. Importing Data in Python

In this part, you'll find out the many ways to import data into Python: from flat files such as .txt and .csv; from files native to other software such as Excel spreadsheets, Stata, SAS, and MATLAB files; and from relational databases such as SQLite and PostgreSQL.

1. [Read flat files by using NumPy](#121-using-numpy)
2. [Read flat files by using Pandas](#122-using-pandas)

### 1. Flat files

Flat files are text files containing records. That is table data.
-   file extension: `.csv` (comma separated values), `.txt`
-   record: row of fields (attributes)
-   column: feature (attribute)
-   delimiters: commas, tabs

#### 1.1 Read text files
- Import entire file
```Python
filepath= "data/moby_dick.txt"
with open(filepath, "r") as fp:
    data = fp.read()
print(data)
```

- Import file line by line
```Python
nb_lines = 4  # read the first 4 lines
with open(filepath, "r") as fp:
    for i in range(nb_lines):
        line = fp.readline()
        print(line)
```

#### 1.2 Read flat files

##### 1.2.1 Using NumPy
- all columns have the same data type: use the method `loadtxt()`.
- columns have different data types: use one of methods: `genfromtxt()`, `recfromcsv()`, or `recfromtxt()`.

###### Using the method `loadtxt()`
This method reads data in a file then returns a numpy array. It **requires all elements having the same data type**. By default, the data type of the output array is `float`. We can change it by giving value to the parameter `dtype` of the method.

- data doesn't have headers.
```Python
import numpy as np
data = np.loadtxt(digits_file, delimiter=",")
data[:5,:]
```
- data has headers, and we skip the header row.
```Python
data = np.loadtxt(digits_header_file, delimiter="\t", skiprows=1)
data[:5,:]
```
The parameter `skiprows` specifies **how many rows to skip**. In this case, we only skip the first row containing headers.

Another useful parameter is `usecols`. This parameter mentions the **indices of columns to keep**.
```Python
data = np.loadtxt(digits_file, delimiter=",", usecols=[0, 2])
data[:5,:]
```
The code above keeps only data in the first and the third columns.

- data has headers, and we keep it with data.
```Python
data_header = np.loadtxt(sea_slug_file, delimiter="\t", dtype="str")
header, data = data_header[0,:], data_header[1:,:].astype("float")
```
In the code above we firstly specify the data type of all elements is `str`, then cast the data type of number elements to `float` after.

###### Using the method `genfromtxt()`

This method can read flat files in which **columns have different data types** by indicating the value of the parameter `dtype` is `None`.

The resulting returned is a structured array object where each element of the array is a row of the flat file imported.

In the data returned, we can:
- access row by the index, for example, `data[2]`
- access column by the column name, for example, `data["Pclass"]`
- access an element by row index & column name/column index, for example, `data[2]["Pclass"]`, `data[2][2]`
```Python
data = np.genfromtxt(titanic_file, delimiter=",", names=True, dtype=None, encoding=None)
# get headers
headers = list(data.dtype.fields.keys())
print(headers)
# explorer data
print(data.shape)
print(data[:5])
print(data["Pclass"])
print(data[2]["Pclass"])
```
Parameters:
- `names`: True, specifying the file has headers
- `dtype`: None, data types will be determined by the contents of each column individually
- `encoding`: None, use the system default encoding to decode the file.

###### Using the method `recfromcsv()`
This method is similar to the method `genfromtxt()` except that:
- the `delimiter` is set to comma `,` by default.
- the `dtype` is set to `None` by default.
- the `names` is set to `True` by default.
- headers are in lower case.
```Python
data = np.recfromcsv(titanic_file, encoding=None)
# get headers
headers = list(data.dtype.fields.keys())
print(headers)
# explorer data
print(data.shape)
print(data[:5])
print(data["pclass"])
print(data[2]["pclass"])
```
##### 1.2.2 Using Pandas

The method `read_csv()` is used to read flat files. By default, it considers:
- the file has headers.
- the delimiter is comma.
- data types of columns will be determined by the contents individually.

The resulting returned is a DataFrame.
```Python
import pandas as pd
df = pd.read_csv(titanic_file)
# explorer data
display(df.head())
print(df.dtypes)
print(df.shape)
# get headers
headers = list(df.columns)
print(headers)
```

Convert a dataframe to numpy array by using its property `values`.
```Python
data = df.values
```

If we want to read a flat file that don't have headers or non comma delimiter, specify them in corresponding parameters `headers`, and `sep`.
```Python
# read file no headers
df = pd.read_csv(digits_file, header=None)
display(df.head())
print(df.shape)

# read file non-comma delimiter
df = pd.read_csv(sea_slug_file, sep="\t")
display(df.head())
print(df.shape)
```

The complete source code of this part is in the notebook [14_Introduction to Importing Data in Python](14_Introduction%20to%20Importing%20Data%20in%20Python.ipynb)