---
author: admin
comments: true
date: 2013-03-04 07:51:14+00:00
layout: post
slug: angularjs-requirejs-seed
title: AngularJs - RequireJs seed
wordpress_id: 316
categories:
- Javascript
- Tuto
tags:
- angularjs
- javascript
---

Ayant commencé à utiliser AngularJs récemment, je dois dire que je suis largement conquis.
Outre la vision sympathique de surcharger le code Html et la facilité d’arriver rapidement à ce qu’on veut, l’organisation en modules réutilisables est très intéressante puisque propre et donc maintenable et surtout testable.
Inspiré par certaines vidéos sur [egghead.io](http://egghead.io/) (que je recommande vivement), j’ai essayé de pousser encore un peu plus loin le concept de modules indépendants en utilisant RequireJs pour gérer le chargement.

Accompagné par un serveur http basé sur express, voici un template d’un mix AngularJs RequireJs et serveur Express avec les tests adaptés sur Testacular.

<!-- more -->


## Présentation du template




### Architecture


[![angular-seed-architecture](http://maxlab.fr/blog/wp-content/uploads/2013/03/angular-seed-architecture2.jpg)](https://github.com/maxdow/angularjs-requirejs-seed)

L’ensemble des sources de l’application ( serveur + client ) est dans le répertoire _src_.

_app.js_ est un simple serveur http basé sur express qui pointe sur le répertoire public, le reste de l’application est coté client.


###  Chargement de l’application


_Index.html_ sert de base pour charger RequireJs. C’est la seule insertion de la balise script, tout le reste est géré par RequireJs.

    
    <script data-main="js/main.js" src="js/lib/Require.js"></script>


Pour plus d’infos sur comment utiliser et débuter un projet avec RequireJs, je vous renvoie vers 2 [excellents ](http://hugeen.wordpress.com/2013/01/03/comment-debuter-avec-Requirejs/)[tutos](http://www.ringabell.org/un-simple-guide-pour-debuter-avec-Requirejs/), simples et efficaces.

_Bien que je ne sois pas encore convaincu que RequireJs apporte réellement un plus au système déjà bien conçu de modules Angular par rapport à l’ajout de “complexité” dans la configuration, il permet néanmoins de garder une bonne organisation et laisse la possibilité d’utiliser [r.js](https://github.com/jrburke/r.js/ ) pour optimiser les sources._

Une fois l’index chargé, _main.js_ configure RequireJs et exécute la fonction d’initialisation qui se trouve dans app.js ( celui du dossier _public_ cette fois ci ).

C’est ici qu’on va activer Angularjs et charger les différents modules. Comme le chargement est différé, l’initialisation de l’application se fait à l’aide de la [commande manuelle bootstrap d’Angular](http://docs.angularjs.org/guide/bootstrap) ( qui remplace <html ng-app> )

On retrouvera aussi le routage de nos vues en fonction de l’url. D’ailleurs, pour l’exemple, il n’y a rien dans l’index, tout se passe dans la vue située dans _template/Main.html_. Elle sera chargée lorsque l’url pointera sur / .

Ca tombe bien, on y est !




### Vues et Controlleurs


La vue est chargée et s’affiche dans notre index grâce à la directive _ng-view_. Elle est gérée par le controleur _MainCtrl_ qui ne fait que donner une valeur à la variable world.

Dans la vue _Main.html_ on retrouvera différents exemples des “jouets” d’Angularjs à savoir : deux directives, un service et caché derrière, un filtre.




### Directives


Les deux directives d’exemples se trouvent dans le dossier _js/Directives/directives.js_ . Elle sont activées dans la vue Main soit par balise comme pour <mydirective></mydirective> soit comme un attribut avec _app-version_.
mydirective se contente seulement de placer du contenu renseigné dans un template alors que appversion va utiliser un service pour le générer.




### Services


Même logique que pour le reste, un dossier Services qui regroupent nos services. Ici il y en a qu’un qui nous retourne un numéro de version.




### Filtres


J’ai repris le même que dans l’exemple Angular-seed à savoir un filtre qui utilise le service de version pour remplacer une chaîne de caractère avec le numéro de version.
Au passage, l'initialisation est un peu différente que pour les directives ou les services, si vous avez un meilleur moyen de le faire, je suis preneur ;)




### Tests


Pour terminer, j’ai réécris quelques tests unitaires qu’on peut démarrer à l’aide des lanceurs situés à la racine du projet dans le dossier scripts.

Ces tests sont réalisés à partir de Testacular avec l’adaptateur Jasmine et RequireJs.

Comme c’est un peu plus complexe avec le chargement différé de RequireJs, ça peut être intéressant d’y jeter un oeuil.

Au passage, pour ceux qui auraient des erreurs du type “Cannot start Chrome
“, il ne faut pas oublier de définir le lien vers l’executable dans le PATH. Plus d’infos par ici ( [http://testacular.github.com/0.6.0/intro/troubleshooting.html](http://testacular.github.com/0.6.0/intro/troubleshooting.html) ). Pareil pour des tests avec PhantomJS.




## Améliorations


Ce Template est un exemple d’architecture d’application AngularJS / RequireJS mais ce n’est pas nécessairement la meilleur solution. Comme j’expliquais précédemment, AngularJs est déjà bien construit et ne nécessite pas forcément l’utilisation d’un système de module parallèle. De plus, cet exemple ne tient pas compte d’une éventuelle optimisation de code. Pour s’éviter tout problème, il faut déclarer explicitement les dépendances lors d’une injection pour conserver le nom de ses fonctions intact .

Par exemple :

    
    myController.$inject = ['$location', '$log'];





## Les sources


Vous pouvez cloner le projet qui se trouve sur github à l'adresse suivante :

[https://github.com/maxdow/angularjs-requirejs-seed](https://github.com/maxdow/angularjs-requirejs-seed)



N'hésiter pas à faire des commentaires ou proposer des améliorations .






