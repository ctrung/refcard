## Concepts

### Types génériques (classes)
Un type générique est une classe qui est paramétrable par des types (type parameters).
```java
class Box<T> { ... }

Box<String> b = new Box<>();
```

### Méthodes génériques
Une méthode générique introduit ses propres paramètres de types locaux.

```java
public static <K, V> boolean compare(Pair<K, V> p1, Pair<K, V> p2) {
  ...
}
boolean same = Util.<Integer, String>compare(p1, p2);
boolean same = Util.compare(p1, p2);                    # inférence de type
```

### Bounded type parameters

```java
public <U extends Number> void inspect(U u)
public <T extends Comparable<T>> int countGreaterThan(T[] anArray, T elem) {


Class A { /* ... */ }
interface B { /* ... */ }
interface C { /* ... */ }
class D <T extends A & B & C> { /* ... */ }     # les classes apparaissent obligatoirement avant les interfaces
```
### Wildcards

Résoudre les erreurs de compilation `capture of` avec les wildcards : https://dev.java/learn/generics/wildcards#wildcard-capture-and-helper-methods

Guide sur l'usage des wildcards lower et upper : https://dev.java/learn/generics/wildcards#guidelines-for-wildcard-use

#### Upper bounded wildcards (extends)
Exemple : `void process(List<? extends Foo> list)`

#### Unbounded wildcards (?)
Exemple : `void printList(List<?> list)`

#### Lower Bounded Wildcards (super)
Exemple : `void addNumbers(List<? super Integer> list)`

## Notions avancées

### Type erasure
En Java, les generics ne sont implémentés et vérifiés qu'à la compilation. \
A l'exécution, la notion de generics n'existe plus au détriment des raw types.

Retour et feedback de Brian Goetze sur le type erasure en 2020 : https://openjdk.org/projects/valhalla/design-notes/in-defense-of-erasure

### Bridge methods
https://dev.java/learn/generics/type-erasure/#bridge-methods

### Types non-réifiés

La réification est la possibilité pour un type d'avoir accès à toutes ses informations, ce qui n'est pas le cas des generics à cause du type erasure.

*TLDR;* En java, les generics (non unbounded) ne sont pas réifiés, à l'opposé des tableaux ou des types primitifs par exemple.  
https://dev.java/learn/generics/type-erasure/#non-reifiable-types)

### Heap pollution

Variables mal typées provoquant un `ClassCastException` au runtime.

Exemple 1 (utilisation de rawtypes)
```java
List<String> list = new ArrayList<String>();
List rawList = list;
rawList.add(8); // heap pollution
for (String str : list) { // ClassCastException at runtime
   System.out.println(str);
}
```

Exemple 2 (utilisation de generic varargs)
```java
public static void faultyMethod(List<String>... l) {
  Object[] objectArray = l;     // Valid
  objectArray[0] = Arrays.asList(42);
  String s = l[0].get(0);       // ClassCastException thrown here
}
```

## Flag `-Xlint:unchecked`, annotation `@SuppressWarnings("unchecked")`

Par défaut, le compilateur affiche un avertissement quand un **raw type** est présent dans le code. \
Pour savoir où, utiliser `-Xlint:unchecked`. \
Pour désactiver complètement ce contrôle, utiliser `-Xlint:-unchecked`.

Pour désactiver une occurence dans le code, utiliser l'annotation `@SuppressWarnings("unchecked")`.

