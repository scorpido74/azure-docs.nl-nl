---
title: Bouw een python-fles-web-app met de API van Azure Cosmos DB voor MongoDB
description: Biedt een voorbeeld van Python Flask-code dat u kunt gebruiken om verbinding te maken met de Azure Cosmos DB-API's voor MongoDB en er query's op uit te voeren.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 43f4cf7e4008aa01a26c48a8e99f7465eeeb234b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061740"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Snelstartgids: een python-app bouwen met de API van Azure Cosmos DB voor MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

In deze Quick Start gebruikt u een Azure Cosmos DB voor Mongo DB API-account of de Azure Cosmos DB-emulator om een python-taak-web-app te klonen die is gekloond van GitHub. Azure Cosmos DB is een database service met meerdere modellen waarmee u snel documenten, tabellen, sleutel waarden en grafische data bases met globale distributie en mogelijkheden voor horizontale schaal kunt maken en er query's op uitvoert.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Of [Probeer gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) zonder een Azure-abonnement. U kunt ook de Azure Cosmos DB- [emulator](local-emulator.md)gebruiken. 
- [Python 3.6 +](https://www.python.org/downloads/)
- [Visual Studio code](https://code.visualstudio.com/Download) met de [python-extensie](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python).

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een Flask MongoDB-app klonen vanaf GitHub, de verbindingsreeks instellen en de app uitvoeren. U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken.

1. Open een opdrachtprompt, maak een nieuwe map met de naam git-samples en sluit vervolgens de opdrachtprompt.

    ```bash
    md "C:\git-samples"
    ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

    ```bash
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Voer de volgende opdracht uit om de Python-modules te installeren.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Open de map in Visual Studio Code.

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources in de code worden gemaakt, kunt u de volgende codefragmenten bekijken. Sla dit anders over en ga naar [De web-app uitvoeren](#run-the-web-app). 

De volgende code fragmenten zijn allemaal afkomstig uit het *app.py* -bestand en gebruiken de Connection String voor de lokale Azure Cosmos DB-emulator. Het wachtwoord moet worden opgesplitst, zoals hieronder wordt weergegeven, omdat de slashes anders niet kunnen worden geparseerd.

* Initialiseer de MongoDB-client, haal de database op en doorloop de verificatie.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* Haal de verzameling op of maak er een als er nog geen is.

    ```python
    todos = db.todo #Select the collection
    ```

* De app maken

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>De web-app uitvoeren

1. Controleer of de Azure Cosmos DB-emulator wordt uitgevoerd.

2. Open een terminalvenster en ga met `cd` naar de map waarin de app wordt opgeslagen.

3. Stel de omgevings variabele voor de kolf-app in op `set FLASK_APP=app.py`, `$env:FLASK_APP = app.py` voor Power shell-editors of `export FLASK_APP=app.py` als u een Mac gebruikt. 

4. Voer de app uit met `flask run` en blader naar *http:\//127.0.0.1:5000/* .

5. Voeg taken toe en verwijder ze, en kijk hoe ze zijn toegevoegd en gewijzigd in de verzameling.

## <a name="create-a-database-account"></a>Een databaseaccount maken

Als u de code wilt testen op een live Azure Cosmos DB-account, gaat u naar de Azure Portal om een account te maken.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Als u de code wilt testen op het Live Azure Cosmos DB-account, kunt u uw connection string-gegevens ophalen. Kopieer deze vervolgens in de app.

1. Selecteer in uw Azure Cosmos DB-account in de Azure Portal in de linkernavigatiebalk **verbindings reeks**en selecteer **sleutels voor lezen/schrijven**. U gebruikt de Kopieer knoppen aan de rechter kant van het scherm om de gebruikers naam, het connection string en het wacht woord te kopiëren. 

2. Open het bestand *app.py* in de hoofdmap.

3. Kopieer de waarde van **gebruikersnaam** van de portal (gebruik hiervoor de kopieerknop) en maak hiervan de waarde van **gebruikersnaam** in het bestand *app.py*.

4. Kopieer vervolgens uw **Connection String** waarde vanuit de portal en geef deze als waarde aan de **MongoClient** in uw *app.py* -bestand.

5. Kopieer ten slotte de waarde van **wachtwoord** van de portal en maak hiervan de waarde van **wachtwoord** in het bestand *app.py*.

U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB. U kunt deze nu net als voorheen uitvoeren.

## <a name="deploy-to-azure"></a>Implementeren in Azure

Als u deze app wilt implementeren, kunt u een nieuwe web-app maken in Azure en continue implementatie inschakelen met een fork van deze GitHub-opslagplaats. Volg deze [zelfstudie](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment) voor het instellen van continue implementatie met GitHub in Azure.

Wanneer u Azure implementeert, moet u de sleutels van uw toepassing verwijderen en controleren of de onderstaande sectie als opmerking is opgenomen:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Vervolgens moet u uw MONGOURL, MONGO_PASSWORD en MONGO_USERNAME toevoegen aan de toepassingsinstellingen. In deze [zelfstudie](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings) vindt u meer informatie over toepassingsinstellingen in Azure Web Apps.

Als u geen Fork van deze opslag plaats wilt maken, kunt u ook de onderstaande knop **implementeren naar Azure** selecteren. Vervolgens gaat u naar Azure en stelt u de toepassings instellingen in met uw Azure Cosmos DB-account gegevens.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Als u van plan bent uw code in GitHub of andere opties voor bronbeheer op te slaan, moet u uw verbindingsreeksen uit de code verwijderen. Ze kunnen ook worden ingesteld met toepassingsinstellingen voor de web-app.

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos DB maakt voor Mongo DB API-account en hoe u de Azure Cosmos DB-emulator gebruikt voor het uitvoeren van een python-taak die is gekloond van GitHub. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [MongoDB-gegevens importeren in Azure Cosmos DB](mongodb-migrate.md)
