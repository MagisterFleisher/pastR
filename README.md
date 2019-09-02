# pastR

rStudio plugin: makes life easier and prettier with find and replace.  It's R, but clean.

--

Let's face it.  R looks ugly.  It hurts your eyes.    Official R hasn't really changed in the last 20 years.  Some things need the tiniest changes.  It could use a couple modern features:
Replace``` %>% ```with ```|>``` because it's easy to type, fast to type, and makes code beautiful.  Have you seen Elixer code?
Replace ```function```with```.\```because it's easy for me to type.  It looks like the letter lambda backward.

Code will look like this

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
    |> as.Doc.Lo
```
 
(It's mostly point-free.)


----

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
 
Having the bar makes it feel like Unix. 

---

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

---

## Some things to ponder


- something to mean purrr:flatten

- something to mean ``` %in% ```

- being able to declare an alias for a package domain space.  I find it ood practise to be explicit.

---

## The name


pastR sounds like the Albanian word for clean

