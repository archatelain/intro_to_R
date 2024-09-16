# An introduction to `ggplot2`

In this tutorial, we will learn how to create synthetic graphical representations using R, which is very well equipped in this field thanks to the `ggplot2` library. The latter implements a [grammar of graphics](https://en.wikibooks.org/wiki/R_Programming/Grammar_of_graphics) that is flexible, coherent, and easy to use.

In this course, the practice of visualization will be done by replicating graphs found on the *open data* page of the City of Paris [here](https://opendata.paris.fr/explore/dataset/comptage-velo-donnees-compteurs/dataviz/?disjunctive.id_compteur&disjunctive.nom_compteur&disjunctive.id&disjunctive.name).

In this chapter, we will use the following libraries:

```r
library(scales)
library(readr)
library(dplyr)
library(forcats)
library(lubridate)
library(ggplot2)
```

> [!NOTE]
> Being able to create interesting data visualizations is a necessary skill for any data scientist or researcher. To improve the quality of these visualizations, it is recommended to follow some advice given by data visualization specialists on graphic semiology.
>
> Good data visualizations, like those from the *New York Times*, rely not only on appropriate tools (JavaScript libraries) but also on certain representation rules that allow the message of a visualization to be understood in just a few seconds.
>
> This [blog post](https://blog.datawrapper.de/text-in-data-visualizations/) is a useful resource to consult regularly. This [blog post by Albert Rapp](https://albert-rapp.de/posts/ggplot2-tips/10_recreating_swd_look/10_recreating_swd_look) clearly demonstrates how to gradually build a good data visualization.

## A first example to understand ggplot's philosophy

Let's load some INSEE data (we're really not interested in what it corresponds to here, we'll just build a graph step by step):

```r
library(doremifasol)
library(dplyr)
library(ggplot2)
library(scales)

df <- telechargerDonnees("FILOSOFI_DISP_IRIS", date = 2017) %>%
  as_tibble() %>%
  sample_frac(0.1)
```
A explained in greater details on ggplot2's [`getting started`](https://ggplot2.tidyverse.org/articles/ggplot2.html) page, ggplot2's graphs should be built layer by layer. 

First we need to initialize a figure linked to some data and specify which variables will be represented with geometric objects:
```r
ggplot(df, aes(x = DISP_MED17, y = DISP_D917))
```

Then we can add layers that will draw the data using the `geom_*` functions: 
```r
ggplot(df, aes(x = DISP_MED17, y = DISP_D917)) +
  geom_point()

# OR

ggplot(df) +
  geom_point(aes(x = DISP_MED17, y = DISP_D917))
```
> [!NOTE]
> In a `geom` layer:
> - aes parameters control parameters related to a variable
> - other options control fixed elements applied to the whole layer 

Adding some options: 
```r
ggplot(df) +
  geom_point(aes(x = DISP_MED17, y = DISP_D917, color = DISP_Q117), shape = 3)
```

Adding another layer:
```r
ggplot(df, aes(x = DISP_MED17, y = DISP_D917)) +
  geom_point(aes(color = DISP_Q117), shape = 3) +
  geom_smooth(color = "red", alpha = 0.7, se = FALSE)
```

Next you can modify the scale using `scale_` functions: 
```r
ggplot(df, aes(x = DISP_MED17, y = DISP_D917)) +
  geom_point(aes(color = DISP_Q117), shape = 3) +
  geom_smooth(color = "red", alpha = 0.7, se = FALSE) +
  scale_x_continuous(labels = unit_format(unit = "k", scale=1e-3)) +
  scale_y_continuous(trans='log', labels = unit_format(unit = "k", scale=1e-3)) +
  scale_color_viridis_c()
```

Let's modify a bit the df to add even more information to the graph:
```r
df <- df %>% mutate(quartile = factor(ntile(DISP_Q117, 4)))
ggplot(df, aes(x = DISP_MED17, y = DISP_D917)) +
  geom_point(aes(color = quartile), shape = 3) +
  geom_smooth(color = "red", alpha = 0.7, se = FALSE) +
  scale_x_continuous(labels = unit_format(unit = "k", scale=1e-3)) +
  scale_y_continuous(trans='log', labels = unit_format(unit = "k", scale=1e-3)) +
  scale_color_viridis_d(option = "turbo")
```

Only at the end do we then modify the aesthetic of the graph: 
```r
p <- ggplot(df, aes(x = DISP_MED17, y = DISP_D917)) +
  geom_point(aes(color = quartile), shape = 3) +
  geom_smooth(color = "red", alpha = 0.7, se = FALSE) +
  scale_x_continuous(labels = unit_format(unit = "k", scale=1e-3)) +
  scale_y_continuous(trans='log', labels = unit_format(unit = "k", scale=1e-3)) +
  scale_color_viridis_d(option = "turbo")

p + theme_bw() +
  labs(x = "Revenu médian", y = "9e décile", color = "Quartile") +
  theme(legend.position = "bottom")
```

## Data

A subset of the data from [Paris Open Data](https://opendata.paris.fr/explore/dataset/comptage-velo-donnees-compteurs/information/?disjunctive.id_compteur&disjunctive.nom_compteur&disjunctive.id&disjunctive.name) has been provided to facilitate the import. It is an older extraction of the data available on the site, where only the columns relevant to this exercise have been retained.

We propose to download this data and save it in a file on the local hard drive before importing it[^1]. However, we will not do this manually but rather use R. Doing such an action manually would be a poor practice from a reproducibility perspective.

[^1]: Normally, we recommend using the download URL directly to avoid creating an intermediate file on the hard drive. However, here, direct import with `readr` will not work because the file is misinterpreted by the library. It does not understand that the file is compressed because it is missing the `.gz` extension (a compressed format) at the end.

```r
url <- "https://minio.lab.sspcloud.fr/projet-formation/diffusion/python-datascientist/bike.csv"
download.file(url, "bike.gz") # <1>
```
1. The `.gz` extension is important for the next step as `readr` needs it to understand that the file is compressed.


## Initial Graphical Productions

Trying to produce a perfect visualization on the first attempt is unrealistic. It is much more feasible to gradually improve a graphical representation in order to gradually highlight structural effects in a dataset.

We will start by representing the distribution of traffic at the main measurement stations. To do this, we will quickly produce a barplot and then gradually improve it.

In this section, we will reproduce the first two graphs from the [data analysis page](https://opendata.paris.fr/explore/dataset/comptage-velo-donnees-compteurs/dataviz/?disjunctive.id_compteur&disjunctive.nom_compteur&disjunctive.id&disjunctive.name): *The 10 counters with the highest hourly average* and *The 10 counters with the most recorded bikes*. The numerical values of the graphs will differ from those on the online page, as we are working with older data.


### Exercise 1: Import the data and quickly produce a first graph

The data contains several dimensions that can be subject to statistical analysis. Therefore, it is necessary to first summarize them through aggregations to create a readable graph.

1. Import the bike counter data from the `bike.gz` file;
2. Keep the ten counters with the highest average;

```r
df <- readr::read_csv("bike.gz")
```

```r
df1 <- df %>%
  group_by(`Nom du compteur`) %>%
  summarise(`Comptage horaire` = mean(`Comptage horaire`, na.rm = TRUE)) %>%
  arrange(desc(`Comptage horaire`)) %>%
  head(10)
```

Now, we can focus on producing the representation.

3. First, without worrying about style or the beauty of the graph, create the structure of the barplot from the [data analysis page](https://opendata.paris.fr/explore/dataset/comptage-velo-donnees-compteurs/dataviz/?disjunctive.id_compteur&disjunctive.nom_compteur&disjunctive.id&disjunctive.name) Note: use the function [`geom_col`](https://ggplot2.tidyverse.org/reference/geom_bar.html): 

<details>
<summary>
Figure obtained without focusing on style
</summary>

  ```r
ggplot(df1, aes(y = `Nom du compteur`, x = `Comptage horaire`)) +
  geom_col()
```
</details>

The rest of the exercise consists of gradually improving this representation to converge toward reproducing the open data version. We are not yet focusing on the aesthetics of the figure but rather on making it intelligible in broad strokes.

4. First, reorder the bars on the y-axis using the `reorder` function. This will make the figure's message more understandable.

<details>
<summary>
Figure with reordered bars
</summary>

```r
figure1 <- ggplot(df1,
       aes(y = reorder(`Nom du compteur`, `Comptage horaire`), # <1>
           x = `Comptage horaire`)
       ) +
  geom_bar(stat = "identity")
figure1
```
1. We reorder `Nom du compteur` based on `Comptage horaire`.

</details>

5. Modify your aesthetic layer to apply a red color to all the bars.

<details>
<summary>
Figure with red bars
</summary>

```r
figure1 <- ggplot(df1,
       aes(y = reorder(`Nom du compteur`, `Comptage horaire`), # <1>
           x = `Comptage horaire`)
       ) +
  geom_bar(stat = "identity", fill = "red")
figure1
```

</details>

We are starting to get something that conveys a synthetic message about the nature of the data. However, we can still notice several problematic elements (e.g., labels), as well as missing or incorrect elements (axis titles, etc.), or missing features (such as the graph's title).


### Exercise 2: A bit of style!

The figure now conveys a message, but it is still not very readable.

1. The minimum requirement for someone unfamiliar with the data to understand the graphical representation is to label the axes. Create the same axis labels as in the original figure. Note: use the `labs`function.

<details>
<summary>
Figure with labeled axes
</summary>

```r
figure1 <- figure1 + labs( # <1>
  title = "The 10 counters with the highest hourly average",
  x = "Counter Name",
  y = "Hourly Average"
)
figure1
```
1. There are many ways to label axes in `ggplot`, but the simplest is the `labs` function.

</details>

2. The gray background is a distinctive mark that the graph was created with `ggplot2`, but it’s not very polished. Use a more minimalist theme to achieve a white background. See themes [here](https://ggplot2.tidyverse.org/reference/ggtheme.html):

<details>
<summary>
Figure with a white background
</summary>

```r
figure1 <- figure1 +
  theme_minimal()
figure1
```

</details>

3. Now, let’s focus on the more aesthetic elements. Since this is a slightly more advanced feature, we directly provide the code to update your figure with the following style elements:

```r
theme(
  axis.text.x = element_text(angle = 45, hjust = 1, color = "red"),
  axis.title.x = element_text(color = "red"),
  plot.title = element_text(hjust = 0.5),
  plot.margin = margin(1, 4, 1, 1, "cm")
)
```

<details>
<summary>
Figure with aesthetic improvements
</summary>

```r
figure1 <- figure1 +
  theme(
    axis.text.x = element_text(angle = 45, hjust = 1, color = "red"),
    axis.title.x = element_text(color = "red"),
    plot.title = element_text(hjust = 0.5),
    plot.margin = margin(1, 4, 1, 1, "cm")
    )
figure1
```

</details>

4. (optional and difficult) Finally, let's add some complexity to the graph by displaying numbers on the bars. Using this [post](https://stackoverflow.com/questions/67767859/adding-text-on-each-bar-in-horizontal-bar-plot-in-ggplot) as guidance, add the average hourly counts as seen in the open data Parisian figure[^annotation].

```r
figure1 <- figure1 + 
  geom_text(
    aes(label=round(`Comptage horaire`)),
    position=position_dodge(width=0.9),
    hjust=-0.5
    )
```

[^annotation]: This is not necessarily a good data visualization practice as it means that the scale and diversity of the data are not directly intelligible.

</details>


We now understand that the Boulevard de Sébastopol is the most frequently used, which won’t surprise you if you cycle in Paris.

### Exercise 3: Produce a new figure

1. Do the same for figure 2 ("The 10 counters with the most recorded bikes") to obtain a similar figure.

<details>

```r
df2 <- df %>%
  group_by(`Nom du compteur`) %>%
  summarise(`Comptage horaire` = sum(`Comptage horaire`, na.rm = TRUE)) %>%
  arrange(desc(`Comptage horaire`)) %>% 
  head(10)
```

```r
# Create a horizontal bar plot
figure2 <- ggplot(df2, aes(y = reorder(`Nom du compteur`, `Comptage horaire`), x = `Comptage horaire`)) +
  geom_bar(stat = "identity", fill = "forestgreen") +
  labs(title = "The 10 counters with the most recorded bikes",
       x = "Counter Name",
       y = "Total Bikes Recorded Over the Selected Period") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1),
        axis.title.x = element_text(color = "forestgreen"),
        plot.title = element_text(hjust = 0.5),
        plot.margin = margin(1, 4, 1, 1, "cm"))
```
</details>

Bar charts (_barplot_) are extremely common, but what do they convey? From a semiological perspective, _lollipop charts_ are preferable: they convey the same information but with less noise (the width of the bars in the barplot tends to obscure the information slightly).

Here, for example, is the second figure from the page, now presented as a _lollipop chart_ instead of a _barplot_:

```r
df2_lollipop <- df2 %>%
  mutate(x = fct_reorder(`Nom du compteur`, `Comptage horaire`), y = `Comptage horaire`)

figure2_lollipop <- ggplot(df2_lollipop, aes(x = x, y = y)) +
    geom_segment(aes(xend = x, yend = 0), alpha = 0.4) +
    geom_point(size = 5, color = "forestgreen") +
    coord_flip() +
  labs(title = "The 10 counters with the most recorded bikes",
       x = "Counter Name",
       y = "Total Bikes Recorded Over the Selected Period") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1),
        axis.title.x = element_text(color = "forestgreen"),
        plot.title = element_text(hjust = 0.5),
        plot.margin = margin(1, 4, 1, 1, "cm")) +
  scale_y_continuous(labels = unit_format(unit = "M", scale = 1e-6))
```

```r
figure2_lollipop
```

## First Temporal Aggregation

We will now focus on the spatial dimension of our dataset through two approaches:

- A bar chart summarizing the information in our dataset on a monthly basis;
- Informative time series illustrating temporal dynamics. This will be covered in the next section.

To start, let's reproduce the third figure, which is again a _barplot_. The first task involves an introduction to temporal data through a common time series operation: changing the format of a date to aggregate it over a broader time scale.

### Exercise 4: Barplot of Monthly Counts

Let us now reproduce the 3rd graph of the [data analysis page](https://opendata.paris.fr/explore/dataset/comptage-velo-donnees-compteurs/dataviz/?disjunctive.id_compteur&disjunctive.nom_compteur&disjunctive.id&disjunctive.name).

Let's use the `format` function to create a `month` variable following the format `2019-08`:
```r
df <- df %>%
  mutate(month = format(`Date et heure de comptage`, "%Y-%m"))
```

1. Let's see if you remember how to use dplyr ! Calculate the average hourly counts for each month.

<details>
<summary>Question 1 solution</summary>
  
```r
comptage_horaires_mois <- df %>%
  group_by(month) %>%
  summarise(value = mean(`Comptage horaire`, na.rm = TRUE))
```
</details>
  
2. Reproducing a bit the steps we took in exercise 2, reproduce the 3rd example on the page.

<details>
<summary>Question 2 solution</summary>
  
```r
figure3 <- ggplot(comptage_horaires_mois) +
  geom_bar(aes(x = month, y = value), fill = "#ffcd00", stat = "identity") +
  labs(x = "Date and Time of Counting", y = "Monthly Average of Hourly Counts\nfor the Selected Period",
       title = "Monthly Average of Bike Counts") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1),
        axis.title.y = element_text(color = "#ffcd00", face = "bold"),
        plot.title = element_text(hjust = 0.5),
        plot.margin = margin(1, 4, 1, 1, "cm"))
```
</details>


## First Time Series

It is more common to represent data with a temporal dimension as a time series.


### Exercise 5: Representing a Time Series

1. Create a `day` variable that transforms the timestamp into a daily format such as `2021-05-01`. Note: use the `date` function from the `lubridate` package.

<details>
  <summary>Solution to question 1</summary>
  
```r
df <- df %>%
  mutate(day = date(`Date et heure de comptage`))

moyenne_quotidienne <- df %>%
  group_by(day) %>%
  summarise(value = mean(`Comptage horaire`, na.rm = TRUE))
```
</details>

2. Represent this information as a time series, without worrying about the style of the figure. Note: use `geom_line`:

<details>
  <summary>Solution to question 2</summary>
  
```r
figure4 <- ggplot(moyenne_quotidienne, aes(x = day, y = value)) +
  geom_line(color = "magenta")
```
</details>
  
3. Fill the area under the line with the appropriate function. This time I let you find which function to use.

<details>
  <summary>Solution to question 3</summary>
  
```r
figure4 <- figure4 +
  geom_area(fill="magenta", alpha = 0.6)
```
</details>

4. Finalize the graph to replicate the figure from the Open Data page. (Add labels and theme).

<details>
  <summary>Solution to question 4</summary>
  
```r
figure4 <- figure4 +
  labs(x = "Date and Time of Counting (Day)", y = "Daily Average of Hourly Counts\nfor the Selected Period",
       title = "Daily Average of Bike Counts") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1),
        plot.title = element_text(hjust = 0.5),
        plot.margin = margin(1, 4, 1, 1, "cm"))
```
</details>
