
Tutorial de dev.java : https://dev.java/learn/api/streams \
Vieux tutorial Java 8 du site d'Oracle : https://docs.oracle.com/javase/tutorial/collections/streams/index.html

## Concepts

- Map-Filter-Reduce algorithm
- Declarative and functional
- Itération interne, parallélisable
- Chainer les opérations sur les streams au lieu de stocker le résultat dans une variable intermédiaire car ils ne sont "consommables" qu'une seule fois (cf. l'erreur `stream has already been operated upon or closed`)
- 4 classes de streams : `Stream` pour les objets et `IntStream`, `LongStream` et `DoubleStream` pour les types primitifs (plus performant car pas d'autoboxing)

## Interfaces fonctionnelles courantes utilisées par l'API des streams 

Fonctions courantes du package `java.util.function` à maîtriser pour apréhender l'API des streams : 
- Consumer<T> : utilise un objet mais ne renvoit rien
- Function<T,R> : transforme un objet en un autre
- IntFunction<R> : spécialisation de Function, très utile pour instancier un tableau (eg. `stream.toArray(String[]::new)`)
- Predicate<T> : teste un objet (renvoie un booléen)
- Bi<XXX> : même chose que `Consumer`, `Function` et `Predicate` mais qui prend deux objets en entrée au lieu d'un seul
- Supplier<T> : fournit un objet


## Comparateurs courants utilisés par l'API des streams

- `Comparator.comparing(Person::getLastName, String.CASE_INSENSITIVE_ORDER)`
- `Map.Entry.comparingByValue()`
- 

## Opérations intermédiaires

- `map()`
- `flatMap()`, `mapMulti()`
- `filter()`
- `dropWhile()`, `takeWhile()`
- `skip()`, `limit()`
- `distinct()`, `sorted()`

## Opérations finales

- `reduce()` ： bas niveau, à utiliser en dernier ressort car l'opération doit être associative
- `collect()` : 
- `min()`, `max()`, `count()`, etc...

### Réduction

Opération finale avec trois formes proposées par l'API :

#### `T reduce(T identity, BinaryOperator<T> accumulator)`
Forme simplifiée avec l'élément identité
```java
Integer sum = integers.reduce(0, (a, b) -> a+b);
Integer sum = integers.reduce(0, Integer::sum);
```

#### `Optional<T> reduce(BinaryOperator<T> accumulator)`
Forme simplifiée sans l'élément identité
```java
Optional<Integer> min = integers.reduce(Integer::min);
```

#### `<U> U reduce(U identity, BiFunction<U,? super T,U> accumulator, BinaryOperator<U> combiner)`
Forme générale
```java
Stream<String> strings = Stream.of("one", "two", "three", "four");

BinaryOperator<Integer> combiner = (length1, length2) -> length1 + length2;
Function<String, Integer> mapper = String::length;
BiFunction<Integer, String, Integer> accumulator =  (partialReduction, element) -> partialReduction + mapper.apply(element);

int result = strings.reduce(0, accumulator, combiner);
System.out.println("sum = " + result);
```

### Collection

Méthodes :
- `<R> R collect(Supplier<R> supplier, BiConsumer<R,? super T> accumulator, BiConsumer<R,R> combiner)`
- `<R,A> R collect(Collector<? super T,A,R> collector)`
- `toArray(String[]::new)`

#### L'API `Collector`

Plusieurs `Collector` "prêt à l'emploi" via la classe utilitaire `Collectors` :

- `Collectors.toList()` : collecte dans une `ArrayList`
- `Collectors.toUnmodifiableList()` : collecte dans une liste non mutable, éq. à `stream.toList()` à partir de Java 16
- `Collectors.toSet()` et `Collectors.toUnmodifiableSet()`
- `Collectors.toCollection(LinkedList::new)`
- `Collectors.counting()` : éq. à `stream.count()`
- `Collectors.joining()` : seulement supporté pour les streams de strings
- `Collectors.partitioningBy()` : collecte dans une `Map<Boolean, T>`
- `Collectors.groupingBy()` : collecte dans une `Map<R, T>` (plusieurs valeurs autorisées par clé)
- `Collectors.toMap()`, `Collectors.toConcurrentMap()` : collecte dans une `Map` (une seule valeur autorisée par clé)
- `Collectors.mapping()`, `Collectors.filtering()`, `Collectors.flatMapping()` : opération intermédiaire avant de déléguer à un deuxième downstream collector, utile en combinaison avec `Collectors.groupingBy()`
- `Collectors.maxBy()/minBy()`, `Collectors.summingInt()/summingLong()/summingDouble()`, `Collectors.averagingInt()/averagingLong()/averagingDouble()`
- `Collectors.teeing()` (Java 12) : pour utiliser deux downstream collectors et merger le resultat
- `Collectors.collectingAndThen()` : applique un finisher à un downstream collector

#### Downstream collector

Fonctionnalité permettant de créer des histogrammes en combinant les valeurs d'une map issu d'un premier `Collector` avec un deuxième `Collector`.

Exemple 1 : 

```java
Collection<String> strings =
        List.of("one", "two", "three", "four", "five", "six", "seven", "eight", "nine",
                "ten", "eleven", "twelve");

Map<Integer, Long> map =
    strings.stream()
           .collect(
               Collectors.groupingBy(
                   String::length, 
                   Collectors.counting()));
```

Exemple 2 : 

```java
Collection<String> strings =
        List.of("one", "two", "three", "four", "five", "six", "seven", "eight", "nine",
                "ten", "eleven", "twelve");

Map<Integer, String> map =
        strings.stream()
                .collect(
                        Collectors.groupingBy(
                                String::length,
                                Collectors.joining(", ")));
```

### max

Pour les streams d'objets, il faut fournir un `Comparator` pour définir l'algorithme de tri.

Exemple 1 : 
```java
Stream<String> strings = Stream.of("one", "two", "three", "four");
String longest =
     strings.max(Comparator.comparing(String::length))
            .orElseThrow();
System.out.println("longest = " + longest);
```

Exemple 2 : 
```java
Stream<Person> persons = ...
Person first = persons.min(Comparator.comparing(Person::getLastName, String.CASE_INSENSITIVE_ORDER))
                orElseThrow();
System.out.println("first = " + first);
```

## `Optional`

Les `Optional` sont stream fluently.

Exemple d'utilisation de `Stream.flatMap()` + `Optional.map()` pour filtrer les optionals vides : \
https://dev.java/learn/api/streams/optionals/#processing

## Bonnes pratiques

### Utiliser `mapMulti(BiConsumer<? super T,? super Consumer<R>> mapper)` au lieu de `flatMap()` pour valider et filtrer (Java 16)

https://dev.java/learn/api/streams/intermediate-operation/#flatmap-and-mapmulti

Avant Java 16, avec `flatMap()` :
```java
Function<String, Stream<Integer>> flatParser = s -> {
    try {
        return Stream.of(Integer.parseInt(s));
    } catch (NumberFormatException e) {
    }
    return Stream.empty();
};

List<String> strings = List.of("1", " ", "2", "3 ", "", "3");
List<Integer> ints = 
    strings.stream()
           .flatMap(flatParser)
           .collect(Collectors.toList());
System.out.println("ints = " + ints);
```

Depuis Java 16, avec `mapMulti()` :
```java
List<Integer> ints =
        strings.stream()
               .<Integer>mapMulti((string, consumer) -> {
                    try {
                        consumer.accept(Integer.parseInt(string));
                    } catch (NumberFormatException ignored) {
                    }
               })
               .collect(Collectors.toList());
System.out.println("ints = " + ints);
```

Avec `mapMulti()`, il y a un gain de performance car on évite de créer un `Stream` pour chaque élément.

### Utiliser `orElseThrow()` au lieu de `get()` (Java 10)

Depuis Java 10, une meilleure alternative est `orElseThrow()` au lieu de `get()` pour rappeler qu'une exception est lancée s'il n'y a pas de valeur dans l'optional.

### Utiliser `toList()` au lieu de `collect(Collectors.toList())` (Java 16)

Alternative plus courte.

> [!NOTE]
> Il y a une subtilité : `toList()` retourne une collection immuable tandis que `collect(Collectors.toList())` utilise une `ArrayList`.

## Avancé

### Débugger

Utilisation de la méthode `peek` pour regarder au lieu de consommer. 

```java
List<String> strings = List.of("one", "two", "three", "four");
List<String> result =
        strings.stream()
                .peek(s -> System.out.println("Starting with = " + s))
                .filter(s -> s.startsWith("t"))
                .peek(s -> System.out.println("Filtered = " + s))
                .map(String::toUpperCase)
                .peek(s -> System.out.println("Mapped = " + s))
                .collect(Collectors.toList());
System.out.println("result = " + result);
```

Output :
```
Starting with = one
Starting with = two
Filtered = two
Mapped = TWO
Starting with = three
Filtered = three
Mapped = THREE
Starting with = four
result = [TWO, THREE]
```


### `flatMap()`

- Equivalent à deux boucles imbriquées en impératif
- Map chaque élément à un stream et applanit le tout

### Parallélisation

https://stackoverflow.com/questions/24603186/why-does-collection-parallelstream-exist-when-stream-parallel-does-the-sa


