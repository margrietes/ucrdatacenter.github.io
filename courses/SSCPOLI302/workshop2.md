---
layout: page
title: "SSCPOLI302:<br> Analysis of a legislative dossier (draft)"
subtitle: "Fall 2024"
date: "Last updated: 2024-09-20"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
    toc: true
---

- [Dossier overview](#dossier-overview)
- [File of interest](#file-of-interest)
- [Preparation and download](#preparation-and-download)
- [Importing file back into R](#importing-file-back-into-r)
- [Main analysis](#main-analysis)

``` r
library(tidyverse)
library(tidytext)
library(writexl)
library(readxl)
library(rio)

Meetings_compl <- import("https://github.com/ucrdatacenter/projects/raw/main/SSCPOLI302/2024h2/Meetings_compl.xlsx", setclass = "tbl_df") |> 
  mutate(meeting_date = as.Date(meeting_date))
```

## Dossier overview

We will now proceed with the second part of the analysis by looking at
lobbying activity regarding specific legislative files. First we are
going to examine the size of the overall files to see which dossiers are
the most heavily referenced.

``` r
dossier <-
    Meetings_compl |>
    group_by(procedure_reference) |>
    summarize(total_references = n()) |>
    arrange(desc(total_references))
```

## File of interest

Based on the overview, we want to get a better look at our file of
interest. In this case, I have picked file code 2018/0902(NLE), which is
about the “Existence of a clear risk of a serious breach by Hungary of
the values on which the Union is founded” (Legislative Observatory,
2024).

``` r
#Filtering the file from the overall data
#At times, multiple attendees have been reported in the same row. In order to
#deal with most of these cases, we unnest the tokens for specified separators.
#This tells R to split these attendees into multiple rows.
Test_dossier <-
    Meetings_compl |>
    filter(procedure_reference == "2018/0902(NLE)") |>
    unnest_tokens(attendees, attendees, token = "regex", pattern = "\\||\\+", to_lower = FALSE) |>
    filter(attendees != "(at staff level)")
```

## Preparation and download

As we can see, the current database is still quite messy. A lot of MEPs
have not reported their attendees in one single format, hindering our
analysis. Therefore, we will need to manually fix these cases in Excel.

``` r
#Grouping the attendees by name and downloading the file
Attendees_test_dossier <-
    Test_dossier |>
    group_by(attendees) |>
    summarize()

    write_xlsx(Attendees_test_dossier, path = "Attendees_test_dossier.xlsx", col_names= TRUE, format_headers = TRUE)
```

## Importing file back into R

After we have created the new file table, we can import it back into R.
Make sure the file is in the correct folder. If all goes well, we can
then join it into the original data to create a new, improved dataset of
the legislative file.

``` r
#Importing Excel table
Import_test <-
    read_xlsx("Attendees_test_dossier_fixed.xlsx")

#Joining data for complete overview
Test_complete <-
    Test_dossier |>
    left_join(Import_test)
```

## Main analysis

The main analysis of the file will largely follow the same steps as the
ones for the complete EP dataset.

``` r
#Counting how many unique attendees were involved and how many times
#they were reported by grouping the attendees based on their fixed names.
Unique_attendee_count <-
    Test_complete |>
    group_by(fixed_names) |>
    summarize(count = n()) |>
    arrange(desc(count))

#Examining political group and role distribution
Group_roles_file <-
    Test_complete |>
    group_by(political_group, member_capacity) |>
    summarize(count = n())

#Examining nationalities and roles involved
Nationality_roles_file <-
    Test_complete |>
    group_by(country, member_capacity) |>
    summarize(count = n())

#Data should be supported by visualisations similar to those of the first lectures
```

However, with our improved dataset we can also do some additional
analyses by looking at the nature of the actors that were involved.

``` r
#Improving the overview of our attendee list by adding their class & structure
Classstruc_attendee_count <-
    Test_complete |>
    group_by(fixed_names, class, structure) |>
    summarize(count = n()) |>
    arrange(desc(count))

#Counting how many attendees of different classes & structures were involved
Classstruc_file <-
    Test_complete |>
    group_by(class, structure) |>
    summarize(count = n()) |>
    arrange(desc(count))

#Additionally, we can also apply these variables to our examination of 
#nationalities and political groups. This way we not only get a picture of how
#many times a group/nationality was lobbied, but also of the types of actors 
#that were involved.
Group_classstruc_file <-
    Test_complete |>
    group_by(political_group, class, structure) |>
    summarize(count = n())
    
Nationality_classstruc_file <-
    Test_complete |>
    group_by(country, class, structure) |>
    summarize(count = n())

#Data should be supported by visualisations similar to those of the first lectures


#How do these results relate to theories of interest representation and access
#to the European Parliament?
```

Lastly, we can look at the timeline of the lobbying activity to see if
there are patterns related to events during the file’s legislative
procedure.

``` r
#Plotting the meeting timeline
 ggplot(Test_dossier) +
  geom_histogram(aes(x = meeting_date), position = "dodge") +
    labs(title = "Reported meeting activity regarding legislative file 2018/0902(NLE)", 
       x = "Date", 
       y = "Frequency of meetings")
```

![](workshop2_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

``` r
#We can see a few peaks around specific dates. These are worth examining further.
#Using external sources, we can try to uncover what events took place at this point.
#Examples if influential events may be the appointments of committees, parliament votes, 
#amendments, court verdicts or the emergence of major crises/scandals.
#https://www.europarl.europa.eu/legislative-train/schedule
#https://oeil.secure.europarl.europa.eu/oeil/home/home.do
#https://parltrack.org/dossier/2018/0902(NLE)#/general


#Optionally, we can also look at whether these attendees met with European
#Institutions besides the EP. This could signify a larger strategy and could
#tell us more about access to European institutions
#https://transparency-register.europa.eu/searchregister-or-update/search-register_en
```
