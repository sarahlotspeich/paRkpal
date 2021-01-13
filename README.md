---
title: "paRkpal: for lovers of data viz & national parks"
author: Sarah C. Lotspeich, Bridget N. Chalifour
output:
  html_document:
    toc: no
---

This package contains color palettes for R data visualization that are inspired by the United States National Parks. We are just two PhD candidates who love pretty graphs and the outdoors. We are in no way associated with the National Park Service (unfortunately). 

## Install
To install the package, run the following in your `R` console: `devtools::install_github("sarahlotspeich/paRkpal")`. 

## Available parks & palettes 

![](images/paRkpal_options.png)

# Examples
## Most-Visited US National Parks (2019)

We demonstrate use of the `paRkpal` package using the `NationalParkVisits_2019` data.

```{r}
# Run once: devtools::install_github("sarahlotspeich/paRkpal")`
library(paRkpal)`
library(ggplot2)

visits <- read.csv("https://raw.githubusercontent.com/sarahlotspeich/nationalpaRkpalettes/master/data/NationalParkVisits_2019.csv")

top10 <- visits[visits$Rank <= 10, ] # Subset to 10 most-visited parks 
top10$ParkName <- factor(top10$ParkName, levels = top10$ParkName)`

ggplot(data = top10) + 
  geom_bar(aes(x = ParkName, y = Value, fill = ParkName), stat = "identity") + 
  scale_fill_manual(values = park_palette("olympic"), guide = F) + 
  xlab("National Park") + ylab("Number of Visitors") + 
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 30, hjust = 1))
```

![Top 10 Most-Visited US National Parks (2019)](images/ParkVisitors2019.png)

We can also use this visitor data to create a pie chart showing visitation percentage in the top 10 most visited parks.

```{r}
top10 <- visits[visits$Rank <= 10, ] # Subset to 10 most-visited parks

top10Parks <- factor(top10$ParkName, levels = top10$ParkName)

# Create pie chart
pie(visits$PercentOfTotal[top10Parks], labels = visits$ParkName[top10Parks], main="Visitation Percentage in Top Ten Most Popular Parks", col=park_palette("smoky_mountains1"))
```

![Top 10 Most-Visited US National Parks (2019)](images/Pie_Chart.png)

Data source: https://irma.nps.gov/Stats/SSRSReports/National%20Reports/Annual%20Park%20Ranking%20Report%20(1979%20-%20Last%20Calendar%20Year)


## Annual Visits to the Five Oldest National Parks (1904--2016)

The five oldest parks, with data going all the way back to 1904, are Hot Springs, Yellowstone, Wind Cave, Crater Lake, and Kings Canyon. We read in the `ParkAttendance_1904_2016` data and subset to these parks.

```{r}
read.csv("https://raw.githubusercontent.com/sarahlotspeich/paRkpal/master/data/ParkAttendance_1904_2016.csv", stringsAsFactors = F) %>% 
  dplyr::filter(Unit.Name %in% c("Hot Springs National Park", "Yellowstone National Park",
                                 "Wind Cave National Park", "Crater Lake National Park",
                                 "Kings Canyon National Park")) -> oldest5

oldest5 %>% 
  ggplot(aes(x = Year, y = as.numeric(Visitors), fill = Unit.Name)) + 
  geom_bar(stat = "identity") +
  scale_fill_manual(values = park_palette("yellowstone"), name = "") + 
  xlab("Year") + ylab("Number of Visitors") + 
  theme_minimal() + theme(legend.position = "top")
```

![Year-on-Year Visitor Counts at the Oldest National Parks](images/oldest5.png)

Data source: https://data.world/garyhoov/national-parks-sorted-by-total-attendance-and-by-year


## Animal Species Recorded in the National Park System

The National Park Service publishes a database of plant and animal species identified and verified to be present in indiviual national parks. These records are made available to the public on the National Park Species Portal (though exceptions are made for threatened species). Lots of interesting metadata can be found in these records, including categories of species found in parks, taxonomic rankings, scientific and common names, nativeness, etc. We read in the entirety of this available data to show what categories of animals make up a larger proportion of recordings in different parks.

```{r}
species <- read.csv("https://raw.githubusercontent.com/brchalifour/paRkpal/master/data/species.csv")

speciesTally <- species %>% group_by(Park_Name, Category) %>% tally()

animalTally <- speciesTally %>% dplyr::filter(!(Category %in% c("Vascular Plant", "Nonvascular Plant", "Algae", "Fungi")))
                      
ggplot(animalTally, aes(fill=Category, y=n, x=Park_Name)) + 
  geom_bar(stat="identity", position = "fill") +
  scale_fill_manual(values = park_palette("death_valley")) +
  theme_minimal() +
  labs(y="Proportion of Species Recorded", x="Category") +
  theme(axis.title.x = element_blank()) +
  theme(legend.text=element_text(size=12), legend.title=element_text(size=14,face="bold")) +
  theme(axis.text=element_text(size=10),
        axis.title=element_text(size=14,face="bold")) +
  theme(axis.title.y = element_text(margin = margin(t = 0, r = 20, b = 0, l = 0))) +
  theme(axis.text.x = element_text(angle = 90, hjust = 1))
```

![Stacked Bar Plot of Recorded Animal Species Categories](images/Stacked_Bar_Plot.png)

Data source: https://irma.nps.gov/NPSpecies

## Average Sepal Length in Three Iris Species

We can use the built-in iris dataset in R to look at average sepal length among three species of irises (Iris setosa, versicolor, and virginica). Our paRkpals look great even when you only need to use a few colors!

```{r}
# Get the mean sepal length of each iris species
Mean.sepal <- aggregate(Sepal.Length ~ Species, data = iris, FUN = mean)
head(Mean.sepal)

# Get the standard deviation of each iris species
SD.sepal <- aggregate(Sepal.Length ~ Species, data = iris, FUN = sd)
head(SD.sepal)

# Plot the data as a stripchart
{stripchart(Sepal.Length ~ Species, data = iris, method = "jitter", 
            vertical = TRUE, pch = 16, col = park_palette("mount_rainier2"),
            ylab = "Sepal Length (in cm)", ylim = c(2, 8), xlab = "Iris Species",
            cex.axis = 0.8,
            main = "Sepal Length in Three Iris Species")
  
  segments(x0 = c(1:3) , x1 = c(1:3) ,
           y0 = Mean.sepal$Sepal.Length-SD.sepal$Sepal.Length,
           y1 = Mean.sepal$Sepal.Length+SD.sepal$Sepal.Length)
  
  points(Mean.sepal$Sepal.Length ~ c(1:3), pch = 16, cex = 1.2)}
```

![Stripchart of Average Sepal Length in Three Irises](images/Stripchart.png)

Data Source: Fisher, R. A. (1936) The use of multiple measurements in taxonomic problems. Annals of Eugenics, 7, Part II, 179–188.

### So, where will your data take you?
