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
