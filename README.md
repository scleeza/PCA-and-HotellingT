# Anomaly Detection and Visualization

_Using Phase 1 analysis to detect ouliers and build SPC chart_

   > _Phase 1 analysis means the process of finding data's outliers, however data has unknown population mean and variance._

The goal of this repo :
- Write a algorithm that is able to detect ouliers from a __multivariate__ dataset based on __Hotelling $T^2$ statistics, m-CUSUM, and m-EWMA chart__ .

# Data Descripton

- This is a manufacturing related dataset, and it has a total of 552 data records.
- Each row is a data record. Each data record contains 209 values.
- This is a multivariate detection dataset in which 𝑝 = 209. The sample size is 𝑛 = 1.
- The physical meaning of each value is omitted.

# Procedure

-  PCA, reduce dimension of the dataset, avoid curse of dimesionality.
-  Hotelling $T^2$ test for phase 1 analysis, we use $T^2$ to represent a how a point is located in a multivariate normal distribuiton (like t-statistics did). 
-  Plot SPC in order to remove outliers (find UCL/LCL, under alpha = 0.05, basically lookup from dist table).
-  Multi-variate chart like CUSUM or EWMA are also implemented to do outlier removal in case that $T^2$ missed some small shiftment.

    >In a high dimension, the noise components can add up to a great magnitude, even if individual ones are relatively small. As a result, the aggregated noise effect can overwhelm the signal effects and makes it harder to reject the null hypothesis. This is known as "curse of dimensionality."

# Mathematics

## 1.Hotelling $T^2$
- Hotelling $T^2$ statistics can be think as a __multivariate data__ version of _**t**_ statistic. As we know that T-test is used to compare difference between two univariate data set
- A conclude table of all kinds of T-test:     

| Data Type    | Comparsion     | Test Method     |
|--------------|----------------|-----------------|
| Univariate   | one pair       | T statistic          |
| Univariate   | multiple pairs | Tuckey's Method |
| Mulitvariate | one pair       | Hotelling $T^2$ |


Form of $T^2$:

>[t-statistics](images/t-statistics.png)

- After we have $T^2$, we can lookup to F-dist table to find certain upper/lower control limits. where n = observations, p = dimension.

- Code implement:
```python
def hotelling_T(X):
    '''create multivariate t-statistics'''
    # create covariance matrix
    cov_mat=np.cov(X.T)
    cov_mat_inv=np.linalg.inv(cov_mat)
    cov_mat.shape
    # calcualate x-mean
    X_bar=[X[:,i].mean() for i in range(0,pca_num)]
    # calculating X_j-X_bar
    X_sub=[X[i,:]-X_bar for i in range(len(X_pca))]
    X_sub=np.asmatrix(X_sub)
    # calculating T^2 statistics
    t_square=[np.float(np.matmul(np.matmul(X_sub[i,:],cov_mat_inv),X_sub[i,:].T)) for i in range(len(X_sub))]
    return t_tsquare
```

## 2.PCA 
- The Basic idea of PCA is simple, it is trying to find **_Vital View_** instead of **_Trivial Many_** , if we put things into a 2D chart, the axis with highest variance is the vital view since it contains more information.
>[pca_illustration](images/pca.png)

## 3.m-CUSUM
- Basic idea is to create sliding window to subset data, and comparing difference between subsets.
>[cusum_explain](images/cusum.png)

- Hyper parameter k is used for determine memory length of previous subsets.
>[cusum_explain_2](images/cusum2.png)
## 4.m-EWMA
- Like m-Cusum did, instead of using sliding window, here use eponential weighted average to include previous trend
>[ewma_explain](images/ewma.png)

- The UCL needs to be done Montecarlo simulation 
>[ewma_explian_2](images/ewma2.png)


# Result

## 1. PCA 
>__Pareto plot__\
![pareto](images/pareto.png)\
__Scree plot__\
![screeplot](images/steer.png)

## 2. Calculated $`T^2`$
>__Before removal__\
![t](images/t2.png)\
>__After removed outliers__\
![t](images/t2-after.png)
## 3. Use m-Cusum to verified again
>__Can find there still remain unremoved small shift__\
![cusum](images/mcusum.png)
