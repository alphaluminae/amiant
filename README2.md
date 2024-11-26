# AmiantRuntimeEnvironment v0.9.8 (03.09.2024)

/Die Power einer LowLevel-Programmierung in einer sicheren Umgebung/

Amiant ist eine modernisierte LISP-ähnliche Programmiersprache, die auf keinerlei externer Headerdateien beruht, ausschließlich in (ANSI-)C entwickelt wurde, Low-Level agieren kann, und eine kleine CodeBase besitzt.

## Einführung

### Konzept und Hello World

Die Kernfunktionen der Sprache lassen sich folgendermaßen beschreiben:
- Die gesamte Laufzeitumgebung ist in plain-C geschrieben (es gibt keine Abhängigkeiten von irgendwelchen Header-Dateien - nichtmal den CXX Bibliotheken), was sie für neue Plattformen und Embedded-Systeme attraktiv macht
- Die AVM besitzt eine hohe Geschwindigkeit, die mit nativen Erweiterungsmöglichkeiten weiter erhöht werden kann
- Die freie S-Syntax erlaubt es, die bei LISP häufig unnötigen und unübersichtlichen Klammern zu überwinden, ohne dabei beim Parsing einen Geschwindigkeitsnachteil zu erhalten
- Amiant besitzt einen ARC-GC, sodass keine Speicherprobleme und Segfaults entstehen. Zusätzlich besitzt die AVM keine Halts, die beim Einsetzen eines GC entstehen, und die Performance verringern
- Amiant ist hochreflektiv: ein Amiant-Programm kann sich während der Laufzeit komplett umprogrammieren, ohne neu gestartet werden zu müssen. Zusätzlich kann mit den MetaVMs dynamisch neuer Code im Programm ausgeführt werden
- Amiant bietet eine Unterstützung für paralleles Arbeiten mithilfe von Async-Await (sollte eine Threadimplementierung vorliegen)
- String-interning sorgt für eine reduzierte Speichernutzung bei der Verwendung von literalen Strings
- Ein einfaches Error-System erlaubt die Möglichkeit der Fehlerbehandlung ohne Overhead oder unkontrolloliertem Control-Flow
- Listen und Structs sind beides Containertypen, die per Referenz übertragen werden
- Der Datentyp VNumber erlaubt beliebig präzise Grundrechenoperatoren
- Duck-Typing erlaubt eine typisierte Programmierung

Ein Hallo-Welt-Programm in Amiant:
```
println "Hello World!";
```

### Syntax

Amiant besitzt eine freie S-Notation. Eine S-Notation verlangt für gewöhnlich eine explizite Klammerung von Ausdrücken: `(expression)` oder `(expression1 expression2)`. Ausdrücke können hierbei auch geschachtelt werden: `(expression1 (expression2 expression3))`. Die Besonderheit von Amiant ist, dass der Klammertyp frei gewählt werden darf. Es stehen drei Typen zur Verfügung: `()`, `{}`, `[]`, die keinerlei Beschränkungen in ihrer Verwendung besitzen. Die Klammertypen können auch gemischt verwendet werden, wobei es nur eine Restriktion gibt: __jede geöffnete Klammer benötigt eine geschlossene Klammer gleichen Typs__! Folgender Ausdruck ist also ungültig: `(expression}`, während dieser hier korrekt wäre: `(expression1 {expression2 [expression3]})`. Zu der freien Wahl des Klammertyps kommt zusätzlich das Semikolon `;` hinzu, welches eine geöffnete und geschlossene Klammer auf einer Ebene ersetzt. Der Ausdruck `(expression1)` wäre auch folgendermaßen korrekt: `expression1;`. Semikolons können auch innerhalb von Klammerungen genutzt werden. Ein gutes Beispiel ist die Definition einer Funktion: 
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
Es ist gut zu erkennen, wie das Semikolon jeweils ein `()`-Paar ersetzen kann. Der allerletze Ausdruck benötigt kein Semikolon, da keine Abtrennung zum nächsten Ausdruck nötig ist.

### Keywords

Folgende Keywords sind belegt, und können deshalb nicht als Feldnamen verwendet werden. Die hinter einem `/` stehenden Keywörter sind alternative Belegungen, die allerdings intern auf die gleiche Funktionalität verweisen:

1. function/func
2. print
3. println
4. string
5. number
6. trim
7. contains
8. startswith
9. var/let
10. yield
11. this
12. defined
13. if
14. else
15. while
16. loop
17. break
18. return
19. exit
20. get
21. put
22. select
23. null
24. assign/varset/mutate
25. native
26. factorial
27. sqrt
28. error
29. throw
30. catch
31. avm
32. amiant
33. assert
34. contract
35. exit
36. copy
37. const
38. struct
39. list
40. number
41. vnumber
42. sin
43. cos
44. tan
45. random
46. modulo
47. log
48. ln
49. exp
50. ipow
51. round
52. line
53. mount
54. unmount
55. call
56. level
57. memory
58. refs
59. async
60. await
70. true
71. false
72. weak
73. expect
74. when
75. new
76. signature
77. is
78. has

### Blöcke

Ein durch Klammern abgegrenzter Bereich wird _Block_ genannt. Ein Block hat folgende Eigenschaften:

1. Ein Block besitzt grundsätzlich __keinen__ eigenen Scope!
2. Steht nur ein einzelner Ausdruck in diesem Block, so ist der Rückgabewert dieses Blockes genau der Wert des enthaltenden Ausdrucks:

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

Wichtig: Auch wenn es so aussieht, als würde man sich bei der Wertzuweisung der Variable j in einem neuen Scope befinden, ist es nicht so. Die Klammerung legt nur einen Block fest, und Blöcke haben keinen eigenen Scope - __nur Funktionen, Schleifen, Verzweigungen, Async__ etc. haben einen. Noch wichtiger ist die Tatsache, dass Expressions, die nach der yield-Expression stehen, nicht erreicht werden können!

### Kommentare
Zeilenkommentare können mit dem Symbol `#` gestartet werden, und gehen bis ans Ende der Zeile. Mehrzeilige Kommentare gibt es nicht. Für diesen Fall muss jede Zeile des Kommentars mit dem Kommentarzeichen beginnen:
````
# Das ist ein Kommentar,
# der über mehrere Zeilen
# verteilt ist
````

## Datentypen - Überblick, Konstanten und Typkonstanten

### Überblick

| Datentyp | Bedeutung                   |
| :---:    |     :---:                   |
| Null     | Null (keine Daten)          |
| Number   | Zahlen jeden Typs           |
| VNumber  | Virtuelle Zahlen            |
| Boolean  | true oder false             |
| String   | Zeichenketten (ASCII!)      |
| Function | Funktion(-spointer)         |
| ByteSequence | Rohdaten (Bytes)        |
| Struct   | Container (Key-Value-Pairs) |
| List     | Liste                       |
| Signature| Signatur eines Structs      |

_Number_ ist der einzige Datentyp, der bei Übertragungen vollständig kopiert wird! Bei allen anderen Datentypen wird lediglich eine _Referenz_ kopiert. Diese Referenz sorgt dafür, dass Amiant weniger Speicher benötigt.

### Konstanten und Typkonstanten
Folgende Keywords sind als Konstanten reserviert, und können deshalb nicht außerhalb ihres Kontextes gebraucht werden:

| Keyword | Bedeutung     |  Bedeutung     |
| :---:   |     :---:     |    :---:       |
| PI      | 3.14159...    |    Kreiszahl   |
| EUL     | 2.71828...    | Euler'sche Zahl|
| PHI     | 1.61803...    |Goldener Schnitt|
| TAU     | 6.28318...    |Vollwinkel 2*Pi(rad)|
| LN2     | 0.69314...    |                |
| LN10    | 2.30258...    |                |
| SQRT2   | 1.41421...    |                |
| SQRT3   | 1.73205...    |                |
| SQRT2PI | 2.50662...    |                |

Typkonstanten enthalten den Datentypnamen als String. Sie werden immer groß geschrieben.

| Typkonstante | Bedeutung     |
| :---:        |     :---:     |
| Null         | "Null"        |
| Number       | "Number"      |
| VNumber      | "VNumber"     |
| Boolean      | "Boolean"     |
| String       | "String"      |
| List         | "List"        |
| Function     | "Function"    |
| Struct       | "Struct"      |
| ByteSequence | "ByteSequence"|
| Signature    | "Signature"   |

## Primitive Datentypen

### Zahlen
Number-Literals müssen im Code mit einem `$` vorgeschrieben werden. Ansonsten wird die Zahl als Feldname gewertet. Mehr zu den Feldnamen findet sich im Kapitel zu den Variablen. Folgende Ausdrücke resultieren nicht in Zahlen: `40`, `-5.2`. Korrekt müsste es heißen: `$40`, `$-5.2`. Sollte ein fehlerhafter Ausdruck hinter dem `$` erkannt werden, wird dieser Ausdruck als Zahl `0` gewertet. Es ist __ausschließlich__ die Notation in Dezimalzahlen möglich!

### Virtuelle Zahlen
Eine Besonderheit von Amiant ist der Datentyp `VNumber`, der virtuelle Zahlen definiert. Dieser Datentyp erlaubt eine beliebig hohe Präzision bei den Grundrechenarten zu erreichen. Während der Datentyp `Number` intern auf den Gleitkommadatentypen `float` und `double` basiert, ist dieser von Rundungsfehlern, Ungenauigkeiten bei gewöhnlichen Rechenoperationen (vor allem der Division), und der damit einhergehenden Uneignung für exakte und vergleichbare Rechnungen auf unterschiedlichen Plattformen betroffen. Dieses grundlegende Problem wird durch virtuelle Zahlen überwiegend gelöst. Sie erlauben das Arbeiten mit genauer Präzision, sowie die Handhabung von sehr großen und sehr kleinen exakten Zahlendarstellungen. Die gewonnene Genauigkeit wird allerdings durch eine etwas langsamere Verarbeitung und einen erhöhten Speicherbedarf bezahlt. Für Rechnungen, die keine hohe Genauigkeit bedürfen, wird der Umgang mit dem Datentyp `number` empfohlen.

### String
Strings werden im Code mit Anführungszeichen markiert. Zeichenketten müssen also folgendermaßen gekapselt werden: `"Hallo Welt!"`. Zeilenumbrüche innerhalb des Strings werden __auch__ gewertet. Somit sind mehrzeilige Strings problemlos möglich. Wenn das `"`-Symbol Teil des Strings werden soll, muss es escaped werden: `"\""`.
Ein einzeiliger String: `"Diese Zeichenkette liegt auf einer Zeile."`
Ein mehrzeiliger String:

```
"Diese Zeichenkette liegt
  auf
mehreren Zeilen."
```
Bei den mehrzeiligen Strings werden alle Zeichen zwischen den `"` beachtet! Somit werden die drei Leerzeichen vor dem Wort `auf` auch gewertet.

## Komplexe Datentypen

### Liste
Listen können mit dem `list`-Keyword erzeugt werden: `list $1 $2 $3` ergibt eine Liste, die die Zahlen `1`, `2` und `3` enthält. Eine Liste kann Objekte __unterschiedlicher__ Datentypen enthalten (eine Liste kann auch Typen mischen). Zugriff auf die in der Liste befindlichen Objekte bekommt man über den `get` Operator. Der Index einer Liste beginnt bei Null.
Der Ausdruck `get (list $2 $4 $6 $8) $2` gibt den Wert `6` zurück. Man kann nach der Erzeugung Daten an eine bestehende Liste anhängen, indem man das `put`-Keyword verwendet: `put (list $4 $5 $6) $7`

### Struct

#### Definition und Anwendung

Ein Struct ist eine Art Map, die unter ein Keyword zugehörige Daten speichert und über eben jenes Keyword wieder zurückgeben kann. Structs in Amiant unterscheiden sich deshalb erheblich von denen in C oder C++. In Amiant ist ein Struct schlicht ein Container, und __nicht__ ein Typ im gewohnten Sinne, der sich aus seinen Subtypen zusammensetzt. Ein Struct darf auch leer sein! Daten können aus einem Struct nicht entfernt werden, allerdings werden die alten Daten gelöscht, sollten sie unter einem bestehenden Keyword neu abgelegt werden. Das Keyword darf jeden Wert jeder __Primitiven__ annehmen. So sind `null`, `$4`, `"value1"` allesamt gültige Keywords. In einem Struct wird garantiert, dass unter einem Keyword immer nur einmal Daten liegen. Doppelt besetzte Keywords sind __nicht__ möglich. Ein Struct wird folgend erzeugt und verwendet:

```
var data struct; # erzeugt ein leeres Struct und speichert es in die Variable data

put data "age_joe" $4; # legt Daten in das Struct

println get data "age_joe"; # gibt die Daten aus dem Struct zurück
```

Es fällt auf, dass sowohl Listen als auch Structs die gleichen Keywörter `put` und `get` verwenden. Bei Structs erwartet der put-Operator logischerweise ein Argument mehr als bei einer Liste. Diese generischen Accessoren werden in einem späteren Kapitel noch einmal behandelt.


#### Typsignaturen

Trotzdessen Structs an sich keine Typen definieren, können sie dennoch eine Typsignatur aufweisen. Diese Signatur ist ein eigenes Objekt, das sich aus den im Struct hinterlegten String-Keys zusammensetzt, und diese repräsentiert. Dabei spielt die Reihenfolge der Hinterlegungen keine Rolle, nur ihr konkreter Inhalt. Amiant garantiert gleiche Typsignaturen für gleiche Keybelegungen. Da intern ein Hash-System verwendet wird, könnte es theoretisch zu Kollisionen kommen. Die Typsignatur eines Structs kann mithilfe des `signature`-Keywords gewonnen werden:


```
var person1 struct;

put person1 .age = $25;
put person1 .name = "Robert"; 
put person1 .heigth = $1.85;

var person2 struct;

put person2 .heigth = $1.62;
put person2 .name = "Willy"; 
put person2 .age = $31;
put person2 $4 = $5; # irgendeine zusätzliche Eigenschaft der person2, die allerdings keinen String-Key besitzt

println = person1 person2; # gibt false aus, da die Structs offenkundig nicht das gleiche Objekt repräsentieren
println = (signature person1) signature person2; # gibt true aus, da beide die gleichen definierten String-Keys haben
```
Mithilfe der Typsignaturen können innerhalb von Amiant schwach typisierte Anwendungen geschrieben werden, die auf das Duck-Typing-Prinzip beruhen. Das ist auch der Grund dafür, warum das Struct nicht als Map in die Sprache eingeführt wurde. Ein Struct, das keine String-Keys besitzt, besitzt dennoch eine Signatur - diese repräsentiert allerdings eine Leere.
Das `Signature`-Keyword gibt eine Referenz auf die Signatur eines Structs zurück, sodass diese Signatur immer an das Struct gekoppelt ist: verändert sich das Struct, wird sich auch die Signatur ändern. Das Keyword kann auch dazu genutzt werden, um neue (entkoppelte) Signaturen zu erstellen:

```
var treeSignature signature .type .height .location; # erzeugt eine neue Signatur, die die angegebenen Eigenschaften repräsentiert

var treeSignature2 signature "type "height" "location"; # zur Erinnerung: diese Syntax ist gleichwertig!
```
In diesem Falle erwartet der `signature`-Operator eine Reihe an String-Werten (oder andere Signaturen), die die zu repräsentierenden Eigenschaften angeben. Da diese Signatur nicht mehr an ein Struct gekoppelt ist, sondern nur für sich steht, wird sie *isolierte* Signatur genannt. Isolierte Signaturen können im Nachgang nicht verändert werden, aber sie können - genauso wie gekoppelte Signaturen - durch den `signature`-Operator miteinander zu neuen isolierten Signaturen verknüpft werden:

```
var treeSignature signature .type .height .location; # erzeugt eine neue Signatur, die die angegebenen Eigenschaften repräsentiert

var plantSignature signature .bioname;

var combinedSignature signature treeSignature plantSignature .age; # erzeugt eine kombinierte Signatur, die alle Eigenschaften der vorherigen beiden enthält, und noch eine zusätzliche hinzufügt
```

Bei der Kombination von Signaturen werden doppelt vorkommene String-Eigenschaften - wie in einer Menge - zu einer einzigen reduziert:

```
var doubleSignature .firstName .lastName .firstName; # diese Signatur ist trotzdem Repräsentant von zwei Eigenschaften - die dritte fällt mit der ersten zusammen
```


#### has-Operator und is-Operator

Die zuvor eingeführten Signaturen erlauben eine schwache Typisierung von Structs, die helfen sollen, bestimmte Eigenschaftskonstellationen als solche festzulegen. Um die Signatur eines Structs abzufragen, könnten mit den bisherigen Kenntnissen if-Operatoren verwendet werden:


```
var struct1 struct;
var struct2 struct;
...

if = (signature struct1) (signature struct2) { # prüft die Signaturgleichheit
   # do something
};
```

Diese Syntax hat mehrere Nachteile: zunächst ist viel Boilerplate-Code nötig, um an die Signaturen zu gelangen, und zweitens kann man keine Subtypen damit abprüfen. Um beide Probleme zu lösen, besitzt Amiant den `has`- und `is`-Operator, deren Anwendung nur im Kontext von Structs und ihren Signaturen gültig ist.
Der `is`-Operator hat zwei Verwendungsweisen: erstens kann er prüfen, ob zwei Structs die gleiche Signatur haben, und zweitens kann er prüfen ob die Signatur eines Structs einer beliebigen (möglicherweise sogar isolierten) Signatur entspricht:

```
var struct1 struct;
var struct2 struct;

var mySignature = signature .name .height .color; # erstellt eine neue isolierte (also an kein Struct gekoppelte) Signatur

println is struct1 mySignature; # gibt false aus
println is struct1 struct2; # gibt true aus
```
Damit ist das Problem der eleganten Überprüfung von Signaturen gelöst. Um Subtypen abzuprüfen wird der `has`-Operator verwendet. Dieser prüft, ob ein Struct alle nachfolgenden Eigenschaften besitzt - also ob die Signatur eines Structs eine Subsignatur ist:

```
var struct1 struct;
var struct2 struct;

...

println has struct1 struct2; # prüft, ob die Signatur des zweiten Structs in der Signatur des ersten Structs enthalten ist
println has struct1 .name .height. age; # prüft, ob das Struct die Signatur besitzt, die aus den drei Eigenschaften besteht
```
Der has-Operator ist noch flexibler, da er nicht nur ein Struct als erstes Argument zulässt, sondern auch explizit eine Signatur. Als nachfolgende Argumente können entweder String-Eigenschaften oder ganze Signaturen übergeben werden. Diese werden wieder kombiniert, und dann wird die Abfrage durchgeführt.

#### Select

Structs, die weak sind, enthalten bei Bedarf weitere Structs. Dadurch können struct-Hierarchien entstehen, die komplexe Eigenschaften  modellieren. Um nun durch so einen Baum zu navigieren, ist die get-Syntax eher unübersichtlich:

```
var person struct;
weak person;
var job struct;
weak job;
var company struct;

put person .job job;
put job .company company;
put company .name "Amiant Ltd.";

println get (get (get person .job) .company) .name; # gibt Amiant Ltd. auf der Konsole aus
```
Besser wäre die Verwendung der select-Syntax, die den hierarchischen Zugriff erleichtert, und potentielle Fehlerquellen durch falsche Klammerungen vorbeugt:

```
...

println select person .job .company .name; # gibt auch Amiant Ltd. auf der Konsole aus
```
Die get-Syntax stimmt also mit der select-Syntax, bei der nur ein Argument abgefragt wird, überein:

```
...

println = (get person .job) (select person .job); # gibt true aus
```


### Weak

Listen und Structs dürfen standardmäßig ausschließlich primitive Datentypen als Daten enthalten. Damit garantiert Amiant, dass nicht genutzter Speicher bereinigt werden kann, und es keine Zugriffe in bereits bereinigte Speicherbereiche gibt. Diese Regel ist für einige Anwendungsfälle jedoch zu stark. Manchmal ist es durchaus sinnvoll, komplexe Datentypen ineinander zu schachteln. Aus diesem Grunde gibt es das `weak` Keyword. Structs und Listen können als *weak* markiert werden, die Umkehrung ist nicht möglich. Als weak markierte Datentypen dürfen auch komplexe Datentypen enthalten. Die Speicherbereinigung kann in diesen Fällen allerdings nicht garantieren, dass der Speicher auch korrekt freigegeben wird (Retain Cycle). Amiant kann in diesem Falle nur garantieren, dass jeder Zugriff auf diese Objekte korrekt ist - Segmentation Faults sind immer ausgeschlossen, Speicherleaks allerdings nicht! Ein Retain Cycle muss manuell verhindert werden, in dem besonderes Augenmerk auf die Speicherstruktur gelegt werden muss.

### Generische Accessoren

Es gibt in Amiant einen generischen Accessor, der `get` Operator. Dieser hat die Eigenschaft, auf alle Objekte, die das ermöglichen, zuzugreifen. Somit ist er in der Lage, Daten aus Listen, Structs und sogar ByteSequences abzurufen. Die get-Syntax hat aber auch einen Nachteil: bei unachtsamer Anwendung kann versehentlich auf einen falschen Typ zugegriffen werden. Man sollte also nicht durcheinander kommen.

### Typumwandlungen
In Amiant können Zahlen in Strings und Strings in Zahlen problemlos umgewandelt werden. Um einen Datentyp in eine Zahl umzuwandeln, wird das `number`-Keyword genutzt. Um einen Datentyp in einen String umzuwandeln das Keyword `string`. Auch VNumbers können auf diese Art und Weise in einen String umgewandelt werden. Der durch `string` erhaltende Dezimaldarstellung der VNumber besitzt dadurch keine exakte Genauigkeit.

## Quick-String Syntax

Das oben eingeführte Struct wird in vielen Fällen als Container von Eigenschaften genutzt werden, wobei einzelne Eigenschaften als String-Key ansprechbar sind. Hieraus resultiert folgende Syntax, die einige Nachteile bietet: unter Anderem wird der Anschein erweckt, als sei der Key an sich auch ein relevanter Datenwert, dabei ist er nur Mittel zum Zweck.

```
var myWebSite struct;

put myWebSite "url" "www.myWebSite.com";

println get myWebSite "url";
```

Die Quick-String-Syntax vereinfacht den Code folgendermaßen:

```
var myWebSite struct;

put myWebSite .url "www.myWebSite.com";

println get myWebSite .url;
```

Jetzt ist deutlich und leserlich zwischen "wichtigen" String-Daten (in diesem Falle die Webadresse), und dem Key unterschieden. Die Quick-String-Syntax hat folgende Einschränkungen: sie gilt nur für einwortige-Strings (ohne Whitespaces)!

```
println "Hey"; # gleicher Output
println .Hey; # wie hier

println = "Test" .Test; # gibt true aus
```

Es ist unbedingt zu beachten, dass die Punkt-Syntax das darauffolgende Fieldname als String auswertet und nicht als Variable!

```
var color "red";

println .color; # gibt "color" auf der Konsole aus
println color; # gibt "red" auf der Konsole aus.
```

Die Quick-String-Syntax wird vom Lexer aufgelöst, sodass bei ihrer Nutzung keinerlei Einbußen in der Geschwindigkeit des Programmes in Kauf genommen werden muss. Der `.` ist dabei **kein** Operator, sondern ein lexikalischer Hinweisgeber. Aus diesem Grunde muss ein String, der durch die Quick-String-Syntax definiert wurde, nicht extra geklammert werden, wenn er als Parameter in einer Liste gilt!

Es ist unbedingt darauf zu achten, dass *nach dem Punkt kein Whitespace (Leerzeichen, Tab, etc.) stehen darf*! Die Syntax ist ein lexikalischer Zusammenhang, das garantiert ein schnelles Lexing, und erhöht ganz nebenbei die Lesbarkeit.

```
println . Hello; # würde NICHT als Quick-String-Syntax interpretiert werden!
println .Hello # das hier würde als Quick-String interpretiert werden
```
Ein Punkt leitet zwar den Quick-String ein, beendet ihn allerdings nicht. Nur Whitespaces, Klammern und Semikolons können einen Quick-String beenden! Damit darf auch der Punkt als Symbol verwendet werden.
```
println .a.b.c..; # gibt "a.b.c.." auf der Konsole aus 
```

## Variablen und Konstanten

### Deklaration und Scope
Die Benennung von Variablen ist beliebig, solange sie nicht mit belegten Keywords übereinstimmt. Der Name darf - anders als in vielen anderen C-ähnlichen Sprachen - auch mit einer Ziffer beginnen, und auch nur aus solchen bestehen. In jedem Scope kann eine Variable gleichen Namens immer __nur ein Mal__ definiert werden. Die Scopes sind hierarchisch organisiert. Eine Variable wird mit dem Keyword `var` erzeugt:
```
var i $4;
```
Hierbei wird eine Variable mit dem Namen `i` erzeugt, die den Wert `4` enthält und vom Typ `Number` ist.

Auf Variablen kann über den Namen zugegriffen werden.
```
println i; # gibt den Wert von i auf der Konsole aus
```

Variablen können mit dem `assign` Operator überschrieben werden:
`(assign i $5)` weist der Variable `i` den Wert `5` zu.
Wenn eine Variable einen Typ während der Initialisierung angenommen hat, so darf man diese Variable __nur mit einem Wert gleichen Typs__ überschreiben. Die einzige Ausnahme ist der Typ Null. Eine Variable, die den Wert `null` besitzt, gilt als _Typuninitialisiert_, und kann mithilfe eines Assigns auf einen festen Typ gebracht werden. Uninitialisierte Variablen werden auf zwei Arten erstellt:

```
var uninitialized; # Beide Variablen gelten als ..
var uninitialized2 null; # .. uninitialisiert!
```

Sobald eine Variable mit einem Typ initialisiert ist, kann man sie nicht mehr auf null setzen.
Amiant verwaltet den Speicher und löscht ungenutzte Variablen, sobald keine Referenzen mehr auf sie zeigen. Variablen müssen also __nicht von Hand freigegeben__ werden. Der `delete` Operator ist dennoch implementiert, um zur Laufzeit einige Optimierungen des Programmierers zuzulassen.

Eine Variable kann im Nachhinein als _konstant_ markiert werden, sodass ein nachträgliches Bearbeiten des Wertes nicht mehr möglich ist:

```
var x $4; # normale Variable
constant x; # jetzt kann x nicht mehr bearbeitet werden
```

### Expect

Um in Amiant einen Codeabschnitt nur dann auszuführen, wenn die darin verwendeten Variablen auch definiert sind, gibt es das `expect` Keyword. Hiermit lässt sich prüfen, ob Feldnamen im aktuellen Scope (lokal und hierarchisch darüber) definiert sind: sollte das der Fall sein, wird der entsprechende Ausdruck ausgeführt. Es ist wichtig anzumerken, dass das Expect-Keyword mindestens zwei Argumente benötigt, theoretisch aber beliebig viele haben darf. Das allerletzte Argument ist dabei immer der Code, der ausgeführt werden soll, falls alle angegebenen Feldnamen im Scope deklariert sind. Es wird __kein__ neuer Scope geöffnet!

Hier ist ein Beispielcode, der das Expect-Keyword in Verbindung mit Funktionen nutzt, um dafür zu sorgen, dass der Code von Funktionen nur dann ausgeführt wird, wenn alle nötigen Variablen existieren.

```
function printMessageFrom expect sender message {
     println sender;
     println message;
};
```

Im Beispiel ist zu sehen, wie eine Fehlerquelle vorzubeugen ist. Der entsprechende Code wird nur dann aufgerufen, wenn die Variablen (in diesem Falle *sender* und *message* auch beide existieren. Und das kann lokal sein (der Funktion werden die Variablen beim Aufruf übergeben), oder global bereits verfügbar. Genaueres zu den Funktionen findet sich im entsprechenden Kapitel.
Expect kann aber auch in jedem anderen Kontext verwendet werden, da es sich um ein eigenständiges Keyword handelt, das an keine Codestruktur gebunden ist (anders als das else-Keyword, das fest an if-Verzweigungen geknüpft ist). So lässt es sich mit Verzweigungen und Loops kombinieren. Aber auch im regulären Codefluss, zu Modulbeginn, oder anderen entscheidenen Punkten im Programm kann so eine Abfrage wichtig sein.

## Funktionen

### Allgemeines

Funktionen können in Amiant mit dem Funktionsoperator (`function`) erzeugt werden. Sie werden dabei in dem aktuellen Scope erstellt, und sind auch nur dort und hierarchisch tiefer aufrufbar. Wenn eine Funktion bereits in dem Scope definiert wurde, kann sie nicht erstellt werden, und es wird ein Fehler geworfen. Beim Funktionsaufruf wird zunächst im aktuellen Scope nach der aufzurufenden Funktion gesucht, ehe hieriarchisch nach oben gegangen wird. Funktionen gehören zu den wenigen Konstruktionen in Amiant, __die einen neuen Scope__ im letzten Argument erstellen!

Funktionsdefinition:
```
function foo {
   # Dieser Code wird in einem neuen Scope ausgeführt
};
```

Funktionsaufruf:
```
function add {
   return + n m;
};

~add n:$4 m:$5;
```
Funktionen sind mit dem Operator `~` und dem dazugehörigen Namen aufrufbar. Der Funktion können beim Aufruf Argumente übergeben werden. Diese müssen __immer__ mit einem Parameter-Label markiert sein! Die übergebenen Werte werden der Funktion als lokale Variablen während des Aufrufes zur Verfügung gestellt. Die obige add-Funktion benötigt beispielsweise `n` und `m` im Funktionskörper. Deshalb müssen diese explizit beim Aufruf übergeben werden. __Die Reihenfolge ist dabei beliebig__, da die Parameter-Label die Eindeutigkeit der Variablennamen festsetzen. Wenn sie nicht übergeben werden, wird die Funktion _trotzdem_ ausgeführt, und an der entsprechenden Stelle wird es zu einem Fehler kommen.

Mit dem Keyword `return` kann die Funktionsausführung an dieser Stelle beendet werden. Man kann mit oder ohne Wert eine Funktion abbrechen. Eine Funktion, die keinen expliziten Wert zurückgibt, gibt implizit den Wert `null` zurück.

Sollte es innerhalb einer Funktion zu einem Fehler kommen, wird mit Standardwerten weiter gearbeitet. Die Funktion bricht ihre Ausführung also __nicht__ ab.

### Contracts und Debugging

Da Amiant eine Sprache ist, die LowLevel-Scripting bereitstellt, ist das Thema Sicherheit von großer Bedeutung. Fehler können während der Laufzeit auftauchen, und sie werden intern so gehandhabt, dass ein Absturz der AVM in jedem Falle auszuschließen ist. _Aus diesem Grunde wird ein Fehler nicht zum Abbruch des Programmes führen, sondern zum Weiterarbeiten mit Standardwerten_. In manchen Situationen ist das Arbeiten mit solchen Standardwerten kritisch oder gar gefährlich. Dazu gibt Amiant zwei Möglichkeiten der Überprüfung und Handhabung:

1) Assert:

Das Keyword `assert` prüft, ob ein bestimmter Ausdruck wahr ist. Sollte er wahr sein, läuft das Programm einfach weiter. Sollte der Ausdruck falsch sein, bricht das gesamte Programm an dieser Stelle mit einer Fehlermeldung ab. Ein Assert kann also das Weiterlaufen eines fehlerhaften Programmes verhindern. Dabei wird die gesamte VM (und MetaVM) unterbrochen!

2) Contract:

Ein weiterer Schlüssel in der Sicherheitsarchitektur Amiants ist das Nutzen von Contracts. Contracts verweisen auf eine Eigenschaft von Funktionen innerhalb der AVM: eine Funktion kann frei sein, oder unter "Vertrag" stehen. Eine freie Funktion ist eine gewöhnliche Funktion, die bei Aufruf ihren Code ausführt. Sollte es innerhalb der Funktion zu Fehlern kommen, so wird die Funktion mit Standardwerten weiterarbeiten, ganz gleich was das Ergebnis davon ist. Sollte eine Funktion jedoch unter Vertrag stehen, ist sie verpflichtet, für die Richtigkeit der in ihr generierten Daten die Verantwortung zu übernehmen. Sollten die Daten allesamt korrekt sein, so wird diese Funktion schlicht ihren Code ausführen. Sollten die Daten inkorrekt sein, so liegt eine "Vertragsverletzung" in Bezug zur AVM vor, sodass die AVM diese Funktion dann __sperrt__. Die Funktion bricht an dem Keyword sofort ab, und kann danach __während der aktuellen Laufzeit des Programmes nicht mehr aufgerufen werden__. Sollte sie dennoch irgendwo aufgerufen werden, so wird mit null an dieser Stelle abgebrochen, und ein Fehler geworfen. Ein Ausführen der Funktion ist somit _unmöglich_. Diese Architektur erlaubt es, fehlerhafte Codeabschnitte zur Laufzeit zu entfernen, sodass zu jedem Zeitpunkt ein fehlerfreies Programm garantiert werden kann. Voraussetzung dafür ist das konsequente Nutzen von Contracts.

```
contract > i $0; # garantiert der AVM, dass der Wert der Variablen i ab diesem Zeitpunkt immer größer 0 ist
```

Die Sicherheit des Codes kann damit zur Laufzeit erhöht werden, in dem fehlerbehaftete Äste innerhalb des Programms nach und nach deaktiviert werden. Eine gesperrte Funktion kann durch einen mount über den gleichen Namen jedoch wieder nutzbar gemacht werden (siehe Reflection). Eine Verletzung des Contracts in der _globalen Funktion_ (globaler Scope) führt allerdings _nicht_ zu einer Sperrung. Die obersten Funktionen (async-Blöcke, MetaVM globaler Scope) sind somit immun gegen Contracts.

### Funktionale Programmierung

In Amiant sind Funktionen first-class-objects. Funktionen können selbst als Wert übergeben werden, sodass man sie durch das Programm bewegen kann. Um eine Variable zu erstellen, die einen Pointer auf eine Funktion enthält, muss man den Function-Pointer-Operator `\` nutzen. Dieser sucht im aktuellen Scope nach der Funktion, und gibt einen Zeiger darauf zurück. Sollte die Funktion nicht gefunden werden, wird `null` zurückgegeben. Der `~` Operator erwartet ein Fieldname und keine Expression, sodass man die Funktion, die man als Pointer besitzt, nur dann ausführen kann, wenn man den Pointer vorher in eine Variable legt, und diese dann hinter dem `~` Operator schreibt. Wichtig ist die Reihenfolge der Definition! Der `~` Operator sucht erst nach einer Funktion im Scope und hierarchisch darüber, ehe nach Variablen mit gleichem Namen vom Typ eines Funktionspointers gesucht wird! Deshalb sollte die Variable nicht den gleichen Namen besitzen, wie die Funktion - sollte sie im aktuellen Scope überhaupt erreichbar sein.

```
function add {
    return + n n;
};

var addFunc \add;

println ~addFunc n:$4; # gibt 8 auf der Konsole aus
```

## Operatorsymbole

### Übersicht
Amiant besitzt eine Vielzahl von Operatoren, die unterschiedliche Aufgaben erfüllen, und verschiedenste Voraussetzungen haben. Ein Operator unterscheidet sich von anderen Fieldnames nur durch das Aussehen - es gibt also auch ausschließlich Präfixoperatoren! Einige Operatoren wurden bereits vorgestellt - hier sei dennoch eine vollständige Auflistung:

| Operator | Bedeutung                                 |
| :---:    |     :---:                                 |
| +        | Addition (number, vnumber)                |
| -        | Subtraktion (number, vnumber)             |
| *        | Multiplikation (number, vnumber)          |
| /        | Division (number, vnumber)                |
| >        | Größer als                                |
| <        | Kleiner als                               |
| &        | Logisches Und                             |
| \|       | Logisches Oder                            |
| !        | Logisches Nicht                           |
| =        | Vergleich (für alle Datentypen) / Identität        |
| @        | Datenpointer (ByteSequence) an eine Speicherstelle |
| ?        | Typname als String                        |
| ^        | Bitbearbeitung (bei ByteSequence)         |
| \        | Funktionspointer                          |
| %        | Gibt die Speicheradresse der Daten zurück |

## ByteSequence

### Bitbearbeitung

Manchmal ist es notwendig, bestimmte Bytes im Speicher direkt zu bearbeiten. Das kann beispielsweise bei der Verwendung vom Videospeicher der Fall sein, oder beim Setzen bestimmter Einstellungen. Amiant kann mithilfe vom `@` Operator beliebige Speicherstellen anvisieren und als ByteSequence nutzen. Es gibt dabei __keinerlei__ Überprüfung vonseiten der AVM. Es ist problemlos möglich, auf verbotene Adressen außerhalb der AVM zuzugreifen (beispielsweise oftmals die Null), und damit einen Segmentation-Fault oder andere unvorhersehbare Probleme zu provozieren! Der Speicherbereich, den Amiant über den `@` Operator ergreift, wird nicht vom Speichermanager bereinigt. Nur der Container selbst wird bei fehlender Notwendigkeit gelöscht. Um bestimmte Bytes zu ändern, wird folgende Syntax genutzt:

```
var unsafeVar @$0 $10; # legt eine 10 Byte lange Sequence ab der Adresse 0 an (Adresse 0-9)

^ unsafeVar $8 $1; # schreibt an die Adresse 0+8 das Byte 0b00000001

var unsafeVar2 @$12 $20;

^ unsafeVar2 $10 $0; # schreibt an die Adresse 12+10 das Byte 0b00000000

var byteAt8 get unsafeVar $8; # gibt das Byte an Adresse 0+8 zurück (als Dezimalzahl!)
```

Hierbei sollte immer beachtet werden, dass Amiant ausschließlich Dezimaldarstellungen von Zahlen erlaubt. Wenn man das Byte 0b00000010 schreiben möchte, dann muss man die entsprechende Dezimalzahl $2 nutzen! Es ist nicht möglich außerhalb des in der Deklaration einer ByteSequence festgelegten Adressbereichs zu schreiben. Amiant wirft in diesem Moment einen OutOfIndex-Fehler. Damit wird - vorausgesetzt der anvisierte Adressbereich ist erreichbar - eine minimale Sicherheit gewährleistet. Einen Schutz vor Segmentation-Faults gibt es dabei allerdings nie.

### New-Operator

Es ist problemlos möglich, eigene ByteSequence-Objekte benutzerdefinierter Länge zu erzeugen. Dafür ist der `new` Operator vprgesehen, der einen Speicherbereich bestimmter Länge reserviert. Dieser gehört dann allerdings zur AVM und wird freigegeben, sobald keine Referenz mehr auf sie zeigt - anders als bei der ByteSequence, die durch den `@` Operator erzeugt wurde!

```
var myByteSequence new $64; # erzeugt eine 64-Byte lange ByteSequence
```

### Copy-Operator

Der Operator `copy` veranlasst die AVM dazu bestimmte Bytes einer ByteSequence in eine zweite zu kopieren. Dabei müssen _immer_ der Offset des Anfangs und des Ziels angegeben werden. Der erste Offset gibt an, wie viele Bytes man von der Quelle überspringen möchte, und der zweite gibt an, an welche Stelle der zweiten ByteSequence die Daten eingefügt werden sollen. 

```
var bsq1 new $20; # 20 Byte lange ByteSequence
var bsq2 new $40; # 40 Byte lange ByteSequence

copy bsq1 bsq2 $0 $10 $20; # kopiert von Byte 0 an der ersten ByteSequence 20 Bytes in die zweite ByteSequence - dort ab dem Index 10 eingefügt
```

Der `copy` Operator gibt true oder false zurück - je nach dem ob der Kopiervorgang erfolgreich war, oder nicht. Fehlerhafte Kopiervorgänge sind zu große Offsets, eine Size von kleiner 1, oder ein interner Kopierfehler. Falsche Typen im Operator werfen allerdings einen Error und geben Null zurück!

### Identitätsoperator

Um die Übersichtlichkeit zu erhöhen, und den Umstieg von C-ähnlichen Programmiersprachen nach Amiant zu erleichtern, gibt es den Identitätsoperator. Dieser ist auf das Symbol `=` gelegt, was mit dem Vergleichsoperator zusammenfällt. Wenn der Vergleichsoperator genutzt werden soll, werden mindestens zwei Argumente benötigt. Wird dem `=`-Operator allerdings nur ein Argument übergeben, so verhält er sich wie die Identität. Der Operator gibt also genau das zurück, was ihm übergeben wurde, ohne daran Änderungen durchzuführen. Die Identität reicht das Objekt weiter nach oben, und hat somit keinerlei Einfluss auf die Programmlogik. Allerdings sind mit ihm bestimmte Syntaxen übersichtlicher, beispielsweise die Deklaration einer Variable:

```
var weather "rain"; # erzeugt eine Variable ohne Identitätsoperator

var weather2 = "snowy"; # erzeugt eine Variable mit Identitätsoperator
```

Die Verwendung des Identitätsoperators ergibt eine Syntax die der von vielen C-ähnlichen Programmiersprachen (Java, JavaScript, C#) völlig gleicht. Zusätzlich ist zwischen Name und Argument optisch besser unterschieden.

### When-Operator & Expect-When-Syntax

Das Keyword `when` hat in Amiant eine ähnliche Funktionsweise wie `expect`. Während letzteres prüft, ob eine Variable im Scope (und hierarchisch darüber) definiert wurde, agiert `when` wie eine Bedingungsüberprüfung. Falls alle Bedingungen, die vom Typ Boolean sein müssen, wahr sind, wird das letzte Argument ausgeführt. Dabei wird **kein neuer Scope erstellt**. Sollte eine Bedingung in der Kette false sein, wird die Ausführung abgebrochen, der Code im letzten Argument wird nicht ausgewertet, und *es werden alle übrigen Bedingungen ebenso nicht mehr ausgewertet*. In diesem Falle gibt der Operator schlicht null zurück. Sollten alle Bedingungen wahr sein, gibt der Operator das Ergebnis des Codes im letzten Argument zurück. Sollte ein Ausdruck in der Bedingungsliste dabei sein, der versehentlich keinen Boolean zurück gibt, so wird ein Typ-Fehler geworfen, und der Operator bricht mit null ab. Eine Typverletzung wird also nicht ignoriert und übergangen!

```
var isRaining = true;
var isMonday = true;
var isYear2024 = false;

when isRaining isMonday println "It's a rainy monday!";

var mood = when isRaining isMonday {
   # kein neuer Scope!
   yield "sad";
};

println mood; # gibt sad aus

var result = when isMonday isYear2024 yield $4;
println result; # es wird null ausgegeben, da when eine false-Bedingung gefunden hat
```

Neben den obigen Beispielanwendungen, die wie eine verkürzte If-Schreibweise agieren, gibt es noch eine weitere Situation, in der diese Syntax vorteilhaft sein kann - bei Funktionsdefinitionen:

```
function safeLn2 expect a when (> a $0) {
    return ln a;
}

println ~safeLn2 a:$1; # gibt 0 aus
println ~safeLn2 b:$4; # gibt null aus
println ~safeLn2 a:$-4; # gibt null aus
```

Diese expect-when-Syntax erlaubt die sichere Ausführung von Funktionen. Beispielsweise wird der Aufruf des natürlichen Logarithmus' durch diese Funktion geschützt, in dem das Argument begrenzt wird.

## Amiant-MetaVM

Die AVM ist in der Lage, dynamisch neuen Code auszuführen. Dieser Code läuft dann in einem gesonderten Bereich, sodass Zugriffe in andere Bereiche nicht möglich sind (Sandboxing). Um innerhalb eines laufenden Programms weiteren Amiant-Code auszuführen, muss dieser Code lediglich als String vorliegen, und mithilfe des Keywords `amiant` ausgeführt werden.

```
amiant "println $4"; # startet ein weiteres Programm, das die Zahl 4 auf der Konsole ausgibt
```

Es ist wichtig zu beachten, dass es sich hierbei nicht um eine Multithreading-Lösung handelt! Der Programmfluss __stoppt__ an dieser Stelle, bis das Unterprogramm seine Ausführung beendet hat. Aber mit einer geeigneten Implementierung einer Threading-Bibliothek über die Native-Schnittstelle ist die Auslagerung in einen separaten Thread ohne Probleme möglich. Da das innere Programm keinen Zugriff auf das äußere hat, kann es dort nicht zu problematischen Interaktionen kommen. Umgekehrt besteht zwar kein direkter Zugriff, wohl aber ein indirekter: Daten können in das Programm gebracht werden, in dem sie in den String des Programmcodes beispielsweise als Variablen geschrieben werden. Da das äußere Programm den Programmcode des inneren Programmes besitzt, können bestimmte Informationen vor dem Start des inneren Programms direkt einprogrammiert werden.
Es ist darauf hinzuweisen, dass trotz dieser Abkapselung es _nicht_ zu einem Starten einer neuen und eigenständigen AVM kommt! Intern läuft weiterhin nur eine einzige AVM, die sich jedoch rekursiv verwenden kann. Das schlägt sich beispielsweise in den internen Speicherstatistiken wieder. Ein äußeres und ein inneres Programm tragen gleichermaßen zu den selben Statistiken bei. Auch das Akquirieren neuen Speichers erfolgt über die exakt gleichen Funktionen. Diese Fähigkeit von Amiant erleichtert es, eine AVM in einem Kernel (oder als Proto-Kernel) laufen zu lassen, die dann wiederum Unterprogramme starten kann - jedoch nur eine Stelle besitzt, an der der gesamte Speicher verwaltet werden muss.

Jedes Amiant-Programm kann sofort beendet werden, in dem das `exit`-Keyword genutzt wird. Dabei wird nur das _lokale Amiant-Programm_ beendet. Wenn ein Unterprogramm `exit` aufruft, so wird nur das Unterprogramm beendet, und das Oberprogramm setzt die Ausführung fort. Sollte das oberste Programm ein `exit` ausführen, so beendet sich die komplette AVM mit allen Unterprogrammen.

Es ist möglich zur Laufzeit herauszufinden, in welchem Level der aktuelle Code ausgeführt wird. Als _Level_ wird die Stufe in der Hierarchie der Programme und Unterprogramme bezeichnet. Das oberste Programm läuft auf Level 0. Wenn dieses Programm mithilfe des `amiant`-Keywords ein Unterprogramm startet, so liefe dieses Unterprogramm auf Level 1, usw. Den numerischen Wert des aktuellen Programmausführungslevels kann mit `level` zurückgegeben werden.

Hinweis: während es mehrere Programme auf Level 1, 2, ... geben kann, gibt es immer nur ein einziges Programm auf Level 0!

## If-Verzweigungen

Bedingte Verzweigungen werden durch den `if` Operator realisiert. Verzweigungen öffnen Scopes! Die allgemeinen Formen einer if-Abzweigung und if-Else-Abzweigung sehen folgendermaßen aus:

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

## Schleifen
In Amiant gibt es einen Schleifentyp, der in C-ähnlichen Sprachen auch while-Schleife gennant wird:

```
while condition {
    # Code here
};
```
Anstelle des Keywords `while` ist auch das Keyword `loop` für diese Operation belegt.

Jeder Schleifenkörper stellt einen eigenen Scope für die Variablen bereit.

## Konsole I

Ausgaben auf dem Standardoutput können mit dem `println` Operator durchgeführt werden. Hierbei werden Strings und Zahlen wie sie sind ausgegeben. Listen werden mit dem Typ und ihrer Größe ausgegeben, z.B. `[List size=1]`. Für Maps gilt das gleiche wie bei Listen. Null wird als `[Null]` und ByteSequence als `[ByteSequence@addressNumber size=size]` ausgegeben. Booleans werden mit ihrem Stringäquivalent ausgegeben: `true` bzw. `false`.


## Mathematische Operationen

Die AVM stellt Grundrechenarten zur Verfügung, die intern und nicht in der Sprache selbst implementiert wurden, um die Geschwindigkeit der Operationen zu erhöhen, sowie eine Plattformunabhängigkeit zu garantieren.

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
|   round  | rundet (Präz.) |

Amiant stellt eigene Implementierungen der mathematischen Operationen bereit (außer für +,-,*,/,modulo), die teilweise gut funktionieren, allerdings können die Berechnungen - gerade bei Logarithmen - mit großen Zahlen langsam und sehr ungenau werden. Alternativ kann über die Native Bridge eine systemeigene Implementierung dieser mathematischen Operationen bereitgestellt werden.

## Error Handling und Fehlercodes

Der letzte geworfene Fehler kann mit `(error)` geholt werden. Das gibt den Typ des Fehlers als Number zurück.

| Operation             | Bedeutung                                                              |
|   :---:               |     :---:                                                              |
|     `(error line)`    | gibt die Zeilennummer als Zahl zurück, an der der letzte Fehler auftrat|
|     `(error)`         | gibt den letzten Fehlercode in der aktuellen Funktion zurück           |
|`(throw errorCodeNumber)`| wirft einen eigenen Fehlercode (Zahl ist ganzrational)               |
|`(catch errorCodeNumber catchBlockExpr)` | Fängt einen geworfenen Fehlercode ab, und führt den Ausdruck dann aus (wichtig: dieser Ausdruck steht __nicht in einem neuen Scope!__)               |

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

In Amiant ist es möglich, eigene Fehlercodes zu setzen. Das geschieht mit dem `throw`-Keyword, das schlicht einen Ausdruck erwartet, der eine Zahl enthält - eben den Fehlercode. Throw unterbricht allerdings nicht den Codeflow! Die Ausführung wird fortgesetzt. Dennoch kann man damit eigene Fehlerroutinen implementieren, und auf eventuelle Fehler reagieren. Fehlercodes sind ganze Zahlen - sie können auch negativ sein, und auch die obigen Fehlercodezahlen mitnutzen. Davon wird der Übersichtlichkeit wegen und wegen der großen Zahl an freien Fehlercodes abgeraten.

```
if (= (~fetchData) "404") {
    throw $404; # Fehler werfen
};

...

if(= (error) $404) {
    # Fehlerbehandlung (im neuen Scope der Verzweigung)
};

# oder eleganter

catch $404 {
    # Fehlerbehandlung (aber kein separater Scope!)
};
```

## Reflection

Amiant erhält während der Ausführung die vollständige Programmstruktur aufrecht. Somit ist es möglich, Informationen dieser Struktur zur Laufzeit zu bekommen, aber auch dynamische Zugriffe in ihr zu erlauben, und sie zu verändern.

`this` gibt den Namen der Funktion als String zurück, in der dieses Keyword steht.

`call <str>` ruft eine Funktion in diesem Scope über den Namen auf, und gibt das Ergebnis der Funktion zurück. Wichtig: Dieses Keyword kann keine Argumente an die Funktion übergeben!

`line` gibt die aktuelle Zeilennummer, in der dieses Keyword steht, als Zahl zurück. Die Zählung beginnt bei 1!

`mount <str> <str>` hängt eine neue Funktion unter dem Namen und unter dem übergebenen Code in die AVM an dieser Stelle im Scope ein. Somit können partielle Codeerweiterungen problemlos erfolgen, ohne direkt eine Meta-VM starten zu müssen.

`unmount <str>` löscht eine Funktion aus dem aktuellen Scope. Ihr Aufruf ist danach nicht mehr möglich.

`defined <fieldName>` prüft, ob eine Variable in diesem Scope definiert wurde (gibt einen Boolean zurück)

`refs <expr>` gibt die Anzahl an Referenzen auf ein Objekt zurück (die aktuelle Referenz für diesen Befehl ist da mit dabei!)

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

var myVar $4;

println defined myVar; # gibt true auf der Konsole aus
```

Hinweise:

1) Reflection ist ein mächtiges Werkzeug. Es werden generell __keine Überprüfungen__ im Bezug auf doppelte oder falsche Benennungen durchgeführt! Das resultiert jedoch in keinerlei Speicherprobleme innerhalb der AVM. Allerdings ist dann nicht eindeutig definiert, welche Funktion aufgerufen wird, wenn es Mehrfachbenennungen durch Reflection gibt! Die Überprüfung der Namensdopplung gibt es nur in der normalen Funktionsdefinition über das `function`-Keyword, aber nicht bei Refelction.

2) Das Hinzufügen von Funktionen ist eine ressourcenintensive Operation, da der übergebene String völlig neu geparst und verarbeitet werden muss. Es wird also von einem übermäßigen Gebrauch abgeraten.


## Async Await

### Konzept und Anwendung

Die AVM unterstützt übersichtliches paralleles Ausführen von Code mithilfe einer Async-Await-Syntax.

`async asyncVarName arg1:capture1 arg2:capture2 ... argN:captureN asyncExpression;`

Die Variable, auf die mit asyncVarName verwiesen wird, __muss vom Typ Null__ sein (also uninitialisiert), damit der async-Block in der Lage ist, ihren Wert mit beliebigen Typen zu überschreiben!

Hier ist ein kleines Beispiel, wie man Async-Await in Amiant nutzen könnte:

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

Async erwartet als erstes Argument ein fieldName zu einer Variable, die am Ende der Ausführung des Async-Codes das Ergebnis eben dieses Codes enthalten soll. Die Variable wird als async markiert, und kann dann __nicht zeitgleich von einem anderen async-Keywort genutzt werden__. Das geht nur wieder, sobald der async-Block fertig ist, und die Variable wieder freigibt. Auf eine asynchrone Variable kann immer zugegriffen werden. Greift man ohne `await` auf eine asynchrone Variable zu, so wird ihr _aktueller_ Wert zurückgegeben, der nicht unbedingt der Rückgabewert des asynchronen Codes sein muss (dieser kann schließlich noch arbeiten). Um nun den echten Rückgabewert des asynchronen Blocks zu bekommen, muss man bei der Verwendung der Variable schlicht das Keyword `await` davor setzen. Der Codefluss __wartet__ an der Stelle, bis die Variable vom async-Code freigegeben wurde. Danach wird ihr Wert erst aufgelöst.

Innerhalb der Async-Blöcke hat man keinen Zugriff auf außenstehende Funktionen oder Variablen! Allerdings bleibt der Zugriff auf alle Funktionen, die mit der Native-Bridge eingebunden sind, bestehen! Um dennoch Funktionen von außerhalb zu nutzen, kann man diese via Funktionspointer in die Captureliste vom async-Block setzen.
Jeder asynchrone Codeblock läuft auf dem AVM-Level 2, sodass Zugriffe auf _restart_ von dort aus __nicht__ möglich sind. Sie sind untergeordnet zum Hauptprogramm auf Level 1!

Die asyncVar enthält dann das Ergebnis des asynchronen Abarbeitens. Der Wert der Variable wird vom async-Block verändert. Wichtig: __auch Konstanten können als async-Variable verwendet werden!__ Wenn man auf eine async-Var zugreift, während sie vom async verwendet wird, wird sie immer `null` zurückgeben! Nur das await-Keyword kann auf den richtigen und fertigen Wert warten. Sobald die Variable wieder freigegeben ist, kann man sie wieder normal nutzen.

__Async-Await funktioniert ausschließlich auf Level 0__ und kann damit nicht in einer Meta-VM genutzt werden! Sollte man dennoch Async-Await nutzen, so wird dieses lediglich linear ausgeführt. Der Code funktioniert also trotzdem - nur die echte Parallelisierung per Threads fällt weg. Nur Hauptprogramme können sich also aufspalten.

Sollte kein Threading unterstützt sein, so gibt `await` schlicht den aktuellen Wert der Variable zurück, und `async` wird synchron ausgeführt, sodass das Programm an dieser Stelle wartet, bis die Ausführung des async-Blockes vollendet wurde. Das Programm __verhält sich also exakt gleich__ - es dauert nur etwas länger, da nicht parallel ausgeführt wird.

### Implementierung

Da reines C kein Multithreading unterstützt, muss eine eigene Implementierung erfolgen. Sollte die verwendete Plattform die _pthread.h_ unterstützen, so kann in die parallel.h gegangen werden, und die entsprechen Zeilen in der `avmThreadStart(...)` auskommentiert werden, sowie das include-Statement über der Funktion. Zusätzlich muss in der `avmThreadWorking(void)` ein paar Zeilen weiter oben der Rückgabeboolean von `false` auf `true` gesetzt werden.
Wenn keine pthread.h zur Verfügung steht (beispielsweise unter Windows) muss in der `avmThreadStart(...)` an der entsprechenden Stelle eine andere Implementierung genutzt werden. Der Boolean in der `avmThreadWorking(void)` muss dennoch auf `true` gesetzt werden.

## Kompilierung der AVM

Der gesamte C-Code soll in einer einzigen Compilation-Unit verarbeitet werden, weshalb sämtliche Funktionalitäten in Header-Dateien abgelegt sind. Dies ist unüblich, gibt allerdings den Vorteil einer einfacheren Kompilierung. Die `amiant.h` enthält alle Includes in der richtigen Reihenfolge. Es ist also nur notwendig, die `amiant.h` erfolgreich einzubinden.
Es wird empfohlen, die AVM unter C11 oder höher zu kompilieren. In Versionen niedriger als C11 funktioniert die gesamte AVM wie gehabt, nur das Statistik-Modul (das außschließlich zu Debugzwecken verwendet werden sollte), kann dort aufgrund fehlender Atomics unter Race-Conditions leiden. Das hat allerdings __keinen__ Einfluss auf die Speichersicherheit der AVM! Diese bleibt unverändert funktionstüchtig.

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
2. Die Funktion muss in einer .h definiert sein.
3. Diese Header-Datei wird in der native_bridge.h von Amiant in der "INCLUDE-SEKTION FÜR DIE STANDARDBIBLIOTHEK HEADER FILES" includiert.
4. In der darunter liegenden Funktion `standardLibraryGetFunctions()` müssen die C-Funktionen für Amiant registriert werden. Das geschieht mithilfe folgender Zeile Code: `standardLibraryAddStandardFunction(libraryFunctionsList, "funcName", 8, false, &functionName);` Dabei gibt es folgendes zu beachten: Der String "functionName" gibt an, unter welchem Namen die C-Funktion _innerhalb von Amiant_ aus aufrufbar ist. Dieser Name muss nicht deckungsgleich mit dem Namen der C-Funktion sein. Die Zahl hinter dem String (in diesem Fall die 8) gibt an, wie viele Zeichen der Name lang ist. "funcName" besitzt 8 Buchstaben, also 8 Zeichen. Der Funktionsname für Amiant darf nur Zahlen und Buchstaben enthalten, ansonsten ist die Funktion nicht aufrufbar! Als letztes Argument muss der Funktionspointer zu der zugehörigen C-Funktion übergeben werden.

Es ist auch möglich C-Funktionen aufzurufen, die nicht mit in die AVM kompiliert wurden. Diese Funktionen müssen dann anderweitig in den Speicher geladen werden, und können dann mit `native fieldName functionPointerExpr` eingehangen werden. Dabei muss der fieldName der Name der Funktion sein, wie sie in Amiant benutzt werden kann, und die functionPointerExpr muss ein Ausdruck sein, der eine Zahl zurück gibt: die Adresse der C-Funktion. Die C-Funktion muss dabei wieder die obige Signatur `void LIST* functionName(struct LIST* data)` besitzen!

Beim Aufrufen von nativen C-Funktionen kann Amiant dieser auch Argumente übergeben. Solche Argumente verlieren bei der Übertragung allerdings ihren Namen, sodass nur noch die Daten an sich und ihre Reihenfolge garantiert werden kann, da in C die Funktionsvariablen eine feste Reihenfolge benötigen. Die Datenliste `data` besitzt alle Amiant-Pointer zu den übergebenen Daten. __Weder die Liste noch die Pointer dürfen von der C-Funktion gelöscht oder Verändert werden!__ Die Datenhoheit besitzt weiterhin die AVM. Als Rückgabewert kann NULL zurückgegeben werden, oder allerdings eine eigene Datenliste. Es ist darauf zu achten, dass Pointer, die man in diese Rückgabeliste legt nach dem Hinzufügen auch wieder freigibt, damit die Liste die einzige Datenhoheit über die Rückgabedaten besitzt. Es sollte darauf verzichtet werden, Pointer der data-Liste in die return-Liste zu legen, da es sonst zu zyklischen Referenzen kommt, und Amiant die Listen nicht mehr freigeben kann.

## Einstellungen und Informationen der AVM

Das Keyword `avm` gibt einen internen Zugriff auf die Amiant Virtual Machine (AVM).

`avm version` gibt die aktuelle Version der AVM als Zahl zurück.
`avm restart` beendet alle Meta-AVMs und startet das gesamte Programm neu. Dabei wird allerdings kein neuer Code eingelesen, sondern der vorhandene schlicht neu gestartet! Alle mit Reflections vorgenommene Änderungen am Code bleiben dabei erhalten! Diese Funktion steht nur der obersten AVM zur Verfügung (Level 0), und kann von Meta-AVMs nicht genutzt werden.
`avm compver` gibt die Version des verwendeten C-Standards bei der Kompilierung zurück. Hierbei werden die offiziellen Versionsnamen verwendet. Bei C89/90 und tiefer wird standardmäßig `198901L` zurückgegeben, auch wenn es diese Versionsnummer offiziell nie gegeben hat.
`memory` gibt den gesamten aktuell genutzten Arbeitsspeicher aller (Unter-)AVMs zurück. Wichtig: __bei C99 und tiefer kann es durch die Verwendung von async-await zu falschen Werten kommen.__ Das Statistikmodul benötigt Atomics, die bei C99 und tiefer allerdings nicht vorhanden waren. 

## Hinweise an LISP-Nutzer

Amiant ist kein LISP, sondern nutzt nur die Idee der Klammerung, erweitert sie, und fügt modernere Keywords ein. Zudem ist es nicht möglich, neue Sprachkonstruktionen in Amiant mithilfe von Makro-Strukturen einzuführen, wie es bei LISP der übliche Fall ist.
