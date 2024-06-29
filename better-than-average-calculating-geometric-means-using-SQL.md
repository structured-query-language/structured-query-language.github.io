# Better than Average: Calculating Geometric Means Using SQL


# Abstract

Geometric means are a robust and precise way to visualize the central tendency of a data set, particularly when examining skewed data or comparing ratios. Measures of central tendency are predominantly presented as arithmetic means or medians that are relatively simple to calculate and interpret, but may be inaccurate in representing data that are not _strictly normal_. Geometric means represent the best of both worlds, providing estimates that take into account all the observations in a data set without being influenced by the extremes. They can be employed by data analytics working in multiple industries including business, finance, health care, and research. Examples are varied and include examining
compounded interest rates or returns on investments, assessing population changes in longitudinal data, or investigating lognormal data such lab assay results, biological concentrations, or decay rates.

While, most databases provide a function to calculate the Arthimetic Mean, none of them provide a function to calcualte the Geometric Mean. We will look at how to calculate Geometric Mean using SQL.

# Introduction

## What is a geometric mean?
Geometric means are a type of _average_, or _measure of central tendency_ in a distribution of data points, in the same group as the median, mode, or arithmetic mean. Whereas the arithmetic mean is calculated by summing a series of data points and then dividing that sum by the number of data points, the geometric mean multiplies a series of data points, and then uses the n number of data points to find the nth root of that product. Mathematically, the geometric mean adds depth and stability to
the mean.

We can easily visualize the geometric mean when applying it to its counterpart, the geometric series of numbers, where each number increases from the previous number according to the same proportion. The geometric mean will lie in the direct center of the values, whereas the arithmetic mean would have been _pulled_ towards the higher values, and thus not truly represent the center of the data.

𝐴𝑟𝑖𝑡ℎ𝑚𝑒𝑡𝑖𝑐 𝑚𝑒𝑎𝑛 = 3+9+27+81+243
5 = 72.6 (See Equation 1)
𝐺𝑒𝑜𝑚𝑒𝑡𝑟𝑖𝑐 𝑚𝑒𝑎𝑛 = √3 ∗ 9 ∗ 27 ∗ 81 ∗ 243
5 = 27 (See Equation 2)

## When should I use the geometric mean instead of the arithmetic mean?
There are no hard rules for which mean you should use. Different types of averages can be used to express slightly different concepts: the center of the data, the values most often seen, and/or the typical "expected" values may or may not all be conveyed by the same measure. Data is rarely perfect, and you may need to look at several different types of averages to decide what works best for what you are trying to communicate with your data. But in general, geometric means are preferable when looking at skewed
data, scaled data, or when averaging ratios. Some common applications include:

* Population growth
* Compounding interest
* Bioassays
* Radioactive decay
* Dose-response relationships
* Count data
* Time Series data
* Longitudinal data
* Repeated measures data
* Bioequivalence trials

If your data involve rate changes or changes over time, your data may be skewed. Often these data have a lognormal distribution, and the geometric mean describes the center of lognormal data perfectly. In addition to skew, you should also consider the size of your sample. When working with small samples,
0
50
100
150
200
250
300
Arithmetic Mean
Geometric Mean
Figure 1: Geometric Series with Means Highlighted
3

the sensitivity of the arithmetic mean can be problematic. The geometric mean might be a better central measure, as it will consider all of the data points, but without being subject to the same “pull” that can deteriorate the interpretation of the arithmetic mean (Figure 1):

![Arithematic Mean vs  Geometric Mean vs  Harmonic Mean](https://github.com/structured-query-language/structured-query-language.github.io/assets/121721444/c99e2197-fa0a-431b-9cbe-eb997c3c74f7)
|:--:| 
|Figure 2: Comparison of Means for a Small Sample (McChesney, 2016)|

In the above case, you should first confirm that 90 is a valid data point and not an error. Trimming outliers, so long as it is justifiable, is another way to produce more stable mean calculations. Geometric means are also appropriate when summarizing ratios or percentages. This has many applications in medicine, and is considered the “gold standard” for calculating certain health measurements. In the financial industry, this concept is applied when constructing stock indexes and rates of return. The geometric mean is also employed in the art world, to choose aspect ratios film and video. The idea of comparing ratios is expanded when you look at scaled data: if you have data that have different attributes or scales, and you have normalized the results to be presented as ratios to reference values, the geometric mean is the correct mean to use.

## Considerations

The calculation of the geometric mean requires that all values are non-zero and positive. So what should you do if you have data that do not meet this requirement? If you have values that equal zero, you have a few options:

1. Adjust your scale so that you add 1 to every number in the data set, and then subtract 1 from the resulting geometric mean.
2. Ignore zeros or missing data in your calculations.
3. Convert zeros to a very small number (often called “below the detection limit”) that is less than the next smallest number in the data set.

If you have negative numbers, you will need to convert those numbers to a positive value before calculating the geometric mean. You can then assign the resulting geometric mean a negative value. If your data set contains both positive and negative values, you will have to separate them and find the geometric means for each group, and you can then find the weighted average of their individual geometric means to find the total geometric mean for the full data set.

If none of these options appeals to you, you are not alone! There is controversy among statisticians about what is the best method for dealing with these values. You may want to calculate several types of averages and decide what makes the most sense for you and the results you are trying to report.

# Conclusion
If you are working with non-normal data, you should consider using the geometric mean as the measure of central tendency for your data. The geometric mean is a more robust and accurate way to find your average or expected value for data that is skewed, scaled, or proportional.
