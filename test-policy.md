## Code quality
Similar to the main module, the test code under is carefully maintained to meet certain quality requirements. That is:

- the test code is based on common principles of software design such as [SOLID](https://en.wikipedia.org/wiki/SOLID) and [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself);
- the test code is readable; for example, the name of a testing method sufficiently describes the purpose;
- the test code is non-redundant; for example, multiple test cases should not exist for a single [equivalence class](https://en.wikipedia.org/wiki/Equivalence_partitioning). 

In addition, the testing code is designed to avoid the [Fragile Test](http://xunitpatterns.com/Fragile%20Test.html) problem. Below are typical anti-patterns that make the tests fragile:

- a test case has side effects to other test cases;
- a test case invokes private methods/variables encapsulated in another class;
- a test case depends on unstable APIs or libraries;
- etc.

See also [XUnit Test Patterns](http://xunitpatterns.com/) for tips to improve the quality of test code.


## Conventions of file name and structure
Under the test directory, the filenames and directory structure reflect the main module directory structure. The test cases are placed in the most reasonable place. For example, the test cases of `optuna.logging` are written in `optuna/tests/test_logging.py`.

If multiple classes/functions have duplicated test patterns, they are merged and placed in the common vertex module, and parametrized with [pytest.mark.parametrize](https://docs.pytest.org/en/stable/how-to/parametrize.html#pytest-mark-parametrize-parametrizing-test-functions). For example, the common tests of sampler classes are placed in [optuna/tests/sampler_tests/test_samplers.py](https://github.com/optuna/optuna/blob/master/tests/samplers_tests/test_samplers.py). A common, parameterized test case should avoid [conditional test logic](http://xunitpatterns.com/Conditional%20Test%20Logic.html) that deals with specific classes/functions.


## Analysis of input values and edge cases
With sufficient [boundary value analysis](https://en.wikipedia.org/wiki/Boundary-value_analysis) and [equivalence partitioning](https://en.wikipedia.org/wiki/Equivalence_partitioning), test cases are written for each equivalence class. Not only the happy paths, but edge cases and error conditions are carefully analyzed. Below are typical edge cases in Optuna tests:

- typical edge cases with Python:
  - `None`, empty list, empty string, etc. 
- typical edge cases with numerical calculation:
  - `NaN`, `inf`, `-inf`, negative values, etc.
- Project specific edge cases

## Private APIs
Private methods and functions are also tested. Note that, unreasonable testing on the private API may cause the Fragile Tests problem especially when the API is still unstable. If an API is unstable or hard to test, it should be refactored with more testable design and architecture, instead of adding unreasonable, fragile test cases.

## Testing utilities
Copying and pasting test code directly harms the code quality. To keep the code [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself), duplicated logic is abstracted and put in the testing module under the main module directory.

## Reproducibility
A seed argument is provided if a sampler, pruner, or any other class/function has randomness. The reproducibility is tested in its unit tests. In principle, the reproducibility tests focus on only single-worker scenarios. Multi-worker scenarios need not be tested, because Optuna does not guarantee any reproducibility of parallel optimization.
