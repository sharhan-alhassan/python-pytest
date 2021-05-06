
### Installation
`Pytest` is a framework to write small tests
- Installation
`pip install pytest == 6.2.4`

- Confirm installation version
`pytest --version`

- Running pytest withoug explicitly mentioning a filename runs all files with the format `test_*.py` or `*_test.py` in the current directory and subdirectories. 
However, you can choose to explicitly mention the filename to run only that one

### Running test
- `pytest <filename>`
- `pytest <filename> -v`

    `-v` for verbose to see more for clarity

### Running a subset of test suite or files
Usually they will be plenty test files. You can select those files to run with either of the two ways:

1. ### Select tests to run based on `substring` matching the test names: Syntax

*NB: Check the `automation` folder for files to this chapter*

[Tutorialspoint](https://www.tutorialspoint.com/pytest/index.htm)

`pytest -k <substring> -v` where `-k` represents the substring to search for.

Example:
We have three tests in a file: `test_square()`, `test_big_square()`, and `test_rectangle()`.

Test command: `pytest -k square -v`
Output: This will run `test_square()` and `test_big_square()` because both have the string `square` in them 

2. ### Select tests groups to run based on the markers applied:

- The aim is to group the tests using markers. **Markers are used to set various features/attributes to test functions**.

- Some in-built pytest markers are `xfail`, `skip`, `parameterize`. Additionally, you can choose to create your own marker names.

- Markers are applied on the tests using the syntax:

    @pytest.mark.markername

- First of all, to use markers, you have to `import pytest` module in the test file. Syntax for running the `marked test`

    pytest -m markername -v 


### Pytest - Fixtures
File: `conftest.py`

- Fixtures are functions which will run before each test function to which is applied to. They are used to feed some data to the tests such as database connections, URLs to test and some sort of input data. 

Syntax for marking a function with a fixture:

`@pytest.fixture`

- **A test function can use a fixture by mentioning the fixture name as in input parameter**

- However, the downside is a fixture can only be used in the test file only and can't be used by many files. The resolution to this problem is to create a file called `conftest.py`

- We can define the fixture functions in the `conftest.py` file to make it accessible to multiple test files.

`Old Code:`

    
    import pytest

    @pytest.fixture
    def input_value():
        return 39 

    def test_divisible_by_3(input_value):
        assert input_value % 3 == 0

    def test_divisible_by_6(input_value):
        assert input_value % 6 == 0

`conftest.py file`

    import pytest

    @pytest.fixture
    def input_value():
        return 39

`refactored code by removing the fixture`

    def test_divisible_by_3(input_value):
        assert input_value % 3 == 0

    def test_divisible_by_6(input_value):
        assert input_value % 6 == 0


        
### Pytest - Parameterizing tests
File: `test_multiply.py`

Parameterizing a test is done to run the test against `multiple sets of input`. It is done by using the marker `parameterize`. Syntax:

`@pytest.mark.parametrize`

Code for parameterizing:

    import pytest

    @pytest.mark.parametrize("num, output", [(1, 11), (2, 22), (3, 35), (4, 44)])
    def test_multiplication_11(num, output):
        assert 11 * num == output

- parametrize has a `num which is the input` and an `output which is the result`. So the input `num` on the left side multiply by the value `11` is always compared to the `output` on the right side


### Pytest - xfail/skip tests
File: `test_compare.py`

1. Using `xfailed` test (`expected to fail`), pytest will execute it but will not consider it whether it's passed test or failed test. Details of the test will not be printed either. The marker `xfail` is used. Syntax

`@pytest.mark.xfail`

2. `skip` test simply means the pytest won't execute the test. It'll be skipped. Syntax

`@pytest.mark.skip`
    
*NB: When the tests become relevant, we can remove the markers*

### Pytest - Stop test suite after N Test failures
File: `test_failure.py`
You only deploy your code if it passes ALL tests. A single test failure means it's not production ready. So you probably want to stop the tests suite as soon as you hit certain number of test (`N`). Say if you run your code and you hit 3 test failures, the stop the entire test suite. Don't even proceed again.

Syntax: `pytest --maxfail = <num>`

Code:

    import pytest
    import math

    def test_sqrt_failure():
        num = 25
        assert math.sqrt(num) == 6

    def test_square_failure():
        num = 7 
        assert 7 * 7 == 40

    def test_equality_failure():
        assert 10 == 11

All the above tests will fail the test. But we want to stop the tests execution when 1 failure (`N=1`) occurs

`pytest test_failure.py -v --maxfail=1`

### Pytest - Run tests in parallel
- In real case, there'll be plenty test files with bunch of tests in them. Running all of them in sequential order will lead to large execution time. To resolve this, we can always run the tests in paralle. 

- A second package is neede for this called `pytest-xdist` 

- Install it: `pip install pytest-xdist`

- Now test can be run with the syntax: `pytest -n 3` where `-n` is the number of workers to run the various tests. For smaller projects like this one, you won't see much time difference unless in large tests suite


### Test execution to XML file
- We can export our test execution results to an xml file to displayed on dashboards, review, or for your dev manager.
Syntax:

- `pytest <filename.py> -v --junitxml="filename.xml"`

Let's generate the test results for the `test_multiply` file

- `pytest test_multiply.py -v --junitxml="results.xml"`