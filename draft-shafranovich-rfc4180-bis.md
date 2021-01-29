---
title: Common Format and MIME Type for Comma-Separated Values (CSV) Files
abbrev: rfc4180-bis
docname: draft-shafranovich-rfc4180-bis-00
ipr: trust200902
cat: info
pi:
  sortrefs: 'yes'
  strict: 'yes'
  symrefs: 'yes'
  toc: 'yes'
author:
- ins: Y. Shafranovich
  name: Yakov Shafranovich
  organization: Nightwatch Cybersecurity
  email: yakov+ietf@nightwatchcybersecurity.com
  
informative:
  ART:
    title: The Art of Unix Programming, Chapter 5
    author:
        name: E. Raymond
    date: September 2003
    target: http://www.catb.org/~esr/writings/taoup/html/ch05s02.html
  CREATIVYST:
    title: 'HOW-TO: The Comma Separated Value (CSV) File Format'
    author:
        name: J. Repici
        org: Creativyst, Inc.
    date: 2010
    target: http://www.creativyst.com/Doc/Articles/CSV/CSV01.htm
  CSVW:
    title: CSV on the Web Working Group
    author:
        org: W3C
    date: 2016
    target: https://www.w3.org/2013/csvw/wiki/Main_Page
  EDOCEO:
    title: Comma Separated Values (CSV) Standard File Format
    author:
        org: Edoceo, Inc.
    date: 2020
    target: https://edoceo.com/dev/csv-file-format

--- abstract
This RFC documents the common format used for Comma-Separated Values (CSV)
files and updates the associated MIME type "text/csv".

--- middle

# Introduction
The comma separated values format (CSV) has been used as a common way
to exchange data between disparate systems and applications for many years.
Surprisingly, while this format is very popular, it has never been formally
documented and didn't have a media type registered. This was addressed in 2005 via publication
of {{!RFC4180}} and the concurrent registration of the "text/csv" media type.
 
Since the publication of {{!RFC4180}}, the CSV format has evolved and this specification
seeks to reflect these changes as well as update the "text/csv" media type registration.

## Terminology
The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and
"OPTIONAL" in this document are to be interpreted as described in
BCP 14 {{!RFC2119}} {{!RFC8174}} when, and only when, they appear in all
capitals, as shown here.

# Definition of the CSV Format {#format}
While there had been various specifications and implementations for the
CSV format (for ex. {{CREATIVYST}}, {{EDOCEO}} and {{ART}})), prior to publication
of {{!RFC4180}} there is no attempt to provide a common specification. Since then,
the CSV format has evolved and there had also been additional attempts to formally
document this format (such as the past work of the W3C's {{CSVW}} group).
 
This section documents the format that seems to be followed by most implementations (incorporating
changes since the publication of {{!RFC4180}}):

1. Each record is located on a separate line, delimited by a line break (CRLF or LF). For example:

   aaa,bbb,cccCRLF<br/>
   zzz,yyy,xxxCRLF

2. The last record in the file MUST have an ending line break. For example:

   aaa,bbb,cccCRLF<br/>
   zzz,yyy,xxxCRLF

3. There MAY be an optional header line appearing as the first line
of the file with the same format as normal record lines. This
header will contain names corresponding to the fields in the file
and SHOULD contain the same number of fields as the records in
the rest of the file. Every name SHOULD be unique, if not empty.
The presence or absence of the header line MAY be indicated via the
optional "header" parameter of this MIME type. For example:

   field_name_1,field_name_2,field_name_3CRLF<br/>
   aaa,bbb,cccCRLF<br/>
   zzz,yyy,xxxCRLF

4. Within the header and each record, there MAY be one or more
fields, separated by commas. Each line SHOULD contain the same
number of fields throughout the file. Spaces are considered part
of a field and SHOULD NOT be ignored. The last field in the
record MUST NOT be followed by a comma. For example:

   aaa,bbb,ccc

5. Each field MAY or MAY not be enclosed in double quotes (however
some programs, do not use double quotes at all). If fields are not
enclosed with double quotes, then double quotes MAY not appear inside the fields.
Whitespace is allowed between the double quotes and commas/line breaks, and SHOULD
BE ignored. For example:

   "aaa","bbb","ccc"CRLF<br/>
   "aaa","bbb", "ccc" CRLF<br/>
   zzz,yyy,xxx

6. Fields containing line breaks (CR or CRLF), double quotes, and commas
MUST be enclosed in double-quotes. For example:

   "aaa","b CRLF<br/>
   bb","ccc"CRLF<br/>
   zzz,yyy,xxx

7. If double-quotes are used to enclose fields, then a double-quote
appearing inside a field MUST be escaped by preceding it with
another double quote. For example:

   "aaa","b""bb","ccc"

## Default charset and line break values
Since the initial publication of {{!RFC4180}}, the default charset for "text/*" media types
has been changed to UTF-8 (as per {{!RFC6657}}).

Although section 4.1.1. of {{!RFC2046}} defines CRLF to denote line breaks,
implementers MAY recognize a single LF as a line break.
However, some implementations MAY use other values.

## ABNF Grammar

The ABNF grammar (as per {{!RFC5234}}) appears as follows:

~~~~~~~~~~
file = [header [CR]LF] *(record [CR]LF)

header = name *(COMMA name)

record = field *(COMMA field)

name = field

field = (escaped / non-escaped)

escaped = *(WSP) DQUOTE *(TEXTDATA / COMMA / CR / LF / 2DQUOTE) DQUOTE *(WSP)

non-escaped = *TEXTDATA

COMMA = %x2C

CR = %x0D ;as per section B.1 of [RFC5234]

DQUOTE =  %x22 ;as per section B.1 of [RFC5234]

LF = %x0A ;as per section B.1 of [RFC5234]

CRLF = CR LF ;as per section B.1 of [RFC5234]

TEXTDATA =  %x20-21 / %x23-2B / %x2D-7E
~~~~~~~~~~

# Update to MIME Type Registration of text/csv {#registration}

The media type registration of "text/csv" should be updated as per specific
fields below: 

Encoding considerations:

> CSV MIME entities can consist of binary data
> as per section 4.8 of {{!RFC6838}}. Although section 4.1.1. of {{!RFC2046}} defines
> CRLF to denote line breaks, implementers MAY recognize a single LF
> as a line break. However, some implementations may use other values.

Published specification:

> While numerous private specifications exist for various programs
> and systems, there is no single "master" specification for this
> format. An attempt at a common definition can be found in {{!RFC4180}}
> and this document.
  
# IANA Considerations

IANA is directed to update the the MIME type registration for "text/csv"
as per instructions provided in {{registration}} of this document
and include a reference to this document within the registration.

# Security Considerations

All security considerations as discussed in {{!RFC4180}} still apply.

# Acknowledgments

In addition to everyone thanked previously in {{!RFC4180}}, the author would like to thank
acknowledge the contributions of the following people to this document:
Alperen Belgic, Abed BenBrahim, Benjamin Kaduk, Damon Koach, Barry Leiba,
Oliver Siegmar, Marco Diniz Sousa and Greg Skinner.

A special thank you to L.T.S.

--- back
# Changes since RFC 4180
- Removing dead references and updating references to newer versions
- Incorporating existing errata
- Changing text to reflect the previous publication
- Changing default encoding to UTF-8, and allowing both LF and CRLF for line breaks
- Allowing whitespace with double quotes

# Note to Readers

> **Note to the RFC Editor:** Please remove this section prior
> to publication.

Development of this draft takes place on Github at: https://github.com/nightwatchcyber/rfc4180-bis

Comments can also be sent to the ART mailing list at: https://www.ietf.org/mailman/listinfo/art

Full list of changes can be viewed via the IETF document tracker:
https://tools.ietf.org/html/draft-shafranovich-rfc4180-bis
