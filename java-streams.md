
dev.java tutorial : https://dev.java/learn/api/streams

## Concepts

- Map-Filter-Reduce algorithm
- Declarative and functional
- Chainer les opérations sur les streams car ils ne peuvent être "consommés" qu'une seule fois (cf. l'erreur `stream has already been operated upon or closed`)
- 4 classes de streams : `Stream` pour les objets et `IntStream`, `LongStream` et `DoubleStream` pour **éviter** l'autoboxing de types primitifs
- Opérations intermédiaires : `map()`, `flatMap()`, `filter()`
- Opérations finales : `collect()`

## Avancé

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
