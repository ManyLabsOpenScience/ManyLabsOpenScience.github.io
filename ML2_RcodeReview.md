# ManLabs2 R Code Review
[ManyLabs2](https://osf.io/8cd4r) (Corresponding coder: [Fred Hasselman](https://osf.io/ujgs6/))  
3 February 2016  


# Review of analysis strategy and results by `R` experts

------

> **NOTE:** Code review has completed.

* [To Code Review Instructions (this page)](https://ManyLabsOpenScience.github.io/ML2_RcodeReview)
* [To PoPS Proposal](https://ManyLabsOpenScience.github.io/ML2_PoPS_proposal)
* [To Data Cleaning Report](https://ManyLabsOpenScience.github.io/ML2_data_cleaning)
* [To Analysis-Specific Variable Functions](https://ManyLabsOpenScience.github.io/ML2_varfuns)

------


# Analysis Strategy

The ManyLabs2 data analysis strategy attempts to regard three principles that maximize research transparency:
  
 1. **Principle of Equality**: All data should be treated equally by a code. That is, the code should do its job generating results while at the same time being as naive as possible to the particular facts of the study being analysed. This will reduce any chances of bias with respect to the outcomes of a certain dataset or a particular study. If it is necessary to add study specific code, the second principle should be regarded.
 
 2. **Principle of Transparency**: All operations that are crucial for obtaining an analysis result should be available for inspection by anyone who wishes to do so. This should be possible without the help of the auhtors that generated the code. The operations concern the application of data filtering rules, computation of variables derived from original measurements, running an analysis and constructing graphs, tables and figures. If full transparency is not possible, the third principle should be regarded. 
 
 3. **Principle of Reproducibility**: The most basic requirement for analysis results is that they should be reproducable given the original code and the original data set. However, any new implementation of the same analysis strategy in a different context, or application of the code to a different dataset, e.g. a replication study, should not be problematic. That is, outcomes may differ between data sets, but this should not be attributable to any details of the code or the analysis strategy.  
    
    
## `R` as a parser of online code.    

The [pre-registered Manylabs2 protocol](https://osf.io/ejcfw/) describes a number of analyses per replication study that can be categorised as **Primary** (target replications per site), **Secondary** (additional analyses per site, e.g. on subgroups), and **Global** (analyses on the entire dataset). 

These *promised analyses* have all been implemented in `R` in a transparent way and this implementation is now ready for an independent review.    

## Implementation

Functions avalaible in an [`R` package on GitHub](https://github.com/ManyLabsOpenScience/manylabRs) ([PDF manual](https://github.com/ManyLabsOpenScience/manylabRs/manyLabRs.pdf)) extract information and instructions about each promised analysis a table that is openly accessible, the [masteRkey spreadsheet](https://docs.google.com/spreadsheets/d/1fqK3WHwFPMIjNVVvmxpMEjzUETftq_DmP5LzEhXxUHA/edit#gid=769239110).

Each row in the table represents an analysis, the columns contain specific information about the analysis:   

* Columns A through E are identifiers for study, analysis and slate.

* Column F and G contain `R` commands which will extract and label the columnms from the dataset needed for the analysis.

* Column H and I contain filter instructions for cases and subsamnples.

* Columns J through L contain information about the nature of the analysis (Global, Primary Secondary).

* Column M lists the name of a analysis specific *variable function* (`varfun.`) which in most cases just reorganises the variables specified in previous columns so they can be passed to the analysis code. In some cases these function perform specific calculations required by the original analyses.

* Columns N through S contain information about the statistiscal tests 

# Instructions for reviewers 

The analysis codes listed in the [masteRkey spreadsheet](https://docs.google.com/spreadsheets/d/1fqK3WHwFPMIjNVVvmxpMEjzUETftq_DmP5LzEhXxUHA/edit#gid=769239110) perfom analyses on the data and generate output, that much we know `:)`.

We would like to get your expert opinion on the following:

## **1. Does the `R` code in column O (`stat.test`) reflect the promised analyses in the protocol?**

+ In order to evaluate this you'll need to look at the analysis plan for a specific study listed in the [protocol](https://ManyLabsOpenScience.github.io/ML2_data_cleaning) and figure out whether the `R` code in rows of column O for that study represent all the tests that are described in the *analysis plan* of the proposal.   

+ In many cases this will be straightforward, without any need to actually run code, by looking at the way in which the variables are grouped and labelled, the way filters are applied and the settings used for the analysis, e.g. direction of the test (column P in the spreadsheet, `stat.params`).

+ In some cases you will need to inspect the contents of the `varfun` listed in column M. The code is available as an [HTML page](https://ManyLabsOpenScience.github.io/ML2_varfuns) and of course in a sourceable file on Github [ML2_variable_functions.R](https://github.com/ManyLabsOpenScience/manylabRs/R/) (to source it in `R`, run the code below).  
  

```r
require(devtools)
source_url("https://raw.githubusercontent.com/ManyLabsOpenScience/manylabRs/master/R/ML2_variable_functions.R")
```


## **2. Does the output correspond to what may be expected by the `R` code in column O?**

+ Think about the test-statistic, the parameters (df) and N.

+ The results files can be found in the dropbox associated with a [private project on OSF](https://osf.io/fprzu/): `/TestOutput`
    * `/AGGREGATE`: Contains results for primary and secondary analyses aggregated at the level of each `source`.
    * `/RAW.CASES`: Contains the raw case data including a filter variable `case.include` indicating which cases were included to calculate the results in `/AGGREGATE`.
    * `/GLOBAL`: Contains results for global analyses, disregarding `source`.
    * `/RESULTS.RDS`: Contains `R` datafiles of the results of global, primary and secondary analyses.
    * `/RESULTS.LOG`: Contains log files with information about omitted source files (e.g. due to sample size) and warnings when the effect size is extreme of close to 0. 

## **3. EXTRA: Data merging and application of exclusions and filters** 

+ The raw data files for each participating lab were merged, cleaned and prepared for analysis using `R` code available in the [Data Cleaning Report](https://ManyLabsOpenScience.github.io/ML2_data_cleaning).

+ The source files are in the dropbox [on OSF](https://osf.io/fprzu/) in `/Raw Data`, the output of the data cleaning script are in:
    * `RAW.DATA.PRIVATE` - Contains the output of the data merging and filtering rules described in 

# **Digging deeper: The `ManyLabRs` package**

There's an `R` package which contains all the `R` code, including the datafiles.

## Install the package

Several ways to install the package.

### Source from GitHub

Use the code below to install the `manylabRs` package directly from [GitHub](https://github.com/ManyLabsOpenScience/manylabRs).

```r
library(devtools)
install_github("ManyLabsOpenScience/manylabRs")
```

### Download tarball from GitHub

First [download the tarball](https://github.com/ManyLabsOpenScience/manylabRs/pkg/), then install the package locally through the RStudio package installer.

## Main function

The main function to inspect is `get.analyses()`.  

+ It will take an analysis number (`studies`) from the `masteRkey` sheet and an indication of whether the analysis is `global`, `primary`, `secondary`. 
+ Have a look at [`saveConsole.R`](https://raw.githubusercontent.com/ManyLabsOpenScience/manylabRs/master/R/saveConsole.R) and the `testScript()` function. This will create a log file of the output.
**Note:** These scripts assume you are on a Mac and you copied the dropbox folder to your harddrive.

### Main algorithm

+ Get information from `masteRkey` on the analyses to run: `get.info()`
+ Get a data filter based on exclusion criteria: `get.chain()`
+ Select the appropriate variables: `get.sourceData()`
+ Apply the analysis-specific variable function: `varfun.ABC.#()`
+ Apply the analysis listed in column `stat.test` to the data
+ Organise the output `get.desriptives()`
+ Calculate confidence intervals for effect sizes `any2any()`
+ Return the ouput







