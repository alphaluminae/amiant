# Amiant Operator Specification (AOSP)

| Operator				|  Rückgabetyp  		|  Erklärung |
| ---             |  ---              | ---        |
| `+ <?:Number> <?:Number> ...`		|  `<Number>`			|  Addiert mehrere Zahlen miteinander |
| `- <?:Number> <?:Number> ...`		|  `<Number>`			|  Subtrahiert mehrere Zahlen miteinander |
| `* <?:Number> <?:Number> ...`		|  `<Number>`			|  Multipliziert mehrere Zahlen miteinander |
| `/ <?:Number> <?:Number> ...`		|  `<Number>`			|  Dividiert mehrere Zahlen miteinander |
| `> <?:Number> <?:Number>`			|  `<Boolean>`			|  Größer als |
| `< <?:Number> <?:Number>`			|  `<Boolean>`			|  Kleiner als |
| `& <?:Boolean> <?:Boolean> ...`		|  `<Boolean>`			|  Logisches Und |
| `\| <?:Boolean> <?:Boolean> ...`		|  `<Boolean>`			|  Logisches Oder |
| `! <?:Boolean>`				|  `<Boolean>`			|  Logisches Nicht |
| `= <?>`					|  `<?>`				|  Identitätsoperator: Gibt genau das zurück, was ihm übergeben wurde |
| `= <?> <?> ...`				|  `<Boolean>`			|  Vergleichsoperator |
| `@ <?:Number> <?:Number>`			|  `<ByteSequence>`		|  Erstellt eine ByteSequence an Adresse x der Länge y |
| `? <?>`					|  `<String>`			|  Gibt den Typnamen des Ausdrucks als String zurück |
| `\ >?<`					|  `<Function>`			|  Gibt einen Funktionspointer zur Funktion zurück |
| `\ <?:Function>`				|  `<Function>`			|  Gibt einen Funktionspointer zur Funktion zurück |
| `\ <?:String>`				|  `<Function>`			|  Gibt einen Funktionspointer zum Funktionsnamen zurück |
| `^ <?:ByteSequence> <?:Number> <?:Number>`|  `<Number>`			|  Schreibt an die Adresse ByteSequence 0+x das Byte y |
| `% <?:Number>`					|  `<?>`			|  Gibt den Wert einer Variablen zurück, wobei diese Variable nicht über ihren Namen sondern ihrem Index der Erstellung zugegriffen wird. Der Index startet bei 1! |
| `' <'?>`					|  `<?>`				|  Identitätsoperator, der im ersten Argument einen neuen Scope öffnet |
| `~ >?< >?< <?> ..2`			|  `<?>`				|  Ruft eine Funktion über ihren Fieldname auf |
| `~ <?:Function> >?< <?> ..2`		|  `<?>`				|  Ruft eine Funktion/Lambda über einen Funktionspointer auf |
| `function/func >?< <'?>`			|  `<Function>`				|  Erzeugt eine Funktion |
| `return`					|  `<Null=null>`				|  Bricht die Ausführung in der aktuellen Funktion mit null ab |
| `return <?>`				|  `<?>`				|  Bricht die Ausführung in der aktuellen Funktion ab |
| `contract <?:Boolean>`			|  `<?>`				|  Notwendige Bedingung für die Möglichkeit der Funktionsausführung |
| `assert <?:Boolean>`			|  `<?>`				|  Notwendige Bedingung für die Ausführung des gesamten Programmes |
| `assert <?:Boolean> <?:String>`		|  `<?>`				|  Notwendige Bedingung für die Ausführung des gesamten Programmes mit Debugtext |
| `lambda <'?>`				|  `<Function>`			|  Erzeugt eine anonyme Funktion (Lambda), führt sie allerdings nicht aus! Das muss mit dem ~ Operator geschehen |
| `yield <?>`				|  `<?>`				|  Bricht in der aktuellen Klammerung mit einem Wert ab |
| `when <?:Boolean> ... <?>`		|  `<?>`				|  Führt das letzte Argument nur dann aus, wenn alle Bedingungen wahr sind |
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
| `PI`					|  `<Number=$3.14159>`		|  Kreiszahl |
| `EUL`					|  `<Number=$2.71828>`		|  Euler'sche Zahl |
| `PHI`					|  `<Number=$1.61803>`		|  Goldener Schnitt |
| `TAU`					|  `<Number=$6.28318>`		|  Vollwinkel 2*pi |
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
| `catch <?:Number> <?>`			|  `<?>`				|  Führt das letzte Argument nur dann aus, wenn der entsprechende Fehlercode geworfen wurde |
| `get <?:List> <?:Number>`			|  `<?>`				|  Gibt ein Objekt aus der Liste über seinen Index zurück |
| `get <?:Struct> <?>`			|  `<?>`				|  Gibt ein Objekt aus einem Struct über sein Key zurück |
| `put <?:List> <?>`			|  `<?>`				|  Hängt ein beliebiges Objekt ans Ende der Liste an |
| `put <?:Struct> <?> <?>`			|  `<?>`				|  Fügt/Überschreibt ein key-value-Paar innerhalb des Structs |
| `putonce <?:List> <?> ...`			|  `<Boolean>`				|  Fügt eines oder mehrere Objekte einer Liste hinzu, vorausgesetzt dass das Objekt noch nicht in der Liste enthalten ist (Mengeneigenschaft) |
| `first <?:List>`				|  `<?>`				|  Gibt das erste Element einer Liste zurück |
| `first <?:List> <?>`				|  `<Number>`				|  Gibt den Index des ersten Elements zurück, das dem entsprechendem Ausdruck gleicht (von vorn das erste entsprechende Element) |
| `last <?:List>`				|  `<?>`				|  Gibt das letzte Element einer Liste zurück |
| `last <?:List> <?>`				|  `<Number>`				|  Gibt den Index des letzten Elements zurück, das dem entsprechendem Ausdruck gleicht (von hinten das erste entsprechende Element) |
| `contains <?:List> <?> ...`		|  `<Boolean>`			|  Prüft, ob ein Objekt sich innerhalb der Liste befindet |
| `contains <?:Struct> <?>	...`		|  `<Boolean>`			|  Prüft, ob ein Objekt als value sich innerhalb des Structs befindet |
| `containskey <?:Struct> <?>`		|  `<Boolean>`			|  Prüft, ob ein Objekt als key sich innerhalb des Structs befindet |
| `size <?:List/Struct/ByteSequence>`	|  `<Number>`			|  Gibt die Anzahl an Elementen einer Liste, bzw. die Anzahl an key-value-Paaren in einem Struct, bzw. die Anzahl an Bytes in einer ByteSequence zurück |
| `list`				|  `<List>`			|  Erzeugt eine neue leere Liste |
| `list <?> ...`				|  `<List>`			|  Erzeugt eine neue Liste, wobei die Arguemente die Elemente in dieser Liste sein sollen; die Reihenfolge wird übernommen |
| `merge <?:List> ...`			|  `<List>`			|  Hängt Listen der Reihe nach aneinander, sodass eine kombinierte lange Liste entsteht |
| `peek <?:List>`				|  `<?>`				|  Gibt das letzte Objekt auf der Liste (oberstes Objekt auf dem Stapel) zurück, ohne es zu entfernen |
| `pop <?:List>`				|  `<?>`				|  Gibt das letzte Objekt aus der Liste (oberstes Objekt auf dem Stapel) zurück, und entfernt es aus der Liste |
| `remove <?:List> <?:Number>`		|  `<?>`				|  Entfernt ein Objekt am Index; die restliche Reihenfolge der Liste bleibt erhalten, die Liste wird verkürzt |
| `remove <?:List> <?:List>`		|  `<List>`				|  Entfernt alle Objekte aus der ersten Liste, die in der zweiten Liste enthalten sind; die restliche Reihenfolge der Liste bleibt erhalten, die Liste wird verkürzt |
| `reverse <?:List>`			|  `<List>`			|  Kehrt die Reihenfolge der Objekte einer Liste um |
| `filter <?:List> >?< <'?>`		|  `<List>`			|  Geht durch jedes Element der Liste, weist das dem Fieldname zu, und das Ergebnis des letzten Ausdrucks für jeden Durchlauf wird in eine neue Liste gelegt. Die Reihenfolge bleibt erhalten. Wenn der letzte Ausdruck false zurückgibt, wird das Objekt herausgefiltert, bei true wird es behalten |
| `map <?:List> ... >?< <'?>`		|  `<List>`			|  Geht durch jedes Element der Liste, weist das dem Fieldname zu, und das Ergebnis des letzten Ausdrucks für jeden Durchlauf wird in eine gemeinsame neue Liste gelegt. Die Reihenfolge bleibt erhalten. Wenn der letzte Ausdruck null zurückgibt, wird das Objekt herausgenommen |
| `foreach/iterate <?:List> >?< <'?>`	|  `<Null=null>`			|  Schleife, die jedes Element der Liste nacheinander dem Fieldname zuweist, und den entsprechenden Code im letzten Ausdruck ausführt |
| `foreach/iterate <?:Struct> >?< >?< <'?>`	|  `<Null=null>`			|  Schleife, die jedes key-value Paar des Structs nacheinander den Fieldnames zuweist, und den entsprechenden Code im letzten Ausdruck ausführt |
| `riterate <?:List> >?< <'?>`		|  `<Null=null>`			|  Schleife, die jedes Element der Liste rückwärts nacheinander dem Fieldname zuweist, und den entsprechenden Code im letzten Ausdruck ausführt |
| `weak <?:List/Struct>`			|  `<List/Struct>`		|  Setzt die Weak-Eigenschaft einer Liste oder eines Structs. Danach darf die Liste oder das Struct selbst auch Listen oder Structs als Elemente enthalten |
| `select <?:Struct> <?:String> ...`	|  `<?>`				|  Gibt den Wert eines String-keys durch hierarchisch miteinander verschachtelte Structs zurück |
| `signature <?:String/Signature/Struct> ...`	|  `<Signature>`		|  Erzeugt eine Signatur mit den entsprechenden Eigenschaften |
| `has <?:Struct/Signature> <?:Signature/String> ...`	|  `<Boolean>`	|  Prüft, ob die erste Signatur/das erste Struct alle nachfolgenden Eigenschaften besitzt |
| `is <?:Struct/Signature> <?:Signature/String> ...`	|  `<Boolean>`	|  Prüft, ob die erste Signatur/das erste Struct exakt alle nachfolgenden Eigenschaften besitzt, und keine mehr |
| `new <?:Number>`				|  `<ByteSequence>`		|  Erzeugt eine ByteSequence einer bestimmten Länge |
| `copy <?:ByteSequence> <?:ByteSequence> <?:Number> <?:Number> <?:Number>`	|  `<Boolean>`	|  Kopiert eine bestimmte Anzahl Bytes (z) mit einem Offset (x) von einer ersten ByteSequence an den Offset (y) einer zweiten ByteSequence |
| `delete <?:ByteSequence>	...`		|  `<Number>`			|  Löscht die Daten einer ByteSequence. Dabei bleibt das Objekt erhalten, aber der Speicher wird freigegeben |
| `bytes <?> ...`				|  `<Number>`			|  Zählt die Bytes eines Objekts (bei ByteSequence ist das Verhalten gleich dem des size-Operators) |
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
| `ipow <?:Number> <?:Number>`		|  `<Number>`			|  Mathematische Funktion |
| `round <?:Number>`			|  `<Number>`			|  Mathematische Funktion |
| `round <?:Number> <?:Number>`		|  `<Number>`			|  Rundet x auf y Nachkommastellen |
| `var >?<`				|  `<Null=null>`			|  Erzeugt eine Variable mit dem Wert null |
| `var >?< <?>`				|  `<Null=null>`			|  Erzeugt eine Variable mit einem Wert |
| `var >?< <?:String> <?>`			|  `<Null=null>`			|  Erzeugt eine Variable mit einem Typ und einem Wert (es gibt eine Typabfrage, ansonsten einen Fehler) |
| `var >?< <?:String> <?> <?:Boolean>`	|  `<Null=null>`			|  Erzeugt eine Variable mit einem Typ, einem Wert und einer Bedingung für den Wert |
| `const >?< <?>`				|  `<Null=null>`			|  Erzeugt eine Konstante |
| `set/assign >?< <?>`			|  `<?>`				|  Ändert den Wert einer Variablen. Der neue Wert muss den gleichen Typ enthalten, wie vorher (null gilt als uninitialisiert, und darf mit jedem neuen Typ überschrieben werden). Das Setzen auf null ist nicht erlaubt |
| `if <?:Boolean> <'?>`			|  `<?>`				|  Bedingte Ausführung |
| `if <?:Boolean> <'?> else <'?>`		|  `<?>`				|  Bedingte Ausführung mit else |
| `switch <?> <?> <?> ..2`		|  `<?>`				|  Switch-Anweisung die den Ausdruck ausführt, der passend ist, ansonsten null |
| `switch <?> <?> <?> ..2 <?>`		|  `<?>`				|  Switch-Anweisung die den Ausdruck ausführt, der passend ist, ansonsten default (letzter Ausdruck) |
| `loop <?:Boolean> <'?>`		|  `<?>`				|  Bedingte Schleife |
| `break`					|  `<Null=null>`			|  Beendet die Schleife |
| `break <?>`					|  `<?>`			|  Beendet die Schleife, und gibt einen Wert zurück |
| `trim <?:String>`		|  `<String>`			|  Entfernt alle Whitespaces zu Beginn und zum Schluss eines Strings |
| `contains <?:String> <?:String>`		|  `<Boolean>`			|  Prüft, ob ein Substring y im String x enthalten ist |
| `startswith <?:String> <?:String>`		|  `<Boolean>`		|  Prüft, ob der String x mit String y beginnt |
| `async >?< >?< <?> ..2 <'?>`		|  `<Boolean>`			|  Startet die parallele Ausführung. Im Capture können dem neuem Scope im letzten Argument Variablen mitgegeben werden. Das erste Argument muss eine Variable vom Typ null sein, die noch nicht von einem anderen Async-Befehl besetzt wurde. Das Ergebnis des letzten Arguments wird in die besetzte Variable gelegt |
| `await >?<`				|  `<?>`				|  Blockiert den Codefluss bis der Wert der Variable, die zuvor von einem Async-Block genutzt werden musste, verfügbar ist |
| `functhis`				|  `<Function>`			|  Gibt einen Funktionspointer auf den Prototypen der Funktion zurück, in der das Keyword aufgerufen wurde |
| `this`					|  `<String>`			|  Gibt den Namen der Funktion zurück, in der der Keyword aufgerufen wurde |
| `line`					|  `<Number>`			|  Gibt die Zeilennummer zurück, in der das Keyword aufgerufen wurde |
| `mount <?:String> <?:String>`		|  `<Boolean>`			|  Fügt eine neue Funktion im aktuellen Scope unter dem Namen zurück. Hierbei wird das zweite Argument als Code geparst und dem Programm dann dynamisch hinzugefügt |
| `unmount <?:String>`			|  `<Boolean>`			|  Entfernt eine bereits existierende Funktion (es wird hierarchisch nach der Funktion gesucht, bis sie gefunden wurde; sie muss nicht unbedingt im gleichen Scope liegen) |
| `defined <?:String>`			|  `<Boolean>`			|  Prüft, ob im aktuellen oder höheren Scope die Variable mit dem Namen x definiert wurde |
| `refs <?>`				|  `<Number>`			|  Gibt die Anzahl an Referenzen auf das Ergebnis eines Ausdrucks zurück |
| `write <?:Stream> <?:String/List/ByteSequence>`				|  `<Number>`			|  Schreibt Daten in einen Datenstrom |
| `read <?:String> <?:Stream>`				|  `<?>`			|  Liest Daten aus einem Datenstrom mit Typ der Daten |
| `read <?:Number> <?:Stream>`				|  `<ByteSequence>`			|  Liest eine bestimmte Anzahl an Bytes aus einem Stream |
| `io file <?:String>`				|  `<Stream>`			|  Gibt einen Stream auf eine Datei über ihren Pfad zurück |
| `io console`				|  `<Stream>`			|  Gibt einen Stream auf die Konsole des Programmes zurück |
| `io net <?:String> <?:Number>`				|  `<Stream>`			|  Gibt einen Stream auf einen Server über seine Ip und den Port zurück |
| `amiant <?:String>`			|  `<Number>`			|  Startet ein neues Amiant-Programm ein Level höher. Dabei wird der übergebene String als Programmcode genutzt. Es wird der Exit-Code dieses Programms zurückgegeben. Der Programmfluss blockiert, bis das Meta-Programm beendet wurde |
| `avm version`				|  `<Number>`			|  Gibt die aktuell verwendete Amiant-Version zurück |
| `avm restart`				|  `<Null=null>`			|  Startet das oberste Programm neu. Dabei werden alle Meta-Programme beendet |
| `avm compver`				|  `<Number>`			|  Gibt die Compiler-Version oder spezifische Implementierungsversion zurück |
| `avm memory`				|  `<Number>`			|  Gibt die Speicherbelegung dieses Amaint-Programms und aller höheren oder tieferen Meta-Programme in Bytes zurück |
| `avm platform`				|  `<String>`			|  Gibt das Betriebssystem/Plattform zurück, auf der die AVM läuft |
| `avm getenv <?:String>`			|  `<String>`			|  Gibt eine Umgebungsvariable zurück |
| `avm setenv <?:String> <?:String>`	|  `<Boolean>`			|  Setzt eine Umgebungsvariable |
| `avm regset <?:String> <?:String>`	|  `<String>`			|  Setzt einen Registry-Wert, auf den alle Amiant-Programme, die auf dem Betriebssystem unter dem Nutzer laufen, zugreifen können |
| `avm regget <?:String>`			|  `<String>`			|  Gibt einen Registry-Wert, auf den alle Amiant-Programme, die auf dem Betriebssystem unter dem Nutzer laufen, zugreifen können, zurück |
| `avm conf <?:String> <?:String>`			|  `<Boolean>`			|  Konfiguriert die lokale AVM-Instanz indem eine Eigenschaft über key-value gesetzt wird. Nur Level 0 Programme dürfen diesen Befehl nutzen |
| `exit <?:Number>`				|  `<Number>`			|  Beendet die sofortige Ausführung des aktuellen Amiant-Programms mit dem Exit-Code (bei den Meta-Programmen wird nur das Meta-Programm beendet; beim obersten Programm die gesamte AVM) |
