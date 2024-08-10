
Tutorial mis à jour : https://docs.oracle.com/javase/tutorial/java/generics/index.html
Tutorial de 2005 quand Java 5 est sorti : https://docs.oracle.com/javase/tutorial/extra/generics/index.html

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

Dans le cas de l'utilisation de generic varargs, si l'on est assuré que la méthode ne produit pas de `ClassCastException` au runtime, on peut utiliser l'annotation `@SafeVarargs`.
```java
@SafeVarargs
public static void safeVarargsMethod(List<String>... l) {
  Object[] objectArray = l;     // Valid
  objectArray[0] = Arrays.asList("hello");
  String s = l[0].get(0);       // pas de ClassCastException ici
}
```
Il est aussi possible d'utiliser `@SuppressWarnings({"varargs"})`, mais les avertissements depuis les points d'appel à la méthode ne seront pas supprimés. 

### Intéropérabilité avec le legacy code

https://docs.oracle.com/javase/tutorial/extra/generics/morefun.html \
https://docs.oracle.com/javase/tutorial/extra/generics/convert.html

Voir l'exemple de la méthode `public static <T extends Object & Comparable<? super T>> T Collections.max(Collection<? extends T> coll)` !

## Restrictions

Voir la page https://dev.java/learn/generics/restrictions pour les explications

- Les generics ne supportent pas les types primitifs : usage de l'autoboxing pour contourner
- Pas d'instanciation (directe) d'une variable d'un type paramétré : contournement possible via la reflexion
- Pas de champs `static` avec un type paramétré
- Pas de instanceof sur les generics : les unbounded types sont autorisés cependant
- Pas de création de tableau de generics
- Pas d'héritage d'un généric à partir de la classe `Throwable`
- Pas de generic dans la clause `catch`
- Pas d'overloading de méthodes avec les generics (à cause du type erasure)  

## Flag `-Xlint:unchecked`, annotation `@SuppressWarnings("unchecked")`

Par défaut, le compilateur affiche un avertissement quand un **raw type** est présent dans le code. \
Pour savoir où, utiliser `-Xlint:unchecked`. \
Pour désactiver complètement ce contrôle, utiliser `-Xlint:-unchecked`.

Pour désactiver une occurence dans le code, utiliser l'annotation `@SuppressWarnings("unchecked")`.

