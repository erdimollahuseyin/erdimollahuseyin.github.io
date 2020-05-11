---
layout: post
title: Python String Methods
category: Python
redirect_from: /2020/01/17/python-string-methods/
---

<img src="/images/blog/2020-01/string-1.jpeg" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

In this article, you will learn how to use some **built-in methods** for Python. Strings in python are surrounded by either single or double quotation marks and each character in a string is assigned an index.

# Section Overview

- count()
- center()
- swapcase()
- find()
- split()
- startswith()
- endswith()
- ljust() and rjust()
- string capitalization
- zfill()
- strip()

Let’s look at string methods:

## Section 1. count()

The [count()](https://docs.python.org/3.7/library/stdtypes.html#str.count) method returns the number of occurrences of a substring in the given string.

**Syntax**:

```python
string.count(substring, start, end)
```

**Parameters**:

- **substring** (Required) is the string whose count is to be found.
- **start** (Optional) is the starting index for the string.
- **end** (Optional) is the ending index for the string.

Let’s look at the example **without optional parameters**:

<img src="/images/blog/2020-01/string-2.png" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

Let’s look at the example **using optional parameters**:

<img src="/images/blog/2020-01/string-3.png" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

## Section 2. center()

The [center()](https://docs.python.org/3.7/library/stdtypes.html#str.center) method creates and returns a new string. **Whitespace** is the default.

**Syntax**:

```python
string.center(length, fillchar)
```

**Parameters**:

- length (Required) is the length of the string.
- fillchar (Optional) is the character which needs to be padded.

Let’s look at the example **without optional parameters**:

<img src="/images/blog/2020-01/string-4.png" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

Let’s look at the example **using optional parameters**:

<img src="/images/blog/2020-01/string-5.png" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

## Section 3. swapcase()

The [swapcase()](https://docs.python.org/3.7/library/stdtypes.html#str.swapcase) method converts all uppercase characters to lowercase or all lowercase characters to uppercase.

**Syntax**:

```python
string.swapcase()
```

Let’s look at the example:

<img src="/images/blog/2020-01/string-6.png" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

## Section 4. find()

The [find()](https://docs.python.org/3.7/library/stdtypes.html#str.find) method returns the lowest index in the string where substring sub is found within the slice **s[start:end]**.

**Syntax**:

```python
string.find(substring, start, end)
```

**Parameters**:

- **substring** (Required) which is to be searched in the given string.
- **start** (Optional) is the starting position where substring is to be checked within the string.
- **end** (Optional) is the ending position where substring is to be checked within the string.

Let’s look at the example **without optional parameters**:

<img src="/images/blog/2020-01/string-7.png" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

Let’s look at the example **using optional parameters**:

<img src="/images/blog/2020-01/string-8.png" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

## Section 5. split()

The [split()](https://docs.python.org/3.7/library/stdtypes.html#str.split) method returns a list of strings in a string for breaking the given string by the specified separator. Also, default separator is any **whitespace**.

**Syntax**:

```python
string.split(separator, maxsplit)
```

**Parameters**:

- **separator** (Optional) is delimiter. The string splits at this specified separator. Default separator is any whitespace.
- **maxsplit** (Optional) is a number of splits. If it is not used than there is not limit.

**Note**: You can use [rsplit()](https://docs.python.org/3.7/library/stdtypes.html#str.rsplit) for splits a string from the right.

Let’s look at the example:

<img src="/images/blog/2020-01/string-9.png" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

## Section 6. startswith()

The [startswith()](https://docs.python.org/3.7/library/stdtypes.html#str.startswith) method return **True**, if starting with the given specified value in a string. Otherwise, it return **False**.

**Syntax**:

```python
string.startswith(search_string, start, end)
```

**Parameters**:

- **search_string** (Required) is the string to be searched.
- **start** (Optional) is the starting position where substring is to be checked within the string.
- **end** (Optional) is the ending position where substring is to be checked within the string.

Let’s look at the example **without optional parameters**:

<img src="/images/blog/2020-01/string-10.png" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

Let’s look at the example **using optional parameters**:

<img src="/images/blog/2020-01/string-11.png" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

## Section 7. endswith()

The [endswith()](https://docs.python.org/3.7/library/stdtypes.html#str.endswith) method return **True**, if ending with the given specified value in a string. Otherwise, it return **False**.

**Syntax**:

```python
string.endwith(search_string, start, end)
```

**Parameters**:

- **search_string** (Required) is the string to be searched.
- **start** (Optional) is the starting position where substring is to be checked within the string.
- **end** (Optional) is the ending position where substring is to be checked within the string.

Let’s look at the example **without optional parameters**:

<img src="/images/blog/2020-01/string-12.png" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

Let’s look at the example **using optional parameters**:

<img src="/images/blog/2020-01/string-13.png" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

## Section 8. ljust() and rjust()

The [ljust](https://docs.python.org/3.7/library/stdtypes.html#str.ljust) and [rjust](https://docs.python.org/3.7/library/stdtypes.html#str.rjust) will left or right align the string, using a specified character (space is default) as the fill character.

**Syntax**:

```python
string.ljust(s, fillchar)
string.rjust(s, fillchar)
```

**Parameters**:

- length (Required) is the width of string to expand it.
- fillchar (Optional) is the character to fill in remaining space.

Let’s look at the example:

<img src="/images/blog/2020-01/string-14.png" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

## Section 9. String Capitalization

### Section 9.1. capitalize()

The [capitalize()](https://docs.python.org/3.7/library/stdtypes.html#str.capitalize) method converts only the first character of a string to capital (*uppercase*) letter.

**Syntax**:

```python
string.capitalize()
```

Let’s look at the example:

<img src="/images/blog/2020-01/string-15.png" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

### Section 9.2. upper()

The [upper()](https://docs.python.org/3.7/library/stdtypes.html#str.upper) method converts all characters of the a string to uppercase letter.

**Syntax**:

```python
string.upper()
```

Let’s look at the example:

<img src="/images/blog/2020-01/string-16.png" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

### Section 9.3. title()

The [title()](https://docs.python.org/3.7/library/stdtypes.html#str.title) method converts all the first character of the a string to uppercase letter.

**Syntax**:

```python
string.title()
```

Let’s look at the example:

<img src="/images/blog/2020-01/string-17.png" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

## Section 10. zfill()

The [zfill()](https://docs.python.org/3.7/library/stdtypes.html#str.zfill) method returns a copy of the string with `0` characters padded to the leftside of the given string.

**Syntax**:

```python
string.zfill(length)
```

**Parameters**:

- **length** is the length of the returned string from zfill() with `0` digits filled to the leftside.

Let’s look at the example:

<img src="/images/blog/2020-01/string-18.png" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

## Section 11. strip()

The [strip()](https://docs.python.org/3.7/library/stdtypes.html#str.strip) method returns a copy of the string with both leading and trailing characters removed.

- **rstrip()**: strips characters from the right in a string.
- **lstrip()**: strips characters from the left in a string.

**Syntax**:

```python
string.strip(character)
```

**Parameters**:

- **character** (Optional): set of characters to be removed.

Let’s look at the example:

<img src="/images/blog/2020-01/string-19.png" alt="Working with M2M Intermediary Models" style="width: 75%; margin-left:10%; margin-right:10%; margin-top:10px; margin-bottom:10px;"/>

# Conclusion

In this article you’ve learned how to use some **built-in methods** for Python. You can visit [Python documentation](https://docs.python.org/3.7/library/stdtypes.html#string-methods) for learning deeper.
