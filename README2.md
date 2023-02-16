### AmiantRuntimeEnvironment

- Amiant: die Power einer LowLevel-Programmierung in einer sicheren Umgebung

Amiant ist eine Programmiersprache, welche die Syntax von LISP modernisiert.

### Ideen und Hello World

Kernfunktionen der Sprache sind:
- die gesamte Laufzeitumgebung ist in plain-C geschrieben (es gibt keine Abhängigkeiten von irgendwelchen Header-Dateien), was sie für neue Plattformen und Embedded-Systeme attraktiv macht 
- die AVM besitzt eine hohe Geschwindigkeit, die mit nativen Erweiterungsmöglichkeiten erhöht werden kann
- die freie S-Syntax erlaubt es, die bei LISP häufig unnötigen und unübersichtlichen Klammern zu überwinden, ohne dabei beim Parsing einen Geschwindigkeitsnachteil zu erhalten
- Amiant besitzt einen ARC-GC, sodass keine Speicherlecks entstehen

Ein Hello World Programm in Amiant:
```
println "Hello World!";
```

### Syntax

Ambient besitzt eine freie S-Notation. Eine S-Notation verlangt eine explizite Klammerung von Ausdrücken: `(expression)` oder `(expression1 expression2)`. Ausdrücke können geschachtelt werden: `(expression1 (expression2 expression3))`. Die Besonderheit bei Amiant ist, dass der Klammertyp frei gewählt werden kann. Hierbei stehen drei Typen zur Verfügung: `()`, `{}`, `[]`. Es gibt keine Beschränkungen, die angeben, welcher Klammertyp benutzt werden soll. In der Verwendung können die Klammertypen auch gemischt verwendet werden. Es gibt hierbei nur eine Restriktion: jede geöffnete Klammer benötigt eine geschlossene Klammer gleichen Typs! Folgender Ausdruck ist also ungültig: `(expression}`, während dieser hier korrekt wäre: `(expression1 {expression2 [expression3]})`. Zu der freien Wahl des Klammertyps kommt zusätzlich das Semikolon `;` hinzu, welches eine geöffnete und geschlossene Klammer ersetzt. Der Ausdruck `(expression1)` wäre auch folgendermaßen korrekt: `expression1;`. Semikolons können auch innerhalb von Klammerungen genutzt werden. Ein gutes Beispiel ist die Definition einer Funktion: 
```
function foo {
    println "Hello";
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

### Kommentare
Zeilenkommentare können mit dem Symbol `#` gestartet werden, und gehen bis ans Ende der Zeile. Mehrzeilige Kommentare gibt es nicht. Für diesen Fall muss jede Zeile des Kommentars mit dem Kommentarzeichen beginnen:
````
# Das ist ein Kommentar,
# der über mehrere Zeilen
# verteilt ist
````
### Datentypen I

| Datentyp | Bedeutung             |
| :---:    |     :---:             |
| Number   | Zahlen jeden Typs     |
| String   | Zeichenketten         |
| Byte     | Ein Byte (8 Bit)      |
| ByteArray| Byteabfolge           |
| List     | Liste                 |
| Map      | Map (Key-Value-Pairs) |
| Matrix   | Matrizen              |

Zahlen und Bytes sind die einzigen Datentypen, die bei Übertragungen kopiert werden! Bei allen anderen Datentypen wird der Wert kopiert (es wird eine Referenz auf die Originaldaten übergeben). Diese Referenz sorgt dafür, dass Amiant weniger Speicher verbraucht. Der Operator `copy` veranlasst der AVM eine Kopie der übergebenen Daten anzufertigen:

```
var name "Booktitle";
var name2 name; # name1 und name2 weisen auf den gleichen String im Speicher
     # der String "Booktitle" existiert nur ein Mal im Speicher!

assign name2 copy name; # name1 und name2 weisen auf unterschiedliche Stellen im Speicher

     # Nun existiert der String "Booktitle" zwei Male im Speicher

```

### Keywords
Folgende Keywords sind reserviert, und können deshalb nicht außerhalb ihres Kontextes gebraucht werden:

| Keyword | Bedeutung     |
| :---:   |     :---:     |
| pi      | 3.14159...    |


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
Listen können erzeugt werden, in dem eine Anzahl an Objekten geklammert werden: `($1 $2 $3)` ergibt eine Liste, die die Zahlen `1`, `2` und `3` enthält. Listen besitzen keine Typbeschränkung. Somit kann eine Liste Objekte unterschiedlicher Datentypen enthalten. Zugriff auf die in der Liste befindlichen Objekte bekommt man über den `listget`-Operator. Der Index einer Liste beginnt bei Null.
Der Ausdruck `listget $2 ($2 $4 $6 $8)` gibt den Wert `6` zurück.

### Map
Eine Map ist eine ungeordnete Sammlung von Key-Value-Pairs. Ein Key ist immer ein String, und besitzt Daten. Jeder Key kann nur einmal vorkommen, ansonsten werden die Daten überschrieben.

### Typumwandlungen
Die Überführung von einem Datentyp zu einem anderen ist eine unsichere Operation.

### Variablen
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
`(assign i $5)` weist der Variable 'i' den Wert `5` zu.
Variablen können gelöscht werden, in dem der `delete`-Operator auf ihnen ausgeführt wird: `delete i;`
Amiant verwaltet den Speicher und löscht ungenutzte Variablen, sobald keine Referenzen mehr auf sie zeigen. Variablen müssen also nicht von Hand gelöscht werden. Der `delete`-Operator ist dennoch implementiert, um zur Laufzeit einige Optimierungen des Programmierers zuzulassen.

### Funktionen

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

### If-Verzweigungen

Bedingte Verzweigungen werden durch den `if`-Operator realisiert. Die allgemeinen Formen einer if-Abzweigung und if-Else-Abzweigung sehen folgendermaßen aus:

If-Abzweigung:
```
if condition then {
    # Code here
};
```

If-Else-Abzweigung:
```
if condition then {
    # if true
} else {
    # if false
};
```

### Schleifen
In Amiant gibt es zwei Schleifentypen. Zunächst gibt es die bedingte Schleife, die in C-ähnlichen Sprachen auch while-Schleife gennant wird:

```
while condition {
    # Code here
};
```
Anstelle des Keywords `while` ist auch das Keyword `loop` für diese Operation belegt.

Der zweite Schleifentyp ist eine for-each Schleife, die jedes Element aus einer Liste nimmt, und es zur Verfügung stellt.


Jeder Schleifenkörper stellt einen eigenen Scope für die Variablen bereit.

```
for elementName in list {
    # Code here
};
```

### Konsole I

Ausgaben auf dem Standardoutput können mit dem `println`-Operator durchgeführt werden. Hierbei werden Strings und Zahlen wie sie sind ausgegeben. Listen werden mit dem Typ und ihrer Größe ausgegeben, z.B. `[list size=1]`. Für Maps gilt das gleiche wie bei Listen. Matrizen werden formatiert auf der Konsole ausgegeben. Somit nehmen Matrizen bei Bedarf mehrere Zeilen an Platz in der Konsole ein, je nach dem wie groß sie sind. Null wird als `[null]` und Any als `[any]` ausgegeben. Booleans werden mit ihrem Stringäquivalent ausgegeben: `true` und `false`.


### Mathematische Operationen I - Grundrechenarten

Die AVM stellt Grundrechenarten zur Verfügung, die intern und nicht in der Sprache selbst implementiert wurden, um die Geschwindigkeit der Operationen zu erhöhen.

| Operator | Bedeutung      |
|   :---:  |     :---:      |
|     +    | Addition       |
|     -    | Subtraktion    |
|     *    | Multiplikation |
|     /    | Divison        | 
|   sqrt   | Wurzel-2       |
|   log10  | Log-10         |

### Mathematische Operationen II - Matrizen

Matrizen können problemlos in Amiant selbst implementiert werden, in dem Listen miteinander verkettet werden. Allerdings stellt die AVM für diesen Fall Operatoren zur direkten Matrixmanipulation bereit, was die Geschwindigkeit dieser Operationen stark erhöht.

| Operation                       | Bedeutung                                                                                                      |
|   :---:                         |     :---:                                                                                                      |
|     `matrix n m stdValue`       | erzeugt eine n x m Matrix (Spalten, Zeilen) und gibt jeder Komponente den Wert `stdValue`                      |
|     `matrixset matrix n m value`| setzt den Wert der Matrix `matrix` an der Stelle `n` und `m` auf `value`. Der vorherige Wert wird überschrieben|
|     `matrixget matrix n m`      | gibt den Wert der Matrix `matrix` an der Stelle `n` und `m` zurück.                                            |

### Bitoperationen

Bitoperatoren bezeichnen die Bearbeitung einzelner Bits in einem Byte. Es gibt die Möglichkeit, Bits um eine Position nach rechts oder links zu bewegen, ein Bitweises-Und oder Oder zwischen zwei Bytes zu vollziehen, oder eine bitweise Negation durchzuführen.

| Operation                 | Bedeutung                              |
|   :---:                   |     :---:                              |
|     `(bitshiftr byte)`    | Bits um eine Stelle nach rechts bewegen|
|     `(bitshiftl byte)`    | Bits um eine Stelle nach links bewegen |
|     `(bitand byte1 byte2)`| Bitweises-Und                          |
|     `(bitor byte1 byte2)` | Bitweises-Oder                         | 
|     `(bitnot byte)`       | Bitweises-Nicht                        |

### Error Handling und Fehlercodes

Die AVM speichert immer nur den
Der letzte geworfene Fehler kann mit `(error)` geholt werden. Das gibt den Typ des Fehlers als String zurück.

| Operation            | Bedeutung                                                              |
|   :---:              |     :---:                                                              |
|     `(errorline)`    | gibt die Zeilennummer als Zahl zurück, an der der letzte Fehler auftrat|
|     `(errorfunction)`| gibt den Namen der Funktion zurück, in der der letzte Fehler auftrat   |
|     `(error clear)`  | löscht den letzten Fehler                                              |

Es gibt folgende Fehlertypen:


### Multithreading Konzepte und Clustering

Die AVM unterstützt nativ kein Multithreading! Aufgrund der angestrebten Unabhängigkeit darf auf keine Thread-Bibliothek zurückgegriffen werden, was dazu führt, das kein Multithreading zur Verfügung gestellt werden kann. Dennoch ist die AVM so konstruiert, dass sie Multithreading unterstützen könnte, wenn die dazugehörigen Bibliotheken zur Verfügung stünden. Hierfür müsste die die Implementierung für AVM allein vollzogen werden, und sie dann neu kompiliert werden. Damit dieser Vorgang so wenige Eingriffe wie nötig erfordert, stellt die AVM die nötigen Definitionen bereit.
Wenn die Anknüpfung mit der AVM gemacht wurde, stehen folgende Operationen zur Nutzung von Threads zur verfügung:

| Operation                             | Bedeutung                                                                                                     |
|   :---:                               |     :---:                                                                                                     |
|     `(thread threadName functionName)`| startet einen neuen Thread namens `threadName` (vom Typ String) und ruft darin die Funktion `functionName` auf|
|     `(threadstop)`                    | stoppt den aktuellen Thread. Wenn diese Funktion im main-Thread aufgerufen wird, passiert nichts              |
|     `(thread)`                        | gibt den Namen des aktuellen Threads als String zurück                                                        |

### Kompilierung der AVM
Der gesamte C-Code soll in einer einzigen Compilation-Unit verarbeitet werden, weshalb sämtliche Funktionalitäten in Header-Dateien abgelegt sind. Dies ist unüblich, gibt allerdings den Vorteil einer einfacheren Kompilierung. Die `amiant.h` enthält alle includes in der richtigen Reihenfolge. Es ist also nur notwendig, die `amiant.h` einzubinden.

### __RAW_MACHINE

### Konsole II

### Alternative Speicherallokation und -deallokation

### AmiantNativeBridge

### Debug-Mode

### Hinweise an LISP-Nutzer

Amiant ist kein LISP, sondern nutzt nur die Idee der Klammerung, erweitert sie, und fügt modernere Keywords ein. Zudem ist es nicht möglich, neue Sprachkonstruktionen in Amiant einzuführen, wie es bei LISP der Fall ist.
