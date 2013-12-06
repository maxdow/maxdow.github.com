---
author: admin
comments: true
date: 2012-05-10 10:25:24+00:00
layout: post
slug: javascript-part-1-bonne-pratiques
title: 'Javascript : Style et Bonnes pratiques'
wordpress_id: 212
categories:
- Javascript
- Tuto
---

Lorsque l’on débute dans un nouveau langage on ne maîtrise pas forcément ses spécificités techniques et sans recul on fait parfois quelques erreurs. Respecter certaines contraintes lorsque l’on code permet d’obtenir un programme maintenable et souvent éviter de perdre beaucoup de temps sur des bugs inutiles.<!-- more -->

Le Javascript est un langage qui permet de mettre en oeuvre des applications web de manière assez simple. L’absence de contraintes de typage et de gestion de la mémoire le rend accessible même pour quelqu’un ne possédant pas de grandes compétences en programmation.

Il est possible d’apprendre à programmer avec Javascript mais il est quand même important de respecter certaines règles (qui s'appliquent aussi très bien à d'autres langages).

Pour commencer cette série d’articles consacrée à javascript , voici quelque conseils de base.


## Guide de style


Le meilleur exemple étant l’illustration, voici un code <del>inutile</del> résumant les différents points que l'on va aborder :

    
    /**
    * Un exemple de code Javascript
    * @param {string} bar Argument inutile
    * @author maxlab.fr
    */
    function functionInutile( bar ) {
    
        var hello = ‘world’,
            test = [1, 2, 3],
            objetJavascript = {
                nombre : 123,
                text : ‘blabla’
            };
    
        // une boucle
        for ( var i=0,t=test.length; i<t; i++) {
            if ( true ) {
                console.log( test[i] );
            }
        }
    }




### Commentaires


Je suis assez partisan du “code en tant que documentation”. C’est à dire écrire un code le plus descriptif possible en particulier dans le **choix du nom des variables et fonctions pour qu’il soit lisible et compréhensible**. Cela reste tout de même insuffisant et **un code commenté est souvent indispensable**. Javascript ne déroge pas à la règle et propose un système de commentaire proche du C++. On peut utiliser des codes spéciaux dans les blocs de commentaires pour générer une documentation grâce à des outils comme [JsDoc](http://code.google.com/p/jsdoc-toolkit/).
L’utilisation de commentaires sur une ligne est aussi possible mais pour garder en lisibilité, il convient de **ne pas faire de commentaires en fin de ligne**.




### Regroupement des variables


Au début de la fonction, on peut **séparer les différentes variables par une virgule** et terminer la déclaration du groupe par un point virgule. Cette pratique est particulièrement utile lorsqu’il y a de nombreuses variable à gérer. On peut remarquer que j’aurai pu faire de même pour les variables i et t dans la boucle. Je fais le choix de déclarer les variables utiles à la boucle dans celle-ci pour pouvoir s’y retrouver.

Concernant l’utilisation du **point virgule : ils sont nécessaire à chaque fin d'instruction**. Absolument [<del>obligatoires</del> ](http://dailyjs.com/2012/04/19/semicolons/)conseillés lors d’une déclaration de variable, il ne [le sont pas pour les fonctions](http://ecma262-5.com/ELS5_HTML.htm#Section_13).


### Utilisation des variables


Pour terminer sur les variables, il faut TOUJOURS utiliser le mot clé _var_ pour la déclaration. On évite ainsi de se retrouver avec des **variables globales dont l'utilisation doit être** **la plus limité le plus possible** ( [question de performance](http://performance.survol.fr/2008/05/portee-des-variables-javascript/) et de maintenabilité du code ).




### Mise en cache


Dans l’exemple, la variable _t_ correspond à la taille de notre tableau _test_. Il est inutile de recalculer cette valeur à chaque itération, par conséquent, même si c’est aussi un sujet touchant à l’[optimisation des performances](http://www.erichynds.com/javascript/javascript-loop-performance-caching-the-length-property-of-an-array/) qu'au style, c’est une pratique importante. Il en va de même pour [jQuery avec le DOM](http://www.artzstudio.com/2009/04/jquery-performance-rules/#cache-jquery-objects). La mise en cache permet d'économiser des ressources processeur surtout lors du traitement en boucle.




### Les accolades sont obligatoires !


Même si il est possible de s’en passer dans certains cas il faut les utiliser. Les risques d’erreurs sont trop important pour passer à coté.
Pour un code lisible, le mieux est de _placer des espaces entre les parenthèses et accolades_ et d’effectuer un **retour à la ligne après l’ouverture du bloc d’instruction**.




### Utilisation des guillemets


Simple ou double peut importe, ce qui compte c’est de **faire un choix et de s’y tenir**. Il est toutefois recommandé d’utiliser les guillemets simple lors de l’utilisation de contenus HTML.




### Être cohérent


Certainement la règle la plus importante, que ce soit seul et encore plus en équipe, **la cohérence est une des clés d’un code propre et maintenable**.
Parmi les éléments importants on peut noter les espaces, les guillemets, formatage des noms ( camelCase ou underscore_case).




### Ne pas mélanger espaces et tabulations


C'est une règle applicable pour l’indentation notamment qui découle de la précédente.

La meilleur chose est d’activer l’affichage des caractères invisibles afin de nettoyer le code. Il est d’autant plus important d’avoir un code propre pour le travail en équipe utilisant des gestionnaires de version. Cela évite en effet d’avoir à constater une modification sur un simple espace.

Pour voir d'autres recommendations je vous conseil de faire un tour [ici](https://github.com/rwldrn/idiomatic.js/).


## Bonne pratiques


On a vu quelques bon réflexes à avoir pour l'écriture de code, passons maintenant à quelques bonnes pratiques plus spécifiques à Javascript.




### Notation simplifiée


Il y a plusieurs manières de déclarer un tableau ou un objet en javascript. Il est conseillé d'utiliser la notation simplifiée car elle simplifie l'écriture et permet d'éviter des erreurs comme l'oublie du mot clé new.



    
    // Objet
    // Notation complète
    var objet = new Object();
    o.couleur = 'Rouge';
    o.angles = 4;
    o.maCouleur = function() {
       console.log(this.couleur);
    };
    
    // Notation simplifiée ( littérale )
    var objet = {
        couleur : 'Rouge',
        angles : 4,
        maCouleur : function() {
            console.log(this.couleur);
        }
    };
    
    // Tableau
    // Notation complète
    var tableau = new Array();
    tableau[0] = 1;
    tableau[1] = 2;
    
    // Notation simplifiée
    var tableau = [1, 2];





### Balise <script>


La balise script **doit être déclarée en bas de la page html**, cela afin de ne pas bloquer le chargement de la page. Aujourd'hui, la meilleur manière de la déclarer est la suivante :

    
    <script>
     // Javascript Ici
    </script>
    
    // Lien vers un fichier
    <script type="text/javascript" src="js/fichierJs.js"></script>


Difficile de faire plus simple ! On peut dire merci au HTML5 qui nous épure nos balises. Il est possible de rajouter _type='text/javascript'_, ce n'est plus obligatoire mais reste une [Obsolete but conforming features](http://www.whatwg.org/specs/web-apps/current-work/multipage/obsolete.html).




### Comparaison


Javascript propose deux méthodes de comparaison. Avec ou sans vérification du type. Il est conseillé d'**utiliser l'opérateur avec vérification ( === )** qui, au delà du [gain de performance](http://jsperf.com/triple-equals-vs-twice-equals) , évite les surprises.

    
    0==false   // true
    0===false  // false
    1=="1"     // true
    1==="1"    // false


La simple comparaison effectue une coercition de type ( ou conversion de type, j'en reparlerai ) . C'est à dire que Javascript essaye de convertir la donnée à tester pour que le type corresponde. Dans le cas du test sans coercition, la valeur ET le type doivent correspondre.




### Utilisation de _eval_, _with()_ et _for in_


Ces trois fonctionnalités de javascript sont à utiliser avec précaution :




#### eval


Eval permet de faire exécuter une chaîne de code par le moteur de javascript. Certains proposent de **ne pas l'utiliser**, j'ajouterai, **à moins de savoir exactement ce que l'on fait** car c'est une énorme source de faille de sécurité puisqu'on donne accès directement au compilateur.




#### With


Honnêtement je ne l'ai jamais utilisé et il ne vaut mieux pas. With() permet de travailler sur le contexte d'un objet.

    
    with document {
    var x = body.scrollLeft;
    write('text1');
    write('text2');
    write('text3');
    }
    
    // Equivalence
    var d = document;
    var x = d.body.scrollLeft;
    d.write('text1');
    d.write('text2');
    d.write('text3');
    
    // Source de l'exemple : http://javascript.about.com/library/blwith.htm


Pour résumer : **ne pas utiliser with()** .




#### For in


Cette déclaration permet de faire une boucle sur un objet. **Il est préférable d'utiliser un simple for **dans la plupart des cas, notamment lorsqu'il s'agit d'itérer sur un tableau. En effet, for-in va faire une liste des propriétés de l'objet ce qui signifie une perte de performances (néanmoins pour moins de 50 éléments à traiter il n'y a aucune différence perceptible) .

Personnellement je trouve aussi plus efficace d'avoir la maîtrise de ses boucles pour éviter les surprises.

Merci à Gildas pour les précisions dans [son commentaire](http://maxlab.fr/blog/2012/05/javascript-part-1-bonne-pratiques/#comment-25) qui indique aussi le fait que l'ordre l’itération d'un for in n'est pas garanti




### Opérateur ternaire


C'est une petite astuce qui permet de réduire son code pour des tests conditionnels simple (vrai ou faux).

    
    if (h === true) {
        console.log(true);
    } else {
        console.log(false);
    }
    
    // Devient
    var retour = (h === true) ? "vrai": "faux";
    console.log(retour);
    
    //Ou même
    console.log(h ? "vrai" : "faux);





### Javascript et le multi-lignes


Il y a deux manières d’écrire du texte multi-lignes en javascript :

    
    // 1ere méthode
    var multiligne1 =
    ‘Lorem ipsum dolor sit amet, consectetur adipiscing elit. \
    Donec eu turpis tortor, at luctus nunc. Nulla suscipit \
    porttitor diam, id dapibus sem mollis ine. Donec semper \
    suscipit ligula, a molestie ante sagittis nec. Nunc diam \
    urna,congue nec sagittis non, feugiat ac nib’ ;
    
    // 2eme méthode
    var multiligne2 = ‘Lorem ipsum dolor sit amet,consectetur'+
    'adipiscing elit. Donec eu turpis tortor,at luctus nunc.'+
    'Nulla suscipit porttitor diam, id dapibus sem mollis id.'+
    'Donec semper suscipit ligula,a molestie ante sagittis nec'+
    'Nunc diam urna, congue nec sagittis non, feugiat ac nib’;


Il est [conseillé](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#Multiline_string_literals) d’utiliser la deuxième méthode.




### Tester son Code


Pour terminer dans les bonnes pratiques, tester la qualité de son code est un bon réflexe. Il existe un testeur javascript bien connu [JSLint](http://www.jslint.com/).[JSHint](http://www.jshint.com/) est moins contraignant et tout aussi performant.


## Un peu de lecture


C'est la fin de ce premier article consacré au Javascript. Pour terminer un peu de lecture pour ceux qui souhaitent aller plus loin.
Liens :

[JavaScript Performance Best Practices](http://www.developer.nokia.com/Community/Wiki/JavaScript_Performance_Best_Practices)

[Google JavaScript Style Guide](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)

[JavaScript Style Guides And Beautifiers](http://addyosmani.com/blog/javascript-style-guides-and-beautifiers/)
