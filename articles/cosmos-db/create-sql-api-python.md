---
title: 'Snelstartgids: een python-app bouwen met Azure Cosmos DB SQL-API-account'
description: Is een Python-codevoorbeeld dat u kunt gebruiken om verbinding te maken met en gegevens op te vragen uit de SQL API van Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
ms.openlocfilehash: f00d0e4f659c1ebe104dac1a25ea3688c9a1f2e5
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327249"
---
# <a name="azure-cosmos-db-build-a-python-application-using-azure-cosmos-db-sql-api-account"></a>Azure Cosmos DB: een python-toepassing maken met behulp van Azure Cosmos DB SQL-API-account

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U kunt snel documenten, sleutel/waarde, brede kolom-en grafiek databases maken en doorzoeken. Al deze bewerkingen profiteren van de distributie en schaal van Azure Cosmos DB.

Deze snelstart laat zien hoe u een [SQL API](sql-api-introduction.md)-account van Azure Cosmos DB, een documentdatabase en een container kunt maken met behulp van de Azure-portal. Vervolgens ontwikkelt u een console-app die is gebouwd met de Python SDK voor [SQL API](sql-api-sdk-python.md) en voert u deze uit. In deze Quick Start wordt versie 3,0 van de [python-SDK](https://pypi.org/project/azure-cosmos)gebruikt.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>Vereisten

* [Python 3,6](https://www.python.org/downloads/), met het `python` uitvoer bare bestand dat beschikbaar is in uw `PATH`.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Python-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview) (Engelstalig)

## <a name="create-a-database-account"></a>Een databaseaccount maken

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Een container toevoegen

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

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

1. Klik in het [Azure Portal](https://portal.azure.com/)in uw Azure Cosmos-account in de linkernavigatiebalk op **sleutels**. In de volgende stap gebruikt u de kopieerknoppen aan de rechterkant van het scherm om de **URI** en **Primaire sleutel** in het `CosmosGetStarted.py`-bestand te kopiëren.

    ![Een toegangssleutel bekijken en kopiëren in Azure Portal, blade Sleutels](./media/create-sql-api-dotnet/keys.png)

2. Open het bestand `CosmosGetStarted.py` in \git-samples\azure-Cosmos-DB-python-Getting-Started in Visual Studio code.

3. Kopieer uw **URI**-waarde vanaf de portal (met de kopieerknop) en geef deze als waarde van de **eindpunt**sleutel in ``CosmosGetStarted.py``. 

    `'ENDPOINT': 'https://FILLME.documents.azure.com',`

4. Kopieer dan de waarde van uw **PRIMAIRE SLEUTEL** vanaf de portal en geef deze als **config.PRIMARYKEY-waarde** in ``CosmosGetStarted.py`` in. U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB. 

    `'PRIMARYKEY': 'FILLME',`

5. Sla het bestand ``CosmosGetStarted.py`` op.

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Meer informatie over de database resources die zijn gemaakt in code of overs Laan om [uw Connection String](#update-your-connection-string)bij te werken.

Als u bekend bent met de vorige versie van de Python-SDK, komen de termen 'verzameling' en 'document' u vertrouwd voor. Azure Cosmos DB ondersteunt meerdere API-modellen. Daarom maakt versie 3.0+ van de Python-SDK gebruik van de generieke termen 'verzameling', wat een collectie, een grafiek of tabel kan zijn, en 'item', om de inhoud van de container te beschrijven.

De volgende codefragmenten zijn allemaal afkomstig uit het bestand `CosmosGetStarted.py`.

* De CosmosClient wordt geïnitialiseerd. Zorg ervoor dat u de waarden voor 'Eindpunt' en 'hoofdsleutel' bijwerkt zoals is beschreven in het gedeelte [Uw verbindingsreeks bijwerken](#update-your-connection-string). 

    ```python
    # Initialize the Cosmos client
    client = cosmos_client.CosmosClient(url_connection=config['ENDPOINT'], auth={'masterKey': config['MASTERKEY']})
    ```

* Er wordt een nieuwe database gemaakt.

    ```python
    # Create a database
    db = client.CreateDatabase({ 'id': config['DATABASE'] })
    ```

* Er wordt een nieuwe container gemaakt.

    ```python
    # Create container options
    options = {
        'offerThroughput': 400
    }

    # Create a container
    container = client.CreateContainer(db['_self'], container_definition, options)
    ```

* Bepaalde items worden toegevoegd aan de container.

    ```python
    # Create and add some items to the container
    item1 = client.CreateItem(container['_self'], {
        'serverId': 'server1',
        'Web Site': 0,
        'Cloud Service': 0,
        'Virtual Machine': 0,
        'message': 'Hello World from Server 1!'
        }
    )

    item2 = client.CreateItem(container['_self'], {
        'serverId': 'server2',
        'Web Site': 1,
        'Cloud Service': 0,
        'Virtual Machine': 0,
        'message': 'Hello World from Server 2!'
        }
    )
    ```

* Een query wordt uitgevoerd met behulp van SQL.

    ```python
    query = {'query': 'SELECT * FROM server s'}

    options = {}
    options['enableCrossPartitionQuery'] = True
    options['maxItemCount'] = 2

    result_iterable = client.QueryItems(container['_self'], query, options)
    for item in iter(result_iterable):
        print(item['message'])
    ```
   
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
    pip3 install azure-cosmos
    ```

    Als u een foutmelding krijgt over geweigerde toegang bij het installeren van azure-cosmos, dient u [VS Code als beheerder uit te voeren](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Voer de volgende opdracht uit om het voorbeeld uit te voeren, nieuwe documenten in Azure Cosmos DB te maken en op te slaan.

    ```python
    python CosmosGetStarted.py
    ```

7. U bevestigt als volgt dat de nieuwe items zijn gemaakt en opgeslagen: selecteer in Azure Portal **Data Explorer**, breid **coll** uit, breid **Documenten** uit en selecteer het document **server1**. De inhoud van het document server1 komt overeen met de inhoud die in het venster van de geïntegreerde terminal is geretourneerd. 

    ![De nieuwe documenten weergeven in Azure Portal](./media/create-sql-api-python/azure-cosmos-db-confirm-documents.png)

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos-account maakt, een container maakt met behulp van de Data Explorer en een app uitvoert. Nu kunt u aanvullende gegevens in uw Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB voor de SQL API](import-data.md)


