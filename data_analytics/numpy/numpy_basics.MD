# Numpy basics

### What is it?

[Numpy Documentation](https://numpy.org/doc/2.2/)

NUMerical PYthon. A package for handing multidimensional arrays, used under the hood by many machine learning packages. Supports linear algebra and other mathematical functions. It's fast (written in C/C++). Can also store and process images.

```arr = np.array([1, 2, 3])``` - create a numpy array from a python list

If the python list have more than one data type, they will be coerced into one type by numpy.

```arr.dtype``` checks the data type: O is object, \<U5 indicates a unicode string less than 5 characters

### Built in methods

```np.arange(start=0, stop=50, step=10)``` similar to python range

```np.zeros(shape=(x, y .... z))``` create an array of 0's with the supplied shape

```np.linspace(start=1, stop=100, num=17)``` create equally spaced items

```np.random.rand(2, 2)``` - create an array of random numbers from 0 to 1

```np.random.randn(8, 3)``` - create a standard normal distribution

```np.random.randint(low=10, high=30, size=4)``` -random integers, size can be a tuple

```np.random.randint(4, 10, size=(2, 3, 4))```

- Set a seed for the generator

```np.random.seed(seed=1)```

```arr = np.random.randint(low=10,high=50,size=5)```

```arr```

- Reshaping

```arr = np.array([1, 2, 3, 4])```

```arr.reshape(2, 2)```

- use -1 to indicate an unknown dimemsion

```arr.reshape(2, -1)```

- min and max

```arr.min()```

```arr.argmin()``` index position of min

```arr.argmin(axis=n)``` index position of min on given axis

- array type and size

```arr.nbytes``` gives size in bytes

```arr2 = np.array(arr, dtype=int8)``` reduce the memory footprint

```arr.ndim``` returns the number of dimensions of the array

#### Filling arrays with constant values
- 1d array
```arr = np.empty(10, dtype=int)``` create empty array first

```arr.fill(12)``` fill this array after creating

- 2d array
```arr = np.full((4, 5), 12)``` create filled array

#### Changing the elements of an array
```arr = np.insert(arr, 2, 10)``` insert 10 at index 2

```arr = np.append(arr, 23)``` Append 23 to the array

```arr = np.delete(arr, 2)``` Delete the element at index 2

Deleting an element from a multidimensional array 
```arr = np.delect(arr, 2, axis=0)```

#### Reshaping an array
```arr = arr.reshape(arr, (3, 4))```

```arr = arr.flatten()``` creates a copy

```new_vew = arr.ravel()``` creates a view, maintains the connection

#### Joining and splitting arrays


#### Unique elements
```arr = np.random.randint(1, 11, 20)```

```np.unique(arr, return_index=True, return_counts=True```

#### Transposing, moving and swapping axes
- Transpose

```np.transpose(2darr)```

- Move axis

```np.moveaxis(arr, 0, -1)``` Move the axis currently at position 0 to the end of the list of axes. So arr.shape (2, 3, 4) becomes arr.shape (3, 4, 2)

- Swap axes

```np.swapaxes(arr, 0, 3)``` Similar, but swaps the positions of first and 4th axes. Again, examine arr.shape before and after to see the change












