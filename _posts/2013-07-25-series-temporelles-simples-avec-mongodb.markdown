---
author: admin
comments: true
date: 2013-07-25 07:40:11+00:00
layout: post
slug: series-temporelles-simples-avec-mongodb
title: Séries temporelles simples avec MongoDB
wordpress_id: 341
categories:
- Javascript
tags:
- mongoDb
- mongoose
- nodejs
---

Voici une méthode pour stocker de manière simple et peu coûteuse en mémoire des données temporelles avec MongoDB et à l'aide de Mongoose.


L’astuce consiste à utiliser l’ObjectId d’un enregistrement qui contient une indication de temps.

<!-- more -->


Dans une base et plus précisément une collection MongoDB, chaque enregistrement doit être stocké avec une clé. La clé par défaut quand elle n’est pas précisé consiste en un type de donnée appelé [ObjectId](http://docs.mongodb.org/manual/reference/object-id/).




Il se trouve que cet objet contient 4bits qui représentent le timestamp Unix. On peut naturellement s’en servir pour trier les données par ordre de création, mais aussi pour récupérer plus précisément une valeur dans le temps.




Prenons un schéma basique ou l’on veut enregistrer une valeur numérique pour un moment donnée :




    
    var valeurSchema = Schema({
     d : Number
    });




A première vu, aucune indication temporelle, mais comme nous n’avons pas fournit de clé, MongoDB va utiliser l’ObjectId en index primaire .





## Générer un ObjectId


Si on veut récupérer une valeur à un moment précis ou sur une plage de temps, il va falloir générer un ObjectId en fonction d’une date.

    
    var ObjectId = mongoose.Types.ObjectId;
    
    function genereObjectId(date)
    {
        return  ObjectId(Math.floor(date/1000).toString(16) + "0000000000000000");
    }


On peut maintenant utiliser cette fonction dans des requêtes sur l'élément __id_ :


### Récupérer toutes les valeurs depuis une date



    
    Valeur.find({"_id":{$gte:genereObjectId(dateDebut)}},"d",function(err,valeurs){
          cb(err,valeurs);
    });




### Récupérer les 10 dernières valeurs avant une date



    
    Valeur.find({"_id":{$lt:genereObjectId(dateMax)}},"d",function(err,valeurs){
          cb(err,valeurs);
    });




### Récupérer des valeurs sur une plage temporelle



    
    Valeur.find({"_id":{$gte:genereObjectId(dateDebut),$lt:genereObjectId(dateMax)}},"d",function(err,valeurs){
          cb(err,valeurs);
    });
