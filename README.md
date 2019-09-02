# pastR

rStudio plugin: makes life easier and prettier with find and replace.  It's R, but clean.
--

Let's face it.  R looks ugly.  It hurts your eyes.    Official R hasn't really changed in the last 20 years.  Some things need the tiniest changes.  It could use a couple modern features:
Replace``` %>% ```with ```|>``` because it's easy to type, fast to type, and makes code beautiful.  Have you seen Elixer code? With workman it's a two key roll.  The syntax has to change a little bit in that case. ```%>%``` 

Replace ```function```with```/.```.
  
This is basic R with maggritr and data.table. Code will look like this
------------
```
library(data.table)

xyData <- 
  "/Lib/etc/why_is_there_data_here.csv"
  |> fread |> clean

coord <- 
  headeData
    |> transform1
    |> sapply(., /.(x, y, z),
      transform2(
      xyData[, 1], xyData[, 2], z))
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

Why isn't this trivial?
-----------------
The regex needed is kind of trivial.  Some caveats exist.  In instances substiution shouldn't occur in a string.  Someone else may use this as part of their code.  As part of someone code.  ```%|>%``` becomes ```%%|>%%```.  

Less trivial comes aspects R that shouldn't be touched has awesome metaprogramming abilities.  Leave those alone.  Maybe.
Does ```/.``` have an obscure meaning? What about ```%/.%```?  R uses dots for piping. Is that legit code? They require ```%``` when adding oporators for a reason.

To get to the point.  This substitution requires parsing R.

It foists magrittr upon people.  ```%>%``` slows things down.  It isn't a macro.  ```%>%``` needs to be rebuilt from scratch.  It slows performance.  Seemingly tiny performance hits become nontrivial when run a billion times.  Any change to syntax needs to improve this.  What if your computer doesn't have magrittr, and you can't add packages without permission?  I've been in that situation.  

Other packages build off of ```%>%```.  future does this especially well.

It requires parsing at runtime.  This should avoid running another parser every time you hit enter in the REPL.  Maybe the best way to do this is parsing on-the-fly as people type. 

Being a pain in the butt makes syntax alterations in worth it.

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

The name
---

pastR sounds like the Albanian word for clean

