HAR-analysis
================
by Maurício Collaça
2017-01-10

Human Activity Recognition (HAR) using smartphones Data Set analysis
--------------------------------------------------------------------

An exciting area in Data Science is Wearable Computing - see for example this [article from Inside Activity Tracking](http://www.insideactivitytracking.com/data-science-activity-tracking-and-the-battle-for-the-worlds-top-sports-brand/). Companies like [Fitbit](https://www.fitbit.com/), [Nike](http://www.nike.com/), [Jawbone Up](https://jawbone.com/up), [Strava](https://www.strava.com/), and many others are racing to develop the most advanced algorithms to attract new users.

The purpose of this project is to collect, tidy, filter and produce some statistics of the HAR data set, producing a new tidy data set.

The data set used in the project is from the University of California Irvine (UCI) Machine Learning Repository: Human Activity Recognition Using Smartphones Data Set Version 1.0. The data set represent data collected from the embedded accelerometer and gyroscope of the Samsung Galaxy S smartphone. A full description is available at:

<http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones>

The data set for the project:

Mirror:
<https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip>
Original:
<http://archive.ics.uci.edu/ml/machine-learning-databases/00240/UCI%20HAR%20Dataset.zip>

About this README:
------------------

This document is an explanation about the Github repository [HAR-analysis](https://github.com/mauriciocramos/HAR-analysis) and its files developed for [John Hopkins University](https://www.jhu.edu/) [Data Science Specialization](https://www.coursera.org/specializations/jhu-data-science). .

The repository [HAR-analysis](https://github.com/mauriciocramos/HAR-analysis) includes the following files:
-----------------------------------------------------------------------------------------------------------

-   [README.md](https://github.com/mauriciocramos/HAR-analysis/blob/master/README.md) - This README file.

-   [CodeBook.md](https://github.com/mauriciocramos/HAR-analysis/blob/master/CodeBook.md) - A code book markdown file that indicate all the variables and summaries calculated, along with units, and any other relevant information about the output file [averages](https://github.com/mauriciocramos/HAR-analysis/blob/master/averages.txt)

-   [run\_analysis.R](https://github.com/mauriciocramos/HAR-analysis/blob/master/run_analysis.R) - R script to perform the data download, tidying and analysis required by the project.

-   [averages.txt](https://github.com/mauriciocramos/HAR-analysis/blob/master/averages.txt) - Tidy data set output from [run\_analysis.R](https://github.com/mauriciocramos/HAR-analysis/blob/master/run_analysis.R), containing the the average of each mean and standard deviation variable for each activity and each subject. Specific details about the data are found in its [CodeBook.md](https://github.com/mauriciocramos/HAR-analysis/blob/master/CodeBook.md).

-   [HAR-utils.R](https://github.com/mauriciocramos/HAR-analysis/blob/master/HAR-utils.R) - R script containing functions developed to encapsulate code and improve the reusability and the readability of the [run\_analysis.R](https://github.com/mauriciocramos/HAR-analysis/blob/master/run_analysis.R).

General information about the environment used
----------------------------------------------

Coffee machine:

    NESPRESSO Inissia, model D40

Hardware:

    Processor: Inter(R) Core(TM) i5-2300 CPU @ 2.80Ghz
    Number of Cores: 4
    Installed RAM: 8.00 GB  
    System type: x64-based processor

Operating System:

    Edition: Windows 10 Home  
    System type: 64-bit

R version:

    R version 3.3.2 (2016-10-31) -- "Sincere Pumpkin Patch"
    Copyright (C) 2016 The R Foundation for Statistical Computing
    Platform: x86_64-w64-mingw32/x64 (64-bit)

R Studio:

    Version 1.0.44

R packages used beyond the R defaults `{base}`, `{utils}`, `{stat}`:

`{dplyr}` (Windows binary r-release: [dplyr\_0.5.0](https://cran.r-project.org/bin/windows/contrib/3.3/dplyr_0.5.0.zip))

`{tidyr}` (Windows binary r-relase: [tidyr\_0.6.0](https://cran.r-project.org/bin/windows/contrib/3.3/tidyr_0.6.0.zip))

The [`run_analysis.R`](https://github.com/mauriciocramos/HAR-analysis/blob/master/run_analysis.R) script
--------------------------------------------------------------------------------------------------------

To run the [`run_analysis.R`](https://github.com/mauriciocramos/HAR-analysis/blob/master/run_analysis.R) script, save it in some working directory, open `R` or `RStudio`, go to that directory and run it with the following command:

`source("run_analysis.R")`

With the hardware and software configuration described earlier, it takes around 55 seconds to complete at the first time, which also includes the downloading and uncompressing tasks.

`system.time(source("run_analysis.R"))`

     user  system elapsed
    18.22    8.89   54.79

At the next times, you run it, there won't be any download and uncompressing task, then it takes around 14 seconds.

`system.time(source("run_analysis.R"))`

     user  system elapsed
    12.93    0.12   13.53

The [`run_analysis.R`](https://github.com/mauriciocramos/HAR-analysis/blob/master/run_analysis.R) script was developed to fullfill the following formal requirements of the project:

> 1.  Merges the training and the test sets to create one data set.
> 2.  Extracts only the measurements on the mean and standard deviation for each measurement.
> 3.  Uses descriptive activity names to name the activities in the data set
> 4.  Appropriately labels the data set with descriptive variable names.
> 5.  From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

Before explaining the solutions developed for the requirements 1 through 5, first it's explained the additional R packages used and the loading of the HAR data set files which consists of multiple directories and files.

The `{dplyr}` functions `group_by()` and `summarize()` are used with the function `mean() {base}` to fast and easily calculate the average of all values grouped by `subject`, `activity` and `variable`. It's also used the `{dplyr}` chain operator `"%>%"` to improve the code readability.

``` r
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

The `{tidyr}` function `gather()` is used to take multiple columns and collapses into key-value pairs, duplicating all other columns as needed.

``` r
library(tidyr)
```

The script downloads the zip file `Dataset.zip` into your working directory only once, unless you delete it letting the script donwload it again.

``` r
url <- 
'https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip'
destfile = 'Dataset.zip'
if(!file.exists(destfile)) { download.file(url, destfile) }
```

The zip file `Dataset.zip` contains a subdirectory structure and multiple files that the `run_analysis.R` script needs to dealth with. The following command displays the relevant files:

``` r
unzip(zipfile = "Dataset.zip", list = TRUE)[c(1:5,16:19,30:32),1]
```

    ##  [1] "UCI HAR Dataset/activity_labels.txt"    
    ##  [2] "UCI HAR Dataset/features.txt"           
    ##  [3] "UCI HAR Dataset/features_info.txt"      
    ##  [4] "UCI HAR Dataset/README.txt"             
    ##  [5] "UCI HAR Dataset/test/"                  
    ##  [6] "UCI HAR Dataset/test/subject_test.txt"  
    ##  [7] "UCI HAR Dataset/test/X_test.txt"        
    ##  [8] "UCI HAR Dataset/test/y_test.txt"        
    ##  [9] "UCI HAR Dataset/train/"                 
    ## [10] "UCI HAR Dataset/train/subject_train.txt"
    ## [11] "UCI HAR Dataset/train/X_train.txt"      
    ## [12] "UCI HAR Dataset/train/y_train.txt"

The script uncompress the zip file `Dataset.zip` into the the folder `UCI HAR Dataset` of the working directory only once, unless you delete this folder letting the script uncompress the zip file again:

``` r
if(!dir.exists("UCI HAR Dataset")) { unzip(destfile, setTimes = TRUE) }
```

The script loads three groups os data set files tied-up with the subdirectory structure:

-   General files at `UCI HAR Dataset/`
-   Training set filest at `UCI HAR Dataset/train/`
-   Testing set files at `UCI HAR Dataset/test/`

More details about these files are found in the [CodeBook](https://github.com/mauriciocramos/HAR-analysis/blob/master/CodeBook.md). All eight data set files area loaded into R `data.frame` objects.

As mentioned above, in order to encapsulate code and improve the reusability and the readability of the [`run_analysis.R`](https://github.com/mauriciocramos/HAR-analysis/blob/master/run_analysis.R) script, the file reading functions were written in a separate R script called [HAR-utils.R](https://github.com/mauriciocramos/HAR-analysis/blob/master/HAR-utils.R). Its reading is not necessary in order to understand the [`run_analysis.R`](https://github.com/mauriciocramos/HAR-analysis/blob/master/run_analysis.R) but you may find it instructive.

``` r
source("HAR-utils.R")
```

Loading the general files `features.txt` and `activity_labels.txt` from the directory `./UCI HAR Dataset/`:

``` r
features <- read_features()
```

Preview the feature names

``` r
head(features)
```

    ##   id              name
    ## 1  1 tBodyAcc-mean()-X
    ## 2  2 tBodyAcc-mean()-Y
    ## 3  3 tBodyAcc-mean()-Z
    ## 4  4  tBodyAcc-std()-X
    ## 5  5  tBodyAcc-std()-Y
    ## 6  6  tBodyAcc-std()-Z

``` r
activity_labels <- read_activity_labels()
```

Print the activity labels

``` r
activity_labels
```

    ##   id               name
    ## 1  1            WALKING
    ## 2  2   WALKING_UPSTAIRS
    ## 3  3 WALKING_DOWNSTAIRS
    ## 4  4            SITTING
    ## 5  5           STANDING
    ## 6  6             LAYING

Loading the trainning data set files `X_train.txt`, `y_train.txt` and `subject_train.txt` from the directory `./UCI HAR Dataset/train`

``` r
X_train <- read_X_train()
y_train <- read_y_train()
subject_train <- read_subject_train()
```

Loading the test data file `X_test.txt`, `y_test.txt` and `subject_test.txt` from the directory `./UCI HAR Dataset/test`

``` r
X_test <- read_X_test()
y_test <- read_y_test()
subject_test <- read_subject_test()
```

### 1. Merges the training and the test sets to create one data set.

In this requirement it's anticipated part of requirements \#3 and \#5. It's decided to merge the measurements (`X_train`, `X_test`) with their respectives subjects (`subject_train`, `subject_test`) and activities (`y_train`, `y_test`) prior to merge both results in one single data set. This approach is based on Hadley Wickham's concept "Tidying messy data sets" and "One type in multiple tables" at: <https://cran.r-project.org/web/packages/tidyr/vignettes/tidy-data.html>

-   Horizontally merge the three trainning-related data sets `subject_train`, `y_train`, `X_train`
-   Horizontally merge the three test-related data sets `subject_test`, `y_test`, `X_test`
-   Vertically merge the two previously merged data sets

Used `dplyr` functions `bind_cols()` for horizontal merging and `bind_rows()` for vertical merging:

``` r
dataset <- bind_rows(bind_cols(subject_train, y_train, X_train),
                     bind_cols(subject_test, y_test, X_test))
```

Number of rows after merging:

``` r
nrow(dataset)
```

    ## [1] 10299

Number of columns after merging:

``` r
length(dataset)
```

    ## [1] 563

Preview of the data set, omitting 556 columns from its middle:

``` r
head(dataset[,c(1:5,562:563)])
```

    ##   subject activity         1           2          3       560         561
    ## 1       1        5 0.2885845 -0.02029417 -0.1329051 0.1799406 -0.05862692
    ## 2       1        5 0.2784188 -0.01641057 -0.1235202 0.1802889 -0.05431672
    ## 3       1        5 0.2796531 -0.01946716 -0.1134617 0.1806373 -0.04911782
    ## 4       1        5 0.2791739 -0.02620065 -0.1232826 0.1819348 -0.04766318
    ## 5       1        5 0.2766288 -0.01656965 -0.1153619 0.1851512 -0.04389225
    ## 6       1        5 0.2771988 -0.01009785 -0.1051373 0.1848225 -0.04212638

As you can see, this data set can be tidier. In order to have a tidier (long) data set, it's necessary to collapse the 561 columns that are not variables into key-value pair, as mentioned in the rubric:

> "either long or wide form is acceptable”.

The following idiom is based on Hadley Wickham's concept "Tidying messy data sets" and "Column headers are values, not variable names" at: <https://cran.r-project.org/web/packages/tidyr/vignettes/tidy-data.html>

Used `tidyr` function `gather()` to take the right-most 561 columns and collapse them into key-value pairs `feature,value`, duplicating rows for columns `subject` and `activity` as needed.

``` r
dataset <- dataset %>%
    gather(key = feature,
           value = value,
           3:563,
           convert = TRUE)
```

Preview of the tidy data set:

``` r
str(dataset)
```

    ## 'data.frame':    5777739 obs. of  4 variables:
    ##  $ subject : int  1 1 1 1 1 1 1 1 1 1 ...
    ##  $ activity: int  5 5 5 5 5 5 5 5 5 5 ...
    ##  $ feature : int  1 1 1 1 1 1 1 1 1 1 ...
    ##  $ value   : num  0.289 0.278 0.28 0.279 0.277 ...

### 2. Extracts only the measurements on the mean and standard deviation for each measurement.

Initially, this requirement \#2 was not perfectly clear whether the extraction should affect the results of the next requirements. Following a good sense and the discussions in the regarding forums, It is assumed the extraction shall affect the existent tidy dataset.

According to the `features_info.txt` provided by Human Activity Recognition Using Smartphones Dataset Version 1.0, relevant features were found:

> The set of variables that were estimated from these signals are:
> mean(): Mean value
> std(): Standard deviation
> (...)
> meanFreq(): Weighted average of the frequency components to obtain a mean frequency
> (...)

According to the `features_info.txt`, there are other vectors that, although means, their respective features are actually `angles`:

> Additional vectors obtained by averaging the signals in a signal window sample. These are used on the angle() variable: gravityMean, tBodyAccMean, tBodyAccJerkMean, tBodyGyroMean, BodyGyroJerkMean.

> angle(tBodyAccMean,gravity)
> angle(tBodyAccJerkMean),gravityMean)
> angle(tBodyGyroMean,gravityMean)
> angle(tBodyGyroJerkMean,gravityMean)
> angle(X,gravityMean)
> angle(Y,gravityMean)
> angle(Z,gravityMean)

Thus, these `angle()` variables will not be extracted to fullfill the requirement \#2 because the atomic unit in the dataset is the `feature` and not an implicit `vector` that may composes a `feature`.

After all this reasoning and for the sake of the completeness, it is assumed that either `mean()` and `meanFreq()` and `std()` represent valid feature variables (substrings) to fullfill the requirement \#2.

Create a regular expression filter to subset the feature ids required, considering the substrings `mean()`, `meanFreq()` and `std()`

``` r
selectedFeatureIds <- features[grep("(mean|meanFreq|std)\\(\\)",features$name), 1]
```

Extract the mean and standard deviation variables

``` r
dataset <- dataset %>% filter(feature %in% selectedFeatureIds)
```

Preview of this filtered data set:

``` r
str(dataset)
```

    ## 'data.frame':    813621 obs. of  4 variables:
    ##  $ subject : int  1 1 1 1 1 1 1 1 1 1 ...
    ##  $ activity: int  5 5 5 5 5 5 5 5 5 5 ...
    ##  $ feature : int  1 1 1 1 1 1 1 1 1 1 ...
    ##  $ value   : num  0.289 0.278 0.28 0.279 0.277 ...

### 3. Uses descriptive activity names to name the activities in the data set

Used `dplyr` function `mutate()` to replace the activity id with the activity label from the `activity_labels` data set.

``` r
dataset <- mutate(dataset, activity = activity_labels$name[activity])
```

Preview of the named activities:

``` r
str(dataset)
```

    ## 'data.frame':    813621 obs. of  4 variables:
    ##  $ subject : int  1 1 1 1 1 1 1 1 1 1 ...
    ##  $ activity: chr  "STANDING" "STANDING" "STANDING" "STANDING" ...
    ##  $ feature : int  1 1 1 1 1 1 1 1 1 1 ...
    ##  $ value   : num  0.289 0.278 0.28 0.279 0.277 ...

### 4. Appropriately labels the data set with descriptive variable names.

Used `dplyr` function `mutate()` to replace the feature id with the feature name from the `features` data set.

``` r
dataset <- dataset %>%
    mutate(feature = features$name[feature]) %>%
    arrange(subject, activity, feature)
```

Preview of the named features:

``` r
str(dataset)
```

    ## 'data.frame':    813621 obs. of  4 variables:
    ##  $ subject : int  1 1 1 1 1 1 1 1 1 1 ...
    ##  $ activity: chr  "LAYING" "LAYING" "LAYING" "LAYING" ...
    ##  $ feature : chr  "fBodyAcc-mean()-X" "fBodyAcc-mean()-X" "fBodyAcc-mean()-X" "fBodyAcc-mean()-X" ...
    ##  $ value   : num  -0.926 -0.987 -0.994 -0.996 -0.997 ...

### 5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

Used `dplyr` functions `group_by()` and `summarize()` with the `base` function `mean()` to fast and easily calculate the average of all `values` grouped by `subject`, `activity` and `variable`.

``` r
averages <- dataset %>%
    group_by(subject, activity, feature) %>%
    summarize(average = mean(value)) %>%
    as.data.frame
```

Preview of the result:

``` r
str(averages)
```

    ## 'data.frame':    14220 obs. of  4 variables:
    ##  $ subject : int  1 1 1 1 1 1 1 1 1 1 ...
    ##  $ activity: chr  "LAYING" "LAYING" "LAYING" "LAYING" ...
    ##  $ feature : chr  "fBodyAcc-mean()-X" "fBodyAcc-mean()-Y" "fBodyAcc-mean()-Z" "fBodyAcc-meanFreq()-X" ...
    ##  $ average : num  -0.9391 -0.8671 -0.8827 -0.1588 0.0975 ...

The result is exported to the file [`averages.txt`](https://github.com/mauriciocramos/HAR-analysis/blob/master/averages.txt) in the current working directory

``` r
write.table(averages, file = "averages.txt", row.names = FALSE)
```

For the peer reviewing of the file [`averages.txt`](https://github.com/mauriciocramos/HAR-analysis/blob/master/averages.txt) one can load it with the following command

``` r
View(read.table("averages.txt", header = TRUE, stringsAsFactors = FALSE))
```

Acknowledgment
--------------

\[1\] Davide Anguita, Alessandro Ghio, Luca Oneto, Xavier Parra and Jorge L. Reyes-Ortiz. A Public Domain Dataset for Human Activity Recognition Using Smartphones. 21th European Symposium on Artificial Neural Networks, Computational Intelligence and Machine Learning, ESANN 2013. Bruges, Belgium 24-26 April 2013.

This dataset is distributed AS-IS and no responsibility implied or explicit can be addressed to the authors or their institutions for its use or misuse. Any commercial use is prohibited.
