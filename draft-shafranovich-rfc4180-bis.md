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
  CREATIVYST:
    title: 'HOW-TO: The Comma Separated Value (CSV) File Format'
    author:
        name: J. Repici
        org: Creativyst, Inc.
    date: 2010
    target: http://www.creativyst.com/Doc/Articles/CSV/CSV01.htm
  EDOCEO:
    title: Comma Separated Values (CSV) Standard File Format
    author:
        org: Edoceo, Inc.
    date: 2020
    target: https://edoceo.com/dev/csv-file-format
  ART:
    title: The Art of Unix Programming, Chapter 5
    author:
        name: E. Raymond
    date: September 2003
    target: http://www.catb.org/~esr/writings/taoup/html/ch05s02.html

--- abstract
This RFC documents the format used for Comma-Separated Values (CSV)
files and registers the associated MIME type "text/csv".

--- middle

# Introduction
The comma separated values format (CSV) has been used for exchanging
and converting data between various spreadsheet programs for quite
some time.  Surprisingly, while this format is very common, it has
never been formally documented.  Additionally, while the IANA MIME
registration tree includes a registration for
"text/tab-separated-values" type, no MIME types have ever been
registered with IANA for CSV.  At the same time, various programs and
operating systems have begun to use different MIME types for this
format.  This RFC documents the format of comma separated values
(CSV) files and formally registers the "text/csv" MIME type for CSV
in accordance with {{!RFC2048}}.

# Definition of the CSV Format {#format}
While there are various specifications and implementations for the
CSV format (for ex. {{CREATIVYST}}, {{EDOCEO}} and {{ART}})), there is no formal
specification in existence, which allows for a wide variety of
interpretations of CSV files.  This section documents the format that
seems to be followed by most implementations:

1. Each record is located on a separate line, delimited by a line break (CRLF). For example:

   aaa,bbb,ccc CRLF<br/>
   zzz,yyy,xxx CRLF

2. The last record in the file may or may not have an ending line break. For example:

   aaa,bbb,ccc CRLF<br/>
   zzz,yyy,xxx

3. There maybe an optional header line appearing as the first line
of the file with the same format as normal record lines. This
header will contain names corresponding to the fields in the file
and should contain the same number of fields as the records in
the rest of the file (the presence or absence of the header line
should be indicated via the optional "header" parameter of this
MIME type). For example:

   field_name,field_name,field_name CRLF<br/>   
   aaa,bbb,ccc CRLF<br/>
   zzz,yyy,xxx CRLF

4. Within the header and each record, there may be one or more
fields, separated by commas. Each line should contain the same
number of fields throughout the file. Spaces are considered part
of a field and should not be ignored. The last field in the
record must not be followed by a comma. For example:

   aaa,bbb,ccc

5. Each field may or may not be enclosed in double quotes (however
some programs, such as Microsoft Excel, do not use double quotes
at all). If fields are not enclosed with double quotes, then
double quotes may not appear inside the fields. For example:

   "aaa","bbb","ccc"CRLF<br/>
   zzz,yyy,xxx

6. Fields containing line breaks (CRLF), double quotes, and commas
should be enclosed in double-quotes. For example:

   "aaa","b CRLF<br/>
   bb","ccc"CRLF<br/>
   zzz,yyy,xxx

7. If double-quotes are used to enclose fields, then a double-quote
appearing inside a field must be escaped by preceding it with
another double quote. For example:

   "aaa","b""bb","ccc"

The ABNF grammar (as per {{!RFC5234}}) appears as follows:

~~~~~~~~~~
file = [header CRLF] record *(CRLF record) [CRLF]

header = name *(COMMA name)

record = field *(COMMA field)

name = field

field = (escaped / non-escaped)

escaped = DQUOTE *(TEXTDATA / COMMA / CR / LF / 2DQUOTE) DQUOTE

non-escaped = *TEXTDATA

COMMA = %x2C

CR = %x0D ;as per section B.1 of [RFC5234]

DQUOTE =  %x22 ;as per section B.1 of [RFC5234]

LF = %x0A ;as per section B.1 of [RFC5234]

CRLF = CR LF ;as per section B.1 of [RFC5234]

TEXTDATA =  %x20-21 / %x23-2B / %x2D-7E
~~~~~~~~~~

# MIME Type Registration of text/csv {#registration}

This section provides the media-type registration application (as per
{{!RFC2048}}.

To: ietf-types@iana.org

Subject: Registration of MIME media type text/csv

MIME media type name: text

MIME subtype name: csv

Required parameters: none

Optional parameters: charset, header

> Common usage of CSV is US-ASCII, but other character sets defined
> by IANA for the "text" tree may be used in conjunction with the
> "charset" parameter.

> The "header" parameter indicates the presence or absence of the
> header line. Valid values are "present" or "absent".
> Implementors choosing not to use this parameter must make their
> own decisions as to whether the header line is present or absent.

Encoding considerations:

> As per section 4.1.1. of {{!RFC2046}}, this media type uses CRLF
> to denote line breaks. However, implementors should be aware that
> some implementations may use other values.

Security considerations:

> CSV files contain passive text data that should not pose any
> risks. However, it is possible in theory that malicious binary
> data may be included in order to exploit potential buffer overruns
> in the program processing CSV data. Additionally, private data
> may be shared via this format (which of course applies to any text
> data).

Interoperability considerations:

> Due to lack of a single specification, there are considerable
> differences among implementations. Implementors should "be
> conservative in what you do, be liberal in what you accept from
> others" ({{?RFC0793}}) when processing CSV files. An attempt at a
> common definition can be found in {{format}}. 

> Implementations deciding not to use the optional "header"
> parameter must make their own decision as to whether the header is
> absent or present.

Published specification:

> While numerous private specifications exist for various programs
> and systems, there is no single "master" specification for this
> format. An attempt at a common definition can be found in {{format}}.
  
Applications that use this media type:

> Spreadsheet programs and various data conversion utilities

Additional information:

> Magic number(s): none

> File extension(s): CSV

>> Macintosh File Type Code(s): TEXT

Person & email address to contact for further information:

> Yakov Shafranovich &lt;ietf@shaftek.org&gt;

Intended usage: COMMON

Author/Change controller: IESG

# IANA Considerations

The IANA has registered the MIME type "text/csv" using the
application provided in {{registration}} of this document.

# Security Considerations

See discussion above in {{registration}}.

# Acknowledgments

The author would like to thank Dave Crocker, Martin Duerst, Joel M.
Halpern, Clyde Ingram, Graham Klyne, Bruce Lilly, Chris Lilley, and
members of the IESG for their helpful suggestions. A special word of
thanks goes to Dave for helping with the ABNF grammar.

The author would also like to thank Henrik Lefkowetz, Marshall Rose,
and the folks at xml.resource.org for providing many of the tools
used for preparing RFCs and Internet drafts.

A special thank you goes to L.T.S.

--- back
# Changes since RFC 4180
- Updating RFC references to newever versions
- Removing dead references
- Incorporating existing errata
- TBD

# Note to Readers

> **Note to the RFC Editor:** Please remove this section prior
> to publication.

Development of this draft takes place on Github at: https://github.com/nightwatchcyber/rfc4180-bis

Comments can also be sent to the ART mailing list at: https://www.ietf.org/mailman/listinfo/art

Full list of changes can be viewed via the IETF document tracker:
https://tools.ietf.org/html/draft-shafranovich-rfc4180-bis