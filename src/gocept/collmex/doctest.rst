Collmex API
===========

Collmex provides a POST- and CSV-based API, which is encapsulated into a
utility that provides methods for the various CSV record types.  API
documentation is available at
http://www.collmex.de/cgi-bin/cgi.exe?1005,1,help,api.


The collmex object
------------------

The collmex object is a central place to access collmex. In the Zope 3 jargon
it is a global utility:

>>> import os
>>> import gocept.collmex.collmex
>>> os.environ['collmex_credential_section'] = 'test-credentials'
>>> collmex = gocept.collmex.collmex.Collmex()


Pre flight cleanup
------------------

First we need to clean up the Collmex environment:

>>> import gocept.collmex.testing
>>> gocept.collmex.testing.cleanup_collmex()


Transaction integration
-----------------------

gocept.collmex has support for transaction integration. All modifying calls are
buffered until the transaction is commited. XXX explain more.

>>> import transaction


Customers: ``create_customer`` and ``get_customers``
----------------------------------------------------

>>> customer = gocept.collmex.model.Customer()
>>> customer['Kundennummer'] = 10000
>>> customer['Firma'] = 'Testkunden'
>>> collmex.create(customer)
>>> transaction.commit()

Customers can be listed using the get_customers method:

>>> customers = collmex.get_customers()
>>> customers
[<gocept.collmex.model.Customer object at 0x...>, <gocept.collmex.model.Customer object at 0x...>]
>>> len(customers)
2

The first customer is the generic one:

>>> customer = customers[0]
>>> customer['Satzart']
'CMXKND'
>>> customer['Kundennummer']
'9999'
>>> customer['Firma']
'Allgemeiner Geschäftspartner'

The second customer is one created during test setup:

>>> customer = customers[1]
>>> customer['Satzart']
'CMXKND'
>>> customer['Kundennummer']
'10000'
>>> customer['Firma']
'Testkunden'

Products: ``create_product`` and ``get_products``
-------------------------------------------------

Products are created using the ``create_product`` method:

>>> product = gocept.collmex.model.Product()
>>> product['Produktnummer'] = 'TEST'
>>> product['Bezeichnung'] = 'Testprodukt'
>>> product['Produktart'] = 1 # Dienstleistung
>>> product['Basismengeneinheit'] = 'HR'
>>> product['Verkaufs-Preis'] = 5
>>> collmex.create(product)
>>> transaction.commit()
>>> collmex.get_products()[0]['Bezeichnung']
'Testprodukt'

Product Groups
--------------
>>> group = gocept.collmex.model.ProductGroup()
>>> group['Produktgruppe Nr'] = '2'
>>> group['Bezeichnung'] = 'Testproduktgruppe'
>>> group['Untergruppe von'] = 'Hauptproduktgruppe'
>>> collmex.create(group)
>>> transaction.commit()
>>> collmex.get_product_groups()[-1]['Bezeichnung']
'Testproduktgruppe'

Customer Agreements
-------------------

>>> cag = gocept.collmex.model.CustomerAgreement()
>>> cag['Kunde Nr'] = '10000'
>>> cag['Firma Nr'] = 1
>>> cag['Produktnummer'] = 'TEST'
>>> cag['Gültig ab'] = '01.01.2000'
>>> cag['Gültig bis'] = '31.12.9999'
>>> cag['Preis'] = 7
>>> cag['Währung'] = "EUR"
>>> collmex.create(cag)
>>> transaction.commit()
>>> cag_from_collmex = collmex.get_customer_agreements()
>>> list(cag)
['CMXCAG', '1', '10000', 'TEST', '(NULL)', '01.01.2000', '31.12.9999', 7, 'EUR', '(NULL)']


Invoices: ``create_invoice`` and ``get_invoices``
-------------------------------------------------

Invoices are created using the ``create_invoice`` method:

>>> import datetime
>>> start_date = datetime.datetime.now()
>>> item = gocept.collmex.model.InvoiceItem()
>>> item['Kunden-Nr'] = '10000'
>>> item['Rechnungsnummer'] = 100000
>>> item['Menge'] = 3
>>> item['Produktnummer'] = 'TEST'
>>> item['Rechnungstext'] = 'item text \u2013 with non-ascii characters'
>>> item['Positionstyp'] = 0
>>> collmex.create_invoice([item])

Invoices can be looked up again, using the ``get_invoices`` method. However, as
discussed above the invoice was only registered for addition. Querying right
now does *not* return the invoice:

>>> collmex.get_invoices(customer_id='10000', start_date=start_date)
[]

After committing, the invoice is found:

>>> transaction.commit()
>>> collmex.get_invoices(customer_id='10000',
...                      start_date=start_date)[0]['Rechnungstext']
'item text – with non-ascii characters'

Activities
----------

This section describes the API for activities (Taetigkeiten erfassen)

Create an activity
~~~~~~~~~~~~~~~~~~

A project with one set and an employee are required to submit activities:

>>> import datetime
>>> import gocept.collmex.testing
>>> gocept.collmex.testing.create_project('Testprojekt', collmex=collmex)
>>> gocept.collmex.testing.create_employee(collmex)
>>> act = gocept.collmex.model.Activity()
>>> act['Projekt Nr'] = '1' # Testprojekt
>>> act['Mitarbeiter Nr'] = '1' # Sebastian Wehrmann
>>> act['Satz Nr'] = '1' # TEST
>>> act['Beschreibung'] = 'allgemeine T\xe4tigkeit'
>>> act['Datum'] = datetime.date(2012, 1, 23)
>>> act['Von'] = datetime.time(8, 7)
>>> act['Bis'] = datetime.time(14, 28)
>>> act['Pausen'] = datetime.timedelta(hours=1, minutes=12)
>>> collmex.create(act)
>>> transaction.commit()

Export using ``get_activities``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

``get_activities`` returns Activity objects.

.. ATTENTION:: In previous versions this method returnd a raw CSV string. This
      was due to Collmex not having an actual API.


>>> activities = collmex.get_activities()
>>> activities[0]['Beschreibung']
'allgemeine T\xe4tigkeit'


Projects: ``get_projects``
--------------------------

Projects can be exported with the ``get_projects`` API. It returns an entry
for every project set (Projektsatz) of each project (Projekt):

>>> proj = collmex.get_projects()
>>> len(proj)
2
>>> proj[0]['Projektnummer'] == proj[1]['Projektnummer']
True

>>> proj[0]['Satz']
'7,00'
>>> proj[1]['Satz']
'9,65'
>>> proj[0]['Inaktiv']
'0'

Caching
-------

Results queried from Collmex are cached for the duration of the transaction.

To demonstrate this, we instrument the _post() method that performs the actual
HTTP communication to show when it is called:

>>> original_post = collmex._post
>>> def tracing_post(self, *args, **kw):
...     print('cache miss')
...     return original_post(*args, **kw)
>>> collmex._post = tracing_post.__get__(collmex, type(collmex))

The first time in an transaction is retrieved from Collmex, of course:

>>> transaction.abort()
>>> collmex.get_products()[0]['Bezeichnung']
cache miss
'Testprodukt'

But after that, values are cached:

>>> collmex.get_products()[0]['Bezeichnung']
'Testprodukt'

When the transaction ends, the cache is invalidated:

>>> transaction.commit()
>>> collmex.get_products()[0]['Bezeichnung']
cache miss
'Testprodukt'

>>> collmex.get_products()[0]['Bezeichnung']
'Testprodukt'

Remove tracing instrumentation:

>>> collmex._post = original_post
