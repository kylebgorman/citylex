🗽 CityLex: a free multisource English lexical database
======================================================

[![PyPI
version](https://badge.fury.io/py/wikipron.svg)](https://pypi.org/project/wikipron)
[![Supported Python
versions](https://img.shields.io/pypi/pyversions/wikipron.svg)](https://pypi.org/project/wikipron)

CityLex is an English lexical database intended to replace or enhance databases
like [CELEX](https://catalog.ldc.upenn.edu/LDC96L14). It combines data from up
to seven unique sources, including frequency norms, morphological analyses, and
pronunciations. Since these have varying license conditions (some are
proprietary, others restrict redistribution), we do not provide the database as
is. Rather the user must generate a personal copy by executing a Python script,
enabling whatever sources they wish to use.

Building your own CityLex
-------------------------

To install CityLex execute

    pip install git+https://github.com/kylebgorman/citylex.git

To see the available data sources and options, execute `citylex --help`.

To generate the lexicon, execute `citylex` with at least one source enabled
using command-line flags. As most of the data is downloaded from outline
sources, an internet connection is normally required. The process takes roughly
four minutes with all sources enabled; much of the time is spent downloading
large files.

To generate a lexicon with all the sources that don't require manual downloads,
execute

    citylex --cmudict \
            --elp \
            --subtlex_uk \
            --subtlex_uk \
            --udlexicons \
            --unimorph \
            --wikipron

File formats
------------

Two files are produced. The first, by default `citylex.tsv`, is a standard
wide-format "tab separated values" (TSV) file, of the sort that can be read into
Excel or R. Some fields (particularly pronunciations and morphological analyses)
can have multiple entries per wordform. In this case, they are separated using
the `^` character.

Advanced users may wish to make use of the second file, by default
`citylex.textproto`, a
[text-format](https://developers.google.com/protocol-buffers/docs/reference/python/google.protobuf.text_format-module)
[protocol buffer](https://developers.google.com/protocol-buffers/) which
provides a better representation of the repeated fields. To parse this file in
Python, use the following snippet:

``` {.sourceCode .python}
from google.protobuf import text_format

import citylex_pb2

lexicon = citylex_pb2.Lexicon()
with open("citylex.textproto", "r") as source: 
   text_format.ParseLines(source, lexicon)
```

This will parse the text-format data and populate `lexicon`. One can then
iterate over `lexicon.entry` like a Python dictionary.

Non-redistributable data sources
--------------------------------

Not all CityLex data can be obtained automatically from online sources. If you
wish to enable CELEX features, follow the instructions below.

This proprietary resource must be obtained from the [Linguistic Data
Consortium](https://catalog.ldc.upenn.edu/LDC96L14) as `LDC96L14.tgz`. The file
should be decompressed using

    tar -xzf LDC96L14.tgz

This will produce a directory named `celex2`. To enable CELEX2 features, use
`--celex` and pass the local path of this directory as an argument to
`--celex_path`.

For more information
--------------------

-   `citylex.proto` for the protocol buffer data structure
-   `citylex.bib` for references to the data sources used

For contributors
----------------

To regenerate `citylex_pb2.py` you will need to install the [Protocol Buffers
C++ runtime](https://github.com/protocolbuffers/protobuf) for your platform,
making sure the version number (e.g., the one returned by `protoc --version`
matches that of `protobuf` in `requirements.txt`. Then, run
`protoc --python_out=. citylex.proto`.

License
-------

The CityLex codebase are distributed under the Apache 2.0 license. Please see
[`License.txt`](LICENSE.txt) for details.

All other data sources bear their original licenses chosen by their creators;
see `citylex --help` for more information.

Author
------

CityLex was created by [Kyle Gorman](http://wellformedness.com).
