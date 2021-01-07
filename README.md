# nationalpaRkpalettes
This package contains color palettes for R data visualization that are inspired by the United States National Parks. 

### Install
To install the package, run the following in your `R` console: `devtools::install_github("sarahlotspeich/nationalpaRkpelettes")`.

### Example: Visualizing US National Park Visitors (2019)

We demonstrate use of the `nationalpaRkpalettes` package using the `NationalParkVisits_2019` data.

`devtools::install_github("sarahlotspeich/nationalpaRkpalettes")`
`library(nationalpaRkpalettes)`

`visits <- read.csv("https://raw.githubusercontent.com/sarahlotspeich/nationalpaRkpalettes/master/data/NationalParkVisits_2019.csv")`

`library(ggplot2)`

`top10 <- visits[visits$Rank <= 10, ]`

`top10$ParkName <- factor(top10$ParkName, levels = top10$ParkName)`

`ggplot(data = top10) + 
  geom_bar(aes(x = ParkName, y = Value, fill = ParkName), stat = "identity") + 
  scale_fill_manual(values = park_palette("olympic"), guide = F) + 
  xlab("National Park") + ylab("Number of Visitors") + 
  ggtitle("Top 10 Most-Visited US National Parks (2019)") + theme_minimal() + 
  theme(axis.text.x = element_text(angle = 30, hjust = 1))`

Data source: https://irma.nps.gov/Stats/SSRSReports/National%20Reports/Annual%20Park%20Ranking%20Report%20(1979%20-%20Last%20Calendar%20Year)
