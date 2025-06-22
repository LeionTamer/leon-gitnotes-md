## return versus yield

Unlike the return keyword which stops further execution of the function, the yield keyword continues to the end of the function.

## GIL - Global Interpreter Lock

The Python Global Interpreter Lock or GIL, in simple words, is a mutex (or a lock) that allows only one thread to hold the control of the Python interpreter.

## How to use .zip()

The zip() method in Python is used to combine multiple iterables (like lists or tuples) element-wise into pairs or tuples. It helps iterate over multiple lists simultaneously and is widely used in efficient looping

```python
list1 = ["Alice", "Bob", "Charlie"]
list2 = [25, 30, 35]

combined = zip(list1, list2)
print(list(combined))  # Output: [('Alice', 25), ('Bob', 30), ('Charlie', 35)]

for name, age in zip(list1, list2):
    print(f"{name} is {age} years old.")

names, ages = zip(*[('Alice', 25), ('Bob', 30), ('Charlie', 35)])
print(names)  # ('Alice', 'Bob', 'Charlie')
print(ages)   # (25, 30, 35)
```
