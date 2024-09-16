# An introduction to the `tidyverse`
The [`tidyverse`](https://www.tidyverse.org/) is a set of packages written by RStudio to facilitate the following common tasks in R. The most notable packages of the ecosystem are used to: 
- reading datasets (`readr`)
- manipulate datasets (`dplyr`)
- work with textual data (`stringr`)
- work with time data ( `lubridate`)
- work with categorical data (`forcats`)
- create graphs (`ggplot2`)
- programming with dataframes (`purrr`)

The `tidyverse` relies on the concept of *tidy* data:
- each variable has its own column
- each observation has its own row
- a value, corresponding to an observation for a variable, is in a single cell

To chain operations we use the `pipe` (`%>%`) of the package `magrittr`. Here is an example: 
```r
library(dplyr)
library(doremifasol)

telechargerDonnees("BPE_ENS") %>%
  as_tibble() %>%
  filter(TYPEQU == "B316") %>% 
  group_by(DEP) %>% 
  summarise(nombre_station_serv = sum(NB_EQUIP, na.rm = TRUE))
```

# A note on data formats
There are very different ways and format to store data. In most cases you will encounter you will want to create a dataframe with the data. Here are some common format you will often encounter: 

### CSV (comma-separated values)
- Format with delimitated columns
- standard: `,` delimitates observations, `.` is used for decimals
- european variant: `;` delimitates observations, `,` is used for decimals

If you open it with a notepad .csv files look like this: 
```
DEP,REG,CHEFLIEU,TNCC,NCC,NCCENR,LIBELLE
01,84,01053,5,AIN,Ain,Ain
02,32,02408,5,AISNE,Aisne,Aisne
03,84,03190,5,ALLIER,Allier,Allier
04,93,04070,4,ALPES DE HAUTE PROVENCE,Alpes-de-Haute-Provence,Alpes-de-Haute-Provence
05,93,05061,4,HAUTES ALPES,Hautes-Alpes,Hautes-Alpes
06,93,06088,4,ALPES MARITIMES,Alpes-Maritimes,Alpes-Maritimes
```
You can use `read_csv`of the package `readr`to read CSVs.

>[!NOTE]
>For european format csvs you can use `read_csv2`. For more complex delimitators use `read_delim`.

### JSON

The format of the *web*, especially of APIs[^API].

[^API]: We don't have time to cover APIs. Essentially they can be seen as counters where you ask for data from an entity.

It looks like this: 
```
[
  {"DEP": "01", "REG": "84", "CHEFLIEU": "01053", "TNCC": "5", "NCC": "AIN", "NCCENR": "Ain", "LIBELLE": "Ain"},
  {"DEP": "02", "REG": "32", "CHEFLIEU": "02408", "TNCC": "5", "NCC": "AISNE", "NCCENR": "Aisne", "LIBELLE": "Aisne"},
  {"DEP": "03", "REG": "84", "CHEFLIEU": "03190", "TNCC": "5", "NCC": "ALLIER", "NCCENR": "Allier", "LIBELLE": "Allier"},
  {"DEP": "04", "REG": "93", "CHEFLIEU": "04070", "TNCC": "4", "NCC": "ALPES DE HAUTE PROVENCE", "NCCENR": "Alpes-de-Haute-Provence", "LIBELLE": "Alpes-de-Haute-Provence"},
  {"DEP": "05", "REG": "93", "CHEFLIEU": "05061", "TNCC": "4", "NCC": "HAUTES ALPES", "NCCENR": "Hautes-Alpes", "LIBELLE": "Hautes-Alpes"},
  {"DEP": "06", "REG": "93", "CHEFLIEU": "06088", "TNCC": "4", "NCC": "ALPES MARITIMES", "NCCENR": "Alpes-Maritimes", "LIBELLE": "Alpes-Maritimes"}
]
```

JSON files have a hierarchical structure. It is not always easy to flatten them into a dataframe. To do it you can use the library `jsonlite` and its function `fromJSON`.

### Excel

They are common but should be avoided when possible. It's a proprietary format that mixes both the data and its formatting. 


# This tutorial
Let us now learn how we can import and manipulate data with R.

In this chapter we will make us of the two following packages of the *tidyverse*:
- `readr` to import data ;
- `dplyr` to manipulate data.

>[!note] 
>The `tidyverse` is not the only ecosystem to work with data. It is however the first one you should learn about. Complementary ecosystems (`data.table`, `arrow`, `duckdb`) all require that you already master the tidyverse and identify its limits.

In this chapter we will use two data sets: 
- greenhouse gas emissions estimated at the commune level in France. The data was produced by the ADEME and is accessible on  [data.gouv](https://www.data.gouv.fr/fr/datasets/inventaire-de-gaz-a-effet-de-serre-territorialise/#_). You can access it directly in R with this [this url](https://koumoul.com/s/data-fair/api/v1/datasets/igt-pouvoir-de-rechauffement-global/convert) (this will be our first exercise)[^notedownload].
- Ideally, we would use data from INSEE (French national statistical agency) but they require a bit of cleaning. To go faster we will use both the packages [`doremifasol`](https://github.com/InseeFrLab/DoReMIFaSol) and [`insee`](https://github.com/pyr-opendatafr/R-Insee-Data) to simplify the INSEE data access.

[^notedownload]: `readr` allows importing data directly from an url. 

## Preliminary step: _packages_ installation

R is an open-source language. Anyone can create code and packages to increase the language functionalities. A package (also called a library) is a coherent set of functionalities. 

As the team managing the R language does not aim to integrate all libraries into the base language (it must remain, as its name suggests, basic), there are community spaces where people can share their _packages_. In the R ecosystem, the two main ones[^bioconductor] are:

- `CRAN` (_Comprehensive R Archive Network_): the official and historical repository of R libraries. To install a _package_ stored in this space, you use `install.package`;
- `Github`: the social network for open-source code. To install a _package_ stored in this space, you use `remotes::install_github`[^remote].

[^bioconductor]: There is also `bioconductor` but since it is mostly oriented towards biostatistics (one of the academic communities that adopted R early on), we don’t really use it. [↩](#user-content-fnref-bioconductor)
    
[^remote]: `remotes::install_github` means using the `install_github` function from the `remotes` _package_. In other words, you need a _package_ to install other _packages_ 🤯. This is because `Github` did not exist when R was created (1990s) and this functionality has not been added since. [↩](#user-content-fnref-remotes)


>[!note]
>
>In general, more mature _packages_ are found on CRAN. `Github` is more of a mixed bag where you can find hidden gems alongside content of more variable quality.
>
>Some of the _packages_ we will see are not on CRAN because the validation process required to submit a _package_ there is quite burdensome and exhausting for volunteer developers, who are usually unpaid for this work and often do it during late hours.

To install a package available on CRAN, for instance [`dplyr`](https://dplyr.tidyverse.org/) you can do; 

```r
install.packages("dplyr")
```

To install a package from `Github` for instance [`doremifasol`](https://github.com/InseeFrLab/DoReMIFaSol) which is available on the account `InseeFrLab`:

```r
remotes::install_github('inseefrlab/DoReMIFaSol')
```

Here is the set of packages needed to run this tutorial:

```r
install.packages(c("readr","dplyr", "tidyr", "ggplot2", "remotes"))
remotes::install_github('inseefrlab/DoReMIFaSol')
```

Once you have installed packages (you only need to do it once), you need to call them in your scripts to have them available in your R session. Let us already call some packages: 

```r
library(dplyr)
library(tidyr)
library(stringr)
```

# Data import 

## Import a csv file

First let us import the ADEME data using [`readr`](https://readr.tidyverse.org/). 

### Exercise 1: Read a CSV with `readr` and examine the data

Here is the URL where the data is available:

```r
url <- "https://koumoul.com/s/data-fair/api/v1/datasets/igt-pouvoir-de-rechauffement-global/convert"
```

1. Use the `readr` _package_ to import this data. Name this object `emissions`.
2. Display the first few rows using `head`
3. Display the class of `emissions`. Do you now understand why this object is a bit different from a basic dataframe?
4. Display the first 10 values, the last 15 values, and a random sample of 10 values using the [appropriate function from the `dplyr` package](https://dplyr.tidyverse.org/reference/slice.html).

>[!note]
>Out of a lack of imagination, we are often tempted to name our main _dataframe_ `df` or `data`. This is often a bad idea because the name is not very informative when you revisit the code a few weeks later. Self-documentation, an approach where the code explains itself, is a good practice, so it's recommended to give a simple yet effective name to understand the nature of the _dataset_ in question.
>

<details>
  <summary> Exercise 1: Solution</summary>

```r
library(readr)
emissions <- read_csv(url)
head(emissions)
class(emissions)
slice_head(emissions, n=10)
slice_tail(emissions, n=15)
slice_sample(emissions, n=10)
```
</details>

## First data manipulations

The main functions of `dplyr` (the _verbs_ of the `dplyr` grammar) are as follows:

- `select()`: select variables by their name;
- `rename()`: rename variables;
- `filter()`: select observations based on one or more conditions;
- `arrange()`: sort the table based on one or more variables;
- `mutate()`: add variables that are functions of other variables;
- `summarise()`: calculate a statistic from the data;
- `group_by()`: perform operations by group.

The cheatsheet on this [page](https://dplyr.tidyverse.org/) is very useful as it illustrates these different functions. It is recommended to consult it regularly (click on the image to zoom 🔎).

### Exercise 2: Exploring `dplyr` verbs for data manipulation

First, let’s get familiar with operations on columns. Each of these questions makes us of one of the verbs above.

1. Create a _dataframe_ `emissions_copy` keeping only the columns `INSEE commune`, `Commune`, `Autres transports`, and `Autres transports international`. Note: Use backticks for column names with spaces.

2. Since the variable names are not very practical, rename them as follows:
    + `INSEE commune` $\to$ `code_insee`
    +  `Autres transports` → `transports`
    + `Autres transports international` $\to$ `transports_international`

3. To simplify, replace missing values (`NA`) with 0. Use the function [`replace_na`](https://tidyr.tidyverse.org/reference/replace_na.html) from the _tidyr_ package, in combination with `mutate`, to transform the missing values into 0.

4. In the same code sequence, create the following variables:
 	- `dep`: the department. This can be created using the first two characters of `code_insee` with the `str_sub` function from the _stringr_ package.
 	- `transports_total`: the total emissions from the transport sector (sum of the two variables).

5. Sort the data from the largest polluter to the smallest, then sort the data by department (from 01 to 95) while still displaying the largest polluters first.

6. Calculate the total emissions by department.

<details>
  <summary>Exercise 2: solution</summary>
  
  ```r
#Question 1
emissions_copy <- emissions %>%
	select(`INSEE commune`, `Commune`, `Autres transports`, `Autres transports international`)

#Question 2
emissions_copy <- emissions_copy %>%
	rename(
	code_insee = `INSEE commune`,
	transports = `Autres transports`,
	transports_international = `Autres transports international`
	)

 #Question 3 
 emissions_copy <- emissions_copy %>%
   mutate(
     transports = replace_na(transports),
     transports_international = replace_na(transports_international)
     )
 
 #Question 4
 emissions_copy <- emissions_copy %>%
   mutate(
     dep = str_sub(code_insee, 1, 2),
     transports_total = transports + transports_international
     )
 
 # Question 5
 emissions_copy %>%
   arrange(desc(transports_total))
 
 emissions_copy %>%
   arrange(dep, desc(transports_total))
 
 # Question 6 
 emissions_copy %>%
   group_by(dep) %>%
   summarise(sum(transports_total))
```
</details>

## Importing data from INSEE

For our communal information, we will use one of the most popular sources from INSEE: the [`Filosofi`](https://www.insee.fr/en/metadonnees/source/serie/s1172) data. To make retrieving this data easier, we will use the community _package_ `doremifasol`:

```r
library(doremifasol)
library(tibble)
filosofi <- as_tibble(
  telechargerDonnees("FILOSOFI_COM", date = 2016)
)
head(filosofi)
```

> [!NOTE]
> The `as_tibble` function helps us transform the base _dataframe_ (since `doremifasol` does not make assumptions about the data manipulation ecosystem you're using) into a _dataframe_ suitable for use with `tidyverse`.

# Data Cleaning

### Exercise 3

Since `readr` or `doremifasol` automatically handled the data import, we’ll now perform a quick quality check:
 
 1. Display the column names of our _dataframes_ `emissions` and `filosofi`. What are the common columns? Use the `intersect` function to understand the issue.
 2. Observe the structure of our datasets (the types of the columns). Are the default types appropriate?
 
 Next, we check the dimensions of the `DataFrames` and the structure of some key variables. In particular, the fundamental variables for linking our data are the communal variables. Here, we have two geographical variables: a commune code and a commune name. We’ll check if they are well-suited for statistical analysis.
 
 3. Check the dimensions of the _dataframes_
 4. Check the number of unique values of the geographical variables in each dataset. Do the results appear consistent? Note: you can use the function `n_distinct()` here on top of the common verbs of the `tidyverse`.
 5. In `filosofi`, identify the commune names that correspond to multiple commune codes and select their codes. In other words, identify the `CODGEO` where there are duplicates in `LIBGEO` and store them in a dataframe called `duplicates`. Note: this question is difficult and there are several ways to answer it. 
 
 We’ll focus temporarily on the observations where the label corresponds to more than two different commune codes.
 
 6. Examine these observations in `filosofi`. To make this clearer, reorder the obtained dataset alphabetically. Note: Here you need to find a clever way to select the correct rows. The `%in%` operator can prove useful here. 
 7. Determine the average size (variable number of people: `NBPERSMENFISC16`) and some descriptive statistics of these data. Compare this with the same statistics for the data where there are no duplicates.
 8. Now, among large cities (more than 100,000 inhabitants), how many of them have a duplicate? What proportion of large cities does this represent?
 9. In `filosofi`, check the cities where the label is equal to Montreuil. Also check those that contain the term 'Saint-Denis'.

<details>
  <summary>Exercise 3: solution</summary>

  ```r
 #Question 1
 colnames(emissions)
 intersect(colnames(filosofi), colnames(emissions))
 
 #Question 2
 str(filosofi)
 str(emissions)
 
 #Question 3
 dim(filosofi)
 dim(emissions)
 
 #Question 4
 emissions %>%
   select('INSEE commune', 'Commune') %>%
   summarize(Unique_Count = n_distinct(Commune))
 
 filosofi %>%
   select('CODGEO', 'LIBGEO') %>%
   summarize(Unique_Count = n_distinct(LIBGEO))
 
 # Question 5
 duplicates <- filosofi %>%
   group_by(LIBGEO) %>%
   summarize(Count = n()) %>%
   select(LIBGEO, Count) %>%
   filter(Count > 1)
 
 # Question 6
 filosofi %>%
   filter(LIBGEO %in% duplicates$LIBGEO) %>%
   arrange(LIBGEO)
 
 # Question 7
 filosofi %>%
   filter(LIBGEO %in% duplicates$LIBGEO) %>%
   summarize(Stats = mean(NBPERSMENFISC16, na.rm = TRUE))
 
 # Calculate summary statistics for 'NBPERSMENFISC16' for rows where 'LIBGEO' is not in 'duplicates$LIBGEO'
 filosofi %>%
   filter(!(LIBGEO %in% duplicates$LIBGEO)) %>%
   summarize(Stats = mean(NBPERSMENFISC16, na.rm = TRUE))
   
 #Question 8
 filosofi_big <- filosofi %>%
   filter(NBPERSMENFISC16 > 100000) %>%
   mutate(probleme = LIBGEO %in% duplicates$LIBGEO)
 
 # Proportion of problematic cities
 mean_probleme <- filosofi_big %>%
   summarize(mean(probleme))
 
 # Filter rows where 'probleme' is TRUE
 df_probleme <- filosofi_big %>%
   filter(probleme)
   
 # Question 9
 filosofi %>%
   filter(LIBGEO == 'Montreuil')
 
 # Question 10
 filosofi %>%
   filter(grepl('Saint-Denis', LIBGEO)) %>%
   head(10)
 ```
</details>



This exercise reassures us that the duplicated labels are in fact identical commune names that are not in the same department. Therefore, these are not duplicated observations. We will rely on the commune codes, which are unique.

### Exercise 4

 We will start exploring the data. This involves some initial data cleaning.
 
 1. Rename the variable `INSEE commune` to `code_insee`. Note: Use backticks to call a column which name contains a space.
 2. The first two digits of the commune codes are the department number. Create a department variable `dep` in both `emissions` and `filosofi` using the `str_sub` function from the `stringr` package[^stringr].
 
 Let's begin calculating our first descriptive statistics.
 
 3. Calculate the total emissions by sector for each department. Log these results into an object called `emissions_log`. Note: Here you should use [`across`](https://dplyr.tidyverse.org/reference/across.html).
 
 4. Starting from the `emissions` dataset, calculate the total emissions by department and produce a list of the top 10 CO2 emitters and the 5 departments with the lowest emissions. Without performing a *merge*, examine the characteristics of these departments (population and standard of living). Note: To compute sums per row you can make use of the following code:
```r
mutate(total = rowSums(pick(where(is.numeric)), na.rm = TRUE))
```
 
 [^stringr]: The limited capabilities of the base language for text manipulation quickly become restrictive. Thus, we often switch to `stringr`, even if that is not the main focus of the chapter.

<details>
  <summary>Exercise 4: solution</summary>

   ```r
  #Question 1
  library(stringr)
  
  emissions <- emissions %>%
   rename('code_insee' = `INSEE commune`)
  
  #Question 2
  emissions <- emissions %>%
   mutate(dep = str_sub(code_insee, start = 1, end = 2))
  filosofi <- filosofi %>%
   mutate(dep = str_sub(CODGEO, start = 1, end = 2))
  
  #Question 3
  emissions_log <- emissions %>%
     group_by(dep) %>%
     summarise(across(where(is.numeric), sum, na.rm = TRUE)) %>%
     mutate(across(where(is.numeric), log))
  
  #Question 4
  ## Total emissions by department
  emissions_dep <- emissions %>%
	mutate(total = rowSums(pick(where(is.numeric)), na.rm = TRUE)) %>%
	group_by(dep) %>%
	summarise(total = sum(total))

################
## Comment: the function here 'rowSums' is a base R function. We could argue that we should not mix base R functions with dplyr ones.
## Here are two variants that do the same thing but stay in the tidyverse ecosystem:
## - using `purrr`:
mutate(total = reduce(across(where(is.numeric)), `+`, .init = 0))

## using only `dplyr`:
emissions_dep <- emissions %>%
  rowwise() %>%
  mutate(total = sum(c_across(where(is.numeric)), na.rm = TRUE)) %>%
  ungroup()
##################

  gros_emetteurs <- emissions_dep %>%
	   arrange(desc(total)) %>%
	   head(10)

  petits_emetteurs <- emissions_dep %>%
	   arrange(total) %>%
	   head(5)
  
  ## Characteristics of these departments in filosofi
  gros_emetteurs_filosofi <- filosofi %>%
	   filter(dep %in% gros_emetteurs$dep) %>%
	   group_by(dep) %>%
	   summarise(across(c('NBPERSMENFISC16','MED16'), \(x) mean(x, na.rm = TRUE)))
  
  head(gros_emetteurs_filosofi)

## The solution above is a fancy solution that makes use of a lambda function \(x). The simpler solution also works:
  gros_emetteurs_filosofi <- filosofi %>%
	   filter(dep %in% gros_emetteurs$dep) %>%
	   group_by(dep) %>%
	   summarise(mean_pop = mean(NBPERSMENFISC16, na.rm = TRUE),
		     mean_med = mean(MED16, na.rm = TRUE))

```
</details>




# Restructuring Data

There are generally two types of data formats:

* __Wide format__: Data includes repeated observations for the same individual (or group) in different columns.
* __Long format__: Data includes repeated observations for the same individual in different rows, with a column that distinguishes the observation levels.

An example of the distinction between the two can be found in Hadley Wickham's reference book, *R for Data Science*:

![Long format (left) and wide format (right) (source: *R for Data Science*)](https://d33wubrfki0l68.cloudfront.net/3aea19108d39606bbe49981acda07696c0c7fcd8/2de65/images/tidy-9.png)

In R, we often need to restructure data, either by lengthening it (_wide to long_) or by widening it (_long to wide_). The _tidyr_ package (part of the _tidyverse_) allows these kinds of transformations.

Switching from *wide* to *long* format (or vice versa) can be extremely useful because certain functions are more suitable for one data format over the other.

In general, *long* formats are often preferable because it is easier to iterate over rows than columns due to the vectorized nature of R. This is especially the preferred format when preparing data for visualizations with `ggplot`, which we will explore in the next chapter.

### Exercise 5: The transformation from *wide to long*
 
 1. Restructure the data into *long* format to have emissions data by sector, keeping the commune as the level of analysis (be mindful of other identifying variables). Note: use the function `pivot_longer` from the package `tidyr`
 2. Sum the data by sector and create a barplot using the following code:
 ```r
library(ggplot2)
ggplot(df_long_summary) +
   geom_bar(
     aes(y = secteur, x = emissions),
     stat ='identity'
   )
 ```
 4. For each department, keep the most polluting sector.
 
<details>
  <summary>Exercise 5: solution</summary>

  ```r
 #Question 1
 library(tidyr)
 
 df_long <- emissions %>%
   pivot_longer(cols = -c(code_insee, Commune, dep),
                names_to = "secteur",
                values_to = "emissions")
 
 #Question 2
 df_long_summary <- df_long %>%
   group_by(secteur) %>%
   summarise(emissions = sum(emissions, na.rm = TRUE))
 
 #Question 3
 df_long_dep <- df_long %>%
   group_by(secteur, dep) %>%
   summarise(emissions = sum(emissions, na.rm = TRUE)) %>%
   arrange(desc(dep), desc(emissions)) %>%
   group_by(dep) %>%
   slice_head(n = 1)
 
 head(df_long_dep)
 ```
</details>

# Combining Data

It is becoming increasingly common to need to combine data from different sources rather than relying on a single database. Here, we will focus on the most favorable case, where information allows for an exact match between two datasets (otherwise, we would be dealing with the much more complex situation of fuzzy matching). The typical scenario involves matching two data sources based on an individual identifier or a commune code, as is the case here.

In the usual language of statisticians, we often use the terms *merge* or *join* interchangeably. The latter term comes from `SQL` syntax and is typically used when coding with `dplyr`.

> [!NOTE]
> The different types of _joins_ available in `dplyr`
> 
> ![](https://rafalab.dfci.harvard.edu/dsbook/wrangling/img/joins.png)



### Exercise 6: Enriching the emissions data
 
 First, we will calculate the carbon footprint for each commune.
 
 1. Calculate the total emissions using `rowSums`once again.
 
 2. Perform a left join between the emissions data and the `filosofi` dataset[^notebiais].
 
 [^notebiais]: Ideally, it is necessary to ensure that this join does not introduce bias. Since our reference years may not be identical, there could be a mismatch between the two sources. As this exercise is already long, we won’t dive into this. Readers interested can perform such an analysis as an additional exercise.
 
 3. Calculate the carbon footprint (total emissions / population).
 
 At this point, we might be inclined to move towards modeling to try and explain the determinants of the carbon footprint using commune-level variables. However, for any inferential approach to be relevant, it is necessary to check descriptive statistics beforehand.
 
 4. Use the following code to generate a histogram of the carbon footprint at both the raw and log levels:
 
 ```r
 ggplot(emissions_merged) +
   geom_histogram(aes(x = empreinte, y = after_stat(density))) +
   scale_x_log10()
 ```
 
 5. For each city, compare the carbon footprint to the department median. Which cities are particularly virtuous (i.e., have emissions well below the departmental average)?
 
 With a better understanding of our data, we are moving closer to inferential statistics. So far, we have constructed univariate statistics but have not examined relationships with other variables. This leads us to bivariate statistics, particularly correlation analysis. This work is important because any subsequent modeling will refine the correlation analysis to account for cross-correlations between multiple factors. We propose doing this analysis in a minimal way.
 
 6. Using the following code, look at the correlation between the framing variables and the carbon footprint. Do any variables appear to potentially influence the carbon footprint?

```r
correlations <- cor(
	emissions_merged %>%
		ungroup() %>%
	 	select(where(is.numeric)),
	use="complete.obs"
	)[,'empreinte']
 
 correlations <- enframe(correlations) %>%
   filter(name %in% colnames(filosofi)) %>%
   arrange(desc(abs(value)))

ggplot(correlations) + geom_bar(aes(x = value, y = name), stat = "identity") +
	scale_y_discrete(limits = correlations$name) 
```

<details>
  <summary>Exercise 6: solution</summary>

   ```r
 #Question 1
 emissions <- emissions %>%
   mutate(total = rowSums(pick(where(is.numeric)), na.rm = TRUE))
 
 #Question 2
 emissions_merged <- emissions %>%
   left_join(filosofi, by = c("code_insee" = "CODGEO"))
 
 #Question 3
 emissions_merged <- emissions_merged %>%
   mutate(empreinte = total/NBPERSMENFISC16)
 
 #Question 4
 ggplot(emissions_merged) +
   geom_histogram(aes(x = empreinte, y = after_stat(density))) +
   scale_x_log10()
 
 #Question 5
 emissions_merged <- emissions_merged %>%
   rename(departement = dep.x) %>%
   group_by(departement) %>%
   mutate(empreinte_mediane = median(empreinte, na.rm = TRUE)) %>%
   mutate(empreinte_relative = empreinte/empreinte_mediane)
 
 emissions_merged %>% arrange(empreinte_relative)
 ```
</details>
