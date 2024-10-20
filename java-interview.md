
## Closeable vs AutoCloseable

`Closeable` existe depuis 1.5 et concerne principalement les resources IO que l'on doit fermer après utilisation. \
`AutoCloseable` a été introduit en 1.7 pour le try-with-resources.

`Closeable` étends `AutoCloseable`.

NB : `ExecutorService` implémente `AutoCloseable` depuis Java 19 (appelle `shutdown()`). 

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

## Covariance

Définition Wikipedia : https://en.wikipedia.org/wiki/Covariance_and_contravariance_(computer_science) \
Question Stackoverflow : https://stackoverflow.com/questions/18666710/why-are-arrays-covariant-but-generics-are-invariant

*Traduction française de l'article Wikipedia* \
La variance définit la relation de sous typage des types complexes par rapport à la relation de sous typages de ses composants.

En Java :
* Les tableaux sont covariants, cad `String[]` est un sous type de `Object[]`.
* Les generics ne sont pas covariants, cad `List<String>` n'est pas un sous type de `List<Object>`.
* Une méthode surchargée peut définir un sous type comme type de retour, aussi appelé **type de retour covariant**.

> [!NOTE]
> Les points 1. et 2. sont liés à l'implémentation sous-jacente du langage. En Java les tableaux sont réifiés, c'est à dire que le type est connu au runtime, ce qui n'est pas le cas pour les generics. Pour des raisons de compatibilité, Java a  pris le parti d'effacer les types paramétrés au moment de la compilatipon (type erasure).
>
> Exemples : 
> ```java
> class Mammal {...}
> class Elephant extends Mammal { ... }
> // tableaux
> Elephant[] elephants = new Elephant[5];
> Mammal[] mammals = elephants;
> mammals[0] = new Mammal();    // java.lang.ArrayStoreException
> 
> // generics
> List<Dog> dogs = new List<Dog>();
> List<Animal> animals = dogs;  // Interdit car si ça l'était, les lignes suivantes seraient problématiques...
> animals.add(new Cat());       // Sans information sur le type sous-jacent, comment être sur que c'est autorisé ?
> ```

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

## Permgen space

https://stackoverflow.com/questions/27131165/what-is-the-difference-between-permgen-and-metaspace

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

`volatile` s'utilise dans les programmes concurrents pour assurer la visibilité des variables. 

C'est un mécanisme de synchronisation léger qui ne bloque pas les threads. 

Il est utilisé pour assurer la visibilité entre threads et se prémunir des optimisations de la JVM (cache CPU, non atomicité des opérations 64 bits, réordonnancement). En pratique, le mot clé `volatile` est suffisant si un seul thread écrit et les autres threads lisent la variable, sinon une synchronisation plus forte est nécessaire.

> [!NOTE]
> Une variable volatile a aussi un impact sur la visibilité des autres variable. \
> La JVM garantit que lorsqu'un *thread A* écrit dans une variable volatile, puis qu'un *thread B* lit dans cette même variable, l'état de toutes les autres variables avant l'écriture du *thread A* est mis à jour dans le *thread B*. 

See : 
- Java Concurrency in Practice (p.36, 37)
- [java-volatile sur baeldung.com ](https://www.baeldung.com/java-volatile)
- [JM Doudoux, accès concurrents](https://www.jmdoudoux.fr/java/dej/chap-acces_concurrents.htm#acces_concurrents-1)

## Weak and soft references

[Brian Goatz - IBM articles : Plugging memory leaks with weak references (Wayback machine 2012/03/03)](https://web.archive.org/web/20120303152804/http://www.ibm.com/developerworks/java/library/j-jtp11225/index.html#resources)

[Brian Goatz - IBM articles : Plugging memory leaks with soft references (Wayback machine 2012/03/03)](https://web.archive.org/web/20120303180318/http://www.ibm.com/developerworks/java/library/j-jtp01246/index.html)
