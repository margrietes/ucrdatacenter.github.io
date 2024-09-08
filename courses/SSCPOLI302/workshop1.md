---
layout: page
title: "SSCPOLI302:<br> Analysis of MEPs (draft)"
subtitle: "Fall 2024"
date: "Last updated: 2024-09-08"
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
- [Exercise 3](#exercise-3-1)
- [Exercise 4](#exercise-4)
- [Exercise 5](#exercise-5)

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
#Counting the nr of MEPs per country
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

#Meetings Timeline of Liesje Schreinemacher
Schreinemacher <-
    Meetings_compl |>
    filter(member_name == "SCHREINEMACHER Liesje") |>
    select(meeting_date, member_name, member_id) |>
    group_by(meeting_date) |>
    mutate(nr = n()) |>
    arrange(meeting_date)


#Which nationalities are represented the most?
Country_meetings <-
    Meetings_compl |>
    group_by(country) |>
    summarize("count" = n()) |>
    arrange(desc(count))


#We can compute the average number of meetings per MEP for each nationality.

#Country_avg <-
    #Meetings_compl |>
    #group_by(country) |>
    #mutate("nr_meetings" = n()) |>
    #looking for a more efficient way to finish the code
```

## Exercise 3

Visualising the results of exercise 2

``` r
#Graphing the nr of MEPs per country with a ggplot.
ggplot(MEP_nationalities, mapping=aes(x = country, y = n)) +
    geom_col()+
    labs(title = "Nr of MEPs per country between 2019-2024", x = "Country", y = "Nr of Meetings")
```

![](workshop1_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
#As we can see, the current plot is very unorganised and hardly legible.
#We can improve its quality by doing the following;

#First we can organise the bars based on their number of meetings using the
#reorder() command.

ggplot(data = MEP_nationalities, mapping=aes(x = reorder(country, n), y = n)) +
    geom_col()+
    labs(title = "Nr of MEPs per country between 2019-2024", x = "Country", y = "Nr of Meetings")
```

![](workshop1_files/figure-gfm/unnamed-chunk-4-2.png)<!-- -->

``` r
#In order to improve the precision of the graph, we can set custom intervals
#for the y-axis using the scale_y_continuous() command.

ggplot(data = MEP_nationalities, mapping=aes(x = reorder(country, n), y = n)) +
    geom_col()+
    scale_y_continuous(breaks = seq(0, 350, by = 20))+
    labs(title = "Nr of MEPs per country between 2019-2024", x = "Country", y = "Nr of Meetings")
```

![](workshop1_files/figure-gfm/unnamed-chunk-4-3.png)<!-- -->

``` r
#By default, the plot will put the country names on the x axis, by flipping the axes
#with coord_flip, we can improve the quality of the plot.

ggplot(data = MEP_nationalities, mapping=aes(x = reorder(country, n), y = n))+
    geom_col()+
     scale_y_continuous(breaks = seq(0, 350, by = 20))+
    coord_flip()+
    labs(title = "Nr of MEPs per country between 2019-2024", x = "Country", y = "Nr of Meetings")
```

![](workshop1_files/figure-gfm/unnamed-chunk-4-4.png)<!-- -->

``` r
#Now we have a legible plot



#In this case, we will visualise the meetings of Liesje Schreinemacher.
#Plotting a meetings timeline can be done in multiple ways.
#The first one is by making a scatterplot of the actual meetings
ggplot(data = Schreinemacher, mapping = aes(x = meeting_date, y = nr))+
    geom_point()
```

![](workshop1_files/figure-gfm/unnamed-chunk-4-5.png)<!-- -->

``` r
#This plot is not very appealing though.


#However, we can improve the visualisation by making
#a density plot of the meeting dates.
ggplot(data = Schreinemacher, mapping = aes(x = meeting_date))+
    geom_density(fill = "lightblue", alpha = 0.5, kernel = "rectangular")+
    labs(title = "Meeting activity of Liesje Schreinemacher during her
         MEP career", x = "Date", y = "Density")
```

![](workshop1_files/figure-gfm/unnamed-chunk-4-6.png)<!-- -->

``` r
#However, we can also compare the density of Liesje Schreinemacher's
#meetings to the overall meeting density of the EP.
#We will do this by changing a few things within the code layout
#and by adding a second density plot showing the overall EP activity.

ggplot()+
geom_density(data = Schreinemacher, mapping = aes(x = meeting_date),
             fill = "lightblue", alpha = 0.5, kernel = "rectangular")+
    geom_density(data = Meetings_compl, mapping = aes(x = meeting_date),
                 fill = "orange", alpha = 0.25, kernel = "rectangular")+
    labs(title = "Meeting density of Liesje Schreinemacher compared to
         the European Parliament between 2019-2024",
         x = "Date", y = "Density")
```

![](workshop1_files/figure-gfm/unnamed-chunk-4-7.png)<!-- -->

``` r
#Note that this graph does not show the amount of meetings. It just
#displays how strong the density of the meeting activity is.
#As we can see, Liesje Schreinemacher's activity stops about midway
#through 2022, which is when she exited the EP.



#Plotting the number of meetings per nationality is practically the same as the
#one for the Nr of MEPS per country.
#How does this plot differ from the order in the NR of MEPS?
ggplot(Country_meetings, mapping=aes(x = country, y = count)) +
    geom_col(aes(reorder(country, count)))+
      scale_y_continuous(breaks = seq(0, 20000, by = 2000))+
    coord_flip() +
    labs(title = "Reported total meetings by nationality from 2019-2024", x = "Country", y = "Nr of Meetings")
```

![](workshop1_files/figure-gfm/unnamed-chunk-4-8.png)<!-- -->

``` r
#graph more informative by avg nr of meetings per MEP by country/group >
#dependent on earlier code fix.
```

## Exercise 3

Analysing meetings per political group

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

Visualising the results of exercise 3

``` r
#Plotting the graph for the NR of MEPs per political group.

ggplot(data = MEPs_per_group, mapping = aes(x = political_group, 
                                            y = nr_of_MEP))+
    geom_col(color = "black")+
    labs(title = "Nr of MEPs per political group in May 2024",
         x = "Political group", y = "Nr of MEPs")
```

![](workshop1_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

``` r
#The current plot is very bland and unorganised, so we can improve its quality
#with a few steps.

#By tweaking the x-scale again, we can organise the bars in the
#ascending order of the NR of MEPs.

ggplot(data = MEPs_per_group, mapping = aes(x = reorder(political_group, nr_of_MEP), 
                                            y = nr_of_MEP))+
    geom_col(color = "black")+
    labs(title = "Nr of MEPs per political group in May 2024",
         x = "Political group", y = "Nr of MEPs")
```

![](workshop1_files/figure-gfm/unnamed-chunk-6-2.png)<!-- -->

``` r
#Now the graph is more structured, but it's still boring to look at.
#We can fix this by specifying the colours of the bars so that they match
#those of their political groups

ggplot(data = MEPs_per_group, mapping = aes(x = reorder(political_group, nr_of_MEP), 
                                            y = nr_of_MEP, fill = political_group))+
    scale_fill_manual(values = c("ECR" = "deepskyblue4", "EPP" = "deepskyblue", "S&D" = "red",
                                 "Renew" = "gold", "Greens" = "forestgreen",
                                 "the Left" = "darkred", "ID" = "darkblue", "Non-attached" = "grey"))+
    geom_col(color = "black")+
    labs(title = "Nr of MEPs per political group in May 2024",
         x = "Political group", y = "Nr of MEPs")
```

![](workshop1_files/figure-gfm/unnamed-chunk-6-3.png)<!-- -->

``` r
#Plotting the seat averages per political group.
ggplot(data = Avg_per_seat, mapping = aes(x = reorder(political_group, avg),
                                          y = avg, fill = political_group))+
    geom_col(color = "black")+
    scale_fill_manual(values = c("ECR" = "deepskyblue4", "EPP" = "deepskyblue", "S&D" = "red",
                                 "Renew" = "gold", "Greens" = "forestgreen",
                                 "the Left" = "darkred", "ID" = "darkblue", "Non-attached" = "grey"))+
    labs(title = "Average nr of reported meetings per political group seat",
         subtitle = "The average number of reported meetings per seat by
         political group between June 2019 and June 2024",
         x = "Political group", y = "Nr of MEPs")
```

![](workshop1_files/figure-gfm/unnamed-chunk-6-4.png)<!-- -->

## Exercise 5

Role distributions

``` r
#Which roles have had the most meetings?

roles <-
    Meetings_compl |>
    group_by(member_capacity) |>
    summarize(nr = n())

#Make the data more informative by showing the role counts per political group
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

\##Exercise 6

Visualising the results of exercise 5

``` r
#If we want to visualise the reported meetings of different
#member roles per political group, we can do the following:
ggplot(data = group_roles, mapping = aes(x = member_capacity, y = nr, fill =
                                             political_group))+
    geom_col()+
    scale_fill_manual(values = c("ECR" = "deepskyblue4", "EPP" = "deepskyblue", "S&D" = "red",
                                 "Renew" = "gold", "Greens" = "forestgreen",
                                 "the Left" = "darkred", "ID" = "darkblue", "Non-attached" = "grey"))
```

![](workshop1_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

``` r
#Once again, the plot is not very nice. We can reuse the coord_flip() command
#to make it better.

ggplot(data = group_roles, mapping = aes(x = member_capacity, y = nr, fill =
                                             political_group))+
    geom_col()+
    scale_fill_manual(values = c("ECR" = "deepskyblue4", "EPP" = "deepskyblue", "S&D" = "red",
                                 "Renew" = "gold", "Greens" = "forestgreen",
                                 "the Left" = "darkred", "ID" = "darkblue", "Non-attached" = "grey"))+
    coord_flip()
```

![](workshop1_files/figure-gfm/unnamed-chunk-8-2.png)<!-- -->

``` r
#Still this is not a very useful graph. We can use position = "dodge" to 
#split the bars.

ggplot(data = group_roles, mapping = aes(x = member_capacity, y = nr, fill =
                                             political_group))+
    geom_col(position = "dodge")+
    scale_fill_manual(values = c("ECR" = "deepskyblue4", "EPP" = "deepskyblue", "S&D" = "red",
                                 "Renew" = "gold", "Greens" = "forestgreen",
                                 "the Left" = "darkred", "ID" = "darkblue", "Non-attached" = "grey"))+
    coord_flip()
```

![](workshop1_files/figure-gfm/unnamed-chunk-8-3.png)<!-- -->

``` r
#Now that the bars have been split, it is a bit hard to see where one
#group of bars ends and another begins. A solution could be to simply
#give each role its own graph using the facet_wrap() function.
#In order to do this, we also need to change variable represented in the
#x-asis.

ggplot(data = group_roles, mapping = aes(x = political_group, y = nr, fill =
                                             political_group))+
     scale_fill_manual(values = c("ECR" = "deepskyblue4", "EPP" = "deepskyblue", "S&D" = "red",
                                 "Renew" = "gold", "Greens" = "forestgreen",
                                 "the Left" = "darkred", "ID" = "darkblue", "Non-attached" = "grey"))+
    geom_col()+
    coord_flip()+
    facet_wrap("member_capacity")
```

![](workshop1_files/figure-gfm/unnamed-chunk-8-4.png)<!-- -->

``` r
#The final problem seems to be that the "Member" group is so large
#that it dwarfs the values of other groups.
#This can be fixed by changing the y-scale values to a log10 scale.
#For extra tidyness, we can also reorder the x-axis based on the nr of meetings, 
#as we have before.

ggplot(data = group_roles, mapping = aes(x = reorder(political_group, nr), y = nr, fill =
                                             political_group))+
     scale_fill_manual(values = c("ECR" = "deepskyblue4", "EPP" = "deepskyblue", "S&D" = "red",
                                 "Renew" = "gold", "Greens" = "forestgreen",
                                 "the Left" = "darkred", "ID" = "darkblue", "Non-attached" = "grey"))+
    geom_col()+
    coord_flip()+
    facet_wrap("member_capacity")+
    scale_y_log10()+
    labs(x = "Political group", y = "Nr of meetings")
```

![](workshop1_files/figure-gfm/unnamed-chunk-8-5.png)<!-- -->
