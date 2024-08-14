
dev.java website tutorial : https://dev.java/learn/api/streams
Old tutorial from Java 8 : https://docs.oracle.com/javase/tutorial/collections/streams/index.html

## Concepts

- Map-Filter-Reduce algorithm
- Declarative and functional
- Itération interne, parallélisable
- Chainer les opérations sur les streams au lieu de stocker le résultat dans une variable intermédiaire car ils ne sont "consommables" qu'une seule fois (cf. l'erreur `stream has already been operated upon or closed`)
- 4 classes de streams : `Stream` pour les objets et `IntStream`, `LongStream` et `DoubleStream` pour les types primitifs (plus performant car pas d'autoboxing)

## Opérations intermédiaires

- `map()`
- `flatMap()`, `mapMulti()`
- `filter()`
- `dropWhile()`, `takeWhile()`
- `skip()`, `limit()`
- `distinct()`, `sorted()`

## Opérations finales

- `reduce()`
- `collect()`

## Fonctions

Fonctions courantes du package `java.util.function` utilisées dans l'API des streams : 
- Consumer<T> : utilise un objet mais ne renvoit rien
- Function<T,R> : transforme un objet en un autre
- Predicate<T> : teste un objet (renvoie un booléen)
- Bi<XXX> : même chose que `Consumer`, `Function` et `Predicate` mais qui prend deux objets en entrée au lieu d'un seul
- Supplier<T> : fournit un objet

## Réduction

Opération finale avec trois formes proposées par l'API :

### `T reduce(T identity, BinaryOperator<T> accumulator)`
Forme simplifiée avec l'élément identité
```java
Integer sum = integers.reduce(0, (a, b) -> a+b);
Integer sum = integers.reduce(0, Integer::sum);
```

### `Optional<T> reduce(BinaryOperator<T> accumulator)`
Forme simplifiée sans l'élément identité
```java
Optional<Integer> min = integers.reduce(Integer::min);
```

### `<U> U reduce(U identity, BiFunction<U,? super T,U> accumulator, BinaryOperator<U> combiner)`
Forme générale
```java
Stream<String> strings = Stream.of("one", "two", "three", "four");

BinaryOperator<Integer> combiner = (length1, length2) -> length1 + length2;
Function<String, Integer> mapper = String::length;
BiFunction<Integer, String, Integer> accumulator =  (partialReduction, element) -> partialReduction + mapper.apply(element);

int result = strings.reduce(0, accumulator, combiner);
System.out.println("sum = " + result);
```

## Bonne pratiques

### Utiliser `mapMulti(BiConsumer<? super T,? super Consumer<R>> mapper)` au lieu de `flatMap()` lors des validations (Java 16)

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


