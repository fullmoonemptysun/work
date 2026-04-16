# RAM Model of Computation

  

1. Model of computer where:
    
    1. Each memory access takes one step (regardless if in cache, or on the disk)
    2. Simple operations like: (if, +, >, <, etc.) take 1 step
    3. Loops and subroutines may take more than 1 step
    
      
    

  

# Best, Worst, And average case complexity

1. Judge an algorithm my looking at how it works at _all_ instances
2. Worst case is the most useful out of the 3 as it gives us the worst possible complexity
3. These 3 define numerical functions representing time versus problem size.

  

# Big Oh notation

1. Counting exact number of steps in RAM and coming up with an accurate description of the function of the algorithm is both difficult and mostly useless.

  

1. We are interested not in how fast an algorithm runs, but in how fast the growth for it is.

  

1. 3 Definitions:
    
    1. $f(n) = O(g(n))$ means c*g(n) is an upper bound on f(n), there exists a constant c such that $f(n) \le c\cdot g(n) \ , (n \ge n_0,$ for some constant at one point, $n_0)$  
          
        
    2. $f(n) = \Omega(g(n))$ means $c\cdot g(n)$ is a lower bound on $f(n)$. There exists some constant $c$ such that $f(n)$ is always $\geq c\cdot g(n),$ $(n \ge n_0,$ for some constant at one point $n_0)$
    3. There’s theta which is the average bound.
    
      
    

# 2.3 Growth rates and dominance relations

![[image 69.png|image 69.png]]

  

## 2.3.1 Dominance Relations

1. Big O categorizes functions into different classes, such that functions in the same class are equivalent in terms of big O. Ex: 0.34n and 234234n are both $\Theta(n)$.

  

1. If 2 functions are from 2 different classes, either $f(n) = O(g(n)) \ \ or \ \ g(n) = O(f(n)).$

  

1. Only A few of these classes are needed for basic algorithm analysis:
    
    1. **Constant functions:** No dependence on the parameter n. $f(n) = 1, \ \ f(n) = print(min(n,100)).$  
          
        
    2. **Logarithmic functions:** Grow very slowly with n but faster than constant function (do not grow at all). $f(n) = logn$
    3. **Linear functions:** passes through an n-element array. $f(n) = 10n, \ \ f(n) = n$
    4. **Superlinear functions:** Arises in _Quicksort_ and _Mergesort._ Grow a little faster than linear. $f(n) = nlogn$
    5. **Quadratic functions:** Look at all pairs of items in an n element array. Happens in _insertion_ and _selection sort._ $f(n) = n^2$
    6. **Cubic functions:** Look at all triplets. Appear in dynamic programming too. $f(n) = n^3$
    7. **Exponential Functions:** Appear in situation where we have to enumerate all subsets of n items. Become useless very fast. $f(n) = c^n \text{ for some constant } c > 1.$
    8. **Factorial functions:** Generating all permutations/ordering of n items. $f(n) = n!$
>[!IMPORTANT] $n! > 2^n > n^3 > n^2 > nlogn > n > logn > 1$

  

# 2.4 Working with the Big Oh

## 2.4.1 Adding functions

1. Sum of 2 functions is the bigger function
    
    $O(f(n)) + O(g(n))$ → $O(max(f(n), g(n))$
    

  

1. Example: $n^3 + n ^2 + n + 1 = O(n^3)$
2. Example: $f(n) = O(n^2) \space\space and \space\space g(n) = O(n^2), \text{ then, } f(n) + g(n) = O(n^2)$

  

## 2.4.2 Multiplying

1. Multiplying with a constant factor does not affect the asymptotic behavior as it is just a multiplicative constant.
2. $O(c \cdot f(n)) = O(f(n))$, c must be greater than 0 of course or this won’t follow.

  

1. However, in multiplication with another function, where both are increasing is different:
    
    In general: $O(f(n)) * O(g(n)) \rightarrow O(f(n) * g(n))$ .
    
      
    

# 2.5 Reasoning about Efficiency

How to reason about an algorithm’s runtime, given a precise written description of the algorithm.

## 2.5.1 Selection Sort

Total number of times the if comparison happens: $S(n)$

Now,

The main for loop runs n times

The inside one runs for n-i-1 times

so we can say,

$S(n) = \sum^{n-1}_{i = 0} {n-i-1}$

  

This can be = n(n-1) at max, $S(n) \leq n(n-1) = O(n^2)$

This will also be bigger than n/2 * n/2, $S(n) \geq \frac{n^2}{4} = \Omega(n^2)$

  

(remember the definitions, for any constant c)

And so for selection sort $\Theta(n^2)$, Quadratic.

  

## 2.5.2 Insertion Sort

O(n^2)

  

## 2.5.3 String Pattern Matching

```C
int findmatch(char *p, char *t)
{
		int i,j;       /* counters */
		int m, n;     /* string lengths */
		m = strlen(p);
		n = strlen(t);
		for (i=0; i<=(n-m); i=i+1) {
		j=0;
		while ((j<m) && (t[i+j]==p[j]))
				j = j+1;
		if (j == m) return(i);
		}
return(-1);
}
```

**Worst Case analysis:**

Outer loop will go at most $(n-m)$ times (why?)

The inner loop will go at most $m$ times. (ignoring the better case, early termination) + 2 Statements (j = 0 and the if)

So the worst case running time is $O((n-m)(m+2))$

  

However, we did not count the time it takes to compute the length of the strings (`strlen`)

adding this, the complexity becomes:

$O(n + m + (n-m)(m+2))$

  

Now we simplify

We know that,

$(m+2) = \Theta(m) \\ \implies O(n+m+(n-m)m) \\ \text{Upon simplifying,} \\ \implies O(n+m+nm-nm^2) \\ \text{We also know that, n will usually be larger than m and so,} \\ m + n \leq 2n = \Theta(n) \\ \implies O(n + nm - m^2) \\ \text{also,} \\ n \leq nm \implies n +nm = \Theta(nm) \\ \implies O(nm -m^2)$

Now, m will ideally always be ≤ n which means nm ≥ $m^2$

And so the negative term will try to bring down the time but it cannot cancel anything out and so the complexity finally becomes:

> $O(nm)$  


## Matrix Multiplication

to be covered

# 2.6 Logarithms and their applications
1. Binary search : number of comparisons is how many times we need to divide n by 2 until it becomes = 1. This is $log_2{n}$ 


## 2.6.2 Logarithms and trees
- What will be the height of a tree where parents have *d* children each?
- Say there are n leaves. h is what?
> $log_dn$


## 2.6.3 Logarithms and bits
- can have 2 bit patters when length 1
- can have 4 bit patterns when have length 2. 
- How many bits w needed to represent *n* items possibilities?
>$log_2 n$

## 2.6.4 Logarithms and multiplication
- how to calculate something like $a^b$ using $e^x$ and ln(x) function?
>$e^{(b\cdot ln(a))}$


1. It is okay to ignore the base of the logarithm when analyzing algorithms.
2. Power of binary search from the logarithmic complexity. Not from the base being particularly 2.




