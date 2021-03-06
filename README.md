
ASN.1 library for Python
------------------------
[![Downloads](https://img.shields.io/pypi/dm/pyasn1.svg)](https://pypi.python.org/pypi/pyasn1)
[![Build status](https://travis-ci.org/etingof/pyasn1.svg?branch=master)](https://secure.travis-ci.org/etingof/pyasn1)
[![Coverage Status](https://img.shields.io/codecov/c/github/etingof/pyasn1.svg)](https://codecov.io/github/etingof/pyasn1)
[![GitHub license](https://img.shields.io/badge/license-BSD-blue.svg)](https://raw.githubusercontent.com/etingof/pyasn1/master/LICENSE.txt)

This is a free and open source implementation of ASN.1 types and codecs
as a Python package. It has been first written to support particular
protocol (SNMP) but then generalized to be suitable for a wide range
of protocols based on
[ASN.1 specification](https://www.itu.int/rec/dologin_pub.asp?lang=e&id=T-REC-X.208-198811-W!!PDF-E&type=items).

Features
--------

* Generic implementation of ASN.1 types (X.208)
* Fully standard compliant BER/CER/DER codecs
* 100% Python, works with Python 2.4 up to Python 3.5
* MT-safe
* Contributed ASN.1 compiler [Asn1ate](https://github.com/kimgr/asn1ate)

Download
--------

The pyasn1 package is distributed under terms and conditions of 2-clause
BSD [license](http://pyasn1.sourceforge.net/license.html). Source code is freely
available as a Github [repo](https://github.com/etingof/pyasn1).

Installation
------------

Download pyasn1 from [PyPI](https://pypi.python.org/pypi/pyasn1) or just run:

```bash
$ pip install pyasn1
```

How to use pyasn1
-----------------

With pyasn1 you can build ASN.1 structures from Python objects. For example, the
following ASN.1 structure:

```bash
Record ::= SEQUENCE {
  id        INTEGER,
  room  [0] INTEGER OPTIONAL,
  house [1] INTEGER DEFAULT 0
}
```

Could be expressed in pyasn1 like this:

```python
class Record(Sequence):
    componentType = NamedTypes(
        NamedType('id', Integer()),
        OptionalNamedType(
            'room',
            Integer().subtype(
                implicitTag=Tag(tagClassContext, tagFormatSimple, 0)
            )
        ),
        DefaultedNamedType(
            'house', 
            Integer(0).subtype(
                implicitTag=Tag(tagClassContext, tagFormatSimple, 1)
            )
        )
    )
```

Once you have your ASN.1 data structure defined, you can use it as a conventional
data structure:

```python
>>> record = Record()
>>> record['id'] = 123
>>> record[1] = 321
>>> print(record.prettyPrint())
Record:
 id=123
 room=321
>>>
```

The power of ASN.1 comes with its serialization features. You can serialize your
initialized data structure and send it over the network. Conversely, you can
turn serialized ASN.1 content, as received from network or read from a file,
into a fully-fledged, initialized data structure.

```python
>>> substrate = encode(record)
>>> substrate
b'0\x07\x02\x01{\x80\x02\x01A'
>>>
>>> decoded_record, rest_of_substrate = decode(substrate, asn1Spec=Record())
>>>
>>> print(decoded_record.prettyPrint())
Record:
 id=123
 room=321
 house=0
>>>
>>> record == decoded_record
True
>>>
```

Many high-profile Internet protocols and file formats utilize ASN.1 serialization.
Quite a number of books cover the topic of ASN.1. 
[Communication between heterogeneous systems](http://www.oss.com/asn1/dubuisson.html)
by Olivier Dubuisson is one of high quality books freely available on the Internet.

Detailed information on pyasn1 APIs can be found in the
[documentation](http://pyasn1.sourceforge.net),
working examples are in the pyasn1-modules
[repo](https://github.com/etingof/pyasn1-modules).

Feedback
--------

If something does not work as expected, try browsing pyasn1
[mailing list archives](https://sourceforge.net/p/pyasn1/mailman/pyasn1-users/)
or post your question
[to Stack Overflow](http://stackoverflow.com/questions/ask).  

Copyright (c) 2005-2016, [Ilya Etingof](http://ilya@glas.net).
All rights reserved.
