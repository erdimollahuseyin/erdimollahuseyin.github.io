---
layout: post
title: Python list tutorial
category: Python
redirect_from: /2019/12/09/python-list-tutorial/
---

<img src="/images/blog/2019-12/python-list-tutorial.jpeg" alt="Python List Tutorial" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

# Section Overview

- Lists
- Slices
- Exception Handling
- Looping through a list
- Sorting and Ranges
- Ranges

## Section 1. Lists

- Lists are one of the most powerful data types in Python.
- It is a data type that holds an ordered collection of items.
- The items can be of various data types. You can even have lists of lists.

Lists are created using comma separated values betweeen square brackets. `The format is`:

```python
list_name = [item_1, item_2, ..., item_N]
list_name = []
```

Using our understanding of Python types, we might think we could store each data point in its own variable. For instance, this is how we might store the first row’s data points:

```python
first_name = 'Erdi'
last_name = 'Mollahüseyin'
email = 'erdimollahuseyinoglu@gmail.com'
age = 29
```

Above, we stored:

- The first name "Erdi" as a string
- The last name "Mollahüseyin" as a string
- The email "erdimollahuseyinoglu@gmail.com" as a string
- The age 29 as a int

We can store data more efficiently using lists. This is how we can create a list of data points for the first row:

```python
user_row_1 = ['Erdi', 'Mollahüseyin', 'erdimollahuseyinoglu@gmail.com', 29]
print(user_row_1)
['Erdi', 'Mollahüseyin', 'erdimollahuseyinoglu@gmail.com', 29]

type(user_row_1)
list
```

After we created the list, we stored it in the computer’s memory by assigning it to a variable named users.

To create a list of data points, we only need to:

- Separate the data points with a comma.
- Surround the sequence of data points with brackets.

Now let’s create 3 lists:

```python
user_row_1 = ['Erdi', 'Mollahüseyin', 'erdimollahuseyinoglu@gmail.com', 29]
user_row_2 = ['Cengiz', 'Under', 'cengizunder@me.com', 23]
user_row_3 = ['Burak', 'Yilmaz', 'burakyilmaz@me.com', 32]
```

- A list like [7, 8, 9] has identical data types (only integers)
- While the list ['Erdi', 'Mollahüseyin', 'erdimollahuseyinoglu@gmail.com', 29] has mixed data types:
- Three strings ('Erdi', 'Mollahüseyin', 'erdimollahuseyinoglu@gmail.com')
- One integer (29)

The ['Erdi', 'Mollahüseyin', 'erdimollahuseyinoglu@gmail.com', 29] list has four data points. To find the length of a list, we can use the **len()** command:

```python
user_row_1 = ['Erdi', 'Mollahüseyin', 'erdimollahuseyinoglu@gmail.com', 29]
print(len(user_row_1))
4

list_1 = [7, 8, 9] 
print(len(list_1))
3

list_2 = []
print(len(list_2))
0
```

In another example is drinks list that items in a list can be accessed by index. List indices are zero based. `The format is`:

```python
list_name[index]

drinks = ['water', 'tea', 'orange juice', 'beer']
print(drinks)
['water', 'tea', 'orange juice', 'beer']

type(drinks)
list

print(drinks[0])
water

print(drinks[1])
tea

print(drinks[2])
orange juice

print(drinks[3])
beer

drinks[0] = 'cider'
print(drinks[0])
cider
```

Access items from the end of the list by using negative indices. `The last item in a list is:`

```python
list_name[-1]

drinks = ['water', 'tea', 'orange juice', 'beer']
print(drinks[-1])
beer

print(drinks[-2])
orange juice

print(drinks[-3])
tea

print(drinks[-3])
water
```

Add items to a list by using the **append()** or **extend()** list methods.

```python
drinks = ['water', 'tea', 'orange juice', 'beer']

drinks.append('wine')
print(drinks[-1])
wine

drinks = ['water', 'tea', 'orange juice', 'beer']
drinks.extend(['cider', 'wine'])
print(drinks)
['water', 'tea', 'orange juice', 'beer', 'cider', 'wine']

drinks = ['water', 'tea', 'orange juice', 'beer']
more_drinks = ['cider', 'wine']
drinks.extend(more_drinks)
print(drinks)
['water', 'tea', 'orange juice', 'beer', 'cider', 'wine']

drinks = ['water', 'tea', 'orange juice', 'beer']
drinks.insert(0, 'wine')
print(drinks)
['wine', 'water', 'tea', 'orange juice', 'beer']

drinks.insert(2, 'cider')
print(drinks)
['wine', 'water', 'cider', 'tea', 'orange juice', 'beer']
```

## Section 2. Slices

Access a portion of a list using a slice. `The format is`:

```python
list_name(start, stop)
list[index1:index2]
list[:index2]
list[index1:]

drinks = ['wine', 'water', 'cider', 'tea', 'orange juice', 'beer']

some_drinks = drinks[1:4]
print('Some drinks: {}'.format(some_drinks))
Some drinks: ['water', 'cider', 'tea']

first_two = drinks[0:2]
print('First two drinks: {}'.format(first_two))
First two drinks: ['wine', 'water']

first_two_again = drinks[:2]
print('First two drinks: {}'.format(first_two_again))
First two drinks: ['wine', 'water']

drinks = ['wine', 'water', 'cider', 'tea', 'orange juice', 'beer']
last_two = drinks[4:6]
print('Last two drinks: {}'.format(last_two))
Last two drinks: ['orange juice', 'beer']

last_two_again = drinks[-2:]
print('Last two drinks: {}'.format(last_two_again))
Last two drinks: ['orange juice', 'beer']

# String Slices
part_of_a_water = 'water'[1:3]
print(part_of_a_water)
at
```

## Section 3. Exception Handling

The list **index()** method accepts a value as a parameter and returns the index of the first value in the list or an exception if the value is not in the list.

```python
# Finding an item in a list.
drinks = ['wine', 'water', 'cider', 'tea', 'orange juice', 'beer']
drinks_index = drinks.index('water')
print(drinks_index)
1

# Exception
drinks = ['water', 'tea', 'orange juice', 'beer']
wine_index = drinks.index('wine')
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-42-d14f756f0077> in <module>
----> 1 wine_index = drinks.index('wine')

ValueError: 'wine' is not in list

drinks = ['water', 'tea', 'orange juice', 'beer']
try: 
    wine_index = drinks.index('wine') 
except: 
    wine_index = 'No wines found.' 
print(wine_index)
No wines found.
```

Unhandled exceptions cause Python programs to terminate. Handle exceptions using **try/except** blocks.

## Section 4. Looping through a list

### Section 4.1 FOR - LOOP

Loop through a list using a for loop. `The format is`:

```python
for item_variable in list_name:
    # Code block

item_variable = list[0]
item_variable = list[1]
item_variable = list[N]

drinks = ['water', 'tea', 'orange juice', 'beer']
for drink in drinks:
    print(drink.upper())
WATER
TEA
ORANGE JUICE
BEER
```

### Section 4.2 WHILE — LOOP

The code block in a while loop executes as long as the condition evaluates to true. `The format is`:

```python
while condition
    # Code block

drinks = ['water', 'tea', 'orange juice', 'beer']
index = 0

while index < len(drinks):
    print(drinks[index])
    index += 1
water
tea
orange juice
beer
```

## Section 5. Sorting and Ranges

To sort a list, use the **sort()** list method or hee built-in **sorted()** function.

```python
drinks = ['water', 'tea', 'orange juice', 'beer']
print('Drinks list: {}'.format(drinks))
Drinks list: ['water', 'tea', 'orange juice', 'beer']

sorted_drinks = sorted(drinks)
print('Sorted animals list: {}'.format(sorted_drinks))
Sorted animals list: ['beer', 'orange juice', 'tea', 'water']

drinks.sort()
print('Drinks after sort method: {}'.format(drinks))
Drinks after sort method: ['beer', 'orange juice', 'tea', 'water']

drinks = ['water', 'tea', 'orange juice', 'beer']
more_drinks = ['wine']
all_drinks = drinks + more_drinks
print(all_drinks)
['water', 'tea', 'orange juice', 'beer', 'wine']

drinks = ['water', 'tea', 'orange juice', 'beer']
print(len(drinks))
4

drinks.append('wine')
print(len(drinks))
5
```

## Section 6. Ranges

The built-in **range()** function generates a list of numbers. `The format is`:

```python
for number in range(3):
    print(number)
0
1
2

for number in range(1, 3):
    print(number)
1
2

for number in range(1, 10, 2):
    print(number)
1
3
5
7
9

drinks = ['water', 'tea', 'orange juice', 'beer']
for number in range(0, len(drinks), 2):
    print(drinks[number])
water
orange juice
```

## Section 7. Conclusion

In this article you’ve learned how to use a list with the Python programming.
