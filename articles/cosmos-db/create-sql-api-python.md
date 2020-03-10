---
title: 'Snelstartgids: een python-app bouwen met Azure Cosmos DB SQL-API-account'
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
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942617"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Snelstartgids: een python-toepassing bouwen met een Azure Cosmos DB SQL-API-account

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

In deze Quick Start maakt en beheert u een Azure Cosmos DB SQL-API-account van de Azure Portal en van Visual Studio code met een python-app die is gekloond van GitHub. Azure Cosmos DB is een database service met meerdere modellen waarmee u snel documenten, tabellen, sleutel waarden en grafische data bases met globale distributie en mogelijkheden voor horizontale schaal kunt maken en er query's op uitvoert.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Of [Probeer gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) zonder een Azure-abonnement. U kunt ook de [Azure Cosmos DB emulator](https://aka.ms/cosmosdb-emulator) gebruiken met een URI van `https://localhost:8081` en de sleutel `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Python 3.6 +](https://www.python.org/downloads/), met de `python` uitvoer bare bestand in uw `PATH`.
- [Visual Studio Code](https://code.visualstudio.com/).
- De [python-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview).
- [Git](https://www.git-scm.com/downloads). 

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
    |**Database-id**|Taken|Voer *Taken* in als de naam voor de nieuwe database. Databasenamen moeten tussen de 1 en 255 tekens zijn en mogen geen `/, \\, #, ?` bevatten en mogen niet eindigen met een spatie. Schakel de optie **doorvoer database inrichten** in, zodat u de door Voer die is ingericht voor de data base, kunt delen in alle containers in de data base. Deze optie helpt ook bij het besparen van kosten. |
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

We gaan nu een SQL API-app klonen vanuit GitHub, de verbindingsreeks instellen en de app uitvoeren. In deze Quick Start wordt versie 4 van de [python-SDK](https://pypi.org/project/azure-cosmos/#history)gebruikt.

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

1. Selecteer in uw Azure Cosmos DB-account in de [Azure Portal](https://portal.azure.com/)de optie **sleutels** in het linkernavigatievenster. Gebruik de Kopieer knoppen aan de rechter kant van het scherm om de **URI** en **primaire sleutel** in het bestand *cosmos_get_started. py* in de volgende stap te kopiëren.

    ![Haal een toegangs sleutel en URI op in de sleutel instellingen in de Azure Portal](./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png)

2. Open in Visual Studio code het bestand *cosmos_get_started. py* in *\git-samples\azure-Cosmos-DB-python-Getting-Started*.

3. Kopieer uw **URI** -waarde vanuit de portal (met de Kopieer knop) en geef deze als waarde aan de **eindpunt** variabele in *cosmos_get_started. py*. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Kopieer vervolgens de waarde van uw **primaire sleutel** uit de portal en geef deze als waarde aan de **sleutel** in *cosmos_get_started. py*. U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB. 

    `key = 'FILLME'`

5. Sla het bestand *cosmos_get_started. py* op.

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Meer informatie over de database resources die zijn gemaakt in code of overs Laan om [uw Connection String](#update-your-connection-string)bij te werken.

De volgende code fragmenten zijn allemaal afkomstig uit het bestand *cosmos_get_started. py* .

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

4. Controleer in het geïntegreerde Terminal venster of u zich in de map *Azure-Cosmos-DB-python-Getting-Started* bevindt. Zo niet, voer dan de volgende opdracht uit om naar de voorbeeldmap over te schakelen. 

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

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos DB account maakt, een container maakt met behulp van de Data Explorer en een python-app uitvoert in Visual Studio code. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB voor de SQL API](import-data.md)


