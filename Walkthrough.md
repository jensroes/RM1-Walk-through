Research Methods (year 1) – Visualization Walk through
================
Jens Roeser
August 22 2019

<style type="text/css">
  p {line-height: 2em;}

body, td {
   font-size: 18px;
}
code.r{
  font-size: 14px;
}
pre {
  font-size: 14px
}
</style>

# Starting Rstudio

Start Rstudio on your computer and open a new script: click on `File >
New File > R Script` (or press CTRL+SHIFT+N).

Load the package `tidyverse` by typing …

``` r
library(tidyverse)
```

… and press CTRL+ENTER with the cursor in the the line of code you want
to run. `tidyverse` includes many useful functions including the
plotting function `ggplot`.

Then, tell R where your data are saved on your computer. In the tab menu
above click on: `Session > Set Working Directory > Choose Directory ...`
and browse to the directory with your data and click on `Open`. There
will be a function appearing in the console `setwd(...)`.

# Loading Data

Load the data frame into a variable, here `Df`. `"data.csv"` should be
the name of the data file you want to read in (don’t forget the quotes).

``` r
Df <- read_csv("data.csv")
```

If that doesn’t work try the following. In Rstudio’s panels you will
find an `Environment` tap: click on `Environment > Import Dataset` and
choose the option `From Text (readr) ...` or if your data are stored in
an Excel sheet `From Excel ...`. Browse to your data file and in `Import
Options:` change the `Name` to the target variable `Df`.

Have a quick peak at the data by typing `Df` into the console.

``` r
Df
```

    ## # A tibble: 384 x 5
    ##    Expression Sex   Attractiveness   Lab Table
    ##    <chr>      <chr>          <dbl> <dbl> <chr>
    ##  1 Happy      Male            14.8     1 T1   
    ##  2 Happy      Male            17.2     1 T1   
    ##  3 Happy      Male            13.9     1 T1   
    ##  4 Happy      Male            14.9     1 T1   
    ##  5 Happy      Male            18.4     1 T1   
    ##  6 Happy      Male            16.7     1 T1   
    ##  7 Happy      Male            12.4     1 T2   
    ##  8 Happy      Male            17.0     1 T2   
    ##  9 Happy      Male            13.8     1 T2   
    ## 10 Happy      Male            13.8     1 T2   
    ## # ... with 374 more rows

The data frame contains `R printnum(ncol(Df))` variables with `R
printnum(nrow(Df))` observations (student ratings). The variable
`Expression` indicates whether the image was happy or unhappy. `Sex`
indicates whether the depicted person was male or female.
`Attractiveness` is our dependent variable indicating the overall
attractiveness rating. The variable `Lab` indicates the lab group and
`Table` is the lab table within the Lab group.

# Plotting the data

For visualization of the attractiveness ratings we use `ggplot` and
Box-and-Whisker plots (box plots).

## Cohort level visualization

In the first plot we visualize the attractiveness ratings on the cohort
level, hence across lab groups. For this visualization we need to
include all our main variables of interest, i.e. the attractiveness
ratings, sex of image (male, female) and expression (happy, unhappy).
The plotting function `ggplot` needs two main arguments, the name of the
variable that contains the data frame (`data = Df`) and aesthetics
(`aes()`) which are the mappings between our variables of interest and
the graph features (such as colour, x-axis,
y-axis).

``` r
ggplot(data = Df, aes(y = Attractiveness, x = Sex, colour = Expression)) +
  geom_boxplot() +
  geom_point(position = position_dodge(.75)) +
  theme_bw(base_size = 14) +
  labs(y = "Attractiveness ratings") +
  ylim(3, 21)
```

![](Walkthrough_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

In the example we map `Attractiveness` onto the y-axis, `Sex` onto the
x-axis and `Expression` should be distinguished by `colour`. The data
are rendered by `geom_boxplot` and `geom_point`. `geom` is short for
geometrics which controls the way data are visualized (e.g. box plots,
bars, lines, points). In our example, the data are shown as box plots
and points. Notice `position = position_dodge(.75)`. This argument of
`geom_points` ensures that the data points horizontally overlap with the
box plots.

This already creates a basic plot. The next piece of code
`theme_bw(base_size = 14)` changes the default customization of
components of the plot that are not the data (e.g. background colour,
frame, grid). `base_size = 14` increases the font size. Try and remove
this bit for plotting to see the change. Try also other themes such as
`theme_light()` and `theme_minial()`.

The name of the y-label was changes to *Attractiveness ratings* using
`labs(y = "Attractiveness ratings")` and the scale of the y-axis was
extended to range from 3 to 21 using `ylim(3, 21)` which are the minimum
and maximum score, respectively.

Aside, note that all these bits need to be combined with a plus sign
`+`.

## Lab level visualization

In the next step we plot the data for each lab group individually. First
we make a small tweak the `Lab` variable. The function `paste0` adds the
number of each lab (1, 2, …) to the string *Lab group:* and then assigns
it (`<-`) to the old variable `Df$Lab`.

``` r
Df$Lab <- paste0("Lab group: ", Df$Lab)
```

Check below how the variable has changed and compare to the `Df` output
above.

``` r
Df
```

    ## # A tibble: 384 x 5
    ##    Expression Sex   Attractiveness Lab          Table
    ##    <chr>      <chr>          <dbl> <chr>        <chr>
    ##  1 Happy      Male            14.8 Lab group: 1 T1   
    ##  2 Happy      Male            17.2 Lab group: 1 T1   
    ##  3 Happy      Male            13.9 Lab group: 1 T1   
    ##  4 Happy      Male            14.9 Lab group: 1 T1   
    ##  5 Happy      Male            18.4 Lab group: 1 T1   
    ##  6 Happy      Male            16.7 Lab group: 1 T1   
    ##  7 Happy      Male            12.4 Lab group: 1 T2   
    ##  8 Happy      Male            17.0 Lab group: 1 T2   
    ##  9 Happy      Male            13.8 Lab group: 1 T2   
    ## 10 Happy      Male            13.8 Lab group: 1 T2   
    ## # ... with 374 more rows

To visualize the the data by lab group we make one small change to the
code above, everything else remains the same. We add `facet_wrap(~Lab)`.
This creates subplots for each lab group as you can see below. In
contrast to `facet_grid(~Lab)` the panels are not forced to be next to
each other as you will see in the next
section.

``` r
ggplot(data = Df, aes(y = Attractiveness, x = Sex, colour = Expression)) +
  geom_boxplot() +
  geom_point(position = position_dodge(.75)) +
  theme_bw(base_size = 14) +
  labs(y = "Attractiveness ratings") +
  ylim(3, 21) +
  facet_wrap(~Lab)
```

![](Walkthrough_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

There are different groups within each lab. We can add these to the
plot. Again, the code below is the same as above with one minor tweak.
Instead of using `facet_wrap(~Lab)` we changed this to
`facet_grid(Lab~Table)`. As you can see, this function separates the
plotting for lab groups (vertical axis) and tables (horizontal axis).

There is one small change. `theme()` can be used to change visual
features. In the code below we moved the position of the legend to the
top of the plot (`legend.position = "top"`) and to the right side
(`legend.justification =
"right"`).

``` r
ggplot(data = Df, aes(y = Attractiveness, x = Sex, colour = Expression)) +
  geom_boxplot() +
  geom_point(position = position_dodge(.75)) +
  theme_bw(base_size = 14) +
  labs(y = "Attractiveness ratings") +
  ylim(3, 21) +
  facet_grid(Lab~Table) +
  theme(legend.position = "top",
        legend.justification = "right")
```

![](Walkthrough_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

## Visualizing subgroups

Sometimes we are only interested in subgroups of the data. To visualize
only subgroups we can restrict the plotting to subsets of the data by
filtering the data for the levels your are interested in.

To pick a subset of our data `Df` we can use the function `filter`.
`filter` needs to know the name of the data frame `Df` and the way it
should subset it. In this example we select only lab group 1 (`Lab ==
"Lab group: 1"`). Remember that we changed the values of `Lab` above.
All other lab groups were removed. The output of this is stored in a new
variable called `lab1`. Notice that we used `==` instead of `=`. This is
because `=` is used for arguments and assignments and `==` is a logical
operator for *is equal to*.

``` r
lab1 <- filter(Df, Lab == "Lab group: 1")
```

The ggplot below is the same as the first plot, except we use the
subsetted data frame `lab1` instead of `Df` (see `data =
lab1`).

``` r
ggplot(data = lab1, aes(y = Attractiveness, x = Sex, colour = Expression)) +
  geom_boxplot() +
  geom_point(position = position_dodge(.75)) +
  theme_bw(base_size = 14) +
  labs(y = "Attractiveness ratings") +
  ylim(3, 21)
```

![](Walkthrough_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

As before we can display the data individually for each table in lab
group 1 by adding `facet_wrap(~Table, ncol = 4)`. `ncol = 4` makes sure
that we show 4 columns next to each
other.

``` r
ggplot(data = lab1, aes(y = Attractiveness, x = Sex, colour = Expression)) +
  geom_boxplot() +
  geom_point(position = position_dodge(.75)) +
  theme_bw(base_size = 14) +
  labs(y = "Attractiveness ratings") +
  ylim(3, 21) +
  facet_wrap(~Table, ncol = 4)
```

![](Walkthrough_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

We can subset the data even further. This time let’s pick to tables,
table T1 and T5 from lab group 1 (`lab1`). If you were to select only
one table, you could use `filter(lab1, Table == "T1")`. If we want to
select two tables we need `%in%` which tests whether a given value is
*in* `Table` and `c()` which is the *concatenate* function because we
need want to select more than one value.

``` r
labgroup <- filter(lab1, Table %in% c("T1", "T5"))
```

The plotting function is the same as the previous one (except, we
omitted
`ncol=4`).

``` r
ggplot(data = labgroup, aes(y = Attractiveness, x = Sex, colour = Expression)) +
  geom_boxplot() +
  geom_point(position = position_dodge(.75)) +
  theme_bw(base_size = 14) +
  labs(y = "Attractiveness ratings") +
  ylim(3, 21) +
  facet_wrap(~Table)
```

![](Walkthrough_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

# Saving your plot

To save a plot use `ggsave()` after creating the plot you want to save.
`ggsave()` will save the plot that is currently open. The plot will be
saved with the name *myplot* (change the name to something more
comprehensive) as .png file in the location that you selected in the
beginning. If you are not sure where that is type `getwd()` into your
console.

``` r
ggsave("myplot.png")
```

To change the size of the plot you can modify the width and height.

``` r
ggsave("myplot.png", width = 6, height = 4)
```
