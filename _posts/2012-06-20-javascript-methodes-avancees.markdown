---
author: admin
comments: true
date: 2012-06-20 09:49:19+00:00
layout: post
slug: javascript-methodes-avancees
title: 'Javascript : méthodes avancées'
wordpress_id: 268
categories:
- Javascript
---

On continue cette petite série d’articles par quelques méthodes pratiques que l’on peut utiliser avec le javascript.

Nous verrons tout d'abord comment utiliser et créer des callbacks. On passera ensuite par la technique des namespaces amenant naturellement à parler de la programmation objet.  On terminera par utiliser les closures pour résoudre un problème de boucle.

<!-- more -->


## Les Callback





### Qu’est ce qu’un Callback et à quoi sert il ?


Un callback est ce qu’on peut traduire par "fonction de retour". C’est une fonction comme une autre sauf qu’elle est placée en paramètre d’une autre fonction pour être exécutée à un moment précis.
Pour ceux qui utilisent Jquery, le principe du callback est souvent utilisé.

    
    $.ajax({
    url: url,
    data: data,
    success: function(donnees){
        console.log(donnees);
    },
    
    });


Lorsque l’on exécute une fonction à la bonne réception d’un appel ajax, on effectue enfait un callback sur une fonction.




### Comment utiliser et créer des callback ?


On va prendre un exemple simple de création de callback, une fonction qui dit “bonjour”

    
    function hello(onBefore,onAfter) {
        if(onBefore) {
            onBefore();
        }
    
        console.log("bonjour");
    
        if(onAfter) {
            onAfter();
        }
    }
    
    // On appel la fonction
    hello(
    function(){
         console.log("avant");
    },
    function(){
        console.log("après");
    }
    );
    
    //Resultat :
    // avant
    // bonjour
    // après


Cette fonction très simple met à disposition deux callback : avant et après l’affichage console.
Le fait de vérifier la présence de paramètre permet d’appeler la fonction sans callback ( ceci peut être utile lors de la création d’une fonction réutilisable, dans une librairie par exemple) .

Un autre bon exemple de callback est la fonction _setTimeout_ de javascript. Cette fonction permet d’effectuer une temporisation avant d'exécuter une fonction passée en paramètre, ceci de manière non-bloquante (principe de l’asynchrone ).

    
    setTimeout(hello,1000);


On réutilise notre fonction _hello_ que l’on passe en paramètre de _setTimeout_. A la fin du temps, _hello_ est executée en callback. On peut noter l'absence de parentheses lors du passage de la fonction en paramètre. Ceci est du au fait que l'on passe un pointeur vers la fonction _hello_ qui sera exécutée à la fin du temps demandé. Si on avait mis des parenthèses, on aurait eu exécution de la fonction lors de l’interprétation .




## Utiliser un Callback dans une boucle


Les callback sont généralement utilisés lors d’opérations asynchrones mais il peut arriver que l’on veuille répéter la tache dans une boucle. On serait alors tenté de faire:

    
    function prendDuTemps(onSuccess) {
        setTimeout(
            function() {
                if(onSuccess) {
                    onSuccess()
                }
            },
        1000);
    }
    
    for (var i=0;i<10;i++) {
        prendDuTemps(
            function(){
                console.log("->"+i);
            }
        );
    }


On se heurte ici à deux problèmes. Non seulement  la valeur de i n’est pas forcément celle attendue ( on verra pourquoi lors de la partie sur les closures ) mais , du fait de l’aspect asynchrone de notre fonction _prendDuTemps_, la boucle exécute tout en même temps sans attendre le résultat.

Voici une astuce qui permet d’**utiliser une fonction de callback dans une boucle** ou autrement dit, transformer une fonction asynchrone en synchrone sans pour autant bloquer l'exécution.

    
    function boucleSynchrone(i) {
    	if(i<10) {
    		prendDuTemps(
    			function(){
    				console.log("blah"+i);
    				boucleSynchrone(i+1);
    			}
    		);	
    	}
    
    }
    boucleSynchrone(0);








## Namespace


C’est une technique simple mais primordiale lorsque l’on travail en équipe ou même lorsque son code commence à grossir. Utiliser des espaces de noms **permet de réduire le nombre de variables présentes dans l'environnement global** et ainsi d’avoir une meilleur organisation de code.

    
    var monApplication = monApplication || {} ;
    
    monApplication.titre = "Titre";
    monApplication.sousTitre = "sous titre";
    
    monApplication.afficheTitre = function () {
        console.log(this.titre + " --- "+this.sousTitre) ;
    }




On créé un objet _monApplication_ et on lui attribut des variables, fonctions..ce qu’on veut, l’avantage est qu’on expose un seul objet à l’environnement global. C’est une manière de protéger son code contre une redéfinition de fonction par une autre librairie par exemple. En faisant ça on approche aussi de la notion de la programmation orientée objet qui peut tout à fait être mise en place avec le Javascript.


## 





## Javascript et la POO


Le javascript possède une approche de l’objet différente de celle que l’on peut utiliser avec des langages comme java par exemple. En effet, il ne possède pas de mots clé tel que class, public private.. car **c’est un langage orienté prototype**. Il existe deux moyens de créer des “classes” en javascript


### 





### Prototypes


Le prototype est une propriété de fonction qui permet de transformer celle-ci en "classe". La grande différence du prototype avec un système de classe standard est son aspect dynamique. Il est tout à fait possible de modifier notre classe pendant l'exécution du programme

    
    var maClasse = function(){}; // Constructeur de la classe "maClasse"
    maClasse.prototype.constructor = maClasse;
    maClasse.prototype.value = 1;
    maClasse.prototype.test = function() { alert(this.value); }
    var a = new maClasse(); // créé une instance de maClasse
    alert(a.value);  // => 1
    alert(a.test()); // => 1




### 





### Closures


La technique des closures est une méthode puissante qui permet d’enfermer des variables dans un contexte qui peut être isolé du contexte global.

    
    var maClasse = function() {
    // fonction privée
    var echo = function(msg) {
        alert(msg);
    };
    this.val = 1;
    this.test = function() {
        echo(this.val);
    };
    };
    var c = new maClasse();
    alert(c.val); // -> 1
    alert(c.test()); // -> 1




### 







### Quelle méthode choisir


La principale différence entre les deux méthodes se situe au niveau de la performance lors de la création de nombreux objets. La méthode prototype est créé une fois puis partagée entre les différentes instances alors que la closure est parsée et exécutée à chaque nouvel objet créé. Il en résulte une consommation mémoire et cpu supérieur pour la méthode des closures lors du l’instanciation de multiples objets. A vous de voir donc en fonction des cas quoi utiliser.


### 





### 




### Aller plus loin avec la POO en javascript


Grâce aux méthodes précédentes, de nombreux design patterns ont vu le jour. Je vous conseil pour découvrir ce vaste monde l’excellent [livre de Addy Osmani](http://addyosmani.com/resources/essentialjsdesignpatterns/book/) qui présente de nombreuses solutions et pistes dans le domaine de la programmation objet en javascript.


## 





## Utilisation des closures dans les boucles


Pour terminer cet article, voici une dernière technique (très) utile concernant l’utilisation des closures en javascript.

Disons que l’on veut créer plusieurs boutons dans une boucle :

    
    for (var i=0, elm; i<5; i++) {
        elm = document.createElement("button");
        elm.innerHTML = "Bouton " + i;
        elm.onclick = function () {
            alert(i);
        }
        document.body.appendChild(elm);
    }


On souhaite afficher l’index du bouton lors du clic. Problème, le nom des boutons est correcte mais pas l’index affiché qui reste celui du dernier élément.
Le soucis vient du fait que lors de l’appel de la boite de dialogue, la fonction alert () va lire i qui vaut la dernière valeur qu’on lui a affecté dans la boucle.

La solution est de garder une trace de la valeur de i pour chaque élément. C’est ce que fait une closure.

    
    for (var i=0, elm; i<5; i++) {
        elm = document.createElement("button");
        elm.innerHTML = "Link " + i;
        elm.onclick = function (index) {
            return function () {
                alert(index);
            }
        }(i);
        document.body.appendChild(elm);
    }


En créant un espace fermé pour chaque bouton, on enregistre la valeur de i dans index. Lorsque l’on clique sur un bouton, on exécute la fonction associée avec son propre contexte, indépendant de la valeur de i.






## Conclusion


On l’a vu, le javascript peut paraître simple d’approche, mais bien utilisé, il peut devenir un langage réellement très puissant.

Son utilisation reste assez intuitive mais certaines astuces peuvent parfois faciliter la vie du développeur. C’est ce que nous verront prochainement.
