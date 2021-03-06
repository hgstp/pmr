# Programmieren mit R: Datenstrukturen
Stephan Haug  
(Programmierpraktikum MA8505)   


When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document.

## Datenstrukturen 

Datenstrukturen in R können bzgl. ihrer Dimension (1-dim,2-dim,n-dim) und ihrem Inhalt (homogen vs. heterogen)
eingeteilt werden

Dimension  | homogener Inhalt | heterogener Inhalt
--|------------------|-------------------
1-dim| Vektoren| Listen
2-dim| Matrizen| Data Frames
n-dim| Arrays|

Die Struktur eines Objekts kann mit der Funktion `str()` bestimmt werden.


```r
> str(c(1, 2, 5))
```

```
##  num [1:3] 1 2 5
```


## Vektoren

Ein Vektor (wie auch eine Liste) ist charakterisiert durch

- Typ `typeof()`
- Länge `length()`
- weitere Attribute `attributes()`

Es gibt vier Haupttypen: `logical, integer, double (numeric), character`, sogenannte atomic types. Weitere Typen sind `complex` und `raw` (ebenfalls atomic).

Vektoren können mit der Funktion `c()` erzeugt werden

```r
> typeof(c(1, 3, 7))
```

```
## [1] "double"
```

```r
> length(c(TRUE, FALSE))
```

```
## [1] 2
```

## Vektoren

Neben der Angabe des Typs, kann man Vektoren auch auf einen bestimmten Typ testen.


```r
> int.vek <- c(3L, 19L)
> doub.vek <- c(2.4, 3, 1.75)
> is.double(doub.vek)
```

```
## [1] TRUE
```

```r
> is.integer(doub.vek)
```

```
## [1] FALSE
```

```r
> is.numeric(int.vek) 
```

```
## [1] TRUE
```

```r
> #numerischer Inhalt (double und integer)
```

## Vektoren

Alle Elemente eines Vektors müssen vom gleichen Typ sein (homogener Inhalt). Werden verschiedene Typen kombiniert, so werden alle Elemente in den "flexibelsten", der beteiligtenTypen, konvertiert: `logical`(am wenigsten flexibel), `integer`, `double`, `character` (am flexibelsten)


```r
> str(c(c("R", "programming"), int.vek))
```

```
##  chr [1:4] "R" "programming" "3" "19"
```

```r
> is.logical(logic.vek <- c(FALSE, TRUE))
```

```
## [1] TRUE
```

```r
> sum(c(logic.vek, int.vek))
```

```
## [1] 23
```

## Vektoren

Die Umwandlung des Typs kann auch ganz explizit vorgenommen werden mit den Funktionen `as.character(), as.numeric(), as.double(), as.integer(), as.logical()`.


```r
> as.numeric(logic.vek)
```

```
## [1] 0 1
```

## Einschub: Logik

Logische Operatoren wie `!`(Negation), `&` (und) oder `|` (oder) und Vergleiche (`==, !=, >, <, >=, <=`) erzeugen Vektoren vom Typ `logical`. 


```r
> x <- 3 != 5
> typeof(x)
```

```
## [1] "logical"
```

```r
> (3 >= 2) & (4 == (3+1))
```

```
## [1] TRUE
```

```r
> c(3 >= 2, 3 > 5) | c(4 == (3+1), 4 == 16/4)
```

```
## [1] TRUE TRUE
```


## Einschub: Logik

Weitere Funktionen zur logischen Abfrage sind `xor(), any()` oder `all()`. Wichtig ist noch die Abfrage nach fehlenden Werte (ausgedrückt durch `NA`). Dies geschieht mit `is.na()`.  


```r
> x <- c(2, 5, NA)
> x == NA # liefert nicht das gewünschte Ergebnis
```

```
## [1] NA NA NA
```

```r
> is.na(x)
```

```
## [1] FALSE FALSE  TRUE
```
`NaN` (Not a Number) wird wie `NA` behandelt.

```r
> is.na(NaN)
```

```
## [1] TRUE
```


## Vektoren: Folgen und Wiederholungen

Folgen lassen sich leicht mit `seq()` oder dem Operator `:` und Wiederholungen mit `rep()` erzeugen


```r
> 1:7
```

```
## [1] 1 2 3 4 5 6 7
```

```r
> 5:1
```

```
## [1] 5 4 3 2 1
```

```r
> seq(from = 1.3, to = 1.8, by = 0.2)
```

```
## [1] 1.3 1.5 1.7
```

```r
> seq(from = 1.3, to = 1.8, length.out = 4)
```

```
## [1] 1.300000 1.466667 1.633333 1.800000
```


## Vektoren: Folgen und Wiederholungen


```r
> rep(TRUE, times = 3)
```

```
## [1] TRUE TRUE TRUE
```

```r
> rep(2:4, times = 1:3)
```

```
## [1] 2 3 3 4 4 4
```


## Listen

Listen unterscheiden sich von Vektoren primär durch die Eigenschaft, dass der Typ der Elemente
einer Liste verschieden sein kann. 


```r
> x <- list(integer.vektor = int.vek, "wort", logic.vek, 
+            c(1.3, 2.89))
> str(x)
```

```
## List of 4
##  $ integer.vektor: int [1:2] 3 19
##  $               : chr "wort"
##  $               : logi [1:2] FALSE TRUE
##  $               : num [1:2] 1.3 2.89
```
`integer.vektor` ist hierbei der Name des ersten Listenelements.

## Listen 

Listen können - im Gegensatz zu Vektoren - auch rekursive aufgebaut sein.


```r
> str(x)
```

```
## List of 4
##  $ integer.vektor: int [1:2] 3 19
##  $               : chr "wort"
##  $               : logi [1:2] FALSE TRUE
##  $               : num [1:2] 1.3 2.89
```

```r
> str(c(c("a", c(1))))
```

```
##  chr [1:2] "a" "1"
```

## Listen



```r
> x <- list(list(1,2), c(3,4))
> is.recursive(x)
```

```
## [1] TRUE
```

```r
> str(x)
```

```
## List of 2
##  $ :List of 2
##   ..$ : num 1
##   ..$ : num 2
##  $ : num [1:2] 3 4
```

## Listen
Kombiniert man mit `c()` Listen und Vektoren, so wird daraus eine Liste.


```r
> y <- c(list(1,2),c(3,4))
> str(y)
```

```
## List of 4
##  $ : num 1
##  $ : num 2
##  $ : num 3
##  $ : num 4
```

## Listen

Listen sind vom Typ `list`. Dies kann mit `is.list()` überprüft werden. Mit `as.list()` kann ein Objekt in eine Liste konvertiert werden. 


```r
> x <- as.list(c("Programmieren", "mit", "R"))
> typeof(x)
```

```
## [1] "list"
```

```r
> unlist(x)
```

```
## [1] "Programmieren" "mit"           "R"
```

```r
> is.character(unlist(x))
```

```
## [1] TRUE
```

## Listen

Viele weitere Objekt sind Listen. So sind Data Frames (später mehr) und z.B. linear models Objekte Listen


```r
> is.list(data.frame(a = c(1,2), b = c(T,F)))
```

```
## [1] TRUE
```

```r
> is.list(lm(Petal.Length ~ Petal.Width, data = iris))
```

```
## [1] TRUE
```

## Attribute

Jedem Objekt kann eine beliebige Anzahl von Attributen zugeordnet werden. Diese Attribute können frei gewählt werden. Mit `attr()` greift man auf ein spezielles Attribute zu und mit `attributes()` erhält man eine Liste allter Attribute.


```r
> y <- 1:5
> attr(y, "Werte") <- "ganzzahlige Einträge"
> attr(y, "Werte")
```

```
## [1] "ganzzahlige Einträge"
```

```r
> attributes(y)
```

```
## $Werte
## [1] "ganzzahlige Einträge"
```


## Attribute

Attribute, die durch Modifizierung des Vektors nicht verloren gehen, sind:

 - Name
 - Dimension
 - Klasse

Diese Attribute können durch `names(), dim()` und `class()` aufgerufen werden.


## Attribute

Namen können dabei auf drei Arten vergeben werden


```r
> x <- c(a = 1, b = 2, c = 3)
> names(x)
```

```
## [1] "a" "b" "c"
```
oder

```r
> y <- 1:3
> names(y) <- c("a", "b", "c") # Funktion links von <- !!
> names(y)
```

```
## [1] "a" "b" "c"
```

## Attribute
... oder


```r
> z <- setNames(1:3, c("a","b","c"))
> names(z)
```

```
## [1] "a" "b" "c"
```

Namen sind hilfreiche Attribute, wie wir beim Indizieren von Vektoren und Listen sehen werden.


## Faktoren

Faktoren sind Vektoren deren Einträge kategorielle Variablen sind. Ein Beispiel wäre der Vektor


```r
> alter <- factor(c("unter 20","über 60","unter 20"))
```

der die Altersklasse von drei Probanden beinhaltet.


```r
> str(alter)
```

```
##  Factor w/ 2 levels "über 60","unter 20": 2 1 2
```

In der Statistik treten kategorielle Variablen relativ häufig auf.

## Faktoren

Ein Vektor wird zum Faktor, falls er von der Klasse


```r
> class(alter)
```

```
## [1] "factor"
```
ist und das Attribut `levels()` gesetzt ist, welches die erlaubten Werte der Faktorvariable definiert.

```r
> levels(alter)
```

```
## [1] "über 60"  "unter 20"
```


## Faktoren

Will man einen "unzulässigen" Wert zu einem Faktor hinzufügen, so wird stattdessen ein fehlender Wert erzeugt


```r
> alter[3] <- "zw. 20 u. 60" # zur Indizierung später mehr
```

```
## Warning in `[<-.factor`(`*tmp*`, 3, value = "zw. 20 u. 60"): invalid factor
## level, NA generated
```

Können die Kategorien eines Faktors geordnet werden, so kann `ordered()` verwendet werden


```r
> alter.go = ordered(c(1,2), levels = c(1, 2), 
+                    labels = c("unter 20", "über 60"))
> alter.go
```

```
## [1] unter 20 über 60 
## Levels: unter 20 < über 60
```



## Faktoren

Ein Vorteil eines Faktors gegenüber einem `character` Vektor ist die Tatsache, dass in Operationen (z.B. Häufigkeiten bestimmen), angewendet auf den Faktor, alle Levels einbezogen werden (auch solche, die im Datensatz nicht vorhanden sind).


```r
> geschlecht_c <- c("w", "w", "w")
> geschlecht_f <- factor(geschlecht_c, levels = c("w", "m"))
> table(geschlecht_c)
```

```
## geschlecht_c
## w 
## 3
```

```r
> table(geschlecht_f)
```

```
## geschlecht_f
## w m 
## 3 0
```


## Faktoren

Obwohl Faktoren oft wie `character`Vektoren aussehen, handelt es sich dabei um `integer` Vektoren wie man mit


```r
> mode(alter)
```

```
## [1] "numeric"
```
und

```r
> unclass(alter)
```

```
## [1]  2  1 NA
## attr(,"levels")
## [1] "über 60"  "unter 20"
```
sieht. 


## Matrizen und Arrays

Vergibt man an einen atomaren Vektor das `dim` Attribut, so verhät er sich wie ein multidimensionales Array. Dabei stellt eine Matrix ein zweidimensionales Array dar. 

Matrizen werden häufig verwendet um die entsprechenden mathematischen Berechnungen durchzuführen. $n$-dimensionale Arrays, $n>2$, werden deutlich seltener verwendet.

Matrizen und Arrays können mit den Funktionen `matrix(), array()` und `dim()` erzeugt werden.

## Matrizen und Arrays


```r
> (a <- matrix(1:6, ncol = 3, nrow = 2))
```

```
##      [,1] [,2] [,3]
## [1,]    1    3    5
## [2,]    2    4    6
```

```r
> (b <- array(1:6, dim = c(1, 3, 2)))
```

```
## , , 1
## 
##      [,1] [,2] [,3]
## [1,]    1    2    3
## 
## , , 2
## 
##      [,1] [,2] [,3]
## [1,]    4    5    6
```


## Matrizen und Arrays


```r
> c <- 1:6
> dim(c) <- c(2, 3)
> c
```

```
##      [,1] [,2] [,3]
## [1,]    1    3    5
## [2,]    2    4    6
```

```r
> attributes(c)
```

```
## $dim
## [1] 2 3
```


## Matrizen und Arrays

Die Funktionen `length()` und `names()` haben "höherdimensionale Verallgemeinerungen":

- `nrow(), ncol()` für Matrizen und `dim()` für Arrays
- `rownames(), colnames()` für Matrizen und `dimnames()` für Arrays (als Liste)


## Matrizen und Arrays


```r
> c(length(a),nrow(a),ncol(a))
```

```
## [1] 6 2 3
```

```r
> rownames(a) <- c("Z1", "Z2")
> colnames(a) <- c("S1","S2","S3")
> attributes(a)
```

```
## $dim
## [1] 2 3
## 
## $dimnames
## $dimnames[[1]]
## [1] "Z1" "Z2"
## 
## $dimnames[[2]]
## [1] "S1" "S2" "S3"
```

## Matrizen und Arrays


```r
> c(length(b), dim(b))
```

```
## [1] 6 1 3 2
```

```r
> dimnames(b) = list("F1.1", c("F2.1", "F2.2", "F2.3"), 
+                     c("F3.1", "F3.2"))
> b
```

```
## , , F3.1
## 
##      F2.1 F2.2 F2.3
## F1.1    1    2    3
## 
## , , F3.2
## 
##      F2.1 F2.2 F2.3
## F1.1    4    5    6
```


## Matrizen und Arrays

Zum Kombinieren von Matrizen können die Funktionen `cbind()` und `rbind()` verwendet werden (sofern die Dimensionen passen).


```r
> cbind(a, matrix(1:4, nrow = 2))
```

```
##    S1 S2 S3    
## Z1  1  3  5 1 3
## Z2  2  4  6 2 4
```

```r
> rbind(a, matrix(1:3, ncol = 3))
```

```
##    S1 S2 S3
## Z1  1  3  5
## Z2  2  4  6
##     1  2  3
```

## Matrizen und Arrays 

`t()` berechnet die Transponierte einer Matrix und `%*%` berechnet das Matrixproduk zweier Matrizen.

`is.matrix()` und `is.array()` überprüfen ob ein Objekt der Klasse `matrix` bzw. `array` vorliegt. Mit `as.matrix()` und `as.array()` kann ein Vektor in eine Matrix bzw. ein Array umgewandelt werden.


```r
> is.array(b)
```

```
## [1] TRUE
```

```r
> class(as.matrix(c("a", 1, 3), ncol = 3))
```

```
## [1] "matrix"
```


## Data Frames

Data Frames sind Listen deren Elemente aus Vektoren gleicher Länge bestehen. Somit sind Data Frames zweidimensionale Objekte ( # Elemente $\times$ Länge Vektoren - Matrizen). 
Data Frames besitzen unter anderem die Attribute `names` und `row.names`, die mit `names()` (oder `colnames()`) bzw. `rownames()` gesetzt werden können.

Erzeugt wird ein Data Frame mit der Funktion `data.frame()`, die benannte Vektoren als Input erwartet.

*Bemerkung:* Datensätze werden in R üblicherweise als Data Frame gespeichert.

## Data Frames


```r
> df <- data.frame(x = 1:3, y = c("eins", "zwei", "drei"))
> str(df)
```

```
## 'data.frame':	3 obs. of  2 variables:
##  $ x: int  1 2 3
##  $ y: Factor w/ 3 levels "drei","eins",..: 2 3 1
```

`data.frame()` verwandelt Zeichenketten automatisch in Faktoren. Für viele Datensätze ist dies das gewünschte Verhalten.

## Data Frames

Sollen `character` Vektoren nicht automatisch in Faktoren umgewandelt werden, so kann man die Option `stringsAsFactors` verwenden.


```r
> df <- data.frame(x = 1:3, y = c("eins", "zwei", "drei"),
+                  stringsAsFactors = FALSE)
> str(df)
```

```
## 'data.frame':	3 obs. of  2 variables:
##  $ x: int  1 2 3
##  $ y: chr  "eins" "zwei" "drei"
```


## Data Frames

Nachdem Data Frames spezielle Listen sind, sind sie vom Typ

```r
> typeof(df)
```

```
## [1] "list"
```
und gehören zur Klasse

```r
> class(df)
```

```
## [1] "data.frame"
```
Man kann mit `is.data.frame()` überprüfen ob es sich bei einem Objekt um ein Data Frame handelt.

## Data Frames

Data Frames können mit `cbind()` und `rbind()` kombiniert werden. 


```r
> cbind(df, data.frame(z = c(0.1, 0.2, 0.3)))
```

```
##   x    y   z
## 1 1 eins 0.1
## 2 2 zwei 0.2
## 3 3 drei 0.3
```

Für `cbind()` muss die Zeilenanzahl der beteiligten Data Frames übereinstimmen.

## Data Frames

`rbind()` erwartet Data Frames mit gleicher Spaltenzahl und gleichen Spaltennamen.


```r
> rbind(df, data.frame(x = 4, z = "vier"))
```

```
## Error in match.names(clabs, names(xi)): names do not match previous names
```

```r
> rbind(df, data.frame(x = 4, y = "vier"))
```

```
##   x    y
## 1 1 eins
## 2 2 zwei
## 3 3 drei
## 4 4 vier
```
