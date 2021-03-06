# R for Pharmaceutical Sciences

The book can be built via:

```
rmarkdown::render_site()
```

This is automatted in newer version(s) of Rstudio in the 'Build' tab near Environment/History/Git, when opening this through the `r-pharmsciences.Rproj` file.

The ordering is managed via `_output.yml`


As mentioned in the `index.Rmd`:


This book is a sum of pouring over dozens of books, countless blogs, and many projects as I developed my own R skills and toolbox. While there are extensive resources available to learn R, two patterns I noticed were: a) most material was very general, forcing a constant 'translation' into datasets or problems faced when dealing with problems specific to pharmaceutical sciences (and in my case, specifically, pharmacometrics) and b) a lot of the scientific examples are quite outdated or take the 'long way around' to get to a solution. This is my attempt to modernize and inform by bringing together the core topics I believe can make someone a successful and productive user of R. 


## How to read this book

When writing this book I have made a conscious decision to introduce
topics in a pragmatic way to keep those new to programming engaged. In essence, I want people to get 'up and running' as quickly as possible to actually doing things like data visualization, data manipulation, etc. Hence, during the first read-through there may be some sections or pieces of code that feel like 'magic', or may not fully make sense. Bear with me, hopefully all those will get addressed in later sections such as 'Core R' and 'Thinking in R'. This way, by the time you are introduced to some topic like 'what attributes are maintained when adding a vector to a data frame)', there will be a foundation of examples that can help provide additional context to the topics and reduce the feeling of trying to digest all the topics in the abstract. 

That said, for more experienced programmers, coming from other languages such as python, javascript, c/c++, java, etc, there may be appreciation in getting up to speed to connect concepts they already know how to perform in one language to how R behaves. If you fall into that category, I would suggest perusing through 'Core R' and 'Thinking in R' either before or in line with the rest of the book as it will help provide a deeper connection to the data structures and functionality available in R to your prior experience(s).

Even more importantly, it does not make sense for me to re-explain the wheel when Hadley has done such a fantastic job, so I defer to explanations of the finer details of how R works, especially 'under the hood', to his book [advanced R](http://adv-r.had.co.nz/). This book should be 'required' reading to better appreciate how to master R.

This work is licensed under the [Creative Commons Attribution-NonCommercial-NoDerivs 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/us/) United States License. 


Rebuild the book for publishing on github via:

```
git checkout --orphan gh-pages
git rm -rf .

# create a hidden file .nojekyll
touch .nojekyll
git add .nojekyll

# move to top level so github picks up index.html
mv _book/* .

git commit -m "update book"
git push origin gh-pages
```