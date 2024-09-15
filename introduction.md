# A Gentle Introduction to R 

## Why are we here? 

As for most scientific endeavors, the accumulation of knowledge in economics is based on a permanent "back and forth" movement between economic theory and empirical observations. Researchers typically:

1. Observe an empirical pattern;
2. Try to build a theory which explains this stylized fact;
3. Empirically test the theory's predictions to confirm or invalidate their understanding of the phenomenon.

Since the 1990s, the amount of data at our disposal has drastically increased, opening new avenues to find surprising stylized facts and test past theories. **In turn, this means you are more than likely to work with data at some point in your career as an economist.**

Unsurprisingly, as more and more academics work with observational data, standards and best practices are progressively emerging to ensure the transparency and replicability of research papers. In fact, an increasing number of top journals require academics to provide detailed replication folders prior to publication ([https://www.aeaweb.org/journals/policies/data-code](https://www.aeaweb.org/journals/policies/data-code)). **In the future, you will need to meet these standards.**

 The purpose of this mini-lecture is to provide you with a gentle introduction to working with data in economics using the open-source language R. In this 8 hour course, you will:

1. Get introduced to R and RStudio
2. Learn how R as a programming language work 
3. Be introduced to the most common ways to perform data and statistical analysis on R
4. Learn how to perform some data visualisation with R 

A few disclaimers before we begin:
1. We won't cover any econometrics in this class for lack of time mostly. See the references section for some resources that cover econometrics with R. 
2. This introduction is meant to give you a first understanding of the language and guide you to the relevant packages and practices. After that the most efficient way to learn R will be for you to start some projects with it and "learn by doing"

## What are R and RStudio

**R** is an open-source programming language and environment specifically designed for statistical computing and data analysis. It provides a wide array of tools for data manipulation, calculation, and graphical display, making it one of the most popular languages in the fields of data science, statistics, and research.

**RStudio** is an integrated development environment (IDE) for R. It provides an intuitive user interface that makes it easier to write and run R code, offering features like a code editor, data viewer, and tools for managing packages and visualizations. 

So to follow this class you will need to download both R and RStudio.

## Installation

You should have an RStudio already set up on your accounts. Now if you want to work on your personal laptops you can. 

You should find instructions to install R here: https://cran.r-project.org/bin/windows/base/
As for RStudio you can find it here: https://posit.co/download/rstudio-desktop/


## A note on using ChatGPT

ChatGPT and the likes are game-changers when it comes to coding. You should however learn to use them wisely to make the most out of them. Here is my two-cent opinion on using them: 

- It is still relevant to learn how to code. It is only when you know how a language works that you can evaluate the quality of the output ChatGPT gives you. This also allows you to write more specific prompts and to assess beforehand how a task should be performed. 
- Use them to learn. If you don't understand a command, ask how it works. Then ask more precise questions on the parts of the answer you did not understand, etc.  
- Use them to comment code you are reading. So say you have to read someone else's code. You can feed it to chatGPT and ask it to explain it step by step or comment it. Be careful however that you are allowed to give the code to ChatGPT (issues of copyright). Be aware also that when using the free version of ChatGPT all your conversations are used to train the models. 

## References

This class is a translation of Lino Galiana's introduction to R (in French). You can find it here: https://rgeo.linogaliana.fr/

To learn about econometrics with R: 
- https://www.econometrics-with-r.org/index.html
- https://bookdown.org/kieranmarray/intro_to_r_for_econometrics/workflow.html#writing-documents-with-rmarkdown
