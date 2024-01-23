---
title: Common Format and MIME Type for Comma-Separated Values (CSV) Files
abbrev: rfc4180-bis
docname: draft-shafranovich-rfc4180-bis-06
ipr: trust200902
cat: info
submissionType: independent
pi:
  sortrefs: 'yes'
  strict: 'yes'
  symrefs: 'yes'
  toc: 'yes'
author:
- ins: Y. Shafranovich
  name: Yakov Shafranovich
  organization: Amazon Web Services (AWS)
  email: yakovsh@amazon.com or ietf@shaftek.org

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
    target: https://www.creativyst.com/Doc/Articles/CSV/CSV01.htm
  CSVW:
    title: Model for Tabular Data and Metadata on the Web
    author:
        org: W3C
    date: December 17th, 2015
    target: https://www.w3.org/TR/tabular-data-model/
  EDOCEO:
    title: Comma Separated Values (CSV) Standard File Format
    author:
        org: Edoceo, Inc.
    date: 2020
    target: https://edoceo.com/dev/csv-file-format
  UNICODE:
    title: The Unicode Standard, Version 15.0.0
    author:
      org: The Unicode Consortium
    date: September 2022
    target: https://www.unicode.org/versions/Unicode15.0.0/

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

## Motivation For and Status of This Document
The original motivation of {{!RFC4180}} was to provide a reference
in order to register the media type "text/csv". It tried to document
existing practices at the time based on the approaches used by most implementations.
This document continues to do the same, and updates the original document to reflect
current practices for generating and consuming of CSV files.

Both {{!RFC4180}} and this document are published as informational RFC for the benefit
of the Internet community and not intended to be used as formal standards.
Implementers should consult {{?RFC1796}} and {{?RFC2026}} for crucial differences
between IETF standards and informational RFCs.

# Definition of the CSV Format {#format}
While there had been various specifications and implementations for the
CSV format (for ex. {{CREATIVYST}}, {{EDOCEO}}, {{CSVW}} and {{ART}})), prior to publication
of {{!RFC4180}} there is no attempt to provide a common specification. This section documents
the format that seems to be followed by most implementations (incorporating
changes since the publication of {{!RFC4180}} and listing common implementation concerns).

## High level description {#desc}
The CSV format uses line breaks to separate records, and commas to separate fields within a given record.
The format is described as follows:

1. Each record is located on a separate line, ended by a line break (CR, LF or CRLF) indicating
the end of this record. For example:

   aaa,bbb,cccCRLF<br/>
   zzz,yyy,xxxCRLF

2. The last record in the file MUST have an ending line break indicating the end of a record. For example:

   aaa,bbb,cccCRLF<br/>
   zzz,yyy,xxxCRLF

3. The first record in the file MAY be an optional header and MUST follow
the same format as normal records. This
header contains names corresponding to the fields in the file
and SHOULD contain the same number of fields as the records in
the rest of the file. For example:

   field_name_1,field_name_2,field_name_3CRLF<br/>
   aaa,bbb,cccCRLF<br/>
   zzz,yyy,xxxCRLF

4. Within each record, there MAY be zero or more fields, separated by commas. Each record SHOULD contain the same
number of fields throughout the file. Spaces are considered part
of a field and SHOULD NOT be ignored. The last field in the
record MUST NOT be followed by a comma (since this will indicate an empty field following the comma).
For example:

   aaa,bbb,cccCRLF

5. Each field MAY be enclosed in double quotes. If fields are not
enclosed with double quotes, then double quotes MUST NOT appear inside the fields.
For example:

   "aaa","bbb","ccc"CRLF<br/>
   zzz,yyy,xxxCRLF

6. Fields containing line breaks (CR, LF or CRLF), double quotes, or commas
MUST be enclosed in double quotes. For example:

   "aaa","b CRLF<br/>
   bb","c,c,c"CRLF<br/>
   zzz,yyy,xxxCRLF

7. A double quote appearing inside a field MUST be escaped by preceding it with
another double quote. For example:

   "aaa","b""bb","ccc"CRLF

## Default charset, binary content and line break values
Since the initial publication of {{!RFC4180}}, the default charset for "text/*" media types
has been changed to UTF-8 (as per {{!RFC6657}}) and {{!RFC7111}}.
This document reflects this change and the default charset for CSV files is now UTF-8.

As per section 4.2.1 of {{!RFC6838}}, the "text/*" media types are defined as those reasonable to present
to the user. While {{!RFC4180}} restricted CSV contents to printable ASCII only,
{{!RFC7111}} updated the MIME registration to allow binary content in CSV entities.
Therefore, this document has been updated to allow binary content within CSV files.

Although section 4.1.1. of {{!RFC2046}} defines CRLF to denote line breaks,
implementers MAY recognize a single CR or LF as a line break (similar to section 3.1.1.3
of {{?RFC7231}}). However, some implementations MAY use other values.

## ABNF Grammar

The ABNF grammar (as per {{!RFC5234}}) appears as follows:

~~~~~~~~~~
file = [header] *(record)

header = [field] *(COMMA field) linebreak

record = [field] *(COMMA field) linebreak

field = (escaped / non-escaped)

escaped = DQUOTE *(textdata / COMMA / CR / LF / 2DQUOTE) DQUOTE

non-escaped = *(textdata)

textdata = %x00-09 / %x0B-0C / %x0E-21 / %x23-2B / %x2D-7F / UTF8-data
         ; all characters except LF, CR, DQUOTE and COMMA

linebreak = CR / LF / CRLF

COMMA = %x2C

CR = %x0D ; as per section B.1 of [RFC5234]

CRLF = CR LF ; as per section B.1 of [RFC5234]

DQUOTE = %x22 ; as per section B.1 of [RFC5234]

LF = %x0A ; as per section B.1 of [RFC5234]

UTF8-data = UTF8-2 / UTF8-3 / UTF8-4 ; as per section 4 of [RFC3629]
~~~~~~~~~~

Note that the authoritative definition of UTF-8 is in section 2.5 of {{UNICODE}}.

# Common implementation concerns
This section describes some common concerns that may arise when
producing or parsing CSV files. These are not part of the formal definition of CSV
and are included for awareness only. Implementers may also use other means to handle
these use cases including approaches like {{CSVW}}.

## Null values
Some implementations (such as databases) treat empty fields and null values differently.
For these implementations, there is a need to define a special value representing
a null. However, this specification does not attempt to define a default value
for nulls.

Example of a CSV file with nulls (if "NULL" is used to mark nulls):

   field_name_1,field_name_2,field_name_3CRLF<br/>
   aaa,bbb,cccCRLF<br/>
   zzz,NULL,xxxCRLF

## Empty files
Implementers should be aware that in accordance to this specification a file
does not need to contain any comments or records. Therefore, an empty file with
zero bytes is considered valid.

## Empty lines
This specification recommends but doesn't require having the same number of fields
in every line. This allows CSV files to have empty lines without
any fields at all. Implementors may choose to skip empty lines
instead of parsing them but this specification does not dictate such behavior.

Example of a CSV file with empty lines:

   field_name_1,field_name_2,field_name_3CRLF<br/>
   aaa,bbb,cccCRLF<br/>
   CRLF<br/>
   zzz,yyy,xxxCRLF

However, if the records are only made up of one field it is not possible to
differentiate between an empty line, and an empty and unquoted field. This
differentiation might play an important role in some implementations such
as database exports/imports.

Example of a CSV file with empty lines and only one field per record:

aaaCRLF<br/>
CRLF<br/>
bbbCRLF

Note that some implementations may interpret the presence of a line break
after the last record in the file as a start of a new but empty record.

## Fields spanning multiple lines
When quoted fields are used, it is possible for a field to span multiple lines,
even when line breaks appear within such field.

## Unique header names
Implementers should be aware that some applications may treat header values as unique
(either case-sensitive or case-insensitive).

## Whitespace outside quoted fields
When quoted fields are used, this document does not allow whitespace
between double quotes and commas. Implementers should be aware that some applications
may be more lenient and allow whitespace outside the double quotes.

## Other field separators
This document defines a comma as a field separator but implementers should be aware
that some applications may use different values, especially with non-English languages.
Those are outside the scope of this document and implementers should consult other
efforts such as {{CSVW}}.

## Escaping double quotes
This document prescribes that a double quote appearing inside a field
must be escaped by preceding it with another double quote. Implementers should
be aware that some applications may choose to use a different escaping mechanism.

## BOM header
Applications that create text files with unicode character encoding might write
a BOM (byte order mark) header in order to support multiple unicode encodings
(like UTF-16 and UTF-32). Some applications might be able to read and properly
interpret such a header, others could break. Implementors should review
section 6 of {{?RFC3629}} and section 23.8 of {{UNICODE}}.

## Bidirectional text
While most of the world's written languages are displayed left-to-right,
many languages such as ones based on Hebrew or Arabic scripts are displayed
primarily right-to-left. Implementers should consult the "bidirectional display" part in section 5 of {{?RFC6365}} for further guidance.

One example of how bidirectional text can be handled in CSV files can be found in section 6.5.1 of {{CSVW}}.

## Comments
Some implementations may use the hash sign ("#") to mark lines that are meant to
be commented lines. Such lines may contain any character until
terminated by a line break (CR, LF or CRLF) and might appear in any line of the file
(before or after the header). Comments should not be confused with a subsequent line
of a multi-line field. If a first field of a record starts with a hash, it should be surrounded
with double quotes to avoid being mistaken for a comment as per {{desc}}.

Example of a CSV file containing comments:

    #commentCRLF<br/>
    aaa,bbb,cccCRLF<br/>
    #comment 2CRLF<br/>
    "aaa","this is CRLF<br/>
    # not a comment","ccc"CRLF<br/>
    "#aaa",bbb,cccCRLF

## IANA Considerations
As per {{!RFC6838}}, IANA is directed to update
the MIME type registration for "text/csv" with the content in {{registration}} and
add a reference to this document within the registration.

The update to the media type registration is copied from the current
one which consists of the original registration from {{!RFC4180}} as
updated by {{!RFC7111}} and updated based on this document.

# Update to MIME Type Registration of text/csv {#registration}

Type name:  text

Subtype name:  csv

Required parameters:  none

Optional parameters:  charset

> The "charset" parameter specifies the charset employed by the
> CSV content.  In accordance with RFC 6657 [RFC6657], the
> charset parameter SHOULD be used, and if it is not present,
> UTF-8 SHOULD be assumed as the default (this implies that US-
> ASCII CSV will work, even when not specifying the "charset"
> parameter).  Any charset defined by IANA for the "text" tree
> may be used in conjunction with the "charset" parameter.

> The "header" parameter defined in {{!RFC4180}} is deprecated
> and SHOULD NOT be used.

Encoding considerations:

> CSV files and CSV MIME entities can consist of binary data
> as per section 4.8 of {{!RFC6838}}. Although section 4.1.1. of {{!RFC2046}} defines
> CRLF to denote line breaks, implementers MAY also recognize a single CR or LF
> as a line break (similar to section 3.1.1.3 of {{!RFC7231}}).
> However, some implementations may use other values.

Security considerations:

> Text/csv consists of nothing but passive text data that should
> not pose any direct risks.  However, it is possible that
> malicious data may be included in order to exploit buffer
> overruns or other bugs in the program processing the text/csv
> data.

> Implementers and users should also be aware that some software
> applications may interpret certain characters in the beginning of CSV
> fields as referring to code or formulas, thus resulting in malicious
> code execution. This is known as "CSV injection" and users consuming
> CSV files should filter out such characters.

> The text/csv format provides no confidentiality or integrity
> protection, so if such protections are needed they must be
> supplied externally.

> The fact that software implementing fragment identifiers for
> CSV and software not implementing them differs in behavior, and
> the fact that different software may show documents or
> fragments to users in different ways, can lead to
> misunderstandings on the part of users.  Such misunderstandings
> might be exploited in a way similar to spoofing or phishing.

> Implementers and users of fragment identifiers for CSV text
> should also be aware of the security considerations in RFC 3986
> {{?RFC3986}} and RFC 3987 {{?RFC3987}}.

Interoperability considerations:

> Due to lack of a single specification, there are considerable differences among
> implementations. Implementers should "be conservative in what you
> do, be liberal in what you accept from others" ({{!RFC0793}})
> when processing CSV files. An attempt at a common definition can
> be found in section 2 of (to be replaced with the RFC number).

> There are numerous differences between different CSV implementations, many of which
> are addressed in section 4 of (to be replaced with the RFC number of this document).

Published specification:

> While numerous private specifications exist
> for various programs and systems, there is no single "master"
> specification for this format.  An attempt at a common definition
> can be found in Section 2 of (to be replaced with the RFC number).

Applications that use this media type:

> Spreadsheet programs and various data conversion utilities.

Fragment identifier considerations:

> Fragment identification for text/csv is supported by using fragment identifiers as specified
> by {{!RFC7111}}.

Additional information:

   Magic number(s):  none

   File extension(s):  CSV

   Macintosh file type code(s):  TEXT

Person & email address to contact for further information:

> Yakov Shafranovich (ietf@shaftek.org) and Erik Wilde (dret@berkeley.edu)

Intended usage:  COMMON

Restrictions on usage:  none

Author:

> Yakov Shafranovich (ietf@shaftek.org) and Erik Wilde (dret@berkeley.edu)

Change controller:  IESG

# Security Considerations

All security considerations discussed in {{registration}} still apply.

# Acknowledgments

In addition to everyone thanked previously in {{!RFC4180}}, the author would like to thank
acknowledge the contributions of the following people to this document:
Alperen Belgic, Abed BenBrahim, Damon Koach, Barry Leiba,
Oliver Siegmar, Marco Diniz Sousa and Greg Skinner.

A special thank you to L.T.S.

--- back

# Major changes since {{!RFC4180}}
- Added a section clarifying motivation for this document and standards status
- Changing default encoding to UTF-8 and adding Unicode to the ABNF grammar
- Allowing CR, LF and CRLF for line breaks
- Allowing binary content including HTAB in CSV files
- Mandating a line break at the end of the last line in the file
- Making records and headers optional, thus allowing for an empty file
- Adding a section on common implementation concerns
- Removed "header" parameter for the MIME type since it is not used

# Changes since the -00 draft
- Added CSV injection to security considerations (#30)
- Added a reference to RFC 7111 (#27)

# Changes since the -01 draft
- No changes yet, refreshed to keep draft alive

# Changes since the -02 draft
- Refreshed to keep draft alive
- Contact information and GitHub link changes
- Minor updates on language
- Added a section on bidi handling

# Changes since the -03 draft
- Moved comments to the common practices section and removed from the ABNF grammar (#32)
- Added more clarifications to the format section
- Made ABNF grammar match the document
- Added a note about text content to the format section

# Changes since the -04 draft
- No changes yet, refreshed to keep draft alive

# Changes since the -05 draft
- TBD

# Note to Readers

> **Note to the RFC Editor:** Please remove this section prior
> to publication.

Development of this draft takes place on Github at: https://github.com/yakovsh/rfc4180-bis

Comments can also be sent to the ART mailing list at: https://www.ietf.org/mailman/listinfo/art

Full list of changes can be viewed via the IETF document tracker:
https://tools.ietf.org/html/draft-shafranovich-rfc4180-bis
