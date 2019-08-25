Python Unit Testing Module
==========================

* Test file name convention (tests should be in a separate file)
```python
test_[module_name]
```
* Test class inherit from `unittest.TestCase` 
* Methods need to be prefixed
```python
test_[method_name]
```
* Update test suite with new bugs

# Running Unit Tests

```shell
$ python -m unittest test_[module_name]
```
or with the following the in py file
```python
if __name__ == '__main__':
        unittest.main()
```
and then
```shell
$ python test_[module_name]
```

# Testing For Exceptions

Two Methods:
```python
self.assertRaises(Error, [method_name], *[params])
# or Context Manager
with self.assertRaises(Error):
    # call method that raises error
    [method_name](*[params])
```

# Test Set Ups and Clean Ups
```python
def setUp(self):
    # Runs before each test method is called
    pass

def tearDown(self):
    # Runs after each test method is called
    pass

@classmethod
def setUpClass(cls):
    # Runs before running all tests in a class
    pass

@classmethod
def tearDownClass(cls):
    # Runs after running all tests in a class
    pass
```

# Mocking Objects

Use the following module `from unittest.mock import patch`. `patch` can be used in a context manager or as a decorator.

```python
# To use this start by mocking the object dependency
with patch('[method_to_mock]') as mock_method:
    mock_method.return_value.property1 = value # set return values
    mock_method.return_value.property2 = value # set return values

    # call the method to test
    return_value = [method_to_test](*[params])
    
    # "call" mocked method
    mock_method.assert_called_with(*[params])

    # Then check return value
    self.assertEqual(return_value, [expected_return_value])
```
