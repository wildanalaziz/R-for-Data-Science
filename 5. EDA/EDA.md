---
title: "Exploratory Data Analysis"
author: "Achmad Wildan Al Aziz"
date: "October 16, 2018"
output:
   html_document:
      self_contained: false
      keep_md: true
    
---



Exploratory data analysis(EDA) is an approach to data analysis for summarising and visualising the important characteristics of a data set. EDA is not a formal process with a strict set of rules. EDA is an important part of any data analysis because you always need to investigate the quality of your data. 

#### Objective
Your goal during EDA is to develop an understanding of your data. 

- Discover any existing patterns
- Identify mistakes
- Suggest hypothesis that can be tested
- Check assumptions
- Identify relationships among explanatory variables
- Assess direction and size of relationship between expplanatory and outcome variables

***
### Example of EDA

#### Univariate

##### Univariate Quantitative

###### Measures of central tendancy

- Mean
- Median
- Mode

###### Measures of dispersion

- Min
- Max
- Range
- Quartiles
- Variance
- Standard deviation

###### Other measures include

- Skewness
- Kurtosis

##### Univariate Graphical

- Histogram
- Box plots
- Bar plots
- Kernel density plots

#### Bivariate

##### Bivariate Quantitative

Bivariate analysis include:

- Crosstabs
- Covariance
- Correlation

- Cluster analysis
- Analysis of variance (ANOVA)
- Factor analysis
- Principal component analysis (PCA)
- Bivariate Graphical
- Graphical techniques include:

- Scatterplot
- Box plot

### Diamonds Dataset

Merupakan data harga berlian dan karakteristiknya.
Variabel yang digunakan adalah sebagai berikut

Variabel | Keterangan
--------- | ------------------------------------------------------------------------------------------
price | _price in_ ($US) ($326-$18,823)
carat | _weight of the diamond_ (0.2-5.01)
cut | _quality of the cut_ (Fair, Good, Very Good, Premium, Ideal)
color | _diamond colour_,  from J (worst) to D (best)
clarity | _a measurement of how clear the diamond is_ (I1 (worst), SI2, SI1, VS2, VS1, VVS2, VVS1, IF (best))
x | _length in mm_ (0-10.74)
y | _width in mm_ (0-58.9)
z | _depth in mm_ (0-31.8)
depth | _total depth percentage_ = z / mean(x, y) = 2 * z / (x + y), (43-79)
table | _width of top of diamond relative to widest point_ (43-95)


```r
data("diamonds")
datatable(diamonds)
```

<!--html_preserve--><div id="htmlwidget-d714aba46baf7a6ebe72" style="width:100%;height:auto;" class="datatables html-widget"></div>


```r
summary(diamonds)
```

```
##      carat               cut        color        clarity     
##  Min.   :0.2000   Fair     : 1610   D: 6775   SI1    :13065  
##  1st Qu.:0.4000   Good     : 4906   E: 9797   VS2    :12258  
##  Median :0.7000   Very Good:12082   F: 9542   SI2    : 9194  
##  Mean   :0.7979   Premium  :13791   G:11292   VS1    : 8171  
##  3rd Qu.:1.0400   Ideal    :21551   H: 8304   VVS2   : 5066  
##  Max.   :5.0100                     I: 5422   VVS1   : 3655  
##                                     J: 2808   (Other): 2531  
##      depth           table           price             x         
##  Min.   :43.00   Min.   :43.00   Min.   :  326   Min.   : 0.000  
##  1st Qu.:61.00   1st Qu.:56.00   1st Qu.:  950   1st Qu.: 4.710  
##  Median :61.80   Median :57.00   Median : 2401   Median : 5.700  
##  Mean   :61.75   Mean   :57.46   Mean   : 3933   Mean   : 5.731  
##  3rd Qu.:62.50   3rd Qu.:59.00   3rd Qu.: 5324   3rd Qu.: 6.540  
##  Max.   :79.00   Max.   :95.00   Max.   :18823   Max.   :10.740  
##                                                                  
##        y                z         
##  Min.   : 0.000   Min.   : 0.000  
##  1st Qu.: 4.720   1st Qu.: 2.910  
##  Median : 5.710   Median : 3.530  
##  Mean   : 5.735   Mean   : 3.539  
##  3rd Qu.: 6.540   3rd Qu.: 4.040  
##  Max.   :58.900   Max.   :31.800  
## 
```

##### Missing values

grafik berikut untuk mengecej _missing values_

```r
library(naniar)
gg_miss_var(diamonds, show_pct = TRUE)
```

![](EDA_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

tidak ada missing values pada data _diamonds_

##### Visualization {.tabset}

Berikut adalah korelasi antar variabel dari data _diamonds_ 

```r
num=diamonds[c('price','carat','x','y','z','depth','table')]
corrplot(cor(num),type="full",method="square")
```

![](EDA_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

beberapa variabel terjadi multikolinearitas, yaitu variabel carat, x, y, z. Variabel _price_ berkorelasi positif cukup tinggi dengan variabel carat, x, y, z.

###### Price

berikut adalah histogram untuk variabel respon, _price_.

```r
ggplot(data = diamonds) +
  geom_histogram(mapping = aes(x = price, fill=..count..), binwidth = 900)+
    scale_y_continuous(name = "Frequency") +
    scale_x_continuous(name = " Price ($US)") +
    ggtitle("Frequency histogram Price of diamonds ($US)")
```

![](EDA_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

Tampak histogram _skew kanan_, sehingga variabel _price_ tidak berdistribusi normal. Mayoritas produsen memberikan harga sekitar 20000-4000 ($US). Sebagian besar Berlian harganya kurang dari $US 5000. Varibel Price dapat dilakukan transformasi.

###### carat

berikut adalah histogram untuk variabel, _carat_.

```r
ggplot(data = diamonds) +
  geom_histogram(mapping = aes(x = carat, fill=..count..), binwidth = 0.4)+
    scale_y_continuous(name = "carat") +
    scale_x_continuous(name = " carat of the diamond") +
    ggtitle("Frequency histogram carat of the diamond")
```

![](EDA_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

Tampak histogram _skew kanan_, sehingga variabel _carat_ tidak berdistribusi normal. Variabel _carat_ bernilai antara 0,2 hingga 5.01, tampak pada histogram _carat_ yang nilainya lebih dari 3 tidak muncul karena frekuensinya terlalu sedikit. Dapat dilakukan penyesuaian seperti histogram berikut.


```r
ggplot(data = diamonds) +
  geom_histogram(mapping = aes(x = carat, fill=..count..), binwidth = 0.4)+
    scale_y_continuous(name = "Frequency") +
    scale_x_continuous(name = " weight of the diamond") +
    ggtitle("Frequency histogram weight of the diamond") +
    coord_cartesian(ylim = c(0, 100))
```

![](EDA_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

Atau dapat juga ditabelkan seperti tabel berikut.


```r
diamonds %>% count(cut_width(carat, 0.4))
```

```
## # A tibble: 12 x 2
##    `cut_width(carat, 0.4)`     n
##    <fct>                   <int>
##  1 [0.2,0.6]               24448
##  2 (0.6,1]                 11990
##  3 (1,1.4]                 11093
##  4 (1.4,1.8]                4135
##  5 (1.8,2.2]                1812
##  6 (2.2,2.6]                 395
##  7 (2.6,3]                    35
##  8 (3,3.4]                    22
##  9 (3.4,3.8]                   4
## 10 (3.8,4.2]                   4
## 11 (4.2,4.6]                   1
## 12 (5,5.4]                     1
```

###### Boxplot

Berikut adalah Boxplot untuk setiap variabel numerik


```r
require(reshape2)
ggplot(data = melt(num[,-1]), aes(x=variable, y=value)) +
  geom_boxplot() +
  facet_wrap(~variable, scales='free')
```

![](EDA_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

juga dapat ditampilkan Boxplot untuk variabel kategorik


```r
kat=diamonds[c('cut','color','clarity','price')]

ggplot(data = kat, aes(x=cut, y=price)) +
  geom_boxplot() 
```

![](EDA_files/figure-html/unnamed-chunk-9-1.png)<!-- -->