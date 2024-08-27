## Tailles en octets

![image](https://github.com/user-attachments/assets/3835851b-9efb-4c39-a4de-f8ab02b1e09c)


## `char`

> [!NOTE]
> Héritage des années 90, Java représente les caractères en UTF-16 (ie. sur deux octets).
>
> Lectures : \
> [What are Unicode, UTF-8, and UTF-16 ?](https://stackoverflow.com/questions/2241348/what-are-unicode-utf-8-and-utf-16) (Stackoverflow) \
> [What other script/character encodings exist other than the UTF variety ?](https://www.quora.com/What-other-script-character-encodings-exist-other-than-the-UTF-variety-UTF-8-UTF-16-and-UTF-32-all-have-drawbacks-Is-there-anything-else-inspiring-out-there-for-creative-encoding-ideas) (Quora)


### Notation

```java
char a       = 'A';
char a       = 65;           // UTF-16 codepoint 
char a       = '\u0041'      // notation hexadecimale du codepoint 65 sur deux octets (unicode escape sequence) 
```

### API

#### Convertir en tableau d'octets

Exemple sur un caractère Unicode composé de deux codepoints (un codepoint est codé sur 2 octets) : \
`byte[] arr = "𝄞".getBytes(StandardCharsets.UTF_8);   // [-16, -99, -124, -98]`

#### Convertir depuis une binary string

```java
int parseInt = Integer.parseInt(your_binary_string, 2);
char c = (char)parseInt;
```

#### Convertir vers une binary string

`String s = Integer.toBinaryString('A');   // -> "1000001" (codepoint 65)`

#### Travailler sur les caractères d'une String

En raison de la représentation interne des caractères Java en UTF-16, privilégier les méthodes sur les codepoints plutôt que les `char`.

Cf. https://blog.jytou.fr/2021/04/13/never-use-char-in-java (paragraphe "Java code points instead of char").

```java
Integer[] integers = "𝄞".codePoints().boxed().toArray(Integer[]::new);
System.out.println(Arrays.toString(integers));
```

## Numériques

### Ecriture

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

### Conversion automatique

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

### Conversion forcée

Pour forcer une conversion, il faut "caster". \
Ne pas oublier qu'une perte de précision est à prévoir.

Exemple : 

```java
short ss = 5;
char aa = (char)ss;
```

### API

#### Conversion depuis une binary string
```java
int i = Integer.parseInt("1001", 2);   // 9
```

#### Conversion vers une binary string

`int` -> binary string : `String str = Integer.toBinaryString(65);   // "1000001"`

`long` -> binary string : `String str = Long.toBinaryString(65L);     // "1000001"`

`byte` -> binary string : \
https://stackoverflow.com/questions/12310017/how-to-convert-a-byte-to-its-binary-string-representation
```java
byte b1 = (byte) 129;     // -127
String s1 = String.format("%8s", Integer.toBinaryString(b1 & 0xFF)).replace(' ', '0');
System.out.println(s1); // 10000001
```

`short` -> binary string :
```java
short b1 = (short) (Short.MAX_VALUE + 1);     // 32768 == -32768
String s1 = String.format("%16s", Integer.toBinaryString(b1 & 0xFFFF)).replace(' ', '0');
System.out.println(s1); // 1000000000000000
```
