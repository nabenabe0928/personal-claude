# General Rules

- We follow the coding style defined by [PEP 8](https://www.python.org/dev/peps/pep-0008/).
  - As an exception, we extend the maximum line length to 99 characters.
- We have some additional, project-specific conventions.
  - Please refer to the [`Personal Specific Conventions`](#personal-specific-conventions) section for the details.
- We use type hints:
  - [PEP484](https://www.python.org/dev/peps/pep-0484/)  
  - [Syntax cheat sheet](https://mypy.readthedocs.io/en/stable/cheat_sheet_py3.html)

# Personal Specific Conventions

## Block and inline comments are supposed to be sentence(s).

```python
# This is a good example.
# bad example
# Bad example
```

## Don't use type hints in examples.

For simplicity.

### Good Example

```python
def objective(trial):
    ...
```

### Bad example

```python
def objective(trial: optuna.trial.Trial) -> float:
    ...
```

## Add `_` prefix to the names of private methods, functions, fields, and classes.

### Example

```python
class PublicClass:  # This class is exposed to external libraries.
    def __init__(self):
        self.public_field = 10            # This field is supposed to be accessed from other libraries.
        self._package_private_field = 20  # This field is supposed to be accessed only within the same library.
        self._private_field = 30          # This field is supposed to be accessed only within the same class.

    def public_method(self):
        pass

    def _package_private_method(self):
        pass

    def _private_method(self):
        pass


class _PackagePrivateClass:  # This class is supposed to be accessed only within the same library.
    def __init__(self):
        self.package_private_field = 10  # This field can be accessed from any place within the same library.
        self._private_field = 20         # This field is supposed to be accessed only within the same class.

    def package_private_method(self):
        pass

    def _private_method(self):
        pass


class _PrivateClass:  # This class is supposed to be accessed only within the same module.
    def __init__(self):
        self.package_private_field = 10  # This field can be accessed from any place within the same module.
        self._private_field = 20         # This field is supposed to be accessed only within the same class.

    def package_private_method(self):
        pass

    def _private_method(self):
        pass


def _package_private_function():  # This function is supposed to be accessed only within the same library.
    pass


def _private_function():  # This function is supposed to be accessed only within the same module.
    pass
```

## Testing

Prefer `pytest` unittest with standard assertions over `unittest` tests. 

### Good Example

```python
def test_foo():
    ...
    assert actual == expected
```

### Bad example

```python
def TestFoo(unittest.Testcase):
    def test_foo(self):
        ...
        self.assertEqual(actual, expected)
```

Similarly, prefer `pytest.raises` for testing expected errors.

## Docstrings

We follow [Example Google Style Python Docstrings](https://sphinxcontrib-napoleon.readthedocs.io/en/latest/example_google.html) with a couple of exceptions:

- Add `Example:` sections.
- ``Args`` and ``Attributes`` sections always start with a new line.
- No inline docstrings.
- The `__init__` method must be documented in the class level docstring, not as a docstring on the `__init__` method.
- Use sphinx-style links to Python objects.

#### Example

```python
def example_function(param1: int, param2: str) -> bool:
    """An example of function docstrings.
    
    Example:
        Using `testsetup` and `testcode`.

        .. testsetup::
            import numpy as np
                
        .. testcode::
            x = np.zeros(10)

    Args:
        param1:
            The first parameter.
        param2:
            The second parameter.

    Returns:
        The return value. :obj:`True` for success, :obj:`False` otherwise.

    """
    return True
```

```python
class ExampleClass(object):
    """The summary line for a class docstring should fit on one line.

    If the class has public attributes, they may be documented here
    in an ``Attributes`` section and follow the same formatting as a
    function's ``Args`` section.

    Properties created with the ``@property`` decorator should be documented
    in the property's getter method.

    The `__init__` method must be documented in the class level docstring,
    not as a docstring on the `__init__` method.

    Args:
        param1:
            Description of `param1`.
        param2:
            Description of `param2`. Multiple
            lines are supported.

    Attributes:
        attr1:
            Description of `attr1`.
        attr2:
            Description of `attr2`.

    """

    def __init__(self, param1: str, param2: Optional[int] = 0):
        self.attr1 = param1
        self.attr2 = param2

    @property
    def readonly_property(self) -> str:
        """Properties should be documented in their getter method."""
        return "readonly_property"

    @property
    def readwrite_property(self) -> List[str]:
        """Properties with both a getter and setter
        should only be documented in their getter method.

        If the setter method contains notable behavior, it should be
        mentioned here.
        """
        return ["readwrite_property"]

    @readwrite_property.setter
    def readwrite_property(self, value: int) -> int:
        value

    def example_method(self, param1: str, param2: int) -> bool:
        """Class methods are similar to regular functions.

        Example:
            Using `testsetup` and `testcode`.

            .. testsetup::
                import numpy as np
                
            .. testcode::
                x = np.zeros(10)

        Note:
            Do not include the `self` parameter in the ``Args`` section.
            (Instead of ``Note:``, you can use ``.. note::``.)

        Args:
            param1:
                The first parameter.
            param2:
                The second parameter.

        Returns:
            :obj:`True` if successful, :obj:`False` otherwise.

        """
        return True
```

Some personal practices are listed below.

### Exceptions
This section is optional and should be used carefully. It should be documented if any of the followings:

- An error is non-obvious. Documentation helps users to understand what happens.
- An exception is expected to be caught in usercode. Documentation helps users to know what exception they should catch.
- When we indicate specifications. We may document exceptions typically in base classes.

#### Good Example

```python
def some_function(x: float):
   """...

   ...

   Args:
       x:
           Positive real number.

   """
```

#### Bad Example

```python
def some_function(x: float):
   """...

   ...

   Args:
       x:
           Positive real number.

   Raises:
       :exc:`ValueError`:
           ``x`` is negative.

   """
```

## Logging HOWTO

Basically, we follow the [Python official document](https://docs.python.org/3/howto/logging.html).
This section describes what needs special attention.

When we issue a warning regarding a particular runtime event, we use the following rule.
- If the issue is avoidable and the client application should be modified to eliminate the warning, please use `warnings.warn()`.
- If there is nothing the client application can do about the situation, but the event should still be noted, please use a project-specific warning.

### Example (with Optuna)

```python
import warnings

from optuna import logging

# Deprecate a feature which we cannot use the deprecation decorator, then use `warnings.warn()`
# https://github.com/optuna/optuna/blob/4d6e1c7e5f163744136dd1b67593d03cb977bc0f/optuna/cli.py
class _StudyOptimize(_BaseCommand):
    ...
    def take_action(self, parsed_args):
        # type: (Namespace) -> int

        message = (
            "The use of the `study optimize` command is deprecated. Please execute your Python "
            "script directly instead."
        )
        warnings.warn(message, DeprecationWarning)
        ...

# Warn the exceptional sampler is used for the `CmaEsSampler` due to out of bounds, then use `optuna.logging.Logger.warnings()`.
# https://github.com/optuna/optuna/blob/4d6e1c7e5f163744136dd1b67593d03cb977bc0f/optuna/samplers/_cmaes.py
_logger = logging.get_logger(__name__)


class CmaEsSampler(BaseSampler):
    ...
     def _log_independent_sampling(self, trial: FrozenTrial, param_name: str) -> None:
        self._logger.warning(
            "The parameter '{}' in trial#{} is sampled independently "
            "by using `{}` instead of `CmaEsSampler` "
            "(optimization performance may be degraded). "
            "You can suppress this warning by setting `warn_independent_sampling` "
            "to `False` in the constructor of `CmaEsSampler`, "
            "if this independent sampling is intended behavior.".format(
                param_name, trial.number, self._independent_sampler.__class__.__name__
            )
        )
    ...
```
