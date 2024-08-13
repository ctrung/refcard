
dev.java tutorial : https://dev.java/learn/api/streams

## Concepts

- Map-Filter-Reduce algorithm
- Declarative and functional
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

- `collect()`

## Avancé

### `flatMap()`

- Equivalent à deux boucles imbriquées en impératif
- Map chaque élément à un stream et applanit le tout

### `mapMulti(BiConsumer<? super T,? super Consumer<R>> mapper)` (Java 16)

https://docs.oracle.com/en/java/javase/22/docs/api/java.base/java/util/stream/Stream.html#mapMulti(java.util.function.BiConsumer)

Exemple d'utilisation : remplacer une validation lors d'un `flatMap` \
https://dev.java/learn/api/streams/intermediate-operation/#flatmap-and-mapmulti

## Fonctions

Fonctions courantes du package `java.util.function` utilisées dans l'API des streams : 
- Consumer<T> : utilise un objet mais ne renvoit rien
- Function<T,R> : transforme un objet en un autre
- Predicate<T> : teste un objet (renvoie un booléen)
- Bi<XXX> : même chose que `Consumer`, `Function` et `Predicate` mais qui prend deux objets en entrée au lieu d'un seul
- Supplier<T> : fournit un objet
