---
title: Een grafiekdatabase bouwen met Java in Azure Cosmos DB
description: Biedt een voorbeeld van Java-code dat u kunt gebruiken om verbinding te maken met gegevens over grafen in Azure Cosmos DB met behulp van Gremlin.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: lbosq
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 9f9b6614c586d9c7c721dfc59da9c4a9c342b57c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062039"
---
# <a name="quickstart-build-a-graph-database-with-the-java-sdk-and-the-azure-cosmos-db-gremlin-api"></a>Snelstart: een grafiekdatabase bouwen met de Java SDK en de Azure Cosmos DB Gremlin API

> [!div class="op_single_selector"]
> * [Gremlin-console](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [Php](create-graph-php.md)
>  

In deze quickstart maakt en beheert u een Azure Cosmos DB Gremlin (grafiek) API-account vanuit de Azure-portal en voegt u gegevens toe met behulp van een Java-app die is gekloond van GitHub. Azure Cosmos DB is een databaseservice met meerdere modellen waarmee u snel document-, tabel-, sleutelwaarde- en grafiekdatabases maken en opvragen met globale distributie- en horizontale schaalmogelijkheden.

## <a name="prerequisites"></a>Vereisten
- Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Wijs `JAVA_HOME` uw omgevingsvariabele op de map waar de JDK is geïnstalleerd.
- Een [Maven binair archief](https://maven.apache.org/download.cgi). 
- [Git.](https://www.git-scm.com/downloads) 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources in de code worden gemaakt, kunt u de volgende codefragmenten bekijken. Als u deze stap wilt overslaan, kunt u verdergaan naar [Uw verbindingsreeks bijwerken](#update-your-connection-information).

De volgende fragmenten zijn allemaal afkomstig uit het *C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\GetStarted\Program.java-bestand.*

Deze Java console app maakt gebruik van een [Gremlin API](graph-introduction.md) database met de OSS [Apache TinkerPop](https://tinkerpop.apache.org/) driver. 

- De Gremlin `Client` is geïnitialiseerd vanuit de configuratie in het *C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\remote.yaml-bestand.*

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

- Reeks Gremlin-stappen wordt uitgevoerd met behulp van de methode `client.submit`.

    ```java
    ResultSet results = client.submit(gremlin);

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```

## <a name="update-your-connection-information"></a>Uw verbindingsgegevens bijwerken

Ga nu terug naar Azure Portal om de verbindingsgegevens op te halen en deze in de app te kopiëren. Met behulp van deze instellingen kan de app communiceren met de gehoste database.

1. Selecteer **Sleutels**in uw Azure Cosmos DB-account in de [Azure-portal](https://portal.azure.com/). 

    Kopieer het eerste gedeelte van de URI-waarde.

    ![Een toegangssleutel in Azure Portal bekijken en kopiëren op de pagina Sleutels](./media/create-graph-java/copy-access-key-azure-portal.png)
2. Open het *src/remote.yaml-bestand* en plak `$name$` `hosts: [$name$.graphs.azure.com]`de unieke ID-waarde omver in .

    Lijn 1 van *remote.yaml* moet er nu uitzien als 

    `hosts: [test-graph.graphs.azure.com]`

3. Wijzig `graphs` in de waarde `endpoint` in `gremlin.cosmosdb`. (Als uw grafiekdatabaseaccount is gemaakt vóór 20 december 2017, brengt u geen wijzigingen aan in de eindpuntwaarde, en gaat u verder met de volgende stap.)

    De eindpuntwaarde ziet er nu als volgt:

    `"endpoint": "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"`

4. Gebruik in Azure Portal de kopieerknop om de PRIMAIRE SLEUTEL te kopiëren en vervang `$masterKey$` in `password: $masterKey$` door deze waarde.

    Lijn 4 van *remote.yaml* moet er nu uitzien als 

    `password: 2Ggkr662ifxz2Mg==`

5. Regel 3 van *remote.yaml* wijzigen van

    `username: /dbs/$database$/colls/$collection$`

    tot 

    `username: /dbs/sample-database/colls/sample-graph`

    Als u een unieke naam voor uw voorbeelddatabase of -grafiek hebt gebruikt, werkt u de waarden waar nodig bij.

6. Sla het *remote.yaml-bestand* op.

## <a name="run-the-console-app"></a>De app console uitvoeren

1. `cd` in het git-terminalvenster naar de map azure-cosmos-db-graph-java-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-java-getting-started"
    ```

2. Gebruik in het git-terminalvenster de volgende opdracht om de vereiste Java-pakketten te installeren.

   ```git
   mvn package
   ```

3. Gebruik in het git-terminalvenster de volgende opdracht om de Java-toepassing te starten.
    
    ```git
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    In het terminalvenster ziet u de hoekpunten die worden toegevoegd aan de grafiek. 
    
    Als er time-outfouten optreden, controleert u of u de verbindingsgegevens correct hebt bijgewerkt in [Uw verbindingsgegevens bijwerken](#update-your-connection-information). Probeer ook de laatste opdracht opnieuw uit te voeren. 
    
    Zodra het programma is gestopt, selecteert u Enter en schakelt u terug naar de Azure-portal in uw internetbrowser. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Voorbeeldgegevens bekijken en toevoegen

U kunt nu teruggaan naar Data Explorer en de hoekpunten bekijken die zijn toegevoegd aan de grafiek. Ook kunt u extra hoekpunten toevoegen.

1. Selecteer **gegevensverkenner,** vouw in uw Azure Cosmos DB-account in de **Azure-portal de optie Gegevensverkenner**uit, selecteer **Grafiek**en selecteer **Filter toepassen**. 

   ![Nieuwe documenten maken in Data Explorer in Azure Portal](./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png)

2. In de lijst met **resultaten** ziet u de nieuwe gebruikers die zijn toegevoegd aan de grafiek. Als u **ben** selecteert, ziet u dat deze gebruiker is verbonden met robin. U kunt de hoekpunten verplaatsen via slepen en neerzetten, in- en uitzoomen door te scrollen met het muiswiel en de grafiek uitvouwen met de dubbele pijl. 

   ![Nieuwe hoekpunten in de grafiek in Data Explorer in Azure Portal](./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png)

3. Laten we nu enkele nieuwe gebruikers toevoegen. Selecteer **Nieuwe Vertex** om gegevens aan uw grafiek toe te voegen.

   ![Nieuwe documenten maken in Data Explorer in Azure Portal](./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png)

4. Typ *persoon* in het labelvak.

5. Selecteer **Eigenschap Toevoegen** om elk van de volgende eigenschappen toe te voegen. U kunt unieke eigenschappen maken voor elke persoon in de grafiek. Alleen de id-sleutel is vereist.

    sleutel|waarde|Opmerkingen
    ----|----|----
    id|ashley|De unieke id voor het hoekpunt. Als u geen id opgeeft, wordt er een id voor u gegenereerd.
    geslacht|vrouwelijk| 
    technisch | java | 

    > [!NOTE]
    > In deze snelstart gaat u een niet-gepartitioneerde verzameling maken. Als u echter een gepartitioneerde verzameling maakt door een partitiesleutel op te geven tijdens het maken van de verzameling, moet u de partitiesleutel opnemen als sleutel bij elk nieuw hoekpunt. 

6. Selecteer **OK**. Mogelijk moet u het scherm groter maken om **OK** weer te geven onder aan het scherm.

7. Selecteer **Opnieuw Nieuwe Vertex** en voeg een extra nieuwe gebruiker toe. 

8. Geef het label *persoon* op.

9. Selecteer **Eigenschap toevoegen** om elk van de volgende eigenschappen toe te voegen:

    sleutel|waarde|Opmerkingen
    ----|----|----
    id|rakesh|De unieke id voor het hoekpunt. Als u geen id opgeeft, wordt er een id voor u gegenereerd.
    geslacht|man| 
    school|MIT| 

10. Selecteer **OK**. 

11. Selecteer de knop **Filter** `g.V()` toepassen met het standaardfilter om alle waarden in de grafiek weer te geven. Alle gebruikers worden nu weergegeven in de lijst met **resultaten**. 

    Als u meer gegevens toevoegt, kunt u filters gebruiken om de resultaten te beperken. Data Explorer maakt standaard gebruik van `g.V()` voor het ophalen van alle hoekpunten van een grafiek. U kunt dit wijzigen in een andere [grafiekquery](tutorial-query-graph.md), bijvoorbeeld `g.V().count()`, om een telling van alle hoekpunten in de grafiek in JSON-indeling te retourneren. Als u het filter hebt gewijzigd, wijzigt u het filter terug naar `g.V()` en selecteert u Filter **toepassen** om alle resultaten weer te geven.

12. Nu kunt u rakesh en ashley met elkaar verbinden. Zorg ervoor dat **Ashley** is geselecteerd ![in de lijst **Resultaten** en selecteer Vervolgens Het doel van een hoekpunt wijzigen in een grafiek](./media/create-graph-java/edit-pencil-button.png) naast **Doelen** rechtsonder. Mogelijk moet u het scherm verbreden om de knop te kunnen zien.

    ![Het doel van een hoekpunt in een grafiek wijzigen - Azure CosmosDB](./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png)

13. Voer in het vak **Doel** *rakesh*in en voer in het vak **Edge-label** *weet*in en schakel het selectievakje in.

    ![Een verbinding toevoegen in Data Explorer - Azure CosmosDB](./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png)

14. Selecteer nu **rakesh** in de lijst met resultaten en kijk of ashley en rakesh zijn verbonden. 

    ![Twee vertices verbonden in Data Explorer - Azure CosmosDB](./media/create-graph-java/azure-cosmosdb-graph-explorer.png)

Hiermee is het onderdeel voor het maken van resources van deze zelfstudie voltooid. U kunt naar eigen inzicht verdergaan met toevoegen van hoekpunten, aanpassen van de bestaande hoekpunten of wijzigen van de query's. Laten we nu de metrische gegevens bekijken die Azure Cosmos DB biedt en vervolgens de resources opschonen. 

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Cosmos DB-account maakt, een grafiek maakt met de Data Explorer en een Java-app uitvoert die gegevens aan de grafiek toevoegt. U kunt nu complexere query's maken en met Gremlin krachtige logica implementeren om door een graaf te gaan. 

> [!div class="nextstepaction"]
> [Query’s uitvoeren met Gremlin](tutorial-query-graph.md)

