# An exercise summarizing what we have seen in this class

Download the data at this url: 
```r 
url <- 'https://www.data.gouv.fr/fr/datasets/r/182268fc-2103-4bcb-a850-6cf90b02a9eb'
```
## Question 1

Create or update the following variables:

- `code_commune`: Using the existing variable and the department, replace the value of `code_commune` to form a proper commune code. For example, for Montrouge, you should get `92049`.
- `candidate`: Create a column with the `prenom` and `nom` concatenated together, remembering to include a space. Do not eliminate the abstention, blank, or null ballots, as we will use them later.

## Question 2

How many candidates were there in 2022? (Obviously you should be using R to answer the question). 

## Question 3
Calculate the national scores for each candidate and store it in `national_scores`. Represent in this table, for each candidate, the number of votes and the percentage of expressed votes (i.e., excluding abstentions and unexpressed votes).

_Note: You can verify your results with [this page](https://www.gouvernement.fr/actualite/election-presidentielle-les-resultats-du-premier-tour)_

## Question 4

Create a dataframe named `departements_scores` that stores, for each department, the number of votes obtained for each candidate and their score (in %).

## Question 5

Merge `national_scores` and `departements_scores`. Name this dataframe `departements_cores`, as we will use it later.

## Question 6

Create a variable `surrepresentation` that compares, in relative terms, the national and departmental scores.

For example, if a candidate has a score of 30% in a department but 15% nationally, the value of `surrepresentation` will be 100 (%).

## Question 7

Observe the top 5 overrepresentations, in absolute value, for each candidate. Represent this on a graph, with 12 horizontal barplots (one for each candidate). 

## Question 8

Find the top 10 communes with the highest abstention rates among those where there were more than 2000 votes and which do not belong to the DROM or French abroad departments (for this, the filter is directly provided: `filter(!str_starts(code_commune, "(fr|98|97)"))`).
