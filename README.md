---
title: "DHS Immigration Statistics"
output: html_document
---

Loading necessary libraries below:

```{r}
library(readxl)
library(dplyr)
library(tidyr)
library(ggplot2)
```

First, needed to download data table from https://www.dhs.gov/immigration-statistics/lawful-permanent-residents
https://www.dhs.gov/sites/default/files/publications/YRBK%202016%20LPR%20Excel%20Final_0.zip
https://www.dhs.gov/sites/default/files/publications/LPR2006_0.zip

Second, had to adjust data tables manually in excel to make sure column names were in the same order and had the same number of 
rows/columns. If row names weren't in the same order after merging, merging won't match up properly and there will be a presumed 
loss of data.

Third, uploaded excel files into 'R' to be read.

```{r}
tableSix_1997to2006 <- read_excel(file.choose(), na ="D", skip = 3, col_names = T)
tableSix_2007to2016 <- read_excel(file.choose(), na ="D", skip = 3,col_names = T)

```

Merging two datatables, (1997 to 2006) and (2007 to 2016) together by row.names to avoid duplicates.

```{r}
combo_data <- merge.data.frame(x = tableSix_1997to2006, y = tableSix_2007to2016, by = "row.names", sort = F, no.dups = T)
```

Changing data table format from 'wide' to 'long'.

```{r}
long <- gather(combo_data, year, value, "1997":"2016")

```

Using lapply function to change all -'s dashes in the dataset to 0s (only years 2007-2016 for the "Asylees" row had dashes)

```{r}

long[2:4] <- lapply(long[2:4], function(col) as.character( gsub("-$|\\,", "0", col) ) )

```

Using lapply function to change the class for all the years from "character" to "numeric" , 3:4 representing column 3 through 4.

```{r}
ix <- 3:4
long[ix] <- lapply(long[ix], as.numeric)

```

Deleted 'row.names' colunm within the "long" dataframe.

```{r}
long <- long[ -c(1) ]
```

  
Filtered 'long' dataset to just include 'Total' for all years from 1997 to 2016.


```{r}
long_sub <- filter(long,  
      long$`Type and class of admission.x` %in% c("Total" ))
```
 
Created a test plot for only the 'Total' row for all years from 1997 to 2016.

```{r}
g1 <- ggplot(long_sub, aes(x = long_sub$year, y = long_sub$value)) +
  geom_col()

plot(g1)
```


