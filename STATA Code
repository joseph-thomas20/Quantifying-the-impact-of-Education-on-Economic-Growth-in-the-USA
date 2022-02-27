. log using "/Users/josephthomas/Documents/Masters/Economic Data Analysis/EDA/log.smcl"
---------------------------------------------------------------------------------------------------
      name:  <EDA_log>
       log:  /Users/josephthomas/Documents/Masters/Economic Data Analysis/EDA/log.smcl
  log type:  smcl
 opened on:  10 Jan 2022, 10:02:53


. cd "/Users/josephthomas/Documents/Masters/Economic Data Analysis/EDA"
/Users/josephthomas/Documents/Masters/Economic Data Analysis/EDA

. ssc install missings 
checking missings consistency and verifying not already installed...
all files already exist and are up to date.

. use "/Users/josephthomas/Documents/Masters/Economic Data Analysis/EDA/pwt100.dta"

. keep country countrycode year rgdpna pop hc csh_i

. sort countrycode

. missings report, percent

Checking missings in all variables:
4173 observations with missing values

--------------------------
        |      #        %
--------+-----------------
    pop |   2411    18.82
     hc |   4173    32.58
 rgdpna |   2411    18.82
  csh_i |   2411    18.82
--------------------------

. drop if pop ==.
(2,411 observations deleted)

. drop if hc ==.
(1,762 observations deleted)

. drop if rgdpna ==.
(0 observations deleted)

. drop if csh_i ==.
(0 observations deleted)

. save pwt100.dta, replace
file pwt100.dta saved

. *** Generating New Variables ***

. generate gdp_pc = rgdpna/pop

. generate loggdp_pc = log(gdp_pc)

. bysort countrycode (year): gen gdp_gr = ((gdp_pc[_n]/gdp_pc[_n-1])-1)*100
(145 missing values generated)

. *** Creating 5 Year Period Grouping ***

. generate period = 5*floor(year/5)

. *** Creating Averages of Variables Per Period ***

. bysort countrycode period: egen avgdp_pc_gr = mean(gdp_gr)
(8 missing values generated)

. bysort countrycode period: generate gr_pop = ((pop[_n]/pop[_n-1])-1)*100
(1,737 missing values generated)

. bysort countrycode period: egen avpop_gr = mean(gr_pop)  
(8 missing values generated)

. bysort countrycode period: egen avloggdp_pc = mean(loggdp_pc)

. bysort countrycode period: egen avhc = mean(hc)

. bysort countrycode period: egen avcsh_i = mean(csh_i)

. save pwt100.dta, replace
file pwt100.dta saved

. 
. import delimited "/Users/josephthomas/Documents/Masters/Economic Data Analysis/EDA/AAP.csv", clea
> r 
(encoding automatically selected: ISO-8859-1)
(8 vars, 59,922 obs)

. keep entity code year averageharmonisedlearningoutcome

. rename entity country 

. rename code countrycode

. rename averageharmonisedlearningoutcome ed_quality

. drop if ed_quality ==.
(59,295 observations deleted)

. describe year

Variable      Storage   Display    Value
    name         type    format    label      Variable label
---------------------------------------------------------------------------------------------------
year            int     %8.0g                 Year

. drop if countrycode == ""
(75 observations deleted)

. duplicates report countrycode year

Duplicates in terms of countrycode year

--------------------------------------
   Copies | Observations       Surplus
----------+---------------------------
        1 |          552             0
--------------------------------------

. save AAP.dta
file AAP.dta saved

. . *** Merging the Datasets ***

. use pwt100.dta

. merge m:1 countrycode year using AAP.dta
(variable country was str34, now str44 to accommodate using data's values)
(variable countrycode was str3, now str8 to accommodate using data's values)

    Result                      Number of obs
    -----------------------------------------
    Not matched                         8,157
        from master                     8,121  (_merge==1)
        from using                         36  (_merge==2)

    Matched                               516  (_merge==3)
    -----------------------------------------

. *** Droping Missing values 

. drop if ed_quality ==.
(8,121 observations deleted)

. drop if pop ==.
(36 observations deleted)

. drop _merge

. keep avgdp_pc_gr loggdp_pc avcsh_i avpop_gr avhc ed_quality countrycode period year

. save merged.dta
file merged.dta saved

. *** Keeping only OECD countries ***

. clear all

. use merged.dta

. keep if countrycode == "AUS"|countrycode == "AUT"|countrycode == "BEL"|countrycode == "CAN"|count
> rycode == "CHL"|countrycode == "COL"|countrycode == "CZE"|countrycode == "DNK"|countrycode == "FI
> N"|countrycode == "FRA"|countrycode == "DEU"|countrycode == "GRC"|countrycode == "HUN"|countrycod
> e == "ISL"|countrycode == "IRL"|countrycode == "ISR"|countrycode == "ITA"|countrycode == "JPN"|co
> untrycode == "LVA"|countrycode == "LTU"|countrycode == "LUX"|countrycode == "MEX"|countrycode == 
> "NLD"|countrycode == "NZL"|countrycode == "NOR"|countrycode == "POL"|countrycode == "PRT"|country
> code == "SVK"|countrycode == "SVN"|countrycode == "KOR"|countrycode == "ESP"|countrycode == "SWE"
> |countrycode == "CHE"|countrycode == "TUR"|countrycode == "GBR"|countrycode == "USA"|countrycode 
> == "EST"
(265 observations deleted)

. 
. encode countrycode, generate(countrycode2)

. xtset countrycode2 period, delta(5)

Panel variable: countrycode2 (unbalanced)
 Time variable: period, 1970 to 2015, but with gaps
         Delta: 5 units

. rename avgdp_pc_gr y

. rename loggdp_pc logGDP_pc

. rename avcsh_i share

. rename avpop_growth pop
variable avpop_growth not found
r(111);

. rename avpop_gr p

. rename avhc edquan

. rename ed_quality edqual

. label variable y "avearge GDP per capita growth rate"

. label variable logGDP_pc "log of initial GDP per capita"

. label variable share "avearge share of gross capital formation (% of GDP)"

. label variable p "average population growth rate"

. label variable edquan "average human capital index per person (education quality)"

.label variable edqual "education quality"

.save OECDonly.dta
file OECDonly.dta saved
. xtsum y logGDP_pc share p edquan edqual

Variable         |      Mean   Std. dev.       Min        Max |    Observations
-----------------+--------------------------------------------+----------------
y        overall |  2.099394   2.152022   -13.8688    9.02817 |     N =     251
         between |              1.17418   .7479415   5.446441 |     n =      37
         within  |             1.877627  -13.66929   8.591493 | T-bar = 6.78378
                 |                                            |
logGDP~c overall |  10.28171   .5047851   8.697314   11.39505 |     N =     251
         between |             .4552395   9.226799   11.10073 |     n =      37
         within  |             .2574615     9.3399   10.98489 | T-bar = 6.78378
                 |                                            |
share    overall |  .2639425   .0561208   .1101773   .4430591 |     N =     251
         between |              .043795   .1806026   .3694423 |     n =      37
         within  |             .0363782   .1581145   .4162719 | T-bar = 6.78378
                 |                                            |
p        overall |  .6139596   .7278332  -1.502301   3.505216 |     N =     251
         between |             .6889353  -1.138868   2.275529 |     n =      37
         within  |              .335245  -.6173377   1.843647 | T-bar = 6.78378
                 |                                            |
edquan   overall |  3.089346   .4095013   1.992919   3.807992 |     N =     251
         between |              .386354   2.227309   3.595816 |     n =      37
         within  |             .2059194   2.530521   3.644613 | T-bar = 6.78378
                 |                                            |
edqual   overall |  491.7786   56.37913     147.23     607.23 |     N =     251
         between |             42.71065    369.928   578.5457 |     n =      37
         within  |             36.64588    190.782   577.8206 | T-bar = 6.78378

. *** VIF ***

. collin y logGDP_pc share p edquan edqual
(obs=251)

  Collinearity Diagnostics

                        SQRT                   R-
  Variable      VIF     VIF    Tolerance    Squared
----------------------------------------------------
         y      1.40    1.18    0.7123      0.2877
 logGDP_pc      2.16    1.47    0.4629      0.5371
     share      1.45    1.20    0.6916      0.3084
         p      1.19    1.09    0.8371      0.1629
    edquan      1.88    1.37    0.5316      0.4684
    edqual      2.08    1.44    0.4808      0.5192
----------------------------------------------------
  Mean VIF      1.69

                           Cond
        Eigenval          Index
---------------------------------
    1     5.9463          1.0000
    2     0.5927          3.1675
    3     0.4156          3.7828
    4     0.0305         13.9561
    5     0.0093         25.3156
    6     0.0049         34.8293
    7     0.0007         91.2090
---------------------------------
 Condition Number        91.2090 
 Eigenvalues & Cond Index computed from scaled raw sscp (w/ intercept)
 Det(correlation matrix)    0.2125

. *** Partial Correlation ***

. pcorr logGDP_pc share p equan equal
(obs=251)

Partial and semipartial correlations of logGDP_pc with

               Partial   Semipartial      Partial   Semipartial   Significance
   Variable |    corr.         corr.      corr.^2       corr.^2          value
------------+-----------------------------------------------------------------
      share |   0.2358        0.1836       0.0556        0.0337         0.0002
          p |   0.1756        0.1350       0.0308        0.0182         0.0056
      equan |   0.4561        0.3878       0.2080        0.1504         0.0000
      equal |   0.1542        0.1181       0.0238        0.0140         0.0151

. pcorr share logGDP_pc p equan equal
(obs=251)

Partial and semipartial correlations of share with

               Partial   Semipartial      Partial   Semipartial   Significance
   Variable |    corr.         corr.      corr.^2       corr.^2          value
------------+-----------------------------------------------------------------
  logGDP_pc |   0.2358        0.2086       0.0556        0.0435         0.0002
          p |   0.2458        0.2180       0.0604        0.0475         0.0001
      equan |  -0.2695       -0.2406       0.0726        0.0579         0.0000
      equal |   0.3752        0.3480       0.1408        0.1211         0.0000

. pcorr p logGDP_pc share edquan edqual
(obs=251)

Partial and semipartial correlations of p with

               Partial   Semipartial      Partial   Semipartial   Significance
   Variable |    corr.         corr.      corr.^2       corr.^2          value
------------+-----------------------------------------------------------------
  logGDP_pc |   0.1756        0.1633       0.0308        0.0267         0.0056
      share |   0.2458        0.2321       0.0604        0.0539         0.0001
      equan |   0.0334        0.0306       0.0011        0.0009         0.6005
      equal |  -0.3341       -0.3245       0.1116        0.1053         0.0000

. pcorr equan logGDP_pc share p equal
(obs=251)

Partial and semipartial correlations of equan with

               Partial   Semipartial      Partial   Semipartial   Significance
   Variable |    corr.         corr.      corr.^2       corr.^2          value
------------+-----------------------------------------------------------------
  logGDP_pc |   0.4561        0.3737       0.2080        0.1396         0.0000
      share |  -0.2695       -0.2040       0.0726        0.0416         0.0000
          p |   0.0334        0.0244       0.0011        0.0006         0.6005
      equal |   0.4295        0.3468       0.1845        0.1203         0.0000

. pcorr edqual logGDP_pc share p edquan 
(obs=251)

Partial and semipartial correlations of equal with

               Partial   Semipartial      Partial   Semipartial   Significance
   Variable |    corr.         corr.      corr.^2       corr.^2          value
------------+-----------------------------------------------------------------
  logGDP_pc |   0.1542        0.1138       0.0238        0.0130         0.0151
      share |   0.3752        0.2951       0.1408        0.0871         0.0000
          p |  -0.3341       -0.2584       0.1116        0.0668         0.0000
      equan |   0.4295        0.3467       0.1845        0.1202         0.0000

. *** B-P LM Test For RE ***

. xtreg y logGDP_pc share p edquan edqual

Random-effects GLS regression                   Number of obs     =        251
Group variable: countrycode2                    Number of groups  =         37

R-squared:                                      Obs per group:
     Within  = 0.3839                                         min =          3
     Between = 0.3236                                         avg =        6.8
     Overall = 0.2856                                         max =         10

                                                Wald chi2(5)      =     119.15
corr(u_i, X) = 0 (assumed)                      Prob > chi2       =     0.0000

------------------------------------------------------------------------------
           y | Coefficient  Std. err.      z    P>|z|     [95% conf. interval]
-------------+----------------------------------------------------------------
   logGDP_pc |  -3.061171   .4034232    -7.59   0.000    -3.851866   -2.270476
       share |   12.44578   2.568633     4.85   0.000     7.411355    17.48021
           p |     .01707   .2145874     0.08   0.937    -.4035136    .4376535
      edquan |   .1542945    .520952     0.30   0.767    -.8667527    1.175342
      edqual |   .0189149   .0029951     6.32   0.000     .0130446    .0247852
       _cons |   20.51578   3.179243     6.45   0.000     14.28458    26.74698
-------------+----------------------------------------------------------------
     sigma_u |  .81134107
     sigma_e |  1.5584393
         rho |  .21324028   (fraction of variance due to u_i)
------------------------------------------------------------------------------

. xttest0

Breusch and Pagan Lagrangian multiplier test for random effects

        y[countrycode2,t] = Xb + u[countrycode2] + e[countrycode2,t]

        Estimated results:
                         |       Var     SD = sqrt(Var)
                ---------+-----------------------------
                       y |   4.631199       2.152022
                       e |   2.428733       1.558439
                       u |   .6582743       .8113411

        Test: Var(u) = 0
                             chibar2(01) =    12.29
                          Prob > chibar2 =   0.0002

. *** Wooldridge Test for Autocorrelation ***

. xtserial y logGDP_pc share p edquan edqual, output

Linear regression                               Number of obs     =        211
                                                F(5, 36)          =      29.50
                                                Prob > F          =     0.0000
                                                R-squared         =     0.4858
                                                Root MSE          =     1.8116

                          (Std. err. adjusted for 37 clusters in countrycode2)
------------------------------------------------------------------------------
             |               Robust
         D.y | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
-------------+----------------------------------------------------------------
   logGDP_pc |
         D1. |  -13.17043   1.386758    -9.50   0.000    -15.98291   -10.35796
             |
       share |
         D1. |   20.04511    4.28122     4.68   0.000     11.36239    28.72783
             |
           p |
         D1. |  -.1427594   .3987972    -0.36   0.722    -.9515577    .6660388
             |
      edquan |
         D1. |   11.56344   2.122626     5.45   0.000     7.258553    15.86832
             |
      edqual |
         D1. |   .0167718   .0053908     3.11   0.004     .0058387     .027705
------------------------------------------------------------------------------

Wooldridge test for autocorrelation in panel data
H0: no first-order autocorrelation
    F(  1,      36) =     35.754
           Prob > F =      0.0000

. *** Wald Test ***

. quietly xtreg logGDP_pc share p edquan edqual , fe vce(cluster countrycode2)
d
. xttest3

Modified Wald test for groupwise heteroskedasticity
in fixed effect regression model

H0: sigma(i)^2 = sigma^2 for all i

chi2 (37)  =    4482.94
Prob>chi2 =      0.0000


. *** Hausman Test ***

. quietly xtreg y logGDP_pc share p edquan edqual , fe vce(cluster countrycode2)

. estimates store fixed2

. quietly xtreg y logGDP_pc share p edquan edqual , re vce(cluster countrycode2)

. estimates store random2

. rhausman fixed2 random2, cluster
bootstrap in progress
----+--- 1 ---+--- 2 ---+--- 3 ---+--- 4 ---+--- 5 
.................................................. 50
.................................................. 100
--------------------------------------------------------------------------------
Cluster-Robust Hausman Test
(based on 100 bootstrap repetitions)

b1: obtained from xtreg y logGDP_pc share p edquan edqual , fe vce(cluster countrycode2)
b2: obtained from xtreg y logGDP_pc share p edquan edqual , re vce(cluster countrycode2)

    Test:  Ho:  difference in coefficients not systematic

                  chi2(5) = (b1-b2)' * [V_bootstrapped(b1-b2)]^(-1) * (b1-b2)
                          =       11.24
                Prob>chi2 =      0.0468

. 
. *** ICC ***

. quietly xtmixed  y logGDP_pc share p edquan edqual||countrycode2: , vce(cluster countrycode2)

. estat icc

Residual intraclass correlation

------------------------------------------------------------------------------
                       Level |        ICC   Std. err.     [95% conf. interval]
-----------------------------+------------------------------------------------
                countrycode2 |   .2456265   .1391397      .0695361    .5865414
------------------------------------------------------------------------------

. 
. *** Model 1 ***

. xtmixed y logGDP_pc share p edquan edqual ||countrycode2: , vce(cluster countrycode2)

Performing EM optimisation: 

Performing gradient-based optimisation: 

Iteration 0:   log pseudolikelihood = -497.43781  
Iteration 1:   log pseudolikelihood =  -497.4378  

Computing standard errors:

Mixed-effects regression                        Number of obs     =        251
Group variable: countrycode2                    Number of groups  =         37
                                                Obs per group:
                                                              min =          3
                                                              avg =        6.8
                                                              max =         10
                                                Wald chi2(5)      =      89.70
Log pseudolikelihood =  -497.4378               Prob > chi2       =     0.0000

                          (Std. err. adjusted for 37 clusters in countrycode2)
------------------------------------------------------------------------------
             |               Robust
           y | Coefficient  std. err.      z    P>|z|     [95% conf. interval]
-------------+----------------------------------------------------------------
   logGDP_pc |  -3.155888   .6622399    -4.77   0.000    -4.453854   -1.857921
       share |    12.7528   3.395719     3.76   0.000     6.097313    19.40829
           p |   .0029874   .2672515     0.01   0.991    -.5208159    .5267908
      edquan |   .2067591   .6924966     0.30   0.765    -1.150509    1.564027
      edqual |    .019294   .0111077     1.74   0.082    -.0024767    .0410646
       _cons |    21.0689    3.12584     6.74   0.000     14.94237    27.19544
------------------------------------------------------------------------------

------------------------------------------------------------------------------
                             |               Robust           
  Random-effects parameters  |   Estimate   std. err.     [95% conf. interval]
-----------------------------+------------------------------------------------
countrycode2: Identity       |
                   sd(_cons) |   .9211572   .3030725      .4833676    1.755456
-----------------------------+------------------------------------------------
                sd(Residual) |   1.614319   .1827485      1.293092    2.015345
------------------------------------------------------------------------------

. *** Checking for Normality ***

. quietly xtmixed y logGDP_pc share p edquan edqual ||countrycode2: , vce(cluster countrycode2)

. predict double xb, xb

. predict double re, residuals

. generate re1 = re<-8 | re>8

. generate countryperiod ="LVA_1990" if re>8
(251 missing values generated)

. replace countryperiod ="LVA_1990" if re<-8
variable countryperiod was str1 now str8
(1 real change made)

. 
. scatter re xb, mcolor(black) msize(small) ylabel( , angle(horizontal) nogrid) || scatter re xb if
>  re1==1,mlabel(countryperiod) mcolor(black) msize(small) ylabel( , angle(horizontal) nogrid)

. 
. graph save "Graph" "/Users/josephthomas/Documents/Masters/Economic Data Analysis/EDA/Res Graph.gp
> h"
file /Users/josephthomas/Documents/Masters/Economic Data Analysis/EDA/Res Graph.gph saved

. *** Running Model 1 Without LVA_1990 Outlier ***

. drop if re1 == 1
(1 observation deleted)

. xtmixed y logGDP_pc share p edquan edqual||countrycode2: , vce(cluster countrycode2)

Performing EM optimisation: 

Performing gradient-based optimisation: 

Iteration 0:   log pseudolikelihood =   -464.588  
Iteration 1:   log pseudolikelihood =   -464.588  

Computing standard errors:

Mixed-effects regression                        Number of obs     =        250
Group variable: countrycode2                    Number of groups  =         37
                                                Obs per group:
                                                              min =          3
                                                              avg =        6.8
                                                              max =         10
                                                Wald chi2(5)      =     110.40
Log pseudolikelihood =   -464.588               Prob > chi2       =     0.0000

                          (Std. err. adjusted for 37 clusters in countrycode2)
------------------------------------------------------------------------------
             |               Robust
           y | Coefficient  std. err.      z    P>|z|     [95% conf. interval]
-------------+----------------------------------------------------------------
   logGDP_pc |  -2.553458   .4209848    -6.07   0.000    -3.378573   -1.728343
       share |   13.45207   2.812111     4.78   0.000     7.940437    18.96371
           p |  -.3316205   .1703759    -1.95   0.052    -.6655512    .0023102
      edquan |   .5756465   .5475373     1.05   0.293    -.4975069      1.6488
      edqual |   .0044095   .0029558     1.49   0.136    -.0013838    .0102028
       _cons |   21.15518   3.338498     6.34   0.000     14.61184    27.69851
------------------------------------------------------------------------------

------------------------------------------------------------------------------
                             |               Robust           
  Random-effects parameters  |   Estimate   std. err.     [95% conf. interval]
-----------------------------+------------------------------------------------
countrycode2: Identity       |
                   sd(_cons) |   .8102155   .2214056      .4742363    1.384224
-----------------------------+------------------------------------------------
                sd(Residual) |    1.42741   .1056294      1.234694    1.650207
------------------------------------------------------------------------------

. *** Stepwise Elimination ***

. use "/Users/josephthomas/Documents/Masters/Economic Data Analysis/EDA/OECDonly.dta"

. stepwise, pr(0.10): regress  y logGDP_pc share p edquan edqual

Wald test, begin with full model:
p = 0.2919 >= 0.1000, removing edquan

      Source |       SS           df       MS      Number of obs   =       250
-------------+----------------------------------   F(4, 245)       =     22.96
       Model |   245.88589         4  61.4714724   Prob > F        =    0.0000
    Residual |  655.910555       245  2.67718594   R-squared       =    0.2727
-------------+----------------------------------   Adj R-squared   =    0.2608
       Total |  901.796445       249  3.62167247   Root MSE        =    1.6362

------------------------------------------------------------------------------
           y | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
-------------+----------------------------------------------------------------
   logGDP_pc |  -2.003649   .2677371    -7.48   0.000    -2.531009   -1.476289
       share |   12.93818   1.994066     6.49   0.000     9.010482    16.86588
           p |  -.4057363   .1479349    -2.74   0.007    -.6971228   -.1143497
      edqual |   .0154095   .3141203     1.72   0.086    -.0777923    1.159649
       _cons |   17.92845   2.199807     8.15   0.000      13.5955    22.26139
------------------------------------------------------------------------------

. *** Random Slopes *** 

. xtmixed  y logGDP_pc share p edquan edqual||countrycode2: , vce(cluster countrycode2)

Performing EM optimisation: 

Performing gradient-based optimisation: 

Iteration 0:   log pseudolikelihood =   -464.588  
Iteration 1:   log pseudolikelihood =   -464.588  

Computing standard errors:

Mixed-effects regression                        Number of obs     =        250
Group variable: countrycode2                    Number of groups  =         37
                                                Obs per group:
                                                              min =          3
                                                              avg =        6.8
                                                              max =         10
                                                Wald chi2(5)      =     110.40
Log pseudolikelihood =   -464.588               Prob > chi2       =     0.0000

                          (Std. err. adjusted for 37 clusters in countrycode2)
------------------------------------------------------------------------------
             |               Robust
           y | Coefficient  std. err.      z    P>|z|     [95% conf. interval]
-------------+----------------------------------------------------------------
   logGDP_pc |  -2.553458   .4209848    -6.07   0.000    -3.378573   -1.728343
       share |   13.45207   2.812111     4.78   0.000     7.940437    18.96371
           p |  -.3316205   .1703759    -1.95   0.052    -.6655512    .0023102
      edquan |   .5756465   .5475373     1.05   0.293    -.4975069      1.6488
      edqual |   .0044095   .0029558     1.49   0.136    -.0013838    .0102028
       _cons |   21.15518   3.338498     6.34   0.000     14.61184    27.69851
------------------------------------------------------------------------------

------------------------------------------------------------------------------
                             |               Robust           
  Random-effects parameters  |   Estimate   std. err.     [95% conf. interval]
-----------------------------+------------------------------------------------
countrycode2: Identity       |
                   sd(_cons) |   .8102155   .2214056      .4742363    1.384224
-----------------------------+------------------------------------------------
                sd(Residual) |    1.42741   .1056294      1.234694    1.650207
------------------------------------------------------------------------------

. 
. *** AIC ***

. quietly xtmixed  y logGDP_pc share p edquan edqual||countrycode2: , vce(cluster countrycode2)

. estat ic

Akaike's information criterion and Bayesian information criterion

-----------------------------------------------------------------------------
       Model |          N   ll(null)  ll(model)      df        AIC        BIC
-------------+---------------------------------------------------------------
           . |        250          .   -464.588       8    945.176   973.3477
-----------------------------------------------------------------------------
Note: BIC uses N = number of observations. See [R] BIC note.

. log close
      name:  <EDA_log>
       log:  /Users/josephthomas/Documents/Masters/Economic Data Analysis/EDA/log.smcl
  log type:  smcl
 closed on:  13 Jan 2022, 12:04:17
---------------------------------------------------------------------------------------------------




