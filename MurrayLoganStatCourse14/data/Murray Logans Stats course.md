Murray Logan's Stats Course
========================================================
install.packages(‘knitr’)

Using library ‘markdown’

<create new R Markdown document>

<knit html>

This will create a html output from the data. Can be used to write papers, so if you change the data, the paper will change with it.

This is an R Markdown document. Markdown is a simple formatting syntax for authoring web pages (click the **Help** toolbar button for more details on using R Markdown).

When you click the **Knit HTML** button a web page will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:


```r
summary(cars)
```

```
##      speed           dist       
##  Min.   : 4.0   Min.   :  2.00  
##  1st Qu.:12.0   1st Qu.: 26.00  
##  Median :15.0   Median : 36.00  
##  Mean   :15.4   Mean   : 42.98  
##  3rd Qu.:19.0   3rd Qu.: 56.00  
##  Max.   :25.0   Max.   :120.00
```

You can also embed plots, for example:


```r
plot(cars)
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png) 

A linear model consists of the intercept, slope and predictor. A linear model DOES NOT have to be a straigth line, it only means that there are No x in the power (eg y = ax + b). A non linear model does not imply that there are x only at the base (eg y = ab^x)

I am experiencing lay-out problems: entering a newline or removing superscript formatting.
www.flutterbys.com.au/stats/downloads/data/ 

Download 'fertilizer.csv'


```r
setwd("~/Documents/Monash/PhD/R")
fert <- read.csv('fertilizer.csv',strip.white=T)
```
It is good practise to enter the 'strip.white=T' command as this chomps off any white spaces in the document.

Give the first 6 lines of the data

```r
head(fert)
```

```
##   FERTILIZER YIELD
## 1         25    84
## 2         50    80
## 3         75    90
## 4        100   154
## 5        125   148
## 6        150   169
```

Also good practise to get the variables of the data

```r
str(fert)
```

```
## 'data.frame':	10 obs. of  2 variables:
##  $ FERTILIZER: int  25 50 75 100 125 150 175 200 225 250
##  $ YIELD     : int  84 80 90 154 148 169 206 244 212 248
```

So quickly explore the data it is also smart to get a summary

```r
summary(fert)
```

```
##    FERTILIZER         YIELD      
##  Min.   : 25.00   Min.   : 80.0  
##  1st Qu.: 81.25   1st Qu.:104.5  
##  Median :137.50   Median :161.5  
##  Mean   :137.50   Mean   :163.5  
##  3rd Qu.:193.75   3rd Qu.:210.5  
##  Max.   :250.00   Max.   :248.0
```

--------------------------

For the next section we need the package 'car'

When installing packages in knitr, it is important to set the mirror and location of the library

```r
install.packages("car", repos="http://cran.ms.unimelb.edu.au/")
```

```
## 
## The downloaded binary packages are in
## 	/var/folders/sm/4cp43lwn32lgnfj964mrw3qw0000gq/T//RtmpFA5R4n/downloaded_packages
```

```r
library("car", lib.loc="/Library/Frameworks/R.framework/Versions/3.0/Resources/library")
```

Now make a scatterplot

```r
scatterplot(YIELD~FERTILIZER,data=fert)
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8-1.png) 



And run ggplot

```r
ggplot(fert,aes(y=YIELD, x=FERTILIZER))+geom_point()
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10-1.png) 

Different options in ggplot (white background)

```r
ggplot(fert,aes(y=YIELD, x=FERTILIZER))+geom_point()+theme_classic()
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-1.png) 

Alter the scales

```r
ggplot(fert,aes(y=YIELD, x=FERTILIZER))+geom_point()+
  theme_classic()+
  scale_x_continuous('Fertilizer') +
  scale_y_continuous('Grass yield')
```

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12-1.png) 

The order of commands is not important EXCEPT for the geometric features (each geometric layer is plotted in the order they are called).




```r
ggplot(fert,aes(y=YIELD, x=FERTILIZER))+geom_point()+
  theme_classic()+
  scale_x_continuous(expression(Fertilizer~(g*m^2))) +
  scale_y_continuous(expression(Grass~yield~(g*m^2))) 
```

![plot of chunk unnamed-chunk-13](figure/unnamed-chunk-13-1.png) 

To see which plotting expression options are available, type
demo(plotmath)

These expressions need to be put into expression() to be transformed.
Anywhere you want a space in between words, put a ~ instead

You can add items to your graphs like this

```r
p+geom_smooth(method='lm')
```

```
## Error in eval(expr, envir, enclos): object 'p' not found
```

```r
p<-ggplot(fert,aes(y=YIELD, x=FERTILIZER))+geom_point()+
  theme_classic()+
  scale_x_continuous(expression(Fertilizer~(g*m^2))) +
  scale_y_continuous(expression(Grass~yield~(g*m^2))) 
p+theme(axis.title.x=element_text(vjust=-1,size=rel(1.5)),
        axis.title.y=element_text(vjust=2,size=rel(1.5)))
```

![plot of chunk unnamed-chunk-14](figure/unnamed-chunk-14-1.png) 

```r
p<-p+theme(axis.title.x=element_text(vjust=-1,size=rel(1.5)),
        axis.title.y=element_text(vjust=2,size=rel(1.5)))
```

Add lines. Blue line is best fit for data, grey area is 95% confidence interval.

```r
p+geom_smooth(method='lm')
```

![plot of chunk unnamed-chunk-15](figure/unnamed-chunk-15-1.png) 

Will also run for general linear models

```r
p+geom_smooth(method='glm',formula=y~x,family='poisson')
```

![plot of chunk unnamed-chunk-16](figure/unnamed-chunk-16-1.png) 

And negative binomial (needs library MASS)


```r
p+geom_smooth(method='glm.nb',formula=y~x)
```

![plot of chunk unnamed-chunk-18](figure/unnamed-chunk-18-1.png) 


------------------------------
**Modelling**

Let's make some models:

```r
fert.lm<-lm(YIELD~FERTILIZER,data=fert)
```

Again look at the variables using extracter functions

```r
str(fert.lm)
```

```
## List of 12
##  $ coefficients : Named num [1:2] 51.933 0.811
##   ..- attr(*, "names")= chr [1:2] "(Intercept)" "FERTILIZER"
##  $ residuals    : Named num [1:10] 11.78 -12.5 -22.79 20.93 -5.36 ...
##   ..- attr(*, "names")= chr [1:10] "1" "2" "3" "4" ...
##  $ effects      : Named num [1:10] -517.03 184.25 -23.73 18.66 -8.96 ...
##   ..- attr(*, "names")= chr [1:10] "(Intercept)" "FERTILIZER" "" "" ...
##  $ rank         : int 2
##  $ fitted.values: Named num [1:10] 72.2 92.5 112.8 133.1 153.4 ...
##   ..- attr(*, "names")= chr [1:10] "1" "2" "3" "4" ...
##  $ assign       : int [1:2] 0 1
##  $ qr           :List of 5
##   ..$ qr   : num [1:10, 1:2] -3.162 0.316 0.316 0.316 0.316 ...
##   .. ..- attr(*, "dimnames")=List of 2
##   .. .. ..$ : chr [1:10] "1" "2" "3" "4" ...
##   .. .. ..$ : chr [1:2] "(Intercept)" "FERTILIZER"
##   .. ..- attr(*, "assign")= int [1:2] 0 1
##   ..$ qraux: num [1:2] 1.32 1.27
##   ..$ pivot: int [1:2] 1 2
##   ..$ tol  : num 1e-07
##   ..$ rank : int 2
##   ..- attr(*, "class")= chr "qr"
##  $ df.residual  : int 8
##  $ xlevels      : Named list()
##  $ call         : language lm(formula = YIELD ~ FERTILIZER, data = fert)
##  $ terms        :Classes 'terms', 'formula' length 3 YIELD ~ FERTILIZER
##   .. ..- attr(*, "variables")= language list(YIELD, FERTILIZER)
##   .. ..- attr(*, "factors")= int [1:2, 1] 0 1
##   .. .. ..- attr(*, "dimnames")=List of 2
##   .. .. .. ..$ : chr [1:2] "YIELD" "FERTILIZER"
##   .. .. .. ..$ : chr "FERTILIZER"
##   .. ..- attr(*, "term.labels")= chr "FERTILIZER"
##   .. ..- attr(*, "order")= int 1
##   .. ..- attr(*, "intercept")= int 1
##   .. ..- attr(*, "response")= int 1
##   .. ..- attr(*, ".Environment")=<environment: R_GlobalEnv> 
##   .. ..- attr(*, "predvars")= language list(YIELD, FERTILIZER)
##   .. ..- attr(*, "dataClasses")= Named chr [1:2] "numeric" "numeric"
##   .. .. ..- attr(*, "names")= chr [1:2] "YIELD" "FERTILIZER"
##  $ model        :'data.frame':	10 obs. of  2 variables:
##   ..$ YIELD     : int [1:10] 84 80 90 154 148 169 206 244 212 248
##   ..$ FERTILIZER: int [1:10] 25 50 75 100 125 150 175 200 225 250
##   ..- attr(*, "terms")=Classes 'terms', 'formula' length 3 YIELD ~ FERTILIZER
##   .. .. ..- attr(*, "variables")= language list(YIELD, FERTILIZER)
##   .. .. ..- attr(*, "factors")= int [1:2, 1] 0 1
##   .. .. .. ..- attr(*, "dimnames")=List of 2
##   .. .. .. .. ..$ : chr [1:2] "YIELD" "FERTILIZER"
##   .. .. .. .. ..$ : chr "FERTILIZER"
##   .. .. ..- attr(*, "term.labels")= chr "FERTILIZER"
##   .. .. ..- attr(*, "order")= int 1
##   .. .. ..- attr(*, "intercept")= int 1
##   .. .. ..- attr(*, "response")= int 1
##   .. .. ..- attr(*, ".Environment")=<environment: R_GlobalEnv> 
##   .. .. ..- attr(*, "predvars")= language list(YIELD, FERTILIZER)
##   .. .. ..- attr(*, "dataClasses")= Named chr [1:2] "numeric" "numeric"
##   .. .. .. ..- attr(*, "names")= chr [1:2] "YIELD" "FERTILIZER"
##  - attr(*, "class")= chr "lm"
```

```r
coef(fert.lm) #the coefficients of the linear model
```

```
## (Intercept)  FERTILIZER 
##  51.9333333   0.8113939
```

```r
resid(fert.lm) #the residuals of each x value
```

```
##          1          2          3          4          5          6 
##  11.781818 -12.503030 -22.787879  20.927273  -5.357576  -4.642424 
##          7          8          9         10 
##  12.072727  29.787879 -22.496970  -6.781818
```

Now start plotting. First prepare the graphical space with par(mfrow = c(2,3)).
Plot all the diagnostic graphs of the model

```r
par(mfrow = c(2,3))
plot(fert.lm,ask=F,which=1:6) 
```

![plot of chunk unnamed-chunk-21](figure/unnamed-chunk-21-1.png) 

```r
#ask=F turns off the asking prompt
#which=1:6 will show the first 6 diagnostic graphs
```

plot [1,1] is the **residual plot**, the unexplained bit of the model. There should be no pattern in this plot. If there is a pattern, this can occur for several reasons:
-wrong model is implied
-unequal variance (will create wedge in residual box)
-temporal autocorrelation (will produce horizontal stripes)
A smoother (red line) will help you identify a potential pattern in the residuals

**q-q normal plot** [1,2] shows the normality of the data. If the data is exactly normal, all points will be exactly on the diagonal line. In itself the q-q plot is not too useful, only if residual plot doesn't make sense

**standarized residual plot** [1,3] is similar to the residual plot. This one should be looked at when you have preformed a correction for autocorrelation

**cook's distance** [2,1] looks at how much of an outlier the data is on BOTH the y and x-axis. The close cook's d is to 1, the more of an outlier it is. This is for each observation, sorted in your observation order

**leverage plots** [2,2] and [2,3] make up the cook's distance plots and show if the outliers are determined by outlier on the x and y-axis respectively.


**If** all of these diagnostic graphs don't show any problem, **only then** can you run the summary of the model

summary() like plot() is an overloaded function. This means that the output of these functions will differ depending on the data that is inputted. For example, a summary of a model or summary of a table.


```r
summary(fert.lm)
```

```
## 
## Call:
## lm(formula = YIELD ~ FERTILIZER, data = fert)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -22.79 -11.07  -5.00  12.00  29.79 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 51.93333   12.97904   4.001  0.00394 ** 
## FERTILIZER   0.81139    0.08367   9.697 1.07e-05 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 19 on 8 degrees of freedom
## Multiple R-squared:  0.9216,	Adjusted R-squared:  0.9118 
## F-statistic: 94.04 on 1 and 8 DF,  p-value: 1.067e-05
```
The **intercept** estimate shows if the intercept is equal to 0, there is almost never a biological interpretation for this
The **slope** estimate shows if the slope is equal to 0 
The **F-statistic** is the squared value of the t-value of the slope. 

Can also look at confidence intervalls instead of p-values, as they are far more informative

```r
confint(fert.lm)
```

```
##                  2.5 %    97.5 %
## (Intercept) 22.0036246 81.863042
## FERTILIZER   0.6184496  1.004338
```
The interval doesn't overlap 0 and is for this reason the effect of the slope is significant

Open a new dataset that has some problems

```r
peake <- read.csv('peake.csv',strip.white=T)
head(peake)
```

```
##      AREA SPECIES INDIV
## 1  516.00       3    18
## 2  469.06       7    60
## 3  462.25       6    57
## 4  938.60       8   100
## 5 1357.15      10    48
## 6 1773.66       9   118
```

```r
str(peake)
```

```
## 'data.frame':	25 obs. of  3 variables:
##  $ AREA   : num  516 469 462 939 1357 ...
##  $ SPECIES: int  3 7 6 8 10 9 10 11 16 9 ...
##  $ INDIV  : int  18 60 57 100 48 118 148 214 225 283 ...
```

```r
summary(peake)
```

```
##       AREA            SPECIES       INDIV       
##  Min.   :  462.2   Min.   : 3   Min.   :  18.0  
##  1st Qu.: 1773.7   1st Qu.:10   1st Qu.: 148.0  
##  Median : 4451.7   Median :14   Median : 338.0  
##  Mean   : 7802.0   Mean   :15   Mean   : 446.9  
##  3rd Qu.: 9287.7   3rd Qu.:21   3rd Qu.: 632.0  
##  Max.   :27144.0   Max.   :25   Max.   :1402.0
```

```r
scatterplot(INDIV~AREA,data=peake)
```

![plot of chunk unnamed-chunk-24](figure/unnamed-chunk-24-1.png) 
Now, this data is not normal distributed. So we need to do something explore this data.

First, transform the data

```r
scatterplot(log(INDIV)~log(AREA),data=peake)
```

![plot of chunk unnamed-chunk-25](figure/unnamed-chunk-25-1.png) 
This seems to have improved the data

Now fit a model reflecting the data

```r
peake.lm<-lm(log(INDIV)~log(AREA),data=peake)
str(peake.lm)
```

```
## List of 12
##  $ coefficients : Named num [1:2] -1.326 0.835
##   ..- attr(*, "names")= chr [1:2] "(Intercept)" "log(AREA)"
##  $ residuals    : Named num [1:25] -0.998 0.285 0.246 0.217 -0.825 ...
##   ..- attr(*, "names")= chr [1:25] "1" "2" "3" "4" ...
##  $ effects      : Named num [1:25] -28.243 5.05 0.306 0.317 -0.704 ...
##   ..- attr(*, "names")= chr [1:25] "(Intercept)" "log(AREA)" "" "" ...
##  $ rank         : int 2
##  $ fitted.values: Named num [1:25] 3.89 3.81 3.8 4.39 4.7 ...
##   ..- attr(*, "names")= chr [1:25] "1" "2" "3" "4" ...
##  $ assign       : int [1:2] 0 1
##  $ qr           :List of 5
##   ..$ qr   : num [1:25, 1:2] -5 0.2 0.2 0.2 0.2 0.2 0.2 0.2 0.2 0.2 ...
##   .. ..- attr(*, "dimnames")=List of 2
##   .. .. ..$ : chr [1:25] "1" "2" "3" "4" ...
##   .. .. ..$ : chr [1:2] "(Intercept)" "log(AREA)"
##   .. ..- attr(*, "assign")= int [1:2] 0 1
##   ..$ qraux: num [1:2] 1.2 1.31
##   ..$ pivot: int [1:2] 1 2
##   ..$ tol  : num 1e-07
##   ..$ rank : int 2
##   ..- attr(*, "class")= chr "qr"
##  $ df.residual  : int 23
##  $ xlevels      : Named list()
##  $ call         : language lm(formula = log(INDIV) ~ log(AREA), data = peake)
##  $ terms        :Classes 'terms', 'formula' length 3 log(INDIV) ~ log(AREA)
##   .. ..- attr(*, "variables")= language list(log(INDIV), log(AREA))
##   .. ..- attr(*, "factors")= int [1:2, 1] 0 1
##   .. .. ..- attr(*, "dimnames")=List of 2
##   .. .. .. ..$ : chr [1:2] "log(INDIV)" "log(AREA)"
##   .. .. .. ..$ : chr "log(AREA)"
##   .. ..- attr(*, "term.labels")= chr "log(AREA)"
##   .. ..- attr(*, "order")= int 1
##   .. ..- attr(*, "intercept")= int 1
##   .. ..- attr(*, "response")= int 1
##   .. ..- attr(*, ".Environment")=<environment: R_GlobalEnv> 
##   .. ..- attr(*, "predvars")= language list(log(INDIV), log(AREA))
##   .. ..- attr(*, "dataClasses")= Named chr [1:2] "numeric" "numeric"
##   .. .. ..- attr(*, "names")= chr [1:2] "log(INDIV)" "log(AREA)"
##  $ model        :'data.frame':	25 obs. of  2 variables:
##   ..$ log(INDIV): num [1:25] 2.89 4.09 4.04 4.61 3.87 ...
##   ..$ log(AREA) : num [1:25] 6.25 6.15 6.14 6.84 7.21 ...
##   ..- attr(*, "terms")=Classes 'terms', 'formula' length 3 log(INDIV) ~ log(AREA)
##   .. .. ..- attr(*, "variables")= language list(log(INDIV), log(AREA))
##   .. .. ..- attr(*, "factors")= int [1:2, 1] 0 1
##   .. .. .. ..- attr(*, "dimnames")=List of 2
##   .. .. .. .. ..$ : chr [1:2] "log(INDIV)" "log(AREA)"
##   .. .. .. .. ..$ : chr "log(AREA)"
##   .. .. ..- attr(*, "term.labels")= chr "log(AREA)"
##   .. .. ..- attr(*, "order")= int 1
##   .. .. ..- attr(*, "intercept")= int 1
##   .. .. ..- attr(*, "response")= int 1
##   .. .. ..- attr(*, ".Environment")=<environment: R_GlobalEnv> 
##   .. .. ..- attr(*, "predvars")= language list(log(INDIV), log(AREA))
##   .. .. ..- attr(*, "dataClasses")= Named chr [1:2] "numeric" "numeric"
##   .. .. .. ..- attr(*, "names")= chr [1:2] "log(INDIV)" "log(AREA)"
##  - attr(*, "class")= chr "lm"
```

```r
coef(peake.lm) 
```

```
## (Intercept)   log(AREA) 
##  -1.3263207   0.8349156
```

```r
resid(peake.lm)
```

```
##           1           2           3           4           5           6 
## -0.99827974  0.28532404  0.24624123  0.21700315 -0.82484350 -0.14883198 
##           7           8           9          10          11          12 
##  0.12000954  0.44053515  0.03308160  0.05109573  0.25738403 -0.06021521 
##          13          14          15          16          17          18 
##  0.04109079 -0.07455441  0.48089427  0.11175433  0.44289111  0.10756519 
##          19          20          21          22          23          24 
##  0.51093621 -0.40743739  0.61750477 -0.44000565 -0.06359940 -0.22935072 
##          25 
## -0.71619315
```

```r
par(mfrow = c(2,3))
plot(peake.lm,ask=F,which=1:6) 
```

![plot of chunk unnamed-chunk-26](figure/unnamed-chunk-26-1.png) 

```r
summary(peake.lm)
```

```
## 
## Call:
## lm(formula = log(INDIV) ~ log(AREA), data = peake)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -0.9983 -0.1488  0.0511  0.2574  0.6175 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) -1.32632    0.59645  -2.224   0.0363 *  
## log(AREA)    0.83492    0.07066  11.816 3.01e-11 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.4274 on 23 degrees of freedom
## Multiple R-squared:  0.8586,	Adjusted R-squared:  0.8524 
## F-statistic: 139.6 on 1 and 23 DF,  p-value: 3.007e-11
```
Great!

Now predict values based on the model, so we can plot

```r
#I first need to 'predict the model (as it was transformed)
xs <- seq(min(peake$AREA),max(peake$AREA),len=100)
#this will make a sequence of 100 points in between the minimum and maximum values
newdata <- data.frame(AREA=xs) #put this in a data frame
head(newdata)
```

```
##        AREA
## 1  462.2500
## 2  731.7629
## 3 1001.2759
## 4 1270.7888
## 5 1540.3017
## 6 1809.8146
```

```r
pred<-predict(peake.lm, newdata=newdata,interval='confidence')
head(pred)
```

```
##        fit      lwr      upr
## 1 3.796810 3.427534 4.166086
## 2 4.180329 3.868340 4.492318
## 3 4.442137 4.166685 4.717589
## 4 4.641150 4.391411 4.890888
## 5 4.801738 4.570995 5.032481
## 6 4.936365 4.720010 5.152719
```

```r
newdata<-cbind(newdata,pred) #cbind binds data according to columns
```
Now for each predicted valua there is a fitted value and a lower and upper confidence interval

And plot

```r
ggplot(newdata,aes(y=fit, x=AREA)) +
  geom_line() +
  geom_ribbon(aes(ymin=lwr,ymax=upr),fill='blue') +
  geom_point(data=peake,aes(y=log(INDIV),x=AREA))
```

![plot of chunk unnamed-chunk-28](figure/unnamed-chunk-28-1.png) 

```r
   #confidence intervals, make sure to name the lower and upper!
```

--------------------------
**Multiple Linear Regression**

Regression with multiple predictor variables.

**Centering** of the data: calculating the mean of your x or y and subtracting this value from all of your individual values. This will also **reduce the correlation** between variables and also help to **convert** your model.

**Co-linearity**: you can't have predictors in the model that correlate to each other. To test this, take each predictor and test them against the other predictors. If there is an R^2 higher than .8, this is considered a correlation. We use 1-R^2, the variance inflation. A variance inflation greater then 5 is generally considered a correlation (some people use 3). If there is a correlating predictor variable, this predictor can;t be in the same model as the others.
**vif** will give this variance inflation between each predictor variable 

Explore this with the next dataset

```r
loyn<-(read.csv('loyn.csv',strip.white=T))
head(loyn)
```

```
##   ABUND AREA YR.ISOL DIST LDIST GRAZE ALT
## 1   5.3  0.1    1968   39    39     2 160
## 2   2.0  0.5    1920  234   234     5  60
## 3   1.5  0.5    1900  104   311     5 140
## 4  17.1  1.0    1966   66    66     3 160
## 5  13.8  1.0    1918  246   246     5 140
## 6  14.1  1.0    1965  234   285     3 130
```

```r
str(loyn)
```

```
## 'data.frame':	56 obs. of  7 variables:
##  $ ABUND  : num  5.3 2 1.5 17.1 13.8 14.1 3.8 2.2 3.3 3 ...
##  $ AREA   : num  0.1 0.5 0.5 1 1 1 1 1 1 1 ...
##  $ YR.ISOL: int  1968 1920 1900 1966 1918 1965 1955 1920 1965 1900 ...
##  $ DIST   : int  39 234 104 66 246 234 467 284 156 311 ...
##  $ LDIST  : int  39 234 311 66 246 285 467 1829 156 571 ...
##  $ GRAZE  : int  2 5 5 3 5 3 5 5 4 5 ...
##  $ ALT    : int  160 60 140 160 140 130 90 60 130 130 ...
```

```r
summary(loyn)
```

```
##      ABUND            AREA            YR.ISOL          DIST       
##  Min.   : 1.50   Min.   :   0.10   Min.   :1890   Min.   :  26.0  
##  1st Qu.:12.40   1st Qu.:   2.00   1st Qu.:1928   1st Qu.:  93.0  
##  Median :21.05   Median :   7.50   Median :1962   Median : 234.0  
##  Mean   :19.51   Mean   :  69.27   Mean   :1950   Mean   : 240.4  
##  3rd Qu.:28.30   3rd Qu.:  29.75   3rd Qu.:1966   3rd Qu.: 333.2  
##  Max.   :39.60   Max.   :1771.00   Max.   :1976   Max.   :1427.0  
##      LDIST            GRAZE            ALT       
##  Min.   :  26.0   Min.   :1.000   Min.   : 60.0  
##  1st Qu.: 158.2   1st Qu.:2.000   1st Qu.:120.0  
##  Median : 338.5   Median :3.000   Median :140.0  
##  Mean   : 733.3   Mean   :2.982   Mean   :146.2  
##  3rd Qu.: 913.8   3rd Qu.:4.000   3rd Qu.:182.5  
##  Max.   :4426.0   Max.   :5.000   Max.   :260.0
```

```r
scatterplotMatrix(~ABUND+DIST+LDIST+AREA+GRAZE+ALT+YR.ISOL,data=loyn,diagonal='boxplot')
```

![plot of chunk unnamed-chunk-29](figure/unnamed-chunk-29-1.png) 

```r
#try to transform some of the data to achieve normality
scatterplotMatrix(~ABUND+log10(DIST)+log10(LDIST)+log10(AREA)+GRAZE+ALT+YR.ISOL,data=loyn,diagonal='boxplot')
```

![plot of chunk unnamed-chunk-29](figure/unnamed-chunk-29-2.png) 

Now make a model

```r
loyn.lm<-lm(ABUND~log10(DIST)+log10(LDIST)+log10(AREA)+GRAZE+ALT+YR.ISOL,data=loyn,
            na.action=na.fail)
```

And check the variance inflation

```r
vif(loyn.lm)
```

```
##  log10(DIST) log10(LDIST)  log10(AREA)        GRAZE          ALT 
##     1.654553     2.009749     1.911514     2.524814     1.467937 
##      YR.ISOL 
##     1.804769
```

Produce the residual plots

```r
par(mfrow=c(2,3),oma=c(0,0,2,0))
plot(loyn.lm,ask=F,which=1:6)
```

![plot of chunk unnamed-chunk-32](figure/unnamed-chunk-32-1.png) 
So looks OK

Get a summary of the model

```r
summary(loyn.lm)
```

```
## 
## Call:
## lm(formula = ABUND ~ log10(DIST) + log10(LDIST) + log10(AREA) + 
##     GRAZE + ALT + YR.ISOL, data = loyn, na.action = na.fail)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -15.6506  -2.9390   0.5289   2.5353  15.2842 
## 
## Coefficients:
##                Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  -125.69725   91.69228  -1.371   0.1767    
## log10(DIST)    -0.90696    2.67572  -0.339   0.7361    
## log10(LDIST)   -0.64842    2.12270  -0.305   0.7613    
## log10(AREA)     7.47023    1.46489   5.099 5.49e-06 ***
## GRAZE          -1.66774    0.92993  -1.793   0.0791 .  
## ALT             0.01951    0.02396   0.814   0.4195    
## YR.ISOL         0.07387    0.04520   1.634   0.1086    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 6.384 on 49 degrees of freedom
## Multiple R-squared:  0.6849,	Adjusted R-squared:  0.6464 
## F-statistic: 17.75 on 6 and 49 DF,  p-value: 8.443e-11
```
Here, the estimates, the **partial slopes**, are the relationship between **each individual** predictor variable and the respones at the **means** of the other predictor variables. 

The next step model selection. One way to do this is with the AIC. The smaller the AIC, the better

```r
AICc(loyn.lm)
```

```
## Error in eval(expr, envir, enclos): could not find function "AICc"
```
Need package MuMIn


Run the function dredge (needs the command na.action = na.fail in the model call)

```r
dredge(loyn.lm,rank="AICc")
```

```
## Global model call: lm(formula = ABUND ~ log10(DIST) + log10(LDIST) + log10(AREA) + 
##     GRAZE + ALT + YR.ISOL, data = loyn, na.action = na.fail)
## ---
## Model selection table 
##       (Int)     ALT    GRA l10(ARE) l10(DIS) l10(LDI)  YR.ISO df   logLik
## 39 -134.300         -1.902    7.166                   0.07835  5 -180.555
## 40 -141.900 0.02586 -1.601    7.076                   0.07991  6 -179.761
## 7    21.600         -2.854    6.890                            4 -182.257
## 55 -113.400         -1.842    7.751           -1.6460 0.06941  6 -180.036
## 47 -120.500         -1.939    7.487  -2.0480          0.07354  6 -180.072
## 23   25.740         -2.630    7.709           -2.1900          5 -181.347
## 38 -236.700 0.03623           8.150                   0.12480  5 -181.428
## 8    17.280 0.02468 -2.584    6.799                            5 -181.577
## 15   26.630         -2.827    7.298  -2.4750                   5 -181.582
## 37 -252.200                   8.593                   0.13520  4 -182.993
## 48 -131.800 0.02145 -1.676    7.295  -1.3030          0.07658  7 -179.585
## 56 -127.700 0.02081 -1.624    7.448           -0.9973 0.07419  7 -179.597
## 24   22.020 0.01615 -2.502    7.472           -1.7160          6 -181.093
## 53 -223.900                   9.213           -1.8930 0.12290  5 -182.360
## 63 -111.800         -1.884    7.756  -1.2570  -1.1060 0.06939  7 -179.908
## 31   27.280         -2.671    7.715  -1.2610  -1.6480          6 -181.225
## 16   22.110 0.01850 -2.632    7.126  -1.8480                   6 -181.238
## 54 -225.800 0.03206           8.479           -0.8482 0.12050  6 -181.316
## 46 -233.700 0.03413           8.294  -0.6964          0.12420  6 -181.380
## 45 -241.900                   8.904  -1.8350          0.13190  5 -182.638
## 64 -125.700 0.01951 -1.668    7.470  -0.9070  -0.6484 0.07387  8 -179.532
## 61 -224.400                   9.235  -0.7046  -1.5930 0.12360  6 -182.322
## 32   23.560 0.01474 -2.546    7.497  -0.9966  -1.3290          7 -181.018
## 62 -226.000 0.03180           8.492  -0.2380  -0.7556 0.12070  7 -181.312
## 6     3.959 0.04862           9.062                            4 -187.342
## 21   18.670                  10.750           -3.6070          4 -187.614
## 22   11.220 0.03555           9.907           -2.4110          5 -186.548
## 14    6.929 0.04490           9.300  -1.1950                   5 -187.226
## 5    10.400                   9.778                            3 -189.659
## 29   18.790                  10.760  -0.1055  -3.5640          5 -187.613
## 30   10.670 0.03599           9.879   0.4070  -2.5640          6 -186.537
## 13   16.130                  10.200  -2.7660                   4 -189.012
## 3    34.370         -4.981                                     3 -194.315
## 4    28.560 0.03191 -4.597                                     4 -193.573
## 20   19.190 0.04441 -4.410                     2.7440          5 -192.507
## 19   29.780         -4.958                     1.7750          4 -193.838
## 35  -62.750         -4.440                            0.04898  4 -193.886
## 52 -135.300 0.04913 -3.497                     3.4700 0.07654  6 -191.457
## 11   32.550         -4.950            0.7777                   4 -194.268
## 36  -73.580 0.03285 -4.017                            0.05143  5 -193.087
## 51 -100.100         -4.234                     2.3020 0.06484  5 -193.112
## 12   23.010 0.03793 -4.448            1.9060                   5 -193.314
## 28   19.760 0.04392 -4.429           -0.3745   2.8950          6 -192.500
## 27   31.190         -4.997           -1.1520   2.2720          5 -193.772
## 43  -72.190         -4.356            1.1420          0.05240  5 -193.786
## 44  -95.930 0.04056 -3.742            2.3960          0.05917  6 -192.681
## 60 -134.700 0.04875 -3.513           -0.2841   3.5830 0.07645  7 -191.452
## 59  -98.700         -4.274           -1.1480   2.7980 0.06482  6 -193.046
## 50 -389.500 0.08802                            5.4380 0.19610  5 -197.226
## 58 -386.700 0.08918                   1.5880   4.7540 0.19360  6 -197.115
## 42 -356.500 0.08167                   5.3870          0.18060  5 -198.902
## 34 -348.500 0.07006                                   0.18350  4 -200.670
## 49 -429.000                                    3.8190 0.22510  4 -202.119
## 33 -392.300                                           0.21120  3 -203.690
## 41 -402.400                           3.5440          0.21230  4 -202.981
## 57 -428.200                           0.5653   3.5680 0.22430  5 -202.107
## 18   -8.295 0.11150                            4.5200          4 -205.516
## 10   -8.908 0.10710                   5.7560                   4 -205.772
## 2     5.598 0.09515                                            3 -207.358
## 26  -12.280 0.11330                   3.2660   3.1370          5 -205.162
## 1    19.510                                                    2 -211.871
## 9    12.230                           3.2870                   3 -211.418
## 17   13.900                                    2.2060          3 -211.478
## 25   11.390                           2.2620   1.2230          4 -211.340
##     AICc delta weight
## 39 372.3  0.00  0.147
## 40 373.2  0.93  0.092
## 7  373.3  0.99  0.090
## 55 373.8  1.48  0.070
## 47 373.9  1.55  0.068
## 23 373.9  1.59  0.066
## 38 374.1  1.75  0.061
## 8  374.4  2.05  0.053
## 15 374.4  2.06  0.053
## 37 374.8  2.46  0.043
## 48 375.5  3.19  0.030
## 56 375.5  3.22  0.029
## 24 375.9  3.59  0.024
## 53 375.9  3.61  0.024
## 63 376.1  3.84  0.022
## 31 376.2  3.86  0.021
## 16 376.2  3.88  0.021
## 54 376.3  4.04  0.020
## 46 376.5  4.16  0.018
## 45 376.5  4.17  0.018
## 64 378.1  5.82  0.008
## 61 378.4  6.05  0.007
## 32 378.4  6.06  0.007
## 62 379.0  6.65  0.005
## 6  383.5 11.16  0.001
## 21 384.0 11.70  0.000
## 22 384.3 11.99  0.000
## 14 385.7 13.34  0.000
## 5  385.8 13.47  0.000
## 29 386.4 14.12  0.000
## 30 386.8 14.48  0.000
## 13 386.8 14.50  0.000
## 3  395.1 22.78  0.000
## 4  395.9 23.62  0.000
## 20 396.2 23.91  0.000
## 19 396.5 24.15  0.000
## 35 396.6 24.25  0.000
## 52 396.6 24.32  0.000
## 11 397.3 25.01  0.000
## 36 397.4 25.07  0.000
## 51 397.4 25.11  0.000
## 12 397.8 25.52  0.000
## 28 398.7 26.41  0.000
## 27 398.7 26.44  0.000
## 43 398.8 26.46  0.000
## 44 399.1 26.77  0.000
## 60 399.2 26.93  0.000
## 59 399.8 27.50  0.000
## 50 405.7 33.34  0.000
## 58 407.9 35.64  0.000
## 42 409.0 36.70  0.000
## 34 410.1 37.82  0.000
## 49 413.0 40.71  0.000
## 33 413.8 41.53  0.000
## 41 414.7 42.44  0.000
## 57 415.4 43.10  0.000
## 18 419.8 47.51  0.000
## 10 420.3 48.02  0.000
## 2  421.2 48.87  0.000
## 26 421.5 49.21  0.000
## 1  428.0 55.66  0.000
## 9  429.3 56.99  0.000
## 17 429.4 57.11  0.000
## 25 431.5 59.16  0.000
```
This will show you the best model based on the AIC, and also the weights for each model, which can be used for model averaging (the best slope can be the **average** of the slopes predicted by different models)

```r
loyn.av<-model.avg(dredge(loyn.lm,rank="AICc"),
                   subset=delta<=2)
```
This now only takes into account those models which have a weight higer then 2.

Get average coefficients

```r
summary(loyn.av)
```

```
## 
## Call:
## model.avg.model.selection(object = dredge(loyn.lm, rank = "AICc"), 
##     subset = delta <= 2)
## 
## Component models: 
##      df  logLik   AICc Delta Weight
## 236   5 -180.55 372.31  0.00   0.25
## 1236  6 -179.76 373.24  0.93   0.16
## 23    4 -182.26 373.30  0.99   0.15
## 2356  6 -180.04 373.79  1.48   0.12
## 2346  6 -180.07 373.86  1.55   0.11
## 235   5 -181.35 373.89  1.59   0.11
## 136   5 -181.43 374.06  1.75   0.10
## 
## Term codes: 
##          ALT        GRAZE  log10(AREA)  log10(DIST) log10(LDIST) 
##            1            2            3            4            5 
##      YR.ISOL 
##            6 
## 
## Model-averaged coefficients: 
##                Estimate Std. Error Adjusted SE z value Pr(>|z|)    
## (Intercept)  -100.59988  108.90653   110.08735   0.914   0.3608    
## GRAZE          -2.09737    0.94820     0.96603   2.171   0.0299 *  
## log10(AREA)     7.37827    1.35872     1.38859   5.313    1e-07 ***
## YR.ISOL         0.08300    0.04594     0.04690   1.770   0.0767 .  
## ALT             0.03000    0.02180     0.02230   1.345   0.1785    
## log10(LDIST)   -1.91066    1.70048     1.74037   1.098   0.2723    
## log10(DIST)    -2.04783    2.17582     2.22869   0.919   0.3582    
## 
## Full model-averaged coefficients (with shrinkage): 
##                Estimate Std. Error Adjusted SE z value Pr(>|z|)    
## (Intercept)  -1.006e+02  1.089e+02   1.101e+02   0.914   0.3608    
## GRAZE        -1.881e+00  1.101e+00   1.115e+00   1.687   0.0917 .  
## log10(AREA)   7.378e+00  1.359e+00   1.389e+00   5.313    1e-07 ***
## YR.ISOL       6.121e-02  5.376e-02   5.437e-02   1.126   0.2602    
## ALT           7.753e-03  1.718e-02   1.735e-02   0.447   0.6549    
## log10(LDIST) -4.393e-01  1.145e+00   1.159e+00   0.379   0.7046    
## log10(DIST)  -2.331e-01  9.807e-01   9.942e-01   0.234   0.8146    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Relative variable importance: 
##                      log10(AREA) GRAZE YR.ISOL ALT  log10(LDIST)
## Importance:          1.00        0.90  0.74    0.26 0.23        
## N containing models:    7           6     5       2    2        
##                      log10(DIST)
## Importance:          0.11       
## N containing models:    1
```

Plot the added-variable plots

```r
avPlots(loyn.lm,ask=F)
```

![plot of chunk unnamed-chunk-39](figure/unnamed-chunk-39-1.png) 

The log10(AREA) plot shows the partial effect of area on abundance. The red line is the predicted trend if you hold all the other variables constant. The dots are the data that you would get if you held all of the other data constant. 

Other way of showing this is in effects


Plot data

```r
plot(allEffects(loyn.lm,default.lecels=1000,ask=F))
```

![plot of chunk unnamed-chunk-41](figure/unnamed-chunk-41-1.png) 

Plot only the model that included important predictor variables

```r
loyn.lm1<-lm(ABUND~log10(AREA)+GRAZE,data=loyn)
plot(allEffects(loyn.lm1,default.lecels=1000,ask=F))
```

![plot of chunk unnamed-chunk-42](figure/unnamed-chunk-42-1.png) 

Get the effect of area at different grazing intesities

```r
newdata<-expand.grid(AREA=seq(1,2000,len=100),GRAZE=1:5)
#expand.grid repeats everything for each grazing intensity.
#This command should make us end up with a data frame with 500 rows
head(newdata)
```

```
##        AREA GRAZE
## 1   1.00000     1
## 2  21.19192     1
## 3  41.38384     1
## 4  61.57576     1
## 5  81.76768     1
## 6 101.95960     1
```

```r
pred<-predict(loyn.lm1,newdata=newdata,interval='confidence')
newdata<-cbind(newdata,pred)
head(newdata)
```

```
##        AREA GRAZE      fit      lwr      upr
## 1   1.00000     1 18.74931 13.80348 23.69515
## 2  21.19192     1 27.88673 24.91726 30.85620
## 3  41.38384     1 29.88941 26.96787 32.81094
## 4  61.57576     1 31.07849 28.09470 34.06227
## 5  81.76768     1 31.92715 28.85971 34.99460
## 6 101.95960     1 32.58754 29.43418 35.74090
```

```r
ggplot(newdata,aes(y=fit,x=AREA)) +
  geom_ribbon(aes(ymin=lwr,ymax=upr))
```

![plot of chunk unnamed-chunk-43](figure/unnamed-chunk-43-1.png) 

```r
ggplot(newdata,aes(y=fit,x=AREA,group=GRAZE,fill=factor(GRAZE),color=factor(GRAZE))) + geom_ribbon(aes(ymin=lwr,ymax=upr),alpha=0.2) + geom_line()
```

![plot of chunk unnamed-chunk-43](figure/unnamed-chunk-43-2.png) 

Get the predicted fitted values how area affects the response for each grazing cat.

```r
ggplot(newdata,aes(y=fit,x=AREA))+facet_grid(~GRAZE) + geom_line() +
  geom_ribbon(aes(ymin=lwr,ymax=upr),alpha=0.2)
```

![plot of chunk unnamed-chunk-44](figure/unnamed-chunk-44-1.png) 

During the reporting of the data it is no longer required to show the best-fitting model. It is even possible to discuss multiple models.


===============================================

Running an Anova


```r
day<-read.csv('day.csv',strip.white=T)
head(day)
```

```
##   TREAT BARNACLE
## 1  ALG1       27
## 2  ALG1       19
## 3  ALG1       18
## 4  ALG1       23
## 5  ALG1       25
## 6  ALG2       24
```

```r
summary(day)
```

```
##   TREAT      BARNACLE    
##  ALG1:5   Min.   : 8.00  
##  ALG2:5   1st Qu.:13.75  
##  NB  :5   Median :19.50  
##  S   :5   Mean   :19.75  
##           3rd Qu.:25.25  
##           Max.   :33.00
```

```r
boxplot(BARNACLE~TREAT,data=day)
```

![plot of chunk unnamed-chunk-45](figure/unnamed-chunk-45-1.png) 

To model an ANOVA just make a linear model. It has grown out of fashion to use aov()

```r
day.lm<-lm(BARNACLE~TREAT,data=day)
par(mfrow=c(1,2))
plot(day.lm,ask=F,which=1:2)
```

![plot of chunk unnamed-chunk-46](figure/unnamed-chunk-46-1.png) 

Make sure residuals are centered. You can also plot the residuals against predictors that have not been included in the model (e.g. time) and look for patterns.


```r
summary(day.lm)
```

```
## 
## Call:
## lm(formula = BARNACLE ~ TREAT, data = day)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
##  -6.00  -2.65  -1.10   2.85   7.00 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   22.400      1.927  11.622 3.27e-09 ***
## TREATALG2      6.000      2.726   2.201  0.04275 *  
## TREATNB       -7.400      2.726  -2.715  0.01530 *  
## TREATS        -9.200      2.726  -3.375  0.00386 ** 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 4.31 on 16 degrees of freedom
## Multiple R-squared:  0.7125,	Adjusted R-squared:  0.6586 
## F-statistic: 13.22 on 3 and 16 DF,  p-value: 0.0001344
```

The intercept is average effect of the first treatment group. The second estimate (TREATALG2) is the effect of the second treatment group **compared** to treatment group 1.

Can also get the confidence intervals

```r
confint(day.lm)
```

```
##                   2.5 %    97.5 %
## (Intercept)  18.3140235 26.485977
## TREATALG2     0.2215566 11.778443
## TREATNB     -13.1784434 -1.621557
## TREATS      -14.9784434 -3.421557
```

Do pair-wise comparisons and corrections




```r
summary(glht(day.lm,linfct=mcp(TREAT="Tukey")))
```

```
## 
## 	 Simultaneous Tests for General Linear Hypotheses
## 
## Multiple Comparisons of Means: Tukey Contrasts
## 
## 
## Fit: lm(formula = BARNACLE ~ TREAT, data = day)
## 
## Linear Hypotheses:
##                  Estimate Std. Error t value Pr(>|t|)    
## ALG2 - ALG1 == 0    6.000      2.726   2.201   0.1653    
## NB - ALG1 == 0     -7.400      2.726  -2.715   0.0661 .  
## S - ALG1 == 0      -9.200      2.726  -3.375   0.0181 *  
## NB - ALG2 == 0    -13.400      2.726  -4.916   <0.001 ***
## S - ALG2 == 0     -15.200      2.726  -5.576   <0.001 ***
## S - NB == 0        -1.800      2.726  -0.660   0.9103    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## (Adjusted p values reported -- single-step method)
```

These comparisons should be independent of each other (autoginal)


**I missed a section here**

-----------------------------
**Multi-factor ANOVA**

Factorial ANOVA includes an interaction. There are different possible scenarios. 


```r
star<-read.csv('starling.csv',strip.white=T)
head(star)
```

```
##   SITUATION    MONTH MASS GROUP
## 1        S1 November   78 S1Nov
## 2        S1 November   88 S1Nov
## 3        S1 November   87 S1Nov
## 4        S1 November   88 S1Nov
## 5        S1 November   83 S1Nov
## 6        S1 November   82 S1Nov
```

```r
summary(star)
```

```
##  SITUATION      MONTH         MASS            GROUP   
##  S1:20     January :40   Min.   : 68.00   S1Jan  :10  
##  S2:20     November:40   1st Qu.: 78.75   S1Nov  :10  
##  S3:20                   Median : 84.00   S2Jan  :10  
##  S4:20                   Mean   : 83.80   S2Nov  :10  
##                          3rd Qu.: 88.00   S3Jan  :10  
##                          Max.   :100.00   S3Nov  :10  
##                                           (Other):20
```

```r
boxplot(MASS~SITUATION*MONTH,data=star)
```

![plot of chunk unnamed-chunk-51](figure/unnamed-chunk-51-1.png) 

There are 8 combinations. Each combination has to be normally distributed. It looks good.

An ANOVA table shows how much of the variation in your data is explained by your predictor variables and their interaction. As the explained and unexplained variation are expressed in terms of each other, the replications need to be balanced (e.g. equal sample sizes). Look at this with

```r
replications(MASS~SITUATION*MONTH,data=star)
```

```
##       SITUATION           MONTH SITUATION:MONTH 
##              20              40              10
```

So this dataset is balanced

Fit the model

```r
star.lm<-lm(MASS~SITUATION*MONTH,data=star)
```

And look at diagnostic plots

```r
par(mfrow=c(2,1))
plot(star.lm,ask=F,which=1:2)
```

![plot of chunk unnamed-chunk-54](figure/unnamed-chunk-54-1.png) 

In case of the following error:
Error in plot.new() : figure margins too large


```r
dev.off()
```

```
## null device 
##           1
```

And run again

Get the interaction plot and stats

```r
with(star,interaction.plot(SITUATION,MONTH,MASS))
```

![plot of chunk unnamed-chunk-56](figure/unnamed-chunk-56-1.png) 

```r
summary(star.lm)
```

```
## 
## Call:
## lm(formula = MASS ~ SITUATION * MONTH, data = star)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
##   -7.4   -3.2   -0.4    2.9    9.2 
## 
## Coefficients:
##                           Estimate Std. Error t value Pr(>|t|)    
## (Intercept)                 90.800      1.330  68.260  < 2e-16 ***
## SITUATIONS2                 -0.600      1.881  -0.319 0.750691    
## SITUATIONS3                 -2.600      1.881  -1.382 0.171213    
## SITUATIONS4                 -6.600      1.881  -3.508 0.000781 ***
## MONTHNovember               -7.200      1.881  -3.827 0.000274 ***
## SITUATIONS2:MONTHNovember   -3.600      2.660  -1.353 0.180233    
## SITUATIONS3:MONTHNovember   -2.400      2.660  -0.902 0.370003    
## SITUATIONS4:MONTHNovember   -1.600      2.660  -0.601 0.549455    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 4.206 on 72 degrees of freedom
## Multiple R-squared:   0.64,	Adjusted R-squared:  0.605 
## F-statistic: 18.28 on 7 and 72 DF,  p-value: 9.546e-14
```

Intercept here represents the mean Mass of SITUATIONS1 in Jan, this is the first possible combination.
Estimate of SITUATIONS2 is the difference compared to SITUATIONS1 in Jan. MONTHNovember is the difference in Mass between S1 in Jan and Nov. 
If there is no interaction with month, the slopes of Jan and Nov are parallel. The SITUATIONS2:MONTHNovember is the difference in slope in S2 compared to the situation where the slopes would be parallel.


```r
summary(glht(something))
```

```
## Error in modelparm(model, ...): object 'something' not found
```


Another dataset

```r
quinn<-read.csv('quinn.csv',strip.white=T)
head(quinn)
```

```
##   SEASON DENSITY RECRUITS SQRTRECRUITS      GROUP
## 1 Spring     Low       15     3.872983  SpringLow
## 2 Spring     Low       10     3.162278  SpringLow
## 3 Spring     Low       13     3.605551  SpringLow
## 4 Spring     Low       13     3.605551  SpringLow
## 5 Spring     Low        5     2.236068  SpringLow
## 6 Spring    High       11     3.316625 SpringHigh
```

```r
summary(quinn)
```

```
##     SEASON   DENSITY      RECRUITS      SQRTRECRUITS          GROUP  
##  Autumn:10   High:24   Min.   : 0.00   Min.   :0.000   AutumnHigh:6  
##  Spring:11   Low :18   1st Qu.: 9.25   1st Qu.:3.041   SpringHigh:6  
##  Summer:12             Median :13.50   Median :3.674   SummerHigh:6  
##  Winter: 9             Mean   :18.33   Mean   :3.871   SummerLow :6  
##                        3rd Qu.:21.75   3rd Qu.:4.663   WinterHigh:6  
##                        Max.   :69.00   Max.   :8.307   SpringLow :5  
##                                                        (Other)   :7
```

We need normality, equal variance, balance and indendence. The boxplot will show the first 2. 

```r
boxplot(SQRTRECRUITS~SEASON*DENSITY,data=quinn)
```

![plot of chunk unnamed-chunk-59](figure/unnamed-chunk-59-1.png) 

To get the balance

```r
replications(SQRTRECRUITS~SEASON*DENSITY,data=quinn)
```

```
## $SEASON
## SEASON
## Autumn Spring Summer Winter 
##     10     11     12      9 
## 
## $DENSITY
## DENSITY
## High  Low 
##   24   18 
## 
## $`SEASON:DENSITY`
##         DENSITY
## SEASON   High Low
##   Autumn    6   4
##   Spring    6   5
##   Summer    6   6
##   Winter    6   3
```

So this is considered to be an unbalanced design.
The consequences are as follows. We make 2 models which should be exactly the same

```r
quin.lm1<-lm(SQRTRECRUITS~SEASON*DENSITY,data=quinn)
quin.lm2<-lm(SQRTRECRUITS~DENSITY*SEASON,data=quinn)
```

Which gives the outputs

```r
anova(quin.lm1)
```

```
## Analysis of Variance Table
## 
## Response: SQRTRECRUITS
##                Df Sum Sq Mean Sq F value    Pr(>F)    
## SEASON          3 87.454 29.1512 28.5740 2.067e-09 ***
## DENSITY         1  7.089  7.0894  6.9491   0.01255 *  
## SEASON:DENSITY  3 11.354  3.7848  3.7098   0.02068 *  
## Residuals      34 34.687  1.0202                      
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

```r
anova(quin.lm2)
```

```
## Analysis of Variance Table
## 
## Response: SQRTRECRUITS
##                Df Sum Sq Mean Sq F value    Pr(>F)    
## DENSITY         1  2.854  2.8541  2.7976   0.10359    
## SEASON          3 91.689 30.5630 29.9579 1.165e-09 ***
## DENSITY:SEASON  3 11.354  3.7848  3.7098   0.02068 *  
## Residuals      34 34.687  1.0202                      
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

However, if we ask for a summary

```r
summary(quin.lm1)
```

```
## 
## Call:
## lm(formula = SQRTRECRUITS ~ SEASON * DENSITY, data = quinn)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -2.2298 -0.5977  0.1384  0.5489  1.8856 
## 
## Coefficients:
##                         Estimate Std. Error t value Pr(>|t|)    
## (Intercept)              4.22979    0.41235  10.258 6.06e-12 ***
## SEASONSpring            -1.20984    0.58315  -2.075  0.04566 *  
## SEASONSummer             2.63583    0.58315   4.520 7.14e-05 ***
## SEASONWinter            -1.95739    0.58315  -3.357  0.00195 ** 
## DENSITYLow               0.02996    0.65198   0.046  0.96362    
## SEASONSpring:DENSITYLow  0.24657    0.89396   0.276  0.78436    
## SEASONSummer:DENSITYLow -2.24336    0.87473  -2.565  0.01492 *  
## SEASONWinter:DENSITYLow -1.35956    0.96705  -1.406  0.16883    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1.01 on 34 degrees of freedom
## Multiple R-squared:  0.7533,	Adjusted R-squared:  0.7025 
## F-statistic: 14.83 on 7 and 34 DF,  p-value: 1.097e-08
```

```r
summary(quin.lm2)
```

```
## 
## Call:
## lm(formula = SQRTRECRUITS ~ DENSITY * SEASON, data = quinn)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -2.2298 -0.5977  0.1384  0.5489  1.8856 
## 
## Coefficients:
##                         Estimate Std. Error t value Pr(>|t|)    
## (Intercept)              4.22979    0.41235  10.258 6.06e-12 ***
## DENSITYLow               0.02996    0.65198   0.046  0.96362    
## SEASONSpring            -1.20984    0.58315  -2.075  0.04566 *  
## SEASONSummer             2.63583    0.58315   4.520 7.14e-05 ***
## SEASONWinter            -1.95739    0.58315  -3.357  0.00195 ** 
## DENSITYLow:SEASONSpring  0.24657    0.89396   0.276  0.78436    
## DENSITYLow:SEASONSummer -2.24336    0.87473  -2.565  0.01492 *  
## DENSITYLow:SEASONWinter -1.35956    0.96705  -1.406  0.16883    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1.01 on 34 degrees of freedom
## Multiple R-squared:  0.7533,	Adjusted R-squared:  0.7025 
## F-statistic: 14.83 on 7 and 34 DF,  p-value: 1.097e-08
```

So if I am **not** interested in an anova table, balance doesn't matter!

So the question is, do we need to look at the anova table? If we do, we need to ask for **Type 3 Sums of Squares**. First of all, before these can be fitted, we need to first make sure not every group is compared to the first group. So we need to make a contrast which is not the treatment

```r
contrasts(quinn$SEASON) <-contr.sum
contrasts(quinn$DENSITY) <- contr.sum
quin.lm <- lm(SQRTRECRUITS~DENSITY*SEASON,data=quinn)
Anova(quin.lm,type='III')
```

```
## Anova Table (Type III tests)
## 
## Response: SQRTRECRUITS
##                Sum Sq Df  F value    Pr(>F)    
## (Intercept)    539.72  1 529.0381 < 2.2e-16 ***
## DENSITY          6.48  1   6.3510   0.01659 *  
## SEASON          90.64  3  29.6135 1.341e-09 ***
## DENSITY:SEASON  11.35  3   3.7098   0.02068 *  
## Residuals       34.69 34                       
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

Conclusion is that there is a significant interaction between SEASON and DENSITY (this procedure will take some of the interaction effect away). Show this interaction

```r
with(quinn,interaction.plot(SEASON,DENSITY,SQRTRECRUITS))
```

![plot of chunk unnamed-chunk-65](figure/unnamed-chunk-65-1.png) 

So what do we do now? We need to divide the data. We can do 2 seperate ANOVAs, one for each density. Or we can run 4, 1 for each season.


```r
data.lm.summer <- lm(SQRTRECRUITS~DENSITY,data=subset(quinn,SEASON=='Summer'))
anova(data.lm.summer)
```

```
## Analysis of Variance Table
## 
## Response: SQRTRECRUITS
##           Df  Sum Sq Mean Sq F value  Pr(>F)   
## DENSITY    1 14.6974  14.697  17.665 0.00182 **
## Residuals 10  8.3199   0.832                   
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

```r
data.lm.autumn <- lm(SQRTRECRUITS~DENSITY,data=subset(quinn,SEASON=='Autumn'))
anova(data.lm.autumn)
```

```
## Analysis of Variance Table
## 
## Response: SQRTRECRUITS
##           Df  Sum Sq Mean Sq F value Pr(>F)
## DENSITY    1  0.0022 0.00215  0.0016 0.9695
## Residuals  8 11.0713 1.38391
```

```r
data.lm.spring <- lm(SQRTRECRUITS~DENSITY,data=subset(quinn,SEASON=='Spring'))
anova(data.lm.spring)
```

```
## Analysis of Variance Table
## 
## Response: SQRTRECRUITS
##           Df Sum Sq Mean Sq F value Pr(>F)
## DENSITY    1 0.2086 0.20856  0.2703 0.6157
## Residuals  9 6.9452 0.77169
```

```r
data.lm.winter <- lm(SQRTRECRUITS~DENSITY,data=subset(quinn,SEASON=='Winter'))
anova(data.lm.winter)
```

```
## Analysis of Variance Table
## 
## Response: SQRTRECRUITS
##           Df Sum Sq Mean Sq F value Pr(>F)
## DENSITY    1 3.5356  3.5356  2.9638 0.1288
## Residuals  7 8.3505  1.1929
```

This is a perfectly fine way to deal with complex datasets


----------------------------------------------
**Dealing with Heterogeneity**


```r
d2<-read.csv('D2.csv',strip.white=T)
head(d2)
```

```
##          y x
## 1 36.24128 A
## 2 41.10186 A
## 3 34.98623 A
## 4 49.57168 A
## 5 41.97705 A
## 6 35.07719 A
```

```r
summary(d2)
```

```
##        y         x     
##  Min.   :29.29   A:10  
##  1st Qu.:36.17   B:10  
##  Median :40.89   C:10  
##  Mean   :42.34   D:10  
##  3rd Qu.:49.32   E:10  
##  Max.   :56.84
```

```r
boxplot(y~x,data=d2)
```

![plot of chunk unnamed-chunk-67](figure/unnamed-chunk-67-1.png) 

There is not necessarily a problem with normality. However, the variance between the groups is quite different (variance of A is much larger than D).

First let's see what happens if we analyze it the standard way

```r
d2.lm <- lm (y~x, data=d2)
plot(d2.lm,ask=F,which=1:2)
```

![plot of chunk unnamed-chunk-68](figure/unnamed-chunk-68-1.png) ![plot of chunk unnamed-chunk-68](figure/unnamed-chunk-68-2.png) 

We are going to estimate the Maximum Likelihood off the data. Instead of lm we're going to use GLS. A GLS will first run an lm and then re-run the analysis to estimate the random effects. Finally, it will run again to update the fixed effects.


The difference between Maximum Likelyhood and Restricted ML: RML is more appropriate for estimating random effects. Residuals and variances are random effects. We want to estimate our variances correctly and thus we need to use RML.


```r
d2.gls <- gls(y~x, data=d2, method='REML')
summary(d2.gls)
```

```
## Generalized least squares fit by REML
##   Model: y ~ x 
##   Data: d2 
##        AIC      BIC    logLik
##   249.4968 260.3368 -118.7484
## 
## Coefficients:
##                 Value Std.Error  t-value p-value
## (Intercept)  40.79322 0.9424249 43.28538  0.0000
## xB            5.20216 1.3327901  3.90321  0.0003
## xC           13.93944 1.3327901 10.45884  0.0000
## xD           -0.73285 1.3327901 -0.54986  0.5851
## xE          -10.65908 1.3327901 -7.99757  0.0000
## 
##  Correlation: 
##    (Intr) xB     xC     xD    
## xB -0.707                     
## xC -0.707  0.500              
## xD -0.707  0.500  0.500       
## xE -0.707  0.500  0.500  0.500
## 
## Standardized residuals:
##         Min          Q1         Med          Q3         Max 
## -3.30653942 -0.24626108  0.06468242  0.39046918  2.94558782 
## 
## Residual standard error: 2.980209 
## Degrees of freedom: 50 total; 45 residual
```

```r
anova(d2.gls)
```

```
## Denom. DF: 45 
##             numDF   F-value p-value
## (Intercept)     1 10093.524  <.0001
## x               4    91.062  <.0001
```

When looking at residual plots, it is the standardized residuals we should care about more.

```r
plot(residuals(d2.gls,'normalized')~fitted(d2.gls))
```

![plot of chunk unnamed-chunk-71](figure/unnamed-chunk-71-1.png) 

Now let's do it with the correct correction. Instead of using the same variance each time, we're going to change the variance using the weights. This is how to correct for unequal variance. It will take the standard variance co-variance matrix and will multiply it by weights, which will tell it to produce a seperate co-variance matrix for each level of x. 


```r
d2.gls1<-gls(y~x, data=d2, weights=varIdent(form=~1|x), method='REML')
```

If we use the normal residual plot, it looks like we didn't do anything.

```r
plot(residuals(d2.gls1,)~fitted(d2.gls1))
```

![plot of chunk unnamed-chunk-73](figure/unnamed-chunk-73-1.png) 

However, if we look at the **standardized residual plot** we  see a difference

```r
plot(residuals(d2.gls1,'normalized')~fitted(d2.gls1))
```

![plot of chunk unnamed-chunk-74](figure/unnamed-chunk-74-1.png) 

Now look at the different levels of x

```r
summary(d2.gls1)
```

```
## Generalized least squares fit by REML
##   Model: y ~ x 
##   Data: d2 
##        AIC      BIC    logLik
##   199.2087 217.2753 -89.60435
## 
## Variance function:
##  Structure: Different standard deviations per stratum
##  Formula: ~1 | x 
##  Parameter estimates:
##          A          B          C          D          E 
## 1.00000000 0.91342375 0.40807004 0.08631969 0.12720393 
## 
## Coefficients:
##                 Value Std.Error   t-value p-value
## (Intercept)  40.79322  1.481066 27.543153  0.0000
## xB            5.20216  2.005924  2.593399  0.0128
## xC           13.93944  1.599634  8.714142  0.0000
## xD           -0.73285  1.486573 -0.492981  0.6244
## xE          -10.65908  1.493000 -7.139371  0.0000
## 
##  Correlation: 
##    (Intr) xB     xC     xD    
## xB -0.738                     
## xC -0.926  0.684              
## xD -0.996  0.736  0.922       
## xE -0.992  0.732  0.918  0.988
## 
## Standardized residuals:
##        Min         Q1        Med         Q3        Max 
## -2.3034239 -0.6178652  0.1064904  0.7596732  1.8743230 
## 
## Residual standard error: 4.683541 
## Degrees of freedom: 50 total; 45 residual
```

Now, was this worth doing? The **wrong** way is to look if this is the result I wanted. We need to look at the AIC


```r
AIC(d2.gls,d2.gls1)
```

```
##         df      AIC
## d2.gls   6 249.4968
## d2.gls1 10 199.2087
```

So it is better to fit seperate variance in this case. However, it was without cost, as the df is lower. AIC does correct for this, so it tells you it was worth correction, despite the cost.

We can also compare the logLikelihood

```r
anova(d2.gls,d2.gls1)
```

```
##         Model df      AIC      BIC     logLik   Test  L.Ratio p-value
## d2.gls      1  6 249.4968 260.3368 -118.74841                        
## d2.gls1     2 10 199.2087 217.2753  -89.60435 1 vs 2 58.28812  <.0001
```

This test confirms that the logLikelihood is also higher for the corrected model, and so we confirm it is a good step to correct for this.


Day 2
=====================================
**Linear Hierachical Models**

This is a linear model with separete covariance structure per block, and has fixed and random effects


```r
curdies <- read.csv('curdies.csv',strip.white=T)
head(curdies)
```

```
##   SEASON SITE   DUGESIA   S4DUGES
## 1 WINTER    1 0.6476829 0.8970995
## 2 WINTER    1 6.0961516 1.5713175
## 3 WINTER    1 1.3105639 1.0699526
## 4 WINTER    1 1.7252788 1.1460797
## 5 WINTER    1 1.4593867 1.0991136
## 6 WINTER    1 1.0575610 1.0140897
```

```r
curdies$SITE <-factor(curdies$SITE)
ggplot(curdies, aes(y=DUGESIA,x=SEASON)) + geom_boxplot() + facet_wrap(~SITE)
```

![plot of chunk unnamed-chunk-78](figure/unnamed-chunk-78-1.png) 

```r
ggplot(curdies, aes(y=S4DUGES,x=SEASON)) + geom_boxplot() + facet_wrap(~SITE)
```

![plot of chunk unnamed-chunk-78](figure/unnamed-chunk-78-2.png) 

```r
ggplot(curdies, aes(y=S4DUGES,x=SEASON)) + geom_boxplot() 
```

![plot of chunk unnamed-chunk-78](figure/unnamed-chunk-78-3.png) 

When testing assumptions, you need to look at the scale of replecations. The latter plot is thus wrong, as it doesn't show the averages for the replicates




```r
curdies.ag <- ddply(curdies, ~SEASON+SITE, numcolwise(mean))
curdies.ag
```

```
##   SEASON SITE   DUGESIA   S4DUGES
## 1 SUMMER    4 0.4190947 0.3508213
## 2 SUMMER    5 0.2290862 0.1804622
## 3 SUMMER    6 0.1942443 0.3811223
## 4 WINTER    1 2.0494375 1.1329421
## 5 WINTER    2 4.1819078 1.2718698
## 6 WINTER    3 0.6782063 0.8678707
```

This will break up the data in groups by SEASON and SITE, can be used for other functions as well, like median


```r
curdies.ag <- ddply(curdies, ~SEASON+SITE, numcolwise(median))
curdies.ag
```

```
##   SEASON SITE   DUGESIA   S4DUGES
## 1 SUMMER    4 0.0000000 0.0000000
## 2 SUMMER    5 0.0000000 0.0000000
## 3 SUMMER    6 0.0664427 0.3018834
## 4 WINTER    1 1.3849753 1.0845331
## 5 WINTER    2 1.5897073 1.1152465
## 6 WINTER    3 0.6730441 0.9057067
```

To compare untransformed and transformed data (accounting for the replications)


```r
ggplot(curdies.ag, aes(y=DUGESIA,x=SEASON)) + geom_boxplot() 
```

![plot of chunk unnamed-chunk-82](figure/unnamed-chunk-82-1.png) 

```r
ggplot(curdies.ag, aes(y=S4DUGES,x=SEASON)) + geom_boxplot() 
```

![plot of chunk unnamed-chunk-82](figure/unnamed-chunk-82-2.png) 

Now model using the linear mixed effects model

```r
curdies.lme <- lme(S4DUGES~SEASON,random=~1|SITE, data=curdies)
```

Main effect is SEASON. By the `random=~1|SITE` part we are accounting for the non-indepence within SITE. It is allowing for random intercept (estimating a seperate mean for each site)

Note that the old function 'lmer' is NOT able to incorporate autocorrelation!

Another option is a random slope model


```r
curdies.lme1 <- lme(S4DUGES~SEASON,random=~SEASON|SITE, data=curdies)
```

This one allows the intercept AND the slope to be random! This means that not only can the sites have different means, also, we are allowing the difference between season to be different between site. What we are interested in is the **overal** difference between season, not the site difference.

What you should do is try to run both versions (random intercept and random slope model) and test which one is better using the AIC. 


```r
AIC(curdies.lme,curdies.lme1)
```

```
##              df      AIC
## curdies.lme   4 46.42966
## curdies.lme1  6 50.10884
```

```r
anova(curdies.lme,curdies.lme1)
```

```
##              Model df      AIC     BIC    logLik   Test   L.Ratio p-value
## curdies.lme      1  4 46.42966 52.5351 -19.21483                         
## curdies.lme1     2  6 50.10884 59.2670 -19.05442 1 vs 2 0.3208206  0.8518
```

Next step is to validate the model.


```r
plot(curdies.lme)
```

![plot of chunk unnamed-chunk-86](figure/unnamed-chunk-86-1.png) 

```r
summary(curdies.lme)
```

```
## Linear mixed-effects model fit by REML
##  Data: curdies 
##        AIC     BIC    logLik
##   46.42966 52.5351 -19.21483
## 
## Random effects:
##  Formula: ~1 | SITE
##         (Intercept)  Residual
## StdDev:  0.04008595 0.3896804
## 
## Fixed effects: S4DUGES ~ SEASON 
##                  Value  Std.Error DF  t-value p-value
## (Intercept)  0.3041353 0.09471949 30 3.210905  0.0031
## SEASONWINTER 0.7867589 0.13395359  4 5.873369  0.0042
##  Correlation: 
##              (Intr)
## SEASONWINTER -0.707
## 
## Standardized Within-Group Residuals:
##        Min         Q1        Med         Q3        Max 
## -1.2064783 -0.7680513 -0.2480589  0.3531126  2.3209353 
## 
## Number of Observations: 36
## Number of Groups: 6
```

`StdDev:  0.04008595 0.3896804`, here `0.04008595` is the variability between sites, and `0.3896804` is the variability between logs.
Intercept here is the **average** intercept (which is the summer mean as being alphabetically first).
SEASONWINTER -0.707 is the correlation between the slopes of the intercepts. In continuous data we can solve this by centering the data, but this is not possible for a categorical dataset like this one. 


```r
anova(curdies.lme)
```

```
##             numDF denDF   F-value p-value
## (Intercept)     1    30 108.45712  <.0001
## SEASON          1     4  34.49647  0.0042
```

We *don't* know what amount degrees of freedom we can use in this test. For this reason, the p-values in this test **don't mean anything!**. A smaller p-value only means that we have used a larger sample size. What we want to know is if there is an effect and the magnitude of that effect.


```r
intervals(curdies.lme)
```

```
## Approximate 95% confidence intervals
## 
##  Fixed effects:
##                  lower      est.     upper
## (Intercept)  0.1106923 0.3041353 0.4975783
## SEASONWINTER 0.4148441 0.7867589 1.1586737
## attr(,"label")
## [1] "Fixed effects:"
## 
##  Random Effects:
##   Level: SITE 
##                        lower       est.    upper
## sd((Intercept)) 1.879311e-07 0.04008595 8550.386
## 
##  Within-group standard error:
##     lower      est.     upper 
## 0.3025657 0.3896804 0.5018771
```


```r
VarCorr(curdies.lme)
```

```
## SITE = pdLogChol(1) 
##             Variance    StdDev    
## (Intercept) 0.001606883 0.04008595
## Residual    0.151850778 0.38968035
```

From this table, we can calculate the percentage of variability that is explained by site and small scale:


```r
c(0.04009,0.3897)/sum(c(0.04009,0.3897))
```

```
## [1] 0.09327811 0.90672189
```

So only 10% of the variation in the data is explained by site. 


```r
newdata <-data.frame(SEASON=levels(curdies$SEASON))
newdata
```

```
##   SEASON
## 1 SUMMER
## 2 WINTER
```

Now we're asking for the coefficient fixed effects, which we need to repopulation the data

```r
coefs <-fixef(curdies.lme)
```

Now we generate a model matrix for our newdata.


```r
xmat <-model.matrix (~SEASON,data=newdata)
xmat
```

```
##   (Intercept) SEASONWINTER
## 1           1            0
## 2           1            1
## attr(,"assign")
## [1] 0 1
## attr(,"contrasts")
## attr(,"contrasts")$SEASON
## [1] "contr.treatment"
```

Now we can estimate the predicted values.


```r
fit <-t(coefs %*% t(xmat))
fit
```

```
##        [,1]
## 1 0.3041353
## 2 1.0908942
```

However, this doesn't give use the standard errors. We get them as follows:


```r
SE <- sqrt(diag(xmat %*% vcov(curdies.lme) %*% t(xmat)))
```

Finally, we want to turn these standarn errors into confidence intervals


```r
ci <-qt(0.975,length(curdies$SEASON)-2)*SE
newdata<-cbind(newdata,data.frame(fit=fit, se=SE, lower=fit-ci,upper=fit+ci))
head(newdata)
```

```
##   SEASON       fit         se     lower     upper
## 1 SUMMER 0.3041353 0.09471949 0.1116421 0.4966285
## 2 WINTER 1.0908942 0.09471949 0.8984010 1.2833874
```

```r
ggplot(newdata,aes(y=fit,x=SEASON)) + geom_point() + 
  geom_errorbar(aes(ymin=lower,ymax=upper),width=0)+
  theme_classic()
```

![plot of chunk unnamed-chunk-96](figure/unnamed-chunk-96-1.png) 

------------------------------------
**Randomized block and repeated measures designs**


```r
tobacco <- read.csv('tobacco.csv',strip.white=T)
head(tobacco)
```

```
##   LEAF TREATMENT   NUMBER
## 1   L1    Strong 35.89776
## 2   L1      Weak 25.01984
## 3   L2    Strong 34.11786
## 4   L2      Weak 23.16740
## 5   L3    Strong 35.70215
## 6   L3      Weak 24.12191
```

First step is to explore the assumptions of normality and equal variance. Here we don't need to do aggregations, as the treatments are the actual replicates (not nested)


```r
ggplot(tobacco, aes(y=NUMBER,x=TREATMENT)) + geom_boxplot() 
```

![plot of chunk unnamed-chunk-98](figure/unnamed-chunk-98-1.png) 

We do have outliers, but that is because the dataset is smaller (the smaller the dataset, the more outliers). 
So no problems there, but we do have dependence. So we need to run a model with leaf as a random effect


```r
tobacco.lme <- lme(NUMBER~TREATMENT,random=~1|LEAF,data=tobacco)
tobacco.lme1 <- lme(NUMBER~TREATMENT,random=~TREATMENT|LEAF,data=tobacco)
AICc(tobacco.lme,tobacco.lme1)
```

```
##              df     AICc
## tobacco.lme   4 100.3444
## tobacco.lme1  6 110.0038
```

```r
anova(tobacco.lme,tobacco.lme1)
```

```
##              Model df       AIC       BIC    logLik   Test    L.Ratio
## tobacco.lme      1  4  96.70799  99.26422 -44.35400                  
## tobacco.lme1     2  6 100.67049 104.50484 -44.33525 1 vs 2 0.03749921
##              p-value
## tobacco.lme         
## tobacco.lme1  0.9814
```

So the first model is the best one, so no need to include a random slope. 

Next we're going to check for normality and homogeneity of variance by plotting the residuals


```r
plot(tobacco.lme)
```

![plot of chunk unnamed-chunk-100](figure/unnamed-chunk-100-1.png) 

Residuals are centered around 0, there is no wedge and no pattern. So that is all fine.

So now we can look at the effect of the model

```r
summary(tobacco.lme)
```

```
## Linear mixed-effects model fit by REML
##  Data: tobacco 
##        AIC      BIC  logLik
##   96.70799 99.26422 -44.354
## 
## Random effects:
##  Formula: ~1 | LEAF
##         (Intercept) Residual
## StdDev:     3.69221 3.802924
## 
## Fixed effects: NUMBER ~ TREATMENT 
##                  Value Std.Error DF   t-value p-value
## (Intercept)   34.94013  1.873988  7 18.644800  0.0000
## TREATMENTWeak -7.87938  1.901462  7 -4.143855  0.0043
##  Correlation: 
##               (Intr)
## TREATMENTWeak -0.507
## 
## Standardized Within-Group Residuals:
##         Min          Q1         Med          Q3         Max 
## -1.61850358 -0.48452374  0.01133376  0.40899999  1.42778213 
## 
## Number of Observations: 16
## Number of Groups: 8
```

`(Intercept)   34.94013  1.873988  7 18.644800  0.0000` shows that 'Strong' has a mean of 34.9, and `TREATMENTWeak -7.87938  1.901462  7 -4.143855  0.0043` shows 'Weak' has a mean of 7.9 less than the average of strong.  

------------------
Next is split-plot design


```r
copper <- read.csv('copper.csv',strip.white=T)
head(copper)
```

```
##    COPPER PLATE DIST WORMS
## 1 control   200    4 11.50
## 2 control   200    3 13.00
## 3 control   200    2 13.50
## 4 control   200    1 12.00
## 5 control    39    4 17.75
## 6 control    39    3 13.75
```

```r
copper$PLATE <- factor(copper$PLATE)
copper$DIST <- factor(copper$DIST)
```

The interaction of 2 fixed effects must be fixed, the interaction of a fixed and a random effects must be random. 

##Variable                          Fixed or random
##COPPER (Control, Week1, Week2)    (F)---
##PLATE (Block)                     (R)<--
##DIST(1,2,3,4)                     (F)----
##COPPER:DIST                       (F)<----
##PLATE:COPPER:DIST (Residuals)     (R)<----

Explore the assumptions of normality and equal variance. We need to aggregate to the level of PLATE


```r
copper.ag <- ddply(copper, ~COPPER+PLATE, numcolwise(mean))
copper.ag
```

```
##     COPPER PLATE   WORMS
## 1  control    34 11.1250
## 2  control    36 11.3125
## 3  control    39 14.1875
## 4  control   179 11.8750
## 5  control   200 12.5000
## 6   Week 1    12  8.9375
## 7   Week 1    21  9.2500
## 8   Week 1    61  7.3125
## 9   Week 1   178  8.2500
## 10  Week 1   199  8.8750
## 11  Week 2    16  3.0625
## 12  Week 2    57  3.0000
## 13  Week 2    63  3.7500
## 14  Week 2   181  3.9375
## 15  Week 2   204  3.1875
```

```r
ggplot(copper.ag, aes(y=WORMS,x=COPPER)) + geom_boxplot() 
```

![plot of chunk unnamed-chunk-103](figure/unnamed-chunk-103-1.png) 

There seems to be a small issue with a reduction in the distribution for copper treatments, but we first need to check the interaction between COPPER and DIST. For this we can just use the raw data.


```r
ggplot(copper, aes(y=WORMS,x=DIST)) + geom_boxplot() + facet_wrap(~COPPER)
```

![plot of chunk unnamed-chunk-104](figure/unnamed-chunk-104-1.png) 

Or


```r
ggplot(copper, aes(y=WORMS,x=DIST,color=COPPER)) + geom_boxplot() +
  theme_classic()
```

![plot of chunk unnamed-chunk-105](figure/unnamed-chunk-105-1.png) 

Let's look at the data with and without transformation 

```r
g1<-ggplot(copper, aes(y=WORMS,x=DIST,color=COPPER)) + geom_boxplot() +
  scale_color_discrete(guide=FALSE) +
  theme_classic()
g2<-ggplot(copper, aes(y=WORMS,x=DIST,color=COPPER)) + geom_boxplot() +
  scale_y_sqrt()+ theme_classic() +
  theme(legend.position=c(1,0),legend.justification=c(1,0))
```

The second plot has a sqrt transformation of the y-axes

Now put multiple panels together

```r
grid.arrange(g1,g2,ncol=2)
```

![plot of chunk unnamed-chunk-107](figure/unnamed-chunk-107-1.png) 

The transformation doesn't seem to be too much better

Now we need to fit the model


```r
copper.lme<-lme(WORMS~DIST*COPPER, random=~1|PLATE, data=copper)
```

But! We might have to account for correlation structure

```r
copper.lme1<-lme(WORMS~DIST*COPPER, random=~1|PLATE, data=copper,correlation=corCompSymm(form=~1|PLATE))
```

`=corCompSymm(form=~1|PLATE))` specifies that we want a separate corralation structure per plate

We can achieve the same by updating the first model


```r
copper.lme1<-update(copper.lme, correlation=corCompSymm(form=~1|PLATE))
```

Which one is better?


```r
AICc(copper.lme,copper.lme1)
```

```
##             df     AICc
## copper.lme  14 227.5848
## copper.lme1 15 231.1606
```

```r
anova(copper.lme,copper.lme1)
```

```
##             Model df      AIC      BIC    logLik   Test      L.Ratio
## copper.lme      1 14 218.2515 244.4483 -95.12575                    
## copper.lme1     2 15 220.2515 248.3195 -95.12575 1 vs 2 1.136868e-13
##             p-value
## copper.lme         
## copper.lme1       1
```

And test for auto-regressive correlation structure

```r
copper.lme2<-update(copper.lme,
                    correlation=corAR1(form=~1|PLATE))
```


```r
AICc(copper.lme1,copper.lme2)
```

```
##             df     AICc
## copper.lme1 15 231.1606
## copper.lme2 15 225.2792
```

```r
anova(copper.lme1,copper.lme2)
```

```
##             Model df      AIC      BIC    logLik
## copper.lme1     1 15 220.2515 248.3195 -95.12575
## copper.lme2     2 15 214.3701 242.4382 -92.18508
```

**This is why we need to use lme instead of lmer**, because the second one can't adjust for spatial or temporal autocorrelation

Make another column where distance is numeric and not categorical

```r
copper$iDIST<-as.numeric(copper$DIST)
```

Update model


```r
copper.lme2<-update(copper.lme,correlation=corAR1(form=~iDIST|PLATE))
copper.lme2<-update(copper.lme2,method='REML')
```

However, we have not checked if the residuals were correct


```r
plot(copper.lme2)
```

![plot of chunk unnamed-chunk-116](figure/unnamed-chunk-116-1.png) 

Again, this is a borderline case. 

However, since we are dealing with a complicated structure, we actually need to break the residual plot down into eacht factor


```r
plot(residuals(copper.lme2,type='normalized')~copper$COPPER)
```

![plot of chunk unnamed-chunk-117](figure/unnamed-chunk-117-1.png) 

We are using the standardized (normalized) residuals, as we accounted for the autocorrelation structure.

We can do the same for distance

```r
plot(residuals(copper.lme2,type='normalized')~copper$DIST)
```

![plot of chunk unnamed-chunk-118](figure/unnamed-chunk-118-1.png) 

Now summarize the model

```r
with(copper,interaction.plot(DIST,COPPER,WORMS))
```

![plot of chunk unnamed-chunk-119](figure/unnamed-chunk-119-1.png) 

```r
summary(copper.lme2)
```

```
## Linear mixed-effects model fit by REML
##  Data: copper 
##        AIC      BIC    logLik
##   214.3702 242.4382 -92.18508
## 
## Random effects:
##  Formula: ~1 | PLATE
##          (Intercept) Residual
## StdDev: 0.0001127302 1.468741
## 
## Correlation Structure: ARMA(1,0)
##  Formula: ~iDIST | PLATE 
##  Parameter estimate(s):
##      Phi1 
## 0.4479824 
## Fixed effects: WORMS ~ DIST * COPPER 
##                     Value Std.Error DF    t-value p-value
## (Intercept)         10.85 0.6568410 36  16.518456  0.0000
## DIST2                1.15 0.6901631 36   1.666273  0.1043
## DIST3                1.55 0.8304880 36   1.866372  0.0702
## DIST4                2.70 0.8861734 36   3.046808  0.0043
## COPPERWeek 1        -3.60 0.9289135 12  -3.875495  0.0022
## COPPERWeek 2       -10.60 0.9289135 12 -11.411181  0.0000
## DIST2:COPPERWeek 1  -0.05 0.9760380 36  -0.051228  0.9594
## DIST3:COPPERWeek 1  -0.30 1.1744874 36  -0.255431  0.7998
## DIST4:COPPERWeek 1   0.05 1.2532384 36   0.039897  0.9684
## DIST2:COPPERWeek 2   0.05 0.9760380 36   0.051228  0.9594
## DIST3:COPPERWeek 2   2.20 1.1744874 36   1.873158  0.0692
## DIST4:COPPERWeek 2   4.90 1.2532384 36   3.909871  0.0004
##  Correlation: 
##                    (Intr) DIST2  DIST3  DIST4  COPPE1 COPPE2 DIST21 DIST31
## DIST2              -0.525                                                 
## DIST3              -0.632  0.602                                          
## DIST4              -0.675  0.468  0.678                                   
## COPPERWeek 1       -0.707  0.371  0.447  0.477                            
## COPPERWeek 2       -0.707  0.371  0.447  0.477  0.500                     
## DIST2:COPPERWeek 1  0.371 -0.707 -0.425 -0.331 -0.525 -0.263              
## DIST3:COPPERWeek 1  0.447 -0.425 -0.707 -0.480 -0.632 -0.316  0.602       
## DIST4:COPPERWeek 1  0.477 -0.331 -0.480 -0.707 -0.675 -0.337  0.468  0.678
## DIST2:COPPERWeek 2  0.371 -0.707 -0.425 -0.331 -0.263 -0.525  0.500  0.301
## DIST3:COPPERWeek 2  0.447 -0.425 -0.707 -0.480 -0.316 -0.632  0.301  0.500
## DIST4:COPPERWeek 2  0.477 -0.331 -0.480 -0.707 -0.337 -0.675  0.234  0.339
##                    DIST41 DIST22 DIST32
## DIST2                                  
## DIST3                                  
## DIST4                                  
## COPPERWeek 1                           
## COPPERWeek 2                           
## DIST2:COPPERWeek 1                     
## DIST3:COPPERWeek 1                     
## DIST4:COPPERWeek 1                     
## DIST2:COPPERWeek 2  0.234              
## DIST3:COPPERWeek 2  0.339  0.602       
## DIST4:COPPERWeek 2  0.500  0.468  0.678
## 
## Standardized Within-Group Residuals:
##        Min         Q1        Med         Q3        Max 
## -1.3957530 -0.5957482 -0.1702138  0.5361734  2.8595916 
## 
## Number of Observations: 60
## Number of Groups: 15
```

The degree of correlation between distance 1 and 2 is     ` Phi1 
0.4479824 `

There is some evidence about the interaction between distance and copper, so we cannot make any statements about a distance effect and a copper effect. 

To explore this further, we can split up the dataset. To do this, we will use degrees of freedom. Better is to name specific contrasts within the analysis we just run



Rerun model

```r
copper.lme2 <- lme(WORMS~COPPER*DIST,random=~1|PLATE,data=copper,
  correlation=corAR1(form=~1|PLATE), method='REML')
```


```r
contrast(copper.lme2,
        a = list(COPPER = "control",DIST = levels(copper$DIST)),
        b = list(COPPER = "Week 1",DIST = levels(copper$DIST)))
```

```
## lme model parameter contrast
## 
##  Contrast      S.E.    Lower    Upper    t df Pr(>|t|)
##      3.60 0.9289135 1.729072 5.470928 3.88 45    3e-04
##      3.65 0.9289135 1.779072 5.520928 3.93 45    3e-04
##      3.90 0.9289135 2.029072 5.770928 4.20 45    1e-04
##      3.55 0.9289135 1.679072 5.420928 3.82 45    4e-04
```

Look at control to Week 2

```r
contrast(copper.lme2,
        a = list(COPPER = "control",DIST = levels(copper$DIST)),
        b = list(COPPER = "Week 2",DIST = levels(copper$DIST)))
```

```
## lme model parameter contrast
## 
##  Contrast      S.E.    Lower     Upper     t df Pr(>|t|)
##     10.60 0.9289135 8.729072 12.470928 11.41 45        0
##     10.55 0.9289135 8.679072 12.420928 11.36 45        0
##      8.40 0.9289135 6.529072 10.270928  9.04 45        0
##      5.70 0.9289135 3.829072  7.570928  6.14 45        0
```

Look at Week1 to Week 2

```r
contrast(copper.lme2,
        a = list(COPPER = "Week 1",DIST = levels(copper$DIST)),
        b = list(COPPER = "Week 2",DIST = levels(copper$DIST)))
```

```
## lme model parameter contrast
## 
##  Contrast      S.E.     Lower    Upper    t df Pr(>|t|)
##      7.00 0.9289135 5.1290722 8.870928 7.54 45   0.0000
##      6.90 0.9289135 5.0290722 8.770928 7.43 45   0.0000
##      4.50 0.9289135 2.6290722 6.370928 4.84 45   0.0000
##      2.15 0.9289135 0.2790722 4.020928 2.31 45   0.0253
```

We are making too many comparisons now, so we need to make some Post-hoc corrections


```r
summary(glht(copper.lme2,linfct=mcp(COPPER='Tukey')))
```

```
## Warning in mcp2matrix(model, linfct = linfct): covariate interactions
## found -- default contrast might be inappropriate
```

```
## 
## 	 Simultaneous Tests for General Linear Hypotheses
## 
## Multiple Comparisons of Means: Tukey Contrasts
## 
## 
## Fit: lme.formula(fixed = WORMS ~ COPPER * DIST, data = copper, random = ~1 | 
##     PLATE, correlation = corAR1(form = ~1 | PLATE), method = "REML")
## 
## Linear Hypotheses:
##                       Estimate Std. Error z value Pr(>|z|)    
## Week 1 - control == 0  -3.6000     0.9289  -3.875 0.000297 ***
## Week 2 - control == 0 -10.6000     0.9289 -11.411  < 1e-04 ***
## Week 2 - Week 1 == 0   -7.0000     0.9289  -7.536  < 1e-04 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## (Adjusted p values reported -- single-step method)
```

Now produce a prettier interaction plot

First, set up a prediction space

```r
newdata <- expand.grid(COPPER=levels(copper$COPPER),
                       DIST=levels(copper$DIST))
```

Next, we extract from our model the coefficients and calculate our model matrix


```r
coefs <- fixef(copper.lme2)
Xmat <- model.matrix(~COPPER*DIST,data=newdata)
fit <- as.vector(Xmat %*% coefs)
newdata$fit <- fit
head(newdata)
```

```
##    COPPER DIST   fit
## 1 control    1 10.85
## 2  Week 1    1  7.25
## 3  Week 2    1  0.25
## 4 control    2 12.00
## 5  Week 1    2  8.35
## 6  Week 2    2  1.45
```

```r
SE <- sqrt(diag(Xmat %*% vcov(copper.lme2) %*% t(Xmat)))
newdata$lower <- fit-2*SE
newdata$upper <- fit+2*SE
head(newdata)
```

```
##    COPPER DIST   fit      lower     upper
## 1 control    1 10.85  9.5363179 12.163682
## 2  Week 1    1  7.25  5.9363179  8.563682
## 3  Week 2    1  0.25 -1.0636821  1.563682
## 4 control    2 12.00 10.6863179 13.313682
## 5  Week 1    2  8.35  7.0363179  9.663682
## 6  Week 2    2  1.45  0.1363179  2.763682
```

There is a negative confidence interval for the 3rd line. This should not be possible. We can fix this by for example do a Poisson or negative binomial

And plot

```r
ggplot(newdata,aes(y=fit,x=as.numeric(DIST),color=COPPER))+
  geom_errorbar(aes(ymin=lower,ymax=upper), width=0) +
  geom_point() + geom_line() + 
  theme_classic()
```

![plot of chunk unnamed-chunk-128](figure/unnamed-chunk-128-1.png) 

Or make it even prettier

```r
ggplot(newdata,aes(y=fit,x=as.numeric(DIST))) +
  geom_errorbar(aes(ymin=lower,ymax=upper), width=0.5) +                  
  geom_line(aes(linetype=COPPER))+  
  geom_point(aes(shape=COPPER,fill=COPPER),size=3) +  
  scale_fill_manual('Treatment',values=c('white','gray','black')) +
  scale_shape_manual('Treatment',values=c(21,21,21)) +
  scale_linetype_discrete('Treatment') +
  scale_y_continuous('Density of worms')+
  scale_x_continuous('Distance')+
  theme_classic() +
  theme(
    text=element_text(size=10),
    axis.title.y=element_text(vjust=2,size=rel(2)),
    axis.title.x=element_text(vjust=-1,size=rel(2)),
    legend.position=c(1,0), legend.justification=c(1,0)
  )
```

![plot of chunk unnamed-chunk-129](figure/unnamed-chunk-129-1.png) 

------------
**Generalized Linear Models**

The presence-absence of lizards can be modelled to the shape of the island.

```r
polis <- read.csv('polis.csv',strip.white=T)
head(polis)
```

```
##       ISLAND RATIO PA
## 1       Bota 15.41  1
## 2     Cabeza  5.63  1
## 3    Cerraja 25.92  1
## 4 Coronadito 15.17  0
## 5     Flecha 13.04  1
## 6   Gemelose 18.85  0
```

```r
ggplot(polis,aes(y=PA, x=RATIO)) +
  geom_point() +
  theme_classic()
```

![plot of chunk unnamed-chunk-130](figure/unnamed-chunk-130-1.png) 

This is binomial data, so we're going to assume the data follows a binomial distribution. Make a pretty plot


```r
ggplot(polis,aes(y=PA, x=RATIO)) +
  geom_point() +
  geom_smooth(method='glm',formula=y~x, family='binomial') +
  theme_classic()
```

![plot of chunk unnamed-chunk-131](figure/unnamed-chunk-131-1.png) 

Now let's put it in a model

```r
polis.glm <- glm(PA~RATIO, family=binomial(link='logit'), data = polis)
par(mfrow=c(2,3))
plot(polis.glm)
```

![plot of chunk unnamed-chunk-132](figure/unnamed-chunk-132-1.png) 

Not perfect but 'Eh', this is a small dataset

Now we need to make sure to check that the model fits the data well.

Option1: compare residuals to the residual df. This is the residual sum of squares
Option2: getting p-value


```r
polis.resid <-sum(resid(polis.glm,type='pearson')^2)
1-pchisq(polis.resid,polis.glm$df.resid)  #higher than 0.05, so no lack of fit
```

```
## [1] 0.5715331
```

```r
1-pchisq(polis.glm$deviance,polis.glm$df.resid) #Idem
```

```
## [1] 0.6514215
```

Some people prefer a ratio deviance (closer to 1 is better)

```r
polis.glm$deviance/polis.glm$df.resid
```

```
## [1] 0.8365126
```

So our model is good, yay! Now we can summarize it.

```r
summary(polis.glm)
```

```
## 
## Call:
## glm(formula = PA ~ RATIO, family = binomial(link = "logit"), 
##     data = polis)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -1.6067  -0.6382   0.2368   0.4332   2.0986  
## 
## Coefficients:
##             Estimate Std. Error z value Pr(>|z|)  
## (Intercept)   3.6061     1.6953   2.127   0.0334 *
## RATIO        -0.2196     0.1005  -2.184   0.0289 *
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 26.287  on 18  degrees of freedom
## Residual deviance: 14.221  on 17  degrees of freedom
## AIC: 18.221
## 
## Number of Fisher Scoring iterations: 6
```

These terms however don't relate to a simple intercept or slope, as they are on a logit scale. So we need to do other things, like calculating the R^2:


```r
1-(polis.glm$deviance/polis.glm$null)
```

```
## [1] 0.4590197
```

We also want to know at which point the switch occurs from likely to be present to likely to be absent (the LD50):

```r
-polis.glm$coef[1]/polis.glm$coef[2]
```

```
## (Intercept) 
##     16.4242
```

If we want to reproduce the curve, we need to predict (which involves one extra step compared to earlier, as we need to do a back-transformation):

```r
xs <- seq(min(polis$RATIO),max(polis$RATIO),len=1000)
ys <- predict(polis.glm,newdata=data.frame(RATIO=xs),
             type='link',se=T)
```

We can also do

```r
head(predict(polis.glm,newdata=data.frame(RATIO=xs),
             type='response',se=T))
```

```
## $fit
##            1            2            3            4            5 
## 9.723466e-01 9.719721e-01 9.715927e-01 9.712084e-01 9.708190e-01 
##            6            7            8            9           10 
## 9.704245e-01 9.700248e-01 9.696199e-01 9.692097e-01 9.687942e-01 
##           11           12           13           14           15 
## 9.683732e-01 9.679467e-01 9.675147e-01 9.670770e-01 9.666336e-01 
##           16           17           18           19           20 
## 9.661845e-01 9.657296e-01 9.652687e-01 9.648019e-01 9.643291e-01 
##           21           22           23           24           25 
## 9.638501e-01 9.633649e-01 9.628735e-01 9.623758e-01 9.618716e-01 
##           26           27           28           29           30 
## 9.613609e-01 9.608437e-01 9.603199e-01 9.597893e-01 9.592520e-01 
##           31           32           33           34           35 
## 9.587077e-01 9.581566e-01 9.575984e-01 9.570330e-01 9.564605e-01 
##           36           37           38           39           40 
## 9.558807e-01 9.552936e-01 9.546990e-01 9.540968e-01 9.534871e-01 
##           41           42           43           44           45 
## 9.528697e-01 9.522444e-01 9.516113e-01 9.509703e-01 9.503212e-01 
##           46           47           48           49           50 
## 9.496639e-01 9.489984e-01 9.483246e-01 9.476424e-01 9.469517e-01 
##           51           52           53           54           55 
## 9.462524e-01 9.455444e-01 9.448277e-01 9.441020e-01 9.433674e-01 
##           56           57           58           59           60 
## 9.426237e-01 9.418708e-01 9.411087e-01 9.403372e-01 9.395563e-01 
##           61           62           63           64           65 
## 9.387658e-01 9.379657e-01 9.371558e-01 9.363360e-01 9.355063e-01 
##           66           67           68           69           70 
## 9.346665e-01 9.338166e-01 9.329564e-01 9.320858e-01 9.312048e-01 
##           71           72           73           74           75 
## 9.303132e-01 9.294109e-01 9.284978e-01 9.275739e-01 9.266389e-01 
##           76           77           78           79           80 
## 9.256928e-01 9.247356e-01 9.237670e-01 9.227870e-01 9.217954e-01 
##           81           82           83           84           85 
## 9.207922e-01 9.197773e-01 9.187505e-01 9.177117e-01 9.166609e-01 
##           86           87           88           89           90 
## 9.155979e-01 9.145226e-01 9.134348e-01 9.123346e-01 9.112218e-01 
##           91           92           93           94           95 
## 9.100962e-01 9.089577e-01 9.078063e-01 9.066419e-01 9.054642e-01 
##           96           97           98           99          100 
## 9.042733e-01 9.030690e-01 9.018512e-01 9.006197e-01 8.993746e-01 
##          101          102          103          104          105 
## 8.981155e-01 8.968426e-01 8.955556e-01 8.942544e-01 8.929390e-01 
##          106          107          108          109          110 
## 8.916092e-01 8.902648e-01 8.889059e-01 8.875323e-01 8.861439e-01 
##          111          112          113          114          115 
## 8.847406e-01 8.833222e-01 8.818888e-01 8.804401e-01 8.789760e-01 
##          116          117          118          119          120 
## 8.774966e-01 8.760016e-01 8.744909e-01 8.729646e-01 8.714224e-01 
##          121          122          123          124          125 
## 8.698642e-01 8.682901e-01 8.666998e-01 8.650933e-01 8.634705e-01 
##          126          127          128          129          130 
## 8.618312e-01 8.601755e-01 8.585032e-01 8.568143e-01 8.551086e-01 
##          131          132          133          134          135 
## 8.533860e-01 8.516465e-01 8.498900e-01 8.481164e-01 8.463257e-01 
##          136          137          138          139          140 
## 8.445177e-01 8.426923e-01 8.408496e-01 8.389895e-01 8.371118e-01 
##          141          142          143          144          145 
## 8.352165e-01 8.333035e-01 8.313728e-01 8.294244e-01 8.274581e-01 
##          146          147          148          149          150 
## 8.254739e-01 8.234717e-01 8.214516e-01 8.194134e-01 8.173571e-01 
##          151          152          153          154          155 
## 8.152826e-01 8.131900e-01 8.110792e-01 8.089502e-01 8.068028e-01 
##          156          157          158          159          160 
## 8.046372e-01 8.024532e-01 8.002508e-01 7.980301e-01 7.957910e-01 
##          161          162          163          164          165 
## 7.935335e-01 7.912576e-01 7.889633e-01 7.866505e-01 7.843194e-01 
##          166          167          168          169          170 
## 7.819698e-01 7.796018e-01 7.772155e-01 7.748107e-01 7.723876e-01 
##          171          172          173          174          175 
## 7.699462e-01 7.674865e-01 7.650085e-01 7.625122e-01 7.599978e-01 
##          176          177          178          179          180 
## 7.574652e-01 7.549145e-01 7.523457e-01 7.497590e-01 7.471543e-01 
##          181          182          183          184          185 
## 7.445317e-01 7.418914e-01 7.392333e-01 7.365575e-01 7.338642e-01 
##          186          187          188          189          190 
## 7.311534e-01 7.284251e-01 7.256796e-01 7.229169e-01 7.201371e-01 
##          191          192          193          194          195 
## 7.173404e-01 7.145267e-01 7.116963e-01 7.088493e-01 7.059858e-01 
##          196          197          198          199          200 
## 7.031059e-01 7.002097e-01 6.972975e-01 6.943694e-01 6.914254e-01 
##          201          202          203          204          205 
## 6.884658e-01 6.854908e-01 6.825004e-01 6.794949e-01 6.764745e-01 
##          206          207          208          209          210 
## 6.734392e-01 6.703893e-01 6.673251e-01 6.642466e-01 6.611541e-01 
##          211          212          213          214          215 
## 6.580478e-01 6.549278e-01 6.517945e-01 6.486479e-01 6.454884e-01 
##          216          217          218          219          220 
## 6.423162e-01 6.391314e-01 6.359344e-01 6.327253e-01 6.295044e-01 
##          221          222          223          224          225 
## 6.262719e-01 6.230281e-01 6.197733e-01 6.165077e-01 6.132315e-01 
##          226          227          228          229          230 
## 6.099450e-01 6.066485e-01 6.033423e-01 6.000266e-01 5.967017e-01 
##          231          232          233          234          235 
## 5.933679e-01 5.900255e-01 5.866748e-01 5.833160e-01 5.799494e-01 
##          236          237          238          239          240 
## 5.765754e-01 5.731943e-01 5.698063e-01 5.664117e-01 5.630109e-01 
##          241          242          243          244          245 
## 5.596041e-01 5.561918e-01 5.527741e-01 5.493514e-01 5.459241e-01 
##          246          247          248          249          250 
## 5.424924e-01 5.390566e-01 5.356172e-01 5.321743e-01 5.287284e-01 
##          251          252          253          254          255 
## 5.252798e-01 5.218287e-01 5.183755e-01 5.149206e-01 5.114643e-01 
##          256          257          258          259          260 
## 5.080069e-01 5.045487e-01 5.010900e-01 4.976313e-01 4.941728e-01 
##          261          262          263          264          265 
## 4.907148e-01 4.872578e-01 4.838019e-01 4.803476e-01 4.768952e-01 
##          266          267          268          269          270 
## 4.734450e-01 4.699973e-01 4.665525e-01 4.631109e-01 4.596728e-01 
##          271          272          273          274          275 
## 4.562385e-01 4.528083e-01 4.493827e-01 4.459618e-01 4.425461e-01 
##          276          277          278          279          280 
## 4.391358e-01 4.357312e-01 4.323326e-01 4.289405e-01 4.255549e-01 
##          281          282          283          284          285 
## 4.221764e-01 4.188051e-01 4.154414e-01 4.120855e-01 4.087378e-01 
##          286          287          288          289          290 
## 4.053986e-01 4.020681e-01 3.987465e-01 3.954343e-01 3.921317e-01 
##          291          292          293          294          295 
## 3.888388e-01 3.855561e-01 3.822838e-01 3.790221e-01 3.757714e-01 
##          296          297          298          299          300 
## 3.725317e-01 3.693035e-01 3.660870e-01 3.628823e-01 3.596898e-01 
##          301          302          303          304          305 
## 3.565096e-01 3.533420e-01 3.501873e-01 3.470456e-01 3.439172e-01 
##          306          307          308          309          310 
## 3.408023e-01 3.377010e-01 3.346137e-01 3.315404e-01 3.284815e-01 
##          311          312          313          314          315 
## 3.254370e-01 3.224072e-01 3.193922e-01 3.163923e-01 3.134076e-01 
##          316          317          318          319          320 
## 3.104382e-01 3.074844e-01 3.045463e-01 3.016240e-01 2.987177e-01 
##          321          322          323          324          325 
## 2.958276e-01 2.929537e-01 2.900963e-01 2.872554e-01 2.844311e-01 
##          326          327          328          329          330 
## 2.816237e-01 2.788332e-01 2.760597e-01 2.733034e-01 2.705642e-01 
##          331          332          333          334          335 
## 2.678425e-01 2.651381e-01 2.624513e-01 2.597820e-01 2.571305e-01 
##          336          337          338          339          340 
## 2.544967e-01 2.518807e-01 2.492826e-01 2.467025e-01 2.441404e-01 
##          341          342          343          344          345 
## 2.415964e-01 2.390705e-01 2.365628e-01 2.340733e-01 2.316021e-01 
##          346          347          348          349          350 
## 2.291491e-01 2.267144e-01 2.242981e-01 2.219002e-01 2.195206e-01 
##          351          352          353          354          355 
## 2.171594e-01 2.148167e-01 2.124923e-01 2.101864e-01 2.078989e-01 
##          356          357          358          359          360 
## 2.056298e-01 2.033791e-01 2.011468e-01 1.989328e-01 1.967373e-01 
##          361          362          363          364          365 
## 1.945601e-01 1.924012e-01 1.902606e-01 1.881383e-01 1.860342e-01 
##          366          367          368          369          370 
## 1.839483e-01 1.818806e-01 1.798310e-01 1.777995e-01 1.757860e-01 
##          371          372          373          374          375 
## 1.737905e-01 1.718129e-01 1.698532e-01 1.679113e-01 1.659872e-01 
##          376          377          378          379          380 
## 1.640808e-01 1.621920e-01 1.603208e-01 1.584671e-01 1.566308e-01 
##          381          382          383          384          385 
## 1.548119e-01 1.530103e-01 1.512259e-01 1.494587e-01 1.477085e-01 
##          386          387          388          389          390 
## 1.459752e-01 1.442589e-01 1.425594e-01 1.408766e-01 1.392104e-01 
##          391          392          393          394          395 
## 1.375608e-01 1.359277e-01 1.343109e-01 1.327104e-01 1.311261e-01 
##          396          397          398          399          400 
## 1.295579e-01 1.280057e-01 1.264693e-01 1.249488e-01 1.234439e-01 
##          401          402          403          404          405 
## 1.219547e-01 1.204809e-01 1.190226e-01 1.175795e-01 1.161517e-01 
##          406          407          408          409          410 
## 1.147389e-01 1.133411e-01 1.119581e-01 1.105900e-01 1.092365e-01 
##          411          412          413          414          415 
## 1.078975e-01 1.065730e-01 1.052629e-01 1.039670e-01 1.026852e-01 
##          416          417          418          419          420 
## 1.014174e-01 1.001635e-01 9.892343e-02 9.769704e-02 9.648422e-02 
##          421          422          423          424          425 
## 9.528486e-02 9.409886e-02 9.292611e-02 9.176650e-02 9.061990e-02 
##          426          427          428          429          430 
## 8.948623e-02 8.836535e-02 8.725718e-02 8.616158e-02 8.507846e-02 
##          431          432          433          434          435 
## 8.400770e-02 8.294920e-02 8.190285e-02 8.086852e-02 7.984613e-02 
##          436          437          438          439          440 
## 7.883555e-02 7.783668e-02 7.684941e-02 7.587363e-02 7.490924e-02 
##          441          442          443          444          445 
## 7.395612e-02 7.301417e-02 7.208329e-02 7.116336e-02 7.025429e-02 
##          446          447          448          449          450 
## 6.935596e-02 6.846827e-02 6.759112e-02 6.672440e-02 6.586800e-02 
##          451          452          453          454          455 
## 6.502184e-02 6.418580e-02 6.335978e-02 6.254368e-02 6.173739e-02 
##          456          457          458          459          460 
## 6.094083e-02 6.015388e-02 5.937646e-02 5.860845e-02 5.784977e-02 
##          461          462          463          464          465 
## 5.710032e-02 5.635999e-02 5.562869e-02 5.490633e-02 5.419282e-02 
##          466          467          468          469          470 
## 5.348805e-02 5.279193e-02 5.210438e-02 5.142529e-02 5.075458e-02 
##          471          472          473          474          475 
## 5.009216e-02 4.943793e-02 4.879181e-02 4.815370e-02 4.752352e-02 
##          476          477          478          479          480 
## 4.690119e-02 4.628660e-02 4.567969e-02 4.508035e-02 4.448851e-02 
##          481          482          483          484          485 
## 4.390409e-02 4.332699e-02 4.275714e-02 4.219446e-02 4.163886e-02 
##          486          487          488          489          490 
## 4.109026e-02 4.054858e-02 4.001374e-02 3.948567e-02 3.896429e-02 
##          491          492          493          494          495 
## 3.844951e-02 3.794127e-02 3.743948e-02 3.694408e-02 3.645498e-02 
##          496          497          498          499          500 
## 3.597211e-02 3.549541e-02 3.502479e-02 3.456019e-02 3.410154e-02 
##          501          502          503          504          505 
## 3.364876e-02 3.320178e-02 3.276054e-02 3.232497e-02 3.189500e-02 
##          506          507          508          509          510 
## 3.147056e-02 3.105159e-02 3.063802e-02 3.022978e-02 2.982682e-02 
##          511          512          513          514          515 
## 2.942907e-02 2.903646e-02 2.864894e-02 2.826643e-02 2.788889e-02 
##          516          517          518          519          520 
## 2.751625e-02 2.714845e-02 2.678542e-02 2.642712e-02 2.607349e-02 
##          521          522          523          524          525 
## 2.572446e-02 2.537998e-02 2.504000e-02 2.470446e-02 2.437330e-02 
##          526          527          528          529          530 
## 2.404647e-02 2.372391e-02 2.340558e-02 2.309142e-02 2.278138e-02 
##          531          532          533          534          535 
## 2.247540e-02 2.217344e-02 2.187545e-02 2.158137e-02 2.129116e-02 
##          536          537          538          539          540 
## 2.100477e-02 2.072215e-02 2.044325e-02 2.016803e-02 1.989644e-02 
##          541          542          543          544          545 
## 1.962843e-02 1.936397e-02 1.910299e-02 1.884547e-02 1.859135e-02 
##          546          547          548          549          550 
## 1.834059e-02 1.809316e-02 1.784900e-02 1.760808e-02 1.737035e-02 
##          551          552          553          554          555 
## 1.713577e-02 1.690431e-02 1.667592e-02 1.645057e-02 1.622821e-02 
##          556          557          558          559          560 
## 1.600881e-02 1.579233e-02 1.557872e-02 1.536797e-02 1.516001e-02 
##          561          562          563          564          565 
## 1.495483e-02 1.475239e-02 1.455264e-02 1.435557e-02 1.416112e-02 
##          566          567          568          569          570 
## 1.396926e-02 1.377997e-02 1.359321e-02 1.340895e-02 1.322715e-02 
##          571          572          573          574          575 
## 1.304779e-02 1.287082e-02 1.269622e-02 1.252396e-02 1.235401e-02 
##          576          577          578          579          580 
## 1.218634e-02 1.202092e-02 1.185771e-02 1.169669e-02 1.153784e-02 
##          581          582          583          584          585 
## 1.138112e-02 1.122650e-02 1.107396e-02 1.092346e-02 1.077500e-02 
##          586          587          588          589          590 
## 1.062852e-02 1.048402e-02 1.034146e-02 1.020082e-02 1.006208e-02 
##          591          592          593          594          595 
## 9.925198e-03 9.790163e-03 9.656948e-03 9.525528e-03 9.395879e-03 
##          596          597          598          599          600 
## 9.267978e-03 9.141802e-03 9.017329e-03 8.894535e-03 8.773398e-03 
##          601          602          603          604          605 
## 8.653897e-03 8.536010e-03 8.419714e-03 8.304990e-03 8.191816e-03 
##          606          607          608          609          610 
## 8.080172e-03 7.970037e-03 7.861392e-03 7.754216e-03 7.648490e-03 
##          611          612          613          614          615 
## 7.544194e-03 7.441310e-03 7.339818e-03 7.239701e-03 7.140940e-03 
##          616          617          618          619          620 
## 7.043516e-03 6.947412e-03 6.852610e-03 6.759094e-03 6.666844e-03 
##          621          622          623          624          625 
## 6.575846e-03 6.486081e-03 6.397534e-03 6.310188e-03 6.224027e-03 
##          626          627          628          629          630 
## 6.139036e-03 6.055198e-03 5.972498e-03 5.890920e-03 5.810451e-03 
##          631          632          633          634          635 
## 5.731074e-03 5.652776e-03 5.575541e-03 5.499355e-03 5.424205e-03 
##          636          637          638          639          640 
## 5.350077e-03 5.276956e-03 5.204829e-03 5.133683e-03 5.063505e-03 
##          641          642          643          644          645 
## 4.994281e-03 4.925999e-03 4.858646e-03 4.792209e-03 4.726676e-03 
##          646          647          648          649          650 
## 4.662036e-03 4.598275e-03 4.535382e-03 4.473346e-03 4.412154e-03 
##          651          652          653          654          655 
## 4.351796e-03 4.292260e-03 4.233535e-03 4.175610e-03 4.118475e-03 
##          656          657          658          659          660 
## 4.062118e-03 4.006529e-03 3.951698e-03 3.897614e-03 3.844267e-03 
##          661          662          663          664          665 
## 3.791648e-03 3.739747e-03 3.688553e-03 3.638058e-03 3.588251e-03 
##          666          667          668          669          670 
## 3.539124e-03 3.490667e-03 3.442871e-03 3.395727e-03 3.349227e-03 
##          671          672          673          674          675 
## 3.303362e-03 3.258122e-03 3.213500e-03 3.169487e-03 3.126075e-03 
##          676          677          678          679          680 
## 3.083256e-03 3.041022e-03 2.999364e-03 2.958276e-03 2.917748e-03 
##          681          682          683          684          685 
## 2.877774e-03 2.838347e-03 2.799458e-03 2.761100e-03 2.723267e-03 
##          686          687          688          689          690 
## 2.685950e-03 2.649143e-03 2.612840e-03 2.577033e-03 2.541715e-03 
##          691          692          693          694          695 
## 2.506880e-03 2.472521e-03 2.438632e-03 2.405206e-03 2.372238e-03 
##          696          697          698          699          700 
## 2.339720e-03 2.307647e-03 2.276013e-03 2.244811e-03 2.214036e-03 
##          701          702          703          704          705 
## 2.183682e-03 2.153744e-03 2.124215e-03 2.095090e-03 2.066363e-03 
##          706          707          708          709          710 
## 2.038029e-03 2.010084e-03 1.982520e-03 1.955334e-03 1.928520e-03 
##          711          712          713          714          715 
## 1.902073e-03 1.875988e-03 1.850260e-03 1.824885e-03 1.799856e-03 
##          716          717          718          719          720 
## 1.775171e-03 1.750823e-03 1.726808e-03 1.703123e-03 1.679762e-03 
##          721          722          723          724          725 
## 1.656720e-03 1.633995e-03 1.611580e-03 1.589473e-03 1.567668e-03 
##          726          727          728          729          730 
## 1.546162e-03 1.524950e-03 1.504029e-03 1.483395e-03 1.463043e-03 
##          731          732          733          734          735 
## 1.442970e-03 1.423173e-03 1.403646e-03 1.384387e-03 1.365392e-03 
##          736          737          738          739          740 
## 1.346657e-03 1.328179e-03 1.309954e-03 1.291979e-03 1.274250e-03 
##          741          742          743          744          745 
## 1.256764e-03 1.239518e-03 1.222508e-03 1.205731e-03 1.189184e-03 
##          746          747          748          749          750 
## 1.172864e-03 1.156768e-03 1.140892e-03 1.125235e-03 1.109791e-03 
##          751          752          753          754          755 
## 1.094560e-03 1.079537e-03 1.064720e-03 1.050106e-03 1.035693e-03 
##          756          757          758          759          760 
## 1.021478e-03 1.007457e-03 9.936284e-04 9.799895e-04 9.665377e-04 
##          761          762          763          764          765 
## 9.532704e-04 9.401850e-04 9.272791e-04 9.145501e-04 9.019958e-04 
##          766          767          768          769          770 
## 8.896136e-04 8.774012e-04 8.653564e-04 8.534767e-04 8.417600e-04 
##          771          772          773          774          775 
## 8.302040e-04 8.188066e-04 8.075655e-04 7.964785e-04 7.855437e-04 
##          776          777          778          779          780 
## 7.747589e-04 7.641220e-04 7.536311e-04 7.432841e-04 7.330790e-04 
##          781          782          783          784          785 
## 7.230139e-04 7.130870e-04 7.032962e-04 6.936398e-04 6.841159e-04 
##          786          787          788          789          790 
## 6.747226e-04 6.654583e-04 6.563210e-04 6.473092e-04 6.384210e-04 
##          791          792          793          794          795 
## 6.296548e-04 6.210088e-04 6.124815e-04 6.040713e-04 5.957764e-04 
##          796          797          798          799          800 
## 5.875954e-04 5.795267e-04 5.715687e-04 5.637199e-04 5.559788e-04 
##          801          802          803          804          805 
## 5.483440e-04 5.408139e-04 5.333872e-04 5.260625e-04 5.188382e-04 
##          806          807          808          809          810 
## 5.117132e-04 5.046859e-04 4.977551e-04 4.909194e-04 4.841776e-04 
##          811          812          813          814          815 
## 4.775283e-04 4.709702e-04 4.645022e-04 4.581230e-04 4.518313e-04 
##          816          817          818          819          820 
## 4.456260e-04 4.395059e-04 4.334698e-04 4.275166e-04 4.216451e-04 
##          821          822          823          824          825 
## 4.158542e-04 4.101428e-04 4.045098e-04 3.989542e-04 3.934748e-04 
##          826          827          828          829          830 
## 3.880707e-04 3.827407e-04 3.774839e-04 3.722993e-04 3.671859e-04 
##          831          832          833          834          835 
## 3.621427e-04 3.571687e-04 3.522630e-04 3.474247e-04 3.426528e-04 
##          836          837          838          839          840 
## 3.379464e-04 3.333046e-04 3.287266e-04 3.242115e-04 3.197583e-04 
##          841          842          843          844          845 
## 3.153663e-04 3.110346e-04 3.067623e-04 3.025488e-04 2.983931e-04 
##          846          847          848          849          850 
## 2.942944e-04 2.902521e-04 2.862652e-04 2.823331e-04 2.784550e-04 
##          851          852          853          854          855 
## 2.746301e-04 2.708578e-04 2.671373e-04 2.634678e-04 2.598488e-04 
##          856          857          858          859          860 
## 2.562795e-04 2.527591e-04 2.492871e-04 2.458628e-04 2.424856e-04 
##          861          862          863          864          865 
## 2.391547e-04 2.358695e-04 2.326295e-04 2.294339e-04 2.262823e-04 
##          866          867          868          869          870 
## 2.231739e-04 2.201082e-04 2.170846e-04 2.141026e-04 2.111615e-04 
##          871          872          873          874          875 
## 2.082608e-04 2.053999e-04 2.025784e-04 1.997955e-04 1.970509e-04 
##          876          877          878          879          880 
## 1.943440e-04 1.916743e-04 1.890412e-04 1.864443e-04 1.838831e-04 
##          881          882          883          884          885 
## 1.813571e-04 1.788657e-04 1.764086e-04 1.739852e-04 1.715951e-04 
##          886          887          888          889          890 
## 1.692378e-04 1.669129e-04 1.646199e-04 1.623584e-04 1.601280e-04 
##          891          892          893          894          895 
## 1.579283e-04 1.557587e-04 1.536189e-04 1.515086e-04 1.494272e-04 
##          896          897          898          899          900 
## 1.473744e-04 1.453498e-04 1.433530e-04 1.413837e-04 1.394414e-04 
##          901          902          903          904          905 
## 1.375257e-04 1.356364e-04 1.337731e-04 1.319353e-04 1.301228e-04 
##          906          907          908          909          910 
## 1.283351e-04 1.265721e-04 1.248332e-04 1.231182e-04 1.214268e-04 
##          911          912          913          914          915 
## 1.197587e-04 1.181134e-04 1.164907e-04 1.148904e-04 1.133120e-04 
##          916          917          918          919          920 
## 1.117553e-04 1.102200e-04 1.087057e-04 1.072123e-04 1.057394e-04 
##          921          922          923          924          925 
## 1.042867e-04 1.028540e-04 1.014409e-04 1.000473e-04 9.867279e-05 
##          926          927          928          929          930 
## 9.731719e-05 9.598020e-05 9.466158e-05 9.336107e-05 9.207843e-05 
##          931          932          933          934          935 
## 9.081341e-05 8.956577e-05 8.833526e-05 8.712166e-05 8.592474e-05 
##          936          937          938          939          940 
## 8.474425e-05 8.357998e-05 8.243171e-05 8.129921e-05 8.018227e-05 
##          941          942          943          944          945 
## 7.908067e-05 7.799420e-05 7.692266e-05 7.586584e-05 7.482354e-05 
##          946          947          948          949          950 
## 7.379556e-05 7.278170e-05 7.178177e-05 7.079558e-05 6.982293e-05 
##          951          952          953          954          955 
## 6.886364e-05 6.791754e-05 6.698443e-05 6.606414e-05 6.515649e-05 
##          956          957          958          959          960 
## 6.426132e-05 6.337844e-05 6.250769e-05 6.164890e-05 6.080191e-05 
##          961          962          963          964          965 
## 5.996656e-05 5.914268e-05 5.833012e-05 5.752872e-05 5.673834e-05 
##          966          967          968          969          970 
## 5.595881e-05 5.518999e-05 5.443174e-05 5.368390e-05 5.294633e-05 
##          971          972          973          974          975 
## 5.221890e-05 5.150146e-05 5.079388e-05 5.009602e-05 4.940775e-05 
##          976          977          978          979          980 
## 4.872893e-05 4.805944e-05 4.739914e-05 4.674792e-05 4.610564e-05 
##          981          982          983          984          985 
## 4.547219e-05 4.484744e-05 4.423128e-05 4.362358e-05 4.302422e-05 
##          986          987          988          989          990 
## 4.243311e-05 4.185011e-05 4.127512e-05 4.070804e-05 4.014874e-05 
##          991          992          993          994          995 
## 3.959713e-05 3.905309e-05 3.851653e-05 3.798735e-05 3.746543e-05 
##          996          997          998          999         1000 
## 3.695068e-05 3.644301e-05 3.594231e-05 3.544849e-05 3.496145e-05 
## 
## $se.fit
##            1            2            3            4            5 
## 0.0450617382 0.0454959694 0.0459334545 0.0463741990 0.0468182079 
##            6            7            8            9           10 
## 0.0472654857 0.0477160364 0.0481698635 0.0486269701 0.0490873588 
##           11           12           13           14           15 
## 0.0495510315 0.0500179897 0.0504882344 0.0509617659 0.0514385840 
##           16           17           18           19           20 
## 0.0519186879 0.0524020762 0.0528887469 0.0533786974 0.0538719244 
##           21           22           23           24           25 
## 0.0543684240 0.0548681917 0.0553712221 0.0558775093 0.0563870467 
##           26           27           28           29           30 
## 0.0568998269 0.0574158418 0.0579350825 0.0584575394 0.0589832022 
##           31           32           33           34           35 
## 0.0595120597 0.0600441000 0.0605793102 0.0611176768 0.0616591852 
##           36           37           38           39           40 
## 0.0622038203 0.0627515658 0.0633024048 0.0638563192 0.0644132902 
##           41           42           43           44           45 
## 0.0649732980 0.0655363219 0.0661023403 0.0666713305 0.0672432689 
##           46           47           48           49           50 
## 0.0678181309 0.0683958910 0.0689765226 0.0695599979 0.0701462884 
##           51           52           53           54           55 
## 0.0707353643 0.0713271949 0.0719217482 0.0725189913 0.0731188902 
##           56           57           58           59           60 
## 0.0737214097 0.0743265135 0.0749341643 0.0755443235 0.0761569514 
##           61           62           63           64           65 
## 0.0767720072 0.0773894489 0.0780092332 0.0786313158 0.0792556511 
##           66           67           68           69           70 
## 0.0798821923 0.0805108916 0.0811416997 0.0817745662 0.0824094396 
##           71           72           73           74           75 
## 0.0830462669 0.0836849942 0.0843255661 0.0849679261 0.0856120165 
##           76           77           78           79           80 
## 0.0862577782 0.0869051510 0.0875540735 0.0882044830 0.0888563155 
##           81           82           83           84           85 
## 0.0895095059 0.0901639878 0.0908196936 0.0914765545 0.0921345005 
##           86           87           88           89           90 
## 0.0927934604 0.0934533618 0.0941141312 0.0947756938 0.0954379737 
##           91           92           93           94           95 
## 0.0961008939 0.0967643763 0.0974283416 0.0980927094 0.0987573984 
##           96           97           98           99          100 
## 0.0994223261 0.1000874089 0.1007525625 0.1014177012 0.1020827386 
##          101          102          103          104          105 
## 0.1027475874 0.1034121593 0.1040763652 0.1047401149 0.1054033178 
##          106          107          108          109          110 
## 0.1060658823 0.1067277160 0.1073887259 0.1080488184 0.1087078991 
##          111          112          113          114          115 
## 0.1093658732 0.1100226454 0.1106781196 0.1113321995 0.1119847885 
##          116          117          118          119          120 
## 0.1126357895 0.1132851051 0.1139326378 0.1145782897 0.1152219631 
##          121          122          123          124          125 
## 0.1158635600 0.1165029825 0.1171401326 0.1177749128 0.1184072254 
##          126          127          128          129          130 
## 0.1190369731 0.1196640591 0.1202883868 0.1209098602 0.1215283837 
##          131          132          133          134          135 
## 0.1221438625 0.1227562023 0.1233653098 0.1239710926 0.1245734588 
##          136          137          138          139          140 
## 0.1251723181 0.1257675809 0.1263591591 0.1269469654 0.1275309145 
##          141          142          143          144          145 
## 0.1281109220 0.1286869054 0.1292587835 0.1298264772 0.1303899089 
##          146          147          148          149          150 
## 0.1309490029 0.1315036858 0.1320538858 0.1325995337 0.1331405623 
##          151          152          153          154          155 
## 0.1336769068 0.1342085048 0.1347352965 0.1352572246 0.1357742346 
##          156          157          158          159          160 
## 0.1362862746 0.1367932959 0.1372952523 0.1377921010 0.1382838022 
##          161          162          163          164          165 
## 0.1387703190 0.1392516182 0.1397276696 0.1401984465 0.1406639257 
##          166          167          168          169          170 
## 0.1411240876 0.1415789158 0.1420283981 0.1424725256 0.1429112932 
##          171          172          173          174          175 
## 0.1433446997 0.1437727476 0.1441954434 0.1446127974 0.1450248239 
##          176          177          178          179          180 
## 0.1454315409 0.1458329707 0.1462291394 0.1466200769 0.1470058172 
##          181          182          183          184          185 
## 0.1473863984 0.1477618623 0.1481322547 0.1484976253 0.1488580275 
##          186          187          188          189          190 
## 0.1492135187 0.1495641598 0.1499100156 0.1502511544 0.1505876479 
##          191          192          193          194          195 
## 0.1509195716 0.1512470040 0.1515700271 0.1518887257 0.1522031881 
##          196          197          198          199          200 
## 0.1525135051 0.1528197705 0.1531220805 0.1534205341 0.1537152323 
##          201          202          203          204          205 
## 0.1540062784 0.1542937776 0.1545778369 0.1548585651 0.1551360720 
##          206          207          208          209          210 
## 0.1554104690 0.1556818683 0.1559503828 0.1562161262 0.1564792123 
##          211          212          213          214          215 
## 0.1567397551 0.1569978684 0.1572536657 0.1575072598 0.1577587626 
##          216          217          218          219          220 
## 0.1580082849 0.1582559361 0.1585018241 0.1587460546 0.1589887314 
##          221          222          223          224          225 
## 0.1592299559 0.1594698265 0.1597084391 0.1599458862 0.1601822569 
##          226          227          228          229          230 
## 0.1604176367 0.1606521070 0.1608857453 0.1611186246 0.1613508133 
##          231          232          233          234          235 
## 0.1615823751 0.1618133684 0.1620438466 0.1622738577 0.1625034440 
##          236          237          238          239          240 
## 0.1627326421 0.1629614825 0.1631899899 0.1634181825 0.1636460724 
##          241          242          243          244          245 
## 0.1638736650 0.1641009594 0.1643279479 0.1645546161 0.1647809429 
##          246          247          248          249          250 
## 0.1650069003 0.1652324535 0.1654575610 0.1656821741 0.1659062375 
##          251          252          253          254          255 
## 0.1661296892 0.1663524602 0.1665744749 0.1667956510 0.1670158999 
##          256          257          258          259          260 
## 0.1672351262 0.1674532286 0.1676700993 0.1678856245 0.1680996846 
##          261          262          263          264          265 
## 0.1683121543 0.1685229027 0.1687317935 0.1689386853 0.1691434319 
##          266          267          268          269          270 
## 0.1693458823 0.1695458808 0.1697432678 0.1699378795 0.1701295484 
##          271          272          273          274          275 
## 0.1703181035 0.1705033707 0.1706851728 0.1708633300 0.1710376601 
##          276          277          278          279          280 
## 0.1712079787 0.1713740998 0.1715358356 0.1716929973 0.1718453948 
##          281          282          283          284          285 
## 0.1719928375 0.1721351345 0.1722720945 0.1724035268 0.1725292406 
##          286          287          288          289          290 
## 0.1726490462 0.1727627547 0.1728701786 0.1729711318 0.1730654300 
##          291          292          293          294          295 
## 0.1731528909 0.1732333345 0.1733065833 0.1733724625 0.1734308003 
##          296          297          298          299          300 
## 0.1734814281 0.1735241806 0.1735588961 0.1735854168 0.1736035887 
##          301          302          303          304          305 
## 0.1736132621 0.1736142915 0.1736065359 0.1735898589 0.1735641289 
##          306          307          308          309          310 
## 0.1735292192 0.1734850081 0.1734313790 0.1733682206 0.1732954268 
##          311          312          313          314          315 
## 0.1732128972 0.1731205365 0.1730182554 0.1729059698 0.1727836018 
##          316          317          318          319          320 
## 0.1726510787 0.1725083338 0.1723553064 0.1721919413 0.1720181892 
##          321          322          323          324          325 
## 0.1718340067 0.1716393562 0.1714342060 0.1712185300 0.1709923081 
##          326          327          328          329          330 
## 0.1707555258 0.1705081745 0.1702502509 0.1699817577 0.1697027031 
##          331          332          333          334          335 
## 0.1694131005 0.1691129692 0.1688023335 0.1684812232 0.1681496732 
##          336          337          338          339          340 
## 0.1678077236 0.1674554197 0.1670928113 0.1667199536 0.1663369062 
##          341          342          343          344          345 
## 0.1659437335 0.1655405043 0.1651272922 0.1647041747 0.1642712339 
##          346          347          348          349          350 
## 0.1638285557 0.1633762302 0.1629143514 0.1624430169 0.1619623281 
##          351          352          353          354          355 
## 0.1614723898 0.1609733102 0.1604652008 0.1599481764 0.1594223546 
##          356          357          358          359          360 
## 0.1588878560 0.1583448041 0.1577933247 0.1572335466 0.1566656007 
##          361          362          363          364          365 
## 0.1560896202 0.1555057407 0.1549140996 0.1543148362 0.1537080919 
##          366          367          368          369          370 
## 0.1530940095 0.1524727335 0.1518444098 0.1512091858 0.1505672098 
##          371          372          373          374          375 
## 0.1499186316 0.1492636018 0.1486022719 0.1479347942 0.1472613218 
##          376          377          378          379          380 
## 0.1465820083 0.1458970078 0.1452064749 0.1445105642 0.1438094309 
##          381          382          383          384          385 
## 0.1431032299 0.1423921166 0.1416762459 0.1409557728 0.1402308519 
##          386          387          388          389          390 
## 0.1395016376 0.1387682840 0.1380309445 0.1372897720 0.1365449191 
##          391          392          393          394          395 
## 0.1357965372 0.1350447774 0.1342897897 0.1335317234 0.1327707267 
##          396          397          398          399          400 
## 0.1320069470 0.1312405305 0.1304716222 0.1297003662 0.1289269053 
##          401          402          403          404          405 
## 0.1281513808 0.1273739331 0.1265947010 0.1258138219 0.1250314319 
##          406          407          408          409          410 
## 0.1242476656 0.1234626560 0.1226765348 0.1218894319 0.1211014757 
##          411          412          413          414          415 
## 0.1203127931 0.1195235091 0.1187337472 0.1179436292 0.1171532752 
##          416          417          418          419          420 
## 0.1163628035 0.1155723308 0.1147819718 0.1139918396 0.1132020454 
##          421          422          423          424          425 
## 0.1124126988 0.1116239073 0.1108357768 0.1100484113 0.1092619130 
##          426          427          428          429          430 
## 0.1084763822 0.1076919174 0.1069086152 0.1061265705 0.1053458762 
##          431          432          433          434          435 
## 0.1045666233 0.1037889013 0.1030127975 0.1022383976 0.1014657852 
##          436          437          438          439          440 
## 0.1006950425 0.0999262495 0.0991594845 0.0983948242 0.0976323432 
##          441          442          443          444          445 
## 0.0968721145 0.0961142094 0.0953586972 0.0946056458 0.0938551210 
##          446          447          448          449          450 
## 0.0931071871 0.0923619067 0.0916193406 0.0908795481 0.0901425866 
##          451          452          453          454          455 
## 0.0894085121 0.0886773787 0.0879492392 0.0872241446 0.0865021444 
##          456          457          458          459          460 
## 0.0857832864 0.0850676172 0.0843551814 0.0836460226 0.0829401825 
##          461          462          463          464          465 
## 0.0822377016 0.0815386188 0.0808429716 0.0801507961 0.0794621272 
##          466          467          468          469          470 
## 0.0787769981 0.0780954408 0.0774174861 0.0767431633 0.0760725005 
##          471          472          473          474          475 
## 0.0754055245 0.0747422609 0.0740827341 0.0734269670 0.0727749818 
##          476          477          478          479          480 
## 0.0721267991 0.0714824387 0.0708419189 0.0702052572 0.0695724698 
##          481          482          483          484          485 
## 0.0689435721 0.0683185782 0.0676975012 0.0670803533 0.0664671457 
##          486          487          488          489          490 
## 0.0658578885 0.0652525910 0.0646512614 0.0640539072 0.0634605348 
##          491          492          493          494          495 
## 0.0628711499 0.0622857573 0.0617043608 0.0611269635 0.0605535678 
##          496          497          498          499          500 
## 0.0599841751 0.0594187862 0.0588574011 0.0583000191 0.0577466387 
##          501          502          503          504          505 
## 0.0571972577 0.0566518734 0.0561104821 0.0555730799 0.0550396618 
##          506          507          508          509          510 
## 0.0545102224 0.0539847559 0.0534632555 0.0529457142 0.0524321242 
##          511          512          513          514          515 
## 0.0519224773 0.0514167646 0.0509149770 0.0504171045 0.0499231370 
##          516          517          518          519          520 
## 0.0494330636 0.0489468733 0.0484645542 0.0479860945 0.0475114815 
##          521          522          523          524          525 
## 0.0470407024 0.0465737439 0.0461105924 0.0456512337 0.0451956536 
##          526          527          528          529          530 
## 0.0447438373 0.0442957698 0.0438514356 0.0434108191 0.0429739043 
##          531          532          533          534          535 
## 0.0425406749 0.0421111144 0.0416852060 0.0412629325 0.0408442767 
##          536          537          538          539          540 
## 0.0404292210 0.0400177476 0.0396098385 0.0392054756 0.0388046404 
##          541          542          543          544          545 
## 0.0384073142 0.0380134784 0.0376231139 0.0372362017 0.0368527225 
##          546          547          548          549          550 
## 0.0364726567 0.0360959850 0.0357226875 0.0353527444 0.0349861358 
##          551          552          553          554          555 
## 0.0346228416 0.0342628417 0.0339061158 0.0335526435 0.0332024044 
##          556          557          558          559          560 
## 0.0328553779 0.0325115435 0.0321708806 0.0318333683 0.0314989860 
##          561          562          563          564          565 
## 0.0311677127 0.0308395277 0.0305144100 0.0301923387 0.0298732928 
##          566          567          568          569          570 
## 0.0295572513 0.0292441932 0.0289340976 0.0286269433 0.0283227093 
##          571          572          573          574          575 
## 0.0280213747 0.0277229183 0.0274273191 0.0271345562 0.0268446085 
##          576          577          578          579          580 
## 0.0265574551 0.0262730750 0.0259914473 0.0257125511 0.0254363655 
##          581          582          583          584          585 
## 0.0251628698 0.0248920431 0.0246238648 0.0243583140 0.0240953703 
##          586          587          588          589          590 
## 0.0238350130 0.0235772215 0.0233219754 0.0230692544 0.0228190380 
##          591          592          593          594          595 
## 0.0225713059 0.0223260380 0.0220832140 0.0218428141 0.0216048180 
##          596          597          598          599          600 
## 0.0213692059 0.0211359580 0.0209050545 0.0206764757 0.0204502020 
##          601          602          603          604          605 
## 0.0202262139 0.0200044920 0.0197850170 0.0195677695 0.0193527304 
##          606          607          608          609          610 
## 0.0191398808 0.0189292015 0.0187206737 0.0185142787 0.0183099977 
##          611          612          613          614          615 
## 0.0181078122 0.0179077038 0.0177096539 0.0175136444 0.0173196570 
##          616          617          618          619          620 
## 0.0171276737 0.0169376765 0.0167496476 0.0165635692 0.0163794236 
##          621          622          623          624          625 
## 0.0161971933 0.0160168609 0.0158384090 0.0156618204 0.0154870780 
##          626          627          628          629          630 
## 0.0153141648 0.0151430638 0.0149737584 0.0148062318 0.0146404676 
##          631          632          633          634          635 
## 0.0144764491 0.0143141601 0.0141535844 0.0139947059 0.0138375085 
##          636          637          638          639          640 
## 0.0136819763 0.0135280937 0.0133758448 0.0132252142 0.0130761864 
##          641          642          643          644          645 
## 0.0129287461 0.0127828781 0.0126385672 0.0124957985 0.0123545571 
##          646          647          648          649          650 
## 0.0122148282 0.0120765972 0.0119398494 0.0118045706 0.0116707463 
##          651          652          653          654          655 
## 0.0115383624 0.0114074047 0.0112778593 0.0111497122 0.0110229497 
##          656          657          658          659          660 
## 0.0108975582 0.0107735241 0.0106508339 0.0105294743 0.0104094321 
##          661          662          663          664          665 
## 0.0102906942 0.0101732476 0.0100570793 0.0099421766 0.0098285267 
##          666          667          668          669          670 
## 0.0097161171 0.0096049354 0.0094949691 0.0093862059 0.0092786338 
##          671          672          673          674          675 
## 0.0091722406 0.0090670144 0.0089629433 0.0088600157 0.0087582197 
##          676          677          678          679          680 
## 0.0086575440 0.0085579769 0.0084595073 0.0083621238 0.0082658154 
##          681          682          683          684          685 
## 0.0081705708 0.0080763793 0.0079832299 0.0078911119 0.0078000146 
##          686          687          688          689          690 
## 0.0077099274 0.0076208399 0.0075327418 0.0074456227 0.0073594724 
##          691          692          693          694          695 
## 0.0072742809 0.0071900381 0.0071067341 0.0070243592 0.0069429035 
##          696          697          698          699          700 
## 0.0068623575 0.0067827117 0.0067039564 0.0066260824 0.0065490804 
##          701          702          703          704          705 
## 0.0064729412 0.0063976557 0.0063232149 0.0062496097 0.0061768314 
##          706          707          708          709          710 
## 0.0061048713 0.0060337205 0.0059633705 0.0058938128 0.0058250389 
##          711          712          713          714          715 
## 0.0057570406 0.0056898094 0.0056233372 0.0055576160 0.0054926375 
##          716          717          718          719          720 
## 0.0054283940 0.0053648774 0.0053020800 0.0052399941 0.0051786120 
##          721          722          723          724          725 
## 0.0051179261 0.0050579289 0.0049986130 0.0049399710 0.0048819957 
##          726          727          728          729          730 
## 0.0048246797 0.0047680161 0.0047119976 0.0046566173 0.0046018683 
##          731          732          733          734          735 
## 0.0045477438 0.0044942368 0.0044413407 0.0043890488 0.0043373545 
##          736          737          738          739          740 
## 0.0042862514 0.0042357329 0.0041857927 0.0041364244 0.0040876217 
##          741          742          743          744          745 
## 0.0040393785 0.0039916886 0.0039445460 0.0038979446 0.0038518784 
##          746          747          748          749          750 
## 0.0038063417 0.0037613284 0.0037168330 0.0036728496 0.0036293727 
##          751          752          753          754          755 
## 0.0035863965 0.0035439157 0.0035019246 0.0034604179 0.0034193902 
##          756          757          758          759          760 
## 0.0033788362 0.0033387506 0.0032991283 0.0032599640 0.0032212527 
##          761          762          763          764          765 
## 0.0031829894 0.0031451691 0.0031077867 0.0030708375 0.0030343166 
##          766          767          768          769          770 
## 0.0029982192 0.0029625406 0.0029272761 0.0028924211 0.0028579709 
##          771          772          773          774          775 
## 0.0028239211 0.0027902672 0.0027570047 0.0027241292 0.0026916364 
##          776          777          778          779          780 
## 0.0026595219 0.0026277816 0.0025964112 0.0025654065 0.0025347635 
##          781          782          783          784          785 
## 0.0025044780 0.0024745460 0.0024449636 0.0024157267 0.0023868315 
##          786          787          788          789          790 
## 0.0023582741 0.0023300506 0.0023021574 0.0022745907 0.0022473467 
##          791          792          793          794          795 
## 0.0022204218 0.0021938124 0.0021675149 0.0021415258 0.0021158415 
##          796          797          798          799          800 
## 0.0020904587 0.0020653737 0.0020405834 0.0020160842 0.0019918730 
##          801          802          803          804          805 
## 0.0019679464 0.0019443011 0.0019209340 0.0018978419 0.0018750216 
##          806          807          808          809          810 
## 0.0018524700 0.0018301841 0.0018081608 0.0017863971 0.0017648901 
##          811          812          813          814          815 
## 0.0017436368 0.0017226342 0.0017018796 0.0016813700 0.0016611027 
##          816          817          818          819          820 
## 0.0016410748 0.0016212837 0.0016017266 0.0015824007 0.0015633036 
##          821          822          823          824          825 
## 0.0015444324 0.0015257846 0.0015073577 0.0014891490 0.0014711562 
##          826          827          828          829          830 
## 0.0014533766 0.0014358078 0.0014184474 0.0014012929 0.0013843421 
##          831          832          833          834          835 
## 0.0013675925 0.0013510418 0.0013346877 0.0013185279 0.0013025603 
##          836          837          838          839          840 
## 0.0012867824 0.0012711923 0.0012557876 0.0012405662 0.0012255260 
##          841          842          843          844          845 
## 0.0012106649 0.0011959808 0.0011814716 0.0011671354 0.0011529701 
##          846          847          848          849          850 
## 0.0011389737 0.0011251442 0.0011114797 0.0010979783 0.0010846381 
##          851          852          853          854          855 
## 0.0010714572 0.0010584338 0.0010455659 0.0010328518 0.0010202898 
##          856          857          858          859          860 
## 0.0010078779 0.0009956145 0.0009834978 0.0009715261 0.0009596978 
##          861          862          863          864          865 
## 0.0009480110 0.0009364643 0.0009250558 0.0009137841 0.0009026475 
##          866          867          868          869          870 
## 0.0008916445 0.0008807733 0.0008700326 0.0008594208 0.0008489364 
##          871          872          873          874          875 
## 0.0008385778 0.0008283436 0.0008182323 0.0008082425 0.0007983728 
##          876          877          878          879          880 
## 0.0007886217 0.0007789878 0.0007694698 0.0007600664 0.0007507760 
##          881          882          883          884          885 
## 0.0007415975 0.0007325295 0.0007235706 0.0007147197 0.0007059754 
##          886          887          888          889          890 
## 0.0006973364 0.0006888016 0.0006803697 0.0006720395 0.0006638098 
##          891          892          893          894          895 
## 0.0006556793 0.0006476469 0.0006397115 0.0006318720 0.0006241270 
##          896          897          898          899          900 
## 0.0006164757 0.0006089167 0.0006014492 0.0005940718 0.0005867837 
##          901          902          903          904          905 
## 0.0005795837 0.0005724708 0.0005654439 0.0005585021 0.0005516443 
##          906          907          908          909          910 
## 0.0005448695 0.0005381768 0.0005315651 0.0005250335 0.0005185811 
##          911          912          913          914          915 
## 0.0005122068 0.0005059099 0.0004996893 0.0004935441 0.0004874735 
##          916          917          918          919          920 
## 0.0004814765 0.0004755523 0.0004697000 0.0004639187 0.0004582077 
##          921          922          923          924          925 
## 0.0004525660 0.0004469929 0.0004414875 0.0004360490 0.0004306766 
##          926          927          928          929          930 
## 0.0004253695 0.0004201270 0.0004149482 0.0004098325 0.0004047790 
##          931          932          933          934          935 
## 0.0003997870 0.0003948557 0.0003899846 0.0003851727 0.0003804194 
##          936          937          938          939          940 
## 0.0003757241 0.0003710860 0.0003665044 0.0003619787 0.0003575081 
##          941          942          943          944          945 
## 0.0003530921 0.0003487299 0.0003444210 0.0003401646 0.0003359602 
##          946          947          948          949          950 
## 0.0003318071 0.0003277047 0.0003236525 0.0003196497 0.0003156958 
##          951          952          953          954          955 
## 0.0003117902 0.0003079324 0.0003041217 0.0003003576 0.0002966396 
##          956          957          958          959          960 
## 0.0002929670 0.0002893393 0.0002857561 0.0002822167 0.0002787206 
##          961          962          963          964          965 
## 0.0002752673 0.0002718563 0.0002684870 0.0002651590 0.0002618718 
##          966          967          968          969          970 
## 0.0002586249 0.0002554178 0.0002522500 0.0002491210 0.0002460304 
##          971          972          973          974          975 
## 0.0002429777 0.0002399624 0.0002369842 0.0002340424 0.0002311368 
##          976          977          978          979          980 
## 0.0002282669 0.0002254322 0.0002226323 0.0002198668 0.0002171352 
##          981          982          983          984          985 
## 0.0002144372 0.0002117724 0.0002091403 0.0002065406 0.0002039728 
##          986          987          988          989          990 
## 0.0002014366 0.0001989316 0.0001964574 0.0001940137 0.0001916000 
##          991          992          993          994          995 
## 0.0001892160 0.0001868614 0.0001845358 0.0001822388 0.0001799700 
##          996          997          998          999         1000 
## 0.0001777293 0.0001755161 0.0001733302 0.0001711712 0.0001690389 
## 
## $residual.scale
## [1] 1
```

Which will project the data back to the original data, but we don't want it to do that yet as we want to calculate the confidence intervals, for which we need the transformed data

```r
ys$lwr <- polis.glm$family$linkinv(ys$fit - 2*ys$se.fit)
ys$upr <- polis.glm$family$linkinv(ys$fit + 2*ys$se.fit)
ys$fit <- polis.glm$family$linkinv(ys$fit)
newdata <- data.frame(RATIO=xs, fit=ys$fit, lower=ys$lwr, upper=ys$upr)
head(newdata)
```

```
##       RATIO       fit     lower     upper
## 1 0.2100000 0.9723466 0.5518734 0.9990049
## 2 0.2730130 0.9719721 0.5513268 0.9989793
## 3 0.3360260 0.9715927 0.5507782 0.9989530
## 4 0.3990390 0.9712084 0.5502276 0.9989260
## 5 0.4620521 0.9708190 0.5496749 0.9988984
## 6 0.5250651 0.9704245 0.5491202 0.9988701
```

Awesome! Now it is time to plooooot (pretty, please)


```r
ggplot(newdata,aes(y=fit, x=RATIO)) +
  geom_line() +
  geom_ribbon(aes(ymin=lower,ymax=upper), fill='blue', alpha=.2) +
  theme_classic()
```

![plot of chunk unnamed-chunk-141](figure/unnamed-chunk-141-1.png) 

Ass this is a model with a single predictor, we could just put the raw data in there, such as

```r
ggplot(newdata,aes(y=fit, x=RATIO)) +
  geom_line() +
  geom_ribbon(aes(ymin=lower,ymax=upper), fill='blue', alpha=.2) +
  geom_point(data=polis,aes(y=PA,x=RATIO)) +
  theme_classic()
```

![plot of chunk unnamed-chunk-142](figure/unnamed-chunk-142-1.png) 


---------------
**Poisson regression**

The closer a poisson distribution is to 0, the more skewed it becomes. The further away from 0, the more it starts resembling a Gaussian. The problem start if the mean is less then 5. In a Poisson, we assume mean = variance. This ratio should be 1. However, a lot of data is over-dispersed, which means that this ratio >1. This can be caused by a lot of factors. A likely scenario is that the data is more variable then you expect based on your model. A second posibility is clumpiness in the data, which is stochastic difference between observations. Another source is an over-representation of zero's in the data.
-higher-than-expected variation (solved with *quasi-poisson*, *negative binomial*, or adding an *observation level random effect*, which will 'soak-up' the excess variability)
-clumpiness (solved with quasi-poisson)
-zero-inflation (solved with zero-inflated poisson)


```r
pois<-read.csv('data.pois.csv', strip.white=T)
head(pois)
```

```
##   y        x
## 1 1 1.024733
## 2 2 2.696719
## 3 3 3.626263
## 4 2 4.948643
## 5 4 6.024718
## 6 8 6.254113
```

```r
scatterplot(y~x, data=pois)
```

![plot of chunk unnamed-chunk-143](figure/unnamed-chunk-143-1.png) 

There is potential non-normality of the y variable. Instead of doing a transformation, let's model it as a Poisson, as this is count data. Good practise is to count the number of zero's and the number we're expecting to get

```r
pois.tab <-table(pois$y==0)
pois.tab
```

```
## 
## FALSE 
##    20
```

So there are no zero's in this data

Now we're going to express it as a percentage (0%). We want to know what proportion of the dataset should be zero. So how many do we expect to get? Get the mean first.

```r
mu <-mean(pois$y)
mu
```

```
## [1] 6.25
```

How many zero's do we expect from a Poisson distribution with mu = 6.25? We can generate data:

```r
cnts <-rpois(1000,mu)
pois.tab <- table(cnts==0)
pois.tab/sum(pois.tab)
```

```
## 
## FALSE  TRUE 
## 0.999 0.001
```

So let's now fit a model with a Poisson distribution

```r
pois.glm <- glm(y~x, data=pois, family='poisson')
pois.glm <- glm(y~x, data=pois, family=poisson(link='log'))
```

Now look at the residuals

```r
par(mfrow=c(2,3))
plot(pois.glm,ask=F,which=1:6)
```

![plot of chunk unnamed-chunk-148](figure/unnamed-chunk-148-1.png) 

No patterns or problems there, so on that basis, we can conclude that our model is not reliable so far. Now let's test for the lack of fit


```r
pois.resid <- sum(resid(pois.glm, type='pearson')^2)
1-pchisq(pois.resid,pois.glm$df.resid)
```

```
## [1] 0.4896553
```

```r
1-pchisq(pois.glm$deviance,pois.glm$df.resid)
```

```
## [1] 0.5076357
```

```r
pois.resid/pois.glm$df.resid
```

```
## [1] 0.9716981
```

```r
pois.glm$deviance/pois.glm$df.resid
```

```
## [1] 0.9569887
```

Great!
But now, how do we know it was even worth doing a Poisson? Fit a standard Gaussian and comparing the fits


```r
pois.glmG <- glm(y~x, data=pois,family='gaussian')
AICc(pois.glm,pois.glmG)
```

```
##           df     AICc
## pois.glm   2 91.02482
## pois.glmG  3 99.77600
```

Yes, it was necessary


```r
summary(pois.glm)
```

```
## 
## Call:
## glm(formula = y ~ x, family = poisson(link = "log"), data = pois)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -1.6353  -0.7049   0.0440   0.5624   2.0457  
## 
## Coefficients:
##             Estimate Std. Error z value Pr(>|z|)    
## (Intercept)  0.56002    0.25395   2.205   0.0274 *  
## x            0.11151    0.01858   6.000 1.97e-09 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for poisson family taken to be 1)
## 
##     Null deviance: 55.614  on 19  degrees of freedom
## Residual deviance: 17.226  on 18  degrees of freedom
## AIC: 90.319
## 
## Number of Fisher Scoring iterations: 4
```

Get the confidence intervals

```r
confint(pois.glm)
```

```
## Waiting for profiling to be done...
```

```
##                  2.5 %    97.5 %
## (Intercept) 0.04158778 1.0384240
## x           0.07556104 0.1485173
```

OR




```r
ci(pois.glm)
```

```
##              Estimate   CI lower  CI upper Std. Error      p-value
## (Intercept) 0.5600204 0.02649343 1.0935474 0.25394898 2.743671e-02
## x           0.1115114 0.07246668 0.1505562 0.01858457 1.970579e-09
```

Upper CI is `exp(0.1115114 + 2*0.01858457) `which is not the same as `exp(0.1505562)`

R^2 is:

```r
1-(pois.glm$deviance/pois.glm$null)
```

```
## [1] 0.6902629
```


Next one, quasi-poisson

```r
data.qp<-read.csv('data.qp.csv', strip.white=T)
head(data.qp)
```

```
##   y        x
## 1 0 1.024733
## 2 3 2.696719
## 3 0 3.626263
## 4 5 4.948643
## 5 6 6.024718
## 6 1 6.254113
```

```r
ggplot(data.qp,aes(y=y,x=x)) +
  geom_point() + 
  geom_rug() +
  geom_smooth(method='lm')
```

![plot of chunk unnamed-chunk-156](figure/unnamed-chunk-156-1.png) 

With `geom_rug()` we can identify clumpiness


```r
data.qp.tab<-table(data.qp$y==0)
data.qp.tab/sum(data.qp.tab)
```

```
## 
## FALSE  TRUE 
##  0.85  0.15
```

```r
mu <- mean(data.qp$y)
cnts <- rpois(1000,mu)
data.qp.tab <- table(cnts == 0)
data.qp.tab/sum(data.qp.tab)
```

```
## 
## FALSE  TRUE 
## 0.995 0.005
```

So it is *possible* the data is over-dispersed, because there are a lot of zero's. We will fit a Poisson, so we have something to compare to, and fit also a quasi-poisson


```r
data.qp.glm <- glm(y~x, data= data.qp, family='poisson')
data.qp.glm2 <- glm(y~x, data= data.qp, family='quasipoisson')
AICc(data.qp.glm,data.qp.glm2)
```

```
##              df     AICc
## data.qp.glm   2 135.5907
## data.qp.glm2  2       NA
```

NOTE: we cannot calculate the AIC from a quasi-poison. So the only option is to use the likely-ratio test.


```r
anova(data.qp.glm,data.qp.glm2)
```

```
## Analysis of Deviance Table
## 
## Model 1: y ~ x
## Model 2: y ~ x
##   Resid. Df Resid. Dev Df Deviance
## 1        18     69.987            
## 2        18     69.987  0        0
```

A quasi-poisson will estimate the mean first, and then go back and estimate the variance. For this reason the confidence intervals will be different for both models.

```r
summary(data.qp.glm2)
```

```
## 
## Call:
## glm(formula = y ~ x, family = "quasipoisson", data = data.qp)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -3.3665  -1.7360  -0.1239   0.7436   3.9335  
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)  
## (Intercept)   0.6996     0.4746   1.474   0.1577  
## x             0.1005     0.0353   2.846   0.0107 *
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for quasipoisson family taken to be 3.698815)
## 
##     Null deviance: 101.521  on 19  degrees of freedom
## Residual deviance:  69.987  on 18  degrees of freedom
## AIC: NA
## 
## Number of Fisher Scoring iterations: 5
```

The over-dispersion parameter is greater than 2 (which we use as a cut-off value), so we conclude there is overdispersion.

Another option is to use a negative binomal, which is:


```r
data.qp.nb <-glm.nb(y~x, data=data.qp)
```

Get the theta

```r
data.qp.nb$theta
```

```
## [1] 2.221211
```

This is not the exact same as the dispersion parameter, but we use it in the same way.


```r
summary(data.qp.nb)
```

```
## 
## Call:
## glm.nb(formula = y ~ x, data = data.qp, init.theta = 2.221210756, 
##     link = log)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -2.3182  -1.0703  -0.1635   0.4198   1.7886  
## 
## Coefficients:
##             Estimate Std. Error z value Pr(>|z|)    
## (Intercept)  0.41366    0.43526   0.950 0.341917    
## x            0.12575    0.03625   3.469 0.000523 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for Negative Binomial(2.2212) family taken to be 1)
## 
##     Null deviance: 34.058  on 19  degrees of freedom
## Residual deviance: 23.709  on 18  degrees of freedom
## AIC: 113.84
## 
## Number of Fisher Scoring iterations: 1
## 
## 
##               Theta:  2.22 
##           Std. Err.:  1.09 
## 
##  2 x log-likelihood:  -107.837
```
 
 We can use a nb for clumpiness or with zero-inflated data (up to 20% zero's)
 

```r
data.qp.glm3<-glm(y~x,data=data.qp,family=negative.binomial(theta=2.2))
```

For this we need to know the theta though, so this method is less useful than the previous one

Let's spend more time on zero-inflated models. There can be 2 reasons: 
-there is nothing to observe (can be modelled with Poisson)
-the observation is missed


```r
data.zip <- read.csv('data.zip.csv',strip.white=T)
head(data.zip)
```

```
##   y        x
## 1 0 1.042191
## 2 3 2.497906
## 3 1 3.676603
## 4 4 3.798047
## 5 5 4.870992
## 6 1 7.689958
```

```r
ggplot(data.zip,aes(y=y,x=x)) + geom_point()
```

![plot of chunk unnamed-chunk-166](figure/unnamed-chunk-166-1.png) 

What we observed and expected

```r
mu <- mean(data.zip$y)
cnts<-rpois(1000,mu)
data.zip.tab <-table(cnts == 0)
data.zip.tab/sum(data.zip.tab)
```

```
## 
## FALSE  TRUE 
## 0.923 0.077
```



Only handle the fact I have excess zero's

```r
data.zip.zip <- zeroinfl(y~x | 1, dist = "poisson", data = data.zip)
```

Or model the excess zero's as well, and see if there is a pattern

```r
data.zip.zip1 <- zeroinfl(y~x | x, dist = "poisson", data = data.zip)
```


```r
summary(data.zip.zip)
```

```
## 
## Call:
## zeroinfl(formula = y ~ x | 1, data = data.zip, dist = "poisson")
## 
## Pearson residuals:
##     Min      1Q  Median      3Q     Max 
## -1.0015 -0.9556 -0.3932  0.9663  1.6195 
## 
## Count model coefficients (poisson with log link):
##             Estimate Std. Error z value Pr(>|z|)    
## (Intercept)  0.70474    0.31960   2.205 0.027449 *  
## x            0.08734    0.02532   3.449 0.000563 ***
## 
## Zero-inflation model coefficients (binomial with logit link):
##             Estimate Std. Error z value Pr(>|z|)
## (Intercept)  -0.2292     0.4563  -0.502    0.615
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1 
## 
## Number of iterations in BFGS optimization: 13 
## Log-likelihood: -36.17 on 3 Df
```

```r
summary(data.zip.zip1)
```

```
## 
## Call:
## zeroinfl(formula = y ~ x | x, data = data.zip, dist = "poisson")
## 
## Pearson residuals:
##     Min      1Q  Median      3Q     Max 
## -1.2761 -0.7814 -0.5933  0.9078  2.1317 
## 
## Count model coefficients (poisson with log link):
##             Estimate Std. Error z value Pr(>|z|)    
## (Intercept)  0.61113    0.33001   1.852 0.064044 .  
## x            0.09389    0.02574   3.647 0.000265 ***
## 
## Zero-inflation model coefficients (binomial with logit link):
##             Estimate Std. Error z value Pr(>|z|)
## (Intercept)  -2.7305     1.8897  -1.445    0.148
## x             0.2170     0.1461   1.485    0.137
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1 
## 
## Number of iterations in BFGS optimization: 17 
## Log-likelihood: -34.41 on 4 Df
```

To see if there is a pattern in the data, look at this part:
'Zero-inflation model coefficients (binomial with logit link)'
In this case there is no pattern

Which models the data better?

```r
AICc(data.zip.zip,data.zip.zip1)
```

```
##               df     AICc
## data.zip.zip   3 79.84277
## data.zip.zip1  4 79.47724
```

Day 3 - Bayesian
==================

With frequentist it is almost impossible *not* to disprove the null-hypothesis, as the probability of the data is calculated from the data. We are not interested in the sample, we are interested in the population. 

```
                   *Frequentist*       *Bayesian*
Observed data     One possible          Fixed, true
Parameters        Fixed, true           Random, distribution
Inferences        Data                  Parameters
Probability       Long-run frequence    Degree of belief
                    $(P|H)$               $P(H|D)$
```

With the Frequentist approach you get one point value for the slope, with the Bayesian you get a distribution. When 90% of this distribution lies above or below zero, you can consider that there is an effect in the data. 


*Bayes rule*

*P*(*H*|*D*) = (*P*(*D*|*H*) * P(*H*)) / *P*(*D*)

posterior probability = ( likelyhood x prior probability) / normalizing constant

Propability distribution: area under curve must be 1
Likelihood distribution: are under curve is unknown

Above formula normalizes the area under the curve and makes it equal to 1

**Bayesian is robust to:**

> unequal sample sizes

> balance

> collinearity

> pair-wise comparisons (no need for *post-hoc* tests)


**MCMC-Sampling**

Random walks on distribution. Will always accept a position with a higher probability and whether or not a lower probability point is accepted depends on the actual likelihood of that point. For this reason, we will get a better overview of more probable parts of the distribution. 
This chain of walks is called a chain!
- Run 3 chains to make sure this is the distribution
- Burning: run for example for a 1000 steps (50% of the actual iterations you want to run) and see what an appropriate distance per step is. After this burning, chuck this burning run out.
- Number of iterations: determined by diagnostics that are run

Start with 10000 iterations and make trace plots:
- needs to be total chaos, when not, include more iterations
- needs to be repeatable

Autocorrelation
- ACF plot
- thinning, thinning factor 10 means only sample every 10th point. When thinning, make sure to increase the number of iterations.

Distribution plots
- multiple modes --> not one single population sampled
- highly skewed --> take median or mode
- area under curve is 1
- credibility intervals (same as confidence interval)

MCMCglmm is not too highly developed in R... Alternative is WinBUGS or JAGS (C++). These programs use a Gibbs sampler. This changes one parameter at a time (for example first intercept, then slope). Better samplers exist, one of which is implemented in STAND. 

In C++ not everything has to be scripted in the same order, but everything **will be run simultaneously**

JAGS can be run through other platforms requires and 3 inputs:
- file with model statement
- data in the form of a list
- parameters that define the run (number of iterations, burning etc)

From R: R2Jags

Start as simple as possible, get something to compare to

```r
fert <- read.csv('fertilizer.csv',strip.white=T)
summary(lm(YIELD~FERTILIZER,data=fert))
```

```
## 
## Call:
## lm(formula = YIELD ~ FERTILIZER, data = fert)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -22.79 -11.07  -5.00  12.00  29.79 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 51.93333   12.97904   4.001  0.00394 ** 
## FERTILIZER   0.81139    0.08367   9.697 1.07e-05 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 19 on 8 degrees of freedom
## Multiple R-squared:  0.9216,	Adjusted R-squared:  0.9118 
## F-statistic: 94.04 on 1 and 8 DF,  p-value: 1.067e-05
```



To get the contents of the package, type `MCMCpack:::`

This package only runs one chain at the time, so it should be run 3 times

```r
fert.mcmc1 <- MCMCregress(YIELD~FERTILIZER, data=fert,
                          burnin = 100, mcmc =1000, thin=1,seed=1)
```

Seed: the random seed, which dictates how the randomization works. So if you want to run another random run, select another seed:


```r
fert.mcmc2 <- MCMCregress(YIELD~FERTILIZER, data=fert,
                          burnin = 100, mcmc =1000, thin=1,seed=2)
fert.mcmc3 <- MCMCregress(YIELD~FERTILIZER, data=fert,
                          burnin = 100, mcmc =1000, thin=1,seed=3)
```

Now merge them together so you have one object which you can process together. 


```r
fert.mcmc <-as.mcmc.list(list(fert.mcmc1,fert.mcmc2,fert.mcmc3))
```

Now take advantage of things that are within coda, like the plotting function

```r
plot(fert.mcmc)
```

![plot of chunk unnamed-chunk-179](figure/unnamed-chunk-179-1.png) 

> Top row: intercept
> Middle row: slope
> Bottom row: variance

Colours of the lines, black, green and red (printed in that order) are the 3 runs. You want these runs to show the exact same. These are, so it's likely you have transfersed the entire prosterior. 

Notice that the slope and intercept are fairly normal, so to describe it a mean would be OK. The variance is skewed so to describe it a mode or median would be better.

We used a thinning of 1, which is a lag of one in the next output:

```r
autocorr.diag(fert.mcmc)
```

```
##          (Intercept)   FERTILIZER       sigma2
## Lag 0   1.000000e+00  1.000000000  1.000000000
## Lag 1   2.134962e-02  0.021334473  0.209033144
## Lag 5  -7.698278e-05 -0.011779817 -0.019172034
## Lag 10 -2.021870e-02 -0.030589123 -0.034440715
## Lag 50 -6.545629e-03  0.006366862 -0.003426807
```
We don't want to see high numbers in this plot. A high number is something higher that .2. We have one sigma2 value which is greater than .2, so we have quite a bit of correlation. So we need to re-run the chains with a different thinning factor. Now this output is obvious. Note that sometimes this autocorrelation loops back to higher value. 

Let's try a thin of 5 (now need to increase the number of iterations 5 times)


```r
fert.mcmc1 <- MCMCregress(YIELD~FERTILIZER, data=fert,
                          burnin = 100, mcmc =5000, thin=5,seed=2)
fert.mcmc2 <- MCMCregress(YIELD~FERTILIZER, data=fert,
                          burnin = 100, mcmc =5000, thin=5,seed=2)
fert.mcmc3 <- MCMCregress(YIELD~FERTILIZER, data=fert,
                          burnin = 100, mcmc =5000, thin=5,seed=3)
fert.mcmc <-as.mcmc.list(list(fert.mcmc1,fert.mcmc2,fert.mcmc3))
plot(fert.mcmc)
```

![plot of chunk unnamed-chunk-181](figure/unnamed-chunk-181-1.png) 

```r
autocorr.diag(fert.mcmc)
```

```
##         (Intercept)   FERTILIZER       sigma2
## Lag 0    1.00000000  1.000000000  1.000000000
## Lag 5   -0.01923931 -0.029998359 -0.036489106
## Lag 25  -0.01544306 -0.013277458 -0.022565790
## Lag 50  -0.02206422 -0.024020508 -0.007797309
## Lag 250 -0.01786679 -0.005201497  0.055493338
```

Now all looks good.

There is another diagnostic, the raftery diagnostic. Looks if you had enough samples in your chains

```r
raftery.diag(fert.mcmc)
```

```
## [[1]]
## 
## Quantile (q) = 0.025
## Accuracy (r) = +/- 0.005
## Probability (s) = 0.95 
## 
## You need a sample size of at least 3746 with these values of q, r and s
## 
## [[2]]
## 
## Quantile (q) = 0.025
## Accuracy (r) = +/- 0.005
## Probability (s) = 0.95 
## 
## You need a sample size of at least 3746 with these values of q, r and s
## 
## [[3]]
## 
## Quantile (q) = 0.025
## Accuracy (r) = +/- 0.005
## Probability (s) = 0.95 
## 
## You need a sample size of at least 3746 with these values of q, r and s
```

So we need at least 3746 samples in our chains, we had 5000, so yay.

Now summarize the data:

```r
summary(fert.mcmc)
```

```
## 
## Iterations = 101:5096
## Thinning interval = 5 
## Number of chains = 3 
## Sample size per chain = 1000 
## 
## 1. Empirical mean and standard deviation for each variable,
##    plus standard error of the mean:
## 
##                 Mean        SD Naive SE Time-series SE
## (Intercept)  51.6028  15.09567 0.275608       0.275694
## FERTILIZER    0.8136   0.09741 0.001778       0.001779
## sigma2      481.4807 326.87948 5.967975       6.082233
## 
## 2. Quantiles for each variable:
## 
##                 2.5%      25%      50%      75%    97.5%
## (Intercept)  22.6589  42.1738  51.3531  60.7618   83.090
## FERTILIZER    0.6177   0.7533   0.8145   0.8742    1.003
## sigma2      163.6239 280.8889 393.0157 567.4057 1304.868
```

We can now make the statement that the slope is between 0.62 and 1.0 with 95% probability. We CANNOT make the same statement with 95% confidence intervals.


```r
fert.mcmc <- rbind(fert.mcmc1, fert.mcmc2,fert.mcmc3)
head(fert.mcmc)
```

```
##      (Intercept) FERTILIZER   sigma2
## [1,]    55.38547  0.8549987 342.9582
## [2,]    46.22085  0.8643291 232.6710
## [3,]    45.03659  0.9064213 533.8965
## [4,]    91.29923  0.5449321 651.3404
## [5,]    84.76013  0.6310048 460.3705
## [6,]    40.13982  0.8640221 434.3334
```

We now have 3000 estimates of slopes (1000 per run).

We can now make a histogram with the distribution of the slopes.

```r
par(mfrow=c(1,2))
hist(fert.mcmc[,2])
```

![plot of chunk unnamed-chunk-185](figure/unnamed-chunk-185-1.png) 

The estimate of the slope is the mean or the median:

```r
mean(fert.mcmc[,2])
```

```
## [1] 0.8135513
```

And the credibility intervals are:

```r
HPDinterval(as.mcmc(fert.mcmc[,2]))
```

```
##          lower     upper
## var1 0.6134619 0.9973995
## attr(,"Probability")
## [1] 0.95
```

These intervals are density based, and don't have to be equal around the mean.

In library(plyr)

```r
fert.sum <- adply(fert.mcmc,2, function(x) {
  data.frame(Mean=mean(x), Median=median(x),HPDinterval(as.mcmc(x)))
})
```

In this formula, the 2 refers to the columns. 

```r
head(fert.sum)
```

```
##            X1        Mean      Median       lower        upper
## 1 (Intercept)  51.6028219  51.3530676  21.9276011   81.0833320
## 2  FERTILIZER   0.8135513   0.8145226   0.6134619    0.9973995
## 3      sigma2 481.4806653 393.0156667 117.0085183 1098.0993029
```

We can now see how many times the estimate of the slope was greater than 0:count up the number of slopes greater than zero and devide this by 3000 (for the total number of iterations):

```r
table(fert.mcmc[,2]>0)
```

```
## 
## TRUE 
## 3000
```

```r
table(fert.mcmc[,2]>0)/length(fert.mcmc[,2])
```

```
## 
## TRUE 
##    1
```

We can now also test our distribution agains any hypothesis (e.g. the slope is .8):

```r
table(fert.mcmc[,2]>0.8)
```

```
## 
## FALSE  TRUE 
##  1312  1688
```

```r
table(fert.mcmc[,2]>0.8)/length(fert.mcmc[,2])
```

```
## 
##     FALSE      TRUE 
## 0.4373333 0.5626667
```

In JAGS/BUGS, things are written in terms of precision, not variance.

Model:
y(i) ~ N(mu(i),tau)
mu(i) = beta(0) + beta(1*x(i))

We are going to use some non-informative priors:

> beta(0) ~ *N*(0,0.000001) : the intercept is 0 with a very high uncertaincy
> beta(1) ~ *N*(0,0.000001) : the slope is 0 with a very high uncertaincy
> sigma ~ *Uniform*(0,100)

The model is (by using R to write the text file for you)

```r
modelString="
model {
  #Likelihood
  for (i in 1:n) {
    y[i]~dnorm(mu[i],tau)
    mu[i] <- beta0+beta1*x[i]
  }
  
  #Priors
  beta0 ~dnorm(0,1.0E-6)
  beta1 ~dnorm(0,1.0E-6)
  tau <- 1/ (sigma * sigma)
  sigma~dunif(0,100)
}
"
writeLines(modelString,con="regression.txt")
```

Now we need to create a list with out data in it. The above model is expecting x, y and n (number of rows)

```r
fert.list <- with(fert,
                  list(x=FERTILIZER,
                  y=YIELD,n=nrow(fert))
                  )
```

Now add the parameters

```r
params<- c("beta0","beta1","sigma")
```

Now put it in Jags
library(R2Jags)
Use the chache=TRUE command in the {r} for knitr, so it stores the data and it doesn't have to re-run every time you run knitter. Do this for any large amounts of data

```r
library("R2jags", lib.loc="/Library/Frameworks/R.framework/Versions/3.0/Resources/library")
fert.r2jags <- jags(data=fert.list,
                    inits=NULL,
                    parameters.to.save=params,
                    model.file="regression.txt",
                    n.chains=3,
                    n.iter=1000,
                    n.burnin=100,
                    n.thin=5
                    )
```

```
## module glm loaded
```

```
## Compiling model graph
##    Resolving undeclared variables
##    Allocating nodes
##    Graph Size: 50
## 
## Initializing model
```

In the output, the first line represents the burnin, the second one the iterations.
This has now come back from jags and we need to run diagnostics

`quartz()` can be used if figure margins are too large


```r
plot(as.mcmc(fert.r2jags))
```

```
## Error in character(ncol(y)): invalid 'length' argument
```

We are not too happy with the trace plots so re-run with more iterations and higher burnin


```r
library("R2jags", lib.loc="/Library/Frameworks/R.framework/Versions/3.0/Resources/library")
fert.r2jags2 <- jags(data=fert.list,
                    inits=NULL,
                    parameters.to.save=params,
                    model.file="regression.txt",
                    n.chains=3,
                    n.iter=10000,
                    n.burnin=1000,
                    n.thin=5
                    )
```

```
## Compiling model graph
##    Resolving undeclared variables
##    Allocating nodes
##    Graph Size: 50
## 
## Initializing model
```

```r
plot(as.mcmc(fert.r2jags2))
```

![plot of chunk unnamed-chunk-196](figure/unnamed-chunk-196-1.png) 

Auto-correlation plot:

```r
autocorr.diag(as.mcmc(fert.r2jags2))
```

```
## Error in complete.cases(object): not all arguments have the same length
```


```r
print(fert.r2jags)
```

```
## $model
## JAGS model:
## 
## 
## model {
##   #Likelihood
##   for (i in 1:n) {
##     y[i]~dnorm(mu[i],tau)
##     mu[i] <- beta0+beta1*x[i]
##   }
##   
##   #Priors
##   beta0 ~dnorm(0,1.0E-6)
##   beta1 ~dnorm(0,1.0E-6)
##   tau <- 1/ (sigma * sigma)
##   sigma~dunif(0,100)
## }
## 
## Fully observed variables:
##  n x y 
## 
## $BUGSoutput
## $n.chains
## [1] 3
## 
## $n.iter
## [1] 1000
## 
## $n.burnin
## [1] 100
## 
## $n.thin
## [1] 5
## 
## $n.keep
## [1] 180
## 
## $n.sims
## [1] 540
## 
## $sims.array
## , , beta0
## 
##            [,1]       [,2]       [,3]
##   [1,] 77.90309  37.891654  57.232413
##   [2,] 72.14328  30.571542  37.848025
##   [3,] 45.01422  64.212696  50.040130
##   [4,] 31.01531  24.747651  57.549357
##   [5,] 41.20492  63.462691  70.840486
##   [6,] 42.75106  68.820837  10.355595
##   [7,] 55.29945  37.777792  39.567621
##   [8,] 54.84640  58.316375  27.927736
##   [9,] 69.65026  66.103466  52.483056
##  [10,] 73.08428  47.959299  39.152711
##  [11,] 81.86409  54.601177  -1.984845
##  [12,] 58.09551  84.187914  55.041760
##  [13,] 49.01540  32.159274  72.533234
##  [14,] 47.19989  39.242239  70.763561
##  [15,] 69.89231  56.362910  51.725009
##  [16,] 53.35316  51.559865  64.997723
##  [17,] 72.62167  36.862189  48.256269
##  [18,] 39.87604  61.437841  52.763529
##  [19,] 66.77821  41.637487  34.117512
##  [20,] 46.80831  28.790751  69.892947
##  [21,] 54.92076  52.782817  70.502571
##  [22,] 61.58018  61.031031  68.817430
##  [23,] 69.39194  52.593145  50.076044
##  [24,] 50.36817  30.809066  65.258798
##  [25,] 69.00666  37.921343  57.708141
##  [26,] 35.21685  42.794529  46.826450
##  [27,] 46.46631  51.617243  40.129279
##  [28,] 31.05910  33.608694  48.602041
##  [29,] 56.25278  52.205238  59.640160
##  [30,] 32.33170  50.208453  56.690764
##  [31,] 46.24281  56.956127  32.393714
##  [32,] 48.98815  33.899096  64.670460
##  [33,] 42.61353  37.135538  68.866203
##  [34,] 57.69658  44.691626  30.735352
##  [35,] 53.00144  26.927484  44.512353
##  [36,] 49.42679  20.836191  43.102176
##  [37,] 45.46965  57.790703  54.213786
##  [38,] 51.49039  65.879918  64.445654
##  [39,] 47.70483  35.957849  63.309220
##  [40,] 61.91871  51.073669  38.624571
##  [41,] 42.01866  48.040361  58.010478
##  [42,] 54.96192  49.758138  51.402927
##  [43,] 49.65181  42.578927  63.550623
##  [44,] 63.92494  47.672272  32.281073
##  [45,] 59.17785  38.948839  44.139726
##  [46,] 39.88067  57.945193  38.905803
##  [47,] 52.76976  29.096009  59.353366
##  [48,] 37.37766  61.167223  43.219627
##  [49,] 80.47909  87.711368  47.124722
##  [50,] 61.27780  53.783100  58.904485
##  [51,] 54.35836  59.855986  43.366037
##  [52,] 80.82242 102.030662  47.109182
##  [53,] 29.11093  53.469993  21.553850
##  [54,] 13.87112  47.105650  82.411780
##  [55,] 52.29449  24.541615  63.735108
##  [56,] 41.17904  -4.204677  71.567554
##  [57,] 55.84767  78.072732  42.793378
##  [58,] 69.52698  42.197142  52.653377
##  [59,] 23.00185  46.060903  54.227303
##  [60,] 65.22351  28.980493  54.259261
##  [61,] 44.39762  55.401832  60.105232
##  [62,] 51.79551  33.685409  35.456965
##  [63,] 69.30817  24.787593  49.355557
##  [64,] 56.32197  51.403008  24.885209
##  [65,] 45.19207  60.605840  51.352237
##  [66,] 25.04494  63.484465  28.116240
##  [67,] 42.79759  51.786565  36.737128
##  [68,] 47.68214  58.311070  39.665905
##  [69,] 61.26595  64.660631  44.334054
##  [70,] 37.58924  36.421710  31.494965
##  [71,] 71.29597  60.487025  70.424751
##  [72,] 70.37574  84.310600  35.798178
##  [73,] 43.75019  57.639619  33.341012
##  [74,] 71.34133  64.198380  49.884001
##  [75,] 61.10047  42.584473  21.482123
##  [76,] 76.79579  49.727098  73.146469
##  [77,] 36.18002  57.700982  18.590485
##  [78,] 46.16292  64.733265  28.986063
##  [79,] 64.86272  50.469750  49.812402
##  [80,] 74.34282  65.970413  58.900653
##  [81,] 49.96075  22.300120  43.340808
##  [82,] 60.37189  85.758120  55.782336
##  [83,] 48.95882  55.767796  88.208864
##  [84,] 36.18107  49.503649  25.596118
##  [85,] 65.40999  66.249598  60.574429
##  [86,] 51.73016  40.171245  50.425361
##  [87,] 24.17343  63.626396  72.301969
##  [88,] 70.82334  60.238832  28.673661
##  [89,] 44.20321  51.914541  42.333542
##  [90,] 57.14633  51.077878  71.349315
##  [91,] 40.40647  57.702631  36.225943
##  [92,] 67.76381  23.680939  57.996799
##  [93,] 57.50883  53.398183  46.043080
##  [94,] 45.22815  56.353106  63.533069
##  [95,] 51.55179  72.463726  29.898680
##  [96,] 62.91169  31.597341  49.392783
##  [97,] 50.00628  45.574896  55.200253
##  [98,] 29.39090  45.034084  42.660351
##  [99,] 35.66871  43.859450  56.868464
## [100,] 51.18105  29.368006  56.857666
## [101,] 55.22516  62.759664  50.777730
## [102,] 59.94042  78.774134  83.153895
## [103,] 60.80205  76.046784  51.363907
## [104,] 30.81394  33.383757  34.767624
## [105,] 57.47932  62.936723  63.794648
## [106,] 59.19315  58.918461  46.193554
## [107,] 56.36900  66.148787  34.128237
## [108,] 63.16183  62.829038  49.160658
## [109,] 81.75272  81.442910  28.585495
## [110,] 38.09487  44.229310  77.439065
## [111,] 76.66485  84.306789  43.405348
## [112,] 22.96521  50.261000  46.104079
## [113,] 61.43181  56.274463  48.372212
## [114,] 58.01402  53.173346  63.201328
## [115,] 51.06787  31.633774  37.584150
## [116,] 60.03097  51.726770  48.269839
## [117,] 40.53318  64.080098  57.827536
## [118,] 55.06263  52.983645  51.089733
## [119,] 56.74077  41.869762  47.978001
## [120,] 49.21973  36.859289  37.310017
## [121,] 39.82955  38.153403  29.500311
## [122,] 69.69472  47.052969  47.811998
## [123,] 44.59718  56.220240  68.966096
## [124,] 39.69521  43.441937  25.811038
## [125,] 38.02944  31.064086  30.191120
## [126,] 52.30581   6.941834  26.589120
## [127,] 58.69933  57.666178  50.608020
## [128,] 57.20549  70.067218  62.503533
## [129,] 68.37969  51.069419  74.313515
## [130,] 66.78415  58.017283  41.116014
## [131,] 71.05243  61.461914  30.225744
## [132,] 39.59731  25.013655  47.586837
## [133,] 62.30630  56.184540  54.167018
## [134,] 71.69719  48.669529  50.470747
## [135,] 53.54447  51.951651  55.175718
## [136,] 52.10638  56.214983  67.676767
## [137,] 45.12976  55.734701  42.976482
## [138,] 41.25091  37.805535  55.539441
## [139,] 44.55249  56.154847  43.568805
## [140,] 67.24487  61.625053  34.095266
## [141,] 74.53742  38.854098   7.954745
## [142,] 38.03766  89.513615  33.452785
## [143,] 51.48092  45.715384  64.700192
## [144,] 66.23987  51.977499  77.098069
## [145,] 49.47309   5.876293  57.189944
## [146,] 51.94211  54.419302  49.830390
## [147,] 37.25350  59.593435  76.620672
## [148,] 84.73423  54.227605  41.667279
## [149,] 42.22177  45.577419  49.532029
## [150,] 42.82796  -9.112670  42.755183
## [151,] 38.65688  40.526237  54.087372
## [152,] 63.27948  64.794712  43.315465
## [153,] 63.01020  76.935575  62.720297
## [154,] 51.60562  54.193862  53.247826
## [155,] 42.89091  48.283516  54.811651
## [156,] 75.94877  67.777818  34.621271
## [157,] 14.57678  63.076736  54.835237
## [158,] 81.24598  49.819490  26.702933
## [159,] 53.81846  88.630172 106.560979
## [160,] 66.64479  23.714444  46.931399
## [161,] 36.19776  49.611746  49.988424
## [162,] 70.80411  52.786661  64.602921
## [163,] 48.52927  53.544604  52.534614
## [164,] 52.71667  72.775004  34.495001
## [165,] 31.17704  55.977809  39.744835
## [166,] 46.22037  31.723266  28.567222
## [167,] 48.33662  36.268143  47.289521
## [168,] 68.09639  45.657384  61.288267
## [169,] 45.06848  53.967293  55.650386
## [170,] 47.02498  52.848702  62.388917
## [171,] 32.07151  63.118460  29.059276
## [172,] 54.24636  82.222420  63.100373
## [173,] 42.20842  39.495963  43.523050
## [174,] 47.04834  66.276861  40.275760
## [175,] 58.34862  92.836417  40.499200
## [176,] 61.62793  39.158655  24.081415
## [177,] 66.50385  73.045054  47.699350
## [178,] 52.62902  36.265758  41.621181
## [179,] 38.01545  53.792120  43.923797
## [180,] 48.25115  36.695335  39.621762
## 
## , , beta1
## 
##             [,1]      [,2]      [,3]
##   [1,] 0.6538775 0.9456800 0.7585591
##   [2,] 0.6644241 0.9548068 0.9456600
##   [3,] 0.8617023 0.6828594 0.8471324
##   [4,] 0.9063204 0.8940281 0.7612722
##   [5,] 0.8416363 0.8098931 0.6810219
##   [6,] 0.8766048 0.7451504 0.9520094
##   [7,] 0.8247159 0.8227388 0.9605434
##   [8,] 0.7946871 0.8287518 0.9719925
##   [9,] 0.7521348 0.6936035 0.7397170
##  [10,] 0.6752700 0.8192477 0.8721601
##  [11,] 0.6578347 0.7791210 1.0683755
##  [12,] 0.7665937 0.6037457 0.8205488
##  [13,] 0.8480022 0.8620050 0.6962828
##  [14,] 0.8766174 0.8956744 0.7484295
##  [15,] 0.7119740 0.8330766 0.8346252
##  [16,] 0.8210406 0.8141126 0.6876485
##  [17,] 0.6788405 0.9024481 0.8884560
##  [18,] 0.8712727 0.7689773 0.7921979
##  [19,] 0.8074589 0.8596507 0.9536996
##  [20,] 0.8234777 0.9771099 0.7272371
##  [21,] 0.8710892 0.7715600 0.6737023
##  [22,] 0.7833810 0.8042923 0.7415219
##  [23,] 0.6784146 0.7472324 0.8375680
##  [24,] 0.8098688 0.9572765 0.7257849
##  [25,] 0.7727662 0.8682276 0.8492953
##  [26,] 0.9691626 0.8430324 0.8524982
##  [27,] 0.8193823 0.7707987 0.9378605
##  [28,] 0.9032286 0.9010878 0.8639909
##  [29,] 0.7062968 0.8365043 0.6969202
##  [30,] 0.9577749 0.8855114 0.7026028
##  [31,] 0.8216587 0.7944993 0.9214477
##  [32,] 0.8270485 0.8894407 0.6952547
##  [33,] 0.8662116 0.8472379 0.8085085
##  [34,] 0.7702616 0.8637157 0.8749440
##  [35,] 0.8792831 0.9647804 0.8538853
##  [36,] 0.7920673 0.9713151 0.8164203
##  [37,] 0.8841107 0.8415476 0.7892438
##  [38,] 0.8876719 0.7291473 0.7230728
##  [39,] 0.8020558 1.0035372 0.7526185
##  [40,] 0.7223206 0.8161566 0.8801425
##  [41,] 0.8894537 0.8392946 0.7924515
##  [42,] 0.7117359 0.8029169 0.8179257
##  [43,] 0.8434461 0.8774089 0.7894394
##  [44,] 0.7136931 0.8414235 0.9818275
##  [45,] 0.7833133 0.9041672 0.6477956
##  [46,] 0.9211362 0.7733722 0.8593682
##  [47,] 0.8733316 0.9764035 0.7599571
##  [48,] 0.7938204 0.7289641 0.9501017
##  [49,] 0.6694939 0.5710945 0.7700567
##  [50,] 0.7055726 0.7867868 0.8068047
##  [51,] 0.8245694 0.7727919 0.8553236
##  [52,] 0.6262290 0.7224086 0.8026133
##  [53,] 0.9030987 0.8387624 0.9837981
##  [54,] 1.0497247 0.9162806 0.6430943
##  [55,] 0.8409467 0.9595702 0.7029908
##  [56,] 0.8427878 0.9368876 0.6325701
##  [57,] 0.7462443 0.6256267 0.8735258
##  [58,] 0.6655723 0.8111593 0.7923947
##  [59,] 0.9743904 0.8279128 0.8739521
##  [60,] 0.7478843 1.0594378 0.7886511
##  [61,] 0.8956735 0.8385181 0.7719972
##  [62,] 0.8173497 0.9342741 0.8798110
##  [63,] 0.6948973 0.8948391 0.7850143
##  [64,] 0.8156141 0.9019203 1.0026027
##  [65,] 0.8854314 0.7140287 0.7767664
##  [66,] 0.9606195 0.6880196 1.0187215
##  [67,] 0.9279144 0.8465822 0.8908851
##  [68,] 0.7340722 0.8763643 0.8435618
##  [69,] 0.7618791 0.7220641 0.8575479
##  [70,] 0.9403084 0.8711635 0.9424207
##  [71,] 0.6538677 0.7941651 0.6877704
##  [72,] 0.6223053 0.6858344 0.9157247
##  [73,] 0.8174615 0.7558955 0.9307244
##  [74,] 0.6454402 0.7176755 0.7962436
##  [75,] 0.8019685 0.8394418 0.9639273
##  [76,] 0.7231161 0.8739930 0.6374051
##  [77,] 0.8903250 0.7559920 1.0261519
##  [78,] 0.8804017 0.7412649 1.0401220
##  [79,] 0.7884804 0.8992043 0.8008490
##  [80,] 0.6600617 0.7943497 0.7955924
##  [81,] 0.8463835 0.9473485 0.8957837
##  [82,] 0.7515603 0.5713864 0.8399689
##  [83,] 0.8746240 0.7153978 0.5453554
##  [84,] 0.8624241 0.8233075 1.0034529
##  [85,] 0.8135716 0.7261293 0.8427485
##  [86,] 0.8337125 0.9198731 0.8451829
##  [87,] 0.9518246 0.7385601 0.6612413
##  [88,] 0.6837196 0.6874561 0.9488820
##  [89,] 0.8859057 0.8477996 0.8469885
##  [90,] 0.8064469 0.7631978 0.7294724
##  [91,] 0.8636397 0.7588248 0.9440948
##  [92,] 0.7584048 0.9282835 0.8097212
##  [93,] 0.7110372 0.7906458 0.8434307
##  [94,] 0.9002299 0.7410747 0.7611426
##  [95,] 0.8517509 0.6404455 0.9389108
##  [96,] 0.7860544 0.9351229 0.8022860
##  [97,] 0.8682501 0.9604134 0.7963535
##  [98,] 0.9849308 0.8333570 0.9554125
##  [99,] 0.8938526 0.8672668 0.7852627
## [100,] 0.8033884 0.9453083 0.6952520
## [101,] 0.7823232 0.7251030 0.8142126
## [102,] 0.7512358 0.7108372 0.6082782
## [103,] 0.7880248 0.6154941 0.8013826
## [104,] 0.9598600 0.8925280 0.8574756
## [105,] 0.7436270 0.6994452 0.7769000
## [106,] 0.7566762 0.7568435 0.8334549
## [107,] 0.7912168 0.7157332 0.8832976
## [108,] 0.7540188 0.8492076 0.8446648
## [109,] 0.6314484 0.6782009 1.0396854
## [110,] 0.8368428 0.6800811 0.6623681
## [111,] 0.6792797 0.5865231 0.7977306
## [112,] 1.0313878 0.7484774 0.8678607
## [113,] 0.6862718 0.7849531 0.8634983
## [114,] 0.7335097 0.9176092 0.7953030
## [115,] 0.7958216 0.9635967 0.9057479
## [116,] 0.7404365 0.7566130 0.8489196
## [117,] 0.8734373 0.7554768 0.8131769
## [118,] 0.8933662 0.8235449 0.8430377
## [119,] 0.7697426 0.8934901 0.8433675
## [120,] 0.8874030 0.9308464 0.9729955
## [121,] 0.8655127 0.9082542 1.0415154
## [122,] 0.7470482 0.8015193 0.8649585
## [123,] 0.8596334 0.8415138 0.7239044
## [124,] 0.9086091 0.8835418 0.9695207
## [125,] 0.8847035 0.9236670 0.8787092
## [126,] 0.7201719 1.0378497 0.9688002
## [127,] 0.8049451 0.7584163 0.8069910
## [128,] 0.5803860 0.6824140 0.7585723
## [129,] 0.7528318 0.8979011 0.6868366
## [130,] 0.6864790 0.7602513 0.8453316
## [131,] 0.8179228 0.7493563 0.8823567
## [132,] 0.8819974 0.9903836 0.8295964
## [133,] 0.6315039 0.7505507 0.7576940
## [134,] 0.6718779 0.8753171 0.7644146
## [135,] 0.8808891 0.7953904 0.8221834
## [136,] 0.8104341 0.8110177 0.7179703
## [137,] 0.8147495 0.7974044 0.7622053
## [138,] 0.8971763 0.8836751 0.8412568
## [139,] 0.8517834 0.7818030 0.8846486
## [140,] 0.7636416 0.7729838 1.0339997
## [141,] 0.7016324 0.9244791 1.2528010
## [142,] 0.9655671 0.5725694 0.8266769
## [143,] 0.8553789 0.9265065 0.6107138
## [144,] 0.6945475 0.8055856 0.7041288
## [145,] 0.7731716 1.0742352 0.7622082
## [146,] 0.7633901 0.8270377 0.8076315
## [147,] 0.9236970 0.7496053 0.7208571
## [148,] 0.6007325 0.8297656 0.8307234
## [149,] 1.0199270 0.8126159 0.8875121
## [150,] 0.8549540 1.2803941 0.8523628
## [151,] 0.8551489 0.8771999 0.8294628
## [152,] 0.8574408 0.7053228 0.8360154
## [153,] 0.7942899 0.6843195 0.7423653
## [154,] 0.8009784 0.8114498 0.8638721
## [155,] 0.8902956 0.8297875 0.8344218
## [156,] 0.7431038 0.7244519 0.9461308
## [157,] 1.1485410 0.7062678 0.8344599
## [158,] 0.6363608 0.7813431 1.0024233
## [159,] 0.7631195 0.6879946 0.4816514
## [160,] 0.7141213 0.9075922 0.8586660
## [161,] 0.8747267 0.7650895 0.8546448
## [162,] 0.7458446 0.8544544 0.7770772
## [163,] 0.7969730 0.7977339 0.7791769
## [164,] 0.7966409 0.7226589 0.9933000
## [165,] 0.9188880 0.7695170 0.8309881
## [166,] 0.7373928 0.9980898 0.9580059
## [167,] 0.8618304 0.9534782 0.8609207
## [168,] 0.7038953 0.9067521 0.7564862
## [169,] 0.8538212 0.7882300 0.7683036
## [170,] 0.8495318 0.7169714 0.7572177
## [171,] 0.8937174 0.7551414 0.9746664
## [172,] 0.7735238 0.5757030 0.7638530
## [173,] 0.9146619 0.8915108 0.8577325
## [174,] 0.8067556 0.7156179 0.9170923
## [175,] 0.8183063 0.6217837 0.9347989
## [176,] 0.6715915 0.8803750 0.9626074
## [177,] 0.8271101 0.6820869 0.8393179
## [178,] 0.8518380 0.8930969 0.7620529
## [179,] 0.8911813 0.8547567 0.8482138
## [180,] 0.8273747 0.9136308 0.8987597
## 
## , , deviance
## 
##            [,1]      [,2]      [,3]
##   [1,] 91.73114  88.66707  86.54942
##   [2,] 88.43115  88.26986  88.32786
##   [3,] 85.48137  89.86272  85.65846
##   [4,] 88.23839  92.41228  85.55646
##   [5,] 87.84946  88.83102  92.00785
##   [6,] 86.58617  88.97836  97.59899
##   [7,] 87.05458  90.62844  89.96452
##   [8,] 85.13134  91.26292  90.50788
##   [9,] 88.31352  93.15728  89.95830
##  [10,] 88.06298  85.70138  87.13125
##  [11,] 90.40791  85.77566  97.22961
##  [12,] 85.50574  93.82391  85.70671
##  [13,] 86.89794  90.01149  87.77892
##  [14,] 86.33568  86.32414  88.65616
##  [15,] 87.20793  86.94511  85.43879
##  [16,] 85.55543  86.18056  88.22812
##  [17,] 88.08160  86.71819  87.54924
##  [18,] 86.16672  87.31073  86.16461
##  [19,] 90.39505  86.03891  88.21553
##  [20,] 85.74559  89.40507  88.16476
##  [21,] 89.63111  86.25155  88.08049
##  [22,] 86.77221  87.54316  87.71707
##  [23,] 87.87343  87.75675  85.31545
##  [24,] 85.73199  92.02727  88.17623
##  [25,] 89.73891  86.98733  92.65823
##  [26,] 90.16328  86.15865  88.02591
##  [27,] 85.84965  87.09224  88.36261
##  [28,] 90.71203  87.41549  86.79097
##  [29,] 91.00329  85.96989  88.84238
##  [30,] 90.72847  89.28486  91.36732
##  [31,] 87.35397  85.36060  88.01356
##  [32,] 87.03069  87.66844  87.77424
##  [33,] 85.73924  88.27731  94.25613
##  [34,] 86.04112  85.87219  93.79217
##  [35,] 88.82854  88.94419  86.25454
##  [36,] 87.08029  90.78968  89.97331
##  [37,] 86.91624  88.33052  86.24367
##  [38,] 89.86824  88.02676  88.13535
##  [39,] 91.90267  92.57171  85.98926
##  [40,] 87.75139  85.26336  91.11410
##  [41,] 86.44924  85.19154  87.00143
##  [42,] 89.78985  88.87906  85.04798
##  [43,] 85.98591  88.16479  87.51824
##  [44,] 87.05398  85.25950  91.24816
##  [45,] 85.95684  87.73616 100.77611
##  [46,] 88.39186  85.34398  87.52910
##  [47,] 89.12351  89.46102  91.68872
##  [48,] 92.00520  87.07347  90.49002
##  [49,] 90.12888  93.36142  88.48613
##  [50,] 90.13805  85.42657  88.10796
##  [51,] 86.16542  86.25650  87.53805
##  [52,] 90.92594 104.21260  87.61566
##  [53,] 89.63807  86.21206  91.32691
##  [54,] 92.82335  89.24828  91.06932
##  [55,] 86.05918  89.89865  87.51628
##  [56,] 88.09321 107.88571  90.60040
##  [57,] 86.57160  91.12190  86.19927
##  [58,] 88.71900  88.00239  85.80660
##  [59,] 91.35129  86.03516  89.17646
##  [60,] 89.64188  95.96879  86.79953
##  [61,] 86.98066  86.83214  88.12971
##  [62,] 85.36387  89.04117  87.32411
##  [63,] 87.43986  93.06404  86.81870
##  [64,] 86.20363  93.67566  94.84155
##  [65,] 87.83158  89.78488  86.21637
##  [66,] 89.84255  89.33897  92.09094
##  [67,] 88.64179  86.20954  87.03169
##  [68,] 91.94060  91.51964  88.79974
##  [69,] 85.71547  86.79050  88.10988
##  [70,] 90.49072  88.53102  88.50687
##  [71,] 89.09694  87.22919  88.78739
##  [72,] 91.26084  92.35758  88.17989
##  [73,] 90.61267  85.69713  87.44834
##  [74,] 91.55609  87.42004  87.15725
##  [75,] 87.01594  87.45140  90.62378
##  [76,] 91.88185  87.57528  89.66644
##  [77,] 86.90583  86.45294  91.31623
##  [78,] 86.74639  87.01485  96.39455
##  [79,] 87.98060  89.33410  85.61805
##  [80,] 88.77227  89.12404  87.42999
##  [81,] 86.58562  92.20365  87.24918
##  [82,] 85.79495  93.17410  87.07921
##  [83,] 88.38531  89.39519  93.65873
##  [84,] 88.38462  85.27111  91.45507
##  [85,] 90.08717  93.57837  90.17865
##  [86,] 86.04263  87.98389  85.56907
##  [87,] 93.59015  86.04894  89.73456
##  [88,] 88.82850  89.34630  88.42297
##  [89,] 87.57510  86.09130  87.93886
##  [90,] 85.80892  93.11558  88.09853
##  [91,] 86.36566  88.51168  88.59683
##  [92,] 88.04526  91.57637  87.07024
##  [93,] 89.95523  85.69022  89.71229
##  [94,] 89.13529  88.31176  86.19911
##  [95,] 86.24832  89.43710  88.36348
##  [96,] 87.75948  87.82307  85.90814
##  [97,] 87.71699  93.47750  85.12684
##  [98,] 91.27501  87.04830  90.71743
##  [99,] 86.99796  86.12015  85.58850
## [100,] 90.97489  88.40280  91.82650
## [101,] 86.18817  88.42043  85.30288
## [102,] 86.28155  90.75320  91.35573
## [103,] 87.57595  90.53430  85.18680
## [104,] 90.38772  87.98488  90.50859
## [105,] 86.70132  89.89966  90.29236
## [106,] 85.56028  85.57338  85.49657
## [107,] 86.06067  90.85109  92.57583
## [108,] 86.25347  92.04436  87.09156
## [109,] 92.80059  90.94808  97.66379
## [110,] 88.41552  98.09765  90.74580
## [111,] 89.21339  93.87844  89.17696
## [112,] 92.99430  95.84228  86.47170
## [113,] 89.58544  88.65679  86.08321
## [114,] 86.95682  92.31637  87.63483
## [115,] 85.39301  88.60068  86.65954
## [116,] 88.48595  87.68364  85.37231
## [117,] 90.05660  86.63464  86.97693
## [118,] 92.46732  85.48549  88.73600
## [119,] 90.47921  86.49245  85.45024
## [120,] 87.82171  87.58773  90.23022
## [121,] 86.24967  90.62112  93.12840
## [122,] 88.14706  86.50316  86.80085
## [123,] 85.55957  87.36833  87.21828
## [124,] 86.96774  88.07331  89.22123
## [125,] 86.62936  88.05991  91.25727
## [126,] 90.45676  98.29488  90.56420
## [127,] 86.94444  85.93930  87.52480
## [128,] 99.85686  90.83271  86.46192
## [129,] 89.27660  89.48182  88.29567
## [130,] 89.55329  85.62126  86.52139
## [131,] 93.80843  85.74601  89.63779
## [132,] 86.29903  95.68181  85.38839
## [133,] 93.83872  86.21777  89.46850
## [134,] 88.70234  87.28042  87.38107
## [135,] 89.27523  85.32902  87.30264
## [136,] 87.75637  86.80254  86.81447
## [137,] 86.80930  86.52997  94.41183
## [138,] 86.46582  86.78386  92.52810
## [139,] 86.17816  85.99512  86.51432
## [140,] 88.39058  92.56725  95.56941
## [141,] 88.86944  87.89894 107.42043
## [142,] 90.38033  92.81182  91.78564
## [143,] 88.44949  90.52222  94.15985
## [144,] 89.04157  89.17908  90.72040
## [145,] 87.12390  94.90184  85.64809
## [146,] 89.47707  99.44411  85.47006
## [147,] 87.12052  85.86690  92.88981
## [148,] 91.27207  86.04726  86.97386
## [149,] 97.82378  88.92497  88.13863
## [150,] 85.87121 101.26140  91.57291
## [151,] 87.20475  89.03121  85.83623
## [152,] 92.68858  92.31728  86.18520
## [153,] 88.15877  92.66678  85.99968
## [154,] 85.19075  85.97089  90.98603
## [155,] 97.58018  86.12207  89.65285
## [156,] 91.31075  91.65820  88.94190
## [157,] 99.68826  87.15624  88.29104
## [158,] 89.99650  86.44844  91.97033
## [159,] 86.21684  94.67858 101.35948
## [160,] 91.54391  93.96030  86.38971
## [161,] 87.24327  87.70952  87.54021
## [162,] 89.20521  87.01352  87.21023
## [163,] 87.89320  85.13011  86.06273
## [164,] 85.12781  88.62652  91.67358
## [165,] 90.56729  85.85304  87.87380
## [166,] 92.71537  91.51868  89.05648
## [167,] 89.19862  89.43472  86.37248
## [168,] 87.08223  88.05115  85.78193
## [169,] 88.27796  89.63670  89.42352
## [170,] 85.31995  90.13009  85.85609
## [171,] 88.41035  88.36274  89.34392
## [172,] 85.65525  92.71792  86.12944
## [173,] 89.04791  89.26557  86.84356
## [174,] 86.70571  86.59360  89.23896
## [175,] 87.35925  95.62269  88.24383
## [176,] 90.12453  86.19366  89.70877
## [177,] 92.03746  87.94953  85.62428
## [178,] 88.70306  88.09152  92.41228
## [179,] 86.50712  87.74104  87.67509
## [180,] 85.49710  86.86526  89.38643
## 
## , , sigma
## 
##            [,1]     [,2]     [,3]
##   [1,] 31.40342 17.67779 22.01812
##   [2,] 21.75882 19.09513 20.52944
##   [3,] 17.61999 27.11500 18.71251
##   [4,] 20.53088 28.32297 18.09524
##   [5,] 14.53007 22.12060 33.58943
##   [6,] 14.58644 25.81372 39.58616
##   [7,] 14.32274 16.87873 22.95093
##   [8,] 16.37610 31.95779 28.17715
##   [9,] 21.09589 36.74350 15.45936
##  [10,] 20.33371 19.88565 22.54748
##  [11,] 23.95385 14.94646 30.04366
##  [12,] 18.71523 35.16496 17.04870
##  [13,] 23.35473 23.85610 19.31369
##  [14,] 17.79461 19.08523 19.33966
##  [15,] 19.53797 17.37269 17.03290
##  [16,] 15.48051 13.61800 22.53135
##  [17,] 18.00440 17.11432 20.67414
##  [18,] 17.11565 13.71536 13.99016
##  [19,] 22.43044 19.43852 19.35798
##  [20,] 19.63250 23.95769 15.93915
##  [21,] 17.75630 19.99357 18.20063
##  [22,] 21.39309 16.34955 21.70166
##  [23,] 18.36632 18.34883 16.32093
##  [24,] 14.52871 33.12632 25.40826
##  [25,] 25.10404 20.60065 34.38788
##  [26,] 17.28969 16.17835 26.18561
##  [27,] 16.08000 22.11052 19.65843
##  [28,] 14.65501 19.46943 14.81668
##  [29,] 28.83510 20.12072 21.46893
##  [30,] 14.61212 25.97832 16.20678
##  [31,] 13.45956 17.89437 22.67705
##  [32,] 12.86561 20.43520 21.78780
##  [33,] 18.68343 21.76767 35.58056
##  [34,] 21.08184 15.30793 36.43813
##  [35,] 21.31950 19.79686 21.53720
##  [36,] 22.97741 20.40507 13.46283
##  [37,] 15.40603 18.00818 22.03636
##  [38,] 26.40159 14.00142 25.29591
##  [39,] 34.54578 21.11599 17.47902
##  [40,] 14.54280 15.35182 32.51185
##  [41,] 15.75365 16.61118 23.50425
##  [42,] 23.82132 28.05827 16.94468
##  [43,] 20.82128 26.04489 17.64154
##  [44,] 21.32209 16.20702 29.43150
##  [45,] 20.22757 24.03238 32.23325
##  [46,] 15.19857 16.55281 15.32801
##  [47,] 25.10399 24.31874 34.38191
##  [48,] 23.25104 22.30870 23.53060
##  [49,] 20.11330 19.58314 19.97021
##  [50,] 14.06590 19.04031 13.84255
##  [51,] 20.71722 21.35877 24.75439
##  [52,] 27.31848 33.82620 14.16949
##  [53,] 24.56569 19.43835 28.35537
##  [54,] 21.88736 19.84449 27.28451
##  [55,] 19.73262 24.64071 16.83097
##  [56,] 14.17796 29.25738 18.76215
##  [57,] 17.78836 28.48208 20.72029
##  [58,] 22.92418 21.16387 20.56186
##  [59,] 16.85980 20.62426 18.71235
##  [60,] 28.97403 37.14344 23.47921
##  [61,] 20.80889 17.80018 26.20393
##  [62,] 19.35547 14.85702 19.33582
##  [63,] 17.30639 31.26894 21.19222
##  [64,] 15.61433 35.93314 39.14141
##  [65,] 24.50123 13.78866 18.87923
##  [66,] 24.91052 25.78428 29.13925
##  [67,] 18.31905 19.93513 21.27587
##  [68,] 28.07124 21.56041 25.99436
##  [69,] 18.38371 21.14566 12.54681
##  [70,] 29.47269 15.03291 16.52713
##  [71,] 23.17097 22.78377 15.23018
##  [72,] 21.17674 23.47365 24.58217
##  [73,] 30.96434 17.85456 18.61762
##  [74,] 30.69395 14.97607 23.70676
##  [75,] 19.40562 14.21296 24.23689
##  [76,] 30.08242 22.54865 23.22058
##  [77,] 17.36796 21.65748 23.40607
##  [78,] 21.23230 22.38560 17.35993
##  [79,] 20.22068 19.88375 18.89193
##  [80,] 18.18262 22.77229 24.14433
##  [81,] 22.35432 17.32676 22.47126
##  [82,] 16.16648 31.05079 19.44120
##  [83,] 14.22525 17.67183 22.16007
##  [84,] 23.54363 18.83229 17.21739
##  [85,] 21.84291 38.35100 18.61763
##  [86,] 20.93632 23.35442 17.23507
##  [87,] 35.93599 18.43644 15.89322
##  [88,] 25.24235 20.84405 20.11880
##  [89,] 24.04066 18.38974 25.26097
##  [90,] 18.78141 36.78145 20.90156
##  [91,] 20.14595 27.07004 23.83502
##  [92,] 22.33838 18.47837 22.63678
##  [93,] 27.17824 20.26728 30.03934
##  [94,] 26.74244 14.28773 18.56533
##  [95,] 17.01209 20.90097 22.25984
##  [96,] 23.28967 20.71941 20.10180
##  [97,] 23.61831 19.79219 16.91666
##  [98,] 15.96741 23.45457 21.31560
##  [99,] 20.01357 20.77127 19.77708
## [100,] 10.82729 21.80572 30.27757
## [101,] 13.93524 26.06034 15.28776
## [102,] 21.13476 23.40646 19.24852
## [103,] 24.05025 24.05985 16.89810
## [104,] 28.64152 21.93943 28.49342
## [105,] 21.09705 14.22176 30.12116
## [106,] 17.60849 17.03647 18.74999
## [107,] 21.40649 31.67961 12.73788
## [108,] 15.85990 27.70510 23.95694
## [109,] 15.85737 19.94474 45.25747
## [110,] 21.81607 29.04956 28.78241
## [111,] 23.76650 34.64739 25.17741
## [112,] 32.09347 42.97483 21.65372
## [113,] 17.59316 11.86413 16.31516
## [114,] 16.60777 24.23474 18.24881
## [115,] 16.96567 21.68337 19.66806
## [116,] 26.60430 16.87301 17.72413
## [117,] 30.30825 15.68391 15.12601
## [118,] 30.59921 18.95834 27.55539
## [119,] 31.70659 16.10652 19.15839
## [120,] 20.77166 17.19710 23.49012
## [121,] 18.52525 31.12419 24.44738
## [122,] 18.31685 16.49132 22.34281
## [123,] 16.19537 19.37854 17.20094
## [124,] 21.07314 25.58894 21.94135
## [125,] 16.42115 21.67318 28.89222
## [126,] 19.31775 18.66932 28.16588
## [127,] 22.19544 20.01857 25.20335
## [128,] 33.97187 30.68179 21.25020
## [129,] 26.46437 22.06446 18.62347
## [130,] 27.32737 18.71715 17.17629
## [131,] 24.80699 17.14511 21.34883
## [132,] 19.84744 41.84645 18.89497
## [133,] 30.44743 18.36248 28.56417
## [134,] 16.59739 15.66071 20.14737
## [135,] 22.19665 18.35592 13.73345
## [136,] 25.87200 22.76986 19.75138
## [137,] 15.68274 13.49640 36.40873
## [138,] 16.61155 16.18207 12.02194
## [139,] 14.46250 14.19244 15.44074
## [140,] 23.66387 36.19313 36.24522
## [141,] 23.38609 22.96184 74.45794
## [142,] 18.32846 28.39865 25.89855
## [143,] 26.06636 27.21397 25.60159
## [144,] 26.39332 29.02605 27.57018
## [145,] 18.90835 31.69720 16.18142
## [146,] 28.16194 54.65984 19.05626
## [147,] 18.03638 19.21948 33.48721
## [148,] 21.38741 19.54890 19.70439
## [149,] 22.59586 13.07513 17.54600
## [150,] 16.00493 41.94862 33.96574
## [151,] 20.83066 27.92750 18.34595
## [152,] 26.95858 34.94204 19.50871
## [153,] 23.51754 14.22489 19.04113
## [154,] 17.77903 14.27831 31.04471
## [155,] 49.04241 13.89488 12.63766
## [156,] 25.86925 33.48350 15.82019
## [157,] 46.91390 18.52355 25.72610
## [158,] 21.75299 17.95268 30.80710
## [159,] 17.12268 26.84093 53.96907
## [160,] 33.28483 34.65310 21.81963
## [161,] 19.77106 18.41882 24.36952
## [162,] 24.70382 20.69214 19.59956
## [163,] 25.08904 16.13991 20.21102
## [164,] 17.01295 22.47985 18.80660
## [165,] 29.73016 20.16057 18.41957
## [166,] 29.94038 28.52486 24.00137
## [167,] 28.42523 25.53411 14.57966
## [168,] 20.23848 21.73121 18.96130
## [169,] 26.72292 29.95519 29.21137
## [170,] 17.83421 22.28625 18.21580
## [171,] 22.08712 13.05444 24.00475
## [172,] 18.67684 29.45597 18.33059
## [173,] 26.35083 28.26519 23.02173
## [174,] 15.05308 18.28676 27.20557
## [175,] 22.24834 36.83134 20.16437
## [176,] 21.78659 18.23323 22.91549
## [177,] 21.66039 19.22068 20.02589
## [178,] 26.47870 14.59055 26.80363
## [179,] 16.80848 16.29393 13.01442
## [180,] 19.59538 17.44792 28.42493
## 
## 
## $sims.list
## $sims.list$beta0
##              [,1]
##   [1,]  53.783100
##   [2,]  53.398183
##   [3,]  54.920755
##   [4,]  48.602041
##   [5,]  74.537425
##   [6,]  51.480917
##   [7,]  49.426795
##   [8,]  63.550623
##   [9,]  70.502571
##  [10,]  51.073669
##  [11,]  84.306789
##  [12,]  54.961921
##  [13,]  37.310017
##  [14,]  63.076736
##  [15,]  25.811038
##  [16,]  68.966096
##  [17,]  31.177036
##  [18,]  35.668708
##  [19,]  42.584473
##  [20,]   7.954745
##  [21,]  50.208453
##  [22,]  47.978001
##  [23,] 106.560979
##  [24,]  60.487025
##  [25,]  52.629022
##  [26,]  56.220240
##  [27,]  44.691626
##  [28,]  64.862716
##  [29,]  60.030967
##  [30,]  61.918710
##  [31,]  47.124722
##  [32,]  36.862189
##  [33,]  33.383757
##  [34,]  63.484465
##  [35,]  49.160658
##  [36,]  56.154847
##  [37,]  76.664852
##  [38,]  66.239870
##  [39,]  64.733265
##  [40,]  66.276861
##  [41,]  65.970413
##  [42,]  41.637487
##  [43,]  48.283516
##  [44,]  61.277797
##  [45,]  38.905803
##  [46,]  62.936723
##  [47,]  32.071513
##  [48,]  51.077878
##  [49,]  39.567621
##  [50,]  23.680939
##  [51,]  81.442910
##  [52,]  45.068476
##  [53,]  39.621762
##  [54,]  61.431812
##  [55,]  31.015310
##  [56,]  72.143280
##  [57,]  56.214983
##  [58,]  55.734701
##  [59,]  43.441937
##  [60,]  44.229310
##  [61,]  54.213786
##  [62,]  31.059103
##  [63,]  51.181048
##  [64,]  52.294494
##  [65,]  49.611746
##  [66,]  64.700192
##  [67,]  51.559865
##  [68,]  58.904485
##  [69,]  46.060903
##  [70,]  42.976482
##  [71,]  51.795512
##  [72,]  57.790703
##  [73,]  33.899096
##  [74,]  71.341329
##  [75,]  71.697193
##  [76,]   6.941834
##  [77,]  31.494965
##  [78,]  49.015397
##  [79,]  37.891654
##  [80,]  55.299451
##  [81,]  31.064086
##  [82,]  54.419302
##  [83,]  63.279479
##  [84,]  49.219733
##  [85,]  34.767624
##  [86,]  38.153403
##  [87,]  47.105650
##  [88,]  49.651807
##  [89,]  66.249598
##  [90,]  61.031031
##  [91,]  34.128237
##  [92,]  56.184540
##  [93,]  49.812402
##  [94,]  39.744835
##  [95,]  32.159274
##  [96,]  71.295974
##  [97,]  23.714444
##  [98,]  58.014020
##  [99,]  40.171245
## [100,]  41.116014
## [101,]  72.301969
## [102,]  38.854098
## [103,]  65.223510
## [104,]  27.927736
## [105,]  58.017283
## [106,]  43.340808
## [107,]  49.830390
## [108,]  59.353366
## [109,]  47.024985
## [110,]  55.650386
## [111,]  78.774134
## [112,]  38.948839
## [113,]  51.730161
## [114,]  43.859450
## [115,]  47.959299
## [116,]  60.574429
## [117,]  35.798178
## [118,]  57.702631
## [119,]  68.866203
## [120,]  53.173346
## [121,]  36.421710
## [122,]  47.109182
## [123,]  34.095266
## [124,]  45.657384
## [125,]  42.797593
## [126,]  34.117512
## [127,]  28.980493
## [128,]  61.461914
## [129,]  18.590485
## [130,]  53.792120
## [131,]  70.840486
## [132,]  63.924939
## [133,]  29.898680
## [134,]  38.094867
## [135,]  51.089733
## [136,]  56.956127
## [137,]  46.826450
## [138,]  56.868464
## [139,]  66.103466
## [140,]  39.876039
## [141,]  33.608694
## [142,]  66.644792
## [143,]  63.735108
## [144,]  24.747651
## [145,]  26.702933
## [146,]  24.885209
## [147,]  46.220369
## [148,]  66.778210
## [149,]  48.336625
## [150,]  46.808307
## [151,]  44.397618
## [152,]  88.630172
## [153,]  40.526237
## [154,]  37.253496
## [155,]  36.268143
## [156,]  21.553850
## [157,]  61.580185
## [158,]  46.931399
## [159,]  69.006659
## [160,]  29.500311
## [161,]  24.787593
## [162,]  25.596118
## [163,]  62.503533
## [164,]  50.425361
## [165,]  25.044943
## [166,]  59.593435
## [167,]  83.153895
## [168,]  23.001847
## [169,]  69.526981
## [170,]  46.162919
## [171,]  52.106384
## [172,]  70.763561
## [173,]  47.289521
## [174,]  53.247826
## [175,]  29.096009
## [176,]  54.358364
## [177,]  58.316375
## [178,]  84.310600
## [179,]  62.388917
## [180,]  50.006277
## [181,]  45.034084
## [182,]  49.355557
## [183,]  80.822424
## [184,]  52.848702
## [185,]  54.259261
## [186,]  57.996799
## [187,]  26.927484
## [188,]  63.309220
## [189,]  42.208419
## [190,]  58.900653
## [191,]  63.626396
## [192,]  56.353106
## [193,]  52.763529
## [194,]  92.836417
## [195,]  42.333542
## [196,]  42.793378
## [197,]  68.820837
## [198,]  28.790751
## [199,]  49.503649
## [200,]  42.197142
## [201,]  54.811651
## [202,]  58.699333
## [203,]  57.232413
## [204,]  73.045054
## [205,]  50.040130
## [206,]  41.667279
## [207,]  62.306298
## [208,]  13.871121
## [209,]  62.829038
## [210,]  56.857666
## [211,]  44.512353
## [212,]  46.466306
## [213,]  26.589120
## [214,]  55.977809
## [215,]  76.795791
## [216,]   5.876293
## [217,]  64.794712
## [218,]  43.923797
## [219,]  77.439065
## [220,]  60.105232
## [221,]  42.751056
## [222,]  59.640160
## [223,]  14.576776
## [224,]  30.571542
## [225,]  42.660351
## [226,]  40.499200
## [227,]  55.175718
## [228,]  37.805535
## [229,]  70.067218
## [230,]  58.348616
## [231,]  55.062628
## [232,]  -9.112670
## [233,]  37.589242
## [234,]  42.613532
## [235,]  39.695211
## [236,]  59.177849
## [237,]  47.699350
## [238,]  52.716665
## [239,]  66.784148
## [240,]  34.621271
## [241,]  49.727098
## [242,]  64.198380
## [243,]  10.355595
## [244,]  50.368167
## [245,]  63.010203
## [246,]  49.532029
## [247,]  44.597181
## [248,]  37.777792
## [249,]  41.621181
## [250,]  73.146469
## [251,]  51.067868
## [252,]  56.740769
## [253,]  51.402927
## [254,]  36.197756
## [255,]  54.601177
## [256,]  52.483056
## [257,]  53.469993
## [258,]  63.533069
## [259,]  81.752722
## [260,]  30.813939
## [261,]  51.617243
## [262,]  84.734229
## [263,]  39.495963
## [264,]  42.827956
## [265,]  63.100373
## [266,]  72.775004
## [267,]  52.593145
## [268,]  81.245982
## [269,]  36.737128
## [270,]  50.777730
## [271,]  57.827536
## [272,]  21.482123
## [273,]  29.110929
## [274,]  40.275760
## [275,]  45.228148
## [276,]  54.167018
## [277,]  84.187914
## [278,]  42.221774
## [279,]  57.666178
## [280,]  57.708141
## [281,]  52.205238
## [282,]  55.847668
## [283,]  37.848025
## [284,]  40.533176
## [285,]  46.242812
## [286,]  43.568805
## [287,]  42.794529
## [288,]  28.673661
## [289,]  48.529274
## [290,]  57.508825
## [291,]  56.362910
## [292,]  43.523050
## [293,]  -4.204677
## [294,]  43.102176
## [295,]  48.372212
## [296,]  47.811998
## [297,]  67.244874
## [298,]  54.193862
## [299,]  54.227605
## [300,]  61.437841
## [301,]  69.892947
## [302,]  51.786565
## [303,]  52.786661
## [304,]  38.656881
## [305,]  30.735352
## [306,]  52.653377
## [307,]  50.469750
## [308,]  64.445654
## [309,]  77.903091
## [310,]  64.080098
## [311,]  68.096389
## [312,]  70.424751
## [313,]  54.087372
## [314,]  63.161832
## [315,]  65.258798
## [316,]  64.670460
## [317,]  24.081415
## [318,]  61.100467
## [319,]  57.696585
## [320,]  38.029438
## [321,]  60.371887
## [322,]  37.584150
## [323,]  25.013655
## [324,]  51.726770
## [325,]  53.001436
## [326,]  52.534614
## [327,]  57.945193
## [328,]  42.755183
## [329,]  31.633774
## [330,]  47.199887
## [331,]  51.490387
## [332,]  71.349315
## [333,]  36.180016
## [334,]  52.305811
## [335,]  68.379685
## [336,]  39.158655
## [337,]  28.585495
## [338,]  51.551787
## [339,]  45.574896
## [340,]  48.669529
## [341,]  49.884001
## [342,]  53.544604
## [343,]  70.804111
## [344,]  51.069419
## [345,]  74.313515
## [346,]  51.977499
## [347,]  44.139726
## [348,]  28.116240
## [349,]  78.072732
## [350,]  38.624571
## [351,]  45.014221
## [352,]  47.586837
## [353,]  55.539441
## [354,]  41.204917
## [355,]  52.782817
## [356,]  67.763813
## [357,]  32.281073
## [358,]  48.988146
## [359,]  56.321974
## [360,]  69.650259
## [361,]  61.627934
## [362,]  34.495001
## [363,]  31.723266
## [364,]  24.541615
## [365,]  61.265950
## [366,]  52.769763
## [367,]  51.352237
## [368,]  57.700982
## [369,]  57.639619
## [370,]  54.846404
## [371,]  76.046784
## [372,]  69.391942
## [373,]  88.208864
## [374,]  53.353157
## [375,]  45.577419
## [376,]  61.288267
## [377,]  75.948767
## [378,]  41.250914
## [379,]  64.602921
## [380,]  22.300120
## [381,]  69.892306
## [382,]  36.859289
## [383,]  32.393714
## [384,]  43.750195
## [385,]  57.205495
## [386,]  44.203212
## [387,]  45.715384
## [388,]  57.479321
## [389,]  63.462691
## [390,]  42.018656
## [391,]  47.682144
## [392,]  31.597341
## [393,]  46.043080
## [394,]  51.363907
## [395,]  29.368006
## [396,]  54.835237
## [397,]  43.366037
## [398,]  85.758120
## [399,] 102.030662
## [400,]  30.809066
## [401,]  87.711368
## [402,]  57.189944
## [403,]  37.921343
## [404,]  76.620672
## [405,]  53.818464
## [406,]  43.315465
## [407,]  49.988424
## [408,]  55.767796
## [409,]  62.720297
## [410,]  49.473090
## [411,]  54.227303
## [412,]  63.201328
## [413,]  69.694716
## [414,]  65.409995
## [415,]  50.608020
## [416,]  58.095512
## [417,]  63.794648
## [418,]  64.212696
## [419,]  47.672272
## [420,]  56.274463
## [421,]  46.104079
## [422,]  67.676767
## [423,]  59.193154
## [424,]  38.037664
## [425,]  89.513615
## [426,]  33.685409
## [427,]  59.855986
## [428,]  71.052432
## [429,]  20.836191
## [430,]  41.179035
## [431,]  35.957849
## [432,]  30.225744
## [433,]  49.758138
## [434,]  53.967293
## [435,]  82.222420
## [436,]  45.469646
## [437,]  60.605840
## [438,]  30.191120
## [439,]  58.010478
## [440,]  48.256269
## [441,]  38.015453
## [442,]  51.725009
## [443,]  76.935575
## [444,]  50.261000
## [445,]  52.983645
## [446,]  48.040361
## [447,]  72.463726
## [448,]  42.890908
## [449,]  49.960755
## [450,]  81.864089
## [451,]  66.148787
## [452,]  29.059276
## [453,]  47.052969
## [454,]  39.665905
## [455,]  72.621667
## [456,]  57.146328
## [457,]  45.129757
## [458,]  55.782336
## [459,]  32.331699
## [460,]  71.567554
## [461,]  48.269839
## [462,]  35.456965
## [463,]  59.940422
## [464,]  39.152711
## [465,]  39.829547
## [466,]  60.802046
## [467,]  67.777818
## [468,]  51.403008
## [469,]  36.225943
## [470,]  69.308168
## [471,]  63.118460
## [472,]  55.225157
## [473,]  73.084281
## [474,]  39.242239
## [475,]  48.251152
## [476,]  56.368995
## [477,]  70.823344
## [478,]  60.238832
## [479,]  54.246364
## [480,]  47.704827
## [481,]  51.605618
## [482,]  40.406468
## [483,]  55.041760
## [484,]  48.958824
## [485,]  33.341012
## [486,]  68.817430
## [487,]  42.578927
## [488,]  51.914541
## [489,]  61.625053
## [490,]  62.911694
## [491,]  56.252778
## [492,]  49.819490
## [493,]  43.405348
## [494,]  37.377664
## [495,]  40.129279
## [496,]  58.918461
## [497,]  35.216849
## [498,]  62.759664
## [499,]  61.167223
## [500,]  -1.984845
## [501,]  22.965208
## [502,]  45.192071
## [503,]  44.552493
## [504,]  77.098069
## [505,]  51.942105
## [506,]  55.401832
## [507,]  58.311070
## [508,]  47.048343
## [509,]  50.076044
## [510,]  36.265758
## [511,]  82.411780
## [512,]  43.219627
## [513,]  33.452785
## [514,]  74.342823
## [515,]  39.597310
## [516,]  72.533234
## [517,]  51.951651
## [518,]  41.869762
## [519,]  46.193554
## [520,]  39.880666
## [521,]  24.173426
## [522,]  53.544467
## [523,]  80.479087
## [524,]  28.986063
## [525,]  57.549357
## [526,]  65.879918
## [527,]  50.470747
## [528,]  49.392783
## [529,]  56.690764
## [530,]  29.390896
## [531,]  36.695335
## [532,]  55.200253
## [533,]  66.503854
## [534,]  44.334054
## [535,]  28.567222
## [536,]  37.135538
## [537,]  36.181073
## [538,]  70.375744
## [539,]  64.997723
## [540,]  64.660631
## 
## $sims.list$beta1
##             [,1]
##   [1,] 0.7867868
##   [2,] 0.7906458
##   [3,] 0.8710892
##   [4,] 0.8639909
##   [5,] 0.7016324
##   [6,] 0.8553789
##   [7,] 0.7920673
##   [8,] 0.7894394
##   [9,] 0.6737023
##  [10,] 0.8161566
##  [11,] 0.5865231
##  [12,] 0.7117359
##  [13,] 0.9729955
##  [14,] 0.7062678
##  [15,] 0.9695207
##  [16,] 0.7239044
##  [17,] 0.9188880
##  [18,] 0.8938526
##  [19,] 0.8394418
##  [20,] 1.2528010
##  [21,] 0.8855114
##  [22,] 0.8433675
##  [23,] 0.4816514
##  [24,] 0.7941651
##  [25,] 0.8518380
##  [26,] 0.8415138
##  [27,] 0.8637157
##  [28,] 0.7884804
##  [29,] 0.7404365
##  [30,] 0.7223206
##  [31,] 0.7700567
##  [32,] 0.9024481
##  [33,] 0.8925280
##  [34,] 0.6880196
##  [35,] 0.8446648
##  [36,] 0.7818030
##  [37,] 0.6792797
##  [38,] 0.6945475
##  [39,] 0.7412649
##  [40,] 0.7156179
##  [41,] 0.7943497
##  [42,] 0.8596507
##  [43,] 0.8297875
##  [44,] 0.7055726
##  [45,] 0.8593682
##  [46,] 0.6994452
##  [47,] 0.8937174
##  [48,] 0.7631978
##  [49,] 0.9605434
##  [50,] 0.9282835
##  [51,] 0.6782009
##  [52,] 0.8538212
##  [53,] 0.8987597
##  [54,] 0.6862718
##  [55,] 0.9063204
##  [56,] 0.6644241
##  [57,] 0.8110177
##  [58,] 0.7974044
##  [59,] 0.8835418
##  [60,] 0.6800811
##  [61,] 0.7892438
##  [62,] 0.9032286
##  [63,] 0.8033884
##  [64,] 0.8409467
##  [65,] 0.7650895
##  [66,] 0.6107138
##  [67,] 0.8141126
##  [68,] 0.8068047
##  [69,] 0.8279128
##  [70,] 0.7622053
##  [71,] 0.8173497
##  [72,] 0.8415476
##  [73,] 0.8894407
##  [74,] 0.6454402
##  [75,] 0.6718779
##  [76,] 1.0378497
##  [77,] 0.9424207
##  [78,] 0.8480022
##  [79,] 0.9456800
##  [80,] 0.8247159
##  [81,] 0.9236670
##  [82,] 0.8270377
##  [83,] 0.8574408
##  [84,] 0.8874030
##  [85,] 0.8574756
##  [86,] 0.9082542
##  [87,] 0.9162806
##  [88,] 0.8434461
##  [89,] 0.7261293
##  [90,] 0.8042923
##  [91,] 0.8832976
##  [92,] 0.7505507
##  [93,] 0.8008490
##  [94,] 0.8309881
##  [95,] 0.8620050
##  [96,] 0.6538677
##  [97,] 0.9075922
##  [98,] 0.7335097
##  [99,] 0.9198731
## [100,] 0.8453316
## [101,] 0.6612413
## [102,] 0.9244791
## [103,] 0.7478843
## [104,] 0.9719925
## [105,] 0.7602513
## [106,] 0.8957837
## [107,] 0.8076315
## [108,] 0.7599571
## [109,] 0.8495318
## [110,] 0.7683036
## [111,] 0.7108372
## [112,] 0.9041672
## [113,] 0.8337125
## [114,] 0.8672668
## [115,] 0.8192477
## [116,] 0.8427485
## [117,] 0.9157247
## [118,] 0.7588248
## [119,] 0.8085085
## [120,] 0.9176092
## [121,] 0.8711635
## [122,] 0.8026133
## [123,] 1.0339997
## [124,] 0.9067521
## [125,] 0.9279144
## [126,] 0.9536996
## [127,] 1.0594378
## [128,] 0.7493563
## [129,] 1.0261519
## [130,] 0.8547567
## [131,] 0.6810219
## [132,] 0.7136931
## [133,] 0.9389108
## [134,] 0.8368428
## [135,] 0.8430377
## [136,] 0.7944993
## [137,] 0.8524982
## [138,] 0.7852627
## [139,] 0.6936035
## [140,] 0.8712727
## [141,] 0.9010878
## [142,] 0.7141213
## [143,] 0.7029908
## [144,] 0.8940281
## [145,] 1.0024233
## [146,] 1.0026027
## [147,] 0.7373928
## [148,] 0.8074589
## [149,] 0.8618304
## [150,] 0.8234777
## [151,] 0.8956735
## [152,] 0.6879946
## [153,] 0.8771999
## [154,] 0.9236970
## [155,] 0.9534782
## [156,] 0.9837981
## [157,] 0.7833810
## [158,] 0.8586660
## [159,] 0.7727662
## [160,] 1.0415154
## [161,] 0.8948391
## [162,] 1.0034529
## [163,] 0.7585723
## [164,] 0.8451829
## [165,] 0.9606195
## [166,] 0.7496053
## [167,] 0.6082782
## [168,] 0.9743904
## [169,] 0.6655723
## [170,] 0.8804017
## [171,] 0.8104341
## [172,] 0.7484295
## [173,] 0.8609207
## [174,] 0.8638721
## [175,] 0.9764035
## [176,] 0.8245694
## [177,] 0.8287518
## [178,] 0.6858344
## [179,] 0.7572177
## [180,] 0.8682501
## [181,] 0.8333570
## [182,] 0.7850143
## [183,] 0.6262290
## [184,] 0.7169714
## [185,] 0.7886511
## [186,] 0.8097212
## [187,] 0.9647804
## [188,] 0.7526185
## [189,] 0.9146619
## [190,] 0.7955924
## [191,] 0.7385601
## [192,] 0.7410747
## [193,] 0.7921979
## [194,] 0.6217837
## [195,] 0.8469885
## [196,] 0.8735258
## [197,] 0.7451504
## [198,] 0.9771099
## [199,] 0.8233075
## [200,] 0.8111593
## [201,] 0.8344218
## [202,] 0.8049451
## [203,] 0.7585591
## [204,] 0.6820869
## [205,] 0.8471324
## [206,] 0.8307234
## [207,] 0.6315039
## [208,] 1.0497247
## [209,] 0.8492076
## [210,] 0.6952520
## [211,] 0.8538853
## [212,] 0.8193823
## [213,] 0.9688002
## [214,] 0.7695170
## [215,] 0.7231161
## [216,] 1.0742352
## [217,] 0.7053228
## [218,] 0.8482138
## [219,] 0.6623681
## [220,] 0.7719972
## [221,] 0.8766048
## [222,] 0.6969202
## [223,] 1.1485410
## [224,] 0.9548068
## [225,] 0.9554125
## [226,] 0.9347989
## [227,] 0.8221834
## [228,] 0.8836751
## [229,] 0.6824140
## [230,] 0.8183063
## [231,] 0.8933662
## [232,] 1.2803941
## [233,] 0.9403084
## [234,] 0.8662116
## [235,] 0.9086091
## [236,] 0.7833133
## [237,] 0.8393179
## [238,] 0.7966409
## [239,] 0.6864790
## [240,] 0.9461308
## [241,] 0.8739930
## [242,] 0.7176755
## [243,] 0.9520094
## [244,] 0.8098688
## [245,] 0.7942899
## [246,] 0.8875121
## [247,] 0.8596334
## [248,] 0.8227388
## [249,] 0.7620529
## [250,] 0.6374051
## [251,] 0.7958216
## [252,] 0.7697426
## [253,] 0.8179257
## [254,] 0.8747267
## [255,] 0.7791210
## [256,] 0.7397170
## [257,] 0.8387624
## [258,] 0.7611426
## [259,] 0.6314484
## [260,] 0.9598600
## [261,] 0.7707987
## [262,] 0.6007325
## [263,] 0.8915108
## [264,] 0.8549540
## [265,] 0.7638530
## [266,] 0.7226589
## [267,] 0.7472324
## [268,] 0.6363608
## [269,] 0.8908851
## [270,] 0.8142126
## [271,] 0.8131769
## [272,] 0.9639273
## [273,] 0.9030987
## [274,] 0.9170923
## [275,] 0.9002299
## [276,] 0.7576940
## [277,] 0.6037457
## [278,] 1.0199270
## [279,] 0.7584163
## [280,] 0.8492953
## [281,] 0.8365043
## [282,] 0.7462443
## [283,] 0.9456600
## [284,] 0.8734373
## [285,] 0.8216587
## [286,] 0.8846486
## [287,] 0.8430324
## [288,] 0.9488820
## [289,] 0.7969730
## [290,] 0.7110372
## [291,] 0.8330766
## [292,] 0.8577325
## [293,] 0.9368876
## [294,] 0.8164203
## [295,] 0.8634983
## [296,] 0.8649585
## [297,] 0.7636416
## [298,] 0.8114498
## [299,] 0.8297656
## [300,] 0.7689773
## [301,] 0.7272371
## [302,] 0.8465822
## [303,] 0.8544544
## [304,] 0.8551489
## [305,] 0.8749440
## [306,] 0.7923947
## [307,] 0.8992043
## [308,] 0.7230728
## [309,] 0.6538775
## [310,] 0.7554768
## [311,] 0.7038953
## [312,] 0.6877704
## [313,] 0.8294628
## [314,] 0.7540188
## [315,] 0.7257849
## [316,] 0.6952547
## [317,] 0.9626074
## [318,] 0.8019685
## [319,] 0.7702616
## [320,] 0.8847035
## [321,] 0.7515603
## [322,] 0.9057479
## [323,] 0.9903836
## [324,] 0.7566130
## [325,] 0.8792831
## [326,] 0.7791769
## [327,] 0.7733722
## [328,] 0.8523628
## [329,] 0.9635967
## [330,] 0.8766174
## [331,] 0.8876719
## [332,] 0.7294724
## [333,] 0.8903250
## [334,] 0.7201719
## [335,] 0.7528318
## [336,] 0.8803750
## [337,] 1.0396854
## [338,] 0.8517509
## [339,] 0.9604134
## [340,] 0.8753171
## [341,] 0.7962436
## [342,] 0.7977339
## [343,] 0.7458446
## [344,] 0.8979011
## [345,] 0.6868366
## [346,] 0.8055856
## [347,] 0.6477956
## [348,] 1.0187215
## [349,] 0.6256267
## [350,] 0.8801425
## [351,] 0.8617023
## [352,] 0.8295964
## [353,] 0.8412568
## [354,] 0.8416363
## [355,] 0.7715600
## [356,] 0.7584048
## [357,] 0.9818275
## [358,] 0.8270485
## [359,] 0.8156141
## [360,] 0.7521348
## [361,] 0.6715915
## [362,] 0.9933000
## [363,] 0.9980898
## [364,] 0.9595702
## [365,] 0.7618791
## [366,] 0.8733316
## [367,] 0.7767664
## [368,] 0.7559920
## [369,] 0.7558955
## [370,] 0.7946871
## [371,] 0.6154941
## [372,] 0.6784146
## [373,] 0.5453554
## [374,] 0.8210406
## [375,] 0.8126159
## [376,] 0.7564862
## [377,] 0.7431038
## [378,] 0.8971763
## [379,] 0.7770772
## [380,] 0.9473485
## [381,] 0.7119740
## [382,] 0.9308464
## [383,] 0.9214477
## [384,] 0.8174615
## [385,] 0.5803860
## [386,] 0.8859057
## [387,] 0.9265065
## [388,] 0.7436270
## [389,] 0.8098931
## [390,] 0.8894537
## [391,] 0.7340722
## [392,] 0.9351229
## [393,] 0.8434307
## [394,] 0.8013826
## [395,] 0.9453083
## [396,] 0.8344599
## [397,] 0.8553236
## [398,] 0.5713864
## [399,] 0.7224086
## [400,] 0.9572765
## [401,] 0.5710945
## [402,] 0.7622082
## [403,] 0.8682276
## [404,] 0.7208571
## [405,] 0.7631195
## [406,] 0.8360154
## [407,] 0.8546448
## [408,] 0.7153978
## [409,] 0.7423653
## [410,] 0.7731716
## [411,] 0.8739521
## [412,] 0.7953030
## [413,] 0.7470482
## [414,] 0.8135716
## [415,] 0.8069910
## [416,] 0.7665937
## [417,] 0.7769000
## [418,] 0.6828594
## [419,] 0.8414235
## [420,] 0.7849531
## [421,] 0.8678607
## [422,] 0.7179703
## [423,] 0.7566762
## [424,] 0.9655671
## [425,] 0.5725694
## [426,] 0.9342741
## [427,] 0.7727919
## [428,] 0.8179228
## [429,] 0.9713151
## [430,] 0.8427878
## [431,] 1.0035372
## [432,] 0.8823567
## [433,] 0.8029169
## [434,] 0.7882300
## [435,] 0.5757030
## [436,] 0.8841107
## [437,] 0.7140287
## [438,] 0.8787092
## [439,] 0.7924515
## [440,] 0.8884560
## [441,] 0.8911813
## [442,] 0.8346252
## [443,] 0.6843195
## [444,] 0.7484774
## [445,] 0.8235449
## [446,] 0.8392946
## [447,] 0.6404455
## [448,] 0.8902956
## [449,] 0.8463835
## [450,] 0.6578347
## [451,] 0.7157332
## [452,] 0.9746664
## [453,] 0.8015193
## [454,] 0.8435618
## [455,] 0.6788405
## [456,] 0.8064469
## [457,] 0.8147495
## [458,] 0.8399689
## [459,] 0.9577749
## [460,] 0.6325701
## [461,] 0.8489196
## [462,] 0.8798110
## [463,] 0.7512358
## [464,] 0.8721601
## [465,] 0.8655127
## [466,] 0.7880248
## [467,] 0.7244519
## [468,] 0.9019203
## [469,] 0.9440948
## [470,] 0.6948973
## [471,] 0.7551414
## [472,] 0.7823232
## [473,] 0.6752700
## [474,] 0.8956744
## [475,] 0.8273747
## [476,] 0.7912168
## [477,] 0.6837196
## [478,] 0.6874561
## [479,] 0.7735238
## [480,] 0.8020558
## [481,] 0.8009784
## [482,] 0.8636397
## [483,] 0.8205488
## [484,] 0.8746240
## [485,] 0.9307244
## [486,] 0.7415219
## [487,] 0.8774089
## [488,] 0.8477996
## [489,] 0.7729838
## [490,] 0.7860544
## [491,] 0.7062968
## [492,] 0.7813431
## [493,] 0.7977306
## [494,] 0.7938204
## [495,] 0.9378605
## [496,] 0.7568435
## [497,] 0.9691626
## [498,] 0.7251030
## [499,] 0.7289641
## [500,] 1.0683755
## [501,] 1.0313878
## [502,] 0.8854314
## [503,] 0.8517834
## [504,] 0.7041288
## [505,] 0.7633901
## [506,] 0.8385181
## [507,] 0.8763643
## [508,] 0.8067556
## [509,] 0.8375680
## [510,] 0.8930969
## [511,] 0.6430943
## [512,] 0.9501017
## [513,] 0.8266769
## [514,] 0.6600617
## [515,] 0.8819974
## [516,] 0.6962828
## [517,] 0.7953904
## [518,] 0.8934901
## [519,] 0.8334549
## [520,] 0.9211362
## [521,] 0.9518246
## [522,] 0.8808891
## [523,] 0.6694939
## [524,] 1.0401220
## [525,] 0.7612722
## [526,] 0.7291473
## [527,] 0.7644146
## [528,] 0.8022860
## [529,] 0.7026028
## [530,] 0.9849308
## [531,] 0.9136308
## [532,] 0.7963535
## [533,] 0.8271101
## [534,] 0.8575479
## [535,] 0.9580059
## [536,] 0.8472379
## [537,] 0.8624241
## [538,] 0.6223053
## [539,] 0.6876485
## [540,] 0.7220641
## 
## $sims.list$deviance
##             [,1]
##   [1,]  85.42657
##   [2,]  85.69022
##   [3,]  89.63111
##   [4,]  86.79097
##   [5,]  88.86944
##   [6,]  88.44949
##   [7,]  87.08029
##   [8,]  87.51824
##   [9,]  88.08049
##  [10,]  85.26336
##  [11,]  93.87844
##  [12,]  89.78985
##  [13,]  90.23022
##  [14,]  87.15624
##  [15,]  89.22123
##  [16,]  87.21828
##  [17,]  90.56729
##  [18,]  86.99796
##  [19,]  87.45140
##  [20,] 107.42043
##  [21,]  89.28486
##  [22,]  85.45024
##  [23,] 101.35948
##  [24,]  87.22919
##  [25,]  88.70306
##  [26,]  87.36833
##  [27,]  85.87219
##  [28,]  87.98060
##  [29,]  88.48595
##  [30,]  87.75139
##  [31,]  88.48613
##  [32,]  86.71819
##  [33,]  87.98488
##  [34,]  89.33897
##  [35,]  87.09156
##  [36,]  85.99512
##  [37,]  89.21339
##  [38,]  89.04157
##  [39,]  87.01485
##  [40,]  86.59360
##  [41,]  89.12404
##  [42,]  86.03891
##  [43,]  86.12207
##  [44,]  90.13805
##  [45,]  87.52910
##  [46,]  89.89966
##  [47,]  88.41035
##  [48,]  93.11558
##  [49,]  89.96452
##  [50,]  91.57637
##  [51,]  90.94808
##  [52,]  88.27796
##  [53,]  89.38643
##  [54,]  89.58544
##  [55,]  88.23839
##  [56,]  88.43115
##  [57,]  86.80254
##  [58,]  86.52997
##  [59,]  88.07331
##  [60,]  98.09765
##  [61,]  86.24367
##  [62,]  90.71203
##  [63,]  90.97489
##  [64,]  86.05918
##  [65,]  87.70952
##  [66,]  94.15985
##  [67,]  86.18056
##  [68,]  88.10796
##  [69,]  86.03516
##  [70,]  94.41183
##  [71,]  85.36387
##  [72,]  88.33052
##  [73,]  87.66844
##  [74,]  91.55609
##  [75,]  88.70234
##  [76,]  98.29488
##  [77,]  88.50687
##  [78,]  86.89794
##  [79,]  88.66707
##  [80,]  87.05458
##  [81,]  88.05991
##  [82,]  99.44411
##  [83,]  92.68858
##  [84,]  87.82171
##  [85,]  90.50859
##  [86,]  90.62112
##  [87,]  89.24828
##  [88,]  85.98591
##  [89,]  93.57837
##  [90,]  87.54316
##  [91,]  92.57583
##  [92,]  86.21777
##  [93,]  85.61805
##  [94,]  87.87380
##  [95,]  90.01149
##  [96,]  89.09694
##  [97,]  93.96030
##  [98,]  86.95682
##  [99,]  87.98389
## [100,]  86.52139
## [101,]  89.73456
## [102,]  87.89894
## [103,]  89.64188
## [104,]  90.50788
## [105,]  85.62126
## [106,]  87.24918
## [107,]  85.47006
## [108,]  91.68872
## [109,]  85.31995
## [110,]  89.42352
## [111,]  90.75320
## [112,]  87.73616
## [113,]  86.04263
## [114,]  86.12015
## [115,]  85.70138
## [116,]  90.17865
## [117,]  88.17989
## [118,]  88.51168
## [119,]  94.25613
## [120,]  92.31637
## [121,]  88.53102
## [122,]  87.61566
## [123,]  95.56941
## [124,]  88.05115
## [125,]  88.64179
## [126,]  88.21553
## [127,]  95.96879
## [128,]  85.74601
## [129,]  91.31623
## [130,]  87.74104
## [131,]  92.00785
## [132,]  87.05398
## [133,]  88.36348
## [134,]  88.41552
## [135,]  88.73600
## [136,]  85.36060
## [137,]  88.02591
## [138,]  85.58850
## [139,]  93.15728
## [140,]  86.16672
## [141,]  87.41549
## [142,]  91.54391
## [143,]  87.51628
## [144,]  92.41228
## [145,]  91.97033
## [146,]  94.84155
## [147,]  92.71537
## [148,]  90.39505
## [149,]  89.19862
## [150,]  85.74559
## [151,]  86.98066
## [152,]  94.67858
## [153,]  89.03121
## [154,]  87.12052
## [155,]  89.43472
## [156,]  91.32691
## [157,]  86.77221
## [158,]  86.38971
## [159,]  89.73891
## [160,]  93.12840
## [161,]  93.06404
## [162,]  91.45507
## [163,]  86.46192
## [164,]  85.56907
## [165,]  89.84255
## [166,]  85.86690
## [167,]  91.35573
## [168,]  91.35129
## [169,]  88.71900
## [170,]  86.74639
## [171,]  87.75637
## [172,]  88.65616
## [173,]  86.37248
## [174,]  90.98603
## [175,]  89.46102
## [176,]  86.16542
## [177,]  91.26292
## [178,]  92.35758
## [179,]  85.85609
## [180,]  87.71699
## [181,]  87.04830
## [182,]  86.81870
## [183,]  90.92594
## [184,]  90.13009
## [185,]  86.79953
## [186,]  87.07024
## [187,]  88.94419
## [188,]  85.98926
## [189,]  89.04791
## [190,]  87.42999
## [191,]  86.04894
## [192,]  88.31176
## [193,]  86.16461
## [194,]  95.62269
## [195,]  87.93886
## [196,]  86.19927
## [197,]  88.97836
## [198,]  89.40507
## [199,]  85.27111
## [200,]  88.00239
## [201,]  89.65285
## [202,]  86.94444
## [203,]  86.54942
## [204,]  87.94953
## [205,]  85.65846
## [206,]  86.97386
## [207,]  93.83872
## [208,]  92.82335
## [209,]  92.04436
## [210,]  91.82650
## [211,]  86.25454
## [212,]  85.84965
## [213,]  90.56420
## [214,]  85.85304
## [215,]  91.88185
## [216,]  94.90184
## [217,]  92.31728
## [218,]  87.67509
## [219,]  90.74580
## [220,]  88.12971
## [221,]  86.58617
## [222,]  88.84238
## [223,]  99.68826
## [224,]  88.26986
## [225,]  90.71743
## [226,]  88.24383
## [227,]  87.30264
## [228,]  86.78386
## [229,]  90.83271
## [230,]  87.35925
## [231,]  92.46732
## [232,] 101.26140
## [233,]  90.49072
## [234,]  85.73924
## [235,]  86.96774
## [236,]  85.95684
## [237,]  85.62428
## [238,]  85.12781
## [239,]  89.55329
## [240,]  88.94190
## [241,]  87.57528
## [242,]  87.42004
## [243,]  97.59899
## [244,]  85.73199
## [245,]  88.15877
## [246,]  88.13863
## [247,]  85.55957
## [248,]  90.62844
## [249,]  92.41228
## [250,]  89.66644
## [251,]  85.39301
## [252,]  90.47921
## [253,]  85.04798
## [254,]  87.24327
## [255,]  85.77566
## [256,]  89.95830
## [257,]  86.21206
## [258,]  86.19911
## [259,]  92.80059
## [260,]  90.38772
## [261,]  87.09224
## [262,]  91.27207
## [263,]  89.26557
## [264,]  85.87121
## [265,]  86.12944
## [266,]  88.62652
## [267,]  87.75675
## [268,]  89.99650
## [269,]  87.03169
## [270,]  85.30288
## [271,]  86.97693
## [272,]  90.62378
## [273,]  89.63807
## [274,]  89.23896
## [275,]  89.13529
## [276,]  89.46850
## [277,]  93.82391
## [278,]  97.82378
## [279,]  85.93930
## [280,]  92.65823
## [281,]  85.96989
## [282,]  86.57160
## [283,]  88.32786
## [284,]  90.05660
## [285,]  87.35397
## [286,]  86.51432
## [287,]  86.15865
## [288,]  88.42297
## [289,]  87.89320
## [290,]  89.95523
## [291,]  86.94511
## [292,]  86.84356
## [293,] 107.88571
## [294,]  89.97331
## [295,]  86.08321
## [296,]  86.80085
## [297,]  88.39058
## [298,]  85.97089
## [299,]  86.04726
## [300,]  87.31073
## [301,]  88.16476
## [302,]  86.20954
## [303,]  87.01352
## [304,]  87.20475
## [305,]  93.79217
## [306,]  85.80660
## [307,]  89.33410
## [308,]  88.13535
## [309,]  91.73114
## [310,]  86.63464
## [311,]  87.08223
## [312,]  88.78739
## [313,]  85.83623
## [314,]  86.25347
## [315,]  88.17623
## [316,]  87.77424
## [317,]  89.70877
## [318,]  87.01594
## [319,]  86.04112
## [320,]  86.62936
## [321,]  85.79495
## [322,]  86.65954
## [323,]  95.68181
## [324,]  87.68364
## [325,]  88.82854
## [326,]  86.06273
## [327,]  85.34398
## [328,]  91.57291
## [329,]  88.60068
## [330,]  86.33568
## [331,]  89.86824
## [332,]  88.09853
## [333,]  86.90583
## [334,]  90.45676
## [335,]  89.27660
## [336,]  86.19366
## [337,]  97.66379
## [338,]  86.24832
## [339,]  93.47750
## [340,]  87.28042
## [341,]  87.15725
## [342,]  85.13011
## [343,]  89.20521
## [344,]  89.48182
## [345,]  88.29567
## [346,]  89.17908
## [347,] 100.77611
## [348,]  92.09094
## [349,]  91.12190
## [350,]  91.11410
## [351,]  85.48137
## [352,]  85.38839
## [353,]  92.52810
## [354,]  87.84946
## [355,]  86.25155
## [356,]  88.04526
## [357,]  91.24816
## [358,]  87.03069
## [359,]  86.20363
## [360,]  88.31352
## [361,]  90.12453
## [362,]  91.67358
## [363,]  91.51868
## [364,]  89.89865
## [365,]  85.71547
## [366,]  89.12351
## [367,]  86.21637
## [368,]  86.45294
## [369,]  85.69713
## [370,]  85.13134
## [371,]  90.53430
## [372,]  87.87343
## [373,]  93.65873
## [374,]  85.55543
## [375,]  88.92497
## [376,]  85.78193
## [377,]  91.31075
## [378,]  86.46582
## [379,]  87.21023
## [380,]  92.20365
## [381,]  87.20793
## [382,]  87.58773
## [383,]  88.01356
## [384,]  90.61267
## [385,]  99.85686
## [386,]  87.57510
## [387,]  90.52222
## [388,]  86.70132
## [389,]  88.83102
## [390,]  86.44924
## [391,]  91.94060
## [392,]  87.82307
## [393,]  89.71229
## [394,]  85.18680
## [395,]  88.40280
## [396,]  88.29104
## [397,]  87.53805
## [398,]  93.17410
## [399,] 104.21260
## [400,]  92.02727
## [401,]  93.36142
## [402,]  85.64809
## [403,]  86.98733
## [404,]  92.88981
## [405,]  86.21684
## [406,]  86.18520
## [407,]  87.54021
## [408,]  89.39519
## [409,]  85.99968
## [410,]  87.12390
## [411,]  89.17646
## [412,]  87.63483
## [413,]  88.14706
## [414,]  90.08717
## [415,]  87.52480
## [416,]  85.50574
## [417,]  90.29236
## [418,]  89.86272
## [419,]  85.25950
## [420,]  88.65679
## [421,]  86.47170
## [422,]  86.81447
## [423,]  85.56028
## [424,]  90.38033
## [425,]  92.81182
## [426,]  89.04117
## [427,]  86.25650
## [428,]  93.80843
## [429,]  90.78968
## [430,]  88.09321
## [431,]  92.57171
## [432,]  89.63779
## [433,]  88.87906
## [434,]  89.63670
## [435,]  92.71792
## [436,]  86.91624
## [437,]  89.78488
## [438,]  91.25727
## [439,]  87.00143
## [440,]  87.54924
## [441,]  86.50712
## [442,]  85.43879
## [443,]  92.66678
## [444,]  95.84228
## [445,]  85.48549
## [446,]  85.19154
## [447,]  89.43710
## [448,]  97.58018
## [449,]  86.58562
## [450,]  90.40791
## [451,]  90.85109
## [452,]  89.34392
## [453,]  86.50316
## [454,]  88.79974
## [455,]  88.08160
## [456,]  85.80892
## [457,]  86.80930
## [458,]  87.07921
## [459,]  90.72847
## [460,]  90.60040
## [461,]  85.37231
## [462,]  87.32411
## [463,]  86.28155
## [464,]  87.13125
## [465,]  86.24967
## [466,]  87.57595
## [467,]  91.65820
## [468,]  93.67566
## [469,]  88.59683
## [470,]  87.43986
## [471,]  88.36274
## [472,]  86.18817
## [473,]  88.06298
## [474,]  86.32414
## [475,]  85.49710
## [476,]  86.06067
## [477,]  88.82850
## [478,]  89.34630
## [479,]  85.65525
## [480,]  91.90267
## [481,]  85.19075
## [482,]  86.36566
## [483,]  85.70671
## [484,]  88.38531
## [485,]  87.44834
## [486,]  87.71707
## [487,]  88.16479
## [488,]  86.09130
## [489,]  92.56725
## [490,]  87.75948
## [491,]  91.00329
## [492,]  86.44844
## [493,]  89.17696
## [494,]  92.00520
## [495,]  88.36261
## [496,]  85.57338
## [497,]  90.16328
## [498,]  88.42043
## [499,]  87.07347
## [500,]  97.22961
## [501,]  92.99430
## [502,]  87.83158
## [503,]  86.17816
## [504,]  90.72040
## [505,]  89.47707
## [506,]  86.83214
## [507,]  91.51964
## [508,]  86.70571
## [509,]  85.31545
## [510,]  88.09152
## [511,]  91.06932
## [512,]  90.49002
## [513,]  91.78564
## [514,]  88.77227
## [515,]  86.29903
## [516,]  87.77892
## [517,]  85.32902
## [518,]  86.49245
## [519,]  85.49657
## [520,]  88.39186
## [521,]  93.59015
## [522,]  89.27523
## [523,]  90.12888
## [524,]  96.39455
## [525,]  85.55646
## [526,]  88.02676
## [527,]  87.38107
## [528,]  85.90814
## [529,]  91.36732
## [530,]  91.27501
## [531,]  86.86526
## [532,]  85.12684
## [533,]  92.03746
## [534,]  88.10988
## [535,]  89.05648
## [536,]  88.27731
## [537,]  88.38462
## [538,]  91.26084
## [539,]  88.22812
## [540,]  86.79050
## 
## $sims.list$sigma
##            [,1]
##   [1,] 19.04031
##   [2,] 20.26728
##   [3,] 17.75630
##   [4,] 14.81668
##   [5,] 23.38609
##   [6,] 26.06636
##   [7,] 22.97741
##   [8,] 17.64154
##   [9,] 18.20063
##  [10,] 15.35182
##  [11,] 34.64739
##  [12,] 23.82132
##  [13,] 23.49012
##  [14,] 18.52355
##  [15,] 21.94135
##  [16,] 17.20094
##  [17,] 29.73016
##  [18,] 20.01357
##  [19,] 14.21296
##  [20,] 74.45794
##  [21,] 25.97832
##  [22,] 19.15839
##  [23,] 53.96907
##  [24,] 22.78377
##  [25,] 26.47870
##  [26,] 19.37854
##  [27,] 15.30793
##  [28,] 20.22068
##  [29,] 26.60430
##  [30,] 14.54280
##  [31,] 19.97021
##  [32,] 17.11432
##  [33,] 21.93943
##  [34,] 25.78428
##  [35,] 23.95694
##  [36,] 14.19244
##  [37,] 23.76650
##  [38,] 26.39332
##  [39,] 22.38560
##  [40,] 18.28676
##  [41,] 22.77229
##  [42,] 19.43852
##  [43,] 13.89488
##  [44,] 14.06590
##  [45,] 15.32801
##  [46,] 14.22176
##  [47,] 22.08712
##  [48,] 36.78145
##  [49,] 22.95093
##  [50,] 18.47837
##  [51,] 19.94474
##  [52,] 26.72292
##  [53,] 28.42493
##  [54,] 17.59316
##  [55,] 20.53088
##  [56,] 21.75882
##  [57,] 22.76986
##  [58,] 13.49640
##  [59,] 25.58894
##  [60,] 29.04956
##  [61,] 22.03636
##  [62,] 14.65501
##  [63,] 10.82729
##  [64,] 19.73262
##  [65,] 18.41882
##  [66,] 25.60159
##  [67,] 13.61800
##  [68,] 13.84255
##  [69,] 20.62426
##  [70,] 36.40873
##  [71,] 19.35547
##  [72,] 18.00818
##  [73,] 20.43520
##  [74,] 30.69395
##  [75,] 16.59739
##  [76,] 18.66932
##  [77,] 16.52713
##  [78,] 23.35473
##  [79,] 17.67779
##  [80,] 14.32274
##  [81,] 21.67318
##  [82,] 54.65984
##  [83,] 26.95858
##  [84,] 20.77166
##  [85,] 28.49342
##  [86,] 31.12419
##  [87,] 19.84449
##  [88,] 20.82128
##  [89,] 38.35100
##  [90,] 16.34955
##  [91,] 12.73788
##  [92,] 18.36248
##  [93,] 18.89193
##  [94,] 18.41957
##  [95,] 23.85610
##  [96,] 23.17097
##  [97,] 34.65310
##  [98,] 16.60777
##  [99,] 23.35442
## [100,] 17.17629
## [101,] 15.89322
## [102,] 22.96184
## [103,] 28.97403
## [104,] 28.17715
## [105,] 18.71715
## [106,] 22.47126
## [107,] 19.05626
## [108,] 34.38191
## [109,] 17.83421
## [110,] 29.21137
## [111,] 23.40646
## [112,] 24.03238
## [113,] 20.93632
## [114,] 20.77127
## [115,] 19.88565
## [116,] 18.61763
## [117,] 24.58217
## [118,] 27.07004
## [119,] 35.58056
## [120,] 24.23474
## [121,] 15.03291
## [122,] 14.16949
## [123,] 36.24522
## [124,] 21.73121
## [125,] 18.31905
## [126,] 19.35798
## [127,] 37.14344
## [128,] 17.14511
## [129,] 23.40607
## [130,] 16.29393
## [131,] 33.58943
## [132,] 21.32209
## [133,] 22.25984
## [134,] 21.81607
## [135,] 27.55539
## [136,] 17.89437
## [137,] 26.18561
## [138,] 19.77708
## [139,] 36.74350
## [140,] 17.11565
## [141,] 19.46943
## [142,] 33.28483
## [143,] 16.83097
## [144,] 28.32297
## [145,] 30.80710
## [146,] 39.14141
## [147,] 29.94038
## [148,] 22.43044
## [149,] 28.42523
## [150,] 19.63250
## [151,] 20.80889
## [152,] 26.84093
## [153,] 27.92750
## [154,] 18.03638
## [155,] 25.53411
## [156,] 28.35537
## [157,] 21.39309
## [158,] 21.81963
## [159,] 25.10404
## [160,] 24.44738
## [161,] 31.26894
## [162,] 17.21739
## [163,] 21.25020
## [164,] 17.23507
## [165,] 24.91052
## [166,] 19.21948
## [167,] 19.24852
## [168,] 16.85980
## [169,] 22.92418
## [170,] 21.23230
## [171,] 25.87200
## [172,] 19.33966
## [173,] 14.57966
## [174,] 31.04471
## [175,] 24.31874
## [176,] 20.71722
## [177,] 31.95779
## [178,] 23.47365
## [179,] 18.21580
## [180,] 23.61831
## [181,] 23.45457
## [182,] 21.19222
## [183,] 27.31848
## [184,] 22.28625
## [185,] 23.47921
## [186,] 22.63678
## [187,] 19.79686
## [188,] 17.47902
## [189,] 26.35083
## [190,] 24.14433
## [191,] 18.43644
## [192,] 14.28773
## [193,] 13.99016
## [194,] 36.83134
## [195,] 25.26097
## [196,] 20.72029
## [197,] 25.81372
## [198,] 23.95769
## [199,] 18.83229
## [200,] 21.16387
## [201,] 12.63766
## [202,] 22.19544
## [203,] 22.01812
## [204,] 19.22068
## [205,] 18.71251
## [206,] 19.70439
## [207,] 30.44743
## [208,] 21.88736
## [209,] 27.70510
## [210,] 30.27757
## [211,] 21.53720
## [212,] 16.08000
## [213,] 28.16588
## [214,] 20.16057
## [215,] 30.08242
## [216,] 31.69720
## [217,] 34.94204
## [218,] 13.01442
## [219,] 28.78241
## [220,] 26.20393
## [221,] 14.58644
## [222,] 21.46893
## [223,] 46.91390
## [224,] 19.09513
## [225,] 21.31560
## [226,] 20.16437
## [227,] 13.73345
## [228,] 16.18207
## [229,] 30.68179
## [230,] 22.24834
## [231,] 30.59921
## [232,] 41.94862
## [233,] 29.47269
## [234,] 18.68343
## [235,] 21.07314
## [236,] 20.22757
## [237,] 20.02589
## [238,] 17.01295
## [239,] 27.32737
## [240,] 15.82019
## [241,] 22.54865
## [242,] 14.97607
## [243,] 39.58616
## [244,] 14.52871
## [245,] 23.51754
## [246,] 17.54600
## [247,] 16.19537
## [248,] 16.87873
## [249,] 26.80363
## [250,] 23.22058
## [251,] 16.96567
## [252,] 31.70659
## [253,] 16.94468
## [254,] 19.77106
## [255,] 14.94646
## [256,] 15.45936
## [257,] 19.43835
## [258,] 18.56533
## [259,] 15.85737
## [260,] 28.64152
## [261,] 22.11052
## [262,] 21.38741
## [263,] 28.26519
## [264,] 16.00493
## [265,] 18.33059
## [266,] 22.47985
## [267,] 18.34883
## [268,] 21.75299
## [269,] 21.27587
## [270,] 15.28776
## [271,] 15.12601
## [272,] 24.23689
## [273,] 24.56569
## [274,] 27.20557
## [275,] 26.74244
## [276,] 28.56417
## [277,] 35.16496
## [278,] 22.59586
## [279,] 20.01857
## [280,] 34.38788
## [281,] 20.12072
## [282,] 17.78836
## [283,] 20.52944
## [284,] 30.30825
## [285,] 13.45956
## [286,] 15.44074
## [287,] 16.17835
## [288,] 20.11880
## [289,] 25.08904
## [290,] 27.17824
## [291,] 17.37269
## [292,] 23.02173
## [293,] 29.25738
## [294,] 13.46283
## [295,] 16.31516
## [296,] 22.34281
## [297,] 23.66387
## [298,] 14.27831
## [299,] 19.54890
## [300,] 13.71536
## [301,] 15.93915
## [302,] 19.93513
## [303,] 20.69214
## [304,] 20.83066
## [305,] 36.43813
## [306,] 20.56186
## [307,] 19.88375
## [308,] 25.29591
## [309,] 31.40342
## [310,] 15.68391
## [311,] 20.23848
## [312,] 15.23018
## [313,] 18.34595
## [314,] 15.85990
## [315,] 25.40826
## [316,] 21.78780
## [317,] 22.91549
## [318,] 19.40562
## [319,] 21.08184
## [320,] 16.42115
## [321,] 16.16648
## [322,] 19.66806
## [323,] 41.84645
## [324,] 16.87301
## [325,] 21.31950
## [326,] 20.21102
## [327,] 16.55281
## [328,] 33.96574
## [329,] 21.68337
## [330,] 17.79461
## [331,] 26.40159
## [332,] 20.90156
## [333,] 17.36796
## [334,] 19.31775
## [335,] 26.46437
## [336,] 18.23323
## [337,] 45.25747
## [338,] 17.01209
## [339,] 19.79219
## [340,] 15.66071
## [341,] 23.70676
## [342,] 16.13991
## [343,] 24.70382
## [344,] 22.06446
## [345,] 18.62347
## [346,] 29.02605
## [347,] 32.23325
## [348,] 29.13925
## [349,] 28.48208
## [350,] 32.51185
## [351,] 17.61999
## [352,] 18.89497
## [353,] 12.02194
## [354,] 14.53007
## [355,] 19.99357
## [356,] 22.33838
## [357,] 29.43150
## [358,] 12.86561
## [359,] 15.61433
## [360,] 21.09589
## [361,] 21.78659
## [362,] 18.80660
## [363,] 28.52486
## [364,] 24.64071
## [365,] 18.38371
## [366,] 25.10399
## [367,] 18.87923
## [368,] 21.65748
## [369,] 17.85456
## [370,] 16.37610
## [371,] 24.05985
## [372,] 18.36632
## [373,] 22.16007
## [374,] 15.48051
## [375,] 13.07513
## [376,] 18.96130
## [377,] 25.86925
## [378,] 16.61155
## [379,] 19.59956
## [380,] 17.32676
## [381,] 19.53797
## [382,] 17.19710
## [383,] 22.67705
## [384,] 30.96434
## [385,] 33.97187
## [386,] 24.04066
## [387,] 27.21397
## [388,] 21.09705
## [389,] 22.12060
## [390,] 15.75365
## [391,] 28.07124
## [392,] 20.71941
## [393,] 30.03934
## [394,] 16.89810
## [395,] 21.80572
## [396,] 25.72610
## [397,] 24.75439
## [398,] 31.05079
## [399,] 33.82620
## [400,] 33.12632
## [401,] 19.58314
## [402,] 16.18142
## [403,] 20.60065
## [404,] 33.48721
## [405,] 17.12268
## [406,] 19.50871
## [407,] 24.36952
## [408,] 17.67183
## [409,] 19.04113
## [410,] 18.90835
## [411,] 18.71235
## [412,] 18.24881
## [413,] 18.31685
## [414,] 21.84291
## [415,] 25.20335
## [416,] 18.71523
## [417,] 30.12116
## [418,] 27.11500
## [419,] 16.20702
## [420,] 11.86413
## [421,] 21.65372
## [422,] 19.75138
## [423,] 17.60849
## [424,] 18.32846
## [425,] 28.39865
## [426,] 14.85702
## [427,] 21.35877
## [428,] 24.80699
## [429,] 20.40507
## [430,] 14.17796
## [431,] 21.11599
## [432,] 21.34883
## [433,] 28.05827
## [434,] 29.95519
## [435,] 29.45597
## [436,] 15.40603
## [437,] 13.78866
## [438,] 28.89222
## [439,] 23.50425
## [440,] 20.67414
## [441,] 16.80848
## [442,] 17.03290
## [443,] 14.22489
## [444,] 42.97483
## [445,] 18.95834
## [446,] 16.61118
## [447,] 20.90097
## [448,] 49.04241
## [449,] 22.35432
## [450,] 23.95385
## [451,] 31.67961
## [452,] 24.00475
## [453,] 16.49132
## [454,] 25.99436
## [455,] 18.00440
## [456,] 18.78141
## [457,] 15.68274
## [458,] 19.44120
## [459,] 14.61212
## [460,] 18.76215
## [461,] 17.72413
## [462,] 19.33582
## [463,] 21.13476
## [464,] 22.54748
## [465,] 18.52525
## [466,] 24.05025
## [467,] 33.48350
## [468,] 35.93314
## [469,] 23.83502
## [470,] 17.30639
## [471,] 13.05444
## [472,] 13.93524
## [473,] 20.33371
## [474,] 19.08523
## [475,] 19.59538
## [476,] 21.40649
## [477,] 25.24235
## [478,] 20.84405
## [479,] 18.67684
## [480,] 34.54578
## [481,] 17.77903
## [482,] 20.14595
## [483,] 17.04870
## [484,] 14.22525
## [485,] 18.61762
## [486,] 21.70166
## [487,] 26.04489
## [488,] 18.38974
## [489,] 36.19313
## [490,] 23.28967
## [491,] 28.83510
## [492,] 17.95268
## [493,] 25.17741
## [494,] 23.25104
## [495,] 19.65843
## [496,] 17.03647
## [497,] 17.28969
## [498,] 26.06034
## [499,] 22.30870
## [500,] 30.04366
## [501,] 32.09347
## [502,] 24.50123
## [503,] 14.46250
## [504,] 27.57018
## [505,] 28.16194
## [506,] 17.80018
## [507,] 21.56041
## [508,] 15.05308
## [509,] 16.32093
## [510,] 14.59055
## [511,] 27.28451
## [512,] 23.53060
## [513,] 25.89855
## [514,] 18.18262
## [515,] 19.84744
## [516,] 19.31369
## [517,] 18.35592
## [518,] 16.10652
## [519,] 18.74999
## [520,] 15.19857
## [521,] 35.93599
## [522,] 22.19665
## [523,] 20.11330
## [524,] 17.35993
## [525,] 18.09524
## [526,] 14.00142
## [527,] 20.14737
## [528,] 20.10180
## [529,] 16.20678
## [530,] 15.96741
## [531,] 17.44792
## [532,] 16.91666
## [533,] 21.66039
## [534,] 12.54681
## [535,] 24.00137
## [536,] 21.76767
## [537,] 23.54363
## [538,] 21.17674
## [539,] 22.53135
## [540,] 21.14566
## 
## 
## $sims.matrix
##             beta0     beta1  deviance    sigma
##   [1,]  53.783100 0.7867868  85.42657 19.04031
##   [2,]  53.398183 0.7906458  85.69022 20.26728
##   [3,]  54.920755 0.8710892  89.63111 17.75630
##   [4,]  48.602041 0.8639909  86.79097 14.81668
##   [5,]  74.537425 0.7016324  88.86944 23.38609
##   [6,]  51.480917 0.8553789  88.44949 26.06636
##   [7,]  49.426795 0.7920673  87.08029 22.97741
##   [8,]  63.550623 0.7894394  87.51824 17.64154
##   [9,]  70.502571 0.6737023  88.08049 18.20063
##  [10,]  51.073669 0.8161566  85.26336 15.35182
##  [11,]  84.306789 0.5865231  93.87844 34.64739
##  [12,]  54.961921 0.7117359  89.78985 23.82132
##  [13,]  37.310017 0.9729955  90.23022 23.49012
##  [14,]  63.076736 0.7062678  87.15624 18.52355
##  [15,]  25.811038 0.9695207  89.22123 21.94135
##  [16,]  68.966096 0.7239044  87.21828 17.20094
##  [17,]  31.177036 0.9188880  90.56729 29.73016
##  [18,]  35.668708 0.8938526  86.99796 20.01357
##  [19,]  42.584473 0.8394418  87.45140 14.21296
##  [20,]   7.954745 1.2528010 107.42043 74.45794
##  [21,]  50.208453 0.8855114  89.28486 25.97832
##  [22,]  47.978001 0.8433675  85.45024 19.15839
##  [23,] 106.560979 0.4816514 101.35948 53.96907
##  [24,]  60.487025 0.7941651  87.22919 22.78377
##  [25,]  52.629022 0.8518380  88.70306 26.47870
##  [26,]  56.220240 0.8415138  87.36833 19.37854
##  [27,]  44.691626 0.8637157  85.87219 15.30793
##  [28,]  64.862716 0.7884804  87.98060 20.22068
##  [29,]  60.030967 0.7404365  88.48595 26.60430
##  [30,]  61.918710 0.7223206  87.75139 14.54280
##  [31,]  47.124722 0.7700567  88.48613 19.97021
##  [32,]  36.862189 0.9024481  86.71819 17.11432
##  [33,]  33.383757 0.8925280  87.98488 21.93943
##  [34,]  63.484465 0.6880196  89.33897 25.78428
##  [35,]  49.160658 0.8446648  87.09156 23.95694
##  [36,]  56.154847 0.7818030  85.99512 14.19244
##  [37,]  76.664852 0.6792797  89.21339 23.76650
##  [38,]  66.239870 0.6945475  89.04157 26.39332
##  [39,]  64.733265 0.7412649  87.01485 22.38560
##  [40,]  66.276861 0.7156179  86.59360 18.28676
##  [41,]  65.970413 0.7943497  89.12404 22.77229
##  [42,]  41.637487 0.8596507  86.03891 19.43852
##  [43,]  48.283516 0.8297875  86.12207 13.89488
##  [44,]  61.277797 0.7055726  90.13805 14.06590
##  [45,]  38.905803 0.8593682  87.52910 15.32801
##  [46,]  62.936723 0.6994452  89.89966 14.22176
##  [47,]  32.071513 0.8937174  88.41035 22.08712
##  [48,]  51.077878 0.7631978  93.11558 36.78145
##  [49,]  39.567621 0.9605434  89.96452 22.95093
##  [50,]  23.680939 0.9282835  91.57637 18.47837
##  [51,]  81.442910 0.6782009  90.94808 19.94474
##  [52,]  45.068476 0.8538212  88.27796 26.72292
##  [53,]  39.621762 0.8987597  89.38643 28.42493
##  [54,]  61.431812 0.6862718  89.58544 17.59316
##  [55,]  31.015310 0.9063204  88.23839 20.53088
##  [56,]  72.143280 0.6644241  88.43115 21.75882
##  [57,]  56.214983 0.8110177  86.80254 22.76986
##  [58,]  55.734701 0.7974044  86.52997 13.49640
##  [59,]  43.441937 0.8835418  88.07331 25.58894
##  [60,]  44.229310 0.6800811  98.09765 29.04956
##  [61,]  54.213786 0.7892438  86.24367 22.03636
##  [62,]  31.059103 0.9032286  90.71203 14.65501
##  [63,]  51.181048 0.8033884  90.97489 10.82729
##  [64,]  52.294494 0.8409467  86.05918 19.73262
##  [65,]  49.611746 0.7650895  87.70952 18.41882
##  [66,]  64.700192 0.6107138  94.15985 25.60159
##  [67,]  51.559865 0.8141126  86.18056 13.61800
##  [68,]  58.904485 0.8068047  88.10796 13.84255
##  [69,]  46.060903 0.8279128  86.03516 20.62426
##  [70,]  42.976482 0.7622053  94.41183 36.40873
##  [71,]  51.795512 0.8173497  85.36387 19.35547
##  [72,]  57.790703 0.8415476  88.33052 18.00818
##  [73,]  33.899096 0.8894407  87.66844 20.43520
##  [74,]  71.341329 0.6454402  91.55609 30.69395
##  [75,]  71.697193 0.6718779  88.70234 16.59739
##  [76,]   6.941834 1.0378497  98.29488 18.66932
##  [77,]  31.494965 0.9424207  88.50687 16.52713
##  [78,]  49.015397 0.8480022  86.89794 23.35473
##  [79,]  37.891654 0.9456800  88.66707 17.67779
##  [80,]  55.299451 0.8247159  87.05458 14.32274
##  [81,]  31.064086 0.9236670  88.05991 21.67318
##  [82,]  54.419302 0.8270377  99.44411 54.65984
##  [83,]  63.279479 0.8574408  92.68858 26.95858
##  [84,]  49.219733 0.8874030  87.82171 20.77166
##  [85,]  34.767624 0.8574756  90.50859 28.49342
##  [86,]  38.153403 0.9082542  90.62112 31.12419
##  [87,]  47.105650 0.9162806  89.24828 19.84449
##  [88,]  49.651807 0.8434461  85.98591 20.82128
##  [89,]  66.249598 0.7261293  93.57837 38.35100
##  [90,]  61.031031 0.8042923  87.54316 16.34955
##  [91,]  34.128237 0.8832976  92.57583 12.73788
##  [92,]  56.184540 0.7505507  86.21777 18.36248
##  [93,]  49.812402 0.8008490  85.61805 18.89193
##  [94,]  39.744835 0.8309881  87.87380 18.41957
##  [95,]  32.159274 0.8620050  90.01149 23.85610
##  [96,]  71.295974 0.6538677  89.09694 23.17097
##  [97,]  23.714444 0.9075922  93.96030 34.65310
##  [98,]  58.014020 0.7335097  86.95682 16.60777
##  [99,]  40.171245 0.9198731  87.98389 23.35442
## [100,]  41.116014 0.8453316  86.52139 17.17629
## [101,]  72.301969 0.6612413  89.73456 15.89322
## [102,]  38.854098 0.9244791  87.89894 22.96184
## [103,]  65.223510 0.7478843  89.64188 28.97403
## [104,]  27.927736 0.9719925  90.50788 28.17715
## [105,]  58.017283 0.7602513  85.62126 18.71715
## [106,]  43.340808 0.8957837  87.24918 22.47126
## [107,]  49.830390 0.8076315  85.47006 19.05626
## [108,]  59.353366 0.7599571  91.68872 34.38191
## [109,]  47.024985 0.8495318  85.31995 17.83421
## [110,]  55.650386 0.7683036  89.42352 29.21137
## [111,]  78.774134 0.7108372  90.75320 23.40646
## [112,]  38.948839 0.9041672  87.73616 24.03238
## [113,]  51.730161 0.8337125  86.04263 20.93632
## [114,]  43.859450 0.8672668  86.12015 20.77127
## [115,]  47.959299 0.8192477  85.70138 19.88565
## [116,]  60.574429 0.8427485  90.17865 18.61763
## [117,]  35.798178 0.9157247  88.17989 24.58217
## [118,]  57.702631 0.7588248  88.51168 27.07004
## [119,]  68.866203 0.8085085  94.25613 35.58056
## [120,]  53.173346 0.9176092  92.31637 24.23474
## [121,]  36.421710 0.8711635  88.53102 15.03291
## [122,]  47.109182 0.8026133  87.61566 14.16949
## [123,]  34.095266 1.0339997  95.56941 36.24522
## [124,]  45.657384 0.9067521  88.05115 21.73121
## [125,]  42.797593 0.9279144  88.64179 18.31905
## [126,]  34.117512 0.9536996  88.21553 19.35798
## [127,]  28.980493 1.0594378  95.96879 37.14344
## [128,]  61.461914 0.7493563  85.74601 17.14511
## [129,]  18.590485 1.0261519  91.31623 23.40607
## [130,]  53.792120 0.8547567  87.74104 16.29393
## [131,]  70.840486 0.6810219  92.00785 33.58943
## [132,]  63.924939 0.7136931  87.05398 21.32209
## [133,]  29.898680 0.9389108  88.36348 22.25984
## [134,]  38.094867 0.8368428  88.41552 21.81607
## [135,]  51.089733 0.8430377  88.73600 27.55539
## [136,]  56.956127 0.7944993  85.36060 17.89437
## [137,]  46.826450 0.8524982  88.02591 26.18561
## [138,]  56.868464 0.7852627  85.58850 19.77708
## [139,]  66.103466 0.6936035  93.15728 36.74350
## [140,]  39.876039 0.8712727  86.16672 17.11565
## [141,]  33.608694 0.9010878  87.41549 19.46943
## [142,]  66.644792 0.7141213  91.54391 33.28483
## [143,]  63.735108 0.7029908  87.51628 16.83097
## [144,]  24.747651 0.8940281  92.41228 28.32297
## [145,]  26.702933 1.0024233  91.97033 30.80710
## [146,]  24.885209 1.0026027  94.84155 39.14141
## [147,]  46.220369 0.7373928  92.71537 29.94038
## [148,]  66.778210 0.8074589  90.39505 22.43044
## [149,]  48.336625 0.8618304  89.19862 28.42523
## [150,]  46.808307 0.8234777  85.74559 19.63250
## [151,]  44.397618 0.8956735  86.98066 20.80889
## [152,]  88.630172 0.6879946  94.67858 26.84093
## [153,]  40.526237 0.8771999  89.03121 27.92750
## [154,]  37.253496 0.9236970  87.12052 18.03638
## [155,]  36.268143 0.9534782  89.43472 25.53411
## [156,]  21.553850 0.9837981  91.32691 28.35537
## [157,]  61.580185 0.7833810  86.77221 21.39309
## [158,]  46.931399 0.8586660  86.38971 21.81963
## [159,]  69.006659 0.7727662  89.73891 25.10404
## [160,]  29.500311 1.0415154  93.12840 24.44738
## [161,]  24.787593 0.8948391  93.06404 31.26894
## [162,]  25.596118 1.0034529  91.45507 17.21739
## [163,]  62.503533 0.7585723  86.46192 21.25020
## [164,]  50.425361 0.8451829  85.56907 17.23507
## [165,]  25.044943 0.9606195  89.84255 24.91052
## [166,]  59.593435 0.7496053  85.86690 19.21948
## [167,]  83.153895 0.6082782  91.35573 19.24852
## [168,]  23.001847 0.9743904  91.35129 16.85980
## [169,]  69.526981 0.6655723  88.71900 22.92418
## [170,]  46.162919 0.8804017  86.74639 21.23230
## [171,]  52.106384 0.8104341  87.75637 25.87200
## [172,]  70.763561 0.7484295  88.65616 19.33966
## [173,]  47.289521 0.8609207  86.37248 14.57966
## [174,]  53.247826 0.8638721  90.98603 31.04471
## [175,]  29.096009 0.9764035  89.46102 24.31874
## [176,]  54.358364 0.8245694  86.16542 20.71722
## [177,]  58.316375 0.8287518  91.26292 31.95779
## [178,]  84.310600 0.6858344  92.35758 23.47365
## [179,]  62.388917 0.7572177  85.85609 18.21580
## [180,]  50.006277 0.8682501  87.71699 23.61831
## [181,]  45.034084 0.8333570  87.04830 23.45457
## [182,]  49.355557 0.7850143  86.81870 21.19222
## [183,]  80.822424 0.6262290  90.92594 27.31848
## [184,]  52.848702 0.7169714  90.13009 22.28625
## [185,]  54.259261 0.7886511  86.79953 23.47921
## [186,]  57.996799 0.8097212  87.07024 22.63678
## [187,]  26.927484 0.9647804  88.94419 19.79686
## [188,]  63.309220 0.7526185  85.98926 17.47902
## [189,]  42.208419 0.9146619  89.04791 26.35083
## [190,]  58.900653 0.7955924  87.42999 24.14433
## [191,]  63.626396 0.7385601  86.04894 18.43644
## [192,]  56.353106 0.7410747  88.31176 14.28773
## [193,]  52.763529 0.7921979  86.16461 13.99016
## [194,]  92.836417 0.6217837  95.62269 36.83134
## [195,]  42.333542 0.8469885  87.93886 25.26097
## [196,]  42.793378 0.8735258  86.19927 20.72029
## [197,]  68.820837 0.7451504  88.97836 25.81372
## [198,]  28.790751 0.9771099  89.40507 23.95769
## [199,]  49.503649 0.8233075  85.27111 18.83229
## [200,]  42.197142 0.8111593  88.00239 21.16387
## [201,]  54.811651 0.8344218  89.65285 12.63766
## [202,]  58.699333 0.8049451  86.94444 22.19544
## [203,]  57.232413 0.7585591  86.54942 22.01812
## [204,]  73.045054 0.6820869  87.94953 19.22068
## [205,]  50.040130 0.8471324  85.65846 18.71251
## [206,]  41.667279 0.8307234  86.97386 19.70439
## [207,]  62.306298 0.6315039  93.83872 30.44743
## [208,]  13.871121 1.0497247  92.82335 21.88736
## [209,]  62.829038 0.8492076  92.04436 27.70510
## [210,]  56.857666 0.6952520  91.82650 30.27757
## [211,]  44.512353 0.8538853  86.25454 21.53720
## [212,]  46.466306 0.8193823  85.84965 16.08000
## [213,]  26.589120 0.9688002  90.56420 28.16588
## [214,]  55.977809 0.7695170  85.85304 20.16057
## [215,]  76.795791 0.7231161  91.88185 30.08242
## [216,]   5.876293 1.0742352  94.90184 31.69720
## [217,]  64.794712 0.7053228  92.31728 34.94204
## [218,]  43.923797 0.8482138  87.67509 13.01442
## [219,]  77.439065 0.6623681  90.74580 28.78241
## [220,]  60.105232 0.7719972  88.12971 26.20393
## [221,]  42.751056 0.8766048  86.58617 14.58644
## [222,]  59.640160 0.6969202  88.84238 21.46893
## [223,]  14.576776 1.1485410  99.68826 46.91390
## [224,]  30.571542 0.9548068  88.26986 19.09513
## [225,]  42.660351 0.9554125  90.71743 21.31560
## [226,]  40.499200 0.9347989  88.24383 20.16437
## [227,]  55.175718 0.8221834  87.30264 13.73345
## [228,]  37.805535 0.8836751  86.78386 16.18207
## [229,]  70.067218 0.6824140  90.83271 30.68179
## [230,]  58.348616 0.8183063  87.35925 22.24834
## [231,]  55.062628 0.8933662  92.46732 30.59921
## [232,]  -9.112670 1.2803941 101.26140 41.94862
## [233,]  37.589242 0.9403084  90.49072 29.47269
## [234,]  42.613532 0.8662116  85.73924 18.68343
## [235,]  39.695211 0.9086091  86.96774 21.07314
## [236,]  59.177849 0.7833133  85.95684 20.22757
## [237,]  47.699350 0.8393179  85.62428 20.02589
## [238,]  52.716665 0.7966409  85.12781 17.01295
## [239,]  66.784148 0.6864790  89.55329 27.32737
## [240,]  34.621271 0.9461308  88.94190 15.82019
## [241,]  49.727098 0.8739930  87.57528 22.54865
## [242,]  64.198380 0.7176755  87.42004 14.97607
## [243,]  10.355595 0.9520094  97.59899 39.58616
## [244,]  50.368167 0.8098688  85.73199 14.52871
## [245,]  63.010203 0.7942899  88.15877 23.51754
## [246,]  49.532029 0.8875121  88.13863 17.54600
## [247,]  44.597181 0.8596334  85.55957 16.19537
## [248,]  37.777792 0.8227388  90.62844 16.87873
## [249,]  41.621181 0.7620529  92.41228 26.80363
## [250,]  73.146469 0.6374051  89.66644 23.22058
## [251,]  51.067868 0.7958216  85.39301 16.96567
## [252,]  56.740769 0.7697426  90.47921 31.70659
## [253,]  51.402927 0.8179257  85.04798 16.94468
## [254,]  36.197756 0.8747267  87.24327 19.77106
## [255,]  54.601177 0.7791210  85.77566 14.94646
## [256,]  52.483056 0.7397170  89.95830 15.45936
## [257,]  53.469993 0.8387624  86.21206 19.43835
## [258,]  63.533069 0.7611426  86.19911 18.56533
## [259,]  81.752722 0.6314484  92.80059 15.85737
## [260,]  30.813939 0.9598600  90.38772 28.64152
## [261,]  51.617243 0.7707987  87.09224 22.11052
## [262,]  84.734229 0.6007325  91.27207 21.38741
## [263,]  39.495963 0.8915108  89.26557 28.26519
## [264,]  42.827956 0.8549540  85.87121 16.00493
## [265,]  63.100373 0.7638530  86.12944 18.33059
## [266,]  72.775004 0.7226589  88.62652 22.47985
## [267,]  52.593145 0.7472324  87.75675 18.34883
## [268,]  81.245982 0.6363608  89.99650 21.75299
## [269,]  36.737128 0.8908851  87.03169 21.27587
## [270,]  50.777730 0.8142126  85.30288 15.28776
## [271,]  57.827536 0.8131769  86.97693 15.12601
## [272,]  21.482123 0.9639273  90.62378 24.23689
## [273,]  29.110929 0.9030987  89.63807 24.56569
## [274,]  40.275760 0.9170923  89.23896 27.20557
## [275,]  45.228148 0.9002299  89.13529 26.74244
## [276,]  54.167018 0.7576940  89.46850 28.56417
## [277,]  84.187914 0.6037457  93.82391 35.16496
## [278,]  42.221774 1.0199270  97.82378 22.59586
## [279,]  57.666178 0.7584163  85.93930 20.01857
## [280,]  57.708141 0.8492953  92.65823 34.38788
## [281,]  52.205238 0.8365043  85.96989 20.12072
## [282,]  55.847668 0.7462443  86.57160 17.78836
## [283,]  37.848025 0.9456600  88.32786 20.52944
## [284,]  40.533176 0.8734373  90.05660 30.30825
## [285,]  46.242812 0.8216587  87.35397 13.45956
## [286,]  43.568805 0.8846486  86.51432 15.44074
## [287,]  42.794529 0.8430324  86.15865 16.17835
## [288,]  28.673661 0.9488820  88.42297 20.11880
## [289,]  48.529274 0.7969730  87.89320 25.08904
## [290,]  57.508825 0.7110372  89.95523 27.17824
## [291,]  56.362910 0.8330766  86.94511 17.37269
## [292,]  43.523050 0.8577325  86.84356 23.02173
## [293,]  -4.204677 0.9368876 107.88571 29.25738
## [294,]  43.102176 0.8164203  89.97331 13.46283
## [295,]  48.372212 0.8634983  86.08321 16.31516
## [296,]  47.811998 0.8649585  86.80085 22.34281
## [297,]  67.244874 0.7636416  88.39058 23.66387
## [298,]  54.193862 0.8114498  85.97089 14.27831
## [299,]  54.227605 0.8297656  86.04726 19.54890
## [300,]  61.437841 0.7689773  87.31073 13.71536
## [301,]  69.892947 0.7272371  88.16476 15.93915
## [302,]  51.786565 0.8465822  86.20954 19.93513
## [303,]  52.786661 0.8544544  87.01352 20.69214
## [304,]  38.656881 0.8551489  87.20475 20.83066
## [305,]  30.735352 0.8749440  93.79217 36.43813
## [306,]  52.653377 0.7923947  85.80660 20.56186
## [307,]  50.469750 0.8992043  89.33410 19.88375
## [308,]  64.445654 0.7230728  88.13535 25.29591
## [309,]  77.903091 0.6538775  91.73114 31.40342
## [310,]  64.080098 0.7554768  86.63464 15.68391
## [311,]  68.096389 0.7038953  87.08223 20.23848
## [312,]  70.424751 0.6877704  88.78739 15.23018
## [313,]  54.087372 0.8294628  85.83623 18.34595
## [314,]  63.161832 0.7540188  86.25347 15.85990
## [315,]  65.258798 0.7257849  88.17623 25.40826
## [316,]  64.670460 0.6952547  87.77424 21.78780
## [317,]  24.081415 0.9626074  89.70877 22.91549
## [318,]  61.100467 0.8019685  87.01594 19.40562
## [319,]  57.696585 0.7702616  86.04112 21.08184
## [320,]  38.029438 0.8847035  86.62936 16.42115
## [321,]  60.371887 0.7515603  85.79495 16.16648
## [322,]  37.584150 0.9057479  86.65954 19.66806
## [323,]  25.013655 0.9903836  95.68181 41.84645
## [324,]  51.726770 0.7566130  87.68364 16.87301
## [325,]  53.001436 0.8792831  88.82854 21.31950
## [326,]  52.534614 0.7791769  86.06273 20.21102
## [327,]  57.945193 0.7733722  85.34398 16.55281
## [328,]  42.755183 0.8523628  91.57291 33.96574
## [329,]  31.633774 0.9635967  88.60068 21.68337
## [330,]  47.199887 0.8766174  86.33568 17.79461
## [331,]  51.490387 0.8876719  89.86824 26.40159
## [332,]  71.349315 0.7294724  88.09853 20.90156
## [333,]  36.180016 0.8903250  86.90583 17.36796
## [334,]  52.305811 0.7201719  90.45676 19.31775
## [335,]  68.379685 0.7528318  89.27660 26.46437
## [336,]  39.158655 0.8803750  86.19366 18.23323
## [337,]  28.585495 1.0396854  97.66379 45.25747
## [338,]  51.551787 0.8517509  86.24832 17.01209
## [339,]  45.574896 0.9604134  93.47750 19.79219
## [340,]  48.669529 0.8753171  87.28042 15.66071
## [341,]  49.884001 0.7962436  87.15725 23.70676
## [342,]  53.544604 0.7977339  85.13011 16.13991
## [343,]  70.804111 0.7458446  89.20521 24.70382
## [344,]  51.069419 0.8979011  89.48182 22.06446
## [345,]  74.313515 0.6868366  88.29567 18.62347
## [346,]  51.977499 0.8055856  89.17908 29.02605
## [347,]  44.139726 0.6477956 100.77611 32.23325
## [348,]  28.116240 1.0187215  92.09094 29.13925
## [349,]  78.072732 0.6256267  91.12190 28.48208
## [350,]  38.624571 0.8801425  91.11410 32.51185
## [351,]  45.014221 0.8617023  85.48137 17.61999
## [352,]  47.586837 0.8295964  85.38839 18.89497
## [353,]  55.539441 0.8412568  92.52810 12.02194
## [354,]  41.204917 0.8416363  87.84946 14.53007
## [355,]  52.782817 0.7715600  86.25155 19.99357
## [356,]  67.763813 0.7584048  88.04526 22.33838
## [357,]  32.281073 0.9818275  91.24816 29.43150
## [358,]  48.988146 0.8270485  87.03069 12.86561
## [359,]  56.321974 0.8156141  86.20363 15.61433
## [360,]  69.650259 0.7521348  88.31352 21.09589
## [361,]  61.627934 0.6715915  90.12453 21.78659
## [362,]  34.495001 0.9933000  91.67358 18.80660
## [363,]  31.723266 0.9980898  91.51868 28.52486
## [364,]  24.541615 0.9595702  89.89865 24.64071
## [365,]  61.265950 0.7618791  85.71547 18.38371
## [366,]  52.769763 0.8733316  89.12351 25.10399
## [367,]  51.352237 0.7767664  86.21637 18.87923
## [368,]  57.700982 0.7559920  86.45294 21.65748
## [369,]  57.639619 0.7558955  85.69713 17.85456
## [370,]  54.846404 0.7946871  85.13134 16.37610
## [371,]  76.046784 0.6154941  90.53430 24.05985
## [372,]  69.391942 0.6784146  87.87343 18.36632
## [373,]  88.208864 0.5453554  93.65873 22.16007
## [374,]  53.353157 0.8210406  85.55543 15.48051
## [375,]  45.577419 0.8126159  88.92497 13.07513
## [376,]  61.288267 0.7564862  85.78193 18.96130
## [377,]  75.948767 0.7431038  91.31075 25.86925
## [378,]  41.250914 0.8971763  86.46582 16.61155
## [379,]  64.602921 0.7770772  87.21023 19.59956
## [380,]  22.300120 0.9473485  92.20365 17.32676
## [381,]  69.892306 0.7119740  87.20793 19.53797
## [382,]  36.859289 0.9308464  87.58773 17.19710
## [383,]  32.393714 0.9214477  88.01356 22.67705
## [384,]  43.750195 0.8174615  90.61267 30.96434
## [385,]  57.205495 0.5803860  99.85686 33.97187
## [386,]  44.203212 0.8859057  87.57510 24.04066
## [387,]  45.715384 0.9265065  90.52222 27.21397
## [388,]  57.479321 0.7436270  86.70132 21.09705
## [389,]  63.462691 0.8098931  88.83102 22.12060
## [390,]  42.018656 0.8894537  86.44924 15.75365
## [391,]  47.682144 0.7340722  91.94060 28.07124
## [392,]  31.597341 0.9351229  87.82307 20.71941
## [393,]  46.043080 0.8434307  89.71229 30.03934
## [394,]  51.363907 0.8013826  85.18680 16.89810
## [395,]  29.368006 0.9453083  88.40280 21.80572
## [396,]  54.835237 0.8344599  88.29104 25.72610
## [397,]  43.366037 0.8553236  87.53805 24.75439
## [398,]  85.758120 0.5713864  93.17410 31.05079
## [399,] 102.030662 0.7224086 104.21260 33.82620
## [400,]  30.809066 0.9572765  92.02727 33.12632
## [401,]  87.711368 0.5710945  93.36142 19.58314
## [402,]  57.189944 0.7622082  85.64809 16.18142
## [403,]  37.921343 0.8682276  86.98733 20.60065
## [404,]  76.620672 0.7208571  92.88981 33.48721
## [405,]  53.818464 0.7631195  86.21684 17.12268
## [406,]  43.315465 0.8360154  86.18520 19.50871
## [407,]  49.988424 0.8546448  87.54021 24.36952
## [408,]  55.767796 0.7153978  89.39519 17.67183
## [409,]  62.720297 0.7423653  85.99968 19.04113
## [410,]  49.473090 0.7731716  87.12390 18.90835
## [411,]  54.227303 0.8739521  89.17646 18.71235
## [412,]  63.201328 0.7953030  87.63483 18.24881
## [413,]  69.694716 0.7470482  88.14706 18.31685
## [414,]  65.409995 0.8135716  90.08717 21.84291
## [415,]  50.608020 0.8069910  87.52480 25.20335
## [416,]  58.095512 0.7665937  85.50574 18.71523
## [417,]  63.794648 0.7769000  90.29236 30.12116
## [418,]  64.212696 0.6828594  89.86272 27.11500
## [419,]  47.672272 0.8414235  85.25950 16.20702
## [420,]  56.274463 0.7849531  88.65679 11.86413
## [421,]  46.104079 0.8678607  86.47170 21.65372
## [422,]  67.676767 0.7179703  86.81447 19.75138
## [423,]  59.193154 0.7566762  85.56028 17.60849
## [424,]  38.037664 0.9655671  90.38033 18.32846
## [425,]  89.513615 0.5725694  92.81182 28.39865
## [426,]  33.685409 0.9342741  89.04117 14.85702
## [427,]  59.855986 0.7727919  86.25650 21.35877
## [428,]  71.052432 0.8179228  93.80843 24.80699
## [429,]  20.836191 0.9713151  90.78968 20.40507
## [430,]  41.179035 0.8427878  88.09321 14.17796
## [431,]  35.957849 1.0035372  92.57171 21.11599
## [432,]  30.225744 0.8823567  89.63779 21.34883
## [433,]  49.758138 0.8029169  88.87906 28.05827
## [434,]  53.967293 0.7882300  89.63670 29.95519
## [435,]  82.222420 0.5757030  92.71792 29.45597
## [436,]  45.469646 0.8841107  86.91624 15.40603
## [437,]  60.605840 0.7140287  89.78488 13.78866
## [438,]  30.191120 0.8787092  91.25727 28.89222
## [439,]  58.010478 0.7924515  87.00143 23.50425
## [440,]  48.256269 0.8884560  87.54924 20.67414
## [441,]  38.015453 0.8911813  86.50712 16.80848
## [442,]  51.725009 0.8346252  85.43879 17.03290
## [443,]  76.935575 0.6843195  92.66678 14.22489
## [444,]  50.261000 0.7484774  95.84228 42.97483
## [445,]  52.983645 0.8235449  85.48549 18.95834
## [446,]  48.040361 0.8392946  85.19154 16.61118
## [447,]  72.463726 0.6404455  89.43710 20.90097
## [448,]  42.890908 0.8902956  97.58018 49.04241
## [449,]  49.960755 0.8463835  86.58562 22.35432
## [450,]  81.864089 0.6578347  90.40791 23.95385
## [451,]  66.148787 0.7157332  90.85109 31.67961
## [452,]  29.059276 0.9746664  89.34392 24.00475
## [453,]  47.052969 0.8015193  86.50316 16.49132
## [454,]  39.665905 0.8435618  88.79974 25.99436
## [455,]  72.621667 0.6788405  88.08160 18.00440
## [456,]  57.146328 0.8064469  85.80892 18.78141
## [457,]  45.129757 0.8147495  86.80930 15.68274
## [458,]  55.782336 0.8399689  87.07921 19.44120
## [459,]  32.331699 0.9577749  90.72847 14.61212
## [460,]  71.567554 0.6325701  90.60040 18.76215
## [461,]  48.269839 0.8489196  85.37231 17.72413
## [462,]  35.456965 0.8798110  87.32411 19.33582
## [463,]  59.940422 0.7512358  86.28155 21.13476
## [464,]  39.152711 0.8721601  87.13125 22.54748
## [465,]  39.829547 0.8655127  86.24967 18.52525
## [466,]  60.802046 0.7880248  87.57595 24.05025
## [467,]  67.777818 0.7244519  91.65820 33.48350
## [468,]  51.403008 0.9019203  93.67566 35.93314
## [469,]  36.225943 0.9440948  88.59683 23.83502
## [470,]  69.308168 0.6948973  87.43986 17.30639
## [471,]  63.118460 0.7551414  88.36274 13.05444
## [472,]  55.225157 0.7823232  86.18817 13.93524
## [473,]  73.084281 0.6752700  88.06298 20.33371
## [474,]  39.242239 0.8956744  86.32414 19.08523
## [475,]  48.251152 0.8273747  85.49710 19.59538
## [476,]  56.368995 0.7912168  86.06067 21.40649
## [477,]  70.823344 0.6837196  88.82850 25.24235
## [478,]  60.238832 0.6874561  89.34630 20.84405
## [479,]  54.246364 0.7735238  85.65525 18.67684
## [480,]  47.704827 0.8020558  91.90267 34.54578
## [481,]  51.605618 0.8009784  85.19075 17.77903
## [482,]  40.406468 0.8636397  86.36566 20.14595
## [483,]  55.041760 0.8205488  85.70671 17.04870
## [484,]  48.958824 0.8746240  88.38531 14.22525
## [485,]  33.341012 0.9307244  87.44834 18.61762
## [486,]  68.817430 0.7415219  87.71707 21.70166
## [487,]  42.578927 0.8774089  88.16479 26.04489
## [488,]  51.914541 0.8477996  86.09130 18.38974
## [489,]  61.625053 0.7729838  92.56725 36.19313
## [490,]  62.911694 0.7860544  87.75948 23.28967
## [491,]  56.252778 0.7062968  91.00329 28.83510
## [492,]  49.819490 0.7813431  86.44844 17.95268
## [493,]  43.405348 0.7977306  89.17696 25.17741
## [494,]  37.377664 0.7938204  92.00520 23.25104
## [495,]  40.129279 0.9378605  88.36261 19.65843
## [496,]  58.918461 0.7568435  85.57338 17.03647
## [497,]  35.216849 0.9691626  90.16328 17.28969
## [498,]  62.759664 0.7251030  88.42043 26.06034
## [499,]  61.167223 0.7289641  87.07347 22.30870
## [500,]  -1.984845 1.0683755  97.22961 30.04366
## [501,]  22.965208 1.0313878  92.99430 32.09347
## [502,]  45.192071 0.8854314  87.83158 24.50123
## [503,]  44.552493 0.8517834  86.17816 14.46250
## [504,]  77.098069 0.7041288  90.72040 27.57018
## [505,]  51.942105 0.7633901  89.47707 28.16194
## [506,]  55.401832 0.8385181  86.83214 17.80018
## [507,]  58.311070 0.8763643  91.51964 21.56041
## [508,]  47.048343 0.8067556  86.70571 15.05308
## [509,]  50.076044 0.8375680  85.31545 16.32093
## [510,]  36.265758 0.8930969  88.09152 14.59055
## [511,]  82.411780 0.6430943  91.06932 27.28451
## [512,]  43.219627 0.9501017  90.49002 23.53060
## [513,]  33.452785 0.8266769  91.78564 25.89855
## [514,]  74.342823 0.6600617  88.77227 18.18262
## [515,]  39.597310 0.8819974  86.29903 19.84744
## [516,]  72.533234 0.6962828  87.77892 19.31369
## [517,]  51.951651 0.7953904  85.32902 18.35592
## [518,]  41.869762 0.8934901  86.49245 16.10652
## [519,]  46.193554 0.8334549  85.49657 18.74999
## [520,]  39.880666 0.9211362  88.39186 15.19857
## [521,]  24.173426 0.9518246  93.59015 35.93599
## [522,]  53.544467 0.8808891  89.27523 22.19665
## [523,]  80.479087 0.6694939  90.12888 20.11330
## [524,]  28.986063 1.0401220  96.39455 17.35993
## [525,]  57.549357 0.7612722  85.55646 18.09524
## [526,]  65.879918 0.7291473  88.02676 14.00142
## [527,]  50.470747 0.7644146  87.38107 20.14737
## [528,]  49.392783 0.8022860  85.90814 20.10180
## [529,]  56.690764 0.7026028  91.36732 16.20678
## [530,]  29.390896 0.9849308  91.27501 15.96741
## [531,]  36.695335 0.9136308  86.86526 17.44792
## [532,]  55.200253 0.7963535  85.12684 16.91666
## [533,]  66.503854 0.8271101  92.03746 21.66039
## [534,]  44.334054 0.8575479  88.10988 12.54681
## [535,]  28.567222 0.9580059  89.05648 24.00137
## [536,]  37.135538 0.8472379  88.27731 21.76767
## [537,]  36.181073 0.8624241  88.38462 23.54363
## [538,]  70.375744 0.6223053  91.26084 21.17674
## [539,]  64.997723 0.6876485  88.22812 22.53135
## [540,]  64.660631 0.7220641  86.79050 21.14566
## 
## $summary
##                mean         sd       2.5%        25%        50%        75%
## beta0    51.0705521 15.7292480 22.6160366 41.1632800 51.4419623 61.2689116
## beta1     0.8194548  0.1031059  0.6220314  0.7548606  0.8213496  0.8835751
## deviance 88.8870990  3.1707903 85.3242573 86.6204211 88.1529150 90.3895514
## sigma    22.2401849  6.7729366 13.6641624 17.7860257 20.8373527 25.2470036
##             97.5%     Rhat n.eff
## beta0    82.32183 1.014531   180
## beta1     1.02319 1.007259   250
## deviance 97.59006 1.014741   360
## sigma    36.80764 1.003094   540
## 
## $mean
## $mean$beta0
## [1] 51.07055
## 
## $mean$beta1
## [1] 0.8194548
## 
## $mean$deviance
## [1] 88.8871
## 
## $mean$sigma
## [1] 22.24018
## 
## 
## $sd
## $sd$beta0
## [1] 15.72925
## 
## $sd$beta1
## [1] 0.1031059
## 
## $sd$deviance
## [1] 3.17079
## 
## $sd$sigma
## [1] 6.772937
## 
## 
## $median
## $median$beta0
## [1] 51.44196
## 
## $median$beta1
## [1] 0.8213496
## 
## $median$deviance
## [1] 88.15291
## 
## $median$sigma
## [1] 20.83735
## 
## 
## $root.short
## [1] "beta0"    "beta1"    "deviance" "sigma"   
## 
## $long.short
## $long.short[[1]]
## [1] 1
## 
## $long.short[[2]]
## [1] 2
## 
## $long.short[[3]]
## [1] 3
## 
## $long.short[[4]]
## [1] 4
## 
## 
## $dimension.short
## [1] 0 0 0 0
## 
## $indexes.short
## $indexes.short[[1]]
## NULL
## 
## $indexes.short[[2]]
## NULL
## 
## $indexes.short[[3]]
## NULL
## 
## $indexes.short[[4]]
## NULL
## 
## 
## $last.values
## $last.values[[1]]
## $last.values[[1]]$beta0
## [1] 48.25115
## 
## $last.values[[1]]$beta1
## [1] 0.8273747
## 
## $last.values[[1]]$deviance
## [1] 85.4971
## 
## 
## $last.values[[2]]
## $last.values[[2]]$beta0
## [1] 36.69534
## 
## $last.values[[2]]$beta1
## [1] 0.9136308
## 
## $last.values[[2]]$deviance
## [1] 86.86526
## 
## 
## $last.values[[3]]
## $last.values[[3]]$beta0
## [1] 39.62176
## 
## $last.values[[3]]$beta1
## [1] 0.8987597
## 
## $last.values[[3]]$deviance
## [1] 89.38643
## 
## 
## 
## $program
## [1] "jags"
## 
## $model.file
## [1] "regression.txt"
## 
## $isDIC
## [1] TRUE
## 
## $DICbyR
## [1] TRUE
## 
## $pD
## [1] 5.016346
## 
## $DIC
## [1] 93.90344
## 
## attr(,"class")
## [1] "bugs"
## 
## $parameters.to.save
## [1] "beta0"    "beta1"    "sigma"    "deviance"
## 
## $model.file
## [1] "regression.txt"
## 
## $n.iter
## [1] 1000
## 
## $DIC
## [1] TRUE
## 
## attr(,"class")
## [1] "rjags"
```

In this, you want n.eff to be at least half of the number of iterations. Because there is no use of calculating the mean slopes etc when you don't have a high enough number of **effective** estimates.

The model shown before was the bare minimum required. Let's make it a little more complicated. We are now storing the residuals (y.err) and something that test  the probability of the slope being greater than 0.

```r
modelString="
model {
  #Likelihood
  for (i in 1:n) {
    y[i]~dnorm(mu[i],tau)
    mu[i] <- beta0+beta1*x[i]
    y.err[i] <- x[i] - mu[i]
  }
  
  #Priors
  beta0 ~dnorm(0,1.0E-6)
  beta1 ~dnorm(0,1.0E-6)
  tau <- 1/ (sigma * sigma)
  sigma~dunif(0,100)
  p.increase <- step(beta1)
  sd.x <- abs(beta1)*sd(x[])
  sd.resid <- sd(y.err)
}
"
writeLines(modelString,con="regression2.txt")

fert.list <- with(fert,
                  list(x=FERTILIZER,
                  y=YIELD,n=nrow(fert))
                  )
```

We need to change the parameters, because we want more to be returned to us

```r
params<- c("beta0","beta1","sigma","p.increase","sd.x","sd.resid")
```


```r
burnInSteps = 2000
nChains = 3
numSavedSteps = 50000
thinSteps = 5
nIter = ceiling((numSavedSteps * thinSteps)/nChains)
nIter
```

```
## [1] 83334
```


```r
library("R2jags", lib.loc="/Library/Frameworks/R.framework/Versions/3.0/Resources/library")
fert.r2jags3 <- jags(data=fert.list,
                    inits=NULL,
                    parameters.to.save=params,
                    model.file="regression2.txt",
                    n.chains=nChains,
                    n.iter=nIter,
                    n.burnin=burnInSteps,
                    n.thin=thinSteps
                    )
```

```
## Compiling model graph
##    Resolving undeclared variables
##    Allocating nodes
##    Graph Size: 67
## 
## Initializing model
```


```r
#print(fert.r2jags3)
```

Start making some plots.
First convert to mcmc output:

```r
fert.mcmc <- as.mcmc(fert.r2jags$BUGSoutput$sims.matrix)
head(fert.mcmc)
```

```
## Markov Chain Monte Carlo (MCMC) output:
## Start = 1 
## End = 7 
## Thinning interval = 1 
##         beta0     beta1 deviance    sigma
## [1,] 53.78310 0.7867868 85.42657 19.04031
## [2,] 53.39818 0.7906458 85.69022 20.26728
## [3,] 54.92076 0.8710892 89.63111 17.75630
## [4,] 48.60204 0.8639909 86.79097 14.81668
## [5,] 74.53742 0.7016324 88.86944 23.38609
## [6,] 51.48092 0.8553789 88.44949 26.06636
## [7,] 49.42679 0.7920673 87.08029 22.97741
```

First step, identify the columns in which the steps and intercept are in: bet0 and beta1, first 2 columns.
We now have 50000 coefficients for each the slope and the intercept. 


```r
coefs <- fert.mcmc[,1:2]
xs <- seq(min(fert$FERTILIZER), max(fert$FERTILIZER),len=100)
Xmat <- model.matrix(~FERTILIZER,data=data.frame(FERTILIZER=xs))
head(Xmat)
```

```
##   (Intercept) FERTILIZER
## 1           1   25.00000
## 2           1   27.27273
## 3           1   29.54545
## 4           1   31.81818
## 5           1   34.09091
## 6           1   36.36364
```

```r
pred <- coefs %*% t(Xmat)
dim(pred)
```

```
## [1] 540 100
```

Now calculate the average of all the predictions

```r
#newdata <- adply(pred,2,function(x){
#  data.frame(Mean=mean(x),Median=median(x),HPDinterval(as.mcmc(x))
#})
#newdata <- cbind(newdata,FERTILIZER=xs)
#head(newdata)
```

Now do the plotting

```r
#ggplot(newdata,aes(y=Mean,x=FERTILIZER)) + geom_line()
#  geom_ribbon(aes(ymin=lower,ymax=upper),fill='blue', alpha=.2) +
# theme_classic()
```



```r
loyn <- read.csv('loyn.csv',strip.white=T)
head(loyn)
```

```
##   ABUND AREA YR.ISOL DIST LDIST GRAZE ALT
## 1   5.3  0.1    1968   39    39     2 160
## 2   2.0  0.5    1920  234   234     5  60
## 3   1.5  0.5    1900  104   311     5 140
## 4  17.1  1.0    1966   66    66     3 160
## 5  13.8  1.0    1918  246   246     5 140
## 6  14.1  1.0    1965  234   285     3 130
```

```r
loyn$logAREA <- log10(loyn$AREA)
loyn$logDIST <- log10(loyn$DIST)
loyn$logLDIST <- log10(loyn$LDIST)
summary(lm(ABUND~logAREA+logDIST+logLDIST+GRAZE+ALT+YR.ISOL,data=loyn))
```

```
## 
## Call:
## lm(formula = ABUND ~ logAREA + logDIST + logLDIST + GRAZE + ALT + 
##     YR.ISOL, data = loyn)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -15.6506  -2.9390   0.5289   2.5353  15.2842 
## 
## Coefficients:
##               Estimate Std. Error t value Pr(>|t|)    
## (Intercept) -125.69725   91.69228  -1.371   0.1767    
## logAREA        7.47023    1.46489   5.099 5.49e-06 ***
## logDIST       -0.90696    2.67572  -0.339   0.7361    
## logLDIST      -0.64842    2.12270  -0.305   0.7613    
## GRAZE         -1.66774    0.92993  -1.793   0.0791 .  
## ALT            0.01951    0.02396   0.814   0.4195    
## YR.ISOL        0.07387    0.04520   1.634   0.1086    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 6.384 on 49 degrees of freedom
## Multiple R-squared:  0.6849,	Adjusted R-squared:  0.6464 
## F-statistic: 17.75 on 6 and 49 DF,  p-value: 8.443e-11
```


```r
modelString="
model {
  #Likelihood
  for (i in 1:n) {
    abund[i]~dnorm(mu[i],tau)
    mu[i] <- beta0+beta.dist*(dist[i])+beta.ldist*(ldist[i]) +
      beta.area*(area[i]) + beta.graze*(graze[i]) + 
      beta.alt*(alt[i]) + beta.yr*(yr[i])
    }
  
  #Priors
  beta0 ~dnorm(0,1.0E-6)
  beta.dist ~dnorm(0,1.0E-6)
  beta.ldist ~dnorm(0,1.0E-6)
  beta.area ~dnorm(0,1.0E-6)
  beta.graze ~dnorm(0,1.0E-6)
  beta.alt ~dnorm(0,1.0E-6)
  beta.yr ~dnorm(0,1.0E-6)
  tau <- 1/ (sigma * sigma)
  sigma~dunif(0,100)
  }
"
writeLines(modelString,con="multregression.txt")

loyn.list <- with(loyn,
                  list(abund=ABUND,dist=logDIST,ldist=logLDIST,area=logAREA,
                       graze=GRAZE,alt=ALT,yr=YR.ISOL,
                       n=nrow(loyn))
                  )
params<- c("beta0","beta.dist","beta.ldist","beta.area","beta.graze",
           "beta.alt","beta.yr","sigma")

burnInSteps = 2000
nChains = 3
numSavedSteps = 50000
thinSteps = 5
nIter = ceiling((numSavedSteps * thinSteps)/nChains)
nIter
```

```
## [1] 83334
```


```r
library("R2jags", lib.loc="/Library/Frameworks/R.framework/Versions/3.0/Resources/library")
loyn.r2jags <- jags(data=loyn.list,
                    inits=NULL,
                    parameters.to.save=params,
                    model.file="multregression.txt",
                    n.chains=nChains,
                    n.iter=nIter,
                    n.burnin=burnInSteps,
                    n.thin=thinSteps
                    )
```

```
## Compiling model graph
##    Resolving undeclared variables
##    Allocating nodes
##    Graph Size: 626
## 
## Initializing model
```

There is a shorter way. Instead of having to write out all the beta's, there is a matrix of beta's. This will work for simple regression, multiple regression etc. It is as follows:


```r
modelString="
model {
  for (i in 1:n) {
    y[i]~dnorm(mu[i],tau)
    mu[i] <- inprod(beta[],x[i,])
    y.err[i] <- y[i] - mu[i]
    }
  for (i in 1:nX) {
    beta[i] ~ dnorm(0,1.0E-06)
    }
  tau <- 1/ (sigma*sigma)
  sigma~dunif(0,100)
  }
"
writeLines(modelString,con="multregression1.txt")
```

beta[] works out how many betas you need and makes a matrix of them. Then we can just look through defining our priors for teh betas!

So the data list we supply must have:
- the resonse vector
- a model matrix of the predictor variables (linear predictor)
- N (the number of rows in teh dataset)
- nX, the number of predictors (including the intercept)

Her is the model matrix. The first column contains only ones, the other columns represent the predictor variables


```r
X <- model.matrix(~logDIST+logLDIST+logAREA+GRAZE+ALT+YR.ISOL,data=loyn)
head(X)
```

```
##   (Intercept)  logDIST logLDIST  logAREA GRAZE ALT YR.ISOL
## 1           1 1.591065 1.591065 -1.00000     2 160    1968
## 2           1 2.369216 2.369216 -0.30103     5  60    1920
## 3           1 2.017033 2.492760 -0.30103     5 140    1900
## 4           1 1.819544 1.819544  0.00000     3 160    1966
## 5           1 2.390935 2.390935  0.00000     5 140    1918
## 6           1 2.369216 2.454845  0.00000     3 130    1965
```

Here, X is a matrix!

Now I will count how many columns there are in this model matrix. In this case this is easy - there are seven. But it is useful to have this calculated for use for some circumstances

```r
nX <- ncol(X)
nX
```

```
## [1] 7
```

Now I have to define the parameters and run jags


```r
params<- c("beta","sigma","y.err")
burnInSteps = 2000
nChains = 3
numSavedSteps = 50000
thinSteps = 5
nIter = ceiling((numSavedSteps * thinSteps)/nChains)
nIter
```

```
## [1] 83334
```

```r
loyn.list <- with(loyn,list(y=ABUND, x=X, nX=nX, n=nrow(loyn)))
```

```r
library("R2jags", lib.loc="/Library/Frameworks/R.framework/Versions/3.0/Resources/library")
loyn.r2jags2 <- jags(data=loyn.list,
                    inits=NULL,
                    parameters.to.save=params,
                    model.file="multregression1.txt",
                    n.chains=nChains,
                    n.iter=nIter,
                    n.burnin=burnInSteps,
                    n.thin=thinSteps
                    )
```

```
## Compiling model graph
##    Resolving undeclared variables
##    Allocating nodes
##    Graph Size: 633
## 
## Initializing model
```

Now, we can generate the traceplots and decide whether we had enough iterations and appropriate thinning. Were the chains well mixed? Is the posterior likely to be stable?


```r
#quartz()
plot(as.mcmc(loyn.r2jags2))
```

```
## Error in character(ncol(y)): invalid 'length' argument
```

So now I am specifically indicating only to plot columns 1 to 7, which I know are the intercept and the partial slopes.


```r
plot(as.mcmc(loyn.r2jags2$BUGSoutput$sims.matrix[,1:7]))   
```

![plot of chunk unnamed-chunk-213](figure/unnamed-chunk-213-1.png) ![plot of chunk unnamed-chunk-213](figure/unnamed-chunk-213-2.png) 

BTW, the parameters are normally arranged in alphabetical order, so it is a good idea to make sure all the one you are interested in (e.g. beta) are before other studd alphabetically. JAGS also throws in Deviance, but this is usually put in at the end.

Check for autocorrelation


```r
autocorr.diag(as.mcmc(loyn.r2jags2$BUGSoutput$sims.matrix[,c(1:7,9)]))
```

```
##              beta[1]       beta[2]      beta[3]       beta[4]
## Lag 0   1.0000000000  1.0000000000 1.0000000000  1.0000000000
## Lag 1  -0.0034959667  0.0045494743 0.0005564878  0.0003734223
## Lag 5   0.0067478229  0.0006726002 0.0026549923 -0.0061700323
## Lag 10  0.0008315104  0.0022944325 0.0022112913 -0.0058832267
## Lag 50 -0.0002163488 -0.0050211099 0.0039649221 -0.0013151119
##              beta[5]       beta[6]       beta[7]         sigma
## Lag 0   1.0000000000  1.0000000000  1.000000e+00  1.0000000000
## Lag 1  -0.0022975685 -0.0118558538 -2.941502e-03 -0.0009093866
## Lag 5   0.0047833007 -0.0028170656  5.479705e-03  0.0009092211
## Lag 10  0.0007394574  0.0007690748  9.813183e-05 -0.0030403735
## Lag 50 -0.0070120378  0.0056205137  1.076503e-04 -0.0017053282
```

No evidence of autocorrelation. 

Now we look at the parameters of the estimates and confidence intervals etc

```r
#print(loyn.r2jags)
```


The first beta will be the intercept. After that, you need to remember what order you put the predictors in the model.matrix. Recall 

```
X <- model.matrix(~logDIST+logLDIST+logAREA+GRAZE+ALT+YR.ISOL,data=loyn)
head(X)
```


--------------------------
ANOVA






