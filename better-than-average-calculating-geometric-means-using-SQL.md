# Better than Average: Calculating Geometric Means Using SQL


# Abstract

Geometric means are a robust and precise way to visualize the central tendency of a data set, particularly when examining skewed data or comparing ratios. Measures of central tendency are predominantly presented as arithmetic means or medians that are relatively simple to calculate and interpret, but may be inaccurate in representing data that are not _strictly normal_. Geometric means represent the best of both worlds, providing estimates that take into account all the observations in a data set without being influenced by the extremes. 

While, most databases provide a function to calculate the Arthimetic Mean, none of them provide a function to calcualte the Geometric Mean. We will look at how to calculate Geometric Mean using SQL.

# Introduction

## What is a geometric mean?
Geometric means are a type of _average_, or _measure of central tendency_ in a distribution of data points, in the same group as the median, mode, or arithmetic mean. Whereas the arithmetic mean is calculated by summing a series of data points and then dividing that sum by the number of data points, the geometric mean multiplies a series of data points, and then uses the n number of data points to find the nth root of that product. Mathematically, the geometric mean adds depth and stability to the mean.

|![Untitled drawing(26)](https://github.com/structured-query-language/structured-query-language.github.io/assets/121721444/781ddbbc-7fe0-4e8f-85d7-a3385e0b886d)|
|:--:|
|_Figure 1._ Imagine a rectangle that has two sides of 5 and 20. The area equals 100. The geometric mean tells you the size of the square (which must have equal sides) that produces the same area as the rectangle. For this example, a square with equal sizes of 10 produces the same area as the 5 X 20 rectangle.|



## When should I use the geometric mean instead of the arithmetic mean?
There are no hard rules for which mean you should use. Different types of averages can be used to express slightly different concepts: the center of the data, the values most often seen, and/or the typical _expected_ values may or may not all be conveyed by the same measure. Data is rarely perfect, and you may need to look at several different types of averages to decide what works best for what you are trying to communicate with your data. But in general, geometric means are preferable when looking at skewed data, scaled data, or when averaging ratios. 

|![Untitled drawing(21)](https://github.com/structured-query-language/structured-query-language.github.io/assets/121721444/dc31bc9e-69e1-4c57-972f-997e6e7b97c4)|
|:--:|
|_Figure 2._ If you have a skewed distribution as above, Geometric Mean may be a better measure of central tendency than the Arithematic Mean.|


### Illustrative Example

Let's take the pay rate for employees in an organization. Most of the Individual Contributors earn less than 200K. The CFO, VP and Director skew the dataset.

|![Copy of Arithematic Mean vs  Geometric Mean vs  Harmonic Mean](https://github.com/structured-query-language/structured-query-language.github.io/assets/121721444/e03d5db0-16c8-42fb-b1c5-1548a4bea38b)|
|:--:|
|_Figure 3._ Comparison of Means|

In the above example the Arthimetic Mean is _pulled_ towards the higher pay rates. Notice that the Average (arithemetic mean) is 266, whereas _most employees earn less than 200_. Geometric mean, 196, might be a better central measure for this dataset, as it will consider all of the data points, but without being subject to the same _pull_ that can deteriorate the interpretation of the arithmetic mean (Figure 3).

### Geometric Mean Calculation using SQL

```sql
select EXP(SUM(LN(pay))/COUNT(pay)) from employee;
```

Alternatively if you are using Google BigQuery, you can create [User Defined Aggregate Function](UDAF-in-google-bigquery.md) for Geometric Mean as following:

```sql
CREATE TEMP AGGREGATE FUNCTION geometric_mean(
  column_values float64
)
RETURNS float64
AS
(
  EXP(SUM(LN(column_values))/COUNT(column_values))
);

with test_data as (
  SELECT 1 AS col1 
  UNION ALL
  SELECT 3
  UNION ALL
  SELECT 5
)
select geometric_mean(col1) from test_data;
```


  

## Considerations

The calculation of the geometric mean requires that all values are non-zero and positive. So what should you do if you have data that do not meet this requirement? If you have values that equal zero, you have a few options:

1. Adjust your scale so that you add 1 to every number in the data set, and then subtract 1 from the resulting geometric mean.
2. Ignore zeros or missing data in your calculations.
3. Convert zeros to a very small number (often called “below the detection limit”) that is less than the next smallest number in the data set.

If you have negative numbers, you will need to convert those numbers to a positive value before calculating the geometric mean. You can then assign the resulting geometric mean a negative value. If your data set contains both positive and negative values, you will have to separate them and find the geometric means for each group, and you can then find the weighted average of their individual geometric means to find the total geometric mean for the full data set.


# Conclusion
If you are working with non-normal data, you should consider using the geometric mean as the measure of central tendency for your data. The geometric mean is a more robust and accurate way to find your average or expected value for data that is skewed, scaled, or proportional.
