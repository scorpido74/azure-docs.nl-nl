---
title: Een Node.js Mongoose-toepassing verbinden met Azure Cosmos DB
description: Meer informatie over het gebruik van het Mongoose Framework om gegevens op te slaan en te beheren in Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: ff4455571aa5cfa5c9214bdf18af1853b0cef352
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585407"
---
# <a name="connect-a-nodejs-mongoose-application-to-azure-cosmos-db"></a>Een Node.js Mongoose-toepassing verbinden met Azure Cosmos DB

Deze zelfstudie laat zien hoe u het [Mongoose Framework kunt](https://mongoosejs.com/) gebruiken bij het opslaan van gegevens in Cosmos DB. We gebruiken de API van Azure Cosmos DB voor MongoDB voor deze walkthrough. Voor wie niet bekend is met Mongoose: dit is een framework voor objectmodellering voor MongoDB in Node.js en biedt een ongecompliceerde, op schema's gebaseerde oplossing voor het modelleren van uw toepassingsgegevens.

Cosmos DB is microsoft's wereldwijd gedistribueerde multi-model database service. U kunt snel databases maken van documenten, sleutel/waarde-paren en grafieken en hier query's op uitvoeren. Deze databases genieten allemaal het voordeel van de globale distributie en horizontale schaalmogelijkheden die ten grondslag liggen aan Cosmos DB.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) versie v0.10.29 of hoger.

## <a name="create-a-cosmos-account"></a>Een Cosmos-account maken

Laten we een Cosmos-account aanmaken. Als u al een account hebt dat u wilt gebruiken, kunt u verder naar de stap Uw Node.js-toepassing instellen. Als u de Azure Cosmos DB Emulator gebruikt, voert u de stappen van [Azure Cosmos DB Emulator](local-emulator.md) uit om de emulator in te stellen en verder te gaan naar Het instellen van uw Node.js-toepassing.

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

### <a name="create-a-database"></a>Een database maken 
In deze toepassing behandelen we twee manieren om verzamelingen te maken in Azure Cosmos DB: 
- **Elk objectmodel opslaan in een aparte verzameling:** We raden u aan [een database met speciale doorvoer te maken.](set-throughput.md#set-throughput-on-a-database) Met behulp van deze capaciteit model geeft u een betere kostenefficiëntie.

    :::image type="content" source="./media/mongodb-mongoose/db-level-throughput.png" alt-text="Node.js-zelfstudie - Schermafbeelding van de Azure-portal, waarin wordt weergegeven hoe u een database maakt in de Data Explorer voor een Azure Cosmos DB-account, voor gebruik met de Mongoose Node-module":::

- **Alle objectmodellen opslaan in één Cosmos DB-verzameling:** Als u alle modellen liever in één verzameling opslaat, u gewoon een nieuwe database maken zonder de optie Doorvoervoorziening te selecteren. Met behulp van dit capaciteitsmodel wordt elke verzameling gemaakt met een eigen doorvoercapaciteit voor elk objectmodel.

Nadat u de database hebt gemaakt, gebruikt `COSMOSDB_DBNAME` u de naam in de onderstaande omgevingsvariabele.

## <a name="set-up-your-nodejs-application"></a>Uw Node.js-toepassing instellen

>[!Note]
> Als u alleen de voorbeeldcode wilt doorlopen in plaats van de toepassing zelf te installeren, kopieert u het [voorbeeld](https://github.com/Azure-Samples/Mongoose_CosmosDB) dat voor deze zelfstudie is gebruikt en bouwt u de Node.js Mongoose-toepassing voor Azure Cosmos DB.

1. Voer de volgende opdracht uit in een knooppuntopdrachtprompt om een Node.js-toepassing in de map van uw keuze te maken.

    ```npm init```

    Beantwoord de vragen en uw project is klaar voor gebruik.

2. Voeg een nieuw bestand toe aan de map en noem het ```index.js```.
3. Installeer de vereiste pakketten met een van de ```npm install```-opties:
   * Mongoose: ```npm install mongoose@5 --save```

     > [!Note]
     > De Mongoose voorbeeld verbinding hieronder is gebaseerd op Mongoose 5 +, die is veranderd sinds eerdere versies.
    
   * Dotenv (als u uw geheimen wilt laden uit een .env-bestand): ```npm install dotenv --save```

     >[!Note]
     > Met de ```--save```-markering wordt de afhankelijkheid toegevoegd aan het package.json-bestand.

4. Importeer de afhankelijkheden in uw index.js-bestand.

    ```JavaScript
   var mongoose = require('mongoose');
   var env = require('dotenv').config();   //Use the .env file to load the variables
    ```

5. Voeg uw Cosmos-DB-verbindingsreeks en de naam van de Cosmos DB toe aan het ```.env```-bestand. Vervang de tijdelijke aanduidingen {cosmos-account-name} en {dbname} door uw eigen Cosmos-accountnaam en databasenaam, zonder de bracesymbolen.

    ```JavaScript
   # You can get the following connection details from the Azure portal. You can find the details on the Connection string pane of your Azure Cosmos account.

   COSMODDB_USER = "<Azure Cosmos account's user name, usually the database account name>"
   COSMOSDB_PASSWORD = "<Azure Cosmos account password, this is one of the keys specified in your account>"
   COSMOSDB_DBNAME = "<Azure Cosmos database name>"
   COSMOSDB_HOST= "<Azure Cosmos Host name>"
   COSMOSDB_PORT=10255
    ```

6. Maak verbinding met Cosmos DB met behulp van het Mongoose-framework door de volgende code toe te voegen aan het einde van index.js.
    ```JavaScript
   mongoose.connect("mongodb://"+process.env.COSMOSDB_HOST+":"+process.env.COSMOSDB_PORT+"/"+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb", {
      auth: {
        user: process.env.COSMODDB_USER,
        password: process.env.COSMOSDB_PASSWORD
      }
    })
    .then(() => console.log('Connection to CosmosDB successful'))
    .catch((err) => console.error(err));
    ```
    >[!Note]
    > Hier worden de omgevingsvariabelen geladen met behulp van process.env. {variableName} met het npm-pakket 'dotenv'.

    Zodra u verbonden bent met Azure Cosmos DB, kunt u objectmodellen gaan instellen in Mongoose.

## <a name="best-practices-for-using-mongoose-with-cosmos-db"></a>Aanbevolen procedures voor het gebruik van Mongoose met Cosmos DB

Voor elk model dat u maakt, creëert Mongoose een nieuwe collectie. Dit kan het beste worden aangepakt met de [optie Doorvoer databaseniveau](set-throughput.md#set-throughput-on-a-database), die eerder is besproken. Om één collectie te gebruiken, moet u Mongoose [Discriminators](https://mongoosejs.com/docs/discriminators.html)gebruiken. Discriminators zijn een mechanisme voor schema-overname. Hiermee kunt u meerdere modellen met overlappende schema's boven op dezelfde onderliggende MongoDB-verzameling hebben.

U kunt de verschillende gegevensmodellen opslaan in dezelfde verzameling en vervolgens een filtercomponent gebruiken op het moment dat de query voor het ophalen van de benodigde gegevens wordt uitgevoerd. Laten we door elk van de modellen lopen.

### <a name="one-collection-per-object-model"></a>Eén verzameling per objectmodel

In dit gedeelte wordt onderzocht hoe u dit bereiken met de API van Azure Cosmos DB voor MongoDB. Deze methode is onze aanbevolen aanpak, omdat het u in staat stelt om kosten en capaciteit te beheersen. Als gevolg hiervan is het aantal aanvraageenheden in de database niet afhankelijk van het aantal objectmodellen. Dit is het standaard bedrijfsmodel voor Mongoose, dus misschien bent u hier bekend mee.

1. Open uw ```index.js``` opnieuw.

1. Maak de schemadefinitie voor 'Family'.

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. Maak een object voor 'Family'.

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. Tot slot, laten we het object opslaan in Cosmos DB. Hiermee maakt u een onderliggende verzameling.

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. We gaan nu een ander schema en object maken. Deze keer maken we er een voor 'Vacation Destinations' waarin de 'families' mogelijk geïnteresseerd in zijn.
   1. Net als de laatste keer maken we het schema
      ```JavaScript
      const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
       name: String,
       country: String
      }));
      ```

   1. Maak een voorbeeldobject (u kunt meerdere objecten toevoegen aan dit schema) en sla het op.
      ```JavaScript
      const vacaySpot = new VacationDestinations({
       name: "Honolulu",
       country: "USA"
      });

      vacaySpot.save((err, saveVacay) => {
       console.log(JSON.stringify(saveVacay));
      });
      ```

1. Als u nu naar de Azure-portal gaat, ziet u twee verzamelingen die zijn gemaakt in Cosmos DB.

    ![Node.js-zelfstudie - Schermafbeelding van de Azure-portal met een Azure Cosmos DB-account, met meerdere verzamelingsnamen gemarkeerd - Knooppuntdatabase][multiple-coll]

1. Tot slot, laten we de gegevens van Cosmos DB lezen. Aangezien we gebruikmaken van het standaardmodel van Mongoose, zijn de leesbewerkingen hetzelfde als alle andere leesbewerkingen met Mongoose.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Mongoose-discriminators gebruiken voor het opslaan van gegevens in één enkele verzameling

Bij deze methode gebruiken we [Mongoose Discriminators](https://mongoosejs.com/docs/discriminators.html) om te helpen optimaliseren voor de kosten van elke collectie. Met discriminators kunt u een onderscheidende 'sleutel' definiëren, zodat u kunt opslaan in, onderscheid maken tussen en filteren op verschillende objectmodellen.

Hier maken we een basisobjectmodel, definiëren we een onderscheidende sleutel en voegen we 'Family' en 'VacationDestinations' toe aan het basismodel.

1. We gaan de basisconfiguratie instellen en de discriminatorsleutel definiëren.

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. Vervolgens gaan we het algemene objectmodel definiëren

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. We definiëren nu het model 'Family'. U ziet hier dat we ```commonModel.discriminator``` gebruiken in plaats van ```mongoose.model```. We voegen de basisconfiguratie ook toe aan het mongoose-schema. Hier is de discriminatorKey dus ```FamilyType```.

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. Op dezelfde manier gaan we nu een ander schema toevoegen voor de 'VacationDestinations'. Hier is de discriminatorKey ```VacationDestinationsType```.

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. Tot slot maken we objecten voor het model en slaan het op.
   1. We gaan objecten toevoegen aan het model 'Family'.
      ```JavaScript
      const family_common = new Family_common({
       lastName: "Volum",
       parents: [
           { firstName: "Thomas" },
           { firstName: "Mary Kay" }
       ],
       children: [
           { firstName: "Ryan", gender: "male", grade: 8 },
           { firstName: "Patrick", gender: "male", grade: 7 }
       ],
       pets: [
           { givenName: "Blackie" }
       ],
       address: { country: "USA", state: "WA", city: "Seattle" }
      });

      family_common.save((err, saveFamily) => {
       console.log("Saved: " + JSON.stringify(saveFamily));
      });
      ```

   1. Vervolgens voegen we objecten toe aan het model 'VacationDestinations' en slaan het op.
      ```JavaScript
      const vacay_common = new Vacation_common({
       name: "Honolulu",
       country: "USA"
      });

      vacay_common.save((err, saveVacay) => {
       console.log("Saved: " + JSON.stringify(saveVacay));
      });
      ```

1. Als u nu terug naar Azure Portal gaat, ziet u dat er slechts één verzameling met de naam ```alldata``` met zowel 'Family'- als 'VacationDestinations'-gegevens heeft.

    ![Node.js-zelfstudie - Schermafbeelding van de Azure-portal met een Azure Cosmos DB-account, met de naam van de verzameling gemarkeerd - Knooppuntdatabase][alldata]

1. U ziet ook dat elk object een ander kenmerk heeft dat wordt aangeroepen als ```__type```, waarmee u onderscheid tussen de twee verschillende objectmodellen kunt maken.

1. Tot slot lezen we de gegevens die zijn opgeslagen in Azure Cosmos DB. Mongoose zorgt voor het filteren van gegevens op basis van het model. U hoeft dus niets anders te doen tijdens het lezen van gegevens. Geef alleen uw model op (in dit geval ```Family_common```) en Mongoose zorgt dat wordt gefilterd op 'DiscriminatorKey'.

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Zoals u ziet, is het eenvoudig om met Mongoose-discriminators te werken. Dus als u een app hebt die het Mongoose-framework gebruikt, is deze zelfstudie een manier om uw toepassing aan de slag te krijgen met de API van Azure Cosmos voor MongoDB zonder al te veel wijzigingen.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van Studio 3T](mongodb-mongochef.md) met de API voor MongoDB van Azure Cosmos DB.
- Meer informatie over het [gebruik van Robo 3T](mongodb-robomongo.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[multiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png
[dbleveltp]: ./media/mongodb-mongoose/db-level-throughput.png
