---
title: "Using Debugger for Diagnosing the Issues"
teaching: 30
exercises: 20
questions:
- "Once we know our program has errors, how can we locate them in the code?"
- "How can we make our programs more resilient to failure?"
objectives:
- "Use a debugger to explore behaviour of a running program"
- "Describe and identify edge and corner test cases and explain why they are important"
- "Apply error handling and defensive programming techniques to improve robustness of a program"
- "Integrate linting tool style checking into a continuous integration job"
keypoints:
- "Unit testing can show us what does not work, but does not help us locate problems in code."
- "Use a **debugger** to help you locate problems in code."
- "A **debugger** allows us to pause code execution and examine its state by adding **breakpoints** to lines in code."
- "Use **preconditions** to ensure correct behaviour of code."
- "Ensure that unit tests check for **edge** and **corner cases** too."
- "Using linting tools to automatically flag suspicious programming language constructs and stylistic errors
can help improve code robustness."
---

## Introduction

Unit testing can tell us something is wrong in our code
and give a rough idea of where the error is
by which test(s) are failing.
But it does not tell us exactly where the problem is (i.e. what line of code),
or how it came about.
To give us a better idea of what is going on, we can:

- split the code into smaller cells to locate the source of error,
- output program state at various points,
   e.g. by using print statements to output the contents of variables,
- use a logging capability to output
  the state of everything as the program progresses, or
- look at intermediately generated files.

But such approaches are often time consuming
and sometimes not enough to fully pinpoint the issue.
In complex programs, like simulation codes,
we often need to get inside the code while it is running and explore.
This is where using a **debugger** can be useful.

## Setting the Scene

Let us add a new function called `patient_normalise()` to our inflammation example
to normalise a given inflammation data array so that all entries fall between 0 and 1.
(Make sure you create a new feature branch for this work off your `develop` branch.)
To normalise each patient's inflammation data
we need to divide it by the maximum inflammation experienced by that patient.
To do so, we can add the following code to `inflammation/models.py`:

~~~
def patient_normalise(data):
    """Normalise patient data from a 2D inflammation data array."""
    max = np.max(data, axis=0)
    return data / max[:, np.newaxis]
~~~
{: .language-python}

**Note:** *there are intentional mistakes in the above code,
which will be detected by further testing and code style checking below
so bear with us for the moment!*

In the code above, we first go row by row
and find the maximum inflammation value for each patient
and store these values in a 1-dimensional NumPy array `max`.
We then want to use NumPy's element-wise division,
to divide each value in every row of inflammation data
(belonging to the same patient)
by the maximum value for that patient stored in the 1D array `max`.
However, we cannot do that division automatically
as `data` is a 2D array (of shape `(60, 40)`)
and `max` is a 1D array (of shape `(60, )`),
which means that their shapes are not compatible.

![NumPy arrays of incompatible shapes](../fig/imgDummy.png){: .image-with-shadow width="800px"}

Hence, to make sure that we can perform this division and get the expected result,
we need to convert `max` to be a 2D array
by using the `newaxis` index operator to insert a new axis into `max`,
making it a 2D array of shape `(60, 1)`.

Now the division will give us the expected result.
Even though the shapes are not identical,
NumPy's automatic `broadcasting` (adjustment of shapes) will make sure that
the shape of the 2D `max` array is now "stretched" ("broadcast")
to match that of `data` - i.e. `(60, 40)`,
and element-wise division can be performed.


> ## Broadcasting
>
> The term broadcasting describes how NumPy treats arrays with different shapes
> during arithmetic operations.
> Subject to certain constraints,
> the smaller array is “broadcast” across the larger array
> so that they have compatible shapes.
> Be careful, though, to understand how the arrays get stretched
> to avoid getting unexpected results.
{: .callout}

Note there is an assumption in this calculation
that the minimum value we want is always zero.
This is a sensible assumption for this particular application,
since the zero value is a special case indicating that a patient
experienced no inflammation on a particular day.

Let us now add a new test in `tests/test_models.py`
to check that the normalisation function is correct for some test data.

~~~
@pytest.mark.parametrize(
    "test, expected",
    [
        ([[1, 2, 3], [4, 5, 6], [7, 8, 9]], [[0.33, 0.67, 1], [0.67, 0.83, 1], [0.78, 0.89, 1]])
    ])
def test_patient_normalise(test, expected):
    """Test normalisation works for arrays of one and positive integers.
       Assumption that test accuracy of two decimal places is sufficient."""
    from inflammation.models import patient_normalise
    npt.assert_almost_equal(patient_normalise(np.array(test)), np.array(expected), decimal=2)
~~~
{: .language-python}

Note that we are using the `assert_almost_equal()` Numpy testing function
instead of `assert_array_equal()`,
since it allows us to test against values that are *almost* equal.
This is very useful when we have numbers with arbitrary decimal places
and are only concerned with a certain degree of precision,
like the test case above,
where we make the assumption that a test accuracy of two decimal places is sufficient.

Run the tests again using `python -m pytest tests/test_models.py`
and you will note that the new test is failing,
with an error message that does not give many clues as to what went wrong.

~~~
E       AssertionError:
E       Arrays are not almost equal to 2 decimals
E
E       Mismatched elements: 6 / 9 (66.7%)
E       Max absolute difference: 0.57142857
E       Max relative difference: 1.345
E        x: array([[0.14, 0.29, 0.43],
E              [0.5 , 0.62, 0.75],
E              [0.78, 0.89, 1.  ]])
E        y: array([[0.33, 0.67, 1.  ],
E              [0.67, 0.83, 1.  ],
E              [0.78, 0.89, 1.  ]])

tests/test_models.py:53: AssertionError
~~~
{: .output}

Let us use a debugger at this point to see what is going on and why the function failed.

## Debugging in Jupyter Lab

Think of debugging like performing exploratory surgery - on code!
Debuggers allow us to peer at the internal workings of a program,
such as variables and other state,
as it performs its functions.

## Corner or Edge Cases

The test case that we have currently written for `patient_normalise`
is parameterised with a fairly standard data array.
However, when writing your test cases,
it is important to consider parameterising them by unusual or extreme values,
in order to test all the edge or corner cases that your code could be exposed to in practice.
Generally speaking, it is at these extreme cases that you will find your code failing,
so it's beneficial to test them beforehand.

What is considered an "edge case" for a given component depends on
what that component is meant to do.
In the case of `patient_normalise` function, the goal is to normalise a numeric array of numbers.
For numerical values, extreme cases could be zeros,
very large or small values,
not-a-number (`NaN`) or infinity values.
Since we are specifically considering an *array* of values,
an edge case could be that all the numbers of the array are equal.

For all the given edge cases you might come up with,
you should also consider their likelihood of occurrence.
It is often too much effort to exhaustively test a given function against every possible input,
so you should prioritise edge cases that are likely to occur.
For our `patient_normalise` function, some common edge cases might be the occurrence of zeros,
and the case where all the values of the array are the same.

When you are considering edge cases to test for,
try also to think about what might break your code.
For `patient_normalise` we can see that there is a division by
the maximum inflammation value for each patient,
so this will clearly break if we are dividing by zero here,
resulting in `NaN` values in the normalised array.

With all this in mind,
let us add a few edge cases to our parametrisation of `test_patient_normalise`.
We will add two extra tests,
corresponding to an input array of all 0,
and an input array of all 1.

~~~
@pytest.mark.parametrize(
    "test, expected",
    [
        ([[0, 0, 0], [0, 0, 0], [0, 0, 0]], [[0, 0, 0], [0, 0, 0], [0, 0, 0]]),
        ([[1, 1, 1], [1, 1, 1], [1, 1, 1]], [[1, 1, 1], [1, 1, 1], [1, 1, 1]]),
        ([[1, 2, 3], [4, 5, 6], [7, 8, 9]], [[0.33, 0.67, 1], [0.67, 0.83, 1], [0.78, 0.89, 1]]),
    ])
~~~
{: .language-python}

Running the tests now from the command line results in the following assertion error,
due to the division by zero as we predicted.

~~~
E           AssertionError:
E           Arrays are not almost equal to 2 decimals
E
E           x and y nan location mismatch:
E            x: array([[nan, nan, nan],
E                  [nan, nan, nan],
E                  [nan, nan, nan]])
E            y: array([[0, 0, 0],
E                  [0, 0, 0],
E                  [0, 0, 0]])

tests/test_models.py:88: AssertionError
~~~
{: .output}

How can we fix this?
Luckily, there is a NumPy function that is useful here,
[`np.isnan()`](https://numpy.org/doc/stable/reference/generated/numpy.isnan.html),
which we can use to replace all the NaN's with our desired result,
which is 0.
We can also silence the run-time warning using
[`np.errstate`](https://numpy.org/doc/stable/reference/generated/numpy.errstate.html):

~~~
...
def patient_normalise(data):
    """
    Normalise patient data from a 2D inflammation data array.

    NaN values are ignored, and normalised to 0.

    Negative values are rounded to 0.
    """
    max = np.nanmax(data, axis=1)
    with np.errstate(invalid='ignore', divide='ignore'):
        normalised = data / max[:, np.newaxis]
    normalised[np.isnan(normalised)] = 0
    normalised[normalised < 0] = 0
    return normalised
...
~~~
{: .language-python}

> ## Exercise: Exploring Tests for Edge Cases
>
> Think of some more suitable edge cases to test our `patient_normalise()` function
> and add them to the parametrised tests.
> After you have finished remember to commit your changes.
>
> > ## Possible Solution
> > ~~~
> > @pytest.mark.parametrize(
> >     "test, expected",
> >     [
> >         (
> >             [[0, 0, 0], [0, 0, 0], [0, 0, 0]],
> >             [[0, 0, 0], [0, 0, 0], [0, 0, 0]],
> >         ),
> >         (
> >             [[1, 1, 1], [1, 1, 1], [1, 1, 1]],
> >             [[1, 1, 1], [1, 1, 1], [1, 1, 1]],
> >         ),
> >         (
> >             [[float('nan'), 1, 1], [1, 1, 1], [1, 1, 1]],
> >             [[0, 1, 1], [1, 1, 1], [1, 1, 1]],
> >         ),
> >         (
> >             [[1, 2, 3], [4, 5, float('nan')], [7, 8, 9]],
> >             [[0.33, 0.67, 1], [0.8, 1, 0], [0.78, 0.89, 1]],
> >         ),
> >         (
> >             [[-1, 2, 3], [4, 5, 6], [7, 8, 9]],
> >             [[0, 0.67, 1], [0.67, 0.83, 1], [0.78, 0.89, 1]],
> >         ),
> >         (
> >             [[1, 2, 3], [4, 5, 6], [7, 8, 9]],
> >             [[0.33, 0.67, 1], [0.67, 0.83, 1], [0.78, 0.89, 1]],
> >         )
> >     ])
> > def test_patient_normalise(test, expected):
> >     """Test normalisation works for arrays of one and positive integers."""
> >     from inflammation.models import patient_normalise
> >     npt.assert_almost_equal(patient_normalise(np.array(test)), np.array(expected), decimal=2)
> > ...
> > ~~~
> > {: .language-python}
> >
> > You could also, for example, test and handle the case of a whole row of NaNs.
> {: .solution}
>
{: .challenge}

## Defensive Programming

In the previous section, we made a few design choices for our `patient_normalise` function:

1. We are implicitly converting any `NaN` and negative values to 0,
2. Normalising a constant 0 array of inflammation results in an identical array of 0s,
3. We don't warn the user of any of these situations.

This could have be handled differently.
We might decide that we do not want to silently make these changes to the data,
but instead to explicitly check that the input data satisfies a given set of assumptions
(e.g. no negative values)
and raise an error if this is not the case.
Then we can proceed with the normalisation,
confident that our normalisation function will work correctly.

Checking that input to a function is valid via a set of preconditions
is one of the simplest forms of **defensive programming**
which is used as a way of avoiding potential errors.
Preconditions are checked at the beginning of the function
to make sure that all assumptions are satisfied.
These assumptions are often based on the *value* of the arguments, like we have already discussed.
However, in a dynamic language like Python
one of the more common preconditions is to check that the arguments of a function
are of the correct *type*.
Currently there is nothing stopping someone from calling `patient_normalise` with
a string, a dictionary, or another object that is not an `ndarray`.

As an example, let us change the behaviour of the `patient_normalise()` function
to raise an error on negative inflammation values.
Edit the `inflammation/models.py` file,
and add a precondition check to the beginning of the `patient_normalise()` function like so:

~~~
...
    if np.any(data < 0):
        raise ValueError('Inflammation values should not be negative')
...
~~~
{: .language-python}

We can then modify our test function in `tests/test_models.py`
to check that the function raises the correct exception - a `ValueError` -
when input to the test contains negative values
(i.e. input case `[[-1, 2, 3], [4, 5, 6], [7, 8, 9]]`).
The [`ValueError`](https://docs.python.org/3/library/exceptions.html#ValueError) exception
is part of the standard Python library
and is used to indicate that the function received an argument of the right type,
but of an inappropriate value.

~~~
@pytest.mark.parametrize(
    "test, expected, expect_raises",
    [
        ... # previous test cases here, with None for expect_raises, except for the next one - add ValueError
        ... # as an expected exception (since it has a negative input value)
        (
            [[-1, 2, 3], [4, 5, 6], [7, 8, 9]],
            [[0, 0.67, 1], [0.67, 0.83, 1], [0.78, 0.89, 1]],
            ValueError,
        ),
        (
            [[1, 2, 3], [4, 5, 6], [7, 8, 9]],
            [[0.33, 0.67, 1], [0.67, 0.83, 1], [0.78, 0.89, 1]],
            None,
        ),
    ])
def test_patient_normalise(test, expected, expect_raises):
    """Test normalisation works for arrays of one and positive integers."""
    from inflammation.models import patient_normalise
    if expect_raises is not None:
        with pytest.raises(expect_raises):
            npt.assert_almost_equal(patient_normalise(np.array(test)), np.array(expected), decimal=2)
    else:
        npt.assert_almost_equal(patient_normalise(np.array(test)), np.array(expected), decimal=2)
~~~
{: .language-python}

Be sure to commit your changes so far and push them to GitHub.

> ## Optional Exercise: Add a Precondition to Check the Correct Type and Shape of Data
>
> Add preconditions to check that data is an `ndarray` object and that it is of the correct shape.
> Add corresponding tests to check that the function raises the correct exception.
> You will find the Python function
> [`isinstance`](https://docs.python.org/3/library/functions.html#isinstance)
> useful here, as well as the Python exception
> [`TypeError`](https://docs.python.org/3/library/exceptions.html#TypeError).
> Once you are done, commit your new files,
> and push the new commits to your remote repository on GitHub.
>
> > ## Solution
> >
> > In `inflammation/models.py`:
> >
> > ~~~
> > ...
> > def patient_normalise(data):
> >     """
> >     Normalise patient data between 0 and 1 of a 2D inflammation data array.
> >
> >     Any NaN values are ignored, and normalised to 0
> >
> >     :param data: 2D array of inflammation data
> >     :type data: ndarray
> >
> >     """
> >     if not isinstance(data, np.ndarray):
> >         raise TypeError('data input should be ndarray')
> >     if len(data.shape) != 2:
> >         raise ValueError('inflammation array should be 2-dimensional')
> >     if np.any(data < 0):
> >         raise ValueError('inflammation values should be non-negative')
> >     max = np.nanmax(data, axis=1)
> >     with np.errstate(invalid='ignore', divide='ignore'):
> >         normalised = data / max[:, np.newaxis]
> >     normalised[np.isnan(normalised)] = 0
> >     return normalised
> > ...
> > ~~~
> >
> > In `test/test_models.py`:
> >
> > ~~~
> > ...
> > @pytest.mark.parametrize(
> >     "test, expected, expect_raises",
> >     [
> >         ...
> >         (
> >             'hello',
> >             None,
> >             TypeError,
> >         ),
> >         (
> >             3,
> >             None,
> >             TypeError,
> >         ),
> >         (
> >             [[1, 2, 3], [4, 5, 6], [7, 8, 9]],
> >             [[0.33, 0.67, 1], [0.67, 0.83, 1], [0.78, 0.89, 1]],
> >             None,
> >         )
> >     ])
> > def test_patient_normalise(test, expected, expect_raises):
> >     """Test normalisation works for arrays of one and positive integers."""
> >     from inflammation.models import patient_normalise
> >     if isinstance(test, list):
> >         test = np.array(test)
> >     if expect_raises is not None:
> >         with pytest.raises(expect_raises):
> >             npt.assert_almost_equal(patient_normalise(test), np.array(expected), decimal=2)
> >     else:
> >         npt.assert_almost_equal(patient_normalise(test), np.array(expected), decimal=2)
> > ...
> > ~~~
> >
> > Note the conversion from `list` to `np.array` has been moved
> > out of the call to `npt.assert_almost_equal()` within the test function,
> > and is now only applied to list items (rather than all items).
> > This allows for greater flexibility with our test inputs,
> > since this wouldn't work in the test case that uses a string.
> >
> > {: .language-python}
> {: .solution}
>
{: .challenge}

If you do the challenge, again, be sure to commit your changes and push them to GitHub.

You should not take it too far by trying to code preconditions for every conceivable eventuality.
You should aim to strike a balance between
making sure you secure your function against incorrect use,
and writing an overly complicated and expensive function
that handles cases that are likely never going to occur.
For example, it would be sensible to validate the shape of your inflammation data array
when it is actually read from the csv file (in `load_csv`),
and therefore there is no reason to test this again in `patient_normalise`.
You can also decide against adding explicit preconditions in your code,
and instead state the assumptions and limitations of your code
for users of your code in the docstring
and rely on them to invoke your code correctly.
This approach is useful when explicitly checking the precondition is too costly.

## Improving Robustness with Automated Code Style Checks

Let's re-run Pylint over our project after having added some more code to it.
From the project root do:

~~~
$ pylint inflammation
~~~
{: .language-bash}

You may see something like the following in Pylint's output:

~~~
************* Module inflammation.models
...
inflammation/models.py:60:4: W0622: Redefining built-in 'max' (redefined-builtin)
...
~~~
{: .language-bash}

The above output indicates that by using the local variable called `max`
in the `patient_normalise` function,
we have redefined a built-in Python function called `max`.
This isn't a good idea and may have some undesired effects
(e.g. if you redefine a built-in name in a global scope
you may cause yourself some trouble which may be difficult to trace).

> ## Exercise: Fix Code Style Errors
>
> Rename our local variable `max` to something else (e.g. call it `max_data`), then rerun your tests and
> commit these latest changes and
> push them to GitHub using our usual feature branch workflow. Make sure your `develop` and `main` branches are up to date.
{: .challenge}

It may be hard to remember to run linter tools every now and then.
Luckily, we can now add this Pylint execution to our continuous integration builds
as one of the extra tasks.
Since we're adding an extra feature to our CI workflow,
let's start this from a new feature branch from the `develop` branch:

~~~
$ git checkout develop
$ git branch pylint-ci
$ git checkout pylint-ci
~~~
{: .language-bash}

Then to add Pylint to our CI workflow,
we can add the following step to our `steps` in `.github/workflows/main.yml`:

~~~
...
    - name: Check style with Pylint
      run: |
        python3 -m pylint --fail-under=0 --reports=y inflammation
...
~~~
{: .language-bash}

Note we need to add `--fail-under=0` otherwise
the builds will fail if we don't get a 'perfect' score of 10!
This seems unlikely, so let's be more pessimistic.
We've also added `--reports=y` which will give us a more detailed report of the code analysis.

Then we can just add this to our repo and trigger a build:

~~~
$ git add .github/workflows/main.yml
$ git commit -m "Add Pylint run to build"
$ git push
~~~
{: .language-bash}

Then once complete, under the build(s) reports you should see
an entry with the output from Pylint as before,
but with an extended breakdown of the infractions by category
as well as other metrics for the code,
such as the number and line percentages of code, docstrings, comments, and empty lines.

So we specified a score of 0 as a minimum which is very low.
If we decide as a team on a suitable minimum score for our codebase,
we can specify this instead.
There are also ways to specify specific style rules that shouldn't be broken
which will cause Pylint to fail,
which could be even more useful if we want to mandate a consistent style.

We can specify overrides to Pylint's rules in a file called `.pylintrc`
which Pylint can helpfully generate for us.
In our repository root directory:

~~~
$ pylint --generate-rcfile > .pylintrc
~~~
{: .language-bash}

Looking at this file, you'll see it's already pre-populated.
No behaviour is currently changed from the default by generating this file,
but we can amend it to suit our team's coding style.
For example, a typical rule to customise - favoured by many projects -
is the one involving line length.
You'll see it's set to 100, so let's set that to a more reasonable 120.
While we're at it, let's also set our `fail-under` in this file:

~~~
...
# Specify a score threshold to be exceeded before program exits with error.
fail-under=0
...
# Maximum number of characters on a single line.
max-line-length=120
...
~~~
{: .language-bash}

Don't forget to remove the `--fail-under` argument to Pytest
in our GitHub Actions configuration file too,
since we don't need it anymore.

Now when we run Pylint we won't be penalised for having a reasonable line length.
For some further hints and tips on how to approach using Pylint for a project,
see [this article](https://pythonspeed.com/articles/pylint/).

Before moving on, be sure to commit all your changes
and then merge to the `develop` and `main` branches in the usual manner,
and push them all to GitHub.

{% include links.md %}
