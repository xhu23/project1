README
================
Xinyu Hu
6/9/2020

# Before Everything

This is a project that go through the pipeline from extracting data from
public API, to processing in the RStudio, and to upload directly to the
Github as README. Data was in JSON format when imported, which provides
a great learning opportunity for JSON files; The output was directly
push to the Github, which is also a great practice of what we learned in
the class. It’s an interesting and steep learning curve, and I love it
quite a bit.

# Describe JSON

## What is Jason data?

Based on Wikipedia, [JSON (Jave Script Object
Notation)](https://en.wikipedia.org/wiki/JSON) is an open standard
lightweight file format for data interchange. It uses human-readable
text to store and transmit data objects. It is a very common data
format, with a diverse range of applications.Data that follows JSON
format is JSON data.

## Where does it get used?

According to
[tutorialspoint](https://www.tutorialspoint.com/json/json_quick_guide.htm#:~:text=JSON%20format%20is%20used%20for,used%20with%20modern%20programming%20languages.),
it is used while writing JavaScript based applications that includes
browser extensions and websites. For example,transmit data between a
server and web applications;Web services and APIs use JSON format to
provide public data;serializing and transmitting structured data over
network connection;used with modern programming languages.

## Why it is a good way to store data?

Firstly, it is light-weighted. So before JSON, web services used XML as
primary data format for transmitting, but after JSON appeard, it became
the preferred formate. Secondly, JSON is easier for both humans and
machines to understand, since its syntax is minimal and its structure is
predictable. JSON was meant to carry structured information between
programs from the very beginning. Plus, the nature of “human readable”
also play a strong role that make JSON a good way to store data, which
makes JSON not as confusing as XML. For more detail, feel free to check
out [The Rise and Rise of
JSON](https://twobithistory.org/2017/09/21/the-rise-and-rise-of-json.html)
and [JSON.ORG](https://www.json.org/json-en.html).

# Discuss JSON R-Packages

There are 3 packages in R that work with JSON file. They are
[rjson](https://cran.r-project.org/web/packages/rjson/rjson.pdf),
[jsonlite](https://cran.r-project.org/web/packages/jsonlite/jsonlite.pdf)
and
[RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/RJSONIO.pdf).
Also, there are package like
[tidyjson](https://cran.r-project.org/web/packages/tidyjson/vignettes/introduction-to-tidyjson.html)
that also can serve similar functionality. `rjsonio` allows R objects to
be inserted into Javascript/ECMAScript/ActionScript code and allows R
programmers to read and convert JSON content to R objects. `rjson`’s
performance is now similar to this package, and perhaps slightly faster
in some cases. `jsonlite` Started out as a fork of `rjsonio`, but has
been completely rewritten in recent versions. The package offers
flexible, robust, high performance tools for working with JSON in R and
is particularly powerful for building pipelines and interacting with a
web API.

For this project, I choose `jsonlite` as it is the most familiar JSON
package to me. Also, its fast speed is appealing.

# Read-in JSON data

This is my step by step process to load in data from [NHL Franchise
API](https://gitlab.com/dword4/nhlapi/-/blob/master/records-api.md).  
Step1: Load library

``` r
library("httr")
library("jsonlite")
```

Step2: Prepare Parent URL

``` r
full_url <- "https://records.nhl.com/site/api"
```

Step3: Obtain Franchise data

``` r
franchise <- fromJSON(paste(full_url,"/franchise",sep=""))
franchise <- franchise[[1]]
```

Step4: Obtain Franchise team
totals

``` r
franchise_team_totals <- fromJSON(paste(full_url,"/franchise-team-totals",sep=""))
franchise_team_totals <- franchise_team_totals[[1]]
```

Step5: Get the season record, goalie record and skater record for
Franchise 1. Using this step, we can obtain the data columns from those
3 tables provided by the
API.

``` r
season_records_all <- fromJSON(paste(full_url,"/franchise-season-records?cayenneExp=franchiseId=1",sep=""))
season_records_all <- season_records_all[[1]]

goalie_records_all <- fromJSON(paste(full_url,"/franchise-goalie-records?cayenneExp=franchiseId=1",sep=""))
goalie_records_all <- goalie_records_all[[1]]

skater_records_all <- fromJSON(paste(full_url,"/franchise-skater-records?cayenneExp=franchiseId=1",sep=""))
skater_records_all <- skater_records_all[[1]]
```

Step6: loop through all the franchise (2 to 38), to obtain the season
record, goalie record and skater record.

``` r
for (i in 2:38){
  # extract data from api
  season_records <- fromJSON(paste(full_url,"/franchise-season-records?cayenneExp=franchiseId=",i,sep=""))
  season_records <- season_records[[1]]
  
  goalie_records <- fromJSON(paste(full_url,"/franchise-goalie-records?cayenneExp=franchiseId=",i,sep=""))
  goalie_records <- goalie_records[[1]]
  
  skater_records <- fromJSON(paste(full_url,"/franchise-skater-records?cayenneExp=franchiseId=",i,sep=""))
  skater_records <- skater_records[[1]]
  # append to the giant table
  season_records_all <-rbind(season_records_all,season_records)
  goalie_records_all <-rbind(goalie_records_all,goalie_records)
  skater_records_all <-rbind(skater_records_all,skater_records)
  # remove temporary table after appending
  rm(season_records,goalie_records,skater_records) 
}
# Exploratory Analysis
```

<!-- ## Including Plots -->

<!-- You can also embed plots, for example: -->

<!-- ```{r pressure, echo=FALSE} -->

<!-- ``` -->

<!-- Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot. -->
