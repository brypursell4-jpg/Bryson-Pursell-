**TLDR: Doors would often not unlock after being scanned, sought to
discover if the reason was device, individual person, or method of
tapping NFC chip. After organizing random sampling and running
observations, analysis discovered that these variables do not
significantly predict whether the door will unlock or not.**

**Introduction: The concept for this research began when my roommate and
I kept having to re-scan our Miami ID in order to enter our dorm room,
when the process is supposed to involve only one quick tap. After
speaking to other students around campus, we found that many people were
having the same problems, and so I decided to run a statistical analysis
to further examine this issue. Upon further discussion with a professor,
I landed upon a Binary Response Generalized Linear Model. And thus, the
project was off!**

**Beginning Research: To start, I wanted to make sure an experiment of
this type was reasonable. To do so, I reached out to Jonathan James, who
is an Honors Advisor here at Miami. We had a good conversation about the
idea, and he directed me to Angeline Poling, an IT worker at Miami who
was versed in the tap card readers used to scan into the rooms. After
discussion, I learned that the school uses CBORD CS Access for the
software end of this process. For hardware, the SCHLAGE aptiQ MT15
Multi-Technology Single Gang Reader. These card readers have a continous
powering feature that ensures the device does not go into a short-term
sleep mode. Now, onto the analysis.**

**Design of Experiments: When figuring out the DOE, the final research
question was as follows: Is there a relationship between unlock method,
device type, and successful door unlocks? For this research, our null
hypothesis was that the method of unlocking and number of successful
door unlocks have no relationship. The alternative hypothesis was that
the method of unlocking and number of successful unlocks do have a
relationship. The experimental unit for the experiment was the one door
to our dorm (did not have the facilities to access other doors), and as
such device + person was limited to those that could access our door
(they are coded to individuals). The three factors for such an
experiment are method of unlocking the door (Flat on the surface, top of
device perpendicular to surface, “Cattycornered” / only corner of device
touching scanner, and hovering near the door), device used to unlock
(iPhone 13 Pro, Samsung Galaxy S23 Ultra, and Apple Watch 10), and the
people unlocking the door (Myself, Logan (roommate), and Ollie (Friend
willing to help)). Given these specifications of 4 method, 3 devices,
and 3 people (With people acting as a block variable to account for
possible noise from attempt inconsistencies), we have 4x3x3 = 36
treatments. As such, we must seek 10-20 replicates to ensure data is
relatively accurate. Again related to facilities, we were not permitted
more than 1 hour to gather our data.**

**Running Experiment: Attempting to ensure a randomized design, we
randomized the person, device, and method for each observation. Using
this logic, we gathered 360 observations, using the 36 treatments
replicated 10 times.**

**Final Results: Spoiler Warning! After running the experiment I had
collected my own data, but wanted confirmation of validity. Using my
contact with Joyce Looby, I obtained the results from the 1 hour of data
collection on the back-end. This data came directly from the CS Access
system, and confirmed that there are times where the system records a
successful unlock but one does not occur. Below, you will find the full
analysis conducted in R Studio. In short, the model appeared promising
to start, but a later discovery revealed that it is not valid. There
still remains an issue with the door unlock system, but it is not
directly related to the Person, Device, or method used to unlock the
door.**

------------------------------------------------------------------------

**Initial glimpse of data:**

    ## Rows: 360
    ## Columns: 6
    ## $ X.     <int> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, …
    ## $ Person <fct> Ollie, Logan, Ollie, Ollie, Bryson, Logan, Logan, Ollie, Ollie,…
    ## $ Device <chr> "Watch", "Watch", "Galaxy", "Galaxy", "Watch", "Galaxy", "iPhon…
    ## $ Method <chr> "Flat", "Hover", "Perp", "Cat", "Hover", "Perp", "Cat", "Perp",…
    ## $ Result <int> 1, 0, 0, 0, 1, 0, 1, 0, 1, 0, 0, 0, 1, 0, 1, 1, 1, 0, 0, 1, 1, …
    ## $ Open   <lgl> TRUE, FALSE, FALSE, FALSE, TRUE, FALSE, TRUE, FALSE, TRUE, FALS…

------------------------------------------------------------------------

**Summary of Fitted Model:**

    ## 
    ## Call:
    ## glm(formula = Open ~ Person + Device + Method, family = binomial(link = "logit"), 
    ##     data = door_data)
    ## 
    ## Coefficients:
    ##              Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)   0.93477    0.42407   2.204   0.0275 *  
    ## PersonLogan   0.08659    0.41626   0.208   0.8352    
    ## PersonOllie  -0.24286    0.40317  -0.602   0.5469    
    ## DeviceiPhone  2.06175    0.43970   4.689 2.75e-06 ***
    ## DeviceWatch   1.94833    0.42983   4.533 5.82e-06 ***
    ## MethodFlat    1.29126    0.61840   2.088   0.0368 *  
    ## MethodHover   0.83928    0.54705   1.534   0.1250    
    ## MethodPerp   -1.99277    0.42626  -4.675 2.94e-06 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 330.76  on 359  degrees of freedom
    ## Residual deviance: 234.98  on 352  degrees of freedom
    ## AIC: 250.98
    ## 
    ## Number of Fisher Scoring iterations: 6

**Total deviance explained by model is 330.76-234.98 = 95.78 (null -
residual). This value is large enough that we shall proceed with
analysis.**

------------------------------------------------------------------------

**Whole Model Test:**

    ## Analysis of Deviance Table
    ## 
    ## Model 1: Open ~ 1
    ## Model 2: Open ~ Person + Device + Method
    ##   Resid. Df Resid. Dev Df Deviance  Pr(>Chi)    
    ## 1       359     330.76                          
    ## 2       352     234.98  7   95.779 < 2.2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

**Chi-square value of 95.779 on 7 degrees of freedom (p-value =
2.2e-16). Model including Method, Device, and Person is significantly
different from the one without these variables. Pesudo R-squared (1 -
234.98/330.76)**\***100 = 28.96% of the deviance is explained by the
model. This deviance result shows us that while the model is significant
against an empty model, it might not be the best predictor that can be
found.**

------------------------------------------------------------------------

**Lkelihood Ratio Test (LRT)**

    ## Single term deletions
    ## 
    ## Model:
    ## Open ~ Person + Device + Method
    ##        Df Deviance    AIC    LRT  Pr(>Chi)    
    ## <none>      234.98 250.98                     
    ## Person  2   235.70 247.70  0.713       0.7    
    ## Device  2   270.54 282.54 35.552 1.905e-08 ***
    ## Method  3   302.34 312.34 67.359 1.569e-14 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

**Method and Device seem to be significant, but Person does not. It is
possible that this being the block variable is having an effect. We must
now compare the full model to one without the Person variable to find
out.**

------------------------------------------------------------------------

**Reduced model - Person**

    ## Analysis of Deviance Table
    ## 
    ## Model 1: Open ~ Method + Device
    ## Model 2: Open ~ Person + Device + Method
    ##   Resid. Df Resid. Dev Df Deviance Pr(>Chi)
    ## 1       354     235.70                     
    ## 2       352     234.98  2  0.71325      0.7

**Having a Chi-square of .71325 on 2 degrees of freedom (p-value of .7),
we do not have evidence that the inclusion of People in the model has a
significant effect on the response.**

------------------------------------------------------------------------

**Exploratory Data Analysis (EDA)**
![](Door_files/figure-markdown_strict/eda-1.png)

**After discovering that a predictor was not significant, a check-up on
other variables is necessary** **Analyzing this EDA plot reveals that
the Samsung Galaxy and Perpendicular method seem to have a larger number
of failures than other predictors. This must now be explored.**

------------------------------------------------------------------------

##### No Perpendicular Model

**Glimpse of data w/o Perp method:**

    ## Rows: 360
    ## Columns: 6
    ## $ X.     <int> 1, 2, NA, 4, 5, NA, 7, NA, 9, NA, 11, 12, 13, NA, 15, 16, NA, 1…
    ## $ Person <chr> "Ollie", "Logan", "", "Ollie", "Bryson", "", "Logan", "", "Olli…
    ## $ Device <chr> "Watch", "Watch", "", "Galaxy", "Watch", "", "iPhone", "", "Gal…
    ## $ Method <chr> "Flat", "Hover", "", "Cat", "Hover", "", "Cat", "", "Hover", ""…
    ## $ Result <int> 1, 0, NA, 0, 1, NA, 1, NA, 1, NA, 0, 0, 1, NA, 1, 1, NA, 0, NA,…
    ## $ Open   <lgl> TRUE, FALSE, NA, FALSE, TRUE, NA, TRUE, NA, TRUE, NA, FALSE, FA…

------------------------------------------------------------------------

**No-Perp Model Summary:**

    ## 
    ## Call:
    ## glm(formula = Open ~ Person + Device + Method, family = binomial(link = "logit"), 
    ##     data = noperp)
    ## 
    ## Coefficients:
    ##              Estimate Std. Error z value Pr(>|z|)   
    ## (Intercept)    1.7377     0.5417   3.208  0.00134 **
    ## PersonLogan   -0.1701     0.5843  -0.291  0.77095   
    ## PersonOllie   -0.4538     0.5568  -0.815  0.41512   
    ## DeviceiPhone   0.5732     0.5459   1.050  0.29369   
    ## DeviceWatch    0.5732     0.5459   1.050  0.29369   
    ## MethodFlat     1.2070     0.6005   2.010  0.04443 * 
    ## MethodHover    0.7753     0.5268   1.472  0.14111   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 152.48  on 269  degrees of freedom
    ## Residual deviance: 145.25  on 263  degrees of freedom
    ##   (90 observations deleted due to missingness)
    ## AIC: 159.25
    ## 
    ## Number of Fisher Scoring iterations: 5

**The total deviance explained by this model is 152.48 - 145.25 = 7.23.
Unfortunately, this finding reveals that with the Perp observations
removed, the model is not statistically significant from the null model.
Thus, our end result iw failure to reject the null hypothesis.**

------------------------------------------------------------------------

**ANOVA to Fully Confirm Insignificance:**

    ## Analysis of Deviance Table
    ## 
    ## Model 1: Open ~ 1
    ## Model 2: Open ~ Person + Device + Method
    ##   Resid. Df Resid. Dev Df Deviance Pr(>Chi)
    ## 1       269     152.48                     
    ## 2       263     145.25  6   7.2346   0.2997

**Chi-square value of 7.2346 on 6 degrees of freedom (p-value = .2997
&gt; alpha = .05). This further proves that the model does not predict
the response.**

****

**While the model did not achieve the original goal of the experiment,
it does still highlight that there is an issue of small extent in the
system. Further analysis may reveal the cause of this issue, but not
with this line of reasoning.**
