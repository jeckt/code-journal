Python Coding Conventions
=========================

Based on PEP 8 -- Style Guide for Python Code

* Folder structure
* Naming Conventions
** Package/Module Names: all-lowercase names with underscores for clarity
** Functions/Methods and variables: lowercase with underscores
** Classes: CapWords convention
** Constants: Capital letters with underscores
* Avoid extraneous whitespace.
```python
Yes: foo = (0,)
No: foo = (0, )
```

# Indentation

```python
# Add 4 spaces (an extra level of indentation) to distinguish arguments from the rest.
def long_function_name(
        var_one, var_two, var_three,
        var_four):
    print(var_one)
```

# Imports

* Imports should usually be on separate lines
* Imports should be grouped in the following order:
1. Standard library imports.
2. Related third party imports.
3. Local application/library specific imports
