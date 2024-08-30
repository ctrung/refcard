## Valeurs temporelles

### LocalDate

```java
LocalDate ld = LocalDate.now();
ld = ld.plusDays(1);
ld = ld.plus(1L, ChronoUnit.DAYS);
```

### LocalTime

```java
LocalTime lt = LocalTime.now();
```

### LocalDateTime

```java
LocalDateTime ldt = LocalDateTime.now();
```

### ZonedDateTime

```java
ZonedDateTime zdt  ZonedDateTime.now();
```

## Unités temporelles

Interface ne possédant qu'une seule implémentation qui est `ChronoUnit`.

### Ecart entre deux valeurs temporelles

```java
var one = LocalTime.of(5, 15);
var two = LocalTime.of(6, 30);
var date = LocalDate.of(2016, 1, 20);
System.out.println(ChronoUnit.HOURS.between(one, two));     // 1 heure
System.out.println(ChronoUnit.MINUTES.between(one, two));   // 75 mins
System.out.println(ChronoUnit.MINUTES.between(one, date));  // DateTimeException
```

### Tronquer certains champs

LocalTime time = LocalTime.of(3,12,45);
System.out.println(time);        // 03:12:45

LocalTime truncated = time.truncatedTo(ChronoUnit.MINUTES);
System.out.println(truncated);   // 03:12

## Quantités de temps

### Period

Exprimée en année, mois, semaines, jours, les périodes s'utilisent principalement avec les valeurs temporelles telles que LocalDate, LocalDateTime et ZonedDateTime.

```java
var annually = Period.ofYears(1);
var quarterly = Period.ofMonths(3);
var everyThreeWeeks = Period.ofWeeks(3);
var everyOtherDay = Period.ofDays(2);
var everyYearAndAWeek = Period.of(1, 0, 7);         // 1 année et 7 jour
```

### Duration

Exprimée en jours, heures, minutes, secondes, les durations s'utilisent principalement avec les valeurs temporelles telles que LocalTime, LocalDateTime et ZonedDateTime.

```java
var daily = Duration.ofDays(1);                 // PT24H
var hourly = Duration.ofHours(1);               // PT1H
var everyMinute = Duration.ofMinutes(1);        // PT1M
var everyTenSeconds = Duration.ofSeconds(10);   // PT10S
var everyMilli = Duration.ofMillis(1);          // PT0.00.1S
var everyNano = Duration.ofNanos(1);            // PT0.000000001S

var daily2 = Duration.of(1, ChronoUnit.DAYS);   // PT24H
// etc...
```

## Moment dans le temps

Modélisée par la classe `Instant`.

Un moment est fabriqué par une des méthode factory de la classe `Instant` ou à travers une instance de `ZonedDateTime`.

```java
var date = LocalDate.of(2022, 5, 25);
var time = LocalTime.of(11, 55, 00);
var zone = ZoneId.of("US/Eastern");
var zonedDateTime = ZonedDateTime.of(date, time, zone);
var instant = zonedDateTime.toInstant(); 
System.out.println(zonedDateTime);  // 2022–05–25T11:55–04:00[US/Eastern]
System.out.println(instant);        // 202–05–25T15:55:00Z
```

> [!NOTE]
> Un moment est plus universel qu'un `LocalDateTime` ou un `ZonedDateTime`. \
> Cf. https://stackoverflow.com/questions/32437550/whats-the-difference-between-instant-and-localdatetime

### Chronométrer

```java
var now = Instant.now();
// do something time consuming
var later = Instant.now();

var duration = Duration.between(now, later);
System.out.println(duration.toMillis()); // Returns number milliseconds
```
