STAT545 Assignment 4
================
Alex
October 9, 2018

-   [Cheatsheet for Data Reshaping Using Tidyr](#cheatsheet-for-data-reshaping-using-tidyr)
    -   [gather()](#gather)
    -   [spread()](#spread)
-   [Cheatsheet for the family of join() functions](#cheatsheet-for-the-family-of-join-functions)

Cheatsheet for Data Reshaping Using Tidyr
-----------------------------------------

Two of the most useful functions in tidyr for data reshaping are the `gather()` and `spread()` functions. Let's take a quick look at how to use them!

### gather()

This function is helpful when you have multiple datasets that you want to combine into a single data frame with nice clean columns.

#### Example

A good practical example is from our Lecture 09, which looks at cleaning the untidy dataset that we get from haphazardly combining three lotr data files:

``` r
lotr1 <- read_csv("https://raw.githubusercontent.com/jennybc/lotr-tidy/master/data/The_Fellowship_Of_The_Ring.csv")
lotr2 <- read_csv("https://raw.githubusercontent.com/jennybc/lotr-tidy/master/data/The_Two_Towers.csv")
lotr3 <- read_csv("https://raw.githubusercontent.com/jennybc/lotr-tidy/master/data/The_Return_Of_The_King.csv")
```

``` r
untidy <- bind_rows(lotr1,lotr2,lotr3) # stack the datasets on top of each other
untidy %>%
  kable("html") %>%
  kable_styling(bootstrap_options = c("striped", "hover", "condensed", full_width = F))
```

<table class="table table-striped table-hover table-condensed" style="margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="text-align:left;">
Film
</th>
<th style="text-align:left;">
Race
</th>
<th style="text-align:right;">
Female
</th>
<th style="text-align:right;">
Male
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
The Fellowship Of The Ring
</td>
<td style="text-align:left;">
Elf
</td>
<td style="text-align:right;">
1229
</td>
<td style="text-align:right;">
971
</td>
</tr>
<tr>
<td style="text-align:left;">
The Fellowship Of The Ring
</td>
<td style="text-align:left;">
Hobbit
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:right;">
3644
</td>
</tr>
<tr>
<td style="text-align:left;">
The Fellowship Of The Ring
</td>
<td style="text-align:left;">
Man
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
1995
</td>
</tr>
<tr>
<td style="text-align:left;">
The Two Towers
</td>
<td style="text-align:left;">
Elf
</td>
<td style="text-align:right;">
331
</td>
<td style="text-align:right;">
513
</td>
</tr>
<tr>
<td style="text-align:left;">
The Two Towers
</td>
<td style="text-align:left;">
Hobbit
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
2463
</td>
</tr>
<tr>
<td style="text-align:left;">
The Two Towers
</td>
<td style="text-align:left;">
Man
</td>
<td style="text-align:right;">
401
</td>
<td style="text-align:right;">
3589
</td>
</tr>
<tr>
<td style="text-align:left;">
The Return Of The King
</td>
<td style="text-align:left;">
Elf
</td>
<td style="text-align:right;">
183
</td>
<td style="text-align:right;">
510
</td>
</tr>
<tr>
<td style="text-align:left;">
The Return Of The King
</td>
<td style="text-align:left;">
Hobbit
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
2673
</td>
</tr>
<tr>
<td style="text-align:left;">
The Return Of The King
</td>
<td style="text-align:left;">
Man
</td>
<td style="text-align:right;">
268
</td>
<td style="text-align:right;">
2459
</td>
</tr>
</tbody>
</table>
Here we see that we have 4 columns *Film, Race, Female*, and *Male*. This isn't toooooo untidy, but it makes more sense to aggregate the Male and Female columns into one variable since they are just two halves of one larger variable that measures *Words*, and just have another variable *Gender* to retain the gender information. This makes it a lot easier for other functions to run on this dataset.

We can use gather() to merge the Female and Male columns into one column called *Word*, with the Male and Female information coded in another column called *Gender*:

``` r
tidy <- gather(untidy, key="Gender", value="Words", Female, Male)
tidy %>%
  kable("html") %>%
  kable_styling(bootstrap_options = c("striped", "hover", "condensed", full_width = F))
```

<table class="table table-striped table-hover table-condensed" style="margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="text-align:left;">
Film
</th>
<th style="text-align:left;">
Race
</th>
<th style="text-align:left;">
Gender
</th>
<th style="text-align:right;">
Words
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
The Fellowship Of The Ring
</td>
<td style="text-align:left;">
Elf
</td>
<td style="text-align:left;">
Female
</td>
<td style="text-align:right;">
1229
</td>
</tr>
<tr>
<td style="text-align:left;">
The Fellowship Of The Ring
</td>
<td style="text-align:left;">
Hobbit
</td>
<td style="text-align:left;">
Female
</td>
<td style="text-align:right;">
14
</td>
</tr>
<tr>
<td style="text-align:left;">
The Fellowship Of The Ring
</td>
<td style="text-align:left;">
Man
</td>
<td style="text-align:left;">
Female
</td>
<td style="text-align:right;">
0
</td>
</tr>
<tr>
<td style="text-align:left;">
The Two Towers
</td>
<td style="text-align:left;">
Elf
</td>
<td style="text-align:left;">
Female
</td>
<td style="text-align:right;">
331
</td>
</tr>
<tr>
<td style="text-align:left;">
The Two Towers
</td>
<td style="text-align:left;">
Hobbit
</td>
<td style="text-align:left;">
Female
</td>
<td style="text-align:right;">
0
</td>
</tr>
<tr>
<td style="text-align:left;">
The Two Towers
</td>
<td style="text-align:left;">
Man
</td>
<td style="text-align:left;">
Female
</td>
<td style="text-align:right;">
401
</td>
</tr>
<tr>
<td style="text-align:left;">
The Return Of The King
</td>
<td style="text-align:left;">
Elf
</td>
<td style="text-align:left;">
Female
</td>
<td style="text-align:right;">
183
</td>
</tr>
<tr>
<td style="text-align:left;">
The Return Of The King
</td>
<td style="text-align:left;">
Hobbit
</td>
<td style="text-align:left;">
Female
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
The Return Of The King
</td>
<td style="text-align:left;">
Man
</td>
<td style="text-align:left;">
Female
</td>
<td style="text-align:right;">
268
</td>
</tr>
<tr>
<td style="text-align:left;">
The Fellowship Of The Ring
</td>
<td style="text-align:left;">
Elf
</td>
<td style="text-align:left;">
Male
</td>
<td style="text-align:right;">
971
</td>
</tr>
<tr>
<td style="text-align:left;">
The Fellowship Of The Ring
</td>
<td style="text-align:left;">
Hobbit
</td>
<td style="text-align:left;">
Male
</td>
<td style="text-align:right;">
3644
</td>
</tr>
<tr>
<td style="text-align:left;">
The Fellowship Of The Ring
</td>
<td style="text-align:left;">
Man
</td>
<td style="text-align:left;">
Male
</td>
<td style="text-align:right;">
1995
</td>
</tr>
<tr>
<td style="text-align:left;">
The Two Towers
</td>
<td style="text-align:left;">
Elf
</td>
<td style="text-align:left;">
Male
</td>
<td style="text-align:right;">
513
</td>
</tr>
<tr>
<td style="text-align:left;">
The Two Towers
</td>
<td style="text-align:left;">
Hobbit
</td>
<td style="text-align:left;">
Male
</td>
<td style="text-align:right;">
2463
</td>
</tr>
<tr>
<td style="text-align:left;">
The Two Towers
</td>
<td style="text-align:left;">
Man
</td>
<td style="text-align:left;">
Male
</td>
<td style="text-align:right;">
3589
</td>
</tr>
<tr>
<td style="text-align:left;">
The Return Of The King
</td>
<td style="text-align:left;">
Elf
</td>
<td style="text-align:left;">
Male
</td>
<td style="text-align:right;">
510
</td>
</tr>
<tr>
<td style="text-align:left;">
The Return Of The King
</td>
<td style="text-align:left;">
Hobbit
</td>
<td style="text-align:left;">
Male
</td>
<td style="text-align:right;">
2673
</td>
</tr>
<tr>
<td style="text-align:left;">
The Return Of The King
</td>
<td style="text-align:left;">
Man
</td>
<td style="text-align:left;">
Male
</td>
<td style="text-align:right;">
2459
</td>
</tr>
</tbody>
</table>
#### Summary Table for gather()

gather(data, key, value, ...)

<table style="width:85%;">
<colgroup>
<col width="15%" />
<col width="69%" />
</colgroup>
<thead>
<tr class="header">
<th>Argument</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>data</td>
<td>The untidy dataset you want to tidy up</td>
</tr>
<tr class="even">
<td>key</td>
<td>The name you want for the variable that will code the columns you want to aggregate together</td>
</tr>
<tr class="odd">
<td>value</td>
<td>The column name you want for the aggregated data</td>
</tr>
<tr class="even">
<td>...</td>
<td>Names of all the columns that you want to aggregate</td>
</tr>
</tbody>
</table>

### spread()

spread() can be thought of as doing the opposite of what gather() does. If we have data in one column that we would rather have separated into multiple columns based on its value on some variable, then we use spread()!

#### Example

We can use the tidy lotr dataset from above as an example. Suppose we wanted to have a separate column of words spoken for each of the Races *Elf, Hobbit,* and *Man*. We can do this by:

``` r
tidy %>%
  spread(key = Race, value = Words) %>%
  kable("html") %>%
  kable_styling(bootstrap_options = c("striped", "hover", "condensed", full_width = F))
```

<table class="table table-striped table-hover table-condensed" style="margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="text-align:left;">
Film
</th>
<th style="text-align:left;">
Gender
</th>
<th style="text-align:right;">
Elf
</th>
<th style="text-align:right;">
Hobbit
</th>
<th style="text-align:right;">
Man
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
The Fellowship Of The Ring
</td>
<td style="text-align:left;">
Female
</td>
<td style="text-align:right;">
1229
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:right;">
0
</td>
</tr>
<tr>
<td style="text-align:left;">
The Fellowship Of The Ring
</td>
<td style="text-align:left;">
Male
</td>
<td style="text-align:right;">
971
</td>
<td style="text-align:right;">
3644
</td>
<td style="text-align:right;">
1995
</td>
</tr>
<tr>
<td style="text-align:left;">
The Return Of The King
</td>
<td style="text-align:left;">
Female
</td>
<td style="text-align:right;">
183
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
268
</td>
</tr>
<tr>
<td style="text-align:left;">
The Return Of The King
</td>
<td style="text-align:left;">
Male
</td>
<td style="text-align:right;">
510
</td>
<td style="text-align:right;">
2673
</td>
<td style="text-align:right;">
2459
</td>
</tr>
<tr>
<td style="text-align:left;">
The Two Towers
</td>
<td style="text-align:left;">
Female
</td>
<td style="text-align:right;">
331
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
401
</td>
</tr>
<tr>
<td style="text-align:left;">
The Two Towers
</td>
<td style="text-align:left;">
Male
</td>
<td style="text-align:right;">
513
</td>
<td style="text-align:right;">
2463
</td>
<td style="text-align:right;">
3589
</td>
</tr>
</tbody>
</table>
We see now that the *Words* column has been spread into three different columns, one for each of the 3 different Races! This is very useful when you wanted to quickly calculate summary statistics based on a subset of your data and you have too many different subsets to efficiently do it manually!

#### Summary

spread(data, key, value)

<table style="width:85%;">
<colgroup>
<col width="15%" />
<col width="69%" />
</colgroup>
<thead>
<tr class="header">
<th>Argument</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>data</td>
<td>The dataset you want to work on</td>
</tr>
<tr class="even">
<td>key</td>
<td>The varaible whose values you wish to use to spread the values of another variable over</td>
</tr>
<tr class="odd">
<td>value</td>
<td>The variable whose values you want to spread over your <em>key</em> variable</td>
</tr>
</tbody>
</table>

Cheatsheet for the family of join() functions
---------------------------------------------
