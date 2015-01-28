% Quelques concepts de programmation fonctionnelle appliqués à JavaScript
% David Sferruzza

# À propos de moi

- [\@d_sferruzza](https://twitter.com/d\_sferruzza)
- [github.com/dsferruzza](https://github.com/dsferruzza)
- développeur et responsable R&D chez [Escale](http://www.escaledigitale.com)
- écrit des projets perso et pro en Scala et en Haskell (notamment) depuis ~ 1 an
- ce talk est inspiré d'un article que j'ai écrit pour [24 jours de web](http://www.24joursdeweb.fr/2014/un-peu-de-programmation-fonctionnelle-en-javascript)


# Découverte de la programmation fonctionnelle

C'est un paradigme différent de la programmation *impérative* "classique", ce qui implique :

- nouveaux concepts
- ré-apprendre à résoudre des problèmes simples
- sortir de sa zone de confort

Intérêt ?

- prendre du recul sur la programmation
- réutiliser les concepts intéressants pour faire de **meilleurs programmes**


# La promesse

> Apprendre Haskell permet d'écrire de meilleurs programmes, même dans d'autres langages.

<div style="text-align:center">![](img/promise.gif)</div>

C'est ce qui s'est passé pour moi \\o/

## On va voir quelques concepts de FP qui peuvent s'appliquer à JavaScript, et comment ça améliore le code !


# Fonction impure

```javascript
function diviserParDeux(nombre) {
	var missile = lancerUnMissileNucleaire();
	fairePorterLeChapeauAuDrManhattan(missile);
	return nombre / 2;
}
```

Cette fonction a des **effets de bord** qu'on ne peut pas deviner en regardant sa valeur de retour.
Il faut donc faire constamment attention lorsqu'on la manipule !

<div style="text-align:center"><img src="img/fail.gif" alt="" width="225"></div>


# Fonction pure

```javascript
function ajouterCinq(nombre) {
	return nombre + 5;
}
```

Cette fonction n'a pas d'effets de bord. Elle renvoie **toujours** le même résultat lorsqu'on l'appelle avec les mêmes arguments.

```javascript
ajouterCinq(-4) == ajouterCinq(-4)
```

<div style="text-align:center"><img src="img/excellent.gif" alt="" width="420"></div>


# Transparence référentielle

> Le résultat du programme ne change pas si on remplace une expression par une expression de valeur équivalente.

Utiliser des fonctions pures permet de **résonner** sur son programme comme sur une équation.

<div style="text-align:center"><img src="img/mind_blown.gif" alt="" width="488"></div>


# Recommandations

## Construire son programme avec un maximum de fonctions pures

La logique métier est fiable, sans surprise et aisément testable.

## Isoler les fonctions qui ont des effets de bord

On sait quelles fonctions ont des effets de bord, ce qui permet d’être prudent lorsqu’on les manipule.


# Fonction d'ordre supérieur

En JS, les fonctions sont des objets de première classe (*first-class citizen*).

Une fonction d’ordre supérieur est une fonction qui possède au moins l'une des propriétés suivantes :

- elle accepte au moins une autre fonction en paramètre
- elle retourne une fonction en résultat


# Exemple de fonction d'ordre supérieur

```javascript
function appliquerDeuxFois(f, x) {
	return f(f(x));
}
// ^ accepte une fonction en paramètre

function foisTrois(x) {
	return x * 3;
}

appliquerDeuxFois(foisTrois, 7); // 63
```


# Intérêt des fonctions d'ordre supérieur

- bien séparer les différentes tâches effectuées par nos fonctions
- écrire des fonctions composables, modulables, paramétrables

On va voir un exemple concret : les opérations sur les tableaux en JS.


# Situation

```javascript
var villes = [
	{ nom: "Nantes", dep: 44, mer: false },
	{ nom: "Dunkerque", dep: 59, mer: true },
	{ nom: "Paris", dep: 75, mer: false },
];
```

On veut obtenir une liste de chaines de type `ville (dep)`.

On va écrire une fonction `rendreVillesAffichables` telle que :

```javascript
rendreVillesAffichables(villes);
// --> ["Nantes (44)", "Dunkerque (59)",
//      "Paris (75)"]
```


# Solution impérative

```javascript
function rendreVillesAffichables(villes) {
	for (var i = 0; i < villes.length; i++) {
		villes[i] = villes[i].nom +
					" (" + villes[i].dep + ")";
	}
	return villes;
}
```

On mélange 2 comportements :

- parcourir le tableau
- transformer les données


# Array.map

```javascript
function rendreVillesAffichables(villes) {
	function transformation(ville) {
		return ville.nom +
				" (" + ville.dep + ")";
	}
	return villes.map(transformation);
}
```

- `Array.map` gère le parcours du tableau \\o/
- on gère la transformation


# Array.filter

```javascript
villes.filter(function(item) {
	// Si la ville est près de la mer,
	// on renvoie true, sinon false
	return item.mer;
});
// --> [ { nom: "Dunkerque", dep: 59,
//			mer: true } 
```


# Chainons !

```javascript
villes.filter(function(item) {
	return !item.mer;
}).map(function(item) {
	return item.nom + " (" + item.dep + ")";
});
// --> ["Nantes (44)", "Paris (75)"]
```

*Rappel : `Array.map` et `Array.filter` sont des fonctions d'ordre supérieur.*


# Array.reduce

```javascript
var personnes = [
	{ nom: "Bruce", age: 30 },
	{ nom: "Tony", age: 35 },
	{ nom: "Peter", age: 26 },
];

personnes.reduce(function(acc, cur) {
	return acc + cur.age;
}, 0);
// --> 91
```


# Array.reduce

```javascript
function map(tableau, transformation) {
	return tableau.reduce(function(acc, cur) {
		acc.push(transformation(cur));
		return acc;
	}, []);
}

function filter(tableau, predicat) {
	return tableau.reduce(function(acc, cur) {
		if (predicat(cur)) acc.push(cur);
		return acc;
	}, []);
}
```


# Antisèche

Si vous avez un tableau et que vous voulez :

- appliquer une transformation sur chacune de ses cases (en conservant leur ordre/nombre) : **map**
- supprimer certaines cases (en conservant l’ordre des autres) : **filter**
- le parcourir pour construire une nouvelle structure de données : **reduce**


# Conclusion

- écrire du code qui fonctionne ne suffit pas
- utiliser des mécanismes qui facilitent le raisonnement :
	- transparence référentielle
	- fonctions d’ordre supérieur
	- *immuabilité*
	- *systèmes de types avancés*

La programmation fonctionnelle offre des belles manières d'écrire des programmes fiables et maintenables.


# Ressources

- [Learn You A Haskell For Great Good](http://learnyouahaskell.com/) : LE livre pour débuter Haskell ; il est très accessible ; disponible en papier ou en ligne ([traduction FR non officielle](http://lyah.haskell.fr/))
- [JavaScript Alongé](https://leanpub.com/javascript-allonge) : un livre avancé sur JavaScript ; il explique et met en place certains concepts que nous avons survolés ici ; disponible en version dématérialisée ou en ligne
- [Lo-Dash](https://lodash.com/) : une bibliothèque JavaScript qui propose notamment des fonctions d'ordre supérieur très intéressantes pour manipuler les collections
- [Bacon.js](https://baconjs.github.io/) : une bibliothèque JavaScript qui permet de faire de la programmation fonctionnelle [réactive](https://fr.wikipedia.org/wiki/Programmation_r%C3%A9active)

# Questions ?

<div style="text-align:center">![](img/question.gif)</div>

<div style="text-align:center">
Slides sur GitHub :

[dsferruzza/talk-programmation-fonctionnelle-en-js](http://github.com/dsferruzza/talk-programmation-fonctionnelle-en-js)
</div>
