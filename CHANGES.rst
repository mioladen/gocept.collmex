Changes
=======

2.1.1 (unreleased)
------------------

- Add ProductGroup (PRDGRP)


2.1.0 (2024-05-31)
------------------

- Update invoice items to current collmex field names.
  (`#27 <https://github.com/gocept/gocept.collmex/pull/27>`_)

- Add CustomerAgreement (CMXCAG)


2.0.1 (2023-08-23)
------------------

- Mark wheel as not universal.


2.0 (2023-08-23)
----------------

- Drop support for Python 2.7, 3.5, 3.6.

- Add support for Python 3.9, 3.10, 3.11.

- Avoid password exhaustion by using invalid username for tests.

- Fix the tests to use fewer connections.


1.9 (2019-09-02)
----------------

- Drop support for Python 3.3 and 3.4.

- Add support for Python 3.6, 3.7 and 3.8b4.

- Migrate code to GitHub.

- Update tests to new Collmex URLs.


1.8.3 (2018-03-16)
------------------

- Implemented CMXABO, only applicable for "collmex Verein".


1.8.2 (2017-01-23)
------------------

- Implemented `MITGLIED_GET` as `get_members()` and `CMXMGD`
  as `models.Member`, both only applicable for "collmex Verein".


1.8.1 (2016-06-09)
------------------

- Extend `Project` to retrieve budget and summed up work via API.


1.8.0 (2016-01-27)
------------------

- Declared compatibility with Python 3.4.

- Drop support for Python 3.2.

- Made sure tests don't use invalid credentials on test account too many times
  in a row.

- Raise ``ValueError`` if the Collmex website returned an error during
  ``Collmex.browser_login``. Until now the error was hidden, but the following
  action failed. In particular this should help to spot invalid credentials.



1.7.0 (2014-09-04)
------------------

- Use collmex.ini file given by the path in environment variable
  ``COLLMEX_INI`` if present, only otherwise look upward from the current
  directory.

- Don't log the password in our debug output.


1.6.0 (2014-08-18)
------------------

- Un-deprecate method ``Collmex.create_invoice``, it now takes care of
  automatically allocating invoice ids.

- Add ``gocept.collmex.testing.ConsoleDump`` utility that fakes a collmex
  connection, but only logs method calls.


1.5.1 (2013-12-09)
------------------

- Fix brown bag release 1.5.0


1.5.0 (2013-12-09)
------------------

- Added methods to Activity to parse/calculate the dates, times, breaks,
  duration.


1.4.4 (2013-09-25)
------------------

- Improve check for invalid credentials when ``gocept.collmex.collmex.Collmex``
  is created.

- Yield more detailed information when parsing of the ini file failed.

1.4.3 (2013-09-24)
------------------

- Fix the check for invalid credentials when ``gocept.collmex.collmex.Collmex``
  is created.


1.4.2 (2013-09-23)
------------------

- Credentials to log into Collmex can be given via an `collmex.ini` file.


1.4.1 (2013-09-16)
------------------

- Creation of test activities is now fully customizable


1.4 (2013-09-13)
----------------

- gocept.collmex is now compatible with Python 3.2 and Python 3.3!


1.3 (2013-02-22)
----------------

- Implement activities API (#11954).


1.2.1 (2012-03-09)
------------------

- Fix ``gocept.collmex.collmex.Collmex.browser_login()`` after collmex website
  changed wich included a rename of form elements.



1.2 (2012-02-21)
----------------

Note: This version was accidentally released without the changes in 1.1.1,
however, the release itself contained the changes of 1.1 and thus isn't
broken.

- Add ``Inaktiv`` attribute on ``CMXPRJ`` (projects)


1.1.1 (2012-02-17)
------------------

- Rectify previous brown-bag release.


1.1 (2012-02-17)
----------------

- Do not honour Collmex' robots.txt as of 2012-02-09. :(


1.0 (2012-01-23)
----------------

- Forced usage of Python 2.7.

- Added testing helper ``get_collmex`` to create a collmex object from
  environment variables.

- Made testing helper ``collmex_login()`` a method: ``gocept.collmex.collmex.Collmex.browser_login()``

- Modified signature of testing helper ``create_activity``, so it no longer
  needs a parameter.


0.9 (2012-01-20)
----------------

- Added testing helper ``create_activity``.


0.8 (2012-01-20)
----------------

- Added API for retrieving activities (``get_activities``).

- Updated tests and test infrastructure to recent changes in Collmex.


0.7 (2009-11-05)
----------------

- Added API for retrieving projects and creation of activities.

0.6 (2009-02-16)
----------------

- Make models robust against API changes so they don't immediately break when
  the record becomes longer.
- Updated customer model to current API.

0.5.1 (2009-01-08)
------------------

- Fixed multi-threading bug: thread-local data needs to be intialized for each
  thread.

0.5 (2008-12-19)
----------------

- Values returned from Collmex are converted to unicode.
- Cache results for the duration of the transaction.

0.4 (2008-12-11)
----------------

- Added `get_products` and `create_product`.
- Added `create_customer`.
- gocept.collmex.testing.cleanup_collmex() now only deletes any existing data,
  it does not add any sample customers or products, use the API for that.

0.3.1 (2008-12-02)
------------------

- Python 2.5 compatibility.

0.3 (2008-12-01)
----------------

- Using Windows-1252 as encoding when uploading data (used to be ISO-8859-1).
- Fixed transaction integration when upload fails.

0.2 (2008-11-28)
----------------

- Modifications for changed Collmex API.
- Added ``get_customers`` to query customers (API ``CUSTOMER_GET``).

0.1 (2008-10-14)
----------------

- first release. Supports getting and storing invoices.
