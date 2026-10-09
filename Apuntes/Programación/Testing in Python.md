#Programming 
# Slides

[chapter1](attachment:0fca5da3-6d4c-4a4e-b987-d151e74104d6:chapter1-1_compressed.pdf)

# Notes

## Creating tests with pytest

### Introduction to Testing in Python

#### The first test suite

In this exercise, you will write the first test suite that includes the types of tests that you have learned using the `pytest` library.

The function `multiple_of_two` checks whether the `num` is a multiple of `2` or not. You will implement and run two regular `assert` tests.

```python
def multiple_of_two(num):
    if num == 0:
        raise(ValueError)
    return num % 2 == 0

def test_numbers():
    assert multiple_of_two(2) is True
    # Write the "False" test below
    assert multiple_of_two(19) == False
```

#### pytest.raises

In this exercise, you will continue writing the first test suite using the `pytest` library.

The function `multiple_of_two` checks whether the `num` is a multiple of `2` or not. In this exercise, you will implement a test that expects to raise an `Exception`.

```python
def multiple_of_two(num):
    if num == 0:
        raise(ValueError)
    return num % 2 == 0

def test_zero():    
  	# Add a context for an exception test here
    with pytest.raises(ValueError):
      	# Check zero input below
        multiple_of_two(0)
```

### Applying test markers

#### Failed tests with xfail

In this exercise, you will use `pytest` markers for the first time in order to specify the behavior of the test. You have already seen the function `multiple_of_two`, which checks whether the `num` is a multiple of `2` or not. The `pytest` library was already imported for you.

```python
def multiple_of_two(num):
    if num == 0:
        raise(ValueError)
    return num % 2 == 0

# Add the pytest marker decorator here
@pytest.mark.xfail
def test_fails():
  	# Write any assert test that will fail
    assert multiple_of_two(2) == False
```

#### Conditional skipping

Sometimes, you want to skip a test if some condition is met. For example, you want to conduct a test unless today is Saturday. In this case, you can use the `datetime` library for getting the current day of the week and the `pytest` marker for conditional skipping the test function. To pass a condition to the conditional skipping `pytest` marker, you can use `@pytest.mark.skipif(condition)`

```python
day_of_week = datetime.now().isoweekday()

def get_unique_values(lst):
    return list(set(lst))

condition_string = 'day_of_week == 6'
# Add the conditional skip marker and the string here
@pytest.mark.skipif(condition_string)
def test_function():
	# Complete the assertion tests here
    assert get_unique_values([1,2,3]) == [1,2,3]
    assert get_unique_values([1,2,3,1]) == [1,2,3]
```

## Pytest Fixtures

### Introduction to fixtures

#### Getting familiar with fixtures

Now, you will recap the things you learned in the video exercise. Fixtures might  
become essential in cases when the testing scripts are too large. So it is important to understand and be ready to use them.

#### Data preparation

In this exercise you will create a fixture and finish test functions. Here, you have a simple data pipeline and some tests to check the data it returns. Since the tests goal is to check that the data is correct, it would be convenient to implement the pipeline as a fixture.

```python
# Import the pytest library
import pytest

# Define the fixture decorator
@pytest.fixture
# Name the fixture function
def prepare_data():
    return [i for i in range(10)]

# Create the tests
def test_elements(prepare_data):
    assert 9 in prepare_data
    assert 10 not in prepare_data
```

### Run with a fixture

You will supplement the test that you made before with a fixture. It is really important that you run your tests after implementation, because that is the way to see the results.

`pytest run_with_fixtures.py`