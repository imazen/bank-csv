---
###
# Internet-Draft Markdown Template
#
# Rename this file from draft-todo-yourname-protocol.md to get started.
# Draft name format is "draft-<yourname>-<workgroup>-<name>.md".
#
# For initial setup, you only need to edit the first block of fields.
# Only "title" needs to be changed; delete "abbrev" if your title is short.
# Any other content can be edited, but be careful not to introduce errors.
# Some fields will be set automatically during setup if they are unchanged.
#
# Don't include "-00" or "-latest" in the filename.
# Labels in the form draft-<yourname>-<workgroup>-<name>-latest are used by
# the tools to refer to the current version; see "docname" for example.
#
# This template uses kramdown-rfc: https://github.com/cabo/kramdown-rfc
# You can replace the entire file if you prefer a different format.
# Change the file extension to match the format (.xml for XML, etc...)
#
###
title: "Common Format for Bank Transaction CSV Files"
abbrev: "bankcsv"
category: info

docname: draft-river-bankcsv-latest
submissiontype: independent
number:
date:
consensus: false
v: 3
area: AREA
workgroup: WG Working Group
keyword:
 - CSV
 - Bank Transactions
 - Accounting and reconciliation
venue:
  group: WG
  type: Working Group
  mail: WG@example.com
  arch: https://example.com/WG
  github: imazen/bank-csv
  latest: https://example.com/LATEST

author:
 -
    fullname: Lilith River
    organization: Imazen
    email: lilith@imazen.io

normative:


informative:


--- abstract

TODO Abstract


--- middle

# Introduction

This Bank Standard CSV specification describes a format based on RFC 4180 (Comma Separated Value) for bank transaction and statement data in order to improve interoperability and accuracy of data transfers between applications and services. Design priorities include human-readability, machine-readability, compatibility with existing software, ease of implementation, and utility for spreadsheet, bookkeeping, budgeting, and tax purposes.

While the vast majority of financial institions permit data to be exported to CSV, no standard format exists to allow parsers to be reused for files from different institutions. In addition, some instutions regularly change the structure of their CSV files in incompatible ways or fail to provide essential information, which can cause significant corruption of data and financial harm. By providing a common format and example validators and parsers/exporters, code reuse will become much easier both for data consumers and data exporters.

The specification also aims to improve quality-of-life for those simply trying to get data into their spreadsheet of choice.

This specification also describes an OPTIONAL API to permit efficient data synchronization using an API key tied to one or more accounts.

Bank CSV files must also strictly comply with RFC 4180 regarding the CSV format.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

## Definitions

1. "Customer" shall mean the account-holder, whether an individual or organization. A Customer may have multiple accounts at an Institution.
2. "Account" shall mean any account or sub-account whose balance which is affected by transactions. For example, some banks may have lines of credit or payment plans that have balances separate from those of the parent account. Some accounts may also include multiple currencies with unrelated balances; these would be considered separate accounts.
3. "Institution" shall mean the organization managing the accounts for the Customer and providing the exported data. This can include (but is not limited to) financial institutions, banks, credit card issuers, payment processors, payment services, retailers providing financing or credit cards, financial exchanges, and investment services.
4. "Custom Field" shall mean any column name not reserved by this specification. 
5. "Currency Code" shall mean a valid ISO 4217 currency code, composed of 3 uppercase letterss (Ex. "USD") 

## Conventions

1. Files MUST always begin with a header row. 
The last column of the header row shall be a quoted string of key-value pairs used by parsers for modular versioning. Ex "type=bankcsv;bankcsv=v1.1;include=categories,_chase*;_chase=v2".
No key or value may contain quotes, commas, equal signs, or semicolons. The "type" and "v" values are required.
"v" and other version fields shall be a semver 2.0 compliant value corresponding with the version of the file format. 
For transaction data, "type" shall be "bankcsv". Additional custom fields may be specified and versioned here. All custom fields must be prefixed with _
All values for this column shall be 
empty. TODO: Test if spaces are needed in the k/v header to prevent table/spreadsheet layout issues.
2. Files MUST be encoded in UTF-8. A UTF-8 Byte Order Mark SHOULD be used. Some spreadsheet applications do not detect UTF-8 reliably.  
3. Files MUST strictly conform to RFC 4180, including regarding use of commas, newlines, quoted values, and quote escaping.
4. Financial institutions MUST select a permanent identifier (hereafter "bankid") representing the company or parent organization. This identifier MUST be alphanumeric and lowercase and cannot begin with a number ([a-z][a-z0-9]+). The identifier SHOULD remain consistent over time. This identifier SHOULD as short as possible. The identifier SHOULD NOT conflict with identifiers already in use by unaffiliated organizations. This identifier is important for allowing parsers to distinguish between custom fields and applications to handle banks as distinct entities.
5. Custom Fields MUST be prefixed with an underscore, "bankid", and another underscore. For example "_greatbank_Teleport Id"
6. Each account MUST be provided a lowercase alphanumeric identifier ("Account ID"). The Account ID SHOULD NOT change for the life of the account, to prevent data duplication and corruption. This SHOULD be a full or shortened account number but MAY be any lowercase alphanumeric identifier that is unique among the accounts held by the Customer at the Institution.
7. All financial values (Ex. "3235.20") MUST be composed exclusively of numbers (0-9) with zero or one period ('.') used as a decimal separator. Financial values MUST NOT include thousands separators or commas, and MUST use '.' as the decimal separator regardless of currency or locale. Currency symbols MUST NOT be included in the field, but in a separate column. Sufficient precision MUST be provided in this field to accurately reconstruct a balance. In regular expression form, values should match ([0-9]+ OR [0-9]+\.[0-9]+).
8. All date values ("complaint datetime strings") MUST be compliant with RFC 3339 and ISO 8601 and MUST either be in UTC time (with the 'Z' suffix) (Ex. "1985-04-12T23:20:50Z") or specify a time zone offset relative to UTC (Ex. "1996-12-19T16:39:57-08:00"). If a time zone offset is used, it SHOULD be used consistently across the account. Fractional sections MAY be used but are not reccomended.
9. No leading or trailing whitespace is permitted in any field
10. File names and extensions SHOULD be lowercase.
11. Transaction file names SHOULD be in the form "\[bankid]_\[AccountID1,AccountID2,..]-from-\[YYYY-MM-DD]-to-\[YYYY-MM-DD].bank.csv" 
12. Account file names SHOULD be in the form "\[bankid]_\[AccountID1,AccountID2,..].accounts.csv"

## Columns in Transaction Files (.bank.csv)

1. "Posted" MUST contain compliant datetime values if the transaction has posted to the account and has affected the balance.
2. "Amount" must include a financial value if the transaction affects the balance
3. 
2. Balance Change?

4. TPPP (Third Party Payment Processor)
5. Merchant Name
6. Description
7. Created
8. Transaction Type
9. Merchant Category Code
10. Currency Code

## Columns in Account Files (.accounts.csv)

The last cell in the header column shall include "type=statements;statements=v1.0"

1. Account ID
2. Currency Code
3. Opening Balance
4. Closing Balance
5. Opening Datetime
6. Closing Datetime
7. Statement Type (Year, Quarter, MonthlyStatement, YTD, QTD, SinceLastStatement)
8. Account Type
9. Liability Account
10. SHA256 of file contents
12. Transaction File URL
13. Authoritative (true/false)

The QTD/YTD/MTD does not require a hash but must at minimum include a last updated date (which can be the date of the most recent transaction).
On the day that an account statement is generated (or ____ if not scheduled), a bankcsv statement shall also be created and stored. In addition, the most recent year, quarter shall be generated if they do not already exist. 
The YTD and QTD shall also be updated on that day.

The statement list must indicate which statements are authoritative. All posted transactions in every listed account must be found within the authoritative set. 
Duplicate transactions within the authoritative set are not permitted; statements within the authoritative set for a given account may not overlap.

Normalcase - the least error-prone casing for a given language (some langs don't have lower?) 

Created Date
Posted Date
IncreaseToAssets (customer assets)
Amount
45

Statement generation 

Transfer IDs should match within a bank and preferably outside a bank.

Card charge, balance payment, balance transfer in/out, 

Require schema 


Csv Statements must be generated at least on pdf interval.

Transfers between accounts for the same customer must indicate they are transfers, the id of the paired account, and a reference number that will be present on both transacations

Swift/iban/ach 


Positive amounts should increase customer assets or reduce their liabilities.  Negative amounts should reduce customer assets or increase liabilities. 

Posted vs settled vs initiated dates

Third party payment processor information shall not be included in the merchant column, but rather in the TPPP? Column.

The merchant field shall not include 

MCC category code.

Provide example export code for most common bank programming languages, prob C#, Java, Javascript, Ruby, 


To reduce the difficulty and time burden on taxpayers,  accountants, preparers, and software developers all financial institutions shall be required to allow their customers access to their data in a standardized, human and machine-readable format.

Customers may select a shorter period for the record retention requirements herein if the financial institution chooses to make such opt-out available, but no benefits or services shall be made contingent upon the customer choosing not to have their transactions retained.

Financial institutions with more than 100M USD in annual revenue shall be required to meet these requirements within 6 months. Other institutions must implement within 18 months.


Financial institutions shall store yearly files for the prior 5 years, and quarterly files for the current year. A YTD export shall be provided on demand within 24 hours. Quarterly and annual exports shall be created, stored, and made accessible within 5 business days of all period-relevant transactions being settled.

Financial institutions MAY split exports into multiple files if more than 8,000 transactions are present in the time range.

Transactions shall be ordered by posted date, oldest to newest. Un-posted transactions shall be listed last with a status of 'pending' or 'authorized' but not 'posted' or 'settled'

The estimated cost of such storage is not expected to exceed five cents per year for the average customer, less if compression is used.

Files may provided in .zip or utilizing gzip compression if the client sends accept headers.

As the vast majority of financial institutions already provide online PDF statement generation and storage and some manner of export to CSV files, the requirements herin are not expected to burden financial institutions and will likely reduce operational costs over time.

Transactions shall not be included in an export if their effect on the balance occurs outside of the time window.


"ISO Bank Transaction Codes" https://www.bankconnect.dk/en/erp-supplier/iso%20bank%20transaction%20codes

















# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
