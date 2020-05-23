## Datentypen

Jeder Wert in Rust ist von einem bestimmten *Datentyp*, der Rust mitteilt,
welche Art von Daten angegeben wird, damit es weiß, wie es mit diesen Daten
arbeiten soll. Wir werden uns zwei Datentyp-Untermengen ansehen: Skalar und
Verbund.

Denk daran, dass Rust eine *statisch typisierte* Sprache ist, was bedeutet,
dass es die Typen von allen Variablen zur Kompilierzeit kennen muss. Der
Kompilierer kann normalerweise auf der Grundlage des Wertes und wie wir ihn
verwenden ableiten, welchen Typ wir verwenden wollen. Wenn mehrere Typen
möglich sind, wie zum Beispiel als wir im Abschnitt [„Vergleich der Vermutung
mit der Geheimzahl“][comparing-the-guess-to-the-secret-number] eine
Zeichenkette (`String`) mittels `parse` zu einem numerischen Typ umwandelten,
müssen wir eine Typ-Annotation ergänzen, wie hier:

```rust
let guess: u32 = "42".parse().expect("Keine Zahl!");
```

Wenn wir diese Typ-Annotation nicht angeben, zeigt Rust den folgenden Fehler
an, was bedeutet, dass der Kompilierer mehr Informationen von uns benötigt, um
zu wissen welchen Typ wir verwenden wollen:

```text
$ cargo build
   Compiling no_type_annotations v0.1.0 (file:///projects/no_type_annotations)
error[E0282]: type annotations needed
 --> src/main.rs:2:9
  |
2 |     let guess = "42".parse().expect("Keine Zahl!");
  |         ^^^^^ consider giving `guess` a type

error: aborting due to previous error

For more information about this error, try `rustc --explain E0282`.
error: could not compile `no_type_annotations`.

To learn more, run the command again with --verbose.
```

Für andere Datentypen wirst du andere Typ-Annotationen sehen.

### Skalare Typen

Ein *skalarer* Typ stellt einen einzelnen Wert dar. Rust hat vier primäre
skalare Typen: Ganze Zahlen, Fließkommazahlen, boolesche Werte (Wahrheitswerte)
und Zeichen. Du erkennst diese vielleicht aus anderen Programmiersprachen. Lass
uns darüber sprechen, wie sie in Rust funktionieren.

#### Ganzzahl-Typen

Eine *ganze Zahl* ist eine Zahl ohne Bruchteilkomponente. Wir verwendeten eine
ganze Zahl in Kapitel 2, den Typ `u32`. Diese Typdeklaration gibt an, dass der
Wert, dem sie zugeordnet ist, eine 32 Bit große ganze Zahl ohne Vorzeichen ist
(vorzeichenbehaftete Ganzzahl-Typen beginnen mit `i` anstatt `u`). Tabelle 3-1
zeigt die in Rust eingebauten Ganzzahl-Typen. Jede Variante in den Spalten
Vorzeichenbehaftet und Vorzeichenlos (zum Beispiel `i16`) kann benutzt werden,
um den Typ eines ganzzahligen Wertes zu deklarieren.

<span class="caption">Tabelle 3-1: Ganzzahlige Typen in Rust</span>

| Länge   | Vorzeichenbehaftet  | Vorzeichenlos |
|--------:|---------------------|---------------|
| 8 Bit   | `i8`                | `u8`          |
| 16 Bit  | `i16`               | `u16`         |
| 32 Bit  | `i32`               | `u32`         |
| 64 Bit  | `i64`               | `u64`         |
| 128 Bit | `i128`              | `u128`        |
| arch    | `isize`             | `usize`       |

Jede Variante kann entweder vorzeichenbehaftet oder vorzeichenlos sein und hat
eine explizite Größe. *Vorzeichenbehaftet* und *vorzeichenlos* bezieht sich
darauf, ob es möglich ist, dass die Zahl negativ oder positiv ist &ndash; in
anderen Worten, ob die Zahl ein Vorzeichen haben muss (vorzeichenbehaftet) oder
ob sie immer nur positiv sein wird und daher ohne Vorzeichen dargestellt werden
kann (vorzeichenlos). Es ist wie das Schreiben von Zahlen auf Papier: Wenn das
Vorzeichen eine Rolle spielt, wird die Zahl mit einem Plus- oder Minuszeichen
geschrieben; wenn man jedoch davon ausgehen kann, dass die Zahl positiv ist,
wird sie ohne Vorzeichen geschrieben. Vorzeichenbehaftete Zahlen werden unter
Verwendung der
[Zweierkomplementdarstellung](https://de.wikipedia.org/wiki/Zweierkomplement)
gespeichert

Jede vorzeichenbehaftete Variante kann Zahlen von -(2<sup>n - 1</sup>) bis
einschließlich 2<sup>n - 1</sup> - 1 speichern, wobei *n* die Anzahl an Bits
ist, die diese Variante benutzt. Ein `i8` kann also Zahlen von -(2<sup>7</sup>)
bis 2<sup>7</sup> - 1 speichern, was -128 bis 127 entspricht. Vorzeichenlose
Varianten können Zahlen von 0 bis 2<sup>n</sup> - 1 speichern, also kann ein
`u8` Zahlen von 0 bis 2<sup>8</sup> - 1 speichern, was 0 bis 255 entspricht.

Zusätzlich hängen die Typen `isize` und `usize` von der Art des Computers ab,
auf dem dein Programm läuft: 64 Bit wenn du dich auf einer 64-Bit-Architektur
befindest und 32 Bit auf einer 32-Bit-Architektur.

Du kannst ganzzahlige Literale in jeder der in Tabelle 3-2 gezeigten Formen
schreiben. Beachte, dass alle Zahlenliterale mit Ausnahme des Byte-Literals
einen Typ-Suffix erlauben, z.B. `57u8` und `_` als visuelles Trennzeichen, z.B.
`1_000`.

<span class="caption">Tabelle 3-2: Ganzzahl-Literale in Rust</span>

| Ganzahl-Literal  | Beispiel      |
|------------------|---------------|
| Dezimal          | `98_222`      |
| Hex              | `0xff`        |
| Oktal            | `0o77`        |
| Binär            | `0b1111_0000` |
| Byte (nur `u8`)  | `b'A'`        |

Woher weist du also, welche Ganzzahlart zu verwenden ist? Wenn du dir unsicher
bist, sind Rusts Standards im Allgemeinen eine gute Wahl und ganzzahlige Typen
sind standardmäßig `i32`: dieser Typ ist im Allgemeinen am schnellsten, selbst
auf 64-Bit-Systemen. Die primäre Situation in der du `isize` oder `usize`
benutzen würdest, ist die Indizierung einer Kollektionsart (collection).

> ##### Ganzzahlüberlauf
>
> Nehmen wir an, du hast eine Variable vom Typ `u8`, die Werte zwischen 0 und
> 255 annehmen kann. Wenn du versuchst, die Variable auf einen Wert außerhalb
> dieses Bereiches zu ändern, z.B. auf 256, tritt ein Ganzzahlüberlauf auf.
> Rust hat einige interessante Regeln, die dieses Verhalten betreffen. Wenn du
> im Fehlersuchmodus (debug mode) kompilierst, fügt Rust Prüfungen auf
> Ganzzahlüberläufe ein, was dazu führt, dass dein Programm zur Laufzeit
> *abbricht* (panic), falls dieses Verhalten auftritt. Rust verwendet den
> Begriff „panic“, wenn ein Programm durch einen Fehler abgebrochen wird; wir
> werden Programmabbrüche im Abschnitt [„Unbehebbare Fehler mit
> `panic!`“][unrecoverable-errors-with-panic] in Kapitel 9 näher betrachten.
>
> Wenn du mit dem Schalter `--release` im Freigabemodus (release mode)
> kompilierst, fügt Rust *keine* Prüfungen auf Ganzzahlüberläufe, die das
> Programm abbrechen, ein. Wenn ein Überlauf auftritt, führt Rust stattdessen
> einen *Zweier-Komplement-Umbruch* durch. Kurz gesagt, Werte die größer als
> der Maximalwert den der Typ enthalten kann sind, werden umgebrochen zum
> kleinsten Wert den der Typ enthalten kann. Im Falle eines `u8` wird 256 zu 0,
> 257 zu 1 und so weiter. Das Programm wird nicht abbrechen, aber die Variable
> wird wahrscheinlich einen anderen Wert annehmen, als du erwartest. Sich auf
> das Verhalten von Ganzzahlüberläufen zu verlassen wird als Fehler angesehen.
> Wenn du explizit umbrechen willst, kannst du den Typ [`Wrapping`][wrapping]
> aus der Standardbibliothek verwenden.

#### Fließkomma-Typen

Rust hat auch zwei primitive Typen für *Fließkommazahlen*, das sind Zahlen mit
Dezimalkomma. Die Fließkomma-Typen in Rust sind `f32` und `f64`, die 32 Bit
bzw. 64 Bit groß sind. Der Standardtyp ist `f64`, da er auf modernen CPUs
ungefähr die gleiche Geschwindigkeit wie `f32` hat, aber eine höhere Präzision
ermöglicht.

Hier ist ein Beispiel, das Fließkommazahlen in Aktion zeigt:

<span class="filename">Dateiname: src/main.rs</span>

```rust
fn main() {
    let x = 2.0; // f64

    let y: f32 = 3.0; // f32
}
```

Fließkommazahlen werden nach dem IEEE-754-Standard dargestellt. Der Typ `f32`
ist eine Fließkommazahl mit einfacher Genauigkeit und `f64` mit doppelter
Genauigkeit.

#### Numerische Operationen

Rust unterstützt grundlegende mathematische Operationen, die man bei allen
Zahlentypen erwartet: Addition, Subtraktion, Multiplikation, Division und
Restberechnung. Der folgende Code zeigt, wie du die einzelnen Typen in einer
`let`-Anweisung verwenden würdest:

<span class="filename">Dateiname: src/main.rs</span>

```rust
fn main() {
    // Addition
    let sum = 5 + 10;

    // Subtraktion
    let difference = 95.5 - 4.3;

    // Multiplikation
    let product = 4 * 30;

    // Division
    let quotient = 56.7 / 32.2;

    // Restberechnung
    let remainder = 43 % 5;
}
```

Jeder Ausdruck in diesen Anweisungen verwendet einen mathematischen Operator
und wird zu einem einzelnen Wert ausgewertet, der dann an eine Variable
gebunden wird. Anhang B enthält eine Liste aller Operatoren, die Rust anbietet.

#### Der boolesche Typ

Wie in den meisten anderen Programmiersprachen hat ein boolescher Typ in Rust
zwei mögliche Werte: `true` (wahr) und `false` (falsch). Boolesche Werte sind
ein Byte groß. In Rust wird der boolesche Typ mit `bool` spezifiziert. Zum
Beispiel:

<span class="filename">Dateiname: src/main.rs</span>

```rust
fn main() {
    let t = true;

    let f: bool = false; // mit expliziter Typ-Annotation
}
```

Hauptsächlich werden boolesche Werte in Bedingungen verwendet, z.B. im
`if`-Ausdruck. Wie `if`-Ausdrücke in Rust funktionieren werden wir im Abschnitt
[„Kontrollfluss“][control-flow] erläutern.

#### Der Zeichen-Typ

Bislang haben wir nur mit Zahlen gearbeitet, aber Rust unterstützt auch
Buchstaben. Rusts Typ `char` ist der primitivste alphabetische Typ der Sprache,
der folgende Code zeigt eine Möglichkeit, ihn zu verwenden. (Beachte, dass
`char`-Literale mit einfachen Anführungszeichen angegeben werden, im Gegensatz
zu Zeichenketten-Literalen, die doppelte Anführungszeichen verwenden.)

<span class="filename">Dateiname: src/main.rs</span>

```rust
fn main() {
    let c = 'z';
    let z = 'ℤ';
    let heart_eyed_cat = '😻';
}
```

Der Typ `char` von Rust ist vier Bytes groß und stellt einen Unicode-Skalarwert
dar, was bedeutet, dass er viel mehr als nur ASCII darstellen kann.
Akzentuierte Buchstaben, chinesische, japanische und koreanische Zeichen, Emoji
und Leerzeichen mit Null-Breite sind gültige `char`-Werte in Rust.
Unicode-Skalarwerte reichen von `U+0000` bis `U+D7FF` und von `U+E000` bis
einschließlich `U+10FFFF`. Ein „Zeichen“ ist jedoch nicht wirklich ein Konzept
in Unicode, deine menschliche Intuition dafür, was ein „Zeichen“ ist, stimmt
möglicherweise nicht mit dem überein, was ein `char` in Rust ist. Wir werden
dieses Thema in [„UTF-8-kodierten Text in Zeichenketten (strings)
ablegen“][strings] in Kapitel 8 im Detail diskutieren.

### Verbund-Typen

*Verbund-Typen* (compound types) können mehrere Werte zu einem Typ gruppieren.
Rust hat zwei primitive Verbund-Typen: Tupel (tuples) und Arrays (arrays).

#### Der Tupel-Typ

Ein Tupel ist eine allgemeine Möglichkeit, eine Reihe von Werten mit einer
Vielzahl von Typen zu einem Verbund-Typ zusammenzufassen. Tupel haben eine
feste Länge: Einmal deklariert, können sie weder wachsen noch schrumpfen.

Wir erzeugen ein Tupel, indem wir eine durch Kommata getrennte Liste von Werten
innerhalb von Klammern schreiben. Jede Position im Tupel hat einen Typ und die
Typen der verschiedenen Werte im Tupel müssen nicht gleich sein. In diesem
Beispiel haben wir optionale Typ-Annotationen angegeben:

<span class="filename">Dateiname: src/main.rs</span>

```rust
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```

Die Variable `tup` bindet das gesamte Tupel, da ein Tupel als ein einziges
Verbundelement betrachtet wird. Um die einzelnen Werte aus einem Tupel
herauszubekommen, können wir den Musterabgleich verwenden, um einen Tupelwert
zu destrukturieren, etwa so:

<span class="filename">Dateiname: src/main.rs</span>

```rust
fn main() {
    let tup = (500, 6.4, 1);

    let (x, y, z) = tup;

    println!("Der Wert von y ist: {}", y);
}
```

Dieses Programm erzeugt zunächst ein Tupel und bindet es an die Variable `tup`.
Dann benutzt es ein Muster mit `let`, um `tup` zu nehmen und in drei separate
Variablen `x`, `y` und `z` umzuwandeln. Dies nennt man *destrukturieren*
(destructuring), weil es das einzelne Tupel in drei Teile zerlegt. Schließlich
gibt das Programm den Wert von `y` aus, der `6.4` ist.

Zusätzlich zum Destrukturieren durch Musterabgleich können wir direkt auf ein
Tupelelement zugreifen, indem wir einen Punkt (`.`) gefolgt vom Index des
Wertes, auf den wir zugreifen wollen, verwenden. Zum Beispiel:

<span class="filename">Dateiname: src/main.rs</span>

```rust
fn main() {
    let x: (i32, f64, u8) = (500, 6.4, 1);

    let five_hundred = x.0;

    let six_point_four = x.1;

    let one = x.2;
}
```

Dieses Programm erzeugt ein Tupel `x` und erstellt dann neue Variablen für
jedes Element, indem es ihre jeweiligen Indizes verwendet. Wie bei den meisten
Programmiersprachen ist der erste Index in einem Tupel 0.

#### Der Array-Typ

Eine andere Möglichkeit, eine Kollektion mit mehreren Werten zu haben, ist mit
einem *Array*. Im Gegensatz zu einem Tupel muss jedes Element eines Arrays den
gleichen Typ haben. Arrays in Rust unterscheiden sich von Arrays in einigen
anderen Sprachen, weil Arrays in Rust eine feste Länge haben, wie Tupel.

In Rust werden die in ein Array eingehenden Werte als kommagetrennte Liste in
eckige Klammern geschrieben:

<span class="filename">Dateiname: src/main.rs</span>

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];
}
```

Arrays sind nützlich, wenn du deine Daten eher auf dem Stapelspeicher als im
dynamischen Speicher abgelegt haben möchtest (auf den Stapelspeicher und den
dynamischen Speicher gehen wir in Kapitel 4 näher ein) oder wenn du
sicherstellen willst, dass du immer eine feste Anzahl von Elementen hast. Ein
Array ist jedoch nicht so flexibel wie der Vektortyp. Ein Vektor ist ein
ähnlicher Kollektionstyp, der von der Standardbibliothek zur Verfügung gestellt
wird und der in seiner Größe wachsen oder schrumpfen kann. Wenn du dir nicht
sicher bist, ob du ein Array oder einen Vektor verwenden sollst, solltest du
wahrscheinlich einen Vektor verwenden. In Kapitel 8 werden Vektoren
ausführlicher besprochen.

Ein Beispiel dafür, wann du ein Array statt eines Vektors verwenden möchtest,
ist in einem Programm, das die Namen der Monate des Jahres kennen muss. Es ist
sehr unwahrscheinlich, dass ein solches Programm Monate hinzufügen oder
entfernen muss, sodass du ein Array verwenden kannst, weil du weißt, dass es
immer 12 Elemente enthalten wird:

```rust
let months = ["Januar", "Februar", "März", "April", "Mai", "Juni", "Juli",
              "August", "September", "Oktober", "November", "Dezember"];
```

Du würdest den Typ eines Arrays mit eckigen Klammern schreiben und innerhalb
der Klammern den Typ jedes Elements, ein Semikolon und dann die Anzahl der
Elemente im Array angeben, etwa so:

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
```

Hier ist `i32` der Typ aller Elemente. Nach dem Semikolon gibt die Zahl `5` an,
dass das Array fünf Elemente enthält.

Das Angeben eines Array-Typs auf diese Weise ähnelt einer alternativen Syntax
für die Initialisierung eines Arrays: Wenn du ein Array erstellen möchtest, das
für jedes Element den gleichen Wert enthält, kannst du den Anfangswert, gefolgt
von einem Semikolon und dann die Länge des Arrays in eckigen Klammern angeben,
wie hier gezeigt:

```rust
let a = [3; 5];
```

Das Array mit dem Namen `a` wird `5` Elemente enthalten, die alle anfänglich
auf den Wert `3` gesetzt werden. Dies ist dasselbe wie das Schreiben von
`let a = [3, 3, 3, 3, 3];`, aber in einer prägnanteren Weise.

##### Zugriff auf Array-Elemente

Ein Array ist ein einzelnes Stück Speicher, das auf den Stapelspeicher gelegt
wird. Du kannst auf Elemente eines Arrays mit Hilfe der Indizierung wie folgt
zugreifen:

<span class="filename">Dateiname: src/main.rs</span>

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];

    let first = a[0];
    let second = a[1];
}
```

In diesem Beispiel erhält die Variable mit dem Namen `first` den Wert `1`, weil
das der Wert am Index `[0]` im Array ist. Die Variable mit dem Namen `second`
wird den Wert `2` vom Index `[1]` im Array erhalten.

##### Ungültiger Array-Element-Zugriff

Was passiert, wenn du versuchst, auf ein Element eines Arrays zuzugreifen, das
sich hinter dem Ende des Arrays befindet? Angenommen, du änderst das Beispiel
in den folgenden Code, der sich kompilieren lässt, aber mit einem Fehler
abbricht, wenn er ausgeführt wird:

<span class="filename">Dateiname: src/main.rs</span>

```rust,ignore,panics
fn main() {
    let a = [1, 2, 3, 4, 5];
    let index = 10;

    let element = a[index];

    println!("Der Wert von element ist: {}", element);
}
```

Die Ausführung dieses Codes mit `cargo run` ergibt folgendes Ergebnis:

```text
$ cargo run
   Compiling arrays v0.1.0 (file:///projects/arrays)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/arrays`
thread 'main' panicked at 'index out of bounds: the len is 5 but the index is 10', src/main.rs:5:19
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace.
```

Das Kompilieren ergab keinen Fehler, aber das Programm führte zu einem
*Laufzeitfehler* und wurde nicht erfolgreich beendet. Wenn du versuchst, auf
ein Element über die Indizierung zuzugreifen, wird Rust prüfen, ob der von dir
angegebene Index kleiner als die Array-Länge ist. Wenn der Index größer oder
gleich der Array-Länge ist, wird Rust das Programm abbrechen.

Dies ist das erste Beispiel für die Umsetzung der Sicherheitsprinzipien von
Rust. In vielen Low-Level-Sprachen wird diese Art der Überprüfung nicht
durchgeführt und wenn du einen falschen Index angibst, kann auf ungültigen
Speicher zugegriffen werden. Rust schützt dich vor dieser Art von Fehlern,
indem es das Programm sofort beendet, anstatt den Speicherzugriff zuzulassen
und fortzusetzen. In Kapitel 9 wird mehr über die Fehlerbehandlung von Rust
gesprochen.

[comparing-the-guess-to-the-secret-number]:
ch02-00-guessing-game-tutorial.html#comparing-the-guess-to-the-secret-number
[control-flow]: ch03-05-control-flow.html#control-flow
[strings]: ch08-02-strings.html#storing-utf-8-encoded-text-with-strings
[unrecoverable-errors-with-panic]: ch09-01-unrecoverable-errors-with-panic.html
[wrapping]: https://doc.rust-lang.org/std/num/struct.Wrapping.html