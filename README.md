# pastR


An language that transpiles to R.
--


Let's face it.  R looks ugly.  It hurts your eyes.  Some features can be a bit verbose or overly confusing.  Official R hasn't really changed in the last 20 years.  Some things need the tiniest changes.  It could use a couple tweaks:

1) piping
2) shorten ```function``` to ```fn```
3) combine all ```apply``` functions, don't get rid of the old ones.  
4) ```v()``` for vector. Don't get rid of ```c()```.
5) transpile statements to data.table syntax


The only changes for the user will be easier to read code, faster to write code, code that makes more sense,  easier to remember base functions.


None of the changes require the user to change how they code.  They don't have to learn a new language.


This drasticly improves speed and reduces memory limitations.  Any existing R code can be converted to pastR.



Examples
------------

Geocoding with 4 GB RAM

In pastR:

Mostly point-free
```
xyData <- "/Lib/etc/why_is_there_data_here.csv" |> read

coord <- xyData |> geoCode
    |> transform1
    |> apply( fn(x)  v(x[,1],x[,2]), ., 'vector')
    |> transform3 |> transform3
    |> as.Doc.Loc
```

With paranthesis

```
xyData <- read("/Lib/etc/why_is_there_data_here.csv")

coord <- xyData 
    |> geoCode(.) 
    |> transform1(.)
    |> apply( fn(x) v(x[, 1], x[, 2]), ., 'vector')
    |> transform3(.)
    |> transform3(.)
    |> as.Doc.Loc(.)
```
Quiz: What data type is coord\[\["doc_loc"]]



Currently:

```
library(data.table)

xyData <- fread("/Lib/etc/why_is_there_data_here.csv", sep=',', colnames = c(x, y))

xyData[, loc_vec := c(x, y), ]
xyData[, trans1_vec := sapply(loc_vec, function(z) sapply(y, function(g) transform1(c(z, g))), ]
xyData[, loc_vec := NULL, ]
xyData[, trans2_vec := transform2(trans1_vec), ]
xyData[, trans1_vec := NULL, ]
xyData[, trans3_vec := transform3(trans2_vec), ]
xyData[, trans2_vec := NULL, ]
xyData[, doc_loc := as.Doc.Loc(.), ]
xyData[, trans3_vec := NULL, ]

coord <- xyData[, .(doc_loc), ]

rm(xyData)
```
Quiz: What data type is coord\[\["doc_loc"]]


Reasoning
--

1)  Replace ```%>% ```with ```|>``` because it's easy to type, fast to type, and makes code beautiful.  Have you seen Elixer code?    The syntax has to change very little for those familiar with the ```%>%``` oporator from magrittr.  A couple quirks need to be fixed.  ```%>%``` only appends to the end of the statement.  ```|>``` should be able to work when placed at the beginning of the next line.  This requires only a simple change.


2) Replace ```function([args]) { }``` with ```fn([args]) { }```, or something else simple, sweet, and obvious.  This requires basically nothing.  It's 1 - 1.  Requiring the evaluation syntax for anonomous functions, `([fn])`, adds an unnecessary level of specification.  Watch:

-- M-Expression ```fn([arg]) (statements)``` == S-Expression ```(fn([args]), statement)```

-- There's no need for ```(fn([arg]) statements))``` because that's like writing ```((fun([args]) statements)))```.  Requiring another paranthesis for anonomous functions makes no sense. 

-- When you don't want to evaluate, the R syntax is ```fn([args]) { statements}```. While we're at it, why not allow beta reduction syntax ```(fn statement)[args]```.  It's the logical heart beneath functional languages like R.


3) ```[letter]apply``` must change.  Myriad varients of ```apply``` turn one of the best features of R into one of the most arcane.  ```apply``` fits perfectly into a vector language.  People with different programming backrounds and people learning R as a first language struggle with the idea of working with a bunch of things at the same time.  It makes sense if you say, "That is your bunch of values.  That is your function.  All you have to do is ```apply``` the function to all of the values."  R requires you specify you specify the output by choosing from a bunch of awkwardly named ```apply``` functions.  The 's' in ```sapply``` probably makes sense given the development of S, but what the hell does 's' have to do with vectors?!  Why not-- this may sound radical, the weak stomache among you can skip this section--- you specify the the type as arguments in the function.  Input type specification can be optional.  Put in a list and want a vector?  ```apply(fn, [list], 'vector')```.  While we're at it, ```mapply``` or ```Map```: choose one.  Why do ```sapply```, ```lapply```, and ```vapply``` exist as separate functions?  This is why arguments exist.  There's much more nonsense in the species of apply, but one more thing.  Why separate mapply from other apply functions?  Why can't all of them apply to as many arguments as long as the function has many arguments?


The way R uses ```apply``` is like buying a bunch of toolboxes for a screw-driver set instead of putting the screw-drivers into one toolbox.  This entails writing a wrapper for the ```apply``` functions in pastR.


4) Speaker of wierd token choices: 'c()' for vector?  That makes sense to almost no one.  How about 'v' is for vector?  ```v(1, 2, 3)```, or ```vector(1, 2, 3)```.  Maybe allow people to use 'l' for list?  ```l('a', 'b', 'c')``` while also allowing the verbose ```list('a', 'b', 'c')```


4) R shouldn't require learning data.table when dealing with large data sets.  In the 1990s, dealing with large data sets wasn't a problem.  The technology wasn't there to make it as easy as it is now to work with a gigabyte of data.  Base R sufficed in the majority of data sets.  Now, base R and tidy require absurd amounts of memory and computational power to deal with large data sets.   data.table integrates good memory management and parallelism without the user needing to look at the number of cores in their system.  A package like Rcpp shouldn't have to exist.  You might as well write pure C++.  R will become useless if it can't handle large data sets.  People are jumping ship to use Julia.  That's not a bad language.  But the people who use R don't want, and don't have the time, to learn an entirely new, and honestly more difficult language.   A simple solution is integrating data.table. The package doesn't require the user to implement the multi-threading, other methods of parallelization, or optimize with Rcpp.


The big problem comes from data.table basically being a DSL that does what R does but requries the user to master a non-base R mode of reasoning.  Several people have begun writing wrapper packages.  This doesn't need to be a comprehensive implementation of data.table.  Transpile base R into data.table even if data.table has more features.




Issues
----

Other packages build off of ```%>%```.  future does this especially well.  This isn't a huge deal for the most part.  future's future notation becomes ```.|>```  The transpiler needs to recognize mods to ```%>%``` and transpile accordingly.


pastR must run as a rStudio plugin.  No one will ever use it otherwise.


It requires transpiling at runtime.  I has to work every time you hit enter in the REPL.


```%>%``` slows down execution. It's not a compile-time macro. ```|>``` shouldn't slow down execution.


The major challenge comes in adding the data.table back end.  Perhaps they can work with us.


Restrictions
-----------------

This needs to transplile to R.  It needs to resemble R in almost all ways.

R has awesome metaprogramming abilities. Leave those alone.

R hurts your hands when you use it.  In makes you stretch all over the keyboard.  Coding is so much faster if your hands don't have to leave the home row.  So no replacement for ``` %in% ``` with ``` #in# ```.  That's atrocious.  Using the workman layout, the home row has these accessable without moving ones hand:


```
]

[


}

{


<

>

/

\

|


'

"

~
`
, 

.

``` 

All the letters.  Numbers require moving ones hands.  So no numbers.


Some things to ponder
---

- purrr:flatten should be a base R feature.  Add it to base pastR

- ``` %in% ``` also needs an equivolant in the basic pastR specs.

- R string handling is terrible and counter intuitive.  Why not offer some simple alternative to regular expressions.  How many scientist have mastered regular expressions.

- declare an alias for a package namespace.  I find it good practice to be explicit.

- declare alias for functions. Right now you can do that as a hack by putting a function in another function.  That slows things down.  Python uses it sucessfully.



The name
---

pastR sounds like the Albanian word for clean

