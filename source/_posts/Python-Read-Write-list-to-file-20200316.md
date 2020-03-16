---
title: Python 读写List到文件的方式
date: 2020-03-16 08:20:12
tags: [Python]
categories: [Python]
---

关于读写文件，在Python中提供了`read()`、`write()`来读写一行，也提供了`readlines()`、`writelines()`读写多行的方式。此外，模块`pickle`、`json`允许我们以更方便快捷的方式处理序列化的数据集。

### 使用read 和write方法

```python
# define list of places
places = ['Berlin', 'Cape Town', 'Sydney', 'Moscow']

with open('listfile.txt', 'w') as filehandle:
    for listitem in places:
        filehandle.write('%s\n' % listitem)
```

注意，最后一行添加了`\n`，从这个文件中读取列表，可以使用以下代码：

```python
# define an empty list
places = []

# open file and read the content in a list
with open('listfile.txt', 'r') as filehandle:
    for line in filehandle:
        # remove linebreak which is the last character of the string
        currentPlace = line[:-1]

        # add item to the list
        places.append(currentPlace)
```

一定要注意，需要把最后的换行符去掉。



### 使用readlines 和 writelines方法

写入文件：

```python
# define list of places
places_list = ['Berlin', 'Cape Town', 'Sydney', 'Moscow']

with open('listfile.txt', 'w') as filehandle:
    filehandle.writelines("%s\n" % place for place in places_list)
```



读取文件：

```python
# define empty list
places = []

# open file and read the content in a list
with open('listfile.txt', 'r') as filehandle:
    filecontents = filehandle.readlines()

    for line in filecontents:
        # remove linebreak which is the last character of the string
        current_place = line[:-1]

        # add item to the list
        places.append(current_place)
```

或者，以更精简的方式：

```python
# define empty list
places = []

# open file and read the content in a list
with open('listfile.txt', 'r') as filehandle:
    places = [current_place.rstrip() for current_place in filehandle.readlines()]
```





### 使用pickle模块

Its `dump()` method stores the list efficiently as a binary data stream. Firstly, in line 7 (in the code below) the output file `listfile.data` is opened for binary writing`"wb"`. Secondly, in line 9
the list is stored in the opened file using the `dump()` method.

```python
# load additional module
import pickle

# define a list of places
placesList = ['Berlin', 'Cape Town', 'Sydney', 'Moscow']

with open('listfile.data', 'wb') as filehandle:
    # store the data as binary data stream
    pickle.dump(placesList, filehandle)
```



We read the list from the file as follows. Firstly, the output file `listfile.data` is opened binary for reading `"rb"` in line 4. Secondly, the list of places is loaded from the file using the `load()` method.

```python
# load additional module
import pickle

with open('listfile.data', 'rb') as filehandle:
    # read the data as binary data stream
    placesList = pickle.load(filehandle)
```

`pickle` works with all kind of Python objects such as strings, numbers, self-defined structures, and every other built-in data structure Python provides.



### 使用JSON

The binary data format `pickle` uses is specific to Python. To improve the interoperability between different programs the JavaScript Object Notation ([JSON](https://en.wikipedia.org/wiki/JSON)) provides an easy-to-use and human-readable schema, and thus became very popular.

写入文件：

```python
import json

# define list with values
basicList = [1, "Cape Town", 4.6]

# open output file for writing
with open('listfile.txt', 'w') as filehandle:
    json.dump(basicList, filehandle)
```



读取文件：

```python
import json

# open output file for reading
with open('listfile.txt', 'r') as filehandle:
    basicList = json.load(filehandle)
```



参考:[Reading and Writing Lists to a File in Python](https://stackabuse.com/reading-and-writing-lists-to-a-file-in-python/)



