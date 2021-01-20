---
title: What is a CSV file?
date: 2021-01-19 17:09:00 +01:00
---

CSV stands for "**c**omma **s**eparated **v**alues" and just means that a simple text file contains a list structured data. This structure is achieved by separating data entities with a ",". That's where the name is from. 

A simple example would be:
`name,email 
Steve Apple,steve@apple.com 
Mike Bryan,mikebryan@foo.net`

One separation is also called a column as it represents a column of a database table for instance.

Sometimes CSV is also called "character separated value" file as the "," can be replaced by any other character, typically semicolon ";" or Tabulator key (↹) are used besides comma. 

When the content within a column can contain a "," as content itself, then it can make sense to use a semicolon ";" as separator to avoid having a broken data structure.

Alternatively a CSV file can use a certain quote character to enclose content of a column. Typically this is a " quotation mark character. For example:

`product,description 
Apple,"the round fruit of a tree of the rose family, which typically has thin green or red skin and crisp flesh." 
Banana,"A banana is an elongated, edible fruit – botanically a berry – produced by several kinds of large herbaceous flowering plants in the genus Musa"`

Without quotation mark a system would not be able to see the descriptions as one text, but split at the commas "," and see another column. 

Alternatively with semicolon as separator and without quotation marks:

`product,description 
Apple;the round fruit of a tree of the rose family, which typically has thin green or red skin and crisp flesh. 
Banana;A banana is an elongated, edible fruit – botanically a berry – produced by several kinds of large herbaceous flowering plants in the genus Musa`

You see it is quite important 

# What is a CSV file used for?

CSV is mainly used for simple flat hierarchy data like addresses, product or order data to be exported and imported from one to another system. But it can also get quite complex with millions of rows and dozens of columns

While JSON or XML data files can represent a more complex structure of data, 


# How do I open a .csv file?

# How can I edit a .csv file?