https://developer.mozilla.org/fr/docs/Learn/Getting_started_with_the_web/JavaScript_basics

### Variables
```js
let   iceCream = "chocolat";   // var
const iceCream = "chocolat";   // val
```

### Types

- primitif : String, Number, Boolean
- référence : Object, Array
- spécial : Null, Undefined

### Propriétés
```js

// existence
log("prenom" in personne) // Affiche true ou false

// supprimer
delete personne.prenom
 
// parcourir les props d'un objet
for (let i in obj) {
  if (obj.hasOwnProperty(i)) {
    resultat += `${nomObjet}.${i} = ${obj[i]}\n`;
  }
}
```

### Fonctions
```js
function multiply(num1, num2) {
  let result = num1 * num2;
  return result;
}
```

### Objets
```js

// création : façon 1
let maVoiture = new Object();
maVoiture.fabricant = "Ford";
maVoiture.modele = "Mustang";
maVoiture.annee = 1969;

// création : façon 2 (initialisateur anonyme)
let maVoiture = {
  make: "Ford",
  model: "Mustang",
  year: 1969,
};

// création : façon 3 (constructeur, par convention mettre une majuscule à la fonction quand pour les constructeurs)
function Voiture(fabricant, modele, annee) {
  this.fabricant = fabricant;
  this.modele = modele;
  this.annee = annee;
}
let maVoiture = new Voiture("Eagle", "Talon TSi", 1993);
let voitureMorgan = new Voiture("Audi", "A3", 2005);

// création : façon 4 (Object.create + prototype)
let Animal = {
  type: "Invertébrés", // Valeur par défaut value of properties
  afficherType: function () {
    // Une méthode pour afficher le type Animal
    console.log(this.type);
  },
};
let animal1 = Object.create(Animal);
animal1.afficherType(); // affichera Invertébrés


maVoiture["fabricant"] = "Ford";
maVoiture["modèle"] = "Mustang";
maVoiture["année"] = 1969;
```

### Prototypes

https://developer.mozilla.org/fr/docs/Web/JavaScript/Guide/Working_with_objects#lh%C3%A9ritage

Objet dont on hérite des propriétés (attributs et méthodes). Non modifiable, fixé à la création d'un objet dans la propriété `prototype` du constructeur.


### Ajouter un élément au DOM
```js
const logoWikipedia = document.createElement("img");
logoWikipedia.setAttribute(
  "src",
  "https://upload.wikimedia.org/wikipedia/commons/6/63/Wikipedia-logo.png",
);
document.querySelector("h1").appendChild(logoWikipedia);
```

### Event listeners
```js
document.querySelector("html").addEventListener("click", function () {
  alert("Aïe, arrêtez de cliquer !!");
});
```

### Stocker une variable entre deux sessions
```js
localStorage.setItem("nom", myName);
...
let storedName = localStorage.getItem("nom");
```
