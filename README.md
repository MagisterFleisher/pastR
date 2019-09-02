# pastR
rStudio plugin: makes life easier and prettier with find and replace.  It's R, but clean.

--
Let's face it.  R looks ugly.  It hurts your eyes.    Official R hasn't really changed in the last 20 years.  Some things need the tiniest changes.  It could use a couple modern features:
replace 

```
%>%
```

with 

```
|>
```

because it's easy to type, fast to type, and makes code beautiful.  Have you seen Elixer code?



replace

```
function
```

with

```
/.
```

because it's easy for me to type.  It looks like the letter lambda.


That's not so hard.

Those two changes do this:

Old
```
list(lapply(colnames(x)[3:11], function(cname)
    lapply(1:18, function(a)
      lapply(1:18, function(b)
        x[[cname]][a] == x[[cname]][b])) 
    %>% unlist 
    %>% (function(.) . * 1) 
    %>% matrix(
      nrow = 18, ncol = 18, 
      dimnames = list(items, items))) 
  %>% Reduce('+', .)
    %>% unlist 
    %>% (function(.) {. * 1}) 
    %>% matrix(
        nrow = 18, ncol = 18, 
        dimnames = list(items, items))) 
  %>% Reduce('+', .) 
  %>% Matrix::bdiag(.) 
  %>% Matrix::as.matrix(.)) 
  %>% Reduce('-', .)
#%>% #heatmap(.)
```

New
```
  lapply(colnames(x)[3:11], /.(cname)
    lapply(1:18, /.(cname
    lapply(1:18, /.(a) 
      lapply(1:18, /.(b)
        x[[cname]][a] == x[[cname]][b]))
      |> unlist 
      |> (function(.) . * 1) 
      |> matrix(
        nrow = 18, ncol = 18, 
        dimnames = list(items, items))) 
    | Reduce('+', .)
    |> unlist 
    |> (/.z { z * 1 }) 
    |> matrix(
      nrow = 18, ncol = 18, 
      dimnames = list(items, items))) 
    |> Reduce('+', .) 
    |> Matrix::bdiag
    |> Matrix::as.matrix 
    |> Reduce('-', .)
  |> heatmap
  ```
 
 Now it could be
 
 ```
 ## Goals  lapply(colnames(x)[3:11], /.(cname)
    lapply(1:18, /.(cname)
      lapply(1:18, /.(a) 
        lapply(1:18, /.(b)
          x[[cname]][a] == x[[cname]][b]))|> unlist 
        |> (./(.) . * 1) 
      |> matrix(
         nrow = 18, ncol = 18, 
         dimnames = list(items, items))) | Reduce('+', .)  |> unlist 
    |> (./(z) z * 1 ) 
    |> matrix(
         nrow = 18, ncol = 18, 
         dimnames = list(items, items))) |> Reduce('+', .) 
      |> Matrix::bdiag 
      |> Matrix::as.matrix |> Reduce('-', .) 
      |> heatmap
  ```
  
While the old isn't as easy to read at a glance
```
list(lapply(colnames(x)[3:11], function(cname)
    lapply(1:18, function(a)
      lapply(1:18, function(b)
        x[[cname]][a] == x[[cname]][b])) %>% unlist 
        %>% (function(.) . * 1) 
        %>% matrix(
          nrow = 18, ncol = 18, 
          dimnames = list(items, items))) %>% Reduce('+', .) %>% unlist 
      %>% (function(.) . * 1) 
      %>% matrix(
          nrow = 18, ncol = 18, 
          dimnames = list(items, items))) %>% Reduce('+', .) 
    %>% Matrix::bdiag(.) 
    %>% Matrix::as.matrix(.)) %>% Reduce('-', .) #%>% 
    #heatmap(.)
```

and still makes sense.  Having the bar makes it feel like Unix. 


R hurts your hands when you use it.  In makes you stretch all over the keyboard.  Coding is so much faster if your hands don't have to leave the home row.  So no replacement for ``` %in%``` with ``#in#``` 

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


## Some things to ponder


-- something to mean purrr:flatten

-- something to mean %in%

-- being able to declare an alias for a package domain space

library(rstudioapi) as rr

-- integrate the commonly used parts of the data.table package so it feels like R.


## The name

pastR sounds like the Albanian word for clean

