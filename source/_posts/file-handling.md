---
title: File Handling (Python and C++)
date: 2020-07-06 11:19:26
tags: 
  - fileIO
categories:
  - Programming

---

All functions to handle file are provdied by operating system. In modern operating system, common programs are forbidden to read and write disk direcctly.
Hence, file handling is an operation which requests operating system to provide a file object and common programs operate files via handling the file object. 

# Python

## Create a File Object

To create a file object, we need to use the built-in `open` function. The `open` function returns the file object that contains methods and attributes to perform
various operations on the file.

```python
file_object = open("filename", "mode")
```
Here,

 - **filename**: gives name of the file that the file object has opened.
 - **mode**: attribute of a file object tells you which mode a file was opened in. 

## Read a File

Use the `open` function and gives the `r` attribute in **mode** as a flag, we could open a file in *read* mode.
```python
f = open("/Users/Carlos/test.txt", "r")
```
Note that `open` function will throw an `IoError` if the file does not exist and print the detailed information about errors.
```python
Traceback (most recent call last):
	File "<stdin>", line 1 in <module>
FileNotFoundError: [Errno 2] No such file or directory: '/Users/Carlos/test.txt'
```
if open the file successfully, use `f.read` to read file datat and store it in variable content.
```python
contents = f.read()
```
Finally, use `f.close` to close the file and release system resources. Note that each file should be closed correctly after being operated.
```python
f.close()
```
However, if `IOError` occurs while reading or writing the file object, the following `f.close` function will not be called. In order to close the file correctly, 
using `with` syntax block to open an file could gurantee that `close` function will be called whatever happens:
```python
with open('/Users/Carlos/test.txt', 'r') as f:
    contents = f.read()
```

### Read a File line by line

The `read` function may lead to memory leak since it will read and save all the contents of the file. Therefore, we could use another function `f.readlines()` or `f.read(size)` to read contents in fixed size.
```python
with open('/User/Carlos/test.txt', 'r') as f:
    lines = f.readlines()
    for line in lines:
        print(line)
```

## Write a File

Use the same `open` function and gives the `w` attribute in **mode** as a flag, wec could opend a file in *write* mode.
```python
f = open('/Users/Carlos/test.txt', 'w')
```
After opening a file object, `f.write` function could be called repeatedly to write contents into the file. Note that the file object have been closed correctly through `f.close` function after all contents being written in it.
```python
f.write('Hello, world!')
f.close()
```
`with` block also could be use in `w` mode:
```python
with open('/User/Carlos/test.txt', 'w') as f:
    f.write('Hello, world!')
```

## Append Data to a File

whatever we write will cover the existing contents in the file if we use `w` mode to open a file object. Hence we could use `a` mode to add a new text to the already existing file.
```python
f = open("/Users/Carlos/test.txt", "a+"):
f.write('Hello world!')
f.close()
```

## Files Modes in Python

| Mode | Description |
| :---:| :----------:|
| 'r'  | This is the default mode. It opens file for reading. |
| 'w'  | This Mode opens file for writing <br> If file does not exist, it creates a new files. <br> If file exists it truncates the file. |
| 'x'  | Creates a new file. If file already exists, the operation fails. |
| 'a'  | Open file in append mode. <br> if file does not exist, it creates a new file. |
| 't'  | This is the default mode. It opens in text mode. |
| 'b'  | This opens in binary mode. |
| '+'  | This will open a file for reading **and** writing. |

# C++

## Standard Library

In C++, the `fstream` standard library allows us to work with files.

To use the `fstream` library, include both the standard `<iostream>` **and** the `<fstream>` header file:

```c++
#include <iostream>
#include <fstream>
```

## Write a File

To create a file, use either the `ofstream` and `fstream` object, and specify the name of the file.

To write to the file, use the insertion operator (`<<`).

```c++
#include <iostream>
#include <fstream>

using namespace std;

int main() {
    // Create and open a text file
    ofstream MyFile("filename.txt");

    // Write to the file
    MyFile << "Files can be tricky, but it is fun enough!";

    // Close the file
    MyFile.close();
}
```

# References

- [name](url)
- [name](url)