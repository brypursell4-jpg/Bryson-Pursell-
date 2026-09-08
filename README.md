# Multi-Device NFC Door Access Statistical Analysis
### Author: Bryson Pursell 
##### Stack: R, tidyverse, GGally, ggfortify, generalized linear models (logistic regression), ANOVA / Likelihood Ratio Testing

--- 

### Research Question
###### Miami University dorm doors frequently failed to unlock on the first NFC tap, requiring a re-scan. Is the failure driven by which device is used, who is scanning, or how the device is presented to the reader — or is the root cause something else in the system itself?

### Project Structure
DoorScannerProject/

├── Door.Rmd &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;# full R Markdown source (design, models, tests)

├── Door.md &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&nbsp; # knitted output with all model summaries

├── DoorLockSpecs.pdf &emsp;&ensp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp; # further info on scanner

├── Door_data.csv &emsp;&emsp;&emsp;&emsp;&ensp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; # primary 360-observation dataset

├── Sheet 2-Data w o Perpendiculars.csv &emsp;&emsp;&emsp; # secondary dataset excluding the "Perp" method

├── eda-1.png  &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&nbsp; # EDA pairwise plot

├── .gitignore

└── README.md

### Methodology
###### Problem scoping: Consulted an Honors Advisor and a Miami IT staff member to confirm the hardware/software stack (SCHLAGE aptiQ MT15 reader, CBORD CS Access software) and validate that a controlled experiment was feasible on real dorm hardware.

###### Design of experiments: Defined a 3-factor design: Method of tap (Flat, Perpendicular, Cattycornered, Hover), Device (iPhone 13 Pro, Galaxy S23 Ultra, Apple Watch 10), and Person (used as a blocking variable to absorb attempt-to-attempt noise across 3 people). 4 × 3 × 3 = 36 treatment combinations, each replicated 10 times, for 360 randomized observations collected within a 1-hour access window.

###### Baseline model: Fit a binomial logistic regression (Open ~ Person + Device + Method) in R. The full model was significant against the null model (deviance 95.78 on 7 df, p < 2.2e-16), explaining ~29% of deviance (pseudo-R²).

###### To confirm significance, variable selection (LRT) was explored.
> A likelihood ratio test on each predictor showed Device and Method were significant, but Person was not (p = 0.70), so Person was dropped from the model.

###### Follow-up EDA: Pairwise plots (GGally::ggpairs) flagged that the Samsung Galaxy and the Perpendicular tap method had visibly higher failure rates than other levels, prompting a closer look.

###### Sensitivity check: Refit without Perpendicular — removing the Perpendicular observations and refitting the model eliminated statistical significance entirely (deviance 7.23 on 6 df, p = 0.30). 
> Result: the original model's apparent significance was being driven almost entirely by one problematic method category, not by a relationship between device, person, or method and unlock success.

###### Conclusion: Failed to reject the null hypothesis in the robustness check. Thus, door failures are not reliably explained by who scans, what device they use, or how they present it. 

> This might instead be pointing to an intermittent issue in the reader/access-control system itself (the backend data even showed cases where the system logged a "successful" unlock that didn't physically occur).

### Key Skills Demonstrated

###### - Design of experiments (DOE): factorial design, blocking, randomization, replicate sizing

###### - Data collection under real-world constraints (limited access, 1-hour window)

###### - Generalized linear models (binomial logistic regression) in R

###### - Hypothesis testing: deviance-based whole-model tests, Likelihood Ratio Tests, ANOVA

###### - Model refinement and critical validation — testing whether a "significant" model actually holds up under a robustness check, not stopping at the first significant p-value

###### - Exploratory data analysis and pairwise visualization (GGally, ggfortify)

###### - R (tidyverse) for data cleaning, wrangling, and reproducible reporting (R Markdown)

### Possible Extensions

###### - Collect data across multiple doors/readers to test whether the issue is isolated to one unit or systemic

###### - Work with IT to pull backend CS Access logs over a longer window to quantify the "phantom success" logging issue directly

###### - Increase replicates per treatment over a longer data collection window to improve power, especially for the Perpendicular method subgroup

###### - Consider a mixed-effects model if data collection expands to multiple doors, treating door/reader as a random effect
