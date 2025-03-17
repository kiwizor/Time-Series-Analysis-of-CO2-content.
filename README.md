# Time-Series-Analysis-of-CO2-content.

---
title: "MTH6139 Time Series" 
subtitle: "CO2 Forecasing with Prophet" 
author: "Kevin Efovi" 
date: "Spring term 2025" 
output: 
  html_document:
    toc: true
    toc_float: true
    theme: spacelab 
    highlight: tango
---

## Section 1: Data Context and Disclaimers

In this project, We're going to be looking at Mauna Loa Atmospheric CO2 (Carbon Dioxide) concentration. The aim is to fit a forecasting model using the Prophet package and then analyze trends, seasonality, and overall growth in the data.

The format of the dataset is a time series of 468 observations; monthly from 1959 to 1997.

It should be noted that the values February, March and April of 1964 were missing and have been obtained by interpolating linearly between the values for January and May of 1964.

---



```{r}
library(prophet)

# Convert the CO2 time series to a data frame
co2.df = data.frame(
  ds=zoo::as.yearmon(time(co2)), # Convert time to friendlier 'year-month' format
  y=co2)

# Fit Prophet model to CO2 data
m = prophet::prophet(co2.df)

# Create a data frame for forecasting future dates (8 quarters)
f = prophet::make_future_dataframe(m, periods=8, freq="quarter")

# Finally generate the forecast using the model
p = predict(m, f)
plot(m,p)
```


## Section 2: Data Analysis

It should be noted that we have disabled daily and weekly seasonality since our data is recorded at a much coarser resolution, so that we don't over fit the model and find patterns that simply don't exist in the data.

The atmospheric CO2 content in ppm (parts per million) is rising over time, with clear, similar seasonal patterns year over year. The rate of increase appears to be linear with respect to time, so there isn't any evidence of an exponential effect happening on the atmpospheric CO2 content. 

By looking at the forecast of 8 quarters beyond the current data, we can see the same pattern occurring. The model has successfully captured the underlying pattern of the data, and it's seasonal fluctuations, as shown by the forecast. The reliability of this forecast is only as good as the reliability of the base dataset, so this should be taken into account in any future references to this model.

```{r}
# Perform a linear regression of CO2 values on time
model_lm <- lm(y ~ as.numeric(ds), data = co2.df)
summary(model_lm)
```

We have performed a linear regression of CO2 values on time to put some numbers to the data analysis.

- The slope estimate of 1.308 confirms that the CO2 content has been rising over time.

- Both the intercept and slope value have extremely small p-values, so the relationship between time and CO2 levels is statistically significant, and it's unlikely that the trend captured in our model was due to chance.

- The Multiple R-squared value of ~0.97 indicates that the model explains 97% of the variability in CO2 levels.

This simple linear regression was performed in addition to the time series analysis; It is evident that the linear regression does not capture the seasonal variations in the CO2 data set, whereas this was clear in the time series. Both models confirm an up trend in the atmospheric CO2 content over time.

## Section 3: Where to from here?

Some things that could be explored in future projects are:

- Increasing the granularity of the time to further analyse the seasonal fluctuations

- Use alternative forecasting methods, such as ARIMA or ML techniques, to see how their predictions differ

- Compare the CO2 data from multiple locations, and see if there is a geographical factor to the rising CO2 content
