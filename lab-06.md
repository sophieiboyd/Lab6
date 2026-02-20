Lab 06 - Ugly charts and Simpson’s paradox
================
Sophie Boyd
2-26-26

### Load packages and data

``` r
library(tidyverse) 
library(dsbox)
library(mosaicData) 
```

### Instructional Staff Employment Trends

``` r
staff <- read_csv("data/instructional-staff.csv")
```

    ## Rows: 5 Columns: 12
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (1): faculty_type
    ## dbl (11): 1975, 1989, 1993, 1995, 1999, 2001, 2003, 2005, 2007, 2009, 2011
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
staff_long <- staff %>%
  pivot_longer(cols = -faculty_type, names_to = "year") %>%
  mutate(value = as.numeric(value))
```

``` r
staff_long %>%
  ggplot(aes(x = year, y = value, color = faculty_type)) +
  geom_line()
```

    ## `geom_line()`: Each group consists of only one observation.
    ## ℹ Do you need to adjust the group aesthetic?

![](lab-06_files/figure-gfm/line-graph-1.png)<!-- -->

``` r
staff_long %>%
  ggplot(aes(
    x = year,
    y = value,
    group = faculty_type,
    color = faculty_type
  )) +
  labs (x = "Year",
        y = "Percentage of Hires",
        title = "Percentage of Hires by Position (1975 to 2011)") +
  geom_line()
```

![](lab-06_files/figure-gfm/line-graph-group-1.png)<!-- -->

If the objective of this plot was to show that the proportion of
part-time faculty had gone up compared to other positions, I would make
all of the lines one color except for the part-time faculty line to make
it stand out. I would also change the title to highlight the comparison
between part-time hires and other positions.

### Fisheries

``` r
fisheries <- read_csv("data/fisheries.csv")
```

    ## Rows: 216 Columns: 4
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): country
    ## dbl (3): capture, aquaculture, total
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
fisheries %>%
  summarize(country, total) %>%
  arrange(desc(total))
```

    ## # A tibble: 216 × 2
    ##    country          total
    ##    <chr>            <dbl>
    ##  1 China         81500000
    ##  2 Indonesia     23184419
    ##  3 India         10785334
    ##  4 Vietnam        6420471
    ##  5 United States  5375386
    ##  6 Russia         4947253
    ##  7 Japan          4343257
    ##  8 Philippines    4228906
    ##  9 Peru           3911989
    ## 10 Bangladesh     3878324
    ## # ℹ 206 more rows

``` r
filtered_fisheries <- fisheries %>%
  filter(country %in% c("China", "Indonesia", "India", "Vietnam", "United States", "Russia", "Japan", "Philippines", "Peru", "Bangladesh"))
```

``` r
filtered_fisheries$country_ordered <- fct_relevel(filtered_fisheries$country, 'Bangladesh', 'Peru', 'Philippines', 'Japan', 'Russia', 'United States', 'Vietnam', 'India', 'Indonesia', 'China')

filtered_fisheries %>%
  ggplot(aes(
    x = country_ordered,
    y = total,
    color = country_ordered,
    fill = country_ordered
  )) +
  labs (x = 'Country',
        y = 'Total Harvest',
        color = 'Country',
        fill = 'Country',
        title = 'Total Harvest for Top 10 Countries') +
  geom_col(show.legend = FALSE) +
  coord_flip() 
```

![](lab-06_files/figure-gfm/fisheries-total-plot-1.png)<!-- -->

``` r
filtered_fisheries$country_ordered <- fct_relevel(filtered_fisheries$country, 'Bangladesh', 'Philippines', 'Vietnam', 'Japan', 'Peru', 'Russia', 'United States', 'India', 'Indonesia', 'China')
                                                  
filtered_fisheries %>%
  ggplot(aes(
    x = country_ordered,
    y = capture,
    color = country_ordered,
    fill = country_ordered
  )) +
  labs (x = 'Country',
        y = 'Capture',
        color = 'Country',
        fill = 'Country',
        title = 'Amount Captured for Top 10 Countries') +
  geom_col(show.legend = FALSE) +
  coord_flip()
```

![](lab-06_files/figure-gfm/fisheries-capture-plot-1.png)<!-- -->

``` r
filtered_fisheries$country_ordered <- fct_relevel(filtered_fisheries$country, 'Peru', 'Russia', 'United States', 'Japan', 'Bangladesh', 'Philippines', 'Vietnam', 'India', 'Indonesia', 'China')
                                                  
filtered_fisheries %>%
  ggplot(aes(
    x = country_ordered,
    y = aquaculture,
    color = country_ordered,
    fill = country_ordered
  )) +
  labs (x = 'Country',
        y = 'Aquaculture',
        color = 'Country',
        fill = 'Country',
        title = 'Amount Farmed for Top 10 Countries') +
  geom_col(show.legend = FALSE) +
  coord_flip() 
```

![](lab-06_files/figure-gfm/fisheries-aquaculture-plot-1.png)<!-- -->

- I opted for bar graphs instead of the density distribution and donut
  charts to provide more clear comparisons of the harvest amounts
  between different countries.

- I limited my sample to the 10 countries with the highest total harvest
  amounts to simplify the plots (but I realize that this results in some
  information lost).

- I used coord_flip() to move the country names to the y-axis and make
  them more readable.

- I assigned a distinct color to each country to create more contrast
  and assist with comparisons.

- I removed the legend because it provided information about country
  that was redundant with the graph.
