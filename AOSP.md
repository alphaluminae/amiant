# Amiant Operator Specification (AOSP)

| Operator				|  Rückgabetyp  		|  Erklärung |
| ---             |  ---              | ---        |
| `+ <?:Number> <?:Number> ...`		|  `<Number>`			|  Addiert mehrere Zahlen miteinander |
| `- <?:Number>`		|  `<Number>`			|  Dreht das Vorzeichen der Zahl um |
| `- <?:Number> <?:Number> ...`		|  `<Number>`			|  Subtrahiert mehrere Zahlen voneinander. Die hinteren Zahlen werden von der ersten Zahl abgezogen |
| `* <?:Number> <?:Number> ...`		|  `<Number>`			|  Multipliziert mehrere Zahlen miteinander |
| `/ <?:Number> <?:Number> ...`		|  `<Number>`			|  Dividiert mehrere Zahlen miteinander |
| `> <?:Number> <?:Number>`			|  `<Boolean>`			|  Größer als |
| `< <?:Number> <?:Number>`			|  `<Boolean>`			|  Kleiner als |
| `& <?:Boolean> <?:Boolean> ...`		|  `<Boolean>`			|  Logisches Und. Sollte ein Argument false sein, werden die anderen nicht mehr ausgewertet! |
| `\| <?:Boolean> <?:Boolean> ...`		|  `<Boolean>`			|  Logisches Oder. Sollte ein Argument true sein, werden die anderen nicht mehr ausgewertet! |
| `! <?:Boolean>`				|  `<Boolean>`			|  Logisches Nicht |
| `= <?>`					|  `<?>`				|  Identitätsoperator: Gibt genau das zurück, was ihm übergeben wurde |
| `= <?> <?> ...`				|  `<Boolean>`			|  Vergleichsoperator |
| `@ <?:Number> <?:Number>`			|  `<ByteSequence>`		|  Erstellt eine ByteSequence an Adresse x der Länge y |
| `? <?>`					|  `<String>`			|  Gibt den Typnamen des Ausdrucks als String zurück |
| `\ >?<`					|  `<Function>`			|  Gibt einen Funktionspointer zur Funktion zurück |
| `\ <?:Function>`				|  `<Function>`			|  Gibt einen Funktionspointer zur Funktion zurück |
| `\ <?:String>`				|  `<Function>`			|  Gibt einen Funktionspointer zum Funktionsnamen zurück |
| `^ <?:ByteSequence> <?:Number> <?:Number>`|  `<Null=null>`			|  Schreibt an die Adresse ByteSequence 0+x das Byte y |
| `% <?:Number>`					|  `<?>`			|  Gibt den Wert einer Variablen zurück, wobei diese Variable nicht über ihren Namen sondern ihrem Index der Erstellung zugegriffen wird. Der Index startet bei 1! |
| `' <'?>`					|  `<?>`				|  Identitätsoperator, der im ersten Argument einen neuen Scope öffnet |
| `~ <?:Quote>`			|  `<?>`				|  Führt einen Quote aus. Hierbei wird kein neuer Scope geöffnet, sondern der ausgeführte Code verhält sich, als hätte er genau an dieser Stelle gestanden |
| `~ >?< >?< <?> ..2`			|  `<?>`				|  Ruft eine Funktion über ihren Fieldname auf |
| `~ <?:Function/String> >?< <?> ..2`		|  `<?>`				|  Ruft eine Funktion/Lambda über einen Funktionspointer oder über ihren Funktionsnamen als String auf |
| `´ <?>`				|  `<Quote>`			|  Erstellt eine Quote. Der hinter dem Operator gelegende Code wird nicht ausgeführt, sondern gekappselt. Dieser Code kann dann später im Programm an beliebiger Stelle ausgeführt werden |
| `function/func/fn >?< <'?>`			|  `<Function>`				|  Erzeugt eine Funktion |
| `return`					|  `<Null=null>`				|  Bricht die Ausführung in der aktuellen Funktion mit null ab |
| `return <?>`				|  `<?>`				|  Bricht die Ausführung in der aktuellen Funktion ab |
| `contract <?:Boolean>`			|  `<Null=null>`				|  Notwendige Bedingung für die Möglichkeit der Funktionsausführung |
| `assert <?:Boolean>`			|  `<?>`				|  Notwendige Bedingung für die Ausführung des gesamten Programmes |
| `assert <?:Boolean> <?:String>`		|  `<?>`				|  Notwendige Bedingung für die Ausführung des gesamten Programmes mit Debugtext |
| `lambda <'?>`				|  `<Function>`			|  Erzeugt eine anonyme Funktion (Lambda), führt sie allerdings nicht aus! Das muss mit dem ~ Operator geschehen |
| `yield <?>`				|  `<?>`				|  Bricht in der aktuellen Klammerung mit einem Wert ab |
| `when <?:Boolean> ... <?>`		|  `<?>`				|  Führt das letzte Argument nur dann aus, wenn alle Bedingungen wahr sind. Sollte eine Bedingung falsch sein, werden die anderen nicht mehr ausgewertet |
| `expect >?< <?:String> ..2 <?>`		|  `<?>`				|  Führt das letzte Argument nur dann aus, wenn alle Variablen vom jeweiligen Typ existieren |
| `expect >?< <?:String> ..2 <?> else <?>`	|  `<?>`				|  Führt das drittletzte Argument nur dann aus, wenn alle Variablen vom jeweiligen Typ existieren, ansonsten das letzte Argument |
| `null`					|  `<Null=null>`			|  Konstantes null |
| `Null`					|  `<String="Null">`		|  Typkonstante |
| `Number`					|  `<String="Number">`		|  Typkonstante |
| `String`					|  `<String="String">`		|  Typkonstante |
| `Boolean`					|  `<String="Boolean">`		|  Typkonstante |
| `List`					|  `<String="List">`		|  Typkonstante |
| `Struct`					|  `<String="Struct">`		|  Typkonstante |
| `Function`				|  `<String="Function">`		|  Typkonstante |
| `ByteSequence`				|  `<String="ByteSequence">`	|  Typkonstante |
| `Signature`				|  `<String="Signature>`		|  Typkonstante |
| `Void`				|  `<String="Void">`		|  Typkonstante |
| `Stream`				|  `<String="Stream">`		|  Typkonstante |
| `Quote`				|  `<String="Quote">`		|  Typkonstante |
| `Mutex`				|  `<String="Mutex">`		|  Typkonstante |
| `PI`					|  `<Number=$3.14159>`		|  Kreiszahl |
| `EUL`					|  `<Number=$2.71828>`		|  Euler'sche Zahl |
| `LN2`					|  `<Number=$0.69314>`		|  Mathematische Konstante |
| `LN10`					|  `<Number=$2.30258>`		|  Mathematische Konstante |
| `SQRT2`					|  `<Number=$1.41421>`		|  Mathematische Konstante |
| `SQRT3`					|  `<Number=$1.73205>`		|  Mathematische Konstante |
| `SQRT2PI`					|  `<Number=$2.50662>`		|  Mathematische Konstante |
| `print <?> ...`				|  `<Null=null>`			|  Gibt alle Objekt nacheinander in der Konsole aus ohne Zeilenumbrüche, aber mit Leerzeichen zwischen den Argumenten |
| `println <?> ...`				|  `<Null=null>`			|  Gibt alle Objekt nacheinander in der Konsole, mit Leerzeichen zwischen den Argumenten und Zeilenumbruch am Ende |
| `string <?> ...`				|  `<String>`			|  Wandelt alle Argumente in einen String um, und verbindet diese ohne Leerzeichen |
| `number <?:String/Number>`		|  `<Number>`			|  Wandelt den Ausdruck in eine Zahl um |
| `bytesequence <?:String/Number>`		|  `<Number>`			|  Wandelt den Ausdruck in eine ByteSequence um |
| `throw <?:Number>`			|  `<Null=null>`			|  Wirft einen Fehler mit dem Fehlercode |
| `throw <?:Number> <?:String>`		|  `<Null=null>`			|  Wirft einen Fehler mit dem Fehlercode und einem Fehlertext, der auf der Konsole ausgegeben wird |
| `error`					|  `<Number>`			|  Gibt den zuletzt geworfenen Fehlercode zurück |
| `error line`				|  `<Number>`			|  Gibt die Zeile an, in der der letzte Fehler geworfen wurde |
| `catch <?>`			|  `<?>`				|  Führt den Code nur dann aus, wenn überhaupt ein Fehler geworfen wurde (Code != 0). Der Fehler wird danach auf 0 zurückgesetzt |
| `catch <?:Number> <?>`			|  `<?>`				|  Führt das letzte Argument nur dann aus, wenn der entsprechende Fehlercode geworfen wurde. Der Fehler wird danach auf 0 zurückgesetzt |
| `get <?:List> <?:Number>`			|  `<?>`				|  Gibt ein Objekt aus der Liste über seinen Index zurück |
| `get <?:Struct> <?>`			|  `<?>`				|  Gibt ein Objekt aus einem Struct über sein Key zurück |
| `put <?:List> <?>`			|  `<?:List>`				|  Hängt ein beliebiges Objekt ans Ende der Liste an. Änderungen einer Liste sind inPlace (da nicht primitiv) |
| `put <?:Struct> <?> <?>`			|  `<?:Struct>`				|  Fügt/Überschreibt ein key-value-Paar innerhalb des Structs. Änderungen eine Structs sind inPlace (da nicht primitiv) |
| `putonce <?:List> <?> `			|  `<Boolean>`				|  Fügt ein Objekte einer Liste hinzu, vorausgesetzt dass das Objekt noch nicht in der Liste enthalten ist (Mengeneigenschaft). Änderungen einer Liste sind inPlace (da nicht primitiv) |
| `first <?:List>`				|  `<?>`				|  Gibt das erste Element einer Liste zurück, sonst null |
| `first <?:List> <?>`				|  `<Number>`				|  Gibt den Index des ersten Elements zurück, das dem entsprechendem Ausdruck gleicht (von vorn das erste entsprechende Element), ansonsten -1 |
| `last <?:List>`				|  `<?>`				|  Gibt das letzte Element einer Liste zurück |
| `last <?:List> <?>`				|  `<Number>`				|  Gibt den Index des letzten Elements zurück, das dem entsprechendem Ausdruck gleicht (von hinten das erste entsprechende Element) |
| `contains <?:List> <?>`		|  `<Boolean>`			|  Prüft, ob ein Objekt sich innerhalb der Liste befindet |
| `contains <?:Struct> <?>`		|  `<Boolean>`			|  Prüft, ob ein Objekt als value sich innerhalb des Structs befindet |
| `containskey <?:Struct> <?>`		|  `<Boolean>`			|  Prüft, ob ein Objekt als key sich innerhalb des Structs befindet |
| `size <?:List/Struct/ByteSequence/String>`	|  `<Number>`			|  Gibt die Anzahl an Elementen einer Liste, bzw. die Anzahl an key-value-Paaren in einem Struct, bzw. die Anzahl an Bytes in einer ByteSequence,oder die Anzahl an Zeichen innerhalb eines Strings zurück |
| `list`				|  `<List>`			|  Erzeugt eine neue leere Liste |
| `list <?> ...`				|  `<List>`			|  Erzeugt eine neue Liste, wobei die Arguemente die Elemente in dieser Liste sein sollen; die Reihenfolge wird übernommen. Wenn ein Element nicht primitiv ist, wird die Liste automatisch weak! |
| `struct`				|  `<Struct>`			|  Erzeugt ein neues leere Struct |
| `struct <?> <?> ..2`				|  `<Struct>`			|  Erzeugt ein neues Struct mit den gegebenen Eigenschaften. Wenn ein nicht primitives Objekt dabei ist, wird das Struct automatisch weak! |
| `merge <?:List> ...`			|  `<List>`			|  Hängt Listen der Reihe nach aneinander, sodass eine kombinierte lange Liste entsteht |
| `peek <?:List>`				|  `<?>`				|  Gibt das letzte Objekt auf der Liste (oberstes Objekt auf dem Stapel) zurück, ohne es zu entfernen, sonst null |
| `pop <?:List>`				|  `<?>`				|  Gibt das letzte Objekt aus der Liste (oberstes Objekt auf dem Stapel) zurück, und entfernt es aus der Liste |
| `remove <?:List> <?:Number>`		|  `<?>`				|  Entfernt ein Objekt am Index; die restliche Reihenfolge der Liste bleibt erhalten, die Liste wird verkürzt |
| `remove <?:List> <?:List>`		|  `<List>`				|  Entfernt alle Objekte aus der ersten Liste, die in der zweiten Liste enthalten sind; die restliche Reihenfolge der Liste bleibt erhalten, die Liste wird verkürzt |
| `remove <?:Struct> <?>`		|  `<?>`				|  Entfernt ein Objekt aus dem Struct. Das ist eine inPlace Operation, da Structs nicht primitiv sind |
| `reverse <?:List>`			|  `<List>`			|  Kehrt die Reihenfolge der Objekte einer Liste um |
| `slice <?:List> <?:Number> <?:Number>`		|  `<List>`				|  Gibt eine Subliste zurück. Die beiden angegebenen Indizes sind inklusiv! |
| `slice <?:String> <?:Number> <?:Number>`		|  `<String>`				|  Gibt einen Substring zurück. Die beiden angegebenen Indizes sind inklusiv! |
| `map <?:List> ... >?< <'?>`		|  `<List>`			|  Geht durch jedes Element der Liste, weist das dem Fieldname zu, und das Ergebnis des letzten Ausdrucks für jeden Durchlauf wird in eine gemeinsame neue Liste gelegt. Die Reihenfolge bleibt erhalten. Wenn der letzte Ausdruck null zurückgibt, wird das Objekt herausgenommen, ansonsten wird es hinzugefügt |
| `foreach/iterate <?:List> >?< <'?>`	|  `<Null=null>`			|  Schleife, die jedes Element der Liste nacheinander dem Fieldname zuweist, und den entsprechenden Code im letzten Ausdruck ausführt |
| `foreach/iterate <?:Struct> >?< >?< <'?>`	|  `<Null=null>`			|  Schleife, die jedes key-value Paar des Structs nacheinander den Fieldnames zuweist, und den entsprechenden Code im letzten Ausdruck ausführt |
| `riterate <?:List> >?< <'?>`		|  `<Null=null>`			|  Schleife, die jedes Element der Liste rückwärts nacheinander dem Fieldname zuweist, und den entsprechenden Code im letzten Ausdruck ausführt |
| `weak <?:List/Struct>`			|  `<List/Struct>`		|  Setzt die Weak-Eigenschaft einer Liste oder eines Structs. Danach darf die Liste oder das Struct selbst auch Listen oder Structs als Elemente enthalten |
| `select <?:Struct> <?:String> ...`	|  `<?>`				|  Gibt den Wert eines String-keys durch hierarchisch miteinander verschachtelte Structs zurück |
| `signature <?:String/Signature/Struct> ...`	|  `<Signature>`		|  Erzeugt eine Signatur mit den entsprechenden Eigenschaften |
| `has <?:Struct/Signature> <?:Signature/String> ...`	|  `<Boolean>`	|  Prüft, ob die erste Signatur/das erste Struct alle nachfolgenden Eigenschaften besitzt |
| `is <?:Struct/Signature> <?:Signature/String> ...`	|  `<Boolean>`	|  Prüft, ob die erste Signatur/das erste Struct exakt alle nachfolgenden Eigenschaften besitzt, und keine mehr |
| `new <?:Number>`				|  `<ByteSequence>`		|  Erzeugt eine ByteSequence einer bestimmten Länge. Diese ByteSequenz ist zu Beginn mit Nullen überschrieben worden |
| `copy <?:ByteSequence> <?:ByteSequence> <?:Number> <?:Number> <?:Number>`	|  `<ByteSequence>`	|  Kopiert eine bestimmte Anzahl Bytes (z) mit einem Offset (x) von einer ersten ByteSequence an den Offset (y) einer zweiten ByteSequence. Zurückgegeben wird die Zielbytesequenz. Es handelt sich um eine inPlace Operation! |
| `delete <?:ByteSequence>	...`		|  `<Number>`			|  Löscht die Daten einer ByteSequence. Dabei bleibt das Objekt erhalten, aber der Speicher wird freigegeben |
| `factorial <?:Number>`			|  `<Number>`			|  Mathematische Funktion |
| `sqrt <?:Number>`				|  `<Number>`			|  Mathematische Funktion |
| `sin <?:Number>`				|  `<Number>`			|  Mathematische Funktion |
| `cos <?:Number>`				|  `<Number>`			|  Mathematische Funktion |
| `tan <?:Number>`				|  `<Number>`			|  Mathematische Funktion |
| `random`					|  `<Number>`			|  Zufällige Zahl zwischen 0 und 1 |
| `random <?:Number> <?:Number>`		|  `<Number>`			|  Zufällige Zahl zwischen inklusiv x und inklusiv y |
| `random uuid`					|  `<String>`			|  Zufällige UUID |
| `modulo <?:Number> <?:Number>`		|  `<Number>`			|  Mathematische Funktion |
| `log10 <?:Number>`			|  `<Number>`			|  Mathematische Funktion |
| `ln <?:Number>`				|  `<Number>`			|  Mathematische Funktion |
| `exp <?:Number>`				|  `<Number>`			|  Mathematische Funktion |
| `pow <?:Number> <?:Number>`		|  `<Number>`			|  Mathematische Funktion |
| `round <?:Number>`			|  `<Number>`			|  Mathematische Funktion |
| `round <?:Number> <?:Number>`		|  `<Number>`			|  Rundet x auf y Nachkommastellen |
| `var >?<`				|  `<Null=null>`			|  Erzeugt eine Variable mit dem Wert null |
| `var >?< <?>`				|  `<?>`			|  Erzeugt eine Variable mit einem Wert, und gibt diesen Wert zurück |
| `var >?< <?:String> <?>`			|  `<?>`			|  Erzeugt eine Variable mit einem Typ und einem Wert (es gibt eine Typabfrage, ansonsten einen Fehler), und gibt diesen Wert zurück |
| `const >?<`				|  `<Null=null>`			|  Erzeugt eine Konstante mit dem Wert null |
| `const >?< <?>`				|  `<?>`			|  Erzeugt eine Konstante mit einem Wert, und gibt diesen Wert zurück |
| `const >?< <?:String> <?>`			|  `<?>`			|  Erzeugt eine Konstante mit einem Typ und einem Wert (es gibt eine Typabfrage, ansonsten einen Fehler), und gibt diesen Wert zurück |
| `set/assign >?< <?>`			|  `<?>`				|  Ändert den Wert einer Variablen. Der neue Wert muss den gleichen Typ enthalten, wie vorher (null gilt als uninitialisiert, und darf mit jedem neuen Typ überschrieben werden). Das Setzen auf null ist nicht erlaubt. Es wird der neue Wert der Variablen zurückgegeben (unter Beachtung einer Änderung durch einen watch-Block) |
| `set/assign <?:String> <?>`			|  `<?>`				|  Ändert den Wert einer Variablen über ihren Namen als String. Der neue Wert muss den gleichen Typ enthalten, wie vorher (null gilt als uninitialisiert, und darf mit jedem neuen Typ überschrieben werden). Das Setzen auf null ist nicht erlaubt. Es wird der neue Wert der Variablen zurückgegeben (unter Beachtung einer Änderung durch einen watch-Block) |
| `inc >?<`			|  `<Number>`				|  Erhöht den Zahlenwert einer Vaiable um Eins, und gibt den neuen Wert zurück |
| `inc <?:String>`			|  `<Number>`				|  Erhöht den Zahlenwert einer Vaiable (die über ihren Namen spezifiziert wird) um Eins, und gibt den neuen Wert zurück |
| `dec >?<`			|  `<Number>`				|  Erniedrigt den Zahlenwert einer Vaiable um Eins, und gibt den neuen Wert zurück |
| `dec <?:String>`			|  `<Number>`				|  Erniedrigt den Zahlenwert einer Vaiable (die über ihren Namen spezifiziert wird) um Eins, und gibt den neuen Wert zurück 
| `if <?:Boolean> <'?>`			|  `<?>`				|  Bedingte Ausführung |
| `if <?:Boolean> <'?> <'?>`		|  `<?>`				|  Bedingte Ausführung |
| `if <?:Boolean> <'?> else <'?>`		|  `<?>`				|  Bedingte Ausführung mit else zur besseren Lesbarkeit |
| `switch <?> <?> <?> ..2`		|  `<?>`				|  Switch-Anweisung die den Ausdruck ausführt, der passend ist, ansonsten null |
| `switch <?> <?> <?> ..2 <?>`		|  `<?>`				|  Switch-Anweisung die den Ausdruck ausführt, der passend ist, ansonsten default (letzter Ausdruck) |
| `loop <?:Boolean> <'?>`		|  `<?>`				|  Bedingte Schleife |
| `break`					|  `<Null=null>`			|  Beendet die Schleife |
| `break <?>`					|  `<?>`			|  Beendet die Schleife, und gibt einen Wert zurück |
| `trim <?:String>`		|  `<String>`			|  Entfernt alle Whitespaces zu Beginn und zum Schluss eines Strings |
| `contains <?:String> <?:String>`		|  `<Boolean>`			|  Prüft, ob ein Substring y im String x enthalten ist |
| `mutex >?<`		|  `<Mutex>`			|  Erzeugt einen Mutex auf eine Variable über ihren Namen |
| `mutex <?:String>`		|  `<Mutex>`			|  Erzeugt einen Mutex auf eine Variable über ihren String-Namen |
| `async >?< >?< <?> ..2 <'?>`		|  `<Boolean>`			|  Startet die parallele Ausführung. Im Capture können dem neuem Scope im letzten Argument Variablen mitgegeben werden. Das erste Argument muss eine Variable vom Typ null sein, die noch nicht von einem anderen Async-Befehl besetzt wurde. Das Ergebnis des letzten Arguments wird in die besetzte Variable gelegt |
| `await <?:Mutex>`				|  `<?>`				|  Gibt den Wert der Variable zurück, auf die der Mutex erzeugt wurde, wenn kein anderer Prozess gerade einen Lock hält. Ansonsten wird der Mutex für diesen Prozess gelockt. Nach der Anwendung sollte der Mutex mit unlock freigegeben werden! |
| `await >?<`				|  `<?>`				|  Blockiert den Codefluss bis der Wert der Variable, die zuvor von einem Async-Block genutzt werden musste, verfügbar ist |
| `unlock <?:Mutex>`		|  `<Null=null>`			|  Löst den Lock eines Mutex auf |
| `time`				|  `<Number>`				|  Gibt die Anzahl an vergangenen Millisekunden seit dem 01.01.1970 zurück |
| `time <?>`				|  `<Number>`				|  Misst die Ausführungszeit des Ausdrucks in Millisekunden (mit Dezimalstellen bis in den Nanosekundenbreich) |
| `functhis`				|  `<Function>`			|  Gibt einen Funktionspointer auf den Prototypen der Funktion zurück, in der das Keyword aufgerufen wurde |
| `this`					|  `<String>`			|  Gibt den Namen der Funktion zurück, in der der Keyword aufgerufen wurde |
| `line`					|  `<Number>`			|  Gibt die Zeilennummer zurück, in der das Keyword aufgerufen wurde |
| `defined <?:String>`			|  `<Boolean>`			|  Prüft, ob im aktuellen oder höheren Scope die Variable mit dem Namen x definiert wurde |
| `refs <?>`				|  `<Number>`			|  Gibt die Anzahl an Referenzen auf das Ergebnis eines Ausdrucks zurück |
| `write <?:Stream> <?:String/List/ByteSequence>`				|  `<Boolean>`			|  Schreibt Daten in einen Datenstrom, und gibt zurück, ob die Aktion erfolgreich war |
| `write <?:Stream> <?:List>`				|  `<Number>`			|  Schreibt Daten unterschiedlichen Typs (nur Primitive! Sublisten werden nicht gesendet) in den Datenstrom. Gibt die Anzahl an erfolgreichen Übertragungen zurück |
| `read <?:Number> <?:Stream> <?:String>`				|  `<?>`			|  Liest Daten aus einem Datenstrom mit bevorzugtem Typ der Daten (Konvertierung wird automatisch vorgenommen) |
| `read <?:Number> <?:Stream>`				|  `<ByteSequence>`			|  Liest eine bestimmte Anzahl an Bytes aus einem Stream |
| `close <?:Stream>`				|  `<Null=null>`			|  Schließt einen Stream. Sollte er schon geschlossen sein, passiert schlicht nichts |
| `io file <?:String>`				|  `<Stream>`			|  Gibt einen Stream auf eine Datei über ihren Pfad zurück |
| `io console`				|  `<Stream>`			|  Gibt einen Stream auf die Konsole des Programmes zurück |
| `io net <?:String> <?:Number>`				|  `<Stream>`			|  Gibt einen Stream auf einen Server über seine Ip und den Port zurück |
| `amiant <?:String>`			|  `<Function>`			|  Parst einen String (mit Amiant Code) und gibt ein Lambda zurück, das diesen Code als Codeblock besitzt |
| `reflect funcname`					|  `<String>`		|  Gibt den Namen der Funktion/Lambda zurück, in der dieser Befehl ausgeführt wurde |
| `reflect this`					|  `<Function>`		|  Gibt den Prototyp der Funktion/Lambda zurück, in dem der Befehl ausgeführt wurde |
| `reflect line`					|  `<Number>`		|  Gibt die Zeilennummer zurück, in der dieser Befehl geschrieben steht (Zählung beginnt bei 1) |
| `reflect renamef <?:String> <?:String>`					|  `<Function>`		|  Benennt die Funktion mit Namen in einen neuen Namen um |
| `reflect listvars`					|  `<List>`		|  Gibt eine Liste von Strings zurück - nämlich die Namen aller im aktuellen Scope definierter Variablen |
| `reflect structvars`					|  `<Struct>`		|  Gibt ein Struct von String-String zurück - nämlich die Namen aller im aktuellen Scope definierter Variablen und ihre Werte |
| `avm version`				|  `<Number>`			|  Gibt die aktuell verwendete Amiant-Version zurück |
| `avm compver`				|  `<Number>`			|  Gibt die Compiler-Version oder spezifische Implementierungsversion zurück |
| `avm memory`				|  `<Number>`			|  Gibt die Speicherbelegung dieses Amaint-Programms und aller höheren oder tieferen Meta-Programme in Bytes zurück |
| `avm platform`				|  `<String>`			|  Gibt das Betriebssystem/Plattform zurück, auf der die AVM läuft |
| `avm getenv <?:String>`			|  `<String>`			|  Gibt eine Umgebungsvariable zurück |
| `avm setenv <?:String> <?:String>`	|  `<Boolean>`			|  Setzt eine Umgebungsvariable |
| `avm regset <?:String> <?:String>`	|  `<String>`			|  Setzt einen Registry-Wert, auf den alle Amiant-Programme, die auf dem Betriebssystem unter dem Nutzer laufen, zugreifen können |
| `avm regget <?:String>`			|  `<String>`			|  Gibt einen Registry-Wert, auf den alle Amiant-Programme, die auf dem Betriebssystem unter dem Nutzer laufen, zugreifen können, zurück |
| `avm conf <?:String> <?:String>`			|  `<Boolean>`			|  Konfiguriert die lokale AVM-Instanz indem eine Eigenschaft über key-value gesetzt wird. Nur Level 0 Programme dürfen diesen Befehl nutzen |
| `avm printstats`			|  `<Null=null>`			|  Gibt die von der AVM bis zu diesem Zeitpunkt gesammelten Statistken auf der Konsole aus |
| `avm createdobjects`			|  `<Number>`			|  Gibt die Anzahl an entstanden Objekten zurück (Statistik) |
| `avm args`			|  `<List>`			|  Die die zu Programmbeginn übergebenen Konsolen-Argumente in eine Liste von Strings zurück |
| `manual <?>`			|  `<Null=null>`				|  Gibt die Spezifikation des darauf folgenden Operators auf der Konsole aus |
| `import <?:String>`			|  `<?>`				|  Lädt den Code über den spezifizierten Pfad und führt ihn an dieser Stelle im aktuellen Scope aus. Dieser Operator ist nur in der globalen Main-Funktion aufrufbar (Level 0). Das doppelte Importieren der gleichen Datei wird von der AVM blockiert |
| `exit <?:Number>`				|  `<Number>`			|  Beendet die sofortige Ausführung des aktuellen Amiant-Programms mit dem Exit-Code (bei den Meta-Programmen wird nur das Meta-Programm beendet; beim obersten Programm die gesamte AVM) |
