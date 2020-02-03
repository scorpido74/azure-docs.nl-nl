---
title: 'Snelstartgids: een python-app bouwen met Azure Cosmos DB SQL-API-account'
description: Is een Python-codevoorbeeld dat u kunt gebruiken om verbinding te maken met en gegevens op te vragen uit de SQL API van Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 11/03/2019
ms.author: sngun
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
ms.openlocfilehash: 1e0016e8ce6bc9178befd1a5ca96aa2554929ac5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719531"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Snelstartgids: een python-toepassing bouwen met een Azure Cosmos DB SQL-API-account

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Deze snelstart laat zien hoe u een [SQL API](sql-api-introduction.md)-account van Azure Cosmos DB, een documentdatabase en een container kunt maken met behulp van de Azure-portal. Vervolgens ontwikkelt u een console-app die is gebouwd met de Python SDK voor [SQL API](sql-api-sdk-python.md) en voert u deze uit.

Azure Cosmos DB is de globaal gedistribueerde multimodel-databaseservice van Microsoft. U kunt snel documenten, sleutel/waarde, brede kolom-en grafiek databases maken en doorzoeken. Al deze bewerkingen profiteren van de distributie en schaal van Azure Cosmos DB.

In deze Quick Start wordt versie 4 van de [python-SDK](https://pypi.org/project/azure-cosmos/#history)gebruikt.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>Vereisten

* [Python 3.6 +](https://www.python.org/downloads/), met het `python` uitvoer bare bestand dat beschikbaar is in uw `PATH`.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Python-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)

## <a name="create-a-database-account"></a>Een databaseaccount maken

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Een container toevoegen

U kunt nu het hulp programma Data Explorer in de Azure Portal gebruiken om een Data Base en container te maken. 

1. Selecteer **Data Explorer** > **nieuwe container**. 
    
    Het gebied **container toevoegen** wordt helemaal rechts weer gegeven. mogelijk moet u naar rechts scrollen om het weer te geven.

    ![Het deel venster Azure Portal Data Explorer, container toevoegen](./media/create-sql-api-python/azure-cosmosdb-data-explorer.png)

2. Voer op de pagina **container toevoegen** de instellingen voor de nieuwe container in.

    |Instelling|Voorgestelde waarde|Beschrijving
    |---|---|---|
    |**Database-id**|Taken|Voer *ToDoList* in als de naam voor de nieuwe data base. Databasenamen moeten tussen de 1 en 255 tekens zijn en mogen geen `/, \\, #, ?` bevatten en mogen niet eindigen met een spatie. Schakel de optie **doorvoer database inrichten** in, zodat u de door Voer die is ingericht voor de data base, kunt delen in alle containers in de data base. Deze optie helpt ook bij het besparen van kosten. |
    |**Doorvoer**|400|De door Voer bij 400 aanvraag eenheden per seconde (RU/s) behouden. U kunt de doorvoer later opschalen als u de latentie wilt beperken.| 
    |**Container-ID**|Items|Voer *items* in als de naam voor de nieuwe container. Container-Id's hebben dezelfde teken vereisten als database namen.|
    |**Partitie sleutel**| /category| Het voor beeld dat in dit artikel wordt beschreven, maakt gebruik van *Category* als de partitie sleutel.|
    
    Naast de voor gaande instellingen kunt u eventueel **unieke sleutels** voor de container toevoegen. In dit voorbeeld laten we het veld leeg. Unieke sleutels bieden ontwikkelaars de mogelijkheid om een gegevensintegriteitslaag aan de database toe te voegen. Door een beleid voor unieke sleutels te maken tijdens het maken van een container, zorgt u ervoor dat een of meer waarden per partitie sleutel uniek zijn. Raadpleeg het artikel [Unique keys in Azure Cosmos DB](unique-keys.md) (Unieke sleutels in Azure Cosmos DB) voor meer informatie.
    
    Selecteer **OK**. De Data Explorer de nieuwe data base en container worden weer gegeven.

## <a name="add-sample-data"></a>Voorbeeldgegevens toevoegen

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Uw gegevens opvragen

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een SQL API-app klonen vanuit GitHub, de verbindingsreeks instellen en de app uitvoeren.

1. Open een opdrachtprompt, maak een nieuwe map met de naam git-samples en sluit vervolgens de opdrachtprompt.

    ```cmd
    md "git-samples"
    ```
   Als u een bash-prompt gebruikt, moet u in plaats daarvan de volgende opdracht gebruiken:

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

1. Klik in het [Azure Portal](https://portal.azure.com/)in uw Azure Cosmos-account in de linkernavigatiebalk op **sleutels**. In de volgende stap gebruikt u de kopieerknoppen aan de rechterkant van het scherm om de **URI** en **Primaire sleutel** in het `cosmos_get_started.py`-bestand te kopiëren.

    ![Haal een toegangs sleutel en URI op in de sleutel instellingen in de Azure Portal](./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png)

2. Open het `cosmos_get_started.py`-bestand in \git-samples\azure-Cosmos-DB-python-Getting-Started in Visual Studio code.

3. Kopieer uw **URI** -waarde vanuit de portal (met de Kopieer knop) en geef deze als waarde aan de **eindpunt** variabele in ``cosmos_get_started.py``. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Kopieer vervolgens de waarde van uw **primaire sleutel** uit de portal en geef deze als waarde aan de **sleutel** in ``cosmos_get_started.py``. U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB. 

    `key = 'FILLME'`

5. Sla het bestand ``cosmos_get_started.py`` op.

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Meer informatie over de database resources die zijn gemaakt in code of overs Laan om [uw Connection String](#update-your-connection-string)bij te werken.

De volgende codefragmenten zijn allemaal afkomstig uit het bestand `cosmos_get_started.py`.

* De CosmosClient wordt geïnitialiseerd. Zorg ervoor dat u de waarden voor het eind punt en de sleutel bijwerkt, zoals beschreven in de sectie [uw Connection String bijwerken](#update-your-connection-string) . 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* Er wordt een nieuwe database gemaakt.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* Er wordt een nieuwe container gemaakt, met 400 RU/s, [ingerichte door Voer](request-units.md). We kiezen `lastName` als de [partitie sleutel](partitioning-overview.md#choose-partitionkey), waardoor we efficiënte query's kunnen uitvoeren op deze eigenschap. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Bepaalde items worden toegevoegd aan de container. Containers zijn een verzameling items (JSON-documenten) die een gevarieerd schema kunnen hebben. De hulp methoden ```get_[name]_family_item``` retour neren van een familie die zijn opgeslagen in Azure Cosmos DB als JSON-documenten.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* Punt lezen (zoek acties voor sleutel waarden) worden uitgevoerd met behulp van de `read_item` methode. De [ru-kosten](request-units.md) van elke bewerking worden afgedrukt.
    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* Er wordt een query uitgevoerd met behulp van de SQL-query syntaxis. Omdat we de partitie sleutel waarden van ```lastName``` in de component WHERE gebruiken, wordt deze query op efficiënte wijze door Azure Cosmos DB gerouteerd naar de relevante partities, waardoor de prestaties worden verbeterd.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>De app kunt uitvoeren

1. Selecteer **View** > **Command Palette** in Visual Studio Code. 

2. Voer na de prompt **Python: Select Interpreter** in en selecteer de te gebruiken Python-versie.

    De voettekst in Visual Studio Code wordt bijgewerkt om de geselecteerde interpreter aan te geven. 

3. Selecteer **View** > **Integrated Terminal** om de in Visual Studio Code geïntegreerde terminal te openen.

4. Controleer of u zich in het venster van de geïntegreerde terminal in de map azure-cosmos-db-python-getting-started bevindt. Zo niet, voer dan de volgende opdracht uit om naar de voorbeeldmap over te schakelen. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Voer de volgende opdracht uit om het azure-cosmos-pakket te installeren. 

    ```python
    pip install --pre azure-cosmos
    ```

    Als u een foutmelding krijgt over geweigerde toegang bij het installeren van azure-cosmos, dient u [VS Code als beheerder uit te voeren](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Voer de volgende opdracht uit om het voor beeld uit te voeren en nieuwe documenten te maken en op te slaan in Azure Cosmos DB.

    ```python
    python cosmos_get_started.py
    ```

7. Als u wilt bevestigen dat de nieuwe items zijn gemaakt en opgeslagen, selecteert u in de Azure Portal **Data Explorer** > **AzureSampleFamilyDatabase** > **items**. De items weer geven die zijn gemaakt. Hier volgt een voor beeld van een JSON-document voor de serie van de splinter:

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

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos-account maakt, een container maakt met behulp van de Data Explorer en een app uitvoert. Nu kunt u aanvullende gegevens in uw Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB voor de SQL API](import-data.md)


