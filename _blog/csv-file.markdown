---
title: What is a CSV file?
date: 2021-01-20 13:09:00 +01:00
image: "/uploads/HeyCSV-CSV-File-#2.png"
---

CSV stands for "**c**omma **s**eparated **v**alues" and just means that a simple text file contains a list of structured data. This structure is achieved by separating data entities with a ",". That's where the name is from.

A simple example would be:
`name,email
Steve Apple,steve@apple.com
Mike Bryan,mikebryan@foo.net`

One separated data entity vertically is also called a column as it represents a column of a database table for instance.

Sometimes CSV is also called "character separated value" file as the "," can be replaced by any other character, typically semicolon ";" or Tabulator key (↹) are used besides comma.

When the content within a column can contain a "," as content itself, then it can make sense to use a semicolon ";" as separator to avoid having a broken data structure.

Alternatively a CSV file can use a certain quote character to enclose content of a column. Typically this is a " quotation mark character. For example:

`product,description 
Apple,"the round fruit of a tree of the rose family, which typically has thin green or red skin and crisp flesh." 
Banana,"A banana is an elongated, edible fruit – botanically a berry – produced by several kinds of large herbaceous flowering plants in the genus Musa"`

Without quotation mark a system would not be able to see the descriptions as one text, but split at the commas "," and see another column.

Alternatively with semicolon as separator and without quotation marks. Then it is also clear what column content belongs together:

`product,description 
Apple;the round fruit of a tree of the rose family, which typically has thin green or red skin and crisp flesh. 
Banana;A banana is an elongated, edible fruit – botanically a berry – produced by several kinds of large herbaceous flowering plants in the genus Musa`

You see it is quite important to watch out for the right configuration setting of a .csv file before exporting or importing from a system.

Since a .csv file doesnt come with a configuration setting attached, it can only be derived by looking at the content when there is no additional information from the origin system.

# What is a CSV file used for?

CSV is mainly used for simple flat hierarchy data like addresses, product or order data to be exported and imported from one to another system. But it can also get quite complex with millions of rows and dozens of columns.

For even more complex data structures and nested data formats like JSON or XML data are typically used.

# How do I open a .csv file?

As a .csv file is a simple text file, the easiest way is to open it with any text editor of your choice (right click on the file and then "open with"). However, for more complex data than the simple examples above it is hard to visually grasp the structure of the data.

Therefore it is recommended to open a .csv file with a spreadsheet tool like Microsoft Excel, Libre Office or Google Sheets.

*![csv-file-example-sheets.png](/uploads/csv-file-example-sheets.png)
.csv file opened in Google Sheets*

As you can see this is a lot more structured than the raw format in the examples above or when you open it in a text editor.

# How can I edit a .csv file?

A spreadsheet tool is also the best way to edit a .csv file as you can apply operations per row or column or in any way you are used to when dealing with spreadsheet tables.

You have to watch out, however, that you keep an eye on the format configuration when you want to save/export the .csv again from the spreadsheet tool. A spreadsheet tool might automatically save a .csv file with semicolon ";" separator while you want to save it with comma "," as separator.