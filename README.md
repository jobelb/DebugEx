Testing and Debugging News Applications in Python
=================================================

## Assumptions

* Python 2.7, because that's what is installed in the NICAR lab
* Git
* Know how to handle a Python exception with `try`/`except`.
* Know how to implement a Python class and class methods.

## Roadmap

* Running tests 
* Reading a traceback
* Outputting debugging messages
* Writing testable code

## Getting started

Clone the this repository:

    git clone origin 


## Running tests

The Python standard library includes [unittest](https://docs.python.org/2/library/unittest.html), a unit testing framework.  We'll talk more about how to write tests soon, but first, let's learn how to run tests.  We're going to do this first because we'll interact with all the code we're going to write and fix in the excercises for this session through unit test cases.

Let's try running a module containing tests: 

    python -m unittest tests.test_basic

`unittest` is running tests in the module `tests.test_basic` (which can be found in the `tests/test_basic.py`) file in the current directory.

You should see output like:

    F..
    ======================================================================
    FAIL: test_true_is_true (tests.test_basic.FailingTestCase)
    ----------------------------------------------------------------------
    Traceback (most recent call last):
      File "tests/test_basic.py", line 13, in test_true_is_true
        self.assertEqual(False, True)
    AssertionError: False != True

    ----------------------------------------------------------------------
    Ran 3 tests in 0.000s

    FAILED (failures=1)


You can run a specific test case:

    python -m unittest tests.test_basic.NoFailuresTestCase 

You should see output like this:

    ..
    ----------------------------------------------------------------------
    Ran 2 tests in 0.000s

    OK

You can even run a specific test within a test case:

    python -m unittest tests.test_basic.NoFailuresTestCase.test_true_is_true

Output should look like this:

    .
    ----------------------------------------------------------------------
    Ran 1 test in 0.000s

    OK

## Reading an error message 

When there is an unhandled exception in your code, Python will display an error message and a **traceback**.  A traceback shows the code that was called leading up to an error. 

Here's an example traceback from the publishing tools we use for dynamic visualizations and interactives at the Chicago Tribune:

    Traceback (most recent call last):
      File "/usr/local/bin/tarbell", line 9, in <module>
        load_entry_point('tarbell==1.0.4', 'console_scripts', 'tarbell')()
      File "/usr/local/lib/python2.7/site-packages/tarbell/cli.py", line 74, in main
        command.__call__(command, args)
      File "/usr/local/lib/python2.7/site-packages/tarbell/cli.py", line 931, in __call__
        return self.fn(*args, **kw_args)
      File "/usr/local/lib/python2.7/site-packages/tarbell/cli.py", line 390, in tarbell_publish
        site.call_hook("publish", site, s3)
      File "/usr/local/lib/python2.7/site-packages/tarbell/app.py", line 359, in call_hook
        function.__call__(*args, **kwargs)
      File "/Users/abercrombiesmyth/tarbell/bears85/_blueprint/blueprint.py", line 115, in p2p_publish
        created, response = p2p_conn.create_or_update_content_item(content_item)
      File "/usr/local/lib/python2.7/site-packages/p2p/__init__.py", line 290, in create_or_update_content_item
        response = self.update_content_item(content_item)
      File "/usr/local/lib/python2.7/site-packages/p2p/__init__.py", line 252, in update_content_item
        resp = self.put_json("/content_items/%s.json" % slug, d)
      File "/usr/local/lib/python2.7/site-packages/p2p/__init__.py", line 727, in put_json
        return utils.parse_response(resp.json())
      File "/usr/local/lib/python2.7/site-packages/requests/models.py", line 763, in json
        return json.loads(self.text, **kwargs)
      File "/Library/Python/2.7/site-packages/simplejson/__init__.py", line 505, in loads
        return _default_decoder.decode(s)
      File "/Library/Python/2.7/site-packages/simplejson/decoder.py", line 370, in decode
        obj, end = self.raw_decode(s)
      File "/Library/Python/2.7/site-packages/simplejson/decoder.py", line 400, in raw_decode
        return self.scan_once(s, idx=_w(s, idx).end())
    simplejson.scanner.JSONDecodeError: Expecting value: line 1 column 1 (char 0)

This traceback is rather long and involves a number of calls from Python modules in the project, the standard library and packages from PyPI.  Let's look at a simpler example to learn how to read an error message in Python.

Run the tests for a simple elections result loader:

    python -m unittest tests.test_result_loader

The tests fail, and show the following error output:

    E
    ======================================================================
    ERROR: test_load_bad_json (tests.test_result_loader.SimpleResultLoaderTestCase)
    ----------------------------------------------------------------------
    Traceback (most recent call last):
      File "tests/test_result_loader.py", line 13, in test_load_bad_json
        results = loader.load(sample_json)
      File "results/__init__.py", line 5, in load
        parsed = json.loads(s)
      File "/usr/local/Cellar/python/2.7.11/Frameworks/Python.framework/Versions/2.7/lib/python2.7/json/__init__.py", line 339, in loads
        return _default_decoder.decode(s)
      File "/usr/local/Cellar/python/2.7.11/Frameworks/Python.framework/Versions/2.7/lib/python2.7/json/decoder.py", line 364, in decode
        obj, end = self.raw_decode(s, idx=_w(s, 0).end())
      File "/usr/local/Cellar/python/2.7.11/Frameworks/Python.framework/Versions/2.7/lib/python2.7/json/decoder.py", line 382, in raw_decode
        raise ValueError("No JSON object could be decoded")
    ValueError: No JSON object could be decoded

    ----------------------------------------------------------------------
    Ran 1 test in 0.001s

    FAILED (errors=1)

The last line of the message is the exception that was raised:

    ValueError: No JSON object could be decoded

Prior to that, the traceback shows the calls leading up to and including the one that caused the error.  These are grouped in pairs of lines, where the first line shows the file name, line number and function name where the call occurred.  The second line in the pair shows the the code that was executed.

The call that raised the exception is shown as the last line in the traceback:

    File "/usr/local/Cellar/python/2.7.11/Frameworks/Python.framework/Versions/2.7/lib/python2.7/json/decoder.py", line 382, in raw_decode
      raise ValueError("No JSON object could be decoded")

The first line is the code where execution started:

    File "tests/test_result_loader.py", line 13, in test_load_bad_json
      results = loader.load(sample_json)

The execution that eventually caused the error starts in our test case.

Update the result loader so it catches the `ValueError` and returns an empty list.  From the traceback, can you see where we should look to fix our code?




## Outputting debugging messages

### pprint

### logging.debug

### repr() methods in your classes

## Writing testable code

### Being explicit about assumptions with `assert`

### Debugging with pdb


    def get_race_name(cls, race):
        race_name = super(IllinoisAPAPIResultLoader, cls).get_race_name(race)
        race_name = race_name.replace("State House", "Illinois House")
        race_name = race_name.replace("State Senate", "Illinois Senate")
        if race.seatname and "County" in race.seatname and race_name.endswith(race.seatname):
            # Put the county name at the beginning of the race instead of the end
            # and remove county from the office name
            race_name = (race.seatname + " " +
                         race_name.replace(race.seatname, "")
                                  .replace("County", "").strip())

## Glossary

Unit test

Test case

Test suite

Exception: An unexpected error detected during program execution.  The [Errors and Exceptions](https://docs.python.org/2/tutorial/errors.html) section of the [Python Tutorial](https://docs.python.org/2/tutorial/) is a useful reference on exceptions.