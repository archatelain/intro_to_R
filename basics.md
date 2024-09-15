# RStudio's interface

Let us explore RStudio's interface with an exercise: 

### Exercise 1 
1. Look at the working directory's structure at the bottom right of RStudio
2. Create a folder called `Introduction_to_R`  
3. Create a new script using the menu `File/New File/R script` and save it in the folder as `script1.R`
4. Use the help section of RStudio to learn more about the function `list.files` 
5. Write some code in `script1.R` that lists all files in the working directory 
6. Press `RStudio` <kbd>CTRL</kbd> + <kbd>ENTER</kbd> to execute the code in the consol


# One-Dimensional Objects 

We'll start with one-dimensional objects, that is lists of values of the same type. For instance:
- `["Economics", "Sociology", "History"]`
- `[4 8 15 16 23 42]`. 

These one-dimensional lists of values can be represented by vectors. The four most common types of vectors in R are: 
- numerical vectors 
- character vectors 
- logical vectors  
- factors 

In a second time, we'll look at more complex data structures that are in fact the most commonly used: lists and dataframes.

## Numerical vectors

### Two types of numerical vectors

R has different types of numerical objects. For data analysis, we will mainly make use of the following two types:
- integers (called `int`)
- real numbers (called `double')

In practice the former are a special case of the latter. 

One technicality here is that R does not constrain numbers without decimals to be integers. This avoids some technical problems but on huge datasets *double* numbers weigh more than *int* ones.

We use the function `class` to see an object type and if we want more information we can also use `typeof`:

```r
class(3)
typeof(3)
class(3.14)
typeof(3.14)
```


The functions `as.numeric` and `as.integer` may be used to convert one type into the other:

```r
# Converting to int
as.integer(3.79)
```

>[!warning]
Be careful when going from *double* to *int*, you lose the decimal!
>  ```r
>  as.numeric(as.integer(3.79))
>  ```

You can also use the scientific notation for *floats*:

```r
2e3
class(2e3)
```

### Basic arithmetics

R is first and foremost a calculator: 
```r
# Addition
8 + 9
```

R is smart, it adapts the variable type to match them when possible:

```r
# Addition
8.1 + as.integer(9)
```

We can of course perform all other standard operations:

```r
# Substraction
5 - 2
# Multiplication
2 * 6
# Division
9 / 4
```

Be careful not to divide by 0: 

```r
# Division par 0
3 / 0
-5 / 0
```

>[!warning]
Some languages like Python do not allow dividing by 0, they print an error rather than giving `Inf`. This might be misleading in R because we might miss cases when we are dividing by 0...

Other operations available:
```r
# Euclidian division : quotient
9 %/% 4
# Euclidian division : remainder
9 %% 4
```

```r
# Power
2 ^ 5
# Square root
sqrt(5)
# Log
log(2)
# Exponential
exp(2)
```

The order of operations follows usual conventions: 

```r
2 + 5 * (10 - 4)
```

### Vector arithmetic

R start becoming very handy when it comes to vector arithmetic. Let us consider that vectors are sequences of numbers placed in a column:

$$
\begin{bmatrix} x_1 \\ x_2 \\ \vdots \\ x_n \end{bmatrix}
$$

We will now apply operations on each line of this vector. We talk about vectorization of operations to talk about an operation that will automatically be applied to each element of a vector.

For instance, multiplications are vectorized by default:
```r
5*c(1,20,2)
```

The same goes for the addition, provided that it makes sense in terms of vector sizes:

```r
c(1,20,2) + c(21,2,20)
c(1,20,2) - 3
```

>[!warning]
>If the vector size does not make sense, R recycles the smallest one until it makes sense again:
>```r
>c(1,20,2) - c(1,20)
>```

## Character vectors

Character vectors are used to store textual information. More precisely, they can store every [Unicode](https://en.wikipedia.org/wiki/Unicode) character, (this  includes letters from different languages, punctuation, figures, smileys, etc.)

### Create a *string*

To create a *string*, you can use quotation marks or apostrophes:

```r
'word' # <1>
"this also works" # <2>
```
1. First method: `'`
2. Second method (preferred): `"`

> [!warning] 
> Do not mix the two !
> ```r
> print('I'll have to take care of this apostrophe')
> ```
> The problem here is that R sees the second apostrophe as the end of the sequence and does not know how to interpret what follows.
>
> You thus need to use the other option when that's the case:
> ```r
> "I'll do it this way" 
> ```
> This is also true the other way around: 
> ```r
> 'but I can "also" do it this way'
> ```
> You can also escape special characters using backslash `\`:
> ```r
> "here is, \"another option\"" 
> 'l\'ve shown every combination possible' 
> ```

### Some useful functions

R offers some useful function to extract or transform textual vectors. For more advanced functions see the package `stringr`.

The function `nchar` allows counting the number of characters in a string, all characters included (letter, number, space, punctuation, etc.)

```r
nchar("I have 20 characters")
```

Be careful not to mistake it with the function `length`. The latter give the vector's length. For instance, 

```r
length("I have 20 characters")
```

is of length 1 since it contains only one string.

An example with a vector with multiple elements:

```r
length(c("I have 20 characters", "I don't"))
```

`nchar` is a vectorized operation, so we can do use it on our vector of length 2:

```r
nchar(c("I have 20 characters", "I don't"))
```

Among the common operation we perform when working with text, we often want to transform all our text. For instance we might want to capitalize all the text or rather remove all capitalization. We can do it as follows:

```r
toupper(c("sequence 850", "Sequence 850")) # <1>
tolower(c("SEQuEnce 850", "SEQUENCE 850")) # <2>
```

There are also some basic function to clean up textual vectors:

```r
strsplit(c("a sequence    to split", "anotheronetosplit"), split = " ")
```

At this point, the output with `[[]]` might seem surprising for we haven't talked about  `lists` yet.

Given that this is not the most handy format for statistical analysis I recommend using the package `stringr` to do splits.

Note also that we can split our strings on other characters:
```r
strsplit(c("a sequence    to split", "anotheronetosplit"), split = "à")
```

We can also concatenate *strings*. To do this we use `paste` or `paste0` (a simpler version meant for simple concatenation):
```r
paste0(
	"Kilimanjaro is a snow covered mountain 19,710 feet high,",
	" ",
	"and is said to be the highest mountain in Africa.",
	" ",
	"Its western summit is called the Masai Ngàje Ngài, the House of God"
) # <1>

paste(
	"Kilimanjaro is a snow covered mountain 19,710 feet high,",
	"and is said to be the highest mountain in Africa.",
	"Its western summit is called the Masai Ngàje Ngài, the House of God",
    sep = " "
) # <2>
```

We can use strings as _templates_. This is especially useful when we want to automatically create texte from values from our data. For this we use `sprintf`:

```r
sprintf("%s is a snow covered mountain %i feet high", "Kilimanjaro", 19710)
```


## Logical vectors 

In R, logical vectors are used to store boolean values (that is `TRUE` or `FALSE`).

Logical vectors are mainly used for logical operations, data filtering and conditional selection. We shall come back to this, but we will be using them a lot but more indirectly.
```r
5 > 3  # <1>
2 == 2 # <2> 
0 == (2 - 2) # <3>
1 < 0 # <4>
```

We can also do comparisons with vectors:

```r
c(2, 4, 6, 8, 10, 1, 3) %% 2 == 0
```

Here is an example of using logical vectors to select some data. Say we have data on age and we want to select only the adults name. We would proceed as follows:

```r
c('Pierre', 'Paul', 'François', 'the others')[
    c(25, 3, 61, 17) >= 18
]
```

However, we will see later on that there is an even handier way to proceed using the package [`dplyr`](https://dplyr.tidyverse.org/reference/filter.html).

## Factors

Factors are used to represent categorical variables, that is variables that take a finite and predetermined number of levels or categories. 

To convert a numerical or textual vector to a factor we use the function `factor`:

```r
factor(
    c("city","county","region","state")
)
factor(c(1,10,3))
```

The levels of a factor are the different categories or values it can take. We can list them with `levels`.

```r
levels(
    factor(
        c("city","county","region","state")
    )
)
```

We can also order these levels if this makes sense. This however implies to know beforehand our different levels and to put them in the correct order.

```r
factor(
    c("city","county","region","state"),
    levels = c("state", "region", "county", "city"),
    ordered = TRUE
)
```


# Create variables

Up until now we have had to define our object everytime we wanted to use it. How should we proceed if we want to reuse an object? Or if we want to use several objects in a single operation? 

To do this we will assign objects to variables. This then allows us to work with the variables.

In R, assigning an object to a variable can be done this way:

```r
variable_name <- object
```

Writing assignment this way in R is a convention and a best practice. Note that the arrow goes from right to left. Don't do it the other way around (or this will make your code more difficult to read)

You can also assign object to variable with `=` but nobody does that so don't. 

Here is an example of creating a vector `x` :

```r
x <- 5
x
```

We can then reuse `x`:

```r
class(x)
```

Variables can be any type of object and we can create variables using other variables:

```r
x <- c(5, 10)
y <- x + 2*x
y
```

>[!warning]
> R is a dynamically typed language. This means that we can reassign a variable to an object of another type easily. This makes it much easier to use but can sometimes lead to errors that are difficult to debug. 
> 
> You should thus always be careful with the type of the variable you are using. Here is an example:
> ```r
> x <- 3
> x <- "blabla"
> class(x) #<1>
> ```
1. `x` is not _numeric_ anymore so you should keep that in mind with future operations you will perform on it !

There are constraints on the type of operations available depending on the type of the object.

```r
x <- "test"
y <- 3
x + y # <1>
```
1. you cannot use a sum for a *string*

You can however change the variables types beforehand: 
```r
x <- "5"
y <- 3
z <- as.numeric(x)
y + z
```
# Indexing

In R vectors have indexes that allow accessing specific elements using their position in the vector. 

Indexes start at 1 meaning that the first vector element has an index of 1, the second one 2, etc.[^noteindice].

[^noteindice]: 
  This might seem obvious but it is not in computer science. In other languages (Python, C) indexing starts at 0.

```r
x <- 2*seq(1,10)
```
1. Note that we used `seq` here. I let you figure out for yourself what this function does. 

To access a specific element in a vector using its index number we use the notation `[ ]`. 

```r
# Access the second vector element
second_element <- x[2]
second_element
```

Now the variable `second_element` has the value 4.

> [!note]
> Note that we can now change the vector `x` this won't affect the vector `second_element`.
> ```r
> x <- seq(5,9)
> print(second_element)
> second_element == x[2]
> ```
> In R the value a variable takes is only changes when there is a reassignment (one way or another).


We can also *slice* the vector:
```r
x <- seq(5,15)
x[1:5]
x[c(2,3,8)]
```

We can also make negative selections that is selecting all values but some. To do this use negative numbers.

```r
x[-1] # <1>
x[c(-3, -1)] # <2>
```

It is however often difficult to use index positions solely. Indeed say you have a vector of length 1,000, you might be unclear at which position a certain value is. 

Instead we favor using selection based on logical conditions. We already saw it with this example:

```r
c('Pierre', 'Paul', 'François', 'the others')[
    c(25, 3, 61, 17) >= 18
]
```

Using variables, this becomes even easier to read: 

```r
prenom <- c('Pierre', 'Paul', 'François', 'the others')
age <- c(25, 3, 61, 17)
prenom[age >= 18]
```

Another example with textual data:

```r
villes <- c("Paris", "Toulouse", "Narbonne", "Foix")
statut <- c("capitale","préfecture","sous-préfecture","préfecture")
villes[statut == "préfecture"]
```

We will see another way to proceed in the next chapter using data filtering.

# Missing values

In practice datasets often contain missing values. For instance in GDP time-series, values in the past might be missing for countries that did not exist at the time.

Missing values are represented by the value `NA` (Not Available) in R.

They are essential and R offers a convenient way to handle them. Still with our example of GDP, say we want to compute an average GDP. Should we include countries with missing values or should this print an error? 

The function `is.na()` allows checking whether there are any missing values in a vector:

```r
donnees <- c(10, NA, 30, NA, 50)
is.na(donnees)
```

We can only stress on handling correctly missing values is crucial in statistical analysis. Missing values are often not random.

> [!note]
> Note on handling missing values 
>  There are several approaches to handling missing values depending on your goal. This goes beyond this class content but here is a brief overview of what you can do:
>  - **delete the missing values**: delete the rows containing missing values with the functions `na.omit()` or `complete.cases()`.This is for instance what the linear regression in R does. This approach might however introduce some bias and some loss of information ;
>  - **Impute the missing values**: making some hypotheses on underlying distributions you can also estimate the missing values. The simplest methods take the mean or the median but you can also go for interpolation. This method does however change the distribution of the observed variable ;
>  - **Treat missing values as an independent group**: in descriptive statistics you can isolate missing values in a separate group.

# Exercises on one-dimensional objects

### Exercice 2
 Print `x`'s type: 
 ```r
  x <- 3
  x <- "test"
  x <- 3.5
 ```

<details>  
  <summary>Exercise 2: solution</summary>
  
```r
typeof(3)
typeof("test")
typeof(3.5)
```

</details>


### Exercice 3
 Compute the sum of the length of each of these 3 character strings:
 - _"a first character string"_
 - _"a second one "_
 - _"and a third one that is also the last"_

<details> 
  <summary>Exercise 3: solution</summary>

```r
x1 <- "une première chaîne"
x2 <- "et une deuxième"
x3 <- "jamais deux sans trois"
nchar(paste(x1, x2, x3, sep = ""))
```
</details>
  
### Exercice 4
Here we are using French *codes communes*. These are identifier for each commune in France (the smallest administrative division in France, there are about 35,000 of them). 
 
Here is a list of code communes from the [Code Officiel Géographique (COG)]((https://www.insee.fr/fr/information/6800675))
```r
liste_communes <- c(
  '01363', '02644', '03137', '11311', '12269', '13018', '14458', '15008',
  '23103', '2A119', '2B352', '34005', '2B015', '2A015',
  '38188', '39574', '40223', '41223', '42064',
  '63117', '64218', '65209', '66036', '67515', '68266', 
  '77440', '78372', '79339', '80810', '81295', '82067',
  '93039', '94054', '95061', '97119', '97219', '97356', '97421', '97611'
)
```
1. Extract the *département* (first two characters) of each commune ;
2. How many unique *département* do we have in the data ;
   
<details>
  <summary>Exercise 4: solution</summary>

```r
# Question 1
dep <- substr(liste_communes, start = 1, stop = 2)
# Question 2
length(
  unique(dep)
)
```  
</details>


### Exercice 5
Delete the superfluous space at the beginning and the end of each of the following character strings: 

```r
a <- "    Un string très mal formatté.         "
```
Note: you might want to use a function we have not covered yet. 
 
<details>
  <summary>Exercise 5: solution</summary>
  
```r
trimws(a)
```
</details>

# More complex objects

## Matrices

Matrices can be seen as two-dimensional vectors. It is as if we concatenated columns next to one another.

$$
X = \begin{bmatrix}
x_{11} & x_{12} \\
x_{21} & x_{22} \\
\end{bmatrix}
$$

Matrices however have one major limitation: they can only store elements of the same type. This means that we will have matrices of numeric values, of character strings or of booleans. We cannot have both numeric values and character strings in the same matrix however. 

This makes them a type of object not suited to store statistical information as can be found in surveys for instance. Matrices will most often be used in mathematical statistical rather. 

We will thus do one exercise with them and then move on to more flexible objects where several type of objects can be stored at the same time: 

### Exercice 6
Let us define a matrix `X`:
```r
 X <- matrix(letters[1:20], nrow = 4, ncol = 5)
```
1. Select the first element of the matrix (first row, first column)
2. Select the first row
3. Select the first column
4. Select the elements at the intersection of:
  1. the 2nd and 3rd row
  2. the 1st and 3rd column

<details>
  <summary>Exercise 6: solution</summary>

```r
X[1,1] # <1>
X[1,]  # <2>
X[,1]  # <3>
X[2:3,c(1,3)] #<4>
```
</details>

## Lists

Lists are much more versatile than matrices. A list can contain all types of objects (numerical vectors, strings, logical vectors, matrices, etc.) and even other lists.

This makes lists the go to object to stores complex and structured information, for instance results of statistical procerures (regression, classification, etc). For more structured data, like data sets, we will then turn to a special type of list *dataframes*.

### Exercise 7
Here is an example of how lists can store several types of objects:
```r
my_list <- list(
  1,
  "text",
  matrix(letters[1:20], nrow = 4, ncol = 5),
  c(2, 3, 4)
  )
```
1. Print the list and notice the differences with the objects we have worked with so far.1. Use the notation `[[]]` to access the second list element. 
2. Now access the 2 integer of the last element of the list.
3. We can also name elements in our list. Create an element named `communes` in the list that will store the vector `c('01363', '02644', '03137', '11311')`
4. Create an element `départements` in the list by extracting the first two figures in each element of your element `communes`

<details>
  <summary>Exercise 7: solution </summary>
  
```r
#Question 1: afficher la liste
ma_liste
# Question 2: Accéder au deuxième élément de la liste
ma_liste[[2]]
ma_liste[[4]][2]
# Question 3: mettre à jour la liste avec un élément nommé et y accéder
ma_liste[['communes']] <- c(
  '01363', '02644', '03137', '11311'
  )
ma_liste[['communes']]  
# Question 4: effectuer une opération 
ma_liste[['departements']] <- substr(ma_liste[['communes']] , start = 1, stop = 2)
```
</details>

>[!note]
>When using lists we can perform operations on each list element. We call that looping through a list. 
> 
>Loops are fundamental in computer science and R like other languages allows writing loops using the 
commands `for` or `while`. We will be using them in the next exercise.
>
>Note however that it is often a bad practice to use loops. Vectorized operations are usually much faster (they are designed to be the fastest option).


### Exercise 8
In this exercise we will see how we can apply the same function to all elements in our list using `lapply`.  Let us reuse the list we created in exercise 7. 
1. How many elements does our list contain?
2. How many elements do each of our list element contain? 
3. Create a numerical vector equal to 1 if the list element is of type "double" and 0 otherwise. 


>[!tip]-
>Here is an example of using `lapply` to do the sum of each element in our list: 
>```r
>my_list <- list(c(1, 2), seq(1, 10))
>lapply(ma_liste_nombres, sum)
>```

<details>
  <summary>Exercise 8: solution</summary>

```r
# Question 1
longueur_liste <- length(ma_liste)
# Question 2
lapply(ma_liste, length)
as.numeric(
	lapply(ma_liste, function(l) typeof(l) == "double")
	)
```
</details>

## Dataframes
Dataframes are the most important objects when it comes to data analysis on R. These objects allow representing in a table (i.e. a two dimensional object) quantitative (numeric vectors) and qualitative data (textual vector or factor). 


![Illustration du principe du _dataframe_ (empruntée à H. Wickham)](https://d33wubrfki0l68.cloudfront.net/6f1ddb544fc5c69a2478e444ab8112fb0eea23f8/91adc/images/tidy-1.png)


Here is a `dataframe`:

```r
df <- data.frame(
  var1 = 1:10,
  var2 = letters[1:10],
  var3 = rep(c(TRUE, FALSE), times = 5)
)
```

You can take a look at its internal structure using `str`:

```r
str(df)
```

We can inspect the first few rows of the dataframe using `head`:

```r
head(df)
```

>[!warning]
You can also use RStudio's viewer pour to show a data set. This viewer however can crash when the dataset is too big. 
>
>I'd rather recommend always using `head` and `sample` to get a random subset of rows.
>
>```r
>df[sample(nrow(df), 3), ]
>```

In terms of structure a data.frame is in fact a list of which all elements are the same size. This allows representing the data in a table.

```r
is.list(df)
lapply(df, length)
```

data.frame thus have both lists and matrices capabilities. We shall see it in the following exercise: 

### Exercise 10
Using the dataframe `df` we created earlier: 
1. Check out the dataframe dimension
2. Count the number of rows. Count the number of columns.
3. Check out the length of the dataframe. Does it behave like a list or a matrix?
4. Extract the element on the 2nd row and 3rd column. Are we indexing like in a list or a matrix?
5. Extract the 3rd row of the variables `var1` and `var2`. 

<details>
  <summary>Exercise 10: solution</summary>

  ```r
  dim(df)
  nrow(df)
  ncol(df)
  length(df) #comme une liste
  df[3, c("var1","var2")]
  ```

</details>

Dataframe can be easily modified. The most classical operations to which we shall come back in the following chapter are: 
- creating a new column based on existing ones.
- Select a subset of the data

There are several ways to refer to an existing column in a dataframe. The simplest one use the structure `dataframe$column`. This gives a vector and we know how to work with them.

```r
class(df$var1)
```
### Exercice 11

1. Create a column `var4` in our dataset that compute that square the values of `var1` 
2. Create a column `var5` in our dataset that concatenates the first two variables and generalises the scheme `1=a`.
3. Create a dataframe `df_small1` for the rows where `var3`is `TRUE`.
4. Create a dataframe`df_small2` for the rows where `var1` is even

In the next chapter we will introduce the ecosystem of the `tidyverse` and it's famous package `dplyr`.
This ecosystem greatly contributed to making R one of the two major statistical language (the other begin Python).
