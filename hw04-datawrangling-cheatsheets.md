STAT545 Assignment 4
================
Alex
October 9, 2018

-   [Cheatsheet for Data Reshaping Using Tidyr](#cheatsheet-for-data-reshaping-using-tidyr)
    -   [gather()](#gather)
    -   [spread()](#spread)
-   [Cheatsheet for the family of join() functions](#cheatsheet-for-the-family-of-join-functions)
    -   [Data Prep](#data-prep)
    -   [inner\_join()](#inner_join)
    -   [semi\_join()](#semi_join)
    -   [left\_join()](#left_join)

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

### Data Prep

As may be apparent from my first assigment, I'm really interested in jazz! In order to look at how the join() functions work, let's first prep a dataset that consists of Name, Age, Instrument, Most Prominent Era:

``` r
jazzgreats <- "
Name, Age, Instrument, Genre
Oscar Peterson, 82, Piano, Swing
Count Basie, 79, Piano, Big Band
Miles Davis, 65, Trumpet, Modal
John Coltrane, 40, Saxophone, Hard Bop
Duke Ellington, 75, Piano, Big Band
Buddy Rich, 69, Drums, Big Band
Charlie Parker, 34, Saxophone, Bebop
Bill Evans, 51, Piano, Modal
Chick Corea, 77, Piano, Fusion
Ella Fitzgerald, 79, Vocals, Swing
Ornette Coleman, 85, Saxophone, Free Jazz
"

jazzgreats <- read_csv(jazzgreats, skip = 1) 

jazzgreats %>%
  kable("html") %>%
  kable_styling(bootstrap_options = c("striped", "hover", "condensed", full_width = F))
```

<table class="table table-striped table-hover table-condensed" style="margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="text-align:left;">
Name
</th>
<th style="text-align:right;">
Age
</th>
<th style="text-align:left;">
Instrument
</th>
<th style="text-align:left;">
Genre
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
Oscar Peterson
</td>
<td style="text-align:right;">
82
</td>
<td style="text-align:left;">
Piano
</td>
<td style="text-align:left;">
Swing
</td>
</tr>
<tr>
<td style="text-align:left;">
Count Basie
</td>
<td style="text-align:right;">
79
</td>
<td style="text-align:left;">
Piano
</td>
<td style="text-align:left;">
Big Band
</td>
</tr>
<tr>
<td style="text-align:left;">
Miles Davis
</td>
<td style="text-align:right;">
65
</td>
<td style="text-align:left;">
Trumpet
</td>
<td style="text-align:left;">
Modal
</td>
</tr>
<tr>
<td style="text-align:left;">
John Coltrane
</td>
<td style="text-align:right;">
40
</td>
<td style="text-align:left;">
Saxophone
</td>
<td style="text-align:left;">
Hard Bop
</td>
</tr>
<tr>
<td style="text-align:left;">
Duke Ellington
</td>
<td style="text-align:right;">
75
</td>
<td style="text-align:left;">
Piano
</td>
<td style="text-align:left;">
Big Band
</td>
</tr>
<tr>
<td style="text-align:left;">
Buddy Rich
</td>
<td style="text-align:right;">
69
</td>
<td style="text-align:left;">
Drums
</td>
<td style="text-align:left;">
Big Band
</td>
</tr>
<tr>
<td style="text-align:left;">
Charlie Parker
</td>
<td style="text-align:right;">
34
</td>
<td style="text-align:left;">
Saxophone
</td>
<td style="text-align:left;">
Bebop
</td>
</tr>
<tr>
<td style="text-align:left;">
Bill Evans
</td>
<td style="text-align:right;">
51
</td>
<td style="text-align:left;">
Piano
</td>
<td style="text-align:left;">
Modal
</td>
</tr>
<tr>
<td style="text-align:left;">
Chick Corea
</td>
<td style="text-align:right;">
77
</td>
<td style="text-align:left;">
Piano
</td>
<td style="text-align:left;">
Fusion
</td>
</tr>
<tr>
<td style="text-align:left;">
Ella Fitzgerald
</td>
<td style="text-align:right;">
79
</td>
<td style="text-align:left;">
Vocals
</td>
<td style="text-align:left;">
Swing
</td>
</tr>
<tr>
<td style="text-align:left;">
Ornette Coleman
</td>
<td style="text-align:right;">
85
</td>
<td style="text-align:left;">
Saxophone
</td>
<td style="text-align:left;">
Free Jazz
</td>
</tr>
</tbody>
</table>
Great! Now suppose we have another dataset that contains different jazz genres along with the starting year for each genre:

``` r
genre_years <- "
Genre, Year
Dixieland, 1920
Swing, 1930
Big Band, 1930
Bebop, 1945
Modal, 1950
Hard Bop, 1955
Fusion, 1970
"

genre_years <- read_csv(genre_years, skip = 1)

genre_years %>%
  kable("html") %>%
  kable_styling(bootstrap_options = c("striped", "hover", "condensed", full_width = F))
```

<table class="table table-striped table-hover table-condensed" style="margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="text-align:left;">
Genre
</th>
<th style="text-align:right;">
Year
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
Dixieland
</td>
<td style="text-align:right;">
1920
</td>
</tr>
<tr>
<td style="text-align:left;">
Swing
</td>
<td style="text-align:right;">
1930
</td>
</tr>
<tr>
<td style="text-align:left;">
Big Band
</td>
<td style="text-align:right;">
1930
</td>
</tr>
<tr>
<td style="text-align:left;">
Bebop
</td>
<td style="text-align:right;">
1945
</td>
</tr>
<tr>
<td style="text-align:left;">
Modal
</td>
<td style="text-align:right;">
1950
</td>
</tr>
<tr>
<td style="text-align:left;">
Hard Bop
</td>
<td style="text-align:right;">
1955
</td>
</tr>
<tr>
<td style="text-align:left;">
Fusion
</td>
<td style="text-align:right;">
1970
</td>
</tr>
</tbody>
</table>
The information in the `genre_years` data frame would be very useful to have in our `jazzgreats` data frame. Let's look at how we can use different join() functions to accomplish this, and how each one differs!

### inner\_join()

inner\_join(x,y) returns all rows in x that have matching values in y, then appends all applicable rows from y to x.

``` r
inner_join(jazzgreats, genre_years) %>%
  kable("html") %>%
  kable_styling(bootstrap_options = c("striped", "hover", "condensed", full_width = F))
```

    ## Joining, by = "Genre"

<table class="table table-striped table-hover table-condensed" style="margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="text-align:left;">
Name
</th>
<th style="text-align:right;">
Age
</th>
<th style="text-align:left;">
Instrument
</th>
<th style="text-align:left;">
Genre
</th>
<th style="text-align:right;">
Year
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
Oscar Peterson
</td>
<td style="text-align:right;">
82
</td>
<td style="text-align:left;">
Piano
</td>
<td style="text-align:left;">
Swing
</td>
<td style="text-align:right;">
1930
</td>
</tr>
<tr>
<td style="text-align:left;">
Count Basie
</td>
<td style="text-align:right;">
79
</td>
<td style="text-align:left;">
Piano
</td>
<td style="text-align:left;">
Big Band
</td>
<td style="text-align:right;">
1930
</td>
</tr>
<tr>
<td style="text-align:left;">
Miles Davis
</td>
<td style="text-align:right;">
65
</td>
<td style="text-align:left;">
Trumpet
</td>
<td style="text-align:left;">
Modal
</td>
<td style="text-align:right;">
1950
</td>
</tr>
<tr>
<td style="text-align:left;">
John Coltrane
</td>
<td style="text-align:right;">
40
</td>
<td style="text-align:left;">
Saxophone
</td>
<td style="text-align:left;">
Hard Bop
</td>
<td style="text-align:right;">
1955
</td>
</tr>
<tr>
<td style="text-align:left;">
Duke Ellington
</td>
<td style="text-align:right;">
75
</td>
<td style="text-align:left;">
Piano
</td>
<td style="text-align:left;">
Big Band
</td>
<td style="text-align:right;">
1930
</td>
</tr>
<tr>
<td style="text-align:left;">
Buddy Rich
</td>
<td style="text-align:right;">
69
</td>
<td style="text-align:left;">
Drums
</td>
<td style="text-align:left;">
Big Band
</td>
<td style="text-align:right;">
1930
</td>
</tr>
<tr>
<td style="text-align:left;">
Charlie Parker
</td>
<td style="text-align:right;">
34
</td>
<td style="text-align:left;">
Saxophone
</td>
<td style="text-align:left;">
Bebop
</td>
<td style="text-align:right;">
1945
</td>
</tr>
<tr>
<td style="text-align:left;">
Bill Evans
</td>
<td style="text-align:right;">
51
</td>
<td style="text-align:left;">
Piano
</td>
<td style="text-align:left;">
Modal
</td>
<td style="text-align:right;">
1950
</td>
</tr>
<tr>
<td style="text-align:left;">
Chick Corea
</td>
<td style="text-align:right;">
77
</td>
<td style="text-align:left;">
Piano
</td>
<td style="text-align:left;">
Fusion
</td>
<td style="text-align:right;">
1970
</td>
</tr>
<tr>
<td style="text-align:left;">
Ella Fitzgerald
</td>
<td style="text-align:right;">
79
</td>
<td style="text-align:left;">
Vocals
</td>
<td style="text-align:left;">
Swing
</td>
<td style="text-align:right;">
1930
</td>
</tr>
</tbody>
</table>
All entries in `jazzgreats` that have a Genre that also appears in `genre_years` have been returned. We see that with this join, we have lost Ornette Coleman from `jazzgreats` since *Free Jazz* was not a genre in `genre_years`.

### semi\_join()

semi\_join(x,y) is like inner\_join(x,y), except at the end it returns only the rows that belonged to x.

``` r
semi_join(jazzgreats, genre_years) %>%
  kable("html") %>%
  kable_styling(bootstrap_options = c("striped", "hover", "condensed", full_width = F))
```

    ## Joining, by = "Genre"

<table class="table table-striped table-hover table-condensed" style="margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="text-align:left;">
Name
</th>
<th style="text-align:right;">
Age
</th>
<th style="text-align:left;">
Instrument
</th>
<th style="text-align:left;">
Genre
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
Oscar Peterson
</td>
<td style="text-align:right;">
82
</td>
<td style="text-align:left;">
Piano
</td>
<td style="text-align:left;">
Swing
</td>
</tr>
<tr>
<td style="text-align:left;">
Count Basie
</td>
<td style="text-align:right;">
79
</td>
<td style="text-align:left;">
Piano
</td>
<td style="text-align:left;">
Big Band
</td>
</tr>
<tr>
<td style="text-align:left;">
Miles Davis
</td>
<td style="text-align:right;">
65
</td>
<td style="text-align:left;">
Trumpet
</td>
<td style="text-align:left;">
Modal
</td>
</tr>
<tr>
<td style="text-align:left;">
John Coltrane
</td>
<td style="text-align:right;">
40
</td>
<td style="text-align:left;">
Saxophone
</td>
<td style="text-align:left;">
Hard Bop
</td>
</tr>
<tr>
<td style="text-align:left;">
Duke Ellington
</td>
<td style="text-align:right;">
75
</td>
<td style="text-align:left;">
Piano
</td>
<td style="text-align:left;">
Big Band
</td>
</tr>
<tr>
<td style="text-align:left;">
Buddy Rich
</td>
<td style="text-align:right;">
69
</td>
<td style="text-align:left;">
Drums
</td>
<td style="text-align:left;">
Big Band
</td>
</tr>
<tr>
<td style="text-align:left;">
Charlie Parker
</td>
<td style="text-align:right;">
34
</td>
<td style="text-align:left;">
Saxophone
</td>
<td style="text-align:left;">
Bebop
</td>
</tr>
<tr>
<td style="text-align:left;">
Bill Evans
</td>
<td style="text-align:right;">
51
</td>
<td style="text-align:left;">
Piano
</td>
<td style="text-align:left;">
Modal
</td>
</tr>
<tr>
<td style="text-align:left;">
Chick Corea
</td>
<td style="text-align:right;">
77
</td>
<td style="text-align:left;">
Piano
</td>
<td style="text-align:left;">
Fusion
</td>
</tr>
<tr>
<td style="text-align:left;">
Ella Fitzgerald
</td>
<td style="text-align:right;">
79
</td>
<td style="text-align:left;">
Vocals
</td>
<td style="text-align:left;">
Swing
</td>
</tr>
</tbody>
</table>
We see that we get exactly the same result as with inner\_join, except the *Years* column has not been appended to `jazzgreats`.

### left\_join()

left\_join(x,y)
