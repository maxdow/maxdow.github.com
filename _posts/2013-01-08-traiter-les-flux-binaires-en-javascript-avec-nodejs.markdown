---
author: admin
comments: true
date: 2013-01-08 13:54:49+00:00
layout: post
slug: traiter-les-flux-binaires-en-javascript-avec-nodejs
title: Traiter les flux binaires en javascript avec nodejs
wordpress_id: 297
categories:
- Javascript
- Non classé
---

Il y a quelques temps, j’ai eu besoin d’implémenter un protocole de communication entre un serveur et une application sous nodejs.
Nodejs étant asynchrone, il est très simple de mettre en place un système qui va récupérer les données provenant d’un serveur tcp par exemple. Mais que faire ensuite de ces paquets pour retrouver nos trames ? Comment décoder des données binaires ?

On va voir dans ce mini tuto :

Comment récupérer et traiter un flux avec nodejs .
Deux méthodes pour décoder des trames : méthode texte, méthode binaire avec node-binary .

<!-- more -->


## Récupération des données


Pour tester, on prend comme exemple de flux l’entrée console stdin. D’après la [doc de node](http://nodejs.org/api/process.html#process_process_stdin)js, c’est un[ Readable Stream](http://nodejs.org/api/stream.html#stream_readable_stream), on a les mêmes fonctionnalité que si on créait un client tcp / udp ou même une lecture de fichier avec fs.

    
    
    process.stdin.resume();
    process.stdin.on('data', function (chunk) {
     console.log(chunk);
     });
    



On active l’écoute de la console avec resume. L'événement _data_ se déclenche dès qu'une donnée arrive.

Pour le moment si on écrit quelque chose, on ne va pas obtenir notre texte tel qu’on l’a entré mais un objet de type Buffer.

**Toute donnée provenant d’un stream est retournée sous cette forme. Pourquoi ?**

Car on va être très vite confronté à autre chose que du texte si on développe coté serveur. Dans le navigateur, pas de soucis, mais si on veut décoder des paquets Tcp par exemple, il nous faut des outils pour travailler sur les données brutes.

Un buffer est un tableau de bits. La [classe Buffer](http://nodejs.org/api/buffer.html) possède différentes méthode pour encoder et décoder un buffer.

    
    
     process.stdin.on('data', function (chunk) {
     // chunk est un Buffer
     console.log(chunk.toString());
     });


On retrouve notre texte, trop facile !

La méthode toString() va décoder notre buffer, par défaut en encodage utf-8.





## Implémentation d'un protocole binaire



Maintenant qu'on a vu les bases, on va essayer d’implémenter notre protocole de communication et de le décoder.

On imagine un protocole qui peut être utilisé aussi bien en tcp que sur une liaison rs-232. On aura donc des données binaires codées en héxadécimal. Pour être sur que nos paquets arrivent bien, on définit un délimiteur de trames ainsi qu'un contrôle d’erreur.

caractère ";" 8bits || Id (8bits) || taille données (16bits) || données || crc (16bits )

L’ensemble est codé en Big E.

**Dans cet exemple on utilise un caractère pour délimiter les trames. Dans la plupart des cas surtout lorsque l’on utilise le TCP, ce système n’est pas justifié car les paquets sont contrôlés en amont par le système. Néanmoins cette technique illustre une méthode de parsing de flux .**

Pour tester, on crée un serveur qui envoie toute les seconde une donnée ( la date ), suivant notre protocole. Comme ce qui nous intéresse pour l’instant, c’est de décoder cette trame, on va surtout s’intéresser à la partie client. Pour les curieux voila le code :

    
    var net = require('net');
    var clients = [];
    var HOST = '127.0.0.1';
    var PORT = 8080;
    
    net.createServer(function(sock) {
     
     clients.push(sock);
     
     sock.on('close', function(client) {
     var i = clients.indexOf(client);
     clients.splice(i, 1); 
     });
     
    }).listen(PORT, HOST);
    console.log('Server listening on ' + HOST +':'+ PORT);
    
    setInterval(function() {
     if(clients.length > 0) {
     var date = new Date() ;
     var paquet = {
       separateur : ";",
       id: 0xdd,
       data : date.toString(),
       crc : 0xff
     };
     paquet.taille = paquet.data.length ;
    //“;” 8bits || Id (8bits) || taille données (16bits) || données || crc (16bits ) 
     
     var paquetB = new Buffer(6 + paquet.taille);
     paquetB.write(paquet.separateur);
     paquetB.writeUInt8(paquet.id,1);
     paquetB.writeUInt16BE(paquet.taille,2);
     paquetB.write(paquet.data.toString(),4,paquet.data.length);
     paquetB.writeUInt16BE(paquet.crc,4+paquet.data.length);
     clients.forEach(function(client){
       client.write(paquetB);
     });
     }
    }, 1000);




Si on suit le programme précédent mais en remplacent l’entrée par un flux TCP voila ce qu’on obtient.

Simple client TCP

    
    var net = require('net');
    var HOST = '127.0.0.1';
    var PORT = 8080;
    var client = new net.Socket();
    client.connect(PORT, HOST);
    client.on('data', function(data) {
     console.log(data); // <Buffer 3b dd 00 31 54 75 ...
    });
    



1er problème, notre protocole n’est pas au format texte, on va devoir jouer avec les buffer et décoder correctement les données.


### Methode texte


Pour décoder nos données on peut utiliser la méthode toString() vue précédemment qui va transformer notre Buffer en chaîne de caractère. Comme les données sont codées en hexadécimal, on va un peu travailler pour décoder notre texte.

    
    function decodeTexte(paquet) {
     
     paquet = paquet.toString("hex");
     var paquetDecode = {
       separateur : paquet.slice(0,2),
       id : paquet.slice(2,4).toString("16"),
       taille : parseInt(paquet.slice(4,8),16)*2
     }
     paquetDecode.data = new Buffer(paquet.slice(8,8+paquetDecode.taille),"hex").toString() ;
     console.log(paquetDecode);
    }


Notre paquet devient une chaîne du type “3bdd024c54 ….”. On utilise split() qui va découper les données selon notre protocole.

Dans cet exemple on récupère ( un peu salement ) notre donnée mais sans se préoccuper du délimiteur (ni du crc qui ne fait pas l'objet de cette présentation ). La donnée est de petite taille et le TCP gère la bonne transmission des paquets, de plus on est en local donc aucun soucis.

Imaginons maintenant qu’on ait besoin d’utiliser notre protocole pour transmettre de plus grosses données sur un réseau qui ne soit pas local. Il va arriver un moment où les paquets seront divisés en plusieurs morceaux. Dans ce cas notre décodeur texte ne suffit plus.



### Méthode texte + buffer


On a besoin de mettre en place un système de tampon qui va se remplir tant que notre paquet n’est pas arrivé entier.

    
    var buffer ="";
    function decodeTexteBuffer(paquet) {
     paquet = paquet.toString("hex");
     buffer += paquet ;
     var indexDebutTrame = buffer.indexOf("3b");
     if(indexDebutTrame != -1) {
      var trame = buffer.slice(0,indexDebutTrame);
      // Traitement de la trame
      buffer = buffer.substr(indexDebutTrame+2);
     }
    }


On cherche à chaque arrivée de donnée le caractère qui représente notre délimiteur ( le caractère ";" vaut 3b en hexadécimal ) . Si il est présent on a bien notre trame et on peut commencer le traitement. On vide ensuite le buffer.


### Méthode Binaire


Le problème avec ces méthodes, c’est qu'elles reposent sur des traitements et des conversion vers des chaines de caractères.

Le meilleur moyen selon moi reste de travailler un maximum en gardant les données sous forme de Buffer.

Pour décoder notre paquet, on peut le faire directement à l’aide des fonction read du Buffer où en utilisant le module [node-binary](https://github.com/substack/node-binary) qui nous fait ça tout propre.


    
    function decodeBinary(paquet) {
    var paquetDecode = binary.parse(paquet)
     .word8("separateur")
     .word8("id")
     .word16bu("taille")
     .tap(function(vars){
     this.buffer("data",vars.taille).toString();
     })
     .word16bu("crc")
     .vars
     ;
    console.log(paquetDecode,paquetDecode.data.toString());
    }



On peut aussi rediriger notre flux directement et ainsi couper notre trame suivant le délimiteur.



    
    var b = binary()
    .loop(function(end,vars){
     this.scan('paquet', new Buffer(';'))
     .tap(function (vars) {
     decodeTrame(vars.paquet);
     });
    });
    serveur.pipe(b);
    





## Conclusion


On l’a vu, il existe différentes méthodes pour décoder des données binaires provenant de flux avec nodejs. Il ne faut pas oublier aussi qu'il est tout à fait possible d’utiliser des opérateurs binaires sur des variables ce qui permet d'utiliser javascript comme on le ferait avec un programme en C pour traiter une communication via port-série par exemple.

Petite astuce au passage, pour** transformer un nombre en hexadécimal ou en binaire avec javascript** :

    
    
    var nombre = 12;
    console.log("nombre en héxadecimal",nombre.toString("16")); // "c"
    console.log("nombre en binaire",nombre.toString("2")); // "1100"
    
