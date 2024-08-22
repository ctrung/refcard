## Conversion des types primitifs

### Rappel écriture des types primitifs

```java
int entier          = 1;
float decimal       = 1.1f;  // f ou F
long entierLong     = 1L;    // l (facilement confondu avec 1) ou L (recommandé)
double decimalLong  = 1.1;   // d ou D facultatif si partie décimale présente

// Autres notations 
int entier   = 0744;         // notation octale                   = 484 en décimal
int entier   = 0x1E4;        // notation hexadécimale (0x ou 0X)  = 484 en décimal
int entier   = 0b111100100;  // notation binaire (0b ou 0B)       = 484 en décimal

char a       = 'A';
char a       = '\u0041'      // notation hexadecimale du codepoint 65 sur deux octets (unicode escape sequence) 
```

### Automatique

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
> 1. sinon même règle avec `float`
> 1. sinon même règle avec `long`
> 1. sinon même règle avec `int`

### Forcée (cast explicite)



## Comparable vs Comparator

Les deux sont des interfaces.

[`Comparable`](https://docs.oracle.com/en/java/javase/22/docs/api/java.base/java/lang/Comparable.html)
- Interface à appliquer à une classe si ses instances sont comparables
- `null` n'étant une instance d'aucune classe, `e.compareTo(null)` doit lancer un NPE
- Méthode à implémenter : `compareTo(T o)`

[`Comparator`](https://docs.oracle.com/en/java/javase/22/docs/api/java.base/java/util/Comparator.html)
- Fonction en charge de comparer deux objets
- Possibilité de comparer des objets `null`
- Méthode à implémenter : `compare(T o1, T o2)`

## Iterator vs Iteratable

Les deux sont des interfaces.

[`Iterator`](https://docs.oracle.com/en/java/javase/22/docs/api/java.base/java/util/Iterator.html) :
- Depuis Java 1.2
- Amélioration de `Enumeration` (noms de méthodes plus courts et une méthode additionnelle `remove`) 
- A privilégier en place de `Enumeration`
- Design pattern `Iterator` : itérer sur une collection indépendamment de son implémentation

[`Iterable`](https://docs.oracle.com/en/java/javase/22/docs/api/java.base/java/lang/Iterable.html)
- Depuis Java 1.5
- Introduit avec la boucle améliorée, communémment appelée la for-each-loop

## Précédence des opérateurs

![image](https://github.com/user-attachments/assets/ebc25d8d-a9c4-4d20-afea-ffe182886cde)


## Volatile (mot clé)

```java
volatile boolean aleesp;
...
while(!asleep) {
  countSOmeSheeps();
}
```

`volatile` s'utilise dans les programmes concurrents pour assurer la visibilité des variables. \
C'est un mécanisme de synchronisation plus léger qu'une section critique puisqu'il ne bloque pas les threads. \
Il est rendu nécessaire pour se prémunir des effets de visibilité rencontrés lors des optimisations de la JVM (caching, non atomicité des ops 64 bits, reordering). \

> [!NOTE]
> Une variable volatile a aussi un impact sur la visibilité des autres variable. \
> La JVM garantit que lorsqu'un *thread A* écrit dans une variable volatile, puis qu'un *thread B* lit dans cette même variable, l'état de toutes les autres variables avant l'écriture du *thread A* est mis à jour dans le *thread B*. 

See : 
- Java Concurrency in Practice (p.36, 37)
- [java-volatile sur baeldung.com ](https://www.baeldung.com/java-volatile)
