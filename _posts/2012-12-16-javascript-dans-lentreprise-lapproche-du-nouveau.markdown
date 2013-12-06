---
author: admin
comments: true
date: 2012-12-16 22:25:51+00:00
layout: post
slug: javascript-dans-lentreprise-lapproche-du-nouveau
title: Javascript dans l'entreprise ? L'approche du nouveau
wordpress_id: 281
categories:
- Javascript
---

Il y a quelques semaines, j'ai troqué mon costume de "gars du chantier" pour celui de "développeur bien au chaud". Quelle joie de pouvoir faire tout plein d'ordinateur toute la journée et discuter encore ordinateur avec les collègues d'à coté ;)

En tant que petit nouveau, on m'explique que la révolution des services web est entrain d'arriver et que c'est le bien et tout et tout.... Pas besoin de prêcher un convaincu mais j’acquiesce, content de voir qu'au moins, on sort progressivement de DOS même si le gros du boulot est encore géré par excel97...Allé j’arrête d'être mauvaise langue !

<!-- more -->

A peine arrivé donc, j'essaye de lister les technos utilisées dans nos "services web".

Coté serveur on se retrouve avec un peu de php et sinon **l'essentiel tourne autour du Java**. IHM ( clients LOURDS et GRAS) en swing et serveurs J2EE, le monstre se réveille ( ne me demandez pas pourquoi mais j'ai une petite dent contre le Java ;) ).


![Java illustration](http://maxlab.fr/blog/wp-content/uploads/2012/12/14231fd6f4e0d8ae84632e25cbfdce30-200x300.png)


N'ayant quasiment aucune connaissance de J2EE on me propose une petite formation qui va bien pour faire des "services web".


## J2EE et services Web, usine à gaz et chamallow


_Petit retour d’expérience d'une semaine de J2EE en immersion._

Tout avait pourtant bien commencé... Un petit TP autour d'un gestionnaire de congé pour illustrer EJB3, JPA, EAR, WAR, POJO et autres haricots magiques de cet univers vaste, étrange mais terriblement puissant. J'avoue avoir été bluffé par l'injection d'EJB3 et comment transformer une petite classe innocente en webservice simplement avec une[ petite annotation](http://docs.oracle.com/javaee/6/api/javax/jws/WebService.html). Moyennant <del>un peu</del> énormément de configuration et <del>quelques connaissances </del>des années d’expérience on arrive à une application solide avec un beau niveau d'abstraction qui fait gagner un paquet de temps au développeur en lui évitant de gérer tout un tas de détails, que ce soit dans l'échange avec le serveur que dans la couche de persistance. D'un autre coté, on a la présentation avec les jsp. J'y vois là une sorte de templating java qui peut se révéler redoutable avec l'utilisation des expressions EL. Et si on rajoute à cet écosystème déjà vaste de nombreux framework tel que spring on peut imaginer

Bref un bilan plutôt positif, notre cher Eclipse qui nous soutient par dessus, l'impression d'être devenu tout puissant. Mais la ou cela devient problématique, c'est que même avec un petit projet comme le notre, l'utilisation mémoire a vite explosée jusqu'à ...planter la JVM. oups.. Malgré ça cette architecture semble terriblement puissante car permettant une abstraction forte qui laisse la place au code métier dans espace à part...pour peu qu'on l'utilise ce pourquoi elle est prévue !

En fait après réflexion ce qui me fait le plus peur c'est que les bonnes pratiques ne sont pas forcement respectées, faute de compétence et / ou de connaissance, ce qui fait qu'on a non seulement un système potentiellement lourd mais qui devient vite inmaintenable au vu de la complexité de mise en oeuvre, et c'est là le problème : l'impression de monter rapidement une énorme usine à gaz dont on perd le contrôle..



[caption id="attachment_284" align="aligncenter" width="230"]![J2EE attention à la machine à gaz ?](http://maxlab.fr/blog/wp-content/uploads/2012/12/heath_robinson_pancake-230x300.jpg) J2EE : Attention à l'usine à gaz[/caption]


## Y a t'il une place pour javascript ?


Comme toute technologie un tant soit peu puissante, J2EE doit être utilisé correctement, en toute connaissance de cause ( lire le passage sur le[ Cargo-cult programming](http://www.codeinstructions.com/2008/10/styles-of-programming.html) qui résume bien le problème)  . C'est la même chose pour le javascript.

La puissance et l'énorme buzz qui monte autour des technos js aussi bien coté client que serveur à déjà commencé à le pousser en entreprise, mais surtout dans les start-up qui ont bien plus de possibilités de manœuvre que des grosses boites ( sans parler des administrations...) . Ce qui manque à mon avis pour une adoption plus forte c'est l'unicité des outils, encore un peu jeunes. Il y a par exemple beaucoup de librairies autour de nodejs, souvent 2 ou 3 similaires pour la même fonction, et il est parfois dur de[ faire son choix](http://immigrechoisi.com/voyages-immigration/le-paradoxe-de-la-liberte-de-choisir-pourquoi-trop-de-choix-tue-le-choix/1854/). Il n'y a pas encore non plus d'IDE dédié au javascript qui a fait ses preuves comme Eclipse ou NetBeans peuvent l'être pour le Java , même si il existe de belles initiatives comme [Cloud9](https://c9.io/).

Enfin j'imagine qu'il faut surtout du temps pour que l'adoption se fasse, en montrant doucement ce qu'il est possible de faire et en prêchant la bonne parole et les bonnes pratiques, mais quoi qu'il en soit, oui Javascript a sa place. **Une place qui devient une nécessité** au minimum dans le navigateur tant l'utilisation du cloud et des applications web se développe rapidement.


