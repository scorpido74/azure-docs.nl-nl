---
title: 'Zelf studie: een gemiddelde stack maken op een virtuele Linux-machine in azure'
description: In deze zelfstudie leert u hoe u een MongoDB-, Express-, AngularJS- en Node.js- (MEAN-)stack maakt op een virtuele Linux-machine in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/08/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 50ccf0d6db5a50f7dd89732bc870ced6bcf1c2d7
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034405"
---
# <a name="tutorial-create-a-mongodb-express-angularjs-and-nodejs-mean-stack-on-a-linux-virtual-machine-in-azure"></a>Zelfstudie: Een MongoDB-, Express-, AngularJS- en Node.js- (MEAN-)stack maken op een virtuele Linux-machine in Azure

In deze zelfstudie leert u hoe u een MongoDB-, Express-, AngularJS- en Node.js- (MEAN-)stack op een virtuele Linux-machine implementeert in Azure. Met de door u gemaakt MEAN-stack kunt u boeken aan een database toevoegen, eruit verwijderen of erin vermelden. Procedures voor:

> [!div class="checklist"]
> * Een Linux-VM maken
> * Node.js installeren
> * MongoDB installeren en de server instellen
> * Express installeren en routes naar de server instellen
> * Routes openen met AngularJS
> * De toepassing uitvoeren

In deze zelf studie wordt gebruikgemaakt van de CLI binnen de [Azure Cloud shell](https://docs.microsoft.com/azure/cloud-shell/overview), die voortdurend wordt bijgewerkt naar de nieuwste versie. Als u de Cloud Shell wilt openen, selecteert u **deze** in het begin van een wille keurig code blok.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.0.30 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.


## <a name="create-a-linux-vm"></a>Een Linux-VM maken

Maak een resourcegroep met de opdracht [az group create](https://docs.microsoft.com/cli/azure/group) en maak een virtuele Linux-machine met de opdracht [az vm create](https://docs.microsoft.com/cli/azure/vm). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt gebruikgemaakt van Azure CLI om in locatie *VS Oost*een resourcegroep te maken met de naam *myResourceGroupMEAN*. Er wordt een VM gemaakt met de naam *myVM* met SSH-sleutels (indien deze niet al op een standaardsleutellocatie aanwezig zijn). Als u een bepaalde set sleutels wilt gebruiken, gebruikt u de optie --ssh-key-value.

```azurecli-interactive
az group create --name myResourceGroupMEAN --location eastus
az vm create \
    --resource-group myResourceGroupMEAN \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password 'Azure12345678!' \
    --generate-ssh-keys
az vm open-port --port 3300 --resource-group myResourceGroupMEAN --name myVM
```

Wanneer de virtuele machine is gemaakt, toont Azure CLI informatie die lijkt op de informatie in het volgende voorbeeld: 

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroupMEAN/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.72.77.9",
  "resourceGroup": "myResourceGroupMEAN"
}
```
Noteer het `publicIpAddress`. Dit adres wordt gebruikt voor toegang tot de virtuele machine.

Gebruik de volgende opdracht om voor de VM een SSH-sessie te maken. Zorg ervoor dat u het juiste IP-adres gebruikt. In ons voorbeeld hierboven was 13.72.77.9 het IP-adres.

```bash
ssh azureuser@13.72.77.9
```

## <a name="install-nodejs"></a>Node.js installeren

[Node.js](https://nodejs.org/en/) is een JavaScript-runtime die op de V8 JavaScript-engine van Chrome is gebouwd. Node.js wordt in deze zelfstudie gebruikt voor het instellen van de Express-routes en AngularJS-controllers.

Installeer Node.js op de VM met behulp van de bash-shell die u met SSH hebt geopend.

```bash
sudo apt-get install -y nodejs
```

## <a name="install-mongodb-and-set-up-the-server"></a>MongoDB installeren en de server instellen
Met [MongoDB](https://www.mongodb.com) worden gegevens opgeslagen in flexibele JSON-achtige documenten. Velden in een database kunnen per document variëren en de gegevensstructuur kan in de loop van de tijd worden gewijzigd. Voor deze toepassing voegen we boekrecords aan MongoDB toe die de naam, de isbn, de auteur en het aantal pagina's van het boek bevatten. 

1. Stel MongoDB in op de VM met behulp van de bash-shell die u met SSH hebt geopend.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
    echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
    ```

2. Werk de pakketmanager met de sleutel bij.
  
    ```bash
    sudo apt-get update
    ```

3. Installeer MongoDB.

    ```bash
    sudo apt-get install -y mongodb
    ```

4. Start de server.

    ```bash
    sudo service mongodb start
    ```

5. Het [body-parser](https://www.npmjs.com/package/body-parser-json)-pakket dient ook te worden geïnstalleerd om de JSON te verwerken die in aanvragen aan de server wordt doorgegeven.

    Installeer de npm-pakketmanager.

    ```bash
    sudo apt-get install npm
    ```

    Installeer het pakket body-parser.
    
    ```bash
    sudo npm install body-parser
    ```

6. Maak een map met de naam *Books* en voeg er een bestand aan toe met de naam *server.js*, dat de configuratie voor de webserver bevat.

    ```javascript
    var express = require('express');
    var bodyParser = require('body-parser');
    var app = express();
    app.use(express.static(__dirname + '/public'));
    app.use(bodyParser.json());
    require('./apps/routes')(app);
    app.set('port', 3300);
    app.listen(app.get('port'), function() {
        console.log('Server up: http://localhost:' + app.get('port'));
    });
    ```

## <a name="install-express-and-set-up-routes-to-the-server"></a>Express installeren en routes naar de server instellen

[Express](https://expressjs.com) is een minimaal en flexibel Node.js-webtoepassingsframework dat functies biedt voor web- en mobiele toepassingen. In deze zelfstudie wordt Express gebruikt om boekgegevens door te geven naar en vanaf de MongoDB-database. [Mongoose](https://mongoosejs.com) biedt een eenvoudige, op schema's gebaseerde oplossing voor het modelleren van uw toepassingsgegevens. In deze zelfstudie wordt Mongoose gebruikt om een boekschema voor de database te bieden.

1. Installeer Express en Mongoose.

    ```bash
    sudo npm install express mongoose
    ```

2. Maak in de map *Books* een map met de naam *apps* en voeg een bestand toe met de naam *routes.js* en waarin de express-routes zijn gedefinieerd.

    ```javascript
    var Book = require('./models/book');
    module.exports = function(app) {
      app.get('/book', function(req, res) {
        Book.find({}, function(err, result) {
          if ( err ) throw err;
          res.json(result);
        });
      }); 
      app.post('/book', function(req, res) {
        var book = new Book( {
          name:req.body.name,
          isbn:req.body.isbn,
          author:req.body.author,
          pages:req.body.pages
        });
        book.save(function(err, result) {
          if ( err ) throw err;
          res.json( {
            message:"Successfully added book",
            book:result
          });
        });
      });
      app.delete("/book/:isbn", function(req, res) {
        Book.findOneAndRemove(req.query, function(err, result) {
          if ( err ) throw err;
          res.json( {
            message: "Successfully deleted the book",
            book: result
          });
        });
      });
      var path = require('path');
      app.get('*', function(req, res) {
        res.sendfile(path.join(__dirname + '/public', 'index.html'));
      });
    };
    ```

3. Maak in de map *apps* een map met de naam *models* en voeg een bestand toe met de naam *book.js* en waarin de configuratie van het boekmodel is gedefinieerd.  

    ```javascript
    var mongoose = require('mongoose');
    var dbHost = 'mongodb://localhost:27017/test';
    mongoose.connect(dbHost);
    mongoose.connection;
    mongoose.set('debug', true);
    var bookSchema = mongoose.Schema( {
      name: String,
      isbn: {type: String, index: true},
      author: String,
      pages: Number
    });
    var Book = mongoose.model('Book', bookSchema);
    module.exports = mongoose.model('Book', bookSchema); 
    ```

## <a name="access-the-routes-with-angularjs"></a>Routes openen met AngularJS

[AngularJS](https://angularjs.org) biedt een webframework voor het maken van dynamische weergaven in uw webtoepassingen. In deze zelfstudie wordt AngularJS gebruikt om de webpagina verbinding te laten maken met Express en acties op de boekdatabase uit te voeren.

1. Wijzig de directory weer in *Books* (`cd ../..`), maak een map met de naam *public* en voeg een bestand toe met de naam *script.js* en waarin de configuratie voor de controller is gedefinieerd.

    ```javascript
    var app = angular.module('myApp', []);
    app.controller('myCtrl', function($scope, $http) {
      $http( {
        method: 'GET',
        url: '/book'
      }).then(function successCallback(response) {
        $scope.books = response.data;
      }, function errorCallback(response) {
        console.log('Error: ' + response);
      });
      $scope.del_book = function(book) {
        $http( {
          method: 'DELETE',
          url: '/book/:isbn',
          params: {'isbn': book.isbn}
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
      $scope.add_book = function() {
        var body = '{ "name": "' + $scope.Name + 
        '", "isbn": "' + $scope.Isbn +
        '", "author": "' + $scope.Author + 
        '", "pages": "' + $scope.Pages + '" }';
        $http({
          method: 'POST',
          url: '/book',
          data: body
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
    });
    ```
    
2. Maak in de map *public* een bestand met de naam *index.html* en waarin de webpagina is gedefinieerd.

    ```html
    <!doctype html>
    <html ng-app="myApp" ng-controller="myCtrl">
      <head>
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
        <script src="script.js"></script>
      </head>
      <body>
        <div>
          <table>
            <tr>
              <td>Name:</td> 
              <td><input type="text" ng-model="Name"></td>
            </tr>
            <tr>
              <td>Isbn:</td>
              <td><input type="text" ng-model="Isbn"></td>
            </tr>
            <tr>
              <td>Author:</td> 
              <td><input type="text" ng-model="Author"></td>
            </tr>
            <tr>
              <td>Pages:</td>
              <td><input type="number" ng-model="Pages"></td>
            </tr>
          </table>
          <button ng-click="add_book()">Add</button>
        </div>
        <hr>
        <div>
          <table>
            <tr>
              <th>Name</th>
              <th>Isbn</th>
              <th>Author</th>
              <th>Pages</th>
            </tr>
            <tr ng-repeat="book in books">
              <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
              <td>{{book.name}}</td>
              <td>{{book.isbn}}</td>
              <td>{{book.author}}</td>
              <td>{{book.pages}}</td>
            </tr>
          </table>
        </div>
      </body>
    </html>
    ```

##  <a name="run-the-application"></a>De toepassing uitvoeren

1. Wijzig de directory weer in *Books* (`cd ..`) en start de server door de volgende opdracht uit te voeren:

    ```bash
    nodejs server.js
    ```

2. Open in een webbrowser het adres dat u voor de VM hebt opgenomen. Bijvoorbeeld *http:\//13.72.77.9:3300*. Er verschijnt een pagina die er ongeveer als volgt uitziet:

    ![Boekrecord](media/tutorial-mean/meanstack-init.png)

3. Voer gegevens in de tekstvakken in en klik op **Toevoegen**. Bijvoorbeeld:

    ![Boekrecord toevoegen](media/tutorial-mean/meanstack-add.png)

4. Als u de pagina hebt vernieuwd, verschijnt een pagina die er ongeveer als volgt uitziet:

    ![Boekrecords vermelden](media/tutorial-mean/meanstack-list.png)

5. Klik op **Verwijderen** en verwijder de boekrecord uit de database.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een webtoepassing gemaakt die boekrecords bijhoudt met behulp van een MEAN-stack op een VM in Linux. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een Linux-VM maken
> * Node.js installeren
> * MongoDB installeren en de server instellen
> * Express installeren en routes naar de server instellen
> * Routes openen met AngularJS
> * De toepassing uitvoeren

Ga door naar de volgende zelfstudie om te leren hoe u webservers kunt beveiligen met behulp van SSL-certificaten.

> [!div class="nextstepaction"]
> [Webserver beveiligen met SSL](tutorial-secure-web-server.md)
