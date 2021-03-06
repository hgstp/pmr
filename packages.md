# Pakete



Aus [Kapitel 1](erste_schritte.html#pakete) wissen wir bereits, dass es sich bei einem Paket um

> eine strukturierte, standardisierte Einheit handelt, welche aus R Code, Dokumentation, 
Daten und externem Quellcode besteht.

Über die Installation und Verwaltung von Paketen wurde ebenfalls bereits in der Einführung gesprochen. Jetzt wollen wir eigene Pakete erstellen.  
Gründe für die Verwendung von Paketen

- dynamisches Laden und Entladen des Packages (Speicherplatz sparend)
- einfache Installation und Update von lokalen Datenträgern oder über das Web, innerhalb von R oder über die Kommandozeile des Betriebssystems
- Validierung: R bietet Befehle zur groben Überprüfung von Code, Dokumentation und Installierbarkeit, sowie, falls man möchte, Überprüfung von Berechnungsergebnissen.
- einfache Verteilung der Software an Dritte; Beispieldatensätze können auch ins Paket!

## Struktur

Ein Paket besteht aus einigen Standard–Dateien und Verzeichnissen:

- `DESCRIPTION`: Informationen über das Paket (Autor,
Lizenz, Titel, Abhängigkeiten, ...)
- `R/`: R Code in `.R` Dateien
- `man/`: Dokumentationen (im `*.Rd` Format) der einzelnen Funktionen
- `NAMESPACE`: Information über die zu "exportierenden" Funktionen
- `data/`: Datenbeispiele
- `src/`: kompilierter C, C++ oder Fortran Code
- `tests/`: Testroutinen zur Validierung des Pakets

Bei der Erzeugung der Struktur (wie auch weiteren Schritten bei der Entwicklung) sollte man so viel wie möglich automatisieren. Wir verwenden dazu die von [Hadley Wickham](http://had.co.nz/) bereitgestellten Werkzeuge.

Falls noch nicht geschehen, sollte man


```r
> install.packages(c("devtools", "roxygen2", "testthat"))
```
ausführen. Diese Pakete erleichtern in Kombination mit RStudio das Erstellen von Paketen. 

## C Compiler

Außerdem benötigt man noch einen C Compiler. Den haben wir aber bereits zur Verwendung von `Rcpp` installiert. Mit `has_devel()` kann überprüft werden ob alles korrekt installiert ist


```r
> library(devtools)
> has_devel()
```

```
## [1] TRUE
```

Dies scheint der Fall zu sein.

## R Code

Das Verzeichnis `R/` ist der wichtigste Bestandteil eines Pakets, da dort der komplette R Code liegt. 
Dieses Verzeichnis (wie auch andere Teile) erzeugt man mit der Funktion `devtools::create()`, z.B.


```r
> create("Pfad_zum_Paket/Paketname")
```
Dieser Befehl erzeugt das Verzeichnis `Pfad_zum_Paket/Paketname`, welches 

1. eine RStudio Projektdatei, `Paketname.Rproj`
2. ein `R/` Verzeichnis
3. eine `DESCRIPTION` Datei
4. eine `NAMESPACE` Datei

enthält. Allen R Code, der im Paket enthalten sein soll, kopiert man anschließend in das Verzeichnis `Pfad_zum_Paket/Paketname/R`.

## RStudio Projekte

Projekte in RStudio besitzen ihr eigenes Arbeitsverzeichnis, ihren eigenen Workspace, ihre eigene History und Quelldateien. Wir können also thematisch getrennte Aufgaben in strikt getrennten Projekten bearbeiten durch (einfachen) Aufruf der jeweiligen Projektdatei.

Weitere Information zum Arbeiten mit Projekten findet man auf der RStudio Seite [Using Projects](https://support.rstudio.com/hc/en-us/articles/200526207-Using-Projects).


Viele der RStudio *Keyboard Shortcuts*, welche man mit Alt + Shift + K aufrufen kann, sind speziell innerhalb eines Projekts sehr hilfreich.


![Keyboard Shortcuts](shortcuts.PNG)

## R Code

Innerhalb eines Pakets ist es nun einfach den kompletten R Code (nach Änderungen) neu zu laden.

- `devtools::load_all()` lädt allen R Code neu. In RStudio auch über **Strg + Shift + L** möglich.
- In RStudio kann man mit **Strg + Shift + B** das Paket installieren, R neu starten und anschließend das Paket über `library()` neu laden.

Dadurch ergibt sich der folgende Arbeitsablauf

1. R Dateien im Editor bearbeiten
2. Strg + Shift + L (oder Strg + Shift + B) ausführen
3. Code in der Konsole überprüfen
4. Das Ganze (falls nötig) wiederholen

## Typen

Man unterscheidet fünf verschiedene Typen: *source*, *bundled*, *binary*, *installed* und *in memory*.

- **source**: Die source Version eines Pakets besteht aus dem Verzeichnis `Paketname`

- **bundled**: Komprimiert man das Verzeichnis in eine `.tar.gz` Datei so spricht man von einem "gebündelten" Paket. Dieser Typ stellt eher einen Zwischenschritt dar. Mit `devtools::build()` kann aber eine solche Version erzeugt werden. Weitere Unterschiede zu einer source Version sind für uns erst mal nicht von Interesse.

- **binary**: Ein gebündeltes Paket kann von anderen R Nutzern nur installiert werden, wenn sie selbst über die entsprechenden "Werkzeuge" (Rtools, ...) verfügen. Binary Pakete bestehen auch nur aus einer Datei, können aber von anderen R Nutzern auch ohne weitere "Werkzeuge" installiert werden. Allerdings sind binary Pakete plattformspezifisch. Ein Windows binary Paket kann also z.B. nicht auf einem Mac installiert werden. Erzeugt wird eine binary Version z.B. mit `devtools::build(binary = TRUE)`.

- **installed**: Ein installiertes Paket ist ein in eine R Library entpacktes binary Paket. Zu diesem Zustand kann man über mehrere Wege gelangen, z.B.
    + `install.packages()`: CRAN binary -> installed
    + `install.packages(type = source)`: CRAN source -> bundled -> installed 
    + `install()`: source -> installed
    
Mit `devtools::install_github()` können source Pakete aus einem [GitHub](https://github.com/) Projekt installiert werden.

- **in memory**: Um mit einem Paket zu arbeiten muss es in den Speicher geladen werden. Dies geschieht mit `library()` (installierte Pakete) oder `load_all()` (beim Entwickeln von Paketen).    


## DESCRIPTION

Die DESCRIPTION Datei enthält Informationen über das Paket und ist ein essentieller Bestandteil jedes Pakets. Der Befehl


```r
> devtools::create("mnRpkt")
```
erzeugt die DESCRIPTION Datei


```{}
Package: mnRpkt
Title: What the Package Does (one line, title case)
Version: 0.0.0.9000
Authors@R: person("First", "Last", email = "first.last@example.com", role = c("aut", "cre"))
Description: What the package does (one paragraph).
Depends: R (>= 3.2.2)
License: What license is it under?
LazyData: true
```


im Ordner `mnRpkt`. Die wichtigsten Felder einer DESCRIPTION Datei sind dadurch vorhanden (und müssen gefüllt werden). Über die Punkte `Imports` und `Suggests` (nicht automatisch vorhanden) kann festgelegt werden welche anderen R Pakete zwingend notwendig bzw. hilfreich sind für das eigene Paket.   
Pakete in `Imports` werden beim Installieren des eigenen Pakets ebenfalls installiert, falls diese noch nicht vorhanden sind. Pakete in `Suggests` werden nicht automatisch installiert. 

Über


```r
> devtools::use_package("Rcpp")
```
kann z.B. das Paket `Rcpp` dem Punkt `Imports` hinzugefügt werden. `devtools::use_package(type = "Suggests")` fügt `Suggests` weitere Pakete hinzu.

## Name, Titel, Beschreibung

Der Name (`Package`) eines Pakets darf Buchstaben, Zahlen und `.` enthalten und sollte mit dem Verzeichnisname übereinstimmen.

`Title` und `Description` sollten beschreiben was das Paket genau macht. `Description` ist dabei deutlich ausführlicher und enthält daher i.d.R. mehrere Zeilen. Nach einem Zeilenumbruch sollte 4 Leerzeichen eingerückt werden. Ein Beispiel wäre


```{}
Title: An implementation of the Grammar of Graphics
Description: An implementation of the grammar of graphics in R. 
    It combines the advantages of both base and lattice graphics: 
    conditioning and shared axes are handled automatically, and 
    you can still build up a plot step by step from multiple data 
    sources. It also implements a sophisticated multidimensional 
    conditioning system and a consistent interface to map data 
    to aesthetic attributes. See the ggplot2 website for more 
    information, documentation and examples.
```


## Autoren

Über das Feld `Authors@R` können die Autoren des Pakets angegeben werden. An dieser Stelle kann
in der DESCRIPTION Datei R Code verwendet werden um die nötigen Informationen

* `given, family`: Vor- und Nachname
* `email`: E-Mailadresse
* `role`: Rolle der Person. Wichtige Beispiele sind
    + `cre`: Ersteller oder Maintainer
    + `aut`: hat wichtige Beiträge zum Paket erbracht
    + `ctb`: hat kleinere Beiträge zum Paket erbracht

Mit der Funktion `person()` können diese Informationen nun angegeben werden
```{}
Authors@R: person(given = "Stephan", family = "Haug", email = "meinname@tum.de", 
                  role = c("cre", "aut"))
```

## Version

`devtools::create()` legt die Default-Version auf `0.0.0.9000` fest, wobei die vier stellen für `major.minor.patch.dev` stehen. Die letzte Stelle zeigt also den Entwicklungsstatus an. Die Versionsnummer eines veröffentlichten Pakets besteht dann aber nur noch aus drei Teilen. R verwendet die Versionsnummern zur Überprüfung von Abhängigkeiten zwischen Paketen.

Die aktuelle Version von `ggplot2` ist z.B.


```r
> packageDescription("ggplot2")$Version
```

```
## [1] "1.0.1"
```

Nun wollen wir unser Paket mit "Inhalt" füllen. Die erste Funktion in unserem Paket sei `sum_R()` aus dem Kapitel [Rcpp](Rcpp.html#beispiele-r-vs-c). Diesen Code speichern wir in der Datei sum.R im Verzeichnis `Pfad_zum_Paket/mnRpkt/R` ab. Anschließend laden wir das source Paket `mnRpkt` mit `devtools::load_all()`.





```r
> ls()
```

```
## character(0)
```

```r
> devtools::load_all("mnRpkt")
```

```
## Loading mnRpkt
```


## Dokumentation  

Ein wichtiger Teil eines Pakets ist die ausreichende Dokumentation (wichtiger) Funktionen. Dokumentationen werden als `.Rd` (LaTeX ähnliches Format) Dateien im Verzeichnis `man/` abgelegt. Das Paket `roxygen2` bietet eine komfortable Möglichkeit aus Funktionskommentare (in einem speziellen Format) `.Rd` Datei zu erzeugen.

*Ablauf:*

1. roxygen Kommentare in die `.R` Datei einfügen
2. `devtools::document()` ausführen um Kommentare in `.Rd` Datei zu konvertieren
3. Dokumentation mit `?` betrachten
4. gegebenenfalls alles wiederholen

roxygen Kommentare beginnen mit `#'`. Um dies zu veranschaulichen, fügen wir eine Datei `add_2.R` mit Inhalt 

```{}
#' Addiere zwei Zahlen
#' 
#' @param x Eine Zahl.
#' @param y Eine Zahl.
#' @return Die Summe von \code{x} und \code{y}.
#' @examples
#' add_2(1, 1)
#' add_2(10, 1)
add_2 <- function(x, y) {
  x + y
}
```
dem Verzeichnis `mnRpkt/R` hinzu. Der Befehl `devtools::document()` (ausgeführt im Verzeichnis `mnRpkt` - source Paket) erzeugt dann die Datei `mnPkt/man/add_2.Rd`


```{}
% Generated by roxygen2 (4.1.1): do not edit by hand
% Please edit documentation in R/add_2.R
\name{add_2}
\alias{add_2}
\title{Addiere zwei Zahlen}
\usage{
add_2(x, y)
}
\arguments{
\item{x}{Eine Zahl.}

\item{y}{Eine Zahl.}
}
\value{
Die Summe von \code{x} und \code{y}.
}
\description{
Addiere zwei Zahlen
}
\examples{
add_2(1, 1)
add_2(10, 1)
}
```

## `roxygen` Kommentare

Alle roxygen Kommentare beginnen mit `#'` und sind unterteilt in verschiedene Tags der Form `@name_tag beschreibung`. Vor dem ersten Tag kommen allerdings

* Titel: erste Zeile des Kommentars
* Beschreibung der Funktion: zweiter Abschnitt des Kommentars (länger als Titel)
* Details zur Funktion: dritter Abschnitt des Kommentars (länger als die Beschreibung) - optional

In unserem vorherigen Beispiel wurde der Titel auch gleich als Beschreibung (da nicht explizit angegeben) verwendet.

Die drei wichtigsten Tags zum Kommentieren einer Funktion sind: `@param, @examples` und `@return`. 
Weitere Tags sind auf der Seite [Documenting functions](http://r-pkgs.had.co.nz/man.html#man-functions) beschrieben.


Mit den Befehlen `\emph{}, \strong{}`, `\code{}` und `\pkg{}` (Paketname) kann der Text formatiert werden. 

Ebenso können Links zu anderen Dokumentationen

* `\code{\link{function}}`: Funktion `function` im eigenen Paket
* `\code{\link[devtools]{load_all}}`: Funktionen aus anderen Paketen, hier `devtools::load_all`

oder dem Netz

* `\url{http://rstudio.com}`
* `\href{http://rstudio.com}{RStudio}`
* `\email{haug@@tum.de}` (`@@` ist nötig, da `@` für die Tags reserviert ist)


Für weitere Formatierungen siehe, http://r-pkgs.had.co.nz/man.html#text-formatting


## NAMESPACE

Es ist nicht nur für das Erstellen von Paketen hilfreich das Konzept eines `NAMESPACE` verstanden zu haben, sondern auch für das generelle Arbeiten mit R. Man unterscheidet dabei **Import** `NAMESPACES` und **Export** `NAMESPACES`. 

**Import** legt dabei fest wie die Funktion eines Pakets eine Funktion in einem anderen Paket findet. Betrachten wir z.B.


```r
> nrow
```

```
## function (x) 
## dim(x)[1L]
## <bytecode: 0x0000000007872400>
## <environment: namespace:base>
```
Diese Funktion ist definiert in Abhängigkeit von `dim()` aus dem `base` Paket. Auch wenn wir die Funktion

```r
> dim <- function(x) c(1,1)
> dim(mtcars)
```

```
## [1] 1 1
```
definieren, findet `nrow()` trotzdem die "richtige" Funktion

```r
> nrow(mtcars)
```

```
## [1] 32
```
da sie den `NAMESPACE` des `base` Pakets verwendet.

**Export** legt fest welche Funktionen eines Pakets außerhalb des Pakets zur Verfügung stehen sollen. Dies müssen/sollten nicht alle Funktionen sein. Ein Paket kann also interne Funktionen haben, die nur intern verwendet werden können.

Über den Operator `::` wird explizit auf den NAMESPACE eines Pakets zugegriffen. Auf diese Art und Weise vermeidet man auch Konflikte zwischen Funktionen aus verschiedenen Paketen, die den gleichem Namen haben. In einem solchen Fall (verschiedene Funktionen mit gleichem Namen) kommt es darauf an welches Paket zuletzt geladen wurde. Die Funktion aus diesem Paket würde dann verwendet werden, wenn in `.GlobalEnv` eine Funktion mit diesem Namen aufgerufen wird. Die Reihenfolge der Pakete ist im Suchpfad


```r
> search()
```

```
##  [1] ".GlobalEnv"        "devtools_shims"    "package:mnRpkt"   
##  [4] "package:devtools"  "package:stats"     "package:graphics" 
##  [7] "package:grDevices" "package:utils"     "package:datasets" 
## [10] "package:methods"   "Autoloads"         "package:base"
```
angegeben, siehe auch [Environments und Scooping](Scoping.html#environments-und-scoping).

In der `NAMESPACE` Datei beschreiben nun

* `export()`: exportiert Funktionen
* `exportPattern()`: exportiert alle Funktionen, die zu einem bestimmten Schema passen
* `S3method()`: exportiert `S3` Methoden

den Export und

* `import()`: importiert alle Funktionen aus einem Paket
* `importFrom()`: importiert eine bestimmte Funktion aus einem Paket
* `useDynLib()`: importiert eine Funktion aus C

den Import. Export-Einträge in der `NAMESPACE` Datei können leicht über `roxygen` Kommentare erzeugt werden.
Über einen `@export` Tag wird angegeben welche Funktion exportiert werden soll

```{}
#' @export
export_function <- function(...){
  ... 
}
```
Je nach Objekttyp wird dann `export(), exportPattern()` oder `S3method()` im `NAMESPACE` verwendet. Generell sollte man sich an die Regel halten lieber weniger als mehr Funktionen zu exportieren, wenn man das Paket an Dritte weitergeben will.

Beim Importieren sollte man noch restriktiver sein. Man sollte über `Imports` in der `DESCRIPTION` Datei sicherstellen, dass alle notwendigen Pakete installiert und geladen sind. Danach kann einfach über den `::` Operator in eigenen Funktionen auf Funktionen aus anderen geladenen Paketen zugegriffen werden, also z.B. `pkg_name::funktion_aus_pkg_name()`.

Dies erhöht die spätere Lesbarkeit der eigenen Programme und vermeidet potentielle Konflikte, die beim Importieren mehrerer Pakete - die dann wieder Funktionen mit gleichem Namen besitzen - auftreten können.

Muss eine Funktion häufig verwendet werden - und man will nicht dauernd `::` verwenden - so sollte man über `importFrom()` eher nur die Funktion und nicht das ganze Paket importieren.


## Daten

Daten, die das Paket enthalten sollte, legt man im Verzeichnis `/data` ab. Als Format sollte man `.rda` wählen (ist aber kein Muss). Der einfachste Weg dies zu berücksichtigen ist die Funktion `devtools::use_data()` zu verwenden.


```r
> x <- sample(1:10, 50, replace = TRUE)
> devtools::use_data(x, mtcars)
```

Dieser Befehl speichert die Objekte `x` und `mtcars` in den Dateien `data/x.rda` und `data/mtcars.rda` ab. Der Dateiname stimmt also mit dem Objektnamen überein. Datensätze sollten auf jeden Fall dokumentiert werden. Dazu fügt man einfach in eine der `.R` Dateien im Verzeichnis `/R` einen weiteren `roxygen2` Absatz ein. Für das Dokumentieren von Datensätzen stehen zwei weitere Tags bereit. Über `@format` beschreibt man das genaue Format der Daten, z.B. die einzelnen Spalten eines Data Frames, und mit `@source` gibt man (falls vorhanden) die Datenquelle an. 

Die Beschreibung des Datensatzes `x` könnte also z.B. so aussehen

```{}
#' Mein Datensatz
#' 
#' Ein Datensatz bestehend aus 50 Zahlen gezogen aus den 
#' Zahlen 1 bis 10 mit Zuruecklegen
#' 
#' @format Ein Integer Vektor der Laenge 50
"x"
```

## C++

Will man in seinem Paket R Funktionen verwenden, die mithilfe von `Rcpp` aus kompiliertem C++ Code entstanden sind, so sollte man zur Vorbereitung


```r
> devtools::use_rcpp()
```
ausführen. Danach ist das das Verzeichnis `/src` angelegt und `Rcpp` zu den Feldern `LinkingTo` und `Imports` in der `DESCRIPTION` Datei hinzugefügt. Außerdem wird man aufgefordert die roxygen Tags

```{}
#' @useDynLib meineRgruppeAU
#' @importFrom Rcpp sourceCpp
```

in eine der `.R` Dateien einzufügen. Ebenso wie in die `.R` Dateien kann auch in die `.cpp` Dateien die roxygen Dokumentation eingefügt werden. Allerdings verwendet man `//'` statt `#'`. Als Beispiel betrachten wir die Funktionn `sum_C()` aus dem Kapitel [Rcpp](Rcpp.html).

```{}
#include <Rcpp.h>
using namespace Rcpp;

//' Berechnet die Summe der Eintraege des Vektors \code{x}
//' 
//' @param x Ein numerischer Vektor.
//' @export
// [[Rcpp::export]]
double sum_C(NumericVector x) {
  int n = x.size();
  double summe = 0;
  for(int i = 0; i < n; ++i) {
    summe += x[i];
  }
  return summe;
}
```


Nun dokumentieren wir erneut unser Paket


```r
> devtools::document("mnRpkt")
```

```
## Updating mnRpkt documentation
## Loading mnRpkt
```

```
## Writing NAMESPACE
## Writing sum_C.Rd
## Writing add_2.Rd
## Writing x.Rd
## Writing sum_R.Rd
```
Danach sieht der `NAMESPACE` folgendermaßen aus
```{}
# Generated by roxygen2 (4.1.1): do not edit by hand

export(sum_C)
importFrom(Rcpp,sourceCpp)
useDynLib(mnRpkt)
```

Wir erkennen, dass nur die Funktion `sum_C()` exportiert wird. Aus dem Paket `Rcpp` wurde die Funktion `sourceCpp()` importiert.

Nun erstellen wir erneut das Paket


```r
> devtools::build("mnRpkt")
```

```
## "C:/PROGRA~1/R/R-32~1.2/bin/x64/R" --no-site-file --no-environ --no-save  \
##   --no-restore CMD build "C:\Users\haug\Documents\GitHub\pmr\mnRpkt"  \
##   --no-resave-data --no-manual
```

```
## [1] "C:/Users/haug/Documents/GitHub/pmr/mnRpkt_0.0.0.9000.tar.gz"
```
Das Paket enthält die Funktionen `sum_R()`, `sum_C()` und `add_2()`. Da nur `sum_C()` exportiert wird, kann nur diese Funktion direkt aufgerufen werden. Um auf die anderen beiden Funktionen zugreifen zu können, muss man den Operator `:::` verwenden.


```r
> sum_C(1:10)
```

```
## [1] 55
```

```r
> mnRpkt:::sum_R(1:10)
```

```
## [1] 55
```

```r
> mnRpkt:::add_2(3, 4)
```

```
## [1] 7
```

Ebenso können wir den Datensatz `x` laden und uns die Beschreibung des Datensatzes ansehen.


```r
> data(x)
> table(x)
```

```
## x
##  1  2  3  4  5  6  7  8  9 10 
##  6  8  3  5  6  7  2  2  5  6
```

```r
> help(x)
```
![helpx](help_x.PNG)


Der Befehl `devtools::build("mnRpkt")` hat auch eine `tar.gz` Datei erzeugt (liegt im Ordner überhalb des "Paketordners"), die nun mit anderen R Nutzern geteilt werden kann. 


## Literatur zum Erstellen von Paketen

Dieser Abschnitt hat einen kleinen Einblick in das Erstellen eigener Pakete gegeben. Allerdings wurden viele weitere Aspekte nicht angesprochen. Weitere Informationen zu R Paketen findet man z.B. im Buch

![R packages](http://akamaicovers.oreilly.com/images/0636920034421/cat.gif)

Wickham, H. (2015). [R Packages](http://shop.oreilly.com/product/0636920034421.do?sortby=publicationDate). O'Reilly Media.

Zu diesem Buch gibt es noch eine eigene [Webseite](http://r-pkgs.had.co.nz/).

