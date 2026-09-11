# Geog4/6300: Lab 0–Tornadoes and social vulnerability


*Your name: Wil Gamble*

This “lab” assignment provides an opportunity to learn the basic
mechanics of Github classroom, loading data, and doing some basic data
manipulation using the tools in the tidyverse. Through this lab, you
will identify counties with the highest levels of social vulnerability
that also had high numbers of severe tornadoes (rated F3 and higher)
during this study period.

This lab assesses the following learning standards:

1.  Classify variables by measurement type (nominal, ordinal, interval,
    ratio) and justify those classifications with reference to the
    characteristics of the data. (Task 2)
2.  Load tabular and spatial data into a code-based environment from
    multiple sources, including local files, remote APIs, and spatial
    file formats. (Task 1)
3.  Filter, aggregate, and transform datasets using grouping and summary
    operations to answer specific analytical questions. (Task 3, 4, and
    5)
4.  Join multiple datasets using appropriate join strategies and explain
    how different join types affect the resulting output. (Task 6 and 7)
5.  Use Git and Github to create and share project materials in a
    repository format. (Lab submission)

## Loading the data

We’ll be combining two datasets for this assignment: NOAA’s database of
tornadoes from 1950-2026 (so far) and the CDC’s Social Vulnerability
Index (SVI): https://www.atsdr.cdc.gov/place-health/php/svi/index.html

You can load the tornado data (`noaa_stormevent_tornado_2026_08.csv` in
the folder `data/stormevents/`) using the `read_csv` function. The SVI
data is located at `data/SVI_2022_US_county.csv`. Load the tornado
dataset into an object called `tornadoes` and the SVI dataset into an
object called `svi`.

(Side note: you can see how we downloaded this data directly from NWS in
the “stormdata_download” script in the data folder.)

**Task 1:** *Load the tornado data and the SVI data using `read_csv`.*

``` r
# Your code goes here.

SVI_Data<-read_csv("data/SVI_2022_US_county.csv")
```

    Rows: 3144 Columns: 158
    ── Column specification ────────────────────────────────────────────────────────
    Delimiter: ","
    chr   (7): ST, STATE, ST_ABBR, STCNTY, COUNTY, FIPS, LOCATION
    dbl (151): AREA_SQMI, E_TOTPOP, M_TOTPOP, E_HU, M_HU, E_HH, M_HH, E_POV150, ...

    ℹ Use `spec()` to retrieve the full column specification for this data.
    ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
Tornados<-read_csv("data/stormevents/noaa_stormevent_tornado_2026_08.csv")
```

    Warning: One or more parsing issues, call `problems()` on your data frame for details,
    e.g.:
      dat <- vroom(...)
      problems(dat)

    Rows: 80996 Columns: 52
    ── Column specification ────────────────────────────────────────────────────────
    Delimiter: ","
    chr (25): STATE, MONTH_NAME, EVENT_TYPE, CZ_TYPE, CZ_NAME, cty_fips, WFO, BE...
    dbl (24): BEGIN_YEARMONTH, BEGIN_DAY, BEGIN_TIME, END_YEARMONTH, END_DAY, EN...
    lgl  (3): MAGNITUDE_TYPE, FLOOD_CAUSE, CATEGORY

    ℹ Use `spec()` to retrieve the full column specification for this data.
    ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

**Task 2:** *Pick two variables from either dataset that use different
levels of measurement (nominal, ordinal, interval or ratio). Explain
which level best describes each variable and why. Also identify one
variable that would be more efficiently read as a factor, and explain
why.* \# The variable ‘state’ in (data/SVI_2022_US_county.csv) is a
nominal level of measurement. States are simply names for geographic
locations in the US. They can not be ordered by anything measurable. \#
The variable “sq miles” is a form of ratio data. There is order to it
and it has a beginning of 0. You can multiply or divide it to give a
ratio of size between the different counties.(it is the sq miles of
counties) \##Factors are used to store character data with predetermined
values. The “county” or “state” variables would be good options. They
both have set geographical locations bound by man’s choice of
state/county line. A factor would make summarizing these data sets
easier for analysis later.

## Filtering and/or summarizing the data

Open up the tornado data frame so you can look at it. There’s multiple
variables here related to the timing of the tornado, its location, its
magnitude, and the number of injuries and fatalities. You can open the
“Storm-Data-Bulk-csv_Format” pdf in the data folder to learn more about
them.

For this part of the lab, you want to count the number of tornadoes
rated F3 or higher (the `TOR_F_SCALE` variable) within each county (the
`cty_fips` and `CZ_NAME` variables). Your resulting data frame should
have one row per county and state combination, with a variable that
stores the total number of severe tornadoes.

To do so, you’ll need to:

1.  Filter the `TOR_F_SCALE` variable to only include observations rated
    F3, F4, or F5.
2.  Use `group_by()` to group the data by the county and state fips
    codes and names (`STATE_FIPS`, `STATE`, `cty_fips`, and `CZ_NAME`).
3.  Use `summarise()` to count the number of these severe tornadoes
    within these groups.

**Task 3:** *Filter the storm data to include tornadoes rated F3 or
higher and then count the number of events by county and state.*

``` r
# Your code goes here. 

Filtered_Tornado_Data<-Tornados%>%filter(TOR_F_SCALE%in%c("F3","F4","F5"))

Filtered_Tornado_Data<-Filtered_Tornado_Data%>%
  group_by(STATE_FIPS,STATE,cty_fips,CZ_NAME) %>%
  summarize(tornado_count=n())
```

    `summarise()` has regrouped the output.
    ℹ Summaries were computed grouped by STATE_FIPS, STATE, cty_fips, and CZ_NAME.
    ℹ Output is grouped by STATE_FIPS, STATE, and cty_fips.
    ℹ Use `summarise(.groups = "drop_last")` to silence this message.
    ℹ Use `summarise(.by = c(STATE_FIPS, STATE, cty_fips, CZ_NAME))` for
      per-operation grouping (`?dplyr::dplyr_by`) instead.

``` r
#Final Code Line
Filtered_Tornado_Data<-Tornados%>%
  filter(TOR_F_SCALE%in%c("F3","F4","F5"))%>%
  group_by(STATE_FIPS,STATE,cty_fips,CZ_NAME)%>%
  summarize(tornado_count=n())
```

    `summarise()` has regrouped the output.
    ℹ Summaries were computed grouped by STATE_FIPS, STATE, cty_fips, and CZ_NAME.
    ℹ Output is grouped by STATE_FIPS, STATE, and cty_fips.
    ℹ Use `summarise(.groups = "drop_last")` to silence this message.
    ℹ Use `summarise(.by = c(STATE_FIPS, STATE, cty_fips, CZ_NAME))` for
      per-operation grouping (`?dplyr::dplyr_by`) instead.

You also want to filter the social vulnerability data so that it only
includes counties with a population over 25,000 people. This allows us
to only track counties with a moderate or large population. Use the SVI
Documentation pdf in the data folder to determine which variable this
is, recognizing that you want the **estimate** and not the margin of
error (MOE).

Filter the data below.

**Task 4:** *Filter the SVI data to include counties with a population
of 25,000 or more.*

``` r
# Your code goes here.
Filtered_SVI_Data<-SVI_Data%>%
  filter(E_TOTPOP>=25000)
```

Let’s add one more filter based on social vulnerability. When you look
at the documentation there are a number of “dummy” variables (0/1) that
flag counties that are at or above the 90th percentile for a number of
factors. For example, `F_DISABL` flags counties in the top 10% for
percentage of persons with a disability. There are also cumulative
flags: `F_THEME2` sums all the flagged variables within the “Household
Characteristics” category.

Pick one of those flags. It could be for a specific variable or a
cumulative one. Your selected flag should have a plausible connection to
tornado preparedness, evacuation, sheltering, recovery, or access to
warnings.

Filter the dataset you created in Task 4 so that it only includes
counties you are considering vulnerable. Then list that flagged variable
and explain how you chose it and (if the flag is cumulative) how you
picked a numeric cutoff.

**Task 5:** *Filter the data from Task 4 to only vulnerable counties and
explain your decision.*

``` r
Filter2_SVI_Data<-Filtered_SVI_Data%>%
  filter(E_TOTPOP>=25000&F_POV150==1)

# Your code goes here.
```

Describe your variable, why you chose it, and (if applicable) why you
chose the cutoff you did. \#I chose a variable without a cutoff. I did
F_POV150. It represents that the population of a county is in the 90th
percentile of people who are greater or equal to 150% of the poverty
line. I filtered it for counties above 25000 people who meet the
threshold. These comunities will have peoples with acess to
signifigantly less resources than richer counties. They are at more risk
to being forced to brave the storm and have less money to buy
goods/repairs after. When money is less of an issue, evacuation,
preperations, and transportation are all easier.

## Connecting the data

Next you’ll need to join these tornado counts to SVI population data
using the `inner_join()` function. To do so you’ll need to have two
fields with the same name in each dataset. The FIPS codes are in both
datasets, but they have different names (`FIPS` and `cty_fips`). The
following code creates a new variable called `cty_fips` in the SVI data
using `rename()`.

**NOTE:** You may need to change the name of the population data below
to match the object you used for the SVI data.

``` r
SVI_Data3<-Filter2_SVI_Data%>%
  rename(cty_fips=FIPS)
```

Call the function above to rename your data. Now you’re ready to join
the data.

**Task 6:** *Use `inner_join()` to connect the filtered tornado data to
your county data from Task 5.*

``` r
Joined_SVI_Tornado<-inner_join(SVI_Data3,Filtered_Tornado_Data,by="cty_fips")

# Your code goes here.
```

**Task 7:** *Find some documentation on the `inner_join()` function
online or using help in R. Describe how it works, and explain how the
results would have been different if you used `full_join()` instead.*

{Reference:https://r4ds.hadley.nz/joins.html} The main difference
between inner_join() and full_join is how they handle columns and rows
(x and y). Inner_join combines rows based on matching values that have
the same name. That is why we needed to rename the SVI data to
“cty_fips” so the data could be combined on one column. Full_join would
combine both data sets fully without matching the “cty_fips”. Full_join
keeps all data set and includes non matching data. Inner_join does not
have the na’s.

## Answering your research question

**Task 8:** *Open up your joined dataset. Which three vulnerable
counties have the highest number of severe tornadoes and how many were
in each? Are there cities or other notable geographic features located
in these counties?*

Natchitoches Parish, Louisiana; Leflore County Missippi; and Copiah
County, Missippi; topped the charts for poverty stricken counties with
f3-f5 tornadoes. All 3 had 7 severe storms. Natchitoches Parish has
Louisiana’s only national forest. Natchitoches is the main town of the
county/parish and reminds me of the look of Savannah. It is historical
but not too densely populated. Leflore county is extremely flat making
it more vulnerable to severe storms. Its “county seat” is Greenwood, but
the are is mainly rural agriculture. Leflore/Greenwood are named after
an extremely prominent plantation/slave owner for its history. Copia
county is pretty unremarkable. It is part of the Jacksonville
metropolitan and does not have any high population towns. The Natchez
Trace corridor runs partly across the county. It is like an Appalachian
Trail in Mississippi stretching 444 miles. Hazelhurst, the county seat,
was the birthplace of Robert Johnson (“founder of rock&roll).

## Challenge question

Find a peer-reviewed article published in the last ten years that uses
this NWS/NOAA tornado dataset. What research question was it trying to
answer? What methods were used? What are the most notable findings? Give
the full citation and a summary of at least 100 words below.

“Na-Yemeh, Dolly Y., et al. ”Assessing the impacts of a weather decision
support system for Oklahoma public safety officials.” Weather, Climate,
and Society 14.2 (2022): 597-608.”

The article focused on Oklahoma’s weather alert system, “Oklahoma’s
First-response Information Resource System using Telecommunications”
(OK-First). It aimed to find out whether the system had actually reduced
the amount of damage, suffering, and response time for Oklahomans. They
criticized that other studies had used only survey data. They focused on
“using surveys, Google Analytics, and NOAA’s Storm Events Database”.
They wanted to answer 5 questions using this data. In OK-First, what
tools/products actually help, are public officers using it, what occurs
with geographic variation, how do public officials decide to save
lives/property, and how can we quantify the impact of decision systems.
They performed an anonymous survey of all 812 active users that focused
on its effectiveness and help in decision making. NOAA was used to
compare OK-First data. Google Analytics was used to see how often
OK-First was used. The researchers found in the survey that 75% of users
found OK-First very valuable. The 812 users estimated saving Oklahoma
1.2 million with the service over a year. The researchers conclude that
OK-First provides valuable foresight for public officials to make
decisions to protect the public. Other states could follow to save money
via preparedness.

Write your response here.

## Final submission stuff

**Disclosure of assistance:** Besides class materials, what other
sources of assistance did you use while completing this lab? These can
include input from classmates, relevant material identified through web
searches (e.g., Stack Overflow), or assistance from ChatGPT or other AI
tools. How did these sources support your own learning in completing
this lab?

Write your response here.

I used Chatgpt to help me form a baseline for typing out a lot of the
code and helping me learn information about the counties I filtered out
for question 7. I originally tried
Filtered_Tornado_Data\<-Tornados%\>%filter(TOR_F_SCALE==“F3”,“F4”,“F5”))
at first for 3. Chatgpt fixed it.I now understand == turns it into a
true/false which can not run. For filtering the SVI2, it helped me by
telling me all I needed was an “&” between the data frames. I could not
get it to work with \| (what I understand to now be an “or”
function).You probably can do it that way, but it would not be the most
efficient.

**Lab reflection:** How do you feel about the work you did on this lab?
Was it easy, moderate, or hard? What are the biggest things you learned
by completing it?

I am giving it a moderate/hard, because it is my first coding
assignment. I have a better understanding of manipulating the actual
objects/data now. I got tripped up for a while on question 6 just
because of getting the correct filtered data. I had to go back in 3/4
times to use the correct data that had the poverty and population\>25000
data correctly. Renaming the FIPS to cty_fips also made me sweat for
some reason. I am scared the whole time I am going to delete a
data/object and have to start over. The third question we cleared up in
class today(Friday) as well. I think I will survive though. The main
thing I improved on was naming objects and keeping previous objects
correct. I could have easily jumbled everything together and had to
restart at some point. Managing my files is still holding me back
though. Keeping things tidy is not my strong suit. I also just realized
I miss-spelled tornadoes but I am not messing around with that.

That’s it! When you’re done with this lab, use the Render command in
Quarto to create a GitHub markdown document. Then push it to GitHub
using the procedure outlined in this week’s videos.
