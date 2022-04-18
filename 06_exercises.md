---
title: 'Weekly Exercises #6'
author: "Kai Yamanishi"
output: 
  html_document:
    keep_md: TRUE
    toc: TRUE
    toc_float: TRUE
    df_print: paged
    code_download: true
---





```r
library(tidyverse)     # for data cleaning and plotting
library(gardenR)       # for Lisa's garden data
library(lubridate)     # for date manipulation
library(openintro)     # for the abbr2state() function
library(palmerpenguins)# for Palmer penguin data
library(maps)          # for map data
library(ggmap)         # for mapping points on maps
library(gplots)        # for col2hex() function
library(RColorBrewer)  # for color palettes
library(sf)            # for working with spatial data
library(leaflet)       # for highly customizable mapping
library(ggthemes)      # for more themes (including theme_map())
library(plotly)        # for the ggplotly() - basic interactivity
library(gganimate)     # for adding animation layers to ggplots
library(gifski)        # for creating the gif (don't need to load this library every time,but need it installed)
library(transformr)    # for "tweening" (gganimate)
library(shiny)         # for creating interactive apps
library(patchwork)     # for nicely combining ggplot2 graphs  
library(gt)            # for creating nice tables
library(rvest)         # for scraping data
library(robotstxt)     # for checking if you can scrape data
theme_set(theme_minimal())
```


```r
# Lisa's garden data
data("garden_harvest")

#COVID-19 data from the New York Times
covid19 <- read_csv("https://raw.githubusercontent.com/nytimes/covid-19-data/master/us-states.csv")
```

## Put your homework on GitHub!

Go [here](https://github.com/llendway/github_for_collaboration/blob/master/github_for_collaboration.md) or to previous homework to remind yourself how to get set up. 

Once your repository is created, you should always open your **project** rather than just opening an .Rmd file. You can do that by either clicking on the .Rproj file in your repository folder on your computer. Or, by going to the upper right hand corner in R Studio and clicking the arrow next to where it says Project: (None). You should see your project come up in that list if you've used it recently. You could also go to File --> Open Project and navigate to your .Rproj file. 

## Instructions

* Put your name at the top of the document. 

* **For ALL graphs, you should include appropriate labels.** 

* Feel free to change the default theme, which I currently have set to `theme_minimal()`. 

* Use good coding practice. Read the short sections on good code with [pipes](https://style.tidyverse.org/pipes.html) and [ggplot2](https://style.tidyverse.org/ggplot2.html). **This is part of your grade!**

* **NEW!!** With animated graphs, add `eval=FALSE` to the code chunk that creates the animation and saves it using `anim_save()`. Add another code chunk to reread the gif back into the file. See the [tutorial](https://animation-and-interactivity-in-r.netlify.app/) for help. 

* When you are finished with ALL the exercises, uncomment the options at the top so your document looks nicer. Don't do it before then, or else you might miss some important warnings and messages.


## Warm-up exercises from tutorial

1. Read in the fake garden harvest data. Find the data [here](https://github.com/llendway/scraping_etc/blob/main/2020_harvest.csv) and click on the `Raw` button to get a direct link to the data. After reading in the data, do one of the quick checks mentioned in the tutorial.
  

```r
garden_fake <- read_csv("https://raw.githubusercontent.com/llendway/scraping_etc/main/2020_harvest.csv")
```
  
2. Read in this [data](https://www.kaggle.com/heeraldedhia/groceries-dataset) from the kaggle website. You will need to download the data first. Save it to your project/repo folder. Do some quick checks of the data to assure it has been read in appropriately.


```r
groceries <- read_csv("Groceries_dataset.csv") 

groceries %>% 
  mutate(across(where(is.character), as.factor)) %>% 
  summary()
```

```
##  Member_number          Date               itemDescription 
##  Min.   :1000   21-01-2015:   96   whole milk      : 2502  
##  1st Qu.:2002   21-07-2015:   93   other vegetables: 1898  
##  Median :3005   08-08-2015:   92   rolls/buns      : 1716  
##  Mean   :3004   29-11-2015:   92   soda            : 1514  
##  3rd Qu.:4007   30-04-2015:   91   yogurt          : 1334  
##  Max.   :5000   26-03-2015:   88   root vegetables : 1071  
##                 (Other)   :38213   (Other)         :28730
```

3. Create a table using `gt` with data from your project or from the `garden_harvest` data if your project data aren't ready. Use at least 3 `gt()` functions.


```r
garden_harvest %>% 
  filter(week(date) == 35) %>% 
  group_by(vegetable, date) %>% 
  summarise(date, weight = sum(weight), units) %>% 
  ungroup() %>% 
  unique() %>% 
  gt(rowname_col = "vegetable", 
     groupname_col = "date") %>% 
  fmt_date(
    columns = c(date),
    date_style = 7) %>% 
  tab_header(
    title = "Week 35 Harvest",
    subtitle = "Uses the garden_harvest dataset") %>% 
  tab_options(column_labels.background.color = "darkgreen")
```

```{=html}
<div id="zywiihteqr" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#zywiihteqr .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#zywiihteqr .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#zywiihteqr .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#zywiihteqr .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#zywiihteqr .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#zywiihteqr .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#zywiihteqr .gt_col_heading {
  color: #FFFFFF;
  background-color: #006400;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#zywiihteqr .gt_column_spanner_outer {
  color: #FFFFFF;
  background-color: #006400;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#zywiihteqr .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#zywiihteqr .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#zywiihteqr .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#zywiihteqr .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#zywiihteqr .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#zywiihteqr .gt_from_md > :first-child {
  margin-top: 0;
}

#zywiihteqr .gt_from_md > :last-child {
  margin-bottom: 0;
}

#zywiihteqr .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#zywiihteqr .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#zywiihteqr .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#zywiihteqr .gt_row_group_first td {
  border-top-width: 2px;
}

#zywiihteqr .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#zywiihteqr .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#zywiihteqr .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#zywiihteqr .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#zywiihteqr .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#zywiihteqr .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#zywiihteqr .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#zywiihteqr .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#zywiihteqr .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#zywiihteqr .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#zywiihteqr .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#zywiihteqr .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#zywiihteqr .gt_left {
  text-align: left;
}

#zywiihteqr .gt_center {
  text-align: center;
}

#zywiihteqr .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#zywiihteqr .gt_font_normal {
  font-weight: normal;
}

#zywiihteqr .gt_font_bold {
  font-weight: bold;
}

#zywiihteqr .gt_font_italic {
  font-style: italic;
}

#zywiihteqr .gt_super {
  font-size: 65%;
}

#zywiihteqr .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#zywiihteqr .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#zywiihteqr .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#zywiihteqr .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#zywiihteqr .gt_fraction_denominator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: -0.05em;
}
</style>
<table class="gt_table">
  <thead class="gt_header">
    <tr>
      <th colspan="3" class="gt_heading gt_title gt_font_normal" style>Week 35 Harvest</th>
    </tr>
    <tr>
      <th colspan="3" class="gt_heading gt_subtitle gt_font_normal gt_bottom_border" style>Uses the garden_harvest dataset</th>
    </tr>
  </thead>
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1"></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">weight</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1">units</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr class="gt_group_heading_row">
      <td colspan="3" class="gt_group_heading">2020-08-29</td>
    </tr>
    <tr class="gt_row_group_first"><td class="gt_row gt_right gt_stub">basil</td>
<td class="gt_row gt_right">24</td>
<td class="gt_row gt_left">grams</td></tr>
    <tr><td class="gt_row gt_right gt_stub">carrots</td>
<td class="gt_row gt_right">1057</td>
<td class="gt_row gt_left">grams</td></tr>
    <tr><td class="gt_row gt_right gt_stub">edamame</td>
<td class="gt_row gt_right">483</td>
<td class="gt_row gt_left">grams</td></tr>
    <tr><td class="gt_row gt_right gt_stub">jalapeño</td>
<td class="gt_row gt_right">352</td>
<td class="gt_row gt_left">grams</td></tr>
    <tr><td class="gt_row gt_right gt_stub">onions</td>
<td class="gt_row gt_right">289</td>
<td class="gt_row gt_left">grams</td></tr>
    <tr><td class="gt_row gt_right gt_stub">peppers</td>
<td class="gt_row gt_right">627</td>
<td class="gt_row gt_left">grams</td></tr>
    <tr><td class="gt_row gt_right gt_stub">potatoes</td>
<td class="gt_row gt_right">978</td>
<td class="gt_row gt_left">grams</td></tr>
    <tr><td class="gt_row gt_right gt_stub">tomatoes</td>
<td class="gt_row gt_right">3813</td>
<td class="gt_row gt_left">grams</td></tr>
    <tr class="gt_group_heading_row">
      <td colspan="3" class="gt_group_heading">2020-09-01</td>
    </tr>
    <tr class="gt_row_group_first"><td class="gt_row gt_right gt_stub">beans</td>
<td class="gt_row gt_right">160</td>
<td class="gt_row gt_left">grams</td></tr>
    <tr><td class="gt_row gt_right gt_stub">pumpkins</td>
<td class="gt_row gt_right">14450</td>
<td class="gt_row gt_left">grams</td></tr>
    <tr><td class="gt_row gt_right gt_stub">squash</td>
<td class="gt_row gt_right">8377</td>
<td class="gt_row gt_left">grams</td></tr>
    <tr><td class="gt_row gt_right gt_stub">tomatoes</td>
<td class="gt_row gt_right">6649</td>
<td class="gt_row gt_left">grams</td></tr>
    <tr><td class="gt_row gt_right gt_stub">zucchini</td>
<td class="gt_row gt_right">2831</td>
<td class="gt_row gt_left">grams</td></tr>
    <tr class="gt_group_heading_row">
      <td colspan="3" class="gt_group_heading">2020-08-26</td>
    </tr>
    <tr class="gt_row_group_first"><td class="gt_row gt_right gt_stub">corn</td>
<td class="gt_row gt_right">1607</td>
<td class="gt_row gt_left">grams</td></tr>
    <tr><td class="gt_row gt_right gt_stub">tomatoes</td>
<td class="gt_row gt_right">6289</td>
<td class="gt_row gt_left">grams</td></tr>
    <tr class="gt_group_heading_row">
      <td colspan="3" class="gt_group_heading">2020-08-27</td>
    </tr>
    <tr class="gt_row_group_first"><td class="gt_row gt_right gt_stub">lettuce</td>
<td class="gt_row gt_right">14</td>
<td class="gt_row gt_left">grams</td></tr>
    <tr class="gt_group_heading_row">
      <td colspan="3" class="gt_group_heading">2020-08-28</td>
    </tr>
    <tr class="gt_row_group_first"><td class="gt_row gt_right gt_stub">lettuce</td>
<td class="gt_row gt_right">85</td>
<td class="gt_row gt_left">grams</td></tr>
    <tr><td class="gt_row gt_right gt_stub">raspberries</td>
<td class="gt_row gt_right">29</td>
<td class="gt_row gt_left">grams</td></tr>
    <tr><td class="gt_row gt_right gt_stub">zucchini</td>
<td class="gt_row gt_right">3244</td>
<td class="gt_row gt_left">grams</td></tr>
    <tr class="gt_group_heading_row">
      <td colspan="3" class="gt_group_heading">2020-08-30</td>
    </tr>
    <tr class="gt_row_group_first"><td class="gt_row gt_right gt_stub">tomatoes</td>
<td class="gt_row gt_right">8398</td>
<td class="gt_row gt_left">grams</td></tr>
  </tbody>
  
  
</table>
</div>
```
  
5. Use `patchwork` operators and functions to combine at least two graphs using your project data or `garden_harvest` data if your project data aren't read.


```r
harvest_weight <- garden_harvest %>% 
  group_by(date) %>% 
  summarise(weight = sum(weight)) %>% 
  ggplot(aes(x = date, y = weight)) +
  geom_line() +
  labs(x = "", y = "", title = "Daily Grams Harvested")

vegetable_harvested <- garden_harvest %>% 
  group_by(date, vegetable) %>% 
  summarise() %>% 
  summarise(num_veg = n()) %>% 
  ggplot(aes(x = date, y = num_veg)) +
  geom_line() +
  labs(x = "", y = "", title = "Daily Unique Vegetables Harvested")

harvest_weight + vegetable_harvested
```

![](06_exercises_files/figure-html/unnamed-chunk-4-1.png)<!-- -->
  
## Webscraping exercise (also from tutorial)

Use the data from the [Macalester Registrar's Fall 2017 Class Schedule](https://www.macalester.edu/registrar/schedules/2017fall/class-schedule/#crs10008) to complete all these exercises.

6. Find the correct selectors for the following fields. Make sure that each matches 762 results:

  * Course Number
  * Course Name
  * Day
  * Time
  * Room
  * Instructor
  * Avail. / Max
  * General Education Requirements (make sure you only match 762; beware of the Mac copyright banner at the bottom of the page!)
  * Description

Then, put all this information into one dataset (tibble or data.frame) Do not include any extraneous information like "Instructor: ".


```r
fall2017 <- read_html("https://www.macalester.edu/registrar/schedules/2017fall/class-schedule/#crs10008")

scrape_col <- function(html, element, cutoff = 0) {
  html %>% 
  html_elements(element) %>%
  html_text2() %>% 
  str_sub(start = cutoff)
}

course_number <- scrape_col(fall2017, ".class-schedule-course-number")
course_title <- scrape_col(fall2017, ".class-schedule-course-title")
course_day <- scrape_col(fall2017, "td.class-schedule-label:nth-child(3)", 7)
course_time <- scrape_col(fall2017, "td.class-schedule-label:nth-child(4)", 7)
course_room <- scrape_col(fall2017, "td.class-schedule-label:nth-child(5)", 7)
course_instructor <- scrape_col(fall2017, "td.class-schedule-label:nth-child(6)", 13) 
course_availability <- scrape_col(fall2017, "td.class-schedule-label:nth-child(7)", 14) 
course_description <- scrape_col(fall2017, "td.class-schedule-notes")

fall_2017 <- tibble(course_number, 
       course_title, 
       course_day, 
       course_time, 
       course_room, 
       course_instructor, 
       course_availability, 
       course_description)
```

7. Create a graph that shows the number of sections offered per department. Hint: The department is a substring of the course number - there are `str_XXX()` functions that can help. Yes, COMP and MATH are the same department, but for this exercise you can just show the results by four letter department code, e.g., with COMP and MATH separate.


```r
fall_2017 %>% 
  summarise(department = str_sub(course_number, end = 4)) %>% 
  group_by(department) %>% 
  summarise(total_courses = n()) %>% 
  filter(str_starts(department, "PE ") != TRUE) %>% 
  ggplot(aes(x = total_courses, y = fct_reorder(department, total_courses))) + 
  geom_bar(stat = "identity") +
  labs(title = "Number of Courses Offered by Each Department, Fall 2017", x = "", y = "")
```

<img src="06_exercises_files/figure-html/unnamed-chunk-6-1.png" title="Total number of courses offered by each department for fall semester, 2017." alt="Total number of courses offered by each department for fall semester, 2017."  />


8. Analyze the typical length of course names by department. To do so, create a new data table based on your courses data table, with the following changes:


```r
fall_2017_8 <- fall_2017 %>% 
  mutate(title_length = str_length(course_title), 
         description_length = str_length(course_description), 
         course_department = str_sub(course_number, end = 4)) %>% 
  group_by(course_department) %>% 
  filter(n() >= 10) %>% 
  mutate(avg_title_length = mean(title_length), avg_description_length = mean(description_length)) %>% 
  ungroup()

one <- fall_2017_8 %>% 
  ggplot(aes()) +
  geom_point(aes(x = title_length, 
                 y = fct_reorder(course_department, avg_title_length))) +
  geom_point(aes(x = avg_title_length, 
                 y = fct_reorder(course_department, avg_title_length)), 
             colour = "red", 
             size = 2) +
  labs(title = "Average Course Title Length by Department", 
       x = "Characters", 
       y = "")

two <- fall_2017_8 %>% 
  ggplot(aes()) +
  geom_point(aes(x = description_length, 
                 y = fct_reorder(course_department, avg_description_length))) +
  geom_point(aes(x = avg_description_length, 
                 y = fct_reorder(course_department, avg_description_length)), 
             colour = "red", 
             size = 2) +
  labs(title = "Average Course Description Length by Department", 
       x = "Characters", 
       y = "")

one + two
```

<img src="06_exercises_files/figure-html/unnamed-chunk-7-1.png" title="Average length in characters of Course Titles and Descriptions by Department at Macalester College, Fall 2017" alt="Average length in characters of Course Titles and Descriptions by Department at Macalester College, Fall 2017"  />
  
  * New columns for the length of the title of a course and the length of the description of the course. Hint: `str_length`.  
  * Remove departments that have fewer than 10 sections of courses. To do so, group by department, then remove observations in groups with fewer than 10 sections (Hint: use filter with n()). Then `ungroup()` the data.  
  * Create a visualization of the differences across groups in lengths of course names or course descriptions. Think carefully about the visualization you should be using!


  

**DID YOU REMEMBER TO UNCOMMENT THE OPTIONS AT THE TOP?**
