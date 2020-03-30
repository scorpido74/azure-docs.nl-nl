---
title: 'Snelstart: een Python-app bouwen met Azure Cosmos DB SQL API-account'
description: Is een Python-codevoorbeeld dat u kunt gebruiken om verbinding te maken met en gegevens op te vragen uit de SQL API van Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 03/09/2020
ms.author: sngun
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
ms.openlocfilehash: 10247e22b3fbe1250a15b06a0cce974905ca6b7f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78942617"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Snelstart: een Python-toepassing bouwen met een Azure Cosmos DB SQL API-account

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

In deze quickstart maakt en beheert u een Azure Cosmos DB SQL API-account vanuit de Azure-portal en vanuit Visual Studio Code met een Python-app gekloond vanuit GitHub. Azure Cosmos DB is een databaseservice met meerdere modellen waarmee u snel document-, tabel-, sleutelwaarde- en grafiekdatabases maken en opvragen met globale distributie- en horizontale schaalmogelijkheden.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Of [probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement. U ook de [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) gebruiken met een URI van `https://localhost:8081` en de sleutel. `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`
- [Python 3.6+](https://www.python.org/downloads/), `python` met de `PATH`uitvoerbare in uw .
- [Visual Studio Code](https://code.visualstudio.com/).
- De [Python-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview).
- [Git.](https://www.git-scm.com/downloads) 

## <a name="create-a-database-account"></a>Een databaseaccount maken

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Een container toevoegen

U nu het hulpprogramma Gegevensverkenner in de Azure-portal gebruiken om een database en container te maken. 

1. Selecteer Nieuwe**container** **van Data Explorer** > . 
    
    Het gebied **Container toevoegen** wordt helemaal rechts weergegeven, het kan zijn dat u naar rechts moet scrollen om het te zien.

    ![Azure Portal Data Explorer, deelvenster Container toevoegen](./media/create-sql-api-python/azure-cosmosdb-data-explorer.png)

2. Voer op de pagina **Container toevoegen** de instellingen voor de nieuwe container in.

    |Instelling|Voorgestelde waarde|Beschrijving
    |---|---|---|
    |**Database-id**|Taken|Voer *Taken* in als de naam voor de nieuwe database. Databasenamen moeten tussen de 1 en 255 tekens zijn en mogen geen `/, \\, #, ?` bevatten en mogen niet eindigen met een spatie. Controleer de optie **Voorhet overslaginrichten** van de database, hiermee u de overslag die is ingericht met de database delen over alle containers in de database. Deze optie helpt ook bij kostenbesparingen. |
    |**Doorvoer**|400|Laat de doorvoer op 400 aanvraageenheden per seconde (RU/s). U kunt de doorvoer later opschalen als u de latentie wilt beperken.| 
    |**Container-ID**|Items|Voer *Items in* als de naam voor uw nieuwe container. Voor id's van containers gelden dezelfde tekenvereisten als voor databasenamen.|
    |**Partitiesleutel**| /category| In het voorbeeld dat in dit artikel wordt beschreven, wordt */categorie* als partitiesleutel gebruikt.|
    
    Naast de voorgaande instellingen u optioneel **unieke sleutels** voor de container toevoegen. In dit voorbeeld laten we het veld leeg. Unieke sleutels bieden ontwikkelaars de mogelijkheid om een gegevensintegriteitslaag aan de database toe te voegen. Door een uniek sleutelbeleid te maken tijdens het maken van een container, zorgt u voor de uniciteit van een of meer waarden per partitiesleutel. Raadpleeg het artikel [Unique keys in Azure Cosmos DB](unique-keys.md) (Unieke sleutels in Azure Cosmos DB) voor meer informatie.
    
    Selecteer **OK**. In Data Explorer worden de nieuwe database en container weergegeven.

## <a name="add-sample-data"></a>Voorbeeldgegevens toevoegen

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Uw gegevens opvragen

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een SQL API-app klonen vanuit GitHub, de verbindingsreeks instellen en de app uitvoeren. Deze quickstart maakt gebruik van versie 4 van de [Python SDK.](https://pypi.org/project/azure-cosmos/#history)

1. Open een opdrachtprompt, maak een nieuwe map met de naam git-samples en sluit vervolgens de opdrachtprompt.

    ```cmd
    md "git-samples"
    ```
   Als u een bashprompt gebruikt, moet u in plaats daarvan de volgende opdracht gebruiken:

   ```bash
   mkdir "git-samples"
   ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app.

1. Selecteer **Sleutels** in de linkernavigatie in uw Azure Cosmos DB-account in de [Azure-portal.](https://portal.azure.com/) Gebruik de kopieerknoppen aan de rechterkant van het scherm om de **URI-** en **primaire sleutel** in de volgende stap naar het *bestand cosmos_get_started.py* te kopiëren.

    ![Een toegangssleutel en URI ophalen in de instellingen voor sleutels in de Azure-portal](./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png)

2. Open in Visual Studio Code het *bestand cosmos_get_started.py* in *\git-samples\azure-cosmos-db-python-getting-started*.

3. Kopieer uw **URI-waarde** van de portal (met behulp van de kopieerknop) en maak er de waarde van de **eindpuntvariabele** in *cosmos_get_started.py*. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Kopieer vervolgens de **primaire sleutelwaarde** van de portal en maak er de waarde van de **sleutel** van in *cosmos_get_started.py*. U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB. 

    `key = 'FILLME'`

5. Sla het *bestand cosmos_get_started.py op.*

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Meer informatie over de databasebronnen die in code zijn gemaakt, of ga vooruit naar [Uw verbindingstekenreeks bijwerken.](#update-your-connection-string)

De volgende fragmenten zijn allemaal afkomstig uit het *cosmos_get_started.py* bestand.

* De CosmosClient wordt geïnitialiseerd. Zorg ervoor dat u de waarden 'Eindpunt' en 'sleutel' bijwerkt zoals beschreven in de sectie [Uw verbindingstekenreeks bijwerken.](#update-your-connection-string) 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* Er wordt een nieuwe database gemaakt.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* Er wordt een nieuwe container gemaakt, met 400 RU/s [van de ingerichte doorvoer](request-units.md). We `lastName` kiezen als de [partitiesleutel,](partitioning-overview.md#choose-partitionkey)waarmee we efficiënte query's kunnen doen die op deze eigenschap filteren. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Bepaalde items worden toegevoegd aan de container. Containers zijn een verzameling items (JSON-documenten) die een gevarieerd schema kunnen hebben. De helpermethoden ```get_[name]_family_item``` retourneren weergaven van een gezin dat is opgeslagen in Azure Cosmos DB als JSON-documenten.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* Point reads (key value lookups) `read_item` worden uitgevoerd met behulp van de methode. We printen de [RU-lading](request-units.md) van elke bewerking uit.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* Een query wordt uitgevoerd met sql-querysyntaxis. Omdat we partitiesleutelwaarden van ```lastName``` in de WHERE-clausule gebruiken, zal Azure Cosmos DB deze query efficiënt doorsturen naar de relevante partities, waardoor de prestaties worden verbeterd.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>De app uitvoeren

1. Selecteer**opdrachtpalet** **weergeven** > in Visual Studio-code . 

2. Voer na de prompt **Python: Select Interpreter** in en selecteer de te gebruiken Python-versie.

    De voettekst in Visual Studio Code wordt bijgewerkt om de geselecteerde interpreter aan te geven. 

3. Selecteer Geïntegreerde terminal **weergeven** > **Integrated Terminal** om de geïntegreerde visual studiocode-terminal te openen.

4. Zorg er in het geïntegreerde terminalvenster voor dat u zich in de map *azure-cosmos-db-python-getting-started bevindt.* Zo niet, voer dan de volgende opdracht uit om naar de voorbeeldmap over te schakelen. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Voer de volgende opdracht uit om het azure-cosmos-pakket te installeren. 

    ```python
    pip install --pre azure-cosmos
    ```

    Als u een foutmelding krijgt over geweigerde toegang bij het installeren van azure-cosmos, dient u [VS Code als beheerder uit te voeren](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Voer de volgende opdracht uit om het voorbeeld uit te voeren en nieuwe documenten te maken en op te slaan in Azure Cosmos DB.

    ```python
    python cosmos_get_started.py
    ```

7. Als u wilt bevestigen dat de nieuwe items zijn gemaakt en opgeslagen, selecteert u in de **Azure-portal de** > optie**AzureSampleFamilyDatabase-items** > **Items**van Gegevensverkenner . Bekijk de items die zijn gemaakt. Hier vindt u bijvoorbeeld een voorbeeld van JSON-document voor de familie Andersen:
   
   ```json
   {
       "id": "Andersen-1569479288379",
       "lastName": "Andersen",
       "district": "WA5",
       "parents": [
           {
               "familyName": null,
               "firstName": "Thomas"
           },
           {
               "familyName": null,
               "firstName": "Mary Kay"
           }
       ],
       "children": null,
       "address": {
           "state": "WA",
           "county": "King",
           "city": "Seattle"
       },
       "registered": true,
       "_rid": "8K5qAIYtZXeBhB4AAAAAAA==",
       "_self": "dbs/8K5qAA==/colls/8K5qAIYtZXc=/docs/8K5qAIYtZXeBhB4AAAAAAA==/",
       "_etag": "\"a3004d78-0000-0800-0000-5d8c5a780000\"",
       "_attachments": "attachments/",
       "_ts": 1569479288
   }
   ```

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Cosmos DB-account maakt, een container maakt met de Data Explorer en een Python-app uitvoert in Visual Studio Code. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB voor de SQL API](import-data.md)


