---
title: "Amazon Sales Data Analysis"
author: "Akanksha Panwar"
date: "2024-08-11"
output:
  html_document:
---

# Business Task:

Sales management has gained importance to meet increasing competition and the
need for improved methods of distribution to reduce cost and to increase profits. Sales
management today is the most important function in a commercial and business
enterprise.

The dataset provided contains sales data from the year 2010 to 2017

I am required to do my own research and come up with my own findings.

# Preparing Data

## Loading Packages
```{r}
library(tidyverse)
library(janitor)
library(reshape2)
```

## Importing Datasets
```{r}
amazon_sales <- read_csv("Amazon Sales data.csv")
```

# Cleaning Process:

- Cleaning upthe column names and correct formats for columns
- Change formats for sales_channel and order_priority columns from chr to factor
```{r}
glimpse(amazon_sales)
amazon_sales <- clean_names(amazon_sales)
amazon_sales$order_date <- as.POSIXct(amazon_sales$order_date, format="%m/%d/%Y", tz=Sys.timezone())
amazon_sales$order_date <- as.Date(amazon_sales$order_date, "%d/%m/%Y", tz=Sys.timezone())
amazon_sales$ship_date <- as.POSIXct(amazon_sales$ship_date, format="%m/%d/%Y", tz=Sys.timezone())
amazon_sales$ship_date <- as.Date(amazon_sales$ship_date, "%d/%m/%Y", tz=Sys.timezone())

amazon_sales$sales_channel <- as.factor(amazon_sales$sales_channel)
amazon_sales$order_priority <- as.factor(amazon_sales$order_priority)
unique(amazon_sales$order_priority)
```
![glimpse](https://github.com/user-attachments/assets/06be16fc-64d4-4326-ad5d-fd12e1a48f8d)

Checking if there are any incomplete cases or missing data
```{r}
amazon_sales %>% filter(!complete.cases(.))
```
![incomplete cases](https://github.com/user-attachments/assets/5ad432ed-5cae-40d9-93ee-27ad9120a9f9)

Checking duplicated data 
```{r}
amazon_sales[duplicated(amazon_sales), ]
```
![duplicate data](https://github.com/user-attachments/assets/49a1ccb2-3447-420b-92fe-aacff7af5249)

# Analysing Data:

Reorder dataset according to order_date
```{r}
amazon_sales <- arrange(amazon_sales, order_date)
```

```{r}
amazon_sales %>% select(units_sold, unit_price, unit_cost, total_revenue, total_cost, total_profit) %>% summary()

n_distinct(amazon_sales$country)
n_distinct(amazon_sales$item_type)
```
![summary and ndistinct](https://github.com/user-attachments/assets/9af1f335-ed82-4a6a-ab4b-b8d6650e5b09)

- Amazon has shipped to 76 countries and the the items sold are categorized into 12 different types.

```{r}
amazon_sales$days_before_shipped <- amazon_sales$ship_date - amazon_sales$order_date
amazon_sales$days_before_shipped <- as.numeric(amazon_sales$days_before_shipped)
amazon_sales %>% select(days_before_shipped) %>% summary()
```
![days before ship summary](https://github.com/user-attachments/assets/b0aa244e-1d63-45ee-9a31-5b6e4e8da048)

- Amazon took 23 days on an average to ship an item

## Creating visualizations

```{r}
amazon_sales %>% ggplot(aes(region, units_sold)
                     )+
     geom_col(fill = "#97B3C6")+
     facet_wrap(~sales_channel)+
     coord_flip()+
     labs(title = "Units sold per region",
        x = "Region",
        y = "Units Sold")
```
![Units Sold per region](https://github.com/user-attachments/assets/60a58903-1cda-4978-b367-ca680481596b)

```{r}
amazon_sales %>% ggplot(aes(order_date, total_profit/units_sold,
                             colour = sales_channel))+
     geom_point(size = 3, alpha = 0.5)+
     geom_smooth(method = lm, se = F)+
     facet_wrap(~region)+
     labs(title = "Profit over the years", x = "Year", y = "Average Profit")+
     theme_bw()
```
![Profit over the years](https://github.com/user-attachments/assets/018b743f-08cf-4a28-9eb3-84c1a678e2a4)

```{r}
amazon_sales %>% ggplot(aes(days_before_shipped, region))+
     geom_boxplot()+
     geom_point(alpha = 0.5, aes(colour = item_type, size = unit_price))+
     theme_light()+
     labs(title = "Shipping time", x = "Days before items are shipped", y = "Region")
```
![Shipping time](https://github.com/user-attachments/assets/597e3a3f-ffc4-4d9b-84f1-cad489ef5e80)


# Conclusion

* According to the Graphs above, we can see that majority of offline and online sales have been done to Sub Saharan Africa, while offline sales conducted are least in Middle East and North Africa and online sales conducted are least in Central America and the Carribean.
* Over the years, average profit from offline sales has seemed to increase in Asia, Central America and the Carribean, North America and Middle East and North Africa.
* Average profit from online sales has seen slight increase in only Europe.
* The third graph shows, that items are usually shipped quickest to Sub-Saharan Africa and Europe and take slightly longer for the rest of the regions.
* The most expensive item types seem to be Household, meat and office supplies, while least costly item types are beverages, snacks and cereals.

### Thank you for reading!
