---
layout: page
title: "R: Importing data"
date: "Last updated: 2025-02-04"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
---

When working on projects in R, you will likely need to draw data from a
variety of online sources. Each source that you use has their own
conventions of storing data, so you have to be able to import multiple
kinds of data files into R.

# Common data files

## CSV

CSV files are a convenient and simple way of storing data. CSV stands
for “comma-separated values”: the raw data is text file where each line
of text is a row of data, and values within a row are separated by
commas. In most cases your computer will automatically open CSV files in
Excel, where they are displayed as a table. CSV files are the most
common and also one of the easiest to import to R.

## Other delimited text files

If data is stored as text, the value separator (also known as delimiter)
does not always a comma. Other common delimiters are semicolons (;) and
tabs (whitespace). The most common file extensions for these files are
.csv (for semicolon-delimited files), .tsv or .txt. These files often
cannot be displayed as a table by simply opening the file, but you can
observe their structure by opening them as text files.

## Other data formats

Sometimes data is provided in a way that uses the specific data formats
of various statistical softwares. The most common formats are Excel
files (.xlsx or .xls extensions), SPSS files (.sav extension), and Stata
files (.dta extension). You can import these files and work with them in
R even if you don’t have e.g. SPSS or Stata installed.

# Importing data files

## Using the *Import dataset* button

At the beginning, you can let R do most of the work of importing data
instead of figuring out the right functions and arguments yourself. Of
course, if you are more comfortable with R, you will find it much more
convenient to write your own importing code, as it is very
straightforward to do so.

Before importing data to R, you need to download it to your computer. In
most cases you will find clear instructions on how to do so on the
website of the data source. Files will most likely be saved to your
Downloads folder. In order to import it to R, you should first move it
to your project folder, or use the full file path when specifying the
file name.

In order to import files to R, open your project in RStudio. Find *File*
-\> *Import dataset* in the top left of RStudio, and choose the correct
file format.

If you downloaded a CSV file, select the option “from text (readr)”.
`readr` is a tidyverse library, so it is preferred over `base` import
functions. In the data import pop-up window click “Browse” and find the
data that you would like to import. RStudio will try to automatically
detect the format of your data: the result of that is shown in the Data
preview window. If something looks wrong, try changing some of the
settings below the data preview until the preview looks correct.
Additionally, you should change the name of the data to something short
and sensible, as you will refer to that name quite frequently in your
code. Once all the settings are ready, you can copy the contents of the
“Code preview” window into your script, and use it to import your data.
As long as you start your script by importing `tidyverse`, you don’t
need to copy `library(readr)`, as `readr` is a part of `tidyverse`.

Importing an Excel file is almost exactly the same, except you you
should click File -\> Import Dataset -\> From Excel. Every following
step is the same as with importing CSV files, except that you do need to
load the `readxl` package separately, as it is not a part of
`tidyverse`.

The result of copying the code should be similar to the following:

``` r
library(tidyverse)
library(readxl)

data1 <- read_csv("filename.csv")
data2 <- read_excel("filename.xlsx")
```

## Own import code

Later on you might find it more convenient to write your own importing
code. In that case, you should be familiar with the most common
packages, functions, and function arguments for importing data. You
should also make sure to remember to assign each data file to an object
so that they are stored in the RStudio environment.

CSV and other delimited text files can be imported using functions in
the `tidyverse` package. CSV files can be easily imported using the
`read_csv()` function; most of the time the only argument you need is
the file path as a string. Other delimited files can be imported using
the `read_delim()` function: there you need to specify both the file
path to the data file and the delimiter as strings. The most common
delimiters are semicolons (`delim = ";"`) and tabs (`delim = "\t"`). If
your data has some special features, you might need to specify some
additional arguments, such as `col_names = FALSE` if your data does not
have column names, or `skip` if your data starts with non-data rows. You
can find more information about these additional arguments in the
help-files of `read_csv()` and `read_delim()`.

You can import Excel files using the `read_excel()` function from the
`readxl` package. The function arguments are very similar to those of
`read_csv()`: often it is enough to only specify the file path as a
string, otherwise you can make use of the additional arguments. If your
spreadsheet has multiple sheets, you also need to specify which sheet to
import using he `sheet` argument.

If your data is a .sav or .dta file (SPSS or Stata data), you need the
`haven` package to import data files. The functions you would need are
`read_sav()` and `read_dta()`, and the main argument is still the file
path as a string. Again, you can rely on the help-files of these
functions for further explanation.

As long as you work in a project, you only need to specify relative file
paths. If your data files are saved to your main project folder, then
the relative file path is simply the file name (including the file
extension, e.g. “data.csv”). If your data is in a subfolder within your
project folder, then your relative file path must also include
references to the subfolder(s), separated by slashes (/), e.g. as
“data/data.csv”.
