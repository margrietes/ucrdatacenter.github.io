---
layout: page
title: "SSCPOLI302:<br> Analysis of MEPs (draft)"
subtitle: "Fall 2024"
date: "Last updated: 2024-08-27"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
    toc: true
---

- [Loading libraries and data](#loading-libraries-and-data)
- [Exercise 1](#exercise-1)
- [Exercise 2](#exercise-2)
- [Exercise 3](#exercise-3)
- [Exercise 4](#exercise-4)

## Loading libraries and data

``` r
library(tidyverse)
library(dplyr)
library(ggplot2)
library(rio)

Meetings_compl <-
    import("https://github.com/ucrdatacenter/projects/raw/main/SSCPOLI302/2024h2/Meetings_compl.xlsx")
```

## Exercise 1

Listing and counting the total number of MEPs

``` r
#Making a list with the MEP names and ID's

MEP_list <-
    Meetings_compl |>
    group_by(member_name, member_id) |>
    summarize()

#Counting the total number of MEPs by counting the rows in the MEP list

Nr_of_MEPs <-
    MEP_list |>
    nrow() |>
    print()
```

    ## [1] 640

``` r
#Note that not all of the 705 MEPs of term 2019-2024 are represented in the data.
```

## Exercise 2

Nationality representation

``` r
#Ranking the nr of MEPs per country
MEP_nationalities <-
    Meetings_compl |>
    group_by(country, member_id) |>
    summarize() |>
    count() |>
    arrange(desc(n))

#Making a variable from the data frame for later use
Country_MEPs <- pluck(MEP_nationalities, "n")

    
#Some countries have more MEPs than their EP seats. This means that an EP member
#did not finish their term and was replaced by another, such as the case of
#Liesje Schreinemacher

#Timeline of Liesje Schreinemacher
Schreinemacher <-
    Meetings_compl |>
    filter("member_name" == "SCHREINEMACHER Liesje") |>
    select(meeting_date, member_name, member_id) |>
    arrange(meeting_date)


#Which countries have the most meetings?
Country_meetings <-
    Meetings_compl |>
    group_by(country) |>
    summarize("count" = n()) |>
    arrange(desc(count))


#To discover which country has been the most effective, 
#we can compute the average number of meetings per MEP for each country.

#Country_avg <-
    #Meetings_compl |>
    #group_by(country) |>
    #mutate("nr_meetings" = n()) |>
    #looking for a more efficient way to finish the code
```

## Exercise 3

Political groups distribution

``` r
#calculating the number of MEPs per political group

MEPs_per_group <-
    Meetings_compl |>
    group_by(political_group, member_id) |>
    summarize("count" = n()) |>
    group_by(political_group) |>
    summarize("nr_of_MEP" = n())

#Calculating the average number of meetings per seat in the political groups to
#See which group has held the most meetings on average whilst accounting for
#their size. I have chosen to specifically use seats for this, since only 1 MEP
#can occupy them at a time. This is needed to account for groups which saw
#a lot of MEP replacements and thus have more MEPs than seats in their data.

Avg_per_seat <-
    Meetings_compl |>
    group_by(political_group) |>
    summarize("Nr_meetings" = n()) |>
    arrange(desc(Nr_meetings)) |>
    add_column("Nr_seats_may2024" = c(139, 72, 102, 176, 37, 61, 69, 49)) |>
    mutate("avg" = (Nr_meetings/Nr_seats_may2024)) |>
    arrange(desc(avg))

#Additionally, we can also compute the share of the total meetings each group has

Share_of_meetings <-
    Avg_per_seat |>
    mutate("total" = sum(Nr_meetings),
           "share(%)" = ((Nr_meetings/total)*100)) |>
    select(-total)
```

## Exercise 4

Role distributions

``` r
#Which roles have had the most meetings?

roles <-
    Meetings_compl |>
    group_by(member_capacity) |>
    summarize(nr = n())

#Make the data more informative by incorporating the political groups
group_roles <-
    Meetings_compl |>
    group_by(member_capacity, political_group) |>
    summarize(nr = n()) |>
    arrange(desc(nr), .by_group = TRUE)


#Optional extra; try to figure out which roles have linked a procedure reference.
#This is imporant for full transparency about the legislative dossier their
#meeting relates to.


#Computing which roles have added a reference with a case_when function.
#Afterwards computing what percentage of each role then referenced a file.
#Finally ranking the results by % within "yes" or "no" reference-groups.

Role_reference <-
    Meetings_compl |>
    group_by(member_capacity) |>
    mutate(reference = if_else(is.na(procedure_reference), "no", "yes")) |>
    group_by(member_capacity, reference) |>
    summarize(nr = n()) |>
    mutate(role_total = sum(nr),
           reference_percentage = ((nr/role_total)*100)) |>
    group_by(reference) |>
    arrange(desc(reference_percentage), .by_group = TRUE)
```
