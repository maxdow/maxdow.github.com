---
author: admin
comments: true
date: 2012-02-12 11:26:04+00:00
layout: post
slug: un-selecteur-de-couleur-avec-enyo
title: Un sélecteur de couleur avec Enyo
wordpress_id: 136
categories:
- Tuto
- Webos
tags:
- enyo
- javascript
- tutoriel
---

Suite aux nouvelles plutôt positives concernant Webos ( passage en Open source ) et au fait que Enyo soit apprécié ( et j'avoue que j'aime bien aussi ;) ), je continue mes développements pour la touchpad.

A ce titre, j'ai eu l'occasion de créer un petit selecteur de couleur et c'était donc une excuse toute trouvée pour faire un petit tuto.

<!-- more -->

L'objectif est simple mais permet d'aborder plusieurs notions que sont les évènements, l'ajout d’éléments à la volée et la création d'un widget perso.

Je vais essayer de bien détailler la mise en place du programme pour qu'il soit le plus accessible possible.

Pour commencer un petit aperçu du résultat :

[![Sélecteur de couleur](http://maxlab.fr/blog/wp-content/uploads/2012/02/colorpick.png)](http://maxlab.fr/blog/wp-content/uploads/2012/02/colorpick.png)



Un petit rappel pour les débutants afin de réaliser facilement ce tuto :

Il vous faut le framework Enyo afin de pouvoir tester le rendu avec chrome par exemple.

Ne pas oublier de passer les paramètres "--disable-web-security"  "--enable-file-cookies" au lancement de chrome. ( [voir ici pour plus d'infos](https://developer.palm.com/content/resources/develop/extended_enyo_tutorial.html) ).

Je vais détailler un maximum, néanmoins si  vous voulez en savoir plus sur les bases du développement sur Webos, je vous renvoie aux [Devcast du site Webos-France ](http://www.webosfrance.com/DevCast_r13.html?start=8)qui pourront vous guider lors de la création de votre application.

En bref, ce qu'il faut savoir c'est que enyo est basé sur des objets appelé _kind_. Un _kind_ peut être un objet vide ou un élément graphique (bouton, checkbox, popup...). Ce système est construit sur la programmation objet ce qui signifie que la notion d'héritage est présente.


## Fichiers de l'application


Pour ce qui est de l'architecture de notre programme, on va créer un nouveau dossier et mettre le framework Enyo dans un dossier nommé 1.0.

Ensuite il nous faut créer les fichiers suivant:

**index.html** : fichier qui sert de base à enyo. Attention ici , j'ai mis dans la balise src "1.0/framework/enyo.js" car je travail avec mon framework dans le même dossier.

    
    <!doctype html>
    <html>
    	<head>
    		<title>Tuto colorpick</title>
    		<script src="1.0/framework/enyo.js" type="text/javascript"></script>
    	</head>
    	<body>
    		<script type="text/javascript">
    			new App().renderInto(document.body);
    		</script>
    	</body>
    </html>


**App.js :** premier script qui sera lu

**app.css :** pour mettre le style de nos classes CSS hors code

**colorpick.js :** notre sélecteur. C'est une bonne habitude de créer un fichier par widget.

**depends.js :** pour faire les dépendances, rien de bien compliqué ici , on inclue les fichiers que l'on vient de créer.

    
    enyo.depends(
    "app.css",
    "App.js",
    "colorpick.js"
    );




## La base du programme




Nous allons commencer par créer la base de notre application dans App.js. Je re précise que le but est vraiment de faire simple afin que ce tuto soit très accessible. Vous pourrez toujours peaufiner ensuite.




    
    enyo.kind({
    	name: "App",
    	kind: enyo.Control,
    
    	components: [
    		{name:"popupCouleur",kind:"Popup",components:[{kind:"colorPick",onColorSelect:"selectionCouleur"}]},
    		{name:"elementCouleur",className:"elementCouleur",onclick:"ouvrePopup"}
    
    	],
    
    	ouvrePopup : function() {
    		this.$.popupCouleur.openAtCenter();
    	}
    
    });




On a ici notre base d'application qu'on appel simplement App. Son _kind_ est du style _Control_, elle va hériter des caractéristiques des _control_ de enyo. On choisit en général ce type lorsque l'on veut créer un élément nouveau. Un control n'affiche rien de particulier, c'est un objet générique mais qui a la possibilité d'agir sur le DOM.




On va lui ajouter deux éléments : un popup et un objet que l'on nommera _elementCouleur_.




Vous remarquerez que l'objet _elementCouleur_ n'a pas de _kind_ définit. Enyo va simplement créer un _control_ qui se manifestera sous la forme d'une div.




Comme _elementCouleur_ est un objet du DOM il peut avoir un style CSS et réagir aux évenements. On va lui attribuer la classe CSS  "elementCouleur" et lui dire de réagir à l'évenement d'un clic en executant la fonction ouvrePopup.




    
    .elementCouleur {
    	margin:50px 0 0 100px;
    	width:50px;
    	height:50px;
    	border:1px solid #777;
    	background:#fff
    }




La fonction _ouvrePopup_ va ouvrir notre Popup appelée _popupCouleur_ au centre




    
    this.$.popupCouleur.openAtCenter();




Pour le moment notre popup est vide. On va donc passer à la création de notre sélecteur de couleur.





## Le sélecteur de couleur




### Ajout d'éléments à la volée




On se place donc dans colorpick.js dont voici le contenu de base :




    
    enyo.kind({
    	name: "colorPick",
    	kind: enyo.Control,
    	layoutKind:"VFlexLayout",
    
    	components : [
    		{content:"Choisissez une couleur",kind:"Header"}
    	],
    
    });


On veut pouvoir, à partir d'une liste de couleurs, créer une palette d'éléments qui réagissent au clic en retournant leur couleur.


Pour créer ces éléments de manière dynamique, on va utiliser la fonction _create_ de Enyo. Cette fonction est utilisée lors de la création du _kind_ ce qui va nous permettre de rajouter des éléments avant l'affichage de celui-ci.


On déclare une liste de couleurs dans un tableau :

    
    listeCouleurs : [
    "#00e000","#20a000","#00a0c0","#2080ff",
    "#6020ff","#80a0e0","#806000","#c00020",
    "#c08000","#e0a000","#ffa060","#ffff00"
    ]


J'ai choisi d'afficher les couleurs par rangées de 4. On va devoir créer des élements du type

    
    {className:"colorPickItem",onclick:"colorSelect",name:nomdelacouleur,style:"background-color:"+couleur}


Comme on veut les aligner par 4 de manière horizontal, il faut les placer dans un conteneur avec un layout du type _HFlexLayout_. On va donc se créer ses éléments à l'interieur d'une variable en faisant une boucle sur notre tableau de couleur.

    
    	var liste_elements = [];
    	var listeCouleurs = this.listeCouleurs;
    
    	for(var i = 0,t=listeCouleurs.length;i<t;i=i+4){
    
    		liste_elements.push({layoutKind:"HFlexLayout",
    		components:[
    			{className:"colorPickItem",onclick:"colorSelect",name:(i+0)+listeCouleurs[i+0],style:"background-color:"+listeCouleurs[i+0]},
    			{className:"colorPickItem",onclick:"colorSelect",name:(i+1)+listeCouleurs[i+1],style:"background-color:"+listeCouleurs[i+1]},
    			{className:"colorPickItem",onclick:"colorSelect",name:(i+2)+listeCouleurs[i+2],style:"background-color:"+listeCouleurs[i+2]},
    			{className:"colorPickItem",onclick:"colorSelect",name:(i+3)+listeCouleurs[i+3],style:"background-color:"+listeCouleurs[i+3]}
    		]});
    
    	}


Petite remarque ici sur le nom des éléments. On utilise notre variable i pour obtenir un nom différent pour chaque élément. C'est important pour Enyo sinon cela va générer une erreur. On verra que le nom n'est pas choisi au hasard.

Chaque élément possède une classe CSS :

    
    .colorPickItem {
    	width:50px;
    	height:50px;
    	border:1px solid #777;
    	margin:20px
    }




On applique un style pour que le fond de chaque case soit de la bonne couleur.  Enfin , comme vu précédemment, _onclick_ va permettre de réagir au clic.




On obtient nos cases colorées, il reste à les afficher. Pour cela on va créer un _Component _à partir de nos objets stockés dans la variable _liste_elements._




    
    this.createComponents(liste_elements,{owner:this});


Voici la fonction create au complet :

    
    create : function() {
    	this.inherited(arguments);
    
    	var liste_elements = [];
    
    	var listeCouleurs = this.listeCouleurs;
    
    	for(var i = 0,t=listeCouleurs.length;i<t;i=i+4){
    
    		liste_elements.push({layoutKind:"HFlexLayout",
    		components:[
    			{className:"colorPickItem",onclick:"colorSelect",name:(i+0)+listeCouleurs[i+0],style:"background-color:"+listeCouleurs[i+0]},
    			{className:"colorPickItem",onclick:"colorSelect",name:(i+1)+listeCouleurs[i+1],style:"background-color:"+listeCouleurs[i+1]},
    			{className:"colorPickItem",onclick:"colorSelect",name:(i+2)+listeCouleurs[i+2],style:"background-color:"+listeCouleurs[i+2]},
    			{className:"colorPickItem",onclick:"colorSelect",name:(i+3)+listeCouleurs[i+3],style:"background-color:"+listeCouleurs[i+3]}
    		]});
    
    	}
    
    	this.createComponents(liste_elements,{owner:this});			
    
    },




On va maintenant écrire la fonction qui retrouve la couleur qui vient d'être séléctionnée et déclenche un évènement.


### Création d'un évènement


Enyo est basé sur du javascript et par conséquent et très lié aux évènements.  On va s'en servir pour indiquer que l'on a sélectionné une couleur. Lorsque l'utilisateur clic ( appuie ) sur une case, il va déclencher la fonction _colorSelect_. En même temps, l'objet qui réagit va envoyer en argument l'ensemble de ses paramètres. On va donc pouvoir récupérer le nom de cet objet et comme on a mis un nom correspondant à une couleur, on connait la couleur choisie.

    
    colorSelect : function(inSender) {
    	var element = inSender.getName();
    	element = element.split("#");
    	this.doColorSelect("#"+element[1]);
    }




On coupe notre nom qui doit être de la forme_  i#couleur _avec _split_ ce qui nous permet d'obtenir un tableau en 2 parties. On a maintenant notre couleur.




Il reste à générer un évènement en passant comme argument la couleur trouvée. Pour cela, il faut l'avoir déclaré précedemment comme ceci :




    
    events : {
    	onColorSelect : ""
    },




Et on l'active en remplacent le _on_ par un _do_ .




On a terminé notre sélecteur de couleur.





### Application dans le programme


On va utiliser notre sélecteur dans la popup qu'on a laissé vide. Rien de plus simple, il suffit de rajouter notre _kind _colorPick que l'on vient de créer, sans oublier d'indiquer la fonction qui va réagir lors de l’évènement déclenché par le choix d'une couleur.

    
    {name:"popupCouleur",kind:"Popup",components:[{kind:"colorPick",onColorSelect:"selectionCouleur"}]},


Voici la fonction en question :

    
    selectionCouleur : function(e,couleur) {
    	this.$.elementCouleur.setStyle("background-color:"+couleur);
    	this.$.popupCouleur.close();
    }




Le premier argument _e_ possède des informations sur l’évènement. On change le fond de notre _elementCouleur_ avec la couleur retournée et on ferme la popup.





## Conclusion


Ce tuto met en évidence la facilité que procure Enyo pour créer des nouveaux widgets avec une gestion des évenements largement simplifiée.

J'ai essayé de détailler un maximum les différents points clé pour aider les débutants. Si j'en ai l'occasion, je proposerai d'autres tuto de ce style sur Enyo.

Si vous avez des idées ou d'autres remarques, surtout n'hésiter pas dans les commentaires. A bientôt ;)
