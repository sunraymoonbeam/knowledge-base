---
title: ✨ Best Practices
---


<div style="text-align: center;">
    <img src="https://i.ytimg.com/vi/plXuoYYFS-Y/maxresdefault.jpg" style="transform: scale(0.8);">
</div>

# Resources
1. https://google.github.io/styleguide/pyguide.html
1. https://gist.github.com/redlotus/3bc387c2591e3e908c9b63b97b11d24e
1. https://peps.python.org/pep-0484/


# Table of Contents
0. [Summary](#summary)
1. [Docstrings](#google-docstrings)
1. [Type Annotations](#type-annotations)
1. [Naming Convetions](#naming-conventions)


## Google Docstrings

Docstrings are an essential part of writing clean, readable, and maintainable code. They provide a convenient way of associating documentation with Python modules, functions, classes, and methods. Google style docstrings are one of the popular formats for writing docstrings in Python. They are structured and easy to read, making them a good choice for documenting your code.

#### Example of a Module Level Function
```python
def module_level_function(param1, param2=None, *args, **kwargs):
    """This is an example of a module level function.
    Function parameters should be documented in the ``Args`` section. The name
    of each parameter is required. The type and description of each parameter
    is optional, but should be included if not obvious.
    If \*args or \*\*kwargs are accepted,
    they should be listed as ``*args`` and ``**kwargs``.
    The format for a parameter is::
        name (type): description
            The description may span multiple lines. Following
            lines should be indented. The "(type)" is optional.
            Multiple paragraphs are supported in parameter
            descriptions.
    Args:
        param1 (int): The first parameter.
        param2 (:obj:`str`, optional): The second parameter. Defaults to None.
            Second line of description should be indented.
        *args: Variable length argument list.
        **kwargs: Arbitrary keyword arguments.
    Returns:
        bool: True if successful, False otherwise.
        The return type is optional and may be specified at the beginning of
        the ``Returns`` section followed by a colon.
        The ``Returns`` section may span multiple lines and paragraphs.
        Following lines should be indented to match the first line.
        The ``Returns`` section supports any reStructuredText formatting,
        including literal blocks::
            {
                'param1': param1,
                'param2': param2
            }
    Raises:
        AttributeError: The ``Raises`` section is a list of all exceptions
            that are relevant to the interface.
        ValueError: If `param2` is equal to `param1`.
    """
    if param1 == param2:
        raise ValueError('param1 may not be equal to param2')
    return True
```

#### Example of a Simple Function
```python
def google_docstrings(num1: int, num2: int) -> int:
    """Add up two integer numbers.

    This function simply wraps the ``+`` operator, and does not
    do anything interesting, except for illustrating what
    the docstring of a very simple function looks like.

    Args:
        num1 (int) : First number to add.
        num2 (int) : Second number to add.

    Returns:
        The sum of ``num1`` and ``num2``.

    Raises:
        AnyError: If anything bad happens.
    """
    return num1 + num2
```


## Type Annotations
Type annotations, introduced in PEP 484, provide a way to specify the expected data types of function arguments and return values. This helps in improving code readability and maintainability, and can also assist with static type checking tools like mypy.

```python
def my_method(
    self,
    first_var: int,
    second_var: Foo,
    third_var: Bar | None,
) -> int:
  ...
```

## Naming Conventions
Consistent naming conventions are crucial for writing readable and maintainable code. Here are some common naming conventions used in Python:

* `module_name`
* `package_name`
* `ClassName`
* `method_name`
* `ExceptionName`
* `function_name`
* `GLOBAL_CONSTANT_NAME`
* `global_var_name`
* `instance_var_name`
* `function_parameter_name`
* `local_var_name`
* `query_proper_noun_for_thing`
* `send_acronym_via_https`


| Type                       | Public              | Internal                          |
|----------------------------|---------------------|-----------------------------------|
| Packages                   | lower_with_under    |                                   |
| Modules                    | lower_with_under    | _lower_with_under                 |
| Classes                    | CapWords            | _CapWords                         |
| Exceptions                 | CapWords            |                                   |
| Functions                  | lower_with_under()  | _lower_with_under()               |
| Global/Class Constants     | CAPS_WITH_UNDER     | _CAPS_WITH_UNDER                  |
| Global/Class Variables     | lower_with_under    | _lower_with_under                 |
| Instance Variables         | lower_with_under    | _lower_with_under (protected)     |
| Method Names               | lower_with_under()  | _lower_with_under() (protected)   |
| Function/Method Parameters | lower_with_under    |                                   |
| Local Variables            | lower_with_under    |                                   |



