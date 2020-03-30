---
title: 'Quickstart: Gremlin-API met Python - Azure Cosmos DB'
description: In deze snelstart ziet u hoe u de Gremlin-API in Azure Cosmos DB gebruikt om een toepassing te maken met Azure Portal en Python
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: python
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: lbosq
ms.openlocfilehash: b1286daaa76c71f88d44ea387a92876a8676783c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062222"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmos-db-using-python-and-the-azure-portal"></a>Snelstart: een grafiekdatabase maken in Azure Cosmos DB met Python en de Azure-portal

> [!div class="op_single_selector"]
> * [Gremlin-console](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [Php](create-graph-php.md)
>  

In deze quickstart maakt en beheert u een Azure Cosmos DB Gremlin (grafiek) API-account vanuit de Azure-portal en voegt u gegevens toe met behulp van een Python-app die is gekloond van GitHub. Azure Cosmos DB is een databaseservice met meerdere modellen waarmee u snel document-, tabel-, sleutelwaarde- en grafiekdatabases maken en opvragen met globale distributie- en horizontale schaalmogelijkheden.

## <a name="prerequisites"></a>Vereisten
- Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Of [probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement.
- [Python 3.5+](https://www.python.org/downloads/) inclusief [pip](https://pip.pypa.io/en/stable/installing/) pakket installer.
- [Python Driver voor Gremlin](https://github.com/apache/tinkerpop/tree/master/gremlin-python).
- [Git.](https://git-scm.com/downloads)

> [!NOTE]
> Voor deze snelstartgids is een grafiekdatabaseaccount vereist dat is gemaakt na 20 december 2017. Bestaande accounts ondersteunen Python zodra ze zijn gemigreerd naar algemene beschikbaarheid.

## <a name="create-a-database-account"></a>Een databaseaccount maken

Voordat u een grafiekdatabase kunt maken, moet u een Gremlin-databaseaccount (Graph) maken met Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Een graaf toevoegen

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Nu gaan we werken met code. We gaan nu een Gremlin API-app klonen vanaf GitHub, de verbindingsreeks instellen en de app uitvoeren U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken.  

1. Open een opdrachtprompt, maak een nieuwe map met de naam git-samples en sluit vervolgens de opdrachtprompt.

    ```bash
    md "C:\git-samples"
    ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de opdracht `cd` om naar een map te gaan voor het installeren van de voorbeeld-app.  

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-python-getting-started.git
    ```

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources in de code worden gemaakt, kunt u de volgende codefragmenten bekijken. De fragmenten zijn allemaal afkomstig uit het *connect.py* bestand in de *C:\git-samples\azure-cosmos-db-graph-python-getting-started\\ * map. Als u deze stap wilt overslaan, kunt u verdergaan naar [Uw verbindingsreeks bijwerken](#update-your-connection-information). 

* De Gremlin `client` is geïnitialiseerd in lijn 104 in *connect.py*:

    ```python
    ...
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ...
    ```

* Een reeks Gremlin-stappen wordt aan het begin van het *connect.py-bestand* gedeclareerd. Ze worden vervolgens uitgevoerd met behulp van de methode `client.submitAsync()`:

    ```python
    client.submitAsync(_gremlin_cleanup_graph)
    ```

## <a name="update-your-connection-information"></a>Uw verbindingsgegevens bijwerken

Ga nu terug naar Azure Portal om de verbindingsgegevens op te halen en deze in de app te kopiëren. Met behulp van deze instellingen kan de app communiceren met de gehoste database.

1. Selecteer **Sleutels**in uw Azure Cosmos DB-account in de [Azure-portal](https://portal.azure.com/). 

    Kopieer het eerste gedeelte van de URI-waarde.

    ![Een toegangssleutel in Azure Portal bekijken en kopiëren op de pagina Sleutels](./media/create-graph-python/keys.png)

2. Open het *connect.py* bestand en plak in lijn `<YOUR_ENDPOINT>` 104 de URI-waarde hierin:

    ```python
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

    Het URI-gedeelte van het clientobject moet er nu als de volgende code uitzien:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

3. Wijzig de tweede parameter van het object `client` ter vervanging van de tekenreeksen `<YOUR_DATABASE>` en `<YOUR_COLLECTION_OR_GRAPH>`. Als u de voorgestelde waarden hebt gebruikt, moet de parameter eruitzien als de volgende code:

    `username="/dbs/sample-database/colls/sample-graph"`

    Het gehele object `client` moet er nu uitzien als de volgende code:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="<YOUR_PASSWORD>")
    ```

4. Gebruik **op** de pagina Toetsen de knop Kopiëren om `<YOUR_PASSWORD>` de `password=<YOUR_PASSWORD>` primaire sleutel te kopiëren en deze in de parameter te plakken.

    De gehele definitie van het object `client` moet er nu als de volgende code uitzien:
    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="asdb13Fadsf14FASc22Ggkr662ifxz2Mg==")
    ```

6. Sla het *connect.py-bestand* op.

## <a name="run-the-console-app"></a>De app console uitvoeren

1. `cd` in het git-terminalvenster naar de map azure-cosmos-db-graph-python-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-python-getting-started"
    ```

2. Gebruik in het git-terminalvenster de volgende opdracht om de vereiste Python-pakketten te installeren.

   ```
   pip install -r requirements.txt
   ```

3. Gebruik in het git-terminalvenster de volgende opdracht om de Python-toepassing te starten.
    
    ```
    python connect.py
    ```

    In het terminalvenster ziet u de hoekpunten en randen die worden toegevoegd aan de grafiek. 
    
    Als er time-outfouten optreden, controleert u of u de verbindingsgegevens correct hebt bijgewerkt in [Uw verbindingsgegevens bijwerken](#update-your-connection-information). Probeer ook de laatste opdracht opnieuw uit te voeren. 
    
    Wanneer het programma is gestopt, drukt u op Enter en gaat u in uw internetbrowser terug naar Azure Portal.

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Voorbeeldgegevens bekijken en toevoegen

Nadat de hoekpunten en randen zijn ingevoegd, u nu teruggaan naar Gegevensverkenner en de hoekpunten aan de grafiek bekijken en extra gegevenspunten toevoegen.

1. Selecteer **gegevensverkenner,** vouw in uw Azure Cosmos DB-account in de **Azure-portal de optie Gegevensverkenner**uit, selecteer **Grafiek**en selecteer **Filter toepassen**. 

   ![Nieuwe documenten maken in Data Explorer in Azure Portal](./media/create-graph-python/azure-cosmosdb-data-explorer-expanded.png)

2. In de lijst **Resultaten** worden drie nieuwe gebruikers aan de grafiek toegevoegd. U kunt de hoekpunten verplaatsen via slepen en neerzetten, in- en uitzoomen door te scrollen met het muiswiel en de grafiek uitvouwen met de dubbele pijl. 

   ![Nieuwe hoekpunten in de grafiek in Data Explorer in Azure Portal](./media/create-graph-python/azure-cosmosdb-graph-explorer-new.png)

3. Laten we nu enkele nieuwe gebruikers toevoegen. Selecteer de knop **Nieuwe Vertex** om gegevens aan uw grafiek toe te voegen.

   ![Nieuwe documenten maken in Data Explorer in Azure Portal](./media/create-graph-python/azure-cosmosdb-data-explorer-new-vertex.png)

4. Geef het label *persoon* op.

5. Selecteer **Eigenschap Toevoegen** om elk van de volgende eigenschappen toe te voegen. U kunt unieke eigenschappen maken voor elke persoon in de grafiek. Alleen de id-sleutel is vereist.

    sleutel|waarde|Opmerkingen
    ----|----|----
    pk|/pk| 
    id|ashley|De unieke id voor het hoekpunt. Als u geen id opgeeft, wordt er een id voor u gegenereerd.
    geslacht|vrouwelijk| 
    technisch | java | 

    > [!NOTE]
    > In deze quickstart gaat u een niet-gepartitioneerde verzameling maken. Als u echter een gepartitioneerde verzameling maakt door een partitiesleutel op te geven tijdens het maken van de verzameling, moet u de partitiesleutel opnemen als sleutel bij elk nieuw hoekpunt. 

6. Selecteer **OK**. Mogelijk moet u het scherm groter maken om **OK** weer te geven onder aan het scherm.

7. Selecteer **Opnieuw Nieuwe Vertex** en voeg een extra nieuwe gebruiker toe. 

8. Geef het label *persoon* op.

9. Selecteer **Eigenschap toevoegen** om elk van de volgende eigenschappen toe te voegen:

    sleutel|waarde|Opmerkingen
    ----|----|----
    pk|/pk| 
    id|rakesh|De unieke id voor het hoekpunt. Als u geen id opgeeft, wordt er een id voor u gegenereerd.
    geslacht|man| 
    school|MIT| 

10. Selecteer **OK**. 

11. Selecteer de knop **Filter** `g.V()` toepassen met het standaardfilter om alle waarden in de grafiek weer te geven. Alle gebruikers worden nu weergegeven in de lijst met **resultaten**. 

    Als u meer gegevens toevoegt, kunt u filters gebruiken om de resultaten te beperken. Data Explorer maakt standaard gebruik van `g.V()` voor het ophalen van alle hoekpunten van een grafiek. U kunt dit wijzigen in een andere [grafiekquery](tutorial-query-graph.md), bijvoorbeeld `g.V().count()`, om een telling van alle hoekpunten in de grafiek in JSON-indeling te retourneren. Als u het filter hebt gewijzigd, wijzigt u het filter terug naar `g.V()` en selecteert u Filter **toepassen** om alle resultaten weer te geven.

12. Nu kunnen we rakesh en ashley met elkaar verbinden. Zorg ervoor dat **Ashley** is geselecteerd in de lijst **Resultaten** en selecteer vervolgens de bewerkingsknop naast **Doelen** rechtsonder. Mogelijk moet u het scherm verbreden om het gedeelte **Eigenschappen** te kunnen zien.

    ![Het doel van een hoekpunt in een grafiek wijzigen](./media/create-graph-python/azure-cosmosdb-data-explorer-edit-target.png)

13. Selecteer in het vak **Doel** het type *rakesh*en in het vak **Edge-label** *dat het weet*en selecteer vervolgens de controle.

    ![Een verbinding tussen ashley en rakesh toevoegen in Data Explorer](./media/create-graph-python/azure-cosmosdb-data-explorer-set-target.png)

14. Selecteer nu **rakesh** in de lijst met resultaten en kijk of ashley en rakesh zijn verbonden. 

    ![Twee hoekpunten die zijn verbonden in Data Explorer](./media/create-graph-python/azure-cosmosdb-graph-explorer.png)

Hiermee is het onderdeel voor het maken van resources van deze zelfstudie voltooid. U kunt naar eigen inzicht verdergaan met toevoegen van hoekpunten, aanpassen van de bestaande hoekpunten of wijzigen van de query's. Laten we nu de metrische gegevens bekijken die Azure Cosmos DB biedt en vervolgens de resources opschonen. 

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Cosmos DB-account maakt, een grafiek maakt met de Data Explorer en een Python-app uitvoert om gegevens aan de grafiek toe te voegen. U kunt nu complexere query's maken en met Gremlin krachtige logica implementeren om door een graaf te gaan. 

> [!div class="nextstepaction"]
> [Query’s uitvoeren met Gremlin](tutorial-query-graph.md)

