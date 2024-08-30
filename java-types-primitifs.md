## Tailles en octets

![image](https://github.com/user-attachments/assets/3835851b-9efb-4c39-a4de-f8ab02b1e09c)


## Caractère (type `char`)

> [!NOTE]
> Java représente les caractères en UTF-16, c'est à dire sur deux octets (implémentation suivant le standard unicode de l'époque). 
>
> Lectures : \
> [What are Unicode, UTF-8, and UTF-16 ?](https://stackoverflow.com/questions/2241348/what-are-unicode-utf-8-and-utf-16) (Stackoverflow) \
> [What other script/character encodings exist other than the UTF variety ?](https://www.quora.com/What-other-script-character-encodings-exist-other-than-the-UTF-variety-UTF-8-UTF-16-and-UTF-32-all-have-drawbacks-Is-there-anything-else-inspiring-out-there-for-creative-encoding-ideas) (Quora)
>
> Si on travaille sur des caractères unicode qui dépasser un encodage sur deux octets, privilégier les méthodes sur les codepoints plutôt que les `char`.
> Cf. https://blog.jytou.fr/2021/04/13/never-use-char-in-java (paragraphe "Java code points instead of char").
>
> Exemple :
> ```java
> Integer[] integers = "𝄞".codePoints().boxed().toArray(Integer[]::new);
> System.out.println(Arrays.toString(integers));  // [119070], code point 0x1D11E
> ```


### Notation

```java
char a       = 'A';
char a       = 65;           // UTF-16 codepoint 
char a       = '\u0041'      // notation hexadecimale du codepoint 65 sur deux octets (unicode escape sequence) 
```

### Conversion...

#### ... en tableau d'octets

Exemple sur un caractère Unicode composé de deux codepoints (un codepoint est codé sur 2 octets) : \
`byte[] arr = "𝄞".getBytes(StandardCharsets.UTF_8);   // [-16, -99, -124, -98]`

#### ... en représentation binaire

Depuis :
```java
int parseInt = Integer.parseInt(your_binary_string, 2);
char c = (char)parseInt;
```

Vers : `String s = Integer.toBinaryString('A');   // -> "1000001" (codepoint 65)`

#### ... en unicode (java notation)

```java
char c = ...;
String.format ("\\u%04x", (int)c);
```



## Types numériques (byte, short, int, long, float, double) 

### Notatation

```java
int entier          = 1;
float decimal       = 1.1f;  // f ou F
long entierLong     = 1L;    // l (facilement confondu avec 1) ou L (recommandé)
double decimalLong  = 1.1d;   // d ou D facultatif si partie décimale présente

// Autres notations 
int entier   = 0744;         // notation octale                   = 484 en décimal
int entier   = 0x1E4;        // notation hexadécimale (0x ou 0X)  = 484 en décimal
int entier   = 0b111100100;  // notation binaire (0b ou 0B)       = 484 en décimal
```

### Conversions entre les types numériques

#### Conversions implicites

La règle est que passer d'un type plus "petit" à plus "grand" est automatique. \
Dans quelques cas, il y a un risque de perte de précision (flêches en pointillé).

![image](https://github.com/user-attachments/assets/868c143f-d678-46ad-92f4-cac0dc37fa98)

Exemple

```java
int n = 123456789;
float f = n; // f vaut 1.2345679E8 et non 1.23456789E8 !
```

> [!NOTE]
> Quand deux valeurs sont combinées par un opérateur binaire, les deux opérandes sont converties dans un type commun selon la priorité suivante :
> 1. si une opérande est un `double`, l'autre est converti en `double`
> 1. sinon même règle si un `float` est présent
> 1. sinon même règle si un `long` est présent
> 1. sinon même règle si un `int` est présent

> [!WARNING]
> Smaller data types, namely, byte , short , and char , are first promoted to int any time
> they’re used with a Java binary arithmetic operator with a variable (as opposed to a
> value), even if neither of the operands is int.

#### Conversions explicites

Pour forcer une conversion, il faut "caster". \
Ne pas oublier qu'une perte de précision est à prévoir.

Exemple : 

```java
short ss = 5;
char aa = (char)ss;
```

### Conversion...

#### ... en représentation binaire

Depuis :
```java
int i = Integer.parseInt("1001", 2);   // 9
```

Vers :
```java
// int
String str = Integer.toBinaryString(65);   // "1000001"

// long
String str = Long.toBinaryString(65L);     // "1000001"

// byte, https://stackoverflow.com/questions/12310017/how-to-convert-a-byte-to-its-binary-string-representation
byte b1 = (byte) 129;     // -127
String s1 = String.format("%8s", Integer.toBinaryString(b1 & 0xFF)).replace(' ', '0');
System.out.println(s1); // 10000001

// short
short b1 = (short) (Short.MAX_VALUE + 1);     // 32768 == -32768
String s1 = String.format("%16s", Integer.toBinaryString(b1 & 0xFFFF)).replace(' ', '0');
System.out.println(s1); // 1000000000000000
```
