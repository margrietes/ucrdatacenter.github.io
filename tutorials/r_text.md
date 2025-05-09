---
layout: page
title: "Working with text"
date: "Last updated: 2025-03-30"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
---

# Introduction

This tutorial introduces how to treat text as data in R using the
`tidytext` package. It introduces methods of importing text, tokenizing
text, looking for (partial) matches with simple regular expressions, and
analyzing word frequencies.

We start by installing and loading the `tidytext` package, and loading
the `tidyverse` package.

``` r
# install.packages("tidytext")
library(tidytext)
library(tidyverse)
```

# Tidy text format

Normally, we think of data as being a table of values, mostly numbers or
categories with a limited number of levels. In tidy data, each row
corresponds to an observation (e.g. a person completing a survey, or a
country about which we know particular characteristics). However, text
can also be a kind of data that we can work with in a systematic manner.

When thinking of the “tidy text format”, we need to think of each row as
a unit of speech, and each column as a variable that describes the
properties of that particular unit of speech. For example a unit of
speech can be a word, and a variable could be the word itself, its stem,
an indicator of what part of speech the word is (noun, verb, etc.), and
so on. For basic text analysis, all we need is the actual text, and
therefore we can convert any existing text to a tidy text format in R
without needing additional information.

For example, we can start by defining our text as a single object called
`raw_text` that contains the full text we want to analyze (notice the
quotation marks around the text to show that it is a character vector).

``` r
raw_text <- "Text analysis in R provides valuable insights by uncovering patterns, trends, and relationships within textual data. It can reveal sentiment, topic distributions, keyword frequencies, and even hidden structures using techniques like natural language processing (NLP) and machine learning. Users can analyze customer reviews, social media posts, or academic papers to identify recurring themes, sentiment shifts, and linguistic trends, ultimately aiding decision-making, market research, and content optimization."
```

If we want to convert this object to tidy text, we first need to choose
what unit we want to work with in our analysis. If our goal is to see
which words appear most frequently in the text, we should conduct on our
analysis on the word level. In that case, each word in the text is
considered as one token, and the text is made up of many tokens arranged
in a meaningful order.

In order to convert a vector like `raw_text` to a tidy format, we first
need to convert the vector to a tibble with the `as_tibble()` function.
Then we can use the `unnest_tokens()` function from `tidytext` to split
the text into tokens so that each token represents one row in our tidy
data. When using `unnest_tokens()`, we need to specify the variable name
of our original text (in this case called `value`), the name of the new
token-level variable we want to create (let’s call it `word`), and the
token type we want to use (in this case “words” but could be
e.g. “sentences”, “lines”, “ngrams” (combinations of n words next to
each other), and so on). You can look at all the token types in the help
file of `unnest_tokens()` by typing `?unnest_tokens()`.

Notice that the code chunk below uses the pipe operator. You can read
more about how the pipe is used [here](../r_intro_pipe).

``` r
clean_text <- raw_text |> 
  as_tibble() |> 
  unnest_tokens(output = word, input = value, token = "words")
```

In the `clean_text` tibble we now have one variable called `word`, which
in each row contains one word from the original text, in the order they
initially appeared. By default, `unnest_tokens()` also cleans the text
by converting all letters to lowercase and removing punctuation.

## Counting word frequencies

The simplest method of getting a quick overview of a long text is to
count the number of times each word appears in the text, and looking at
what the most frequent words are. We can get these word frequencies
using the `count()` function, specifying which variable we want to
count.

``` r
clean_text |> 
  count(word)
```

    ## # A tibble: 60 × 2
    ##    word         n
    ##    <chr>    <int>
    ##  1 academic     1
    ##  2 aiding       1
    ##  3 analysis     1
    ##  4 analyze      1
    ##  5 and          5
    ##  6 by           1
    ##  7 can          2
    ##  8 content      1
    ##  9 customer     1
    ## 10 data         1
    ## # ℹ 50 more rows

The `count()` function has an argument `sort`, which allows us to sort
the output from most frequent to least frequent words.

``` r
clean_text |> 
  count(word, sort = TRUE)
```

    ## # A tibble: 60 × 2
    ##    word          n
    ##    <chr>     <int>
    ##  1 and           5
    ##  2 can           2
    ##  3 sentiment     2
    ##  4 trends        2
    ##  5 academic      1
    ##  6 aiding        1
    ##  7 analysis      1
    ##  8 analyze       1
    ##  9 by            1
    ## 10 content       1
    ## # ℹ 50 more rows

## Looking for exact and partial word matches

In many cases we are interested only in analyzing parts of a text that
contain our topic of interest. For example, we may want to find which
parts of a text talk about “data” and in what context. In that case, we
can use the `filter()` function to keep only observations that meet a
particular criteria. You can see some more explanation and general
examples of the `filter()` function in [this
tutorial](../r_basics_subset).

When we are working with words, exact matches are often enough for our
purposes. For example, we can look at how many rows in our `clean_text`
tibble have “data” as the value of the `word` variable.

``` r
clean_text |> 
  filter(word == "data")
```

    ## # A tibble: 1 × 1
    ##   word 
    ##   <chr>
    ## 1 data

However, this filter tells us nothing about the context in which “data”
appears. For that, it would be better to split the text into sentences,
and find which sentence contains the word “data”. But if we tokenize the
text into sentences, an exact match won’t find the sentence we’re
looking for.

``` r
# split the text into sentences
sentences <- raw_text |> 
  as_tibble() |> 
  unnest_tokens(output = sentence, input = value, token = "sentences")

# keep only rows with an exact match to "data" (no such rows)
sentences |> 
  filter(sentence == "data")
```

    ## # A tibble: 0 × 1
    ## # ℹ 1 variable: sentence <chr>

If we want to find a partial string match (i.e. a sentence that among
other content contains the word “data”), we need to use a special
function to detect partial matches. This function is called
`str_detect()` and takes the arguments of the variable that contains the
elements you want to evaluate and the pattern you’re looking for. In our
case, this variable is `sentence` and the pattern is `data`.
`str_detect()` returns a logical vector, i.e. for each element of your
variable it tells you whether it matches the pattern (`TRUE`) or not
(`FALSE`).

``` r
# example of str_detect()
str_detect(string = c("A", "AB", "BB"), pattern = "A")
```

    ## [1]  TRUE  TRUE FALSE

``` r
# look for partial match to "data" (one sentence)
sentences |> 
  filter(str_detect(string = sentence, pattern = "data"))
```

    ## # A tibble: 1 × 1
    ##   sentence                                                                      
    ##   <chr>                                                                         
    ## 1 text analysis in r provides valuable insights by uncovering patterns, trends,…
