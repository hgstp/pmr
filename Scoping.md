# Environments und Scoping



Wir haben nun bereits gelernt wie man neue Objekte erzeugt. Uns ist der Workspace ein Begriff, d.h. uns ist klar, dass wir darin neu erzeugte Objekte finden. Ebenso haben wir gesehen, dass es - sei es in den Basispaketen oder in geladenen Zusatzpaketen -  eine Vielzahlt von Funktionsobjekten gibt. Daher stellt sich eine berechtigte Frage. *Wie weiß nun R welchen Wert es welchem Symbol zuordnen soll?* 


```r
> sqrt(2)
```

```
## [1] 1.414214
```

```r
> sqrt <- function(x) x^2
> sqrt(2)
```

```
## [1] 4
```
Warum wählt R hier nicht die Standardfunktion `sqrt()` aus dem `base` Paket, sondern die von uns gerade definierte Funktion?

## Zuordnungen

Wenn R einem Symbol einen Wert zuordnet, dann durchsucht R eine Reihe von `environments` (bereits in [Kapitel 6](Funktionen.html) gesehen) nach dem passenden Wert. Gesucht wird in folgender Reihenfolge

1. Das `Global Environment` wird durchsucht.
2. Die `namespaces` (dazu später mehr) der Pakete auf der `search` Liste werden durchsucht.



```r
> search()
```

```
## [1] ".GlobalEnv"        "package:stats"     "package:graphics" 
## [4] "package:grDevices" "package:utils"     "package:datasets" 
## [7] "package:methods"   "Autoloads"         "package:base"
```
Das `global environment` (der Workspace) wird dabei immer zuerst und `base` zuletzt durchsucht. Die `search` Liste wird also beeinflusst durch die von uns geladenen Pakete.


```r
> library(ggplot2)
> search()
```

```
##  [1] ".GlobalEnv"        "package:ggplot2"   "package:stats"    
##  [4] "package:graphics"  "package:grDevices" "package:utils"    
##  [7] "package:datasets"  "package:methods"   "Autoloads"        
## [10] "package:base"
```
Das zuletzt geladene Paket (`ggplot2`) kommt also an die höchstmögliche (`.GlobalEnv` ist ja immer an erster Stelle) Stelle in der `search` Liste.

Man beachte, dass R die Namen von Daten- und Funktionsobjekten unterscheiden kann.


```r
> (sqrt <- 2)
```

```
## [1] 2
```

```r
> sqrt(2)
```

```
## [1] 1.414214
```

## Environments

Ein Environment verbindet eine Menge von Namen mit einer entsprechenden Menge von Werten. Environments sind ähnlich zu einer Liste, aber es gibt entscheidende Unterschiede

* die Namen der Objekte in einem Environment sind eindeutig
* die Objekte eines Environments sind nicht geordnet
* jedes Environment besitzt ein `parent` Environment, außer `emptyenv()`

Die Einträge von `search()` sind die Eltern des `Global Environment`. Mit `new.env()` kann auch ein neues Environment erzeugt werden.


```r
> neues_env <- new.env()
> neues_env$eins <- c(1, 2)
> neues_env$zwei <- c("a", "b")
> ls(neues_env)
```

```
## [1] "eins" "zwei"
```

```r
> parent.env(neues_env)
```

```
## <environment: R_GlobalEnv>
```


*Bemerkung:* Die meisten Environments erzeugt man durch die Verwendung von Funktionen. 



## Scoping Rules

Der Sichtbarkeitsbereich von Variablen (Scope) wird nun über *Scoping Rules* festgelegt. R verwendet *static scoping* oder auch *lexical scoping* genannt (eine Alternative ist das *dynamic scoping*). 

Wir betrachten die Funktion 

```r
> f <- function(x, y){
+   x + y/z
+ }
```
`f` hat die formellen Argumente `x` und `y` sowie die freie Variable `z`. Die Scoping Rule bestimmt wie Werte freien Variablen zugeordnet werden. In R bedeutet dies

> Der Wert freier Variablen (in einer Funktion) wird in dem Environment gesucht, in dem die Funktion definiert wurde.



## Static scoping

Oftmals werden Funktionen im Globalen Environment definiert. Die Werte freier Variablen sollten dann im Workspace zu finden sein. Dieses "Verhalten" wird von den meisten "Nutzern" erwartet und als sinnvoll angesehen. Allerdings können Funktionen auch innerhalb von Funktionen definiert werden. In diesem Fall ist das Environment der `body` einer anderen Funktion. 



```r
> bilde_potenz <- function(n){
+   potenz <- function(x) x^n
+ }
```
`bilde_potenz()` liefert somit eine Funktion als Ausgabe.


```r
> zweite_potenz <- bilde_potenz(2)
> dritte_potenz <- bilde_potenz(3)
> dritte_potenz
```

```
## function(x) x^n
## <environment: 0x000000000740b770>
```

Sucht man nach einer Variable und/oder möchte man ihren Wert ausgeben, so kann man mit den Funktionen `exists()` und `get()` arbeiten. Beide verwenden static scoping.


```r
> get("eins", envir = neues_env)
```

```
## [1] 1 2
```

```r
> x <- 1
```
Das Objekt `x` ist nun im Global Environment definiert. Trotzdem sagt

```r
> exists("x", envir = neues_env)
```

```
## [1] TRUE
```
dass `x` existiert. Dies ist der Fall, da über das Argument `inherits` der Funktion `existis()` standardmäßig auch die "Eltern" durchsucht werden, falls im angegebenen Environment das Objekt nicht gefunden wurde. Somit
ist dann auch das Ergebnis des folgenden Befehls klar.


```r
> exists("x", envir = neues_env, inherits = FALSE)
```

```
## [1] FALSE
```


Schauen wir uns nun den `closure` (Funktion + zugehöriges Environment) von `zweite_potenz()` und `dritte_potenz()` genauer an.


```r
> ls(environment(zweite_potenz))
```

```
## [1] "n"      "potenz"
```

```r
> get("n", envir = environment(zweite_potenz))
```

```
## [1] 2
```

```r
> ls(environment(dritte_potenz))
```

```
## [1] "n"      "potenz"
```

```r
> get("n", envir = environment(dritte_potenz))
```

```
## [1] 3
```
Wir sehen, der Wert von `n` hängt ab vom Environment in dem die Funktion definiert wurde (d.h. dem Environment der jeweiligen Funktion).

## Anwendungsbeispiel

Wir betrachten als Anwendungsbeispiel die Minimierung der Funktion

$$
  l_x(\mu,\sigma^2) =-\left(-\frac{n}{2}\log(2\pi\sigma^2)-\frac1{2\sigma^2}\sum_{i=1}^n(x_i-\mu)^2\right)\,,
$$
wobei $$x_i, i=1,\dots,n,$$ bekannte Größen sind. Die obigen Funktion ist der negative log-Likelihood einer Normalverteilung mit Parametern $$\mu$$ und $$\sigma^2$$, aber das spielt hier keine Rolle.

Optimierungsroutinen wie `optim()`, `nlm()` oder `optimize()` erwarten als Eingabe eine
Funktion, deren Argumente die zu optimierenden Parameterwerte sind. Oftmals (wie im obigen Beispiel) hängen Funktionen aber von weiteren Werten ab. Um diese zusätzlichen Werte nicht umständlich an die Optimierungsroutine weitergeben zu müssen, kann man durch Anwendung der Scoping Regeln diese Werte gleich im Environment der zu optimierenden Funktion "ablegen". Eine deratige Implementierung der obigen Funktion ist die folgende.



```r
> negLogLik <- function(data, fix = c(FALSE, FALSE)){
+   param <- fix
+   function(theta){
+     param[!fix] <- theta
+     mu <- param[1]
+     sigma_2 <- param[2]
+     l_x <- -( -length(data)/2 * log(2 * pi * sigma_2 ) - sum((data-mu)^2) / (2*sigma_2))
+     l_x
+   }
+ }
```

Der letzte Befehl der Funktion `negLogLik()` ist eine FUnktionsdefinition. Somit ist die Ausgabe der Funktion wieder eine Funktion. `negLogLik()` ist dabei so geschrieben, dass einer der beiden Parameter fixiert werden kann.



```r
> set.seed(1234)
> x <- rnorm(1000, mean = 1, sd = 2)
> l_x <- negLogLik(x)
> l_x
```

```
## function(theta){
##     param[!fix] <- theta
##     mu <- param[1]
##     sigma_2 <- param[2]
##     l_x <- -( -length(data)/2 * log(2 * pi * sigma_2 ) - sum((data-mu)^2) / (2*sigma_2))
##     l_x
##   }
## <environment: 0x000000000b57c4d0>
```

Schaut man sich nun das Environment der Funktion `l_x()` an

```r
> ls(environment(l_x))
```

```
## [1] "data"  "fix"   "param"
```
so findet man dort die Objekte data, fix, param. Alle drei Objekte sind innerhalb der Funktion `negLogLik()` bekannt und damit innerhalb des Environments in dem `l_x()` definiert wurde.

Optimieren wir nun die Funktion `l_x()` bzgl. beider Parameter, so erhält man

```r
> optim(par = c(0, 1), fn = l_x)$par
```

```
## [1] 0.9465684 3.9744817
```
Man beachte, dass für den Datensatz `x` der Parameter $$\sigma^2$$ gleich 4 gewählt wurde. 

Betrachten wir nun einen Parameter (z.B. $$\mu$$) als bekannt, so können wir diesen in einer erneuten Definition von `l_x()` fixieren (z.B. gleich 1 setzen). Dies ermöglicht uns nun die Funktion nur noch
bzgl. eines Paramters zu optimieren.


```r
> l_x <- negLogLik(x, fix = c(1, FALSE))
> optimize(f = l_x, interval = c(1e-6, 10))$minimum
```

```
## [1] 3.97759
```


*Bemerkung:* Wir verwenden `optimize()` anstatt `optim()`, da letztere nicht für eindimensionale Optimierung geeignet ist.


Durch Übergabe aller weiteren Größen (data, fix, param) im Environment

```r
> environment(l_x)
```

```
## <environment: 0x000000000b110b48>
```
konnte also die Funktion `l_x()` im `.GlobalEnv` nur als Funktion der unbekannten Parameter  definiert werden. 


```r
> parent.env(environment(l_x))
```

```
## <environment: R_GlobalEnv>
```


Es musste also keine Liste weiterer Argumente der Optimierungsfunktion übergeben werden um die Likelihoodfunktion vollständig zu spezifizieren.
