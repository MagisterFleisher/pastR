# pastR

An language that's almost R.  It's R, but clean.
--


Let's face it.  R looks ugly.  It hurts your eyes.  Some features can be a bit verbose or overly confusing.  Official R hasn't really changed in the last 20 years.  Some things need the tiniest changes.  It could use a couple tweaks:

1) piping
2) shorten '```function```'
3) combine all ```apply``` functions
4) transpile some statements to data.table syntax


Reasoning
===


First, replace ```%>% ```with ```|>``` because it's easy to type, fast to type, and makes code beautiful.  Have you seen Elixer code?    The syntax has to change very little for those familiar with the ```%>%``` oporator from magrittr.  A couple quirks need to be fixed.  ```%>%``` only appends to the end of the statement.  ```|>``` should be able to work when placed at the beginning of the next line.  This requires only a simple change.


Second, replace ```function([args]) { }``` with ```fn([args]) { }```, or something else simple, sweet, and obvious.  This requires basically nothing.  It's 1 - 1.  While we're at it.  Why not make that declaration sufficient for a lambda.  Requiring the evaluation syntax, `([fn])`, adds an unneccary level of specification.  Watch:


M-Expression ```fn([arg]) (statements)``` == S-Expression ```(fn([args]), statement)```


There's no need for ```(fn([arg]) statements))``` because that's like writing ```((fun([args]) statements)))```.  Requiring another paranthesis for anonomous functions makes no sense.  When you don't want to evaluate, the R syntax is ```fn([args]) { statements}```. (While we're at it, why not allow beta reduction syntax ```(fn statement)[args]```.  It's the logical heart beneath functional languages like R.)


Third, ```[letter]apply``` must change.  Myriad varients of ```apply``` turn one of the best features of R into one of the most arcane.  ```apply``` fits perfectly into a vector language.  People with different programming backrounds and people learning R as a first language struggle with the idea of working with a bunch of things at the same time.  It makes sense if you say, "That is your bunch of values.  That is your function.  All you have to do is ```apply``` the function to all of the values."  R requires you specify you specify the output by choosing from a bunch of awkwardly named ```apply``` functions.  The 's' in ```sapply``` probably makes sense given the development of S, but what the hell does 's' have to do with vectors?!  Why not-- this may sound radical, the weak stomache among you can skip this section--- you specify the the type as arguments in the function.  Input type specification can be optional.  Put in a list and want a vector?  ```apply(fn, [list], 'vector')```.  While we're at it, ```mapply``` or ```Map```: choose one.  Why do ```sapply```, ```lapply```, and ```vapply``` exist as separate functions?  This is why arguments exist.  There's much more nonsense in the species of apply, but one more thing.  Why separate mapply from other apply functions?  Why can't all of them apply to as many arguments as long as the function has many arguments?


The way R uses ```apply``` is like buying a bunch of toolboxes for a screw-driver set instead of putting the screw-drivers into one toolbox.  This entails writing a wrapper for the ```apply``` functions in pastR.


Fourth, R shouldn't require learning data.table when dealing with large data sets.  In the 1990s, it wasn't a problem.  base R sufficed in the majority of cases.  data.table integrates good memory management and parallelism without the user needing to look at the number of cores in their system.  A package like Rcpp shouldn't have to exist.  R will become useless if it can't handle large data sets.  A simple solution is integrating data.table into pastR.  Do what R does without learning data.table.  R already has the feature set found in data.table.  Transpile the base R provided by pastR into data.table code.  



This is basic R with maggritr and data.table. Code will look like this
------------
```
library(data.table)

xyData <-
  "/Lib/etc/why_is_there_data_here.csv"
  |> read |> clean

coord <- 
  headeData
    |> transform1
    |> apply(., fn(x, y, z),
      transform2(
      xyData[, 1], xyData[, 2], z), 
      'vector')
    |> transform3 |> transform3
    |> as.Doc.Loc
```
 
(It's mostly point-free.)

As of now:
```
library(data.table)

xyData <- 
  "/Lib/etc/why_is_there_data_here.csv" %>% 
  fread %>% clean

coord <- 
  headeData %>%
  transform1 %>%
  sapply(., function(x, y, z),
      transform2(
      xyData[, 1], xyData[, 2], z)) %>% 
      transform3 %>% transform3 %>%
      as.Doc.Loc
```
 

Another Example
----


Old
```
list(lapply(colnames(x)[3:11], function(cname)
    lapply(1:18, function(a)
      lapply(1:18, function(b)
        x[[cname]][a] == x[[cname]][b])) %>% 
        unlist %>% 
        (function(.) . * 1) %>% 
        matrix(
        nrow = 18, ncol = 18, 
        dimnames = list(items, items))) %>%
  Reduce('+', .) %>% 
  unlist %>%
  (function(.) {. * 1})  %>% 
  matrix(
    nrow = 18, ncol = 18, 
    dimnames = list(items, items))) %>% 
  Reduce('+', .) %>% 
  Matrix::bdiag(.) %>% 
  Matrix::as.matrix(.)) %>%
  Reduce('-', .) %>% 
  heatmap(.)
```

New
 ``` 
 lapply(colnames(x)[3:11], /.(cname)
   lapply(1:18, /.(cname)
     lapply(1:18, /.(a) 
       lapply(1:18, /.(b)
       
       
        x[[cname]][a] == x[[cname]][b]))|> unlist 
          |> (./(.) . * 13) 
          |> matrix(
             nrow = 18, ncol = 18, 
             dimnames = list(items, items)))      
        |> Reduce('+', .)  |> unlist 
    
    
        |> (/.(z) z * 1 )
        |> matrix(
          nrow = 18, ncol = 18, 
          dimnames = list(items, items))) 
   |> Reduce('+', .) 


   |> Matrix::bdiag |> Matrix::as.matrix 
   |> Reduce('-', .) 
   
   |> heatmap
  ```

How to
-----------------
This needs to transplile to R.  It needs to resemble R in almost all ways.  
rStudio plugin: makes life easier and prettier with find and replace.  

The regex substition.  The substiution shouldn't occur in a string.  Someone else may use this as part of their code.  As part of someone code.  ```%|>%``` becomes ```%%|>%%```.  Regex can handle that.

Other packages build off of ```%>%```.  future does this especially well.

```%>``` can't go on new lines.  That should be possible to fix.

It requires parsing at runtime.  This should avoid running another parser every time you hit enter in the REPL.  Maybe the best way to do this is parsing on-the-fly as people type. 

R has awesome metaprogramming abilities. Leave those alone.  Maybe.
Does ```/.``` have an obscure meaning? What about ```%/.%```?  R uses dots for piping. Is that legit code? They require ```%``` when adding oporators for a reason.

Restrictions
-----------------

R hurts your hands when you use it.  In makes you stretch all over the keyboard.  Coding is so much faster if your hands don't have to leave the home row.  So no replacement for ``` %in% ``` with ``` #in# ```.  That's atrocious. 

Using the workman layout The home row has these accessable without moving ones hand:

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

- something to mean purrr:flatten

- something to mean ``` %in% ```

- being able to declare an alias for a package domain space.  I find it good practise to be explicit.

- declare alias for functions. Right now you can do that as a hack by putting a function in another function.  That slows things down.  Python uses it sucessfully.

```%>%``` slows things down.  It isn't a simple macro.  ```%>%``` needs to be rebuilt from scratch.  It slows performance.  Seemingly tiny performance hits become nontrivial when run a billion times.  Any change to syntax needs to improve this.  What if your computer doesn't have magrittr, and you can't add packages without permission?  I've been in that situation.  

The name
---

pastR sounds like the Albanian word for clean

