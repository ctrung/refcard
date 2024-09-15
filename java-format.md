## Affichage : méthode statique `System.out.format()`

```java
var pi = 3.14159265359;
System.out.format("[%f]",pi);       // [3.141593]
System.out.format("[%12.8f]",pi);   // [  3.14159265]
System.out.format("[%012f]",pi);    // [00003.141593]
System.out.format("[%12.2f]",pi);   // [        3.14]
System.out.format("[%-12.2f]",pi);  // [3.14        ]
System.out.format("[%.3f]",pi);     // [3.142]
```

## Dates

```java

// Formatage depuis les objets de l'api date

LocalDate date = LocalDate.of(2022, Month.OCTOBER, 20);
LocalTime time = LocalTime.of(11, 12, 34);
LocalDateTime dt = LocalDateTime.of(date, time);

System.out.println(date.format(DateTimeFormatter.ISO_LOCAL_DATE));
System.out.println(time.format(DateTimeFormatter.ISO_LOCAL_TIME));
System.out.println(dt.format(DateTimeFormatter.ISO_LOCAL_DATE_TIME));

// Personnaliser le format

var f = DateTimeFormatter.ofPattern("MMMM dd, yyyy 'at' hh:mm");
System.out.println(dt.format(f));

// Formater depuis un DateTimeFormatter est équivalent

var dateTime = LocalDateTime.of(2022, Month.OCTOBER, 20, 6, 15, 30);
var formatter = DateTimeFormatter.ofPattern("MM/dd/yyyy hh:mm:ss");

System.out.println(dateTime.format(formatter));   // 20/10/2022 06:15:30
System.out.println(formatter.format(dateTime));   // 20/10/2022 06:15:30

// Insérer du texte entre quote simple (')
// Doubler le quote pour l'échapper    ('') 
var g1 = DateTimeFormatter.ofPattern("MMMM dd', Party''s at' hh:mm");
System.out.println(dt.format(g1)); // October 20, Party's at 06:15

```

### Localization

D'autres méthodes sont disponibles pour formater en tenant compte de la locale : 
- pour les dates : `DateTimeFormatter.ofLocalizedDate(FormatStyle dateStyle)`
- pour l'heure : `DateTimeFormatter.ofLocalizedTime(FormatStyle timeStyle)`
- pour les dates heure :
  * DateTimeFormatter.ofLocalizedDateTime(FormatStyle dateStyle, FormatStyle timeStyle)
  * DateTimeFormatter.ofLocalizedDateTime(FormatStyle dateTimeStyle)
- pour changer la locale : `withLocale(Locale locale)`

Exemples :
```java
var italy = new Locale("it", "IT");
var dt = LocalDateTime.of(2022, Month.OCTOBER, 20, 15, 12, 34);

var dtf = DateTimeFormatter.ofLocalizedDate(SHORT);
System.out.println(dtf.withLocale(locale).format(dt));      // 20/10/22

dtf = DateTimeFormatter.ofLocalizedTime(SHORT);
System.out.println(dtf.withLocale(locale).format(dt));      // 15:12

dtf = DateTimeFormatter.ofLocalizedDateTime(SHORT, SHORT);
System.out.println(dtf.withLocale(locale).format(dt));      // 20/10/22, 15:12
```

## Nombres : classe `NumberFormat`

`interface NumberFormat` \
`class DecimalFormat implements NumberFormat`

```java
// locale fr, FR

double d = 1234.567;
NumberFormat f1 = new DecimalFormat("###,###,###.0");
System.out.println(f1.format(d)); // 1 234.6

NumberFormat f2 = new DecimalFormat("000,000,000.00000");
System.out.println(f2.format(d)); // 000 001 234.56700

NumberFormat f3 = new DecimalFormat("Votre solde €#,###,###.##");
System.out.println(f3.format(d)); // Votre solde €1 234.57
```

Caractères spéciaux de formatage :
- `#` : pas d'affichage si pas de chiffre à cette position
- `0` : afiichage d'un zéro si pas de chiffre à cette position

### Localization

NB : `getInstance()` retourne une instance avec la `Locale` du système par défaut.

- Générique :
  * `NumberFormat.getInstance()`
  * `NumberFormat.getInstance(Locale locale)`
- Identique à `getInstance`
  * `NumberFormat.getNumberInstance()`
  * `NumberFormat.getNumberInstance(Locale locale)`
- Formater les montants :
  * `NumberFormat.getCurrencyInstance()`
  * `NumberFormat.getCurrencyInstance(Locale locale)`
- Formater les pourcentages :
  * `NumberFormat.getPercentInstance()`
  * `NumberFormat.getPercentInstance(Locale locale)`
- Formater les nombres décimaux :
  * `NumberFormat.getIntegerInstance()`
  * `NumberFormat.getIntegerInstance(Locale locale)`
- Formater les nombres de manière compact :
  * `NumberFormat.getCompactNumberInstance()`
  * `NumberFormat.getCompactNumberInstance(Locale locale, NumberFormat.Style formatStyle)`

Exemples :

```java
var ca = NumberFormat.getInstance(Locale.CANADA_FRENCH);
System.out.println(ca.format(attendeesPerMonth)); // 266 666

double price = 48;
var myLocale = NumberFormat.getCurrencyInstance();
System.out.println(myLocale.format(price));       // 48,00 $ CA

int nb = 7_123_456;
System.out.println(NumberFormat.getCompactNumberInstance().format(nb));                                  // 7M
System.out.println(NumberFormat.getCompactNumberInstance(Locale.getDefault(), Style.SHORT).format(nb));  // 7M
System.out.println(NumberFormat.getCompactNumberInstance(Locale.getDefault(), Style.LONG).format(nb));   // 7 million
```

## String : méthode d'instance `string.formatted()`

```java
String mesg = "Hello %s, order %d is ready".formatted(name, orderId);
```

## String : méthode statique `String.format()`

```java
String mesg = String.format("Hello %s, order %d is ready", name, orderId);
```

Symboles : 
- `%s` : tout type, principalement valeurs de type `String`
- `%d` : valeurs entières comme `int` ou `long`
- `%f` : valeurs flottantes comme `float` ou `double`
- `%n` : insère un saut de ligne


Caractères spéciaux de formatage :
- %`x`  : longueur totale + padding gauche avec des espaces
- %`-x` : longueur totale + padding droit avec des espaces
- %`0x` : longueur totale + padding avec des zéros
- .`x`f : nombre de chiffre après la virgule
