## AmiantRuntimeEnvironment v0.9 (18.08.2023)

/Die Power einer LowLevel-Programmierung in einer sicheren Umgebung/

Amiant ist eine Programmiersprache, welche die Syntax von LISP modernisiert, auf keinen externen Headerdateien beruht, und Low-Level agiert.

### Ideen und Hello World

Kernfunktionen der Sprache sind:
- Die gesamte Laufzeitumgebung ist in plain-C geschrieben (es gibt keine Abhängigkeiten von irgendwelchen Header-Dateien - nichtmal den CXX Bibliotheken), was sie für neue Plattformen und Embedded-Systeme attraktiv macht 
- Die AVM besitzt eine hohe Geschwindigkeit, die mit nativen Erweiterungsmöglichkeiten erhöht werden kann
- Die freie S-Syntax erlaubt es, die bei LISP häufig unnötigen und unübersichtlichen Klammern zu überwinden, ohne dabei beim Parsing einen Geschwindigkeitsnachteil zu erhalten
- Amiant besitzt einen ARC-GC, sodass keine Speicherlecks entstehen, und die Wahrscheinlichkeit für Segfaults bei Null liegt. Zusätzlich besitzt man keine Halts, die beim Einsetzen eines GC entstehen, und die Performance verringern
- Amiant ist hochreflektiv. Ein Amiant-Programm kann sich während der Laufzeit komplett umprogrammieren, ohne neu gestartet werden zu müssen. Zusätzlich kann mit den MetaVMs dynamisch neuer Code im Programm ausgeführt werden
- Amiant bietet eine Unterstützung für paralleles Arbeiten mithilfe von Async-Await (sollte eine Threadimplementierung vorliegen)
- String interning sorgt für eine reduzierte Speichernutzung bei der Verwendung von literalen Strings
- Ein einfaches Error-System erlaubt die Möglichkeit der Fehlerbehandlung ohne Overhead oder unkontrolloliertem Control-Flow
- Listen und Structs sind beides Containertypen, die per Referenz übertragen werden.

Ein Hello World Programm in Amiant:
```
println "Hello World!";
```

### Syntax

Ambient besitzt eine freie S-Notation. Eine S-Notation verlangt eine explizite Klammerung von Ausdrücken: `(expression)` oder `(expression1 expression2)`. Ausdrücke können geschachtelt werden: `(expression1 (expression2 expression3))`. Die Besonderheit bei Amiant ist, dass der Klammertyp frei gewählt werden kann. Hierbei stehen drei Typen zur Verfügung: `()`, `{}`, `[]`. Es gibt keine Beschränkungen, die angeben, welcher Klammertyp benutzt werden soll. In der Verwendung können die Klammertypen auch gemischt verwendet werden. Es gibt hierbei nur eine Restriktion: jede geöffnete Klammer benötigt eine geschlossene Klammer gleichen Typs! Folgender Ausdruck ist also ungültig: `(expression}`, während dieser hier korrekt wäre: `(expression1 {expression2 [expression3]})`. Zu der freien Wahl des Klammertyps kommt zusätzlich das Semikolon `;` hinzu, welches eine geöffnete und geschlossene Klammer ersetzt. Der Ausdruck `(expression1)` wäre auch folgendermaßen korrekt: `expression1;`. Semikolons können auch innerhalb von Klammerungen genutzt werden. Ein gutes Beispiel ist die Definition einer Funktion: 
```
function foo {
    print "Hello "; # ohne Zeilenumbruch, danach mit
    println "World"
};
```

Diese Deklaration kann auch in strikter S-Notation (also ohne `;` erfolgen):

```
(function foo (
    (println "Hello")
    (println "World")
))
```
Es ist gut zu erkennen, wie das Semikolon jeweils ein `()`-Paar ersetzen kann. Der jeweils letzte Ausdruck benötigt kein Semikolon, da keine Abtrennung zum nächsten Ausdruck nötig ist.

### Blöcke

Ein durch Klammern abgegrenzter Bereich wird Block genannt. Ein Block hat folgende Eigenschaften:

1. Ein Block besitzt keinen eigenen Scope!
2. Steht nur ein Ausdruck in diesem Block, so ist der Rückgabewert dieses Blockes genau der Wert des enthaltenden Ausdrucks:

```
println ("Hallo Welt"); # Diese beiden Ausgaben...
println (((("Hallo Welt"))); # ... sind exakt gleich.

var i {
   "Amiant";
}; # Wert der Variable i ist der String "Amiant"
```

3. Stehen mehrere Ausdrücke durch ein Semikolon voneinander getrennt im Block, so ist der Rückgabewert des Blockes unbestimmt und null:

```
var j {
   "Amiant";
   "Something else";
}; # Wert der Variable j ist null
```

4. Mit dem Keyword `yield` kann bewusst gesteuert werden, welcher Ausdruck innerhalb eines Blockes sein Ergebnis darstellen soll:

```
# gleiches Beispiel wie bei Eigenschaft 2:

var j {
   "Amiant";
   var i $4;
   yield "Something else";
   "Dead code"; # unerreichbarer Code!
}; # Wert der Variable j ist nun "Something else" und nicht mehr null wie in Eigenschaft 2.

println i; # i ist hier plötzlich definiert, da ein Block keinen eigenen Scope besitzt (Eigenschaft 1).
```

Wichtig: Auch wenn es so aussieht, als würde man sich bei der Wertzuweisung der Variable j in einem neuen Scope befinden, ist es nicht so. Die Klammerung legt nur einen Block fest, und Blöcke haben keinen eigenen Scope - nur Funktionen, Schleifen, Async etc. haben einen. Noch wichtiger ist die Tatsache, dass Expressions, die nach der yield-Expression stehen, nicht erreicht werden können!

### Kommentare
Zeilenkommentare können mit dem Symbol `#` gestartet werden, und gehen bis ans Ende der Zeile. Mehrzeilige Kommentare gibt es nicht. Für diesen Fall muss jede Zeile des Kommentars mit dem Kommentarzeichen beginnen:
````
# Das ist ein Kommentar,
# der über mehrere Zeilen
# verteilt ist
````
### Datentypen

| Datentyp | Bedeutung                   |
| :---:    |     :---:                   |
| Number   | Zahlen jeden Typs           |
| String   | Zeichenketten               |
| List     | Liste                       |
| Struct   | Container (Key-Value-Pairs) |

Zahlen sind der einzige Datentyp, der bei Übertragungen kopiert wird! Bei allen anderen Datentypen wird eine Referenz kopiert. Diese Referenz sorgt dafür, dass Amiant weniger Speicher verbraucht. Der Operator `copy` veranlasst bei der AVM eine Kopie der übergebenen Daten anzufertigen:

```
var name1 "Booktitle";
var name2 name1; # name1 und name2 weisen auf den gleichen String im Speicher
     # der String "Booktitle" existiert nur ein Mal im Speicher!

assign name2 copy name1; # name1 und name2 weisen auf unterschiedliche Stellen im Speicher

     # Nun existiert der String "Booktitle" zwei Male im Speicher

```

### Keywords
Folgende Keywords sind reserviert, und können deshalb nicht außerhalb ihres Kontextes gebraucht werden:

| Keyword | Bedeutung     |
| :---:   |     :---:     |
| pi      | 3.14159...    |
| eul     | 2.71828...    |
| line    | ------------- |
| level   | ------------- |
| avm     | ------------- |

### Zahlen
Number-Literals müssen im Code mit einem `$` vorgeschrieben werden. Ansonsten wird die Zahl als fieldname gewertet. Mehr zu den fieldnames findet sich im Kapitel zu den Variablen. Folgende Ausdrücke resultieren nicht in Zahlen: `40`, `-5.2`. Korrekt müsste es heißen: `$40`, `$-5.2`. Sollte ein fehlerhafter Ausdruck hinter dem `$` erkannt werden, wird dieser Ausdruck als Zahl `0` gewertet. Es ist ausschließlich die Notation in Dezimalzahlen möglich.

### String
Strings werden im Code mit Anführungszeichen markiert. Zeichenketten müssen also folgendermaßen gekapselt werden: `"Hallo Welt!"`. Zeilenumbrüche innerhalb des Strings werden mit gewertet. Somit sind mehrzeilige Strings problemlos möglich. Wenn das `"`-Symbol Teil des Strings werden soll, muss es escaped werden: `"\""`.
Ein einzeiliger String: `"Diese Zeichenkette liegt auf einer Zeile."`
Ein mehrzeiliger String:

```
"Diese Zeichenkette liegt
  auf
mehreren Zeilen."
```
Bei den mehrzeiligen Strings werden alle Zeichen zwischen den `"` beachtet! Somit werden die drei Leerzeichen vor `auf` auch gewertet.

### Liste
Listen können mit dem `list`-Keyword erzeugt werden: `list $1 $2 $3` ergibt eine Liste, die die Zahlen `1`, `2` und `3` enthält. Listen dürfen keine Listen oder Structs als Inhalt besitzen. Allerdings kann eine Liste Objekte unterschiedlicher Datentypen enthalten, solange sie primitiv sind (eine Liste kann auch Typen mischen). Zugriff auf die in der Liste befindlichen Objekte bekommt man über den `get`-Operator. Der Index einer Liste beginnt bei Null.
Der Ausdruck `get ($2 $4 $6 $8) $2` gibt den Wert `6` zurück. Man kann nach der Erzeugung Daten an eine bestehende Liste anhängen, indem man das `put`-Keyword verwendet: `put ($4 $5 $6) $7`

### Struct

Ein Struct ist eine Art Map, die unter ein Keyword Daten speichern und über das Keyword wieder zurückgeben kann. Structs in Amiant unterscheiden sich deshalb erheblich von denen in C oder C++. In Amiant ist ein Struct schlicht ein Container, und nicht ein Type, der sich aus seinen Subtypen zusammensetzt. Ein Struct darf auch leer sein. Daten können aus einem Struct nicht entfernt werden, allerdings werden die alten Daten gelöscht, wenn man unter einem bestehenden Keyword neue Daten ablegen möchte. Das Keyword darf jeden Wert jeder Primitiven annehmen. So sind `null`, `$4`, `"value1"` allesamt gültige Keywords. In einem Struct wird garantiert, dass unter einem Keyword immer nur einmal Daten liegen. Doppelte Keywords sind nicht möglich. Ein Struct wird folgend erzeugt und verwendet:

```
var data struct; # erzeugt ein Struct und speichert es in die Variable data

put data "age_joe" $4; # legt Daten in das Struct

println get data "age_joe"; # gibt die Daten aus dem Struct zurück

```

Es fällt auf, dass sowohl Listen als auch Structs die gleichen Keywörter `put` und `get` verwenden. Bei Structs erwartet der put-Operator logischerweise ein Argument mehr als bei einer Liste.

### Typumwandlungen
Die Überführung von einem Datentyp zu einem anderen ist eine unsichere Operation.

### Variablen, Konstanten und Lazy
In jedem Scope kann eine Variable gleichen Namens immer nur einmal definiert werden. Die Scopes sind hierarchisch organisiert. Eine Variable wird mit dem Keyword `var` erzeugt:
```
var i $0;
```
Hierbei wird eine Variable mit dem Namen `i` erzeugt, die den Wert `4` enthält und vom Typ `Number` ist.

Auf Variablen kann mit ihrem Namen zugegriffen werden.
```
println i; # gibt den Wert von i auf der Konsole aus
```

Variablen können mit dem `assign`-Operator überschrieben werden:
`(assign i $5)` weist der Variable `i` den Wert `5` zu.
Wenn eine Variable einen Typ während der Initialisierung angenommen hat, so darf man diese Variable nur mit einem Wert gleichen Typs überschreiben. Die einzige Ausnahme ist der Typ Null. Eine Variable, die den Wert `null` besitzt, gilt als Typuninitialisiert, und kann mithilfe eines Assigns auf einen festen Typ gebracht werden.

Amiant verwaltet den Speicher und löscht ungenutzte Variablen, sobald keine Referenzen mehr auf sie zeigen. Variablen müssen also nicht von Hand gelöscht werden. Der `delete`-Operator ist dennoch implementiert, um zur Laufzeit einige Optimierungen des Programmierers zuzulassen.

Eine Variable kann im Nachhinein als konstant markiert werden, sodass ein nachträgliches Bearbeiten des Wertes nicht mehr möglich ist:

```
var x $4; # normale Variable
constant x; # jetzt kann x nicht mehr bearbeitet werden

```

Wenn man eine Variable nicht initialisiert, wird sie automatisch den Wert `null` annehmen:

```
var i; # i hat den Wert null
```

Variablen können als `lazy` definiert sein. Eine Lazyvar besitzt solange den Wert null, bis sie im Code als Field-Name verwendet wird. Erst dann wird ihr Wert (der hinter ihrer Definition steht) berechnet. Damit kann Rechenzeit und Arbeitsspeicher gespart werden, und Definitionen erst dann erfolgen, wenn man die Variable auch wirklich braucht.

```
lazy i ~load4KVideo;

# i hat hier den Wert null. Die Funktion wurde noch nicht aufgerufen, und kein RAM ist belegt.

~renderSomethingElse; # i ist noch immer nicht definiert, und nimmt somit keinen großen Platz ein. Der Ram ist frei für das Rendering.

~play video:i; # jetzt erst wird i geladen

# Es wurde Speicherplatz und Rechenkapazität nur in dem Moment benötigt, wo es unabdingbar war 

```

### Funktionen I - Allgemeines

Funktionen können im Amiant mit dem Funktionsoperator erzeugt werden. Sie werden dabei in dem aktuellen Scope erzeugt, und sind auch nur dort aufrufbar. Wenn eine Funktion bereits in dem Scope definiert wurde, kann sie nicht erstellt werden. Beim Funktionsaufruf wird zunächst im aktuellen Scope nach der aufzurufenden Funktion suchen, ehe hieriarchisch nach oben gesucht wurde.

Funktionsdefinition:
```
function foo {
   # Code here in a new scope
};
```

Funktionsaufruf:
```
function add {
   return + n m;
};

~add n:$4 m:$5;
```
Funktionen können mit dem Operator `~` aufgerufen werden. Dabei muss der Name der Funktion angegeben werden. Der Funktion kann beim Aufruf Argumente übergeben werden. Diese müssen immer mit einem Parameter-Label markiert sein! Die übergebenen Werte werden der Funktion als lokale Variablen während des Aufrufes zur Verfügung gestellt. Die add-Funktion benötigt `n` und `m` im Funktionskörper. Deshalb müssen diese explizit beim Aufruf übergeben werden. Die Reihenfolge ist dabei egal. Wenn sie nicht übergeben werden, wird die Funktion trotzdem ausgeführt, und an der entsprechenden Stelle wird es zu einem Fehler kommen.

Mit dem Keyword `return` kann die Funktionsausführung an dieser Stelle beendet werden. Man kann mit oder ohne Wert eine Funktion abbrechen. Eine Funktion, die keinen expliziten Wert zurückgibt, gibt implizit den Wert `null` zurück.

Sollte es innerhalb einer Funktion zu einem Fehler kommt, so wird mit Standardwerten weiter gearbeitet. Die Funktion bricht ihre Ausführung nicht ab.

### Funktionen II - Contacts und Debugging

Da Amiant eine Sprache ist, die LowLevel-Scripting verlangt, ist das Thema Sicherheit von großer Bedeutung. Fehler können während der Laufzeit auftauchen, und sie werden intern so gehandhabt, dass ein Absturz der AVM in jedem Falle auszuschließen ist. Aus diesem Grunde wird ein Fehler nicht zum Abbruch des Programmes führen, sondern zum Weiterarbeiten mit Standardwerten. In manchen Situationen ist das Arbeiten mit solchen Standardwerten kritisch oder gar gefährlich. Dazu gibt Amiant zwei Möglichkeiten der Überprüfung:

1) Assert:

Das Keyword `assert` prüft, ob ein bestimmter Ausdruck wahr ist. Sollte er wahr sein, läuft das Programm einfach weiter. Sollte der Ausdruck falsch sein, bricht das gesamte Programm an dieser Stelle mit einer Fehlermeldung ab. Ein Assert kann also das Weiterlaufen eines fehlerhaften Programmes verhindern.

2) Contract:

Ein weiterer Schlüssel in der Sicherheitsarchitektur Amiants ist das Nutzen von Contracts. Contracts verweisen auf eine Eigenschaft von Funktionen innerhalb der AVM. Eine Funktion kann frei sein, oder unter Vertrag stehen. Eine freie Funktion ist eine gewöhnliche Funktion, die bei Aufruf ihren Code ausführt. Sollte es innerhalb der Funktion zu Fehlern kommen, so wird die Funktion mit Standardwerten weiterarbeiten, ganz gleich was das Ergebnis davon ist. Sollte eine Funktion jedoch unter Vertrag stehen, ist sie verpflichtet, für die Richtigkeit der in ihr generierten Daten die Verantwortung zu übernehmen. Sollten die Daten allesamt korrekt sein, so wird diese Funktion schlicht ihren Code ausführen. Sollten die Daten inkorrekt sein, so liegt eine Vertragsverletzung im Bezug zur AVM vor, sodass die AVM diese Funktion dann sperrt. Die Funktion bricht an dem Keyword sofort ab, und kann danach während der aktuellen Laufzeit des Programmes nicht mehr aufgerufen werden. Sollte sie dennoch irgendwo aufgerufen werden, so wird mit null an dieser Stelle abgebrochen. Ein Ausführen der Funktion ist somit unmöglich. Diese Architektur erlaubt es, fehlerhafte Codeabschnitte zur Laufzeit zu entfernen, sodass zu jedem Zeitpunkt ein fehlerfreies Programm garantiert werden kann. Voraussetzung dafür ist das konsequente Nutzen von Contracts.

```
contract > i $0; # garantiert der AVM, dass der Wert der Variablen immer größer Null ist
```

Die Sicherheit des Codes kann damit zur Laufzeit erhöht werden, in dem fehlerbehaftete Äste innerhalb des Programms dadurch nach und nach deaktiviert werden. Eine gesperrte Funktion kann durch einen mount über den gleichen Namen jedoch wieder nutzbar gemacht werden (siehe Reflection)
Eine Verletzung des Contracts in der globalen Funktion (globaler Scope) führt nicht zu einer Sperrung. Die obersten Funktionen (async-Blöcke, Meta Vm globaler Scope)sind somit immun gegen contracts.

### Funktionen III - Funktionale Programmierung

In Amiant sind Funktionen first-class-objects. Funktionen können also selbst als Wert übergeben werden, sodass man sie durch das Programm bewegen kann. Um eine Variable zu erstellen, die einen Pointer auf eine Funktion enthält, muss man den Function-Pointer-Operator `\` nutzen. Dieser sucht im aktuellen Scope nach der Funktion, und gibt einen Zeiger darauf zurück. Sollte die Funktion nicht gefunden werden, wird `null` zurückgegeben. Der `~` Operator erwartet ein Fieldname und keine Expression, sodass man die Funktion, die man als Pointer besitzt, nur dann ausführen kann, wenn man den Pointer vorher in eine Variable legt, und diese dann hinter dem `~` Operator schreibt:

```
function add {
    return + n n;
};

var addFunc \add;

println ~addFunc n:$4; # gibt 8 auf der Konsole aus
```

### Amiant-Meta-VM

Die AVM ist in der Lage, dynamisch neuen Code auszuführen. Dieser Code läuft dann in einem gesonderten Bereich, sodass Zugriffe in andere Bereiche nicht möglich sind. Um innerhalb eines laufenden Programms weiteren Amiant-Code auszuführen, muss dieser Code lediglich als String vorliegen, und mithilfe des Keywords `amiant` ausgeführt werden.

```
amiant "println $4"; # startet ein weiteres Programm, das die Zahl 4 auf der Konsole ausgibt
```

Es ist wichtig zu beachten, dass es sich hierbei nicht um eine Multithreading-Lösung handelt! Der Programmfluss stoppt an dieser Stelle, bis das Unterprogramm seine Ausführung beendet hat. Aber mit einer geeigneten Implementierung einer Threading-Bibliothek über die Native-Schnittstelle ist die Auslagerung in einen separaten Thread ohne Probleme möglich. Da das innere Programm keinen Zugriff auf das äußere Programm hat, kann es dort nicht zu problematischen Interaktionen kommen. Umgekehrt besteht zwar kein direkter Zugriff, wohl aber ein indirekter: Daten können in das Programm gebracht werden, in dem sie in den String des Programmcodes beispielsweise als Variablen geschrieben werden. Da das äußere Programm den Programmcode des inneren Programmes besitzt, kann der eben so angepasst werden, dass bestimmte Informationen vor dem Start des inneren Programms eben direkt einprogrammiert werden.
Es ist darauf hinzuweisen, dass trotz dieser Abkapselung es nicht zu einem Starten einer neuen und eigenständigen AVM kommt! Intern läuft weiterhin nur eine einzige AVM, die sich jedoch rekursiv verwenden kann. Das schlägt sich beispielsweise in den internen Speicherstatistiken wieder. Ein äußeres und ein inneres Programm tragen gleichermaßen zu den selben Statistiken bei. Auch das Akquirieren neuen Speichers erfolgt über die exakt gleichen Funktionen. Diese Fähigkeit von Amiant erleichtert es, eine AVM in einem Kernel (oder als Proto-Kernel) laufen zu lassen, die dann wiederum Unterprogramme starten kann, jedoch nur eine Stelle besitzt, an der der gesamte Speicher verwaltet werden muss.

Jedes Amiant-Programm kann sofort beendet werden, in dem das `exit`-Keyword genutzt wird. Dabei wird nur das lokale Amiant-Programm beendet. Wenn ein Unterprogramm `exit` aufruft, so wird nur das Unterprogramm beendet, und das Oberprogramm setzt die Ausführung fort. Sollte das oberste Programm ein `exit` ausführen, so beendet sich die komplette AVM.

Es ist möglich, zur Laufzeit herauszufinden, in welchem Level der aktuelle Code ausgeführt wird. Als Level wird die Stufe in der Hierarchie der Programme und Unterprogramme bezeichnet. Das oberste Programm läuft auf Level 0. Wenn dieses Programm mithilfe des `amiant`-Keywords ein Unterprogramm startet, so liefe dieses Unterprogramm auf Level 1, usw. Den numerischen Wert des aktuellen Programmausführungslevels kann mit `level` zurückgegeben werden.

Hinweis: während es mehrere Programme auf Level 1, 2, ... geben kann, gibt es immer nur ein einziges Programm auf Level 0!

### If-Verzweigungen

Bedingte Verzweigungen werden durch den `if`-Operator realisiert. Die allgemeinen Formen einer if-Abzweigung und if-Else-Abzweigung sehen folgendermaßen aus:

If-Abzweigung:
```
if condition {
    # Code here
};
```

If-Else-Abzweigung:
```
if condition {
    # if true
} else {
    # if false
};
```

Ein If-Statement gibt immer den Wert des ausgeführten Blockes zurück:

```
var i $4;

println if (> i $5) {
    "Bigger";
} else {
    "Smaller";
}; # gibt "Smaller" auf der Konsole aus
```

### Schleifen
In Amiant gibt es zwei Schleifentypen. Zunächst gibt es die bedingte Schleife, die in C-ähnlichen Sprachen auch while-Schleife gennant wird:

```
while condition {
    # Code here
};
```
Anstelle des Keywords `while` ist auch das Keyword `loop` für diese Operation belegt.

Jeder Schleifenkörper stellt einen eigenen Scope für die Variablen bereit.

```
foreach elementName in list {
    # Code here
};
```

### Konsole I

Ausgaben auf dem Standardoutput können mit dem `println`-Operator durchgeführt werden. Hierbei werden Strings und Zahlen wie sie sind ausgegeben. Listen werden mit dem Typ und ihrer Größe ausgegeben, z.B. `[list size=1]`. Für Maps gilt das gleiche wie bei Listen. Matrizen werden formatiert auf der Konsole ausgegeben. Somit nehmen Matrizen bei Bedarf mehrere Zeilen an Platz in der Konsole ein, je nach dem wie groß sie sind. Null wird als `[null]` und Any als `[any]` ausgegeben. Booleans werden mit ihrem Stringäquivalent ausgegeben: `true` und `false`.


### Mathematische Operationen

Die AVM stellt Grundrechenarten zur Verfügung, die intern und nicht in der Sprache selbst implementiert wurden, um die Geschwindigkeit der Operationen zu erhöhen.
Zudem stehen diese Operationen auf allen Plattformen zur Verfügung, da die Implementierung dieser Rechenoperationen ohne Mithilfe von externen Bibliotheken erfolgt.

| Operator | Bedeutung      |
|   :---:  |     :---:      |
|     +    | Addition       |
|     -    | Subtraktion    |
|     *    | Multiplikation |
|     /    | Divison        |
|  modulo  | Modulo         |
|   sqrt   | Wurzel-2       |
|   log10  | Log-10         |
|   ln     | Log-E          |
|   sin    | Sinus          |
|   cos    | Kosinus        |
|   tan    | Tangens        |
|factorial | Fakultät       |
|   exp    | E hoch         |
|   ipow   | n hoch integer |

Amiant stellt eigene Implementierungen der mathematischen Operationen bereit (außer für +,-,*,/,modulo). Diese funktionieren teilweise gut, allerdings können die Berechnungen - gerade bei Logarithmen - mit großen Zahlen langsam und sehr ungenau werden. Alternativ kann über die Native Bridge eine systemeigene Implementierung diese mathematischen Operationen bereitgestellt werden.

### Error Handling und Fehlercodes

Die AVM speichert immer nur den

Der letzte geworfene Fehler kann mit `(error)` geholt werden. Das gibt den Typ des Fehlers als String zurück.

| Operation             | Bedeutung                                                              |
|   :---:               |     :---:                                                              |
|     `(error line)`    | gibt die Zeilennummer als Zahl zurück, an der der letzte Fehler auftrat|
|     `(error)`         | gibt den letzten Fehlercode in der aktuellen Funktion zurück           |

Es gibt folgende Fehlertypen:

| Error-Code | Bedeutung                                                                       |
|   :---:    |     :---:                                                                       |
|     0      | Es trat bisher kein Fehler auf                                                  |
|     1      | -                                                                               |
|     2      | Variable nicht gefunden                                                         |
|     3      | Falsche Syntax an einem Operator                                                |
|     4      | Es wurde bereits eine Funktion unter diesem Namen definiert                     |
|     5      | Es wurde bereits eine native Funktion unter diesem Namen definiert              |
|     6      | Die Variable wird bereits asynchron von einem async-Statement verwendet         |
|     7      | Es wurde versucht, eine asynchrone Variable zu verändern                        |
|     8      | Falscher Typ                                                                    |
|     9      | Falscher Token                                                                  |
|    10      | Funktion nicht definiert                                                        |
|    11      | Out of index                                                                    |
|    12      | Eine Variable wurde mit einem Wert überschrieben, der einen anderen Typ besitzt |
|    13      | Es wurde versucht, den Wert einer Konstanten zu verändern                       |
|    14      | Es wurde versucht, eine durch contract gesperrte Funktion aufzurufen            |
|    15      | Interner schwerwiegender Fehler                                                 |

In Amiant ist es möglich, eigene Fehlercodes zu setzen. Das geschieht mit dem `throw`-Keyword, das schlicht einen Ausdruck erwartet, der eine Zahl enthält - eben den Fehlercode. Throw unterbricht allerdings nicht den Codeflow! Die Ausführung wird fortgesetzt. Dennoch kann man damit eigene Fehlerroutinen implementieren, und auf Fehler reagieren. Fehlercodes sind ganze Zahlen - sie können auch negativ sein, und auch die obigen Fehlercodezahlen mitnutzen. Davon wird der Übersichtlichkeit wegen und wegen der großen Zahl an freien Fehlercodes abgeraten.

```

if (= (~fetchData) "404") {
    throw $404; # Fehler werfen
};

...

if(= (error) $404) {
    # Fehlerbehandlung
};

```

### Reflection

Amiant erhält während der Ausführung die vollständige Programmstruktur. Somit ist es möglich, Informationen dieser Struktur zur Laufzeit zu bekommen, aber auch dynamische Zugriffe in ihr zu erlauben.

`this` gibt den Namen der Funktion als String zurück, in der dieses Keyword steht.
`call <str>` ruft eine Funktion in diesem Scope über den Namen auf, und gibt das Ergebnis der Funktion zurück. Wichtig: Dieses Keyword kann keine Argumente an die Funktion übergeben!
`line` gibt die aktuelle Zeilennummer, in der dieses Keyword steht, als Zahl zurück. Die Zählung beginnt bei 1!
(`take <str>` greift auf eine Variable über ihren Namen aus dem Scope zurück, und gibt die Referenz darauf zurück, ansonsten einen Nullpointer.)
`rename <str> <str>` benennt eine Funktion während der Laufzeit um. Es ist wichtig zu beachten, dass die Funktion in ihrem Scope bleibt, und dass die Funktion möglicherweise einen Namen besitzt, der in dem Scope schon definiert ist.
`mount <str> <str>` hängt eine neue Funktion unter dem Namen und unter dem übergebenen Code in die AVM an dieser Stelle im Scope ein. Somit können partielle Codeerweiterungen problemlos erfolgen, ohne direkt eine Meta-VM starten zu müssen.
`unmount <str>` löscht eine Funktion aus dem aktuellen Scope. Ihr Aufruf ist danach nicht mehr möglich.
`defined <fieldName>` prüft, ob eine Variable in diesem Scope definiert wurde (gibt einen Boolean zurück)

Folgendes Beispielprogramm erzeugt eine Funktion und tauscht sie während der Laufzeit wieder aus:

```
function reflectionTest {
     mount "newFunc" "println $4;"; # hängt dynamisch eine neue Funktion ein

     call "newFunc"; # newFunc könnte auch normal mit ~newFunc; aufgerufen werden
};

~reflectionTest; # gibt 4 auf der Konsole aus

unmount "reflectionTest"; # löscht die Funktion reflectionTest

mount "reflectionTest" "print $5"; # hängt eine neue Funktion unter dem gleichen Namen ein

~reflectionTest; # nun wird die Zahl 5 auf der Konsole ausgegeben (die Funktion wurde ausgetauscht)

```

Hinweise:

1) Reflection ist ein mächtiges Werkzeug. Es werden generell keine Überprüfungen im Bezug auf doppelte oder falsche Benennungen durchgeführt! Das resultiert jedoch in keine Speicherprobleme innerhalb der VM. Allerdings ist nicht eindeutig definiert, welche Funktion aufgerufen wird, wenn es Mehrfachbenennungen durch Reflection gibt! Die Überprüfung der Namensdopplung gibt es nur in der normalen Funktionsdefinition über das `function`-Keyword.

2) Das Hinzufügen von Funktionen ist eine große Operation, da der übergebene String völlig neu geparst und verarbeitet werden muss. Es wird also von einem übermäßigen Gebrauch abgeraten.


### Async Await I - Konzept und Nutzung

Die AVM unterstützt paralleles Ausführen von Code mithilfe von Async-Await.

`async asyncVarName arg1:capture1 arg2:capture2 ... argn:capturen asyncExpression;`

Die Variable, auf die mit asyncVarName verwiesen wird, muss vom Typ Null sein, damit der async-Block in der Lage ist, ihren Wert mit beliebigen Typen zu überschreiben!

Hier ist ein kleines Beispiel, wie man Async Await in Amiant nutzen könnte:

```
var loginToken "12345";
var secureKey $12345;

var firstName; # Wert ist null
var lastName; # Wert ist null

async firstName token:loginToken key:secureKey { # der Block wird parallel ausgeführt
   return ~fetchSomeDataFromDataBase token:token key:key;
};

async lastName token:loginToken key:secureKey { # die Ausführung schreitet sofort weiter und führt diesen Block ebenso parallel aus
   return ~fetchSomeDataFromDataBase token:token key:key;
};

println string "Vorname: " (await firstName) " Nachname: " await lastName; # hier wird auf die Ergebnisse der einzelnen Operationen gewartet

```

Async erwartet als erstes Argument ein fieldName zu einer Variable, die am Ende der Ausführung des Async-Codes das Ergebnis eben dieses Codes enthalten soll. Die Variable wird als async markiert, und kann dann nicht zeitgleich von einem anderen async-Keywort genutzt werden. Das geht nur, wenn der async-Block fertig ist, und die Variable wieder freigibt. Auf eine asynchrone Variable kann immer zugegriffen werden. Greift man ohne `await` auf eine asynchrone Variable zu, so wird ihr aktueller Wert zurückgegeben, der nicht unbedingt der Rückgabewert des asynchronen Codes sein muss (dieser kann schließlich noch arbeiten). Um nun den Rückgabewert des asynchronen Blocks zu bekommen, muss man bei der Verwendung der Variable schlicht das Keyword `await` davor setzen. Der Codefluss wartet an der Stelle, bis die Variable vom async-Code freigegeben wurde. Danach wird ihr Wert erst aufgelöst.

Innerhalb der Async-Blöcke hat man keinen Zugriff auf außenstehende Funktionen oder Variablen! Allerdings bleibt der Zugriff auf alle Funktionen, die mit der Native-Bridge eingebunden sind, bestehen!
Jede aynchrone Codeblock läuft auf dem avm-level 2, sodass Zugriffe auf restart von dort aus nicht möglich sind.

Die asyncVar enthält dann das Ergebnis des asynchronen Abarbeitens. Der Wert der Variable wird vom async-Block verändert. Wichtig: auch Konstanten können als async-Variable verwendet werden! Wenn man auf eine async-Var zugreift, während sie vom async verwendet wird, wird sie immer `null` zurückgeben! Nur das await-Keyword kann auf den richtigen und fertigen Wert warten. Sobald die Variable wieder freigegeben ist, kann man sie wieder normal nutzen.

Async-Await funktioniert nur auf Level 0 und kann damit nicht in einer Meta-VM genutzt werden! Sollte man dennoch Async-Await nutzen, so wird dieses lediglich linear ausgeführt. Der Code funktioniert also trotzdem - nur die echte Parallelisierung per Threads fällt weg.

Sollte kein Threading unterstützt sein, so gibt `await` schlicht den aktuellen Wert der Variable zurück, und `async` wird synchron ausgeführt, sodass das Programm an dieser Stelle wartet, bis die Ausführung des async-Blockes vollendet wurde. Das Programm verhält sich also exakt gleich - es dauert nur etwas länger, da nicht parallel ausgeführt wird.

### Async Await II - Implementierung

Da reines C keine Multithreading unterstützt, muss eine eigene Implementierung erfolgen. Sollte die verwendete Plattform die pthread.h unterstützen, so kann in die parallel.h gegangen werden, und die entsprechen Zeilen in der `avmThreadStart(...)` auskommentiert werden, sowie das include-Statement über der Funktion. Zusätzlich muss in der `avmThreadWorking(void)` ein paar Zeilen weiter oben der Rückgabeboolean von `false` auf `true` gesetzt werden.
Wenn keine pthread.h zur Verfügung steht (beispielsweise unter Windows) muss in der `avmThreadStart(...)` an der entsprechenden Stelle eine andere Implementierung genutzt werden. Der Boolean in der `avmThreadWorking(void)` muss dennoch auf `true` gesetzt werden.

### Kompilierung der AVM

Der gesamte C-Code soll in einer einzigen Compilation-Unit verarbeitet werden, weshalb sämtliche Funktionalitäten in Header-Dateien abgelegt sind. Dies ist unüblich, gibt allerdings den Vorteil einer einfacheren Kompilierung. Die `amiant.h` enthält alle includes in der richtigen Reihenfolge. Es ist also nur notwendig, die `amiant.h` erfolgreich einzubinden.
Es wird empfohlen, die AVM unter C11 oder höher zu kompilieren. Unter C11 funktioniert die gesamte AVM wie gehabt, nur das Statistik-Modul (das außschließlich zu Debugzwecken verwendet werden sollte), kann dort aufgrund fehlender atomics unter Race-Conditions leiden. Das hat allerdings keinen Einfluss auf die Speichersicherheit der AVM! Diese bleibt unverändert funktionstüchtig!

Folgender Befehl unter gcc sollte verwendet werden, um keine Warnungen zu erhalten:

```
gcc main.c -o avm -std=c11 -Wint-to-pointer-cast
```

Ansonsten genügt auch schlicht ein:

```
gcc main.c -o avm
```

### AmiantNativeBridge

Amiant unterstützt das Aufrufen von C-Funktionen. Diese Funktionen müssen mit in die AVM kompiliert werden. Dabei geht man folgendermaßen vor:

1. Die Funktion, die von Amiant aufgerufen werden soll, muss folgende Signatur besitzen: `void LIST* functionName(struct LIST* data)`.
2. Die Funktion muss in einer .h definiert sein
3. Diese Header-Datei wird in der native_bridge.h von Amiant in der "INCLUDE-SEKTION FÜR DIE STANDARDBIBLIOTHEK HEADER FILES" includiert.
4. In der darunter liegenden Funktion `standardLibraryGetFunctions()` müssen die C-Funktionen für Amiant registriert werden. Das geschieht mithilfe folgender Zeile code: `standardLibraryAddStandardFunction(libraryFunctionsList, "funcName", 8, false, &functionName);` Dabei gibt es folgendes zu beachten: Der String "functionName" gibt an, unter welchem Namen die C-Funktion innerhalb von Amiant aus aufrufbar ist. Dieser Name muss nicht deckungsgleich mit dem Namen der C-Funktion sein. Die Zahl hinter dem String (in diesem Fall die 8) gibt an, wie viele Zeichen der Name lang ist. "funcName" besitzt 8 Buchstaben, also 8 Zeichen. Der Funktionsname für Amiant darf nur Zahlen und Buchstaben enthalten, ansonsten ist die Funktion nicht aufrufbar! Als letztes Argument muss der Funktionspointer zu der C-Funktion übergeben werden.

Es ist auch möglich C-Funktionen aufzurufen, die nicht mit in die AVM kompiliert wurden. Diese Funktionen müssen dann anderweitig in den Speicher geladen werden, und können dann mit `native fieldName functionPointerExpr` eingehangen werden. Dabei muss der fieldName der Name der Funktion sein, wie sie in Amiant benutzt werden kann, und die functionPointerExpr muss ein Ausdruck sein, der eine Zahl zurück gibt: die Adresse der C-Funktion. Die C-Funktion muss dabei wieder die obige Signatur `void LIST* functionName(struct LIST* data)` besitzen!

Beim Aufrufen von nativen C-Funktionen kann Amiant dieser Argumente übergeben. Diese Argumente verlieren bei der Übertragung allerdings ihren Namen, sodass nur noch die Daten an sich und ihre Reihenfolge garantiert werden kann. Die Datenliste data besitzt alle Amiant Pointer zu den übergebenen Daten. Weder die Liste noch die Pointer dürfen von der C-Funktion gelöscht oder Verändert werden! Die Datenhoheit besitzt weiterhin die AVM. Als Rückgabewert kann NULL zurückgegeben werden, oder allerdings eine eigene Datenliste. Es ist darauf zu achten, dass Pointer, die man in diese Rückgabeliste legt, nach dem Hinzufügen auch wieder freigibt, damit die Liste die einzige Datenhoheit über die Rückgabedaten besitzt. Es sollte darauf verzichtet werden, Pointer der data-Liste in die return Liste zu legen.

### Einstellungen und Informationen der AVM

Das Keyword `avm` gibt einen internen Zugriff auf die Amiant Virtual Machine (AVM). Hierbei kann auf die verwendete Version zugegriffen werden

`avm version` gibt die aktuelle Version der AVM als Zahl zurück.
`avm restart` beendet alle Meta-AVMs und startet das gesamte Programm neu. Dabei wird allerdings kein neuer Code eingelesen, sondern der vorhandene schlicht neu gestartet! Alle mit Reflections vorgenommene Änderungen am Code bleiben dabei erhalten! Diese Funktion steht nur der obersten AVM zur Verfügung (level 1), und kann von Meta-AVMs nicht genutzt werden.
`avm compver` gibt die Version des verwendeten C-Standards bei der Kompilierung zurück. Hierbei werden die offiziellen Versionsnamen verwendet. Bei C89/90 und tiefer wird standardmäßig `198901L` zurückgegeben, auch wenn es diese Versionsnummer offiziell nie gegeben hat.
`memory` gibt den gesamten aktuell genutzten Arbeitsspeicher aller (Unter-)AVMs zurück. Wichtig: bei C99 und tiefer kann es durch die Verwendung von async-await zu falschen Werten kommen. Das Statistikmodul benötigt atomics, die bei C99 und tiefer allerdings nicht vorhanden sind. 

### Hinweise an LISP-Nutzer

Amiant ist kein LISP, sondern nutzt nur die Idee der Klammerung, erweitert sie, und fügt modernere Keywords ein. Zudem ist es nicht möglich, neue Sprachkonstruktionen in Amiant einzuführen, wie es bei LISP der Fall ist.
