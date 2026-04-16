# Population vs Sample

1. All observations that ever existed or will exist is a **population**
2. A portion of a population used for analysis is a **random sample**
3. Expected value of the population is the **population mean** $\mu$
4. The average of the sample is the sample **mean** $\bar{x}$
5. Spread of a population is the **variance** $\sigma^2$
6. Spread of a sample is the **sample variance** $s^2$

  

  

1. for a population of finite size, N,
    
    $\mu = \frac{\sum^{N}_{i = 1} X_i}{N}$
    
      
    
2. for a sample of size n,
    
    $\bar{x} = \frac{\sum^{n}{i = 1} x_i}{n}$
    

  

1. Variances:
    1. Population: $\frac{\sum^{N}_{i=1}(\mu - X_i)^2}{N}$
    2. Sample: $\frac{\sum^{n}_{i=1}(\bar{x} - x_i)^2}{n-1}$

  

1. The n-1 is called the degrees of freedom. It compensates for the error in the formula for sample estimation of variance. Why? the population variance is based on data deviations from the population mean. But the sample variance is deviations from the sample’s mean.

  

  

# Histograms

1. Bar chart of data that is gathered into values, or intervals.
2. The values and intervals are referred to as bins.
3. Shape can tell us the probability distribution the data are from.
4. Rule of thumb:
    - The number of bins in a histogram should be close to the square root of the sample size n
    - Width of each bin should be same (mostly)

  

1. If a histogram has a left tail (negative)
2. If a histogram is symmetric (Normal)
3. if a histogram has a right tail (positively skewed)

  

1. Y axis is always frequency

  

# Pareto Charts

1. Also a bar chart but starts with the largest bin and goes in decreasing order of bins.
2. Used when data is categorical