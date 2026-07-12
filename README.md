# Assignment 5: Modeling the spread of COVID-19

## Modeling a disease

Epidemiologists (scientists who study disease) often want to predict how the disease will spread in the future.

One way to do so is with a simple model called the **SIR** model of disease. SIR stands for:

* **S**usceptible (the number of people who can still catch the disease)
* **I**nfectious (the number of people who have currently got the disease, and can spread it to susceptible people)
* **R**ecovered (the number of people who have recovered from the disease, and who are immune to reinfection)

In this model, susceptible people get infected, and then those infected people eventually recover.

Epidemiologists have used the SIR model to predict how COVID-19 will spread. In this assignment, we are going to write an R program to run the SIR model, and compare its predictions to the actual COVID-19 infection data.

> #### Statistical models versus Mathematical models
>
> Although we call SIR a model, it is different to the linear models that we created in earlier assignments.
>
> In all these contexts, *model* means "a simplification of reality".
>
> However, a linear model is a *statistical model* where we try to fit a pattern (e.g. a line of best fit) to some existing data. (In theory, we can then extrapolate that pattern to predict the future.)
>
> The SIR model is a *mathematical model*: we define certain simple mathematical rules for a system (e.g. there are 1000 people, each infected person has a 10% chance of infecting somebody else every day, etc.), and then we "run" the model to simulate how this system changes over time if it follows these rules.

## How we calculate S, I, and R

Certain things in the world change exponentially. This means that how fast they change at any point in time depends on how big they are at that time.

For example, the amount of interest that your bank pays you depends on how much money you currently have in your bank account:

* bank balance<sub>day 2</sub> = bank balance<sub>day 1</sub> + interest<sub>from day 1's balance</sub>

We could write code in R that calculates the bank balance on day 2. Let's assume that we start with $1000 and our bank is paying 1% interest per day:

```r
balance_on_day_2 <- 1000 + 0.01 * 1000
```

What about if you wanted to know the balance every day for the next year (365 days). You could write 365 lines of code, each doing this same calculation... or, you could use a *for* loop!

```r
bank_balance = 1000

for(day in 2:365){
  bank_balance[day] <- bank_balance[day - 1] + 0.01 * bank_balance[day - 1]
}
```

This creates a vector called bank_balance with 365 values: how much money we have each day.

We can do the same thing for the S, I, and R values of the SIR model to predict the spread of a disease:

* We start off with the initial value for each category (just like our starting bank balance).
* We have an equation that shows how much each value grows each day, based off the value from the previous day.
* We use a *for* loop to iterate through every day in the time period we are interested in, and build up a vector for each category.



## Exercises

Write your code in the `sir_model.Rmd` file. Run the setup chunk at the top of the file before starting.

1. Create a vector called `days_range` that holds the values 1 through 250. (We will run our model for 250 days.)

    Commit your code.

2. Create three new vectors: `susceptible`, `infectious`, and `recovered`. Each vector should hold one value: 300,000,000 in `susceptible`, 1 in `infectious`, and 0 in `recovered`. 

    This represents the number of people in each category of the SIR model when it starts:
    
    * 300 million people are happily walking around uninfected but *susceptible* to the disease, representing the total population of the USA.
    * 1 person starts carrying the disease (*infectious*).
    * Initially, nobody has recovered (because nobody has had the disease long enough).
    
    Commit your code.

3. In this exercise, you will create a single *for* loop that iterates through the 250 days that we are running our simulation for, and in each iteration adds a new value to the end of the `susceptible`, `infectious`, and `recovered` vectors (like the bank balance for loop in the introduction's example).

    Each day, a certain number of people are infected, and a certain number of people recover. The changes in the three numbers, S, I, and R, are given by these equations:
  
    ![](images/sir_equations_delta.png)
    
    Yikes! But its not as bad as it looks!
    
    The subscript *d* means the current day (and *d-1* means the previous day). The &Delta; symbol means the change in each value (just as the bank interest was the change in your bank balance). In other words, **the value on any day _equals_ the value from the previous day _plus_ the change since the previous day**.
    
    How do we calculate the change in each value?
    
    According to the SIR model, these are the full equations (with each change replaced by the values you will need to use in your code):
    
    ![](images/sir_equations_full.png)
    
    (There are some new symbols here, &beta;, &gamma;, and *N* - these will be explained below.)
    
    For example, the number of susceptible people, S, *equals* the number of susceptible people from the previous day *minus* the number of people who got infected. In code, you would write that as:
    
    ```r
    susceptible[day] <- susceptible[day - 1] - (beta * susceptible[day - 1] * infectious[day - 1]) / N
    ```
    
    This code would go inside the for loop, just like our code to add a new bank balance value did.
  
    **Your turn:**
    
    * Define three new variables, `beta` (i.e. &beta;), `gamma` (&gamma;), and `N` with these values:
    
        * `beta`: 0.25  (This represents the infection rate of the disease, i.e. the number of susceptible people that an infected person infects per day. We are estimating that every infected person has a 25% chance of infecting a susceptible person every day.)
        
        * `gamma`: 0.125 (This represents the recovery rate i.e. an infected person has a 12.5% chance of recovery every day.)
        
        * `N`: 300,000,001 (The total number of people, i.e. the 300 million susceptible people plus the 1 infected person.)
    
    * Write a *for* loop that runs from day 2 to day 250. This *for* loop should look something like this:
    
            for(day in ...){
                susceptible[day] <- ...
                infectious[day] <- ...
                recovered[day] <- ...
            }
        
        Note that in the example code, `day` is a variable created by the for loop, e.g. `for(day in ...)`, who's value will be updated automatically during each iteration of the loop.
        
        The first value of `day` in the first for loop iteration should be `2`, because we already defined the day 1 values of `suceptible`, `infectious`, and `recovered` in Exercise 2.
    
    * Inside the *for* loop, we are appending a new value to the end of the `susceptible`, `infectious`, and `recovered` vectors. We already provided an example of the code for `susceptible` above - you will need to modify this for the other two categories of people to match their equations.
    
      Remember that the square brackets, `[]`, allow us to access a single value from a vector based on its position, e.g. `some_vector[5]` would get the 5<sup>th</sup> value of a vector. This is called *indexing* into the vector. If a variable (such as `day`) contains a number, you can use that variable to index into the vector (`susceptible[day]`) and you can even do math inside the square brackets (e.g. `susceptible[day-1]` to get the previous day's value from the vector).
    
    By the end of your loop's iterations, each of the `susceptible`, `infectious`, and `recovered` vectors should **contain 250 items**. There should not be any missing data in any of the vectors.
    
    When you have got this code working, commit your code.


4. Combine the 4 vectors (`days_range`, `susceptible`, `infectious`, and `recovered`) into a tibble (i.e. a dataframe) and store it in a new variable called `covid_sim`.

    Commit your code.

5. 
    i. Using the `covid_sim` dataframe, create a line graph (i.e. with the `geom_line()` function) that shows a different colored line for each of the three count variables (`susceptible`, `infectious`, and `recovered`), plotted against the `days_range` variable on the x-axis.
    
      To do this, you may wish to use the color argument of the `aes()` function to break the dataframe up by a categorical variable into a colored line for each count - however, this may require you to first reshape the 3 count columns into the tidy data format.

      Add a title and axis labels to your graph using the `labs()` function. You can add this function onto the end of a *ggplot* graph like this:
   
      ```r
      ggplot(...) +
        geom_FUNCTION(...) +
        labs(
          title = "A title for the graph",
          x = "x-axis label",
          y = "y-axis label"
        )
      ```
       
      The title of any graph should be a succinct description of the data the graph is showing, e.g. "Simulated COVID-19 cases from SIR model".
   
      The x and y axes should describe the variable (and unit, if necessary) plotted on this axis. Sometimes the default label may be fine; other times you may need to change it manually. For example:
       
      * the y axis in this graph might say "Counts" or "Number of People"
      * the x axis might say "Days in USA"

    ii. Describe the trends shown in the graph you plotted in the previous exercise. What can you say about the numbers of susceptible, infectious, and recovered people over time, according to our simple model?
    
    When you have finished both parts of this exercise, commit your code and answers.

6. When creating simulation models, we usually want to know how accurate their predictions are. There are two important steps to do this: *validation* and *sensitivity analysis*.

    > #### Some definitions
    >
    > * **Sensitivity analysis** is the process of determining how much the simulation's output changes if we change one of the inputs. This is necessary because mdels are often quite sensitive to the parameters that we choose and we often have some uncertainty in parameters that we are estimating from the real world.
    >
    >     For example, the SIR model can be quite sensitive to how many people each infected person infects before they recover (in epidemiology this is called the *reproduction number*, and in the SIR model is is controlled by the values of &beta; and &gamma;).
    >
    >     **Validation** is the process of comparing the simulation model's predictions with real-world data, and seeing how closely they match. Obviously we would prefer that there is a good match! If there is not a good match, it indicates that our model is doing a poor job of capturing the complexity of the real world.

    In this exercise we will *validate* our model against the actual COVID infection data from the USA. This data was loaded in the set-up chunk at the start of the `sir_model.Rmd` file and stored in a variable called `covid_real`.
    
    The `covid_real` dataframe contains the following columns:
    
    | Column name | Description |
    | ----------- | ----------- |
    | `date`      | The date of the observation |
    | `country`   | The country of the observation (the US for all rows in this dataset) |
    | `cumulative_cases` | A cumulative total of all cases in the USA by that date (includes people since recovered or deceased) |
    | `recovered` | The number of people who had recovered from COVID-19 |
    | `deaths` | The number of people who had died from COVID-19 |
    | `current_cases` | The number of people who had an active case of COVID-19 on that day (who had not yet recovered or died) |
    | `days_in_country` | Number of days since the disease entered the country |
    
  
    i. Plot a line graph showing the number of simulated and real cases of COVID-19 over time. This should be a single graph with 2 lines:
        
      * One line should plot `infectious` (y-axis) vs. `days_range` from the `covid_sim` dataframe.
        
      * The second line should show `current_cases` (y-axis) vs. `days_in_country` from the `covid_real` dataframe.
        
      You will need a separate `geom_line()` function for each line.
    
      * You can use multiple datasets in a single ggplot by using the `data = ...` argument within a geom function to override any default dataset that was passed to the `ggplot()` function. For example, here we are plotting 2 histograms, each using a different dataset:
        
          ```r
          ggplot(data = default_dataset) +
            geom_histogram() +                # uses default data
            geom_histogram(data = dataset_2)  # uses a 2nd dataset
          ```
          
          (You can also provide all datasets to their respective geom functions directly and skip the default dataset in the `ggplot()` function.)
        
      * Despite the fact we are using two geom functions, we can force ggplot to automatically color the lines and create a legend by adding the `color` argument *inside* the `aes()` function with a fixed character value as a label, e.g. `color = "simulation"`
        
          (Note that this is an exception to the usual rule that fixed parameters should go *outside* the `aes` function.)
        
      * Make sure to give the graph an appropriate title and axis labels using the `labs()` function.
        
    ii. Describe how the SIR model's predictions compare to the real data.
    
    
    Commit your work at the end of this exercise.

7. The spread of an epidemic is an example of *exponential growth*. Exponential growth happens when the rate of change of a variable is related to how much of the variable is currently there.

    Exponential growth can start slowly, but will eventually become incredibly rapid. Viral diseases such as COVID-19 often spread exponentially; other examples include your bank balance in a savings account (or, less positively, your debt on a credit card), growth of a population (if unchecked), and atomic fission in a nuclear chain reaction (such as in a nuclear bomb or power plant).
    
    It can be hard to plot exponentially growing variables on a graph because the variable will contain values that are *orders of magnitude* in difference. One solution is *log transform* the exponentially growing variable, i.e. plot the logarithm of the variable.
    
    As this is a common problem, the `ggplot2` package includes useful functions to scale variables when plotting them, such as the `scale_y_log10` and `scale_x_log10` functions.
    
    *Copy your graph code from the previous exercise* into a new code chunk, and add this function to the end of the graph expression to log transform the y-axis (and adjust the axis tick formatting):
    
    ```r
    ... +
      scale_y_log10(labels=trans_format('log10', math_format(10^.x)))
    ```
    
    The transformed graph shows allows us to more easily compare the number of cases in the early days of the pandemic, when the number of cases was relatively low. You should notice that our SIR model currently predicts a slower spread of COVID-19 in the early days of the pandemic (i.e. days 1-60).
    
    It is not surprising that the case counts diverge around day 60 - this corresponds to mid March, when many people and organizations in the USA began to take the disease seriously and take measures to slow the spread of the disease (e.g. Mason switched to online instruction around this time).
    
    Our model does not take these social distancing strategies into account - however, our validation graphs show that we might be able to tweak our model's parameters to get better predictions early in the pandemic.
    
    Commit your work when you have finished this exercise.

8. When our validation shows that the model does not fit real data well, we often want to update the model to make it more accurate.
    
    The process of iteratively validating, adjusting, and then re-validating a model is called **calibration**.

    Let's see if we can calibrate our model a little better to the real world data.
    
    Copy your earlier code and rerun it with a different value of the infection rate, &beta;. Here are the particular steps you should follow:
    
    * Make one new code chunk - we will copy all the code into this chunk so that we can re-run our entire model by running just one code chunk.
    
    * Copy the code from exercises 1-4 into this single code chunk. Pick a new value for &beta; (beta).
    
    * Copy the graph code from Exercise 7 into the same chunk.
    
    * Rerun your model with different values of beta until the early parts of the simulated and real data lines match more closely (the first 60 days). Try values of `beta` between 0.125 and 0.5 (you do not need to adjust any of the other parameters).
    
    Epidemiologists use a value called the *reproduction number* to describe how quickly a disease can spread through a population. In our simulation, the reproduction number is equal to `beta` divided by `gamma` (i.e. &beta; / &gamma;).
    
    What is the reproduction number that you have found allows the SIR model to fit the first 60 days of the US COVID-19 pandemic?
    
    When you have finished this exercise, commit your work.


## Complete the Academic Integrity statement

Complete the academic integrity statement indicating whether you used an AI tool or not, in the same fashion that the Assignment 4 instructions taught you to do.

Don't forget that if you did use an AI tool, you need to submit the transcript of your conversation (either by creating a shareable link to the conversation, or by submitting an additional file to Blackboard that contains the full text of your conversation).

* How to create a shareable link to a ChatGPT conversation: [video tutorial](https://www.loom.com/share/7714b7c73d00411b89b5ec49f6393cb5?sid=28d4fc90-be7d-4170-8623-cc5261fed8f0)

## How to submit

To submit your assignment, follow the two steps below.
Your assignment will be graded for credit **after** you've completed both steps!

1.  Save, commit, and push your completed R Markdown file so that everything is synchronized to GitHub.
    If you do this right, then you will be able to view your completed file on the GitHub website.

2.  Knit your R Markdown document to the PDF format, export (download) the PDF file from RStudio Server, and then upload it to *Assignment 5* posting on Blackboard.

## Cheatsheets

You are encouraged to review and keep the following cheatsheets handy while working on this assignment:

*   [What graph should I make?][what-graph]

*   [dplyr cheatsheet][dplyr-cheatsheet]

*   [ggplot2 cheatsheet][ggplot2-cheatsheet]

*   [RStudio cheatsheet][rstudio-cheatsheet]

*   [R Markdown cheatsheet][rmarkdown-cheatsheet]

*   [R Markdown reference][rmarkdown-reference]


## (Optional) Further reading and activities

As we are currently living through the COVID pandemic, you may be interested in extending or playing around with this model for your own personal interest. **Everything below this point is not part of the assignment and should not be submitted**, but is given for the interested student who wishes to learn more about this topic.

First, Our World In Data has a [great page of visualizations](https://ourworldindata.org/coronavirus) of COVID-19 data for many other countries.

The CDC aggregates a number of models predicting COVID cases in the USA. [As you can see](https://www.cdc.gov/coronavirus/2019-ncov/cases-updates/forecasts-cases.html), even these professional models disagree widely about the future spread of the disease.

Epidemiologists have used more complicated versions of the SIR model to study COVID. One improvement is to add more categories of people:

* The SEIR model adds a 4th compartment of people, "exposed", that is between susceptible and infectious (you enter this compartment after catching the disease - once you become infectious to others, you move to the infectious compartment).

* You can even more compartments than 4 - many of the most advanced models have 10 or more compartments! If you wish to do so, you might want to look into the `EpiModel` package - [overview here](https://rviews.rstudio.com/2020/03/19/simulating-covid-19-interventions-with-r/).

* A key part of many of these models is the reproduction number, which you can read more on [here](https://www.cebm.net/covid-19/when-will-it-be-over-an-introduction-to-viral-reproduction-numbers-r0-and-re/).

You could also make the code you wrote in this assignment more complicated by allowing the infection rate, &beta;, to vary over time. The value we used was estimated from the early rate of spread of the disease in January/February, before there was significant social distancing and lockdowns. What happens to the spread of the disease if you "implement" social distancing in your model after a certain period of time (e.g. from mid-March onwards) and reduce &beta; to less than 0.125 (i.e. less than the recovery rate)?

Finally, a lot has been written on how we should interpret the many COVID models that have been made over the course of 2020. You will find interesting discussions in these articles:

* [Why it's so freaking hard to make a good COVID-19 model](https://fivethirtyeight.com/features/why-its-so-freaking-hard-to-make-a-good-covid-19-model/) - FiveThirtyEight

* [Wrong but Useful — What Covid-19 Epidemiologic Models Can and Cannot Tell Us](https://www.nejm.org/doi/full/10.1056/NEJMp2016822) - New England Journal of Medicine - also contains a nice discussion of the difference between statistical and mathematical ("mechanistic") models for disease modeling.

[what-graph]:           https://drive.google.com/file/d/1zsedQ9kHFtxhFE3R99PQ1a_yTTamJu3e/view?usp=sharing
[dplyr-cheatsheet]:     https://gmuedu-my.sharepoint.com/:b:/g/personal/dwhite34_gmu_edu/ESQlogUDLfpNiXc3cD40crwBz0C0zfESw-6jRwTrHT4UBA?e=bQKhzS
[ggplot2-cheatsheet]:   https://gmuedu-my.sharepoint.com/:b:/g/personal/dwhite34_gmu_edu/ESLxplzb1sdLszfqs3208G0BdScfSbNqrikzJ1pIKczsFw?e=cwYcjM
[rstudio-cheatsheet]:   https://gmuedu-my.sharepoint.com/:b:/g/personal/dwhite34_gmu_edu/EVAQYYLorhxPh49NdlZV4KgBNNBQHRdJNthHK0ZuID8_Gw?e=dfzJPt
[rmarkdown-reference]:  https://gmuedu-my.sharepoint.com/:b:/g/personal/dwhite34_gmu_edu/Ed4VQ0-6mEhBp2IkjIdGDK0BwaR9BDzEnpnVyyxDn_gasg?e=1eLHsa
[rmarkdown-cheatsheet]: https://gmuedu-my.sharepoint.com/:b:/g/personal/dwhite34_gmu_edu/ETKKUWqePhRJv-VvAOsg4F4BPte7yKfQJKyyr1gNMg46yQ?e=hJPHXV

