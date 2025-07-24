1. Figure out bounds on a particular population paramter

  

# Confidence intervals on $\mu$ (populations mean)

1. Can be two sided $\hat{L} < \mu < \hat{U}$
2. 1 sided lower $\hat{L} < \mu$
3. 1 sided upper $\mu < \hat{U}$

  

1. With what confidence can we say this though?
2. both L and U based on the n items (sample)
3. There is a probability of **1-**$\alpha$ that the CI will contain the true value of $\mu$

  

# $Z_{\alpha/2}$ and $Z_{\alpha}$

1. Z alpha/2 is the value of z on the normal cdf z axis (x axis) such that the area above it under the z pdf is equal to $\alpha/2$
2. Z $\alpha$ is the value of z on the normal cdf z axis (x axis) such that the area above it under the z pdf is equal to $\alpha$

![[image 65.png|image 65.png]]

  

1. How to find? invNrm(areabelow, mean, S.D)

  

  

# Confidence Interval Formulas on $\mu$ and $\sigma$ known

Two sided confidence interval

$P(\hat{L} < \mu < \hat{U}) = 1 - \alpha$

$L = \bar{x} - z_{\alpha/2} \cdot S.E$

$U = \bar{x} + z_{\alpha/2} \cdot S.E$

  

and for 1 sided intervals

$\hat{L} = \bar{x} - z_{\alpha} \cdot S.E$

$\hat{U} = \bar{x} + z_{\alpha} \cdot S.E$

  

Easier way to do this? Use Z interval test on calculator

  

# Sample size for a specified Error

1. Sample size what if I want my sample mean to be close to population mean
    
      
    
    $n = (\frac{z_{\frac{\alpha}{2}} \cdot \sigma}{E})^2$
    

where, E = will be given. = $| \mu - \bar{x}|$

1. Always round up.

  

  

# The T Distribution

Recall what Z becomes in normal cdf

  

We replace $\sigma$ by s, (\sigma is not known), the sample standard deviation, we a random variable T = $\frac{\bar{X} - \mu}{\frac{s}{\sqrt{n}}}$ with n - 1 degrees of freedom.

  

1. E(T) = E(Z) = 1
2. Standard deviation varies.
3. Need to know the interval in which t lies.
4. Must use tcdf/invT from calculator

![[image 1 17.png|image 1 17.png]]

  

Now why all this? what is t?

just like with z, we have t alpha and t alpha/2

that is calculated using invT()

  

which leads to the same formulas as above for the case where $\sigma$ **is not known**

ultimately we can just use test: T interval

  

  

# The $\chi^2$ (kai) Distribution (For Variance)

1. Chi square random variable, $\chi^2$ with n -1 df is
    
    1. $\chi^2_{n-1} = \frac{(n-1)S^2}{\sigma^2}$
    2. range: $\chi^2 > 0$
    3. Not symmetric like T or Z
    4. functions: chiCDF, invChi
    5. Then same as the above distributions but for variance.
    6. and then,
    
    for 2 sided confidence intervals
    
    $\hat{L} = \frac{(n-1)s^2}{\chi^2_{\frac{\alpha}{2}, \space\space n-1}}$
    
    $\hat{U} = \frac{(n-1)s^2}{1-\chi^2_{\frac{\alpha}{2}, \space\space n-1}}$
    
      
    
    For 1 sided intervals
    
    $\hat{L} = \frac{(n-1)s^2}{\chi^2_{{\alpha}, \space\space n-1}}$
    
    $\hat{U} = \frac{(n-1)s^2}{1-\chi^2_{{\alpha}, \space\space n-1}}$
    
      
    
    No easier formula
    
      
    
    # Confidence interval formulas on proportion, p
    
    1. Based on binomial distribution.
    2. 2 sided interval:
        
        (point estimate for proportion) $\hat{p} = \frac{x}{n}$
        
        $\hat{L} = \hat{p} - z_{\frac{\alpha}{2}}*\sqrt{\frac{\hat{p}(1-\hat{p})}{n}}$
        
        $\hat{U} = \hat{p} + z_{\frac{\alpha}{2}}*\sqrt{\frac{\hat{p}(1-\hat{p})}{n}}$
        
          
        
    3. 1 sided intervals:
        
        $\hat{L} = \hat{p} - z_{{\alpha}}*\sqrt{\frac{\hat{p}(1-\hat{p})}{n}}$
        
        $\hat{U} = \hat{p} + z_{{\alpha}}*\sqrt{\frac{\hat{p}(1-\hat{p})}{n}}$
        
    4. Easy function: 1-PropZ
    5. How close do I want it to be to the real proportion (what sample size)
    
    $n = (\frac{z_{\frac{\alpha}{2}}}{E})^2 (0.25)$
    
      
    
    or,
    
    $n = (\frac{z_{\frac{\alpha}{2}}}{E})^2\ \hat{p}(1-\hat{p})$