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
