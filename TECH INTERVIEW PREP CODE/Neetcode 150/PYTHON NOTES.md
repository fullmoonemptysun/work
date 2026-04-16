## Unpacking operator & 
```python
(*array) 
```
Interesting piece of code. Unpacks the array and passes the contents.
```python
print(*array)
```
Will print contents of the array but without braces because they have been directly passed to print and not as a list.
```python
zip(*matrix)

```
unpacks the inner arrays and passes them to zip, which makes tuples out of the columns.