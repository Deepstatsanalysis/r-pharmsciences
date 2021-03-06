
# Control Structures 

Control structures offer the ability to control how your code executes and what
is returned. R was designed to abstract away many of the basic use-cases so you 
can get a long way without ever explicitly using some of these constructs, however, both the concepts and techniques are vital to appreciate as more complex
problems arise.

## IF statements

`if` statements are the most basic control structure, and have the structure and syntax:

```
if (<condition>) {
## do something
}
```

One basic use case is when doing clinical trial simulations where many reps are
run, it can be useful to only occasionally print status updates. In pseudocode
the objective would be to have code that looked like


```r
for (rep in 1:50) {
  #every 10 reps print a status update
  if(rep %% 10 == 0) {
    # %% is the modulus operator, a nice way to detect every 10th rep
    print(paste("starting rep", rep))
  }
  # run some more code
}
#> [1] "starting rep 10"
#> [1] "starting rep 20"
#> [1] "starting rep 30"
#> [1] "starting rep 40"
#> [1] "starting rep 50"
```

Another common pattern is to handle certain scenarios in a function, again using
print (since it is easy to demonstrate) for whether a function prints out a message
before returning the values.


```r
chatty_sum <- function(a, b, verbose=FALSE) {
  if(verbose) {
    print(paste("calculating the sum of a:",a, "and b:", b))
  }
  return(a+b)
}
chatty_sum(1, 3)
#> [1] 4

chatty_sum(2, 4, verbose=TRUE)
#> [1] "calculating the sum of a: 2 and b: 4"
#> [1] 6
```

The `chatty_sum` function also illustrates another concept about `if` statements,
**they are inherently truthy**, which means that if the condition you are evaluating naturally resolves to be `TRUE` or `FALSE`, you do not need to explicitly evaluate it. In this case, an example is worth a thousand words:

*Correct way*

```
condition <- TRUE

if (condition) {

}

```

*INCORRECT way*

```
condition <- TRUE

if(condition ==TRUE) {
# do stuff
}
```

It is key to understand the inherent truthiness of **if** statements to further extend their usefulness. For example, if you need to execute the code inside the
if statement when the tested condition is **FALSE** you can do-so using the `!` (often pronounced as 'bang') operator, which means *not*, so we are saying we want to run the code when it
is **not FALSE**, which is equivalent to **TRUE**


```r
good_condition <- FALSE
if(!good_condition) {
  print("bad news!")
}
#> [1] "bad news!"
```

## ELSE

if statements are powerful to testing single conditions, however in may situations it is valuable to to also do something different if the condition is NOT met. 

In **select** situations you may want to cover each of these scenarios via multiple if statements

```
if (<condition1>) ## do something
if (<condition2>) ## do something
if (<condition3>) ## do something
```

However this is generally frowned upon as it makes the code
harder to make sense of, and easier to have unexpected conditions when a later condition is also TRUE, thereby overwriting a the results of a earlier TRUE condition.


Instead, the `else` block offers a separate chunk of code which will only run
if the if condition is FALSE.

```r
if (<condition>) {
## do something
} else {
## do something else
}
```

This can be further expanded to handle multiple conditions, however the value of
this technique is only the code in the block with the *first* block that evaluates to TRUE will be run, and if no blocks are TRUE, then the code in the else block will run. 

```r
if(<condition>) {
    ## do stuff
} else if (<conditition2>) {
    ## do other stuff
    } else {
    ## do something for any other condition
}
```

## ifelse

Full if/else blocks can be tedious to write out, and do not always play well with
R's vectorization principles. Hence, a 'shortcut' block, `ifelse` is also available. 
`ifelse` is most likely going to be your mostly frequently used control statement used during the data analysis process so make sure it is well understood!

`ifelse` blocks are designed with the format `ifelse(<test condition>, <if yes>, <if no>)`


```r

gender <- c("Male", "Male", "Female")

ismale <- ifelse(gender == "Male", 1, 0)

gender
#> [1] "Male"   "Male"   "Female"
ismale
#> [1] 1 1 0
```

In the above block, the test is saying, go through the **gender** vector, and for each
element, test if that value is equal to "Male", then return a value of 1, and if it is **anything other than Male**, return 0. Since this is performed for each element separately, it will return a vector the same length as the tested vector.

You can even nest `ifelse` statements


```r
race <- c("white", "black", "hispanic", "asian", "alien")

race_num <- ifelse(race == "white", 1, 
                   ifelse(race == "black", 2, 
                          ifelse(race == "hispanic", 3, 4)))
race
#> [1] "white"    "black"    "hispanic" "asian"    "alien"
race_num
#> [1] 1 2 3 4 4
```

Notice that in this technique if none if the `TRUE` conditions are met, it will be assigned the value `4`. This is a good practice to make sure you catch all possible values. 
The last false value should always be used to handle 'all other conditions'. 

For example


```r
race <- c("white", "black", "hispanic")

## bad way
race_num <- ifelse(race =="white", 1, 
       ifelse(race == "black", 2, 
              3)) # expect 3 to be hispanics
race_num
#> [1] 1 2 3
```

Works as expected, however if you accidentally miss a condition or other conditions are present


```r
race <- c("white", "black", "hispanic", "asian", "alien")
race_num <- ifelse(race =="white", 1, 
       ifelse(race == "black", 2, 3)) 
race_num 
#> [1] 1 2 3 3 3
```

You can silently get unexpected assigned values.

One technique that can be used is to use the final condition as `-99` or some other
very obvious flag to check that you captured all conditions


```r
race <- c("white", "black", "hispanic", "asian", "alien")
race_num <- ifelse(race =="white", 1, 
       ifelse(race == "black", 2, 
              ifelse(race == "hispanic", 3, -99))) 
race_num 
#> [1]   1   2   3 -99 -99

if(any(race_num < 0)) {
  print("missed a condition")
} else {
  print("handled all")
}
#> [1] "missed a condition"
```

And it even makes it easy to subset out the conditions missed to find and correct them


```r
race[race_num == -99]
#> [1] "asian" "alien"
```

see that missed alien and asian


```r
race <- c("white", "black", "hispanic", "asian", "alien")
race_num <- ifelse(race =="white", 1, 
       ifelse(race == "black", 2, 
              ifelse(race == "hispanic", 3, 
                     ifelse(race == "asian", 4, 
                            ifelse(race == "alien", 5, -99)
                            )
                     )
              )
       )
race_num 
#> [1] 1 2 3 4 5

if(any(race_num < 0)) {
  print("missed a condition")
} else {
  print("handled all")
}
#> [1] "handled all"
```


## Loops

* `for` looping structure

Loops can be constructed based on a specified vector length or by specific indices

```
for (i in 1:5) {
    store_results[i] <- do_something()
}
```

While you may be more familiar with the construct:

```
for(i in 1:length(x)) {
     results[i] <- do_something(x[i])
}
```

This is actually a "bad" habit that can run you into trouble with objects of length(0)

`seq_along` is a 'safer' option that has the exact same effect if you're starting from the first indice, with the added benefit of failing more gracefully.


* `next` - skip iteration of loop

`next` can be used to skip iterations in a loop, so as soon as a `next` is seen, the for
loop moves to the next iteration and will ignore any code remaining from the existing iteration.

This is useful if you are checking a condition at the beginning and if that condition is met
go on so you don't run extra unnecessary code.


## more For loop information

Given a for loop where certain elements you do not want anything to happen,
the `next` keyword allows one to immediately go back to the top of the loop and start
with the next indice. 


```r
for(i in 1:10) {
    if (i < 3) next
    print(i)
}
#> [1] 3
#> [1] 4
#> [1] 5
#> [1] 6
#> [1] 7
#> [1] 8
#> [1] 9
#> [1] 10
```

This is convenient if there is additional code below that you do not want R to run/evaluate
given certain conditions are met, without having to resort to complex or nested `if` statments.

`break` break the execution of a loop. Unlike next, this will actually halt the loop completely 
and procede on to any later code after the loop.


```r
## some code

for (i in 1:10) {
  start <- i
  print(paste0("before break, rep: " ,  i))
  if (i == 5) {
    break
  }
  print(paste0("after break, rep: " ,  i))
  finished <- i
}
#> [1] "before break, rep: 1"
#> [1] "after break, rep: 1"
#> [1] "before break, rep: 2"
#> [1] "after break, rep: 2"
#> [1] "before break, rep: 3"
#> [1] "after break, rep: 3"
#> [1] "before break, rep: 4"
#> [1] "after break, rep: 4"
#> [1] "before break, rep: 5"
```

In the above example, we see a conditional check where if `i == 5` break out of the loop.
As the print statements show, only 4 values are printed for `after break`, as
the last rep that executed code to the end of the loop was `4`, however you can see
that the fifth replicate did start, and executed code up until the `break` statement.

Finally, in the context of a function, a for loop (or any other code) can be prematurely
completed by using a `return` statement.

`return` - exit function


```r
number_is_present <- function(nums, test_number) {
  for (i in seq_along(nums)) {
    if(i == test_number) {
      print("number found!")
      return(TRUE) 
    }
  }
  print("completed scanning, number not found!")
  return(FALSE)
}

number_is_present(1:10, 5)
#> [1] "number found!"
#> [1] TRUE

number_is_present(1:10, 12)
#> [1] "completed scanning, number not found!"
#> [1] FALSE
```

In the example above, it makes sense to not continue scanning more numbers if we already know
that the number is present, so by returning as soon as the number is detected, we prevent
the need to continue running the function to completion. 

## While loops

* `while` - execute loop *while* tested condition is true. Often used if you need to evaluate until a certain condition is met. **danger**: can potentially result in infinite loops if not written properly or if the tested condition is never met.


```r
count <- 0
while(count < 10) {
    print(count)
    count <- count + 1
}
#> [1] 0
#> [1] 1
#> [1] 2
#> [1] 3
#> [1] 4
#> [1] 5
#> [1] 6
#> [1] 7
#> [1] 8
#> [1] 9
```

While loops are frankly not used often for data analysis tasks.
In most cases, a for-loop, the in-built apply functions, or using vectorization 
is be preferable. While loops can be valuable for instances when you are unsure about 
when the final criteria required will be satisfied. For example, given a resampling function
that you must resample from a mathematical distribution, however want to apply some other physiological constraints such that some samples might need to be resampled again before being stored.

A simple example of this is, given simulations from a truncated normal distribution, where only values greater than 0.1 should be kept.

A couple different ways this could be mentally managed


```r
# while loop inside a for loop
result <- c()
for (i in 1:10) {
  sample <- 0
  while (sample < 0.1) {
    ## will always fire the first time per rep since at first sample will be 0
    print(paste0("sample less than 0.1, resampling on rep", i))
    sample <- rnorm(1, mean = 0.5, sd = 2)
  }
  result <- c(result, sample)
}
#> [1] "sample less than 0.1, resampling on rep1"
#> [1] "sample less than 0.1, resampling on rep1"
#> [1] "sample less than 0.1, resampling on rep2"
#> [1] "sample less than 0.1, resampling on rep2"
#> [1] "sample less than 0.1, resampling on rep3"
#> [1] "sample less than 0.1, resampling on rep4"
#> [1] "sample less than 0.1, resampling on rep5"
#> [1] "sample less than 0.1, resampling on rep5"
#> [1] "sample less than 0.1, resampling on rep5"
#> [1] "sample less than 0.1, resampling on rep5"
#> [1] "sample less than 0.1, resampling on rep5"
#> [1] "sample less than 0.1, resampling on rep5"
#> [1] "sample less than 0.1, resampling on rep6"
#> [1] "sample less than 0.1, resampling on rep7"
#> [1] "sample less than 0.1, resampling on rep7"
#> [1] "sample less than 0.1, resampling on rep8"
#> [1] "sample less than 0.1, resampling on rep8"
#> [1] "sample less than 0.1, resampling on rep8"
#> [1] "sample less than 0.1, resampling on rep9"
#> [1] "sample less than 0.1, resampling on rep10"
#> [1] "sample less than 0.1, resampling on rep10"

# in the end get our nice set of 10 results
result
#>  [1] 1.011 0.489 1.743 2.797 1.758 4.630 1.525 0.395 1.586 1.436
```

The problem with this result is it reallocates a new vector each time a new sample is concatenated,
so for large numbers of samples will be quite slow. Instead, we can also manage via vectorization. Since we are going to do all the calculations at once, we need to change our logic in how we are resampling.


```r
result <- rnorm(10, 0.5, 2)
while(any(result < 0.1)) {
  print("at least 1 result still below 0.1, resampling those values") 
  # find which indices correspond to values less than 0.1
  to_low <- which(result < 0.1)  
  # replace them with resampled values
  result[to_low] <- rnorm(length(to_low), 0.5, 2)
}
#> [1] "at least 1 result still below 0.1, resampling those values"

result
#>  [1] 1.226 0.853 1.976 4.277 0.305 0.987 0.468 3.747 0.724 2.371
```



```r
looped_resampling <- function(total_num = 100) {
  result <- c()
  for (i in 1:total_num) {
    sample <- 0
    while (sample < 0.1) {
      ## will always fire the first time per rep since at first sample will be 0
      message(paste0("sample less than 0.1, resampling on rep", i))
      sample <- rnorm(1, mean = 0.5, sd = 2)
    }
    result <- c(result, sample)
  }
  result
}

vectorized_resampling <- function(total_num = 100) {
  result <- rnorm(10, 0.5, 2)
  while(any(result < 0.1)) {
    message("at least 1 result still below 0.1, resampling those values") 
    # find which indices correspond to values less than 0.1
    to_low <- which(result < 0.1)  
    # replace them with resampled values
    result[to_low] <- rnorm(length(to_low), 0.5, 2)
  }
  result
}
```



```r
library(dplyr)
#> 
#> Attaching package: 'dplyr'
#> The following objects are masked from 'package:stats':
#> 
#>     filter, lag
#> The following objects are masked from 'package:base':
#> 
#>     intersect, setdiff, setequal, union
library(tibble)
res <- suppressMessages(microbenchmark::microbenchmark(
  looped_resampling(),
  vectorized_resampling(),
  times = 20L
))
res_df <- data_frame(expr = res$expr, timing = res$time)

res_df %>% group_by(expr) %>% 
  summarize(min = min(timing), 
            mean = mean(timing),
            max = max(timing))
#> # A tibble: 2 × 4
#>                      expr      min     mean      max
#>                    <fctr>    <dbl>    <dbl>    <dbl>
#> 1     looped_resampling() 37744495 44265336 52146784
#> 2 vectorized_resampling()   234257   821522  1856040
ggplot2::autoplot(res)
```

<img src="control-structures_files/figure-html/unnamed-chunk-19-1.png" width="672" />


## Assignments

* within theoph add a new column called `ISEVEN` - for all subjucts with even ID numbers assign a value 1, for all odds assign value 0

* write a creatinine clearance calculator function - use it to calculate the CRCL for some simulated data in a vectorized manner
