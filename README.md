# Data Scientist with Python
Career track "Data Scientist with Python" at DataCamp.

## I. Importing Data in Python

In this part, you'll find out the many ways to import data into Python: from flat files such as .txt and .csv; from files native to other software such as Excel spreadsheets, Stata, SAS, and MATLAB files; and from relational databases such as SQLite and PostgreSQL.

1. Read flat files: [use NumPy](#121-using-numpy), or [use Pandas](#122-using-pandas) method `read_csv()`.
2. [Read pickle files](#21-pickle-files): use the package `pickle`.
3. [Read Excel files](#22-excel-files): use Pandas method `read_excel()`, or Pandas class `ExcelFile`.
4. [Read SAS files](#23-sas-files): use the package `sas7bdat`.
5. [Read Stata files](#24-stata-files): use Pandas method `read_stata()`.
6. [Read HDF5 files](#25-hdf5-files): use the package `h5py`.
7. [Read MATLAB files](#26-matlab-files): use the module `scpy.io`.
8. Relational databases: query data [using a connection](#32-query-data-using-a-connection), or [using Pandas](#33-query-data-using-pandas-method) method `read_sql_query()`.

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
- all columns have the same data type: use the method [`loadtxt()`](#using-the-method-loadtxt).
- columns have different data types: use one of methods: [`genfromtxt()`](#using-the-method-genfromtxt), [`recfromcsv()`](#using-the-method-recfromcsv), or `recfromtxt()`.

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
This method derives from the method `genfromtxt()` and it has:
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

### 2. Other files

In the first section, we know how to import flat files, but there are many other file types we will potentially have to work with as a data scientist. In this section, we'll figure out how to import data into Python from a wide array of important file types. These include pickled files, Excel spreadsheets, SAS and Stata files, HDF5 files, a file type for storing large quantities of numerical data, and MATLAB files.

#### 2.1 Pickle files

A pickle file contain data serialized into a sequence of bytes.

Some datatypes that cannot be saved easily to flat files, such as lists and dictionaries, that's why we have pickle file (dictionaries can be serialized in JSON file which is easier for human reader).
```Python
import pickle
# read data
with open(pickle_file, "rb") as fp:
    data = pickle.load(fp)
print(data)

# serialize data
with open(pickle_file, "wb") as fp:
    pickle.dump(data, fp, protocol=pickle.HIGHEST_PROTOCOL)
```
- The advantage of `HIGHEST_PROTOCOL` is that files get smaller. This make unpickling sometimes much faster.
- The **maximum file size** of pickle is about 2GB.
- The type of returned data is the type of the data serialized.

#### 2.2 Excel files

We use Pandas to read Excel files.
```Python
import pandas as pd
```

##### Using the method `read_excel()`
```Python
# read the first sheet
df = pd.read_excel(battle_file, engine="openpyxl")
df.head()
```
- The latest version of xlrd (2.0.1) only supports `.xls` files. The reason `xlsx` support was removed is because it had potential security vulnerabilities and no-one was maintaining it.
- The engine `openpyxl` is used to read `.xlsx` file.
- By default, it **reads the first sheet** of input Excel file.
- The returned data is a dataframe.

```Python
# read all sheets
data = pd.read_excel(battle_file, engine="openpyxl", sheet_name=None)
```
- `sheet_name=None` to read all sheets in the Excel file.
- The returned data is a dictionary which has keys contain sheet names, values are dataframes.
```Python
sheet_names = data.keys()
print(sheet_names)
# explorer data
display(data["2002"].head())
display(data["2004"].head())
```

##### Using the class `ExcelFile()`
```Python
xls = pd.ExcelFile(battle_file, engine="openpyxl")
# get sheet names
sheet_names = xls.sheet_names
print(sheet_names)
# read a sheet by its index
df = xls.parse(0)
display(df.head())
# read a sheet by its name
df = xls.parse("2004")
display(df.head())
```
- The class returns an ExcelFile object.
- Its property `sheet_names` returns the names of sheets of input Excel file.
- Its method `parse()` gets an index of sheet, or a sheet name and returns a dataframe.

#### 2.3 SAS files

SAS file is a Statistical Analysis Software file. It has extension `.sas7bdat` (storing dataset) or `.sas7bcat` (storing SAS catalog).

We use the package `sas7bdat` to read SAS files.
```Python
from sas7bdat import SAS7BDAT
```
The method `to_data_frame()` returns the data of input SAS file in a dataframe.
```Python
with SAS7BDAT(sales_file) as fp:
    df = fp.to_data_frame()
display(df.head())
print(df.shape)
```

#### 2.4 Stata files

Stata file stores statistic and data. It has extension `.dta`.

We use the Pandas method `read_stata()` to read Stata files.
```Python
df = pd.read_stata(disarea_file)
display(df.head())
print(df.shape)
```

The returned datatype is a dataframe.

#### 2.5 HDF5 files

HDF5 stands for Hierarchical Data Format version 5. It is standard for storing large quatities of numerical data. Datasets can be hundreds of gigabytes or terabytes. HDF5 can scale to exabytes.

We use the package `h5py` to read HDF5 files.
```Python
import h5py
data = h5py.File(ligo_file, "r")
```

The returned datatype is h5py File. It contains keys and array-likely data.
```Python
print(data.keys())
print(data["strain"].keys())
# access the data
strain = data["strain"]["Strain"]
nb_samples = int(1e4)
gps_time = np.arange(0, 1, 1/nb_samples)
plt.plot(gps_time, strain[:nb_samples])
plt.xlabel("GPS Time (s)")
plt.ylabel("Strain")
plt.show()
```

#### 2.6 MATLAB files

MATLAB data is saved in files with extension `.mat`.

We use the module `scipy.io` to read MATLAB files.
```Python
import scipy.io
mat = scipy.io.loadmat(albeck_file)
```
The return data is a dictionary. Its keys are variables in MATLAB environment. Its values contain the data of those variables.
```Python
print(type(mat))
print(mat.keys())
```

### 3. Relational databases

In this section, we'll learn how to extract meaningful data from relational databases, an essential skill for any data scientist.

We use the package `sqlalchemy` since it works with many Relational Database Management Systems (RDMS).

```Python
from sqlalchemy import create_engine
```

#### 3.1 Create a database engine

The Engine is the starting point for any SQLAlchemy application.
```Python
db_url = "sqlite:///data/Chinook.sqlite"
engine = create_engine(db_url)
```
```Python
# get table names
table_names = engine.table_names()
print(table_names)
```

#### 3.2 Query data using a connection
```Python
query = "SELECT * FROM Album"
with engine.connect() as conn:
    rs = conn.execute(query)
    df = pd.DataFrame(rs.fetchall())
    df.columns = rs.keys()
display(df.head())
print(len(df))
```

#### 3.3 Query data using Pandas method
```Python
query = "SELECT * FROM Album"
df = pd.read_sql_query(query, engine)
display(df.head())
print(len(df))
```

The complete source code of this part is in the notebook [14_Introduction to Importing Data in Python](14_Introduction%20to%20Importing%20Data%20in%20Python.ipynb).

My statement of accomplishment is at [here](https://www.datacamp.com/statement-of-accomplishment/course/c7d72f1f865f9da933cfaf7431b56ede104c2f5e).