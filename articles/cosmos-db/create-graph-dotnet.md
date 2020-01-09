---
title: Een Azure Cosmos DB .NET Framework, kern toepassing bouwen met behulp van de Gremlin-API
description: Presenteert een .NET Framework-/Core-codevoorbeeld dat u kunt gebruiken om verbinding te maken met Azure Cosmos DB en een query uit te voeren
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: lbosq
ms.openlocfilehash: d74a7d2171f926a7a97562339d4cab36b354bfbe
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441973"
---
# <a name="quickstart-build-a-net-framework-or-core-application-using-the-azure-cosmos-db-gremlin-api-account"></a>Snelstartgids: een .NET Framework-of kern toepassing bouwen met het Azure Cosmos DB Gremlin-API-account

> [!div class="op_single_selector"]
> * [Gremlin-console](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U kunt snel databases maken van documenten, sleutel/waarde-paren en grafen en hier query’s op uitvoeren. Deze databases genieten allemaal het voordeel van de wereldwijde distributie en horizontale schaalmogelijkheden die ten grondslag liggen aan Azure Cosmos DB. 

In deze Quick start ziet u hoe u een Azure Cosmos DB [GREMLIN API](graph-introduction.md) -account,-data base en-grafiek (container) kunt maken met behulp van de Azure Portal. U gaat vervolgens een console-app ontwikkelen en uitvoeren met behulp van het opensourcestuurprogramma [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet).  

## <a name="prerequisites"></a>Vereisten

Als Visual Studio 2019 nog niet is geïnstalleerd, kunt u de **gratis** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Een databaseaccount maken

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Een graaf toevoegen

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een Gremlin-API-app klonen vanaf GitHub, de verbindingsreeks instellen en de app uitvoeren. U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken. 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-gremlindotnet-getting-started.git
    ```

4. Open vervolgens Visual Studio en open het oplossingenbestand.

5. Herstel de NuGet-pakketten in het project. Dit geldt voor het stuurprogramma Gremlin.Net, evenals het pakket Newtonsoft.Json.


6. U kunt het stuurprogramma Gremlin.Net ook handmatig installeren met Nuget-pakketbeheer of het [Nuget-opdrachtregelprogramma](https://docs.microsoft.com/nuget/install-nuget-client-tools): 

    ```bash
    nuget install Gremlin.Net
    ```

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources in de code worden gemaakt, kunt u de volgende codefragmenten bekijken. Als u deze stap wilt overslaan, kunt u verdergaan naar [Uw verbindingsreeks bijwerken](#update-your-connection-string). 

De volgende codefragmenten zijn allemaal afkomstig uit het bestand Program.cs.

* Stel uw verbindingsparameters in op basis van het hierboven gemaakte account (regel 19): 

    ```csharp
    private static string hostname = "your-endpoint.gremlin.cosmosdb.azure.com";
    private static int port = 443;
    private static string authKey = "your-authentication-key";
    private static string database = "your-database";
    private static string collection = "your-graph-container";
    ```

* De uit te voeren Gremlin-opdrachten worden vermeld in een woordenboek (regel 26):

    ```csharp
    private static Dictionary<string, string> gremlinQueries = new Dictionary<string, string>
    {
        { "Cleanup",        "g.V().drop()" },
        { "AddVertex 1",    "g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44).property('pk', 'pk')" },
        { "AddVertex 2",    "g.addV('person').property('id', 'mary').property('firstName', 'Mary').property('lastName', 'Andersen').property('age', 39).property('pk', 'pk')" },
        { "AddVertex 3",    "g.addV('person').property('id', 'ben').property('firstName', 'Ben').property('lastName', 'Miller').property('pk', 'pk')" },
        { "AddVertex 4",    "g.addV('person').property('id', 'robin').property('firstName', 'Robin').property('lastName', 'Wakefield').property('pk', 'pk')" },
        { "AddEdge 1",      "g.V('thomas').addE('knows').to(g.V('mary'))" },
        { "AddEdge 2",      "g.V('thomas').addE('knows').to(g.V('ben'))" },
        { "AddEdge 3",      "g.V('ben').addE('knows').to(g.V('robin'))" },
        { "UpdateVertex",   "g.V('thomas').property('age', 44)" },
        { "CountVertices",  "g.V().count()" },
        { "Filter Range",   "g.V().hasLabel('person').has('age', gt(40))" },
        { "Project",        "g.V().hasLabel('person').values('firstName')" },
        { "Sort",           "g.V().hasLabel('person').order().by('firstName', decr)" },
        { "Traverse",       "g.V('thomas').out('knows').hasLabel('person')" },
        { "Traverse 2x",    "g.V('thomas').out('knows').hasLabel('person').out('knows').hasLabel('person')" },
        { "Loop",           "g.V('thomas').repeat(out()).until(has('id', 'robin')).path()" },
        { "DropEdge",       "g.V('thomas').outE('knows').where(inV().has('id', 'mary')).drop()" },
        { "CountEdges",     "g.E().count()" },
        { "DropVertex",     "g.V('thomas').drop()" },
    };
    ```


* Maak een `GremlinServer`-verbindingsobject met de bovenstaande parameters (regel 52):

    ```csharp
    var gremlinServer = new GremlinServer(hostname, port, enableSsl: true, 
                                                    username: "/dbs/" + database + "/colls/" + collection, 
                                                    password: authKey);
    ```

* Maak een nieuw `GremlinClient`-object (regel 56):

    ```csharp
    var gremlinClient = new GremlinClient(gremlinServer, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType);
    ```

* Voer elke Gremlin-query uit met behulp van het `GremlinClient`-object met een asynchrone taak (regel 63). Hiermee worden de Gremlin-query's gelezen uit het hierboven gedefinieerde woordenboek (regel 26):

    ```csharp
    var results = await gremlinClient.SubmitAsync<dynamic>(query.Value);
    ```

* Haal het resultaat op en lees de waarden, die zijn opgemaakt als een woordenboek, met behulp van de `JsonSerializer`-klasse vanuit Newtonsoft.Json:

    ```csharp
    foreach (var result in results)
    {
        // The vertex results are formed as dictionaries with a nested dictionary for their properties
        string output = JsonConvert.SerializeObject(result);
        Console.WriteLine(String.Format("\tResult:\n\t{0}", output));
    }
    ```

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app.

1. Vanuit [Azure Portal](https://portal.azure.com/) gaat u naar uw grafiekdatabaseaccount. In het tabblad **Overzicht** ziet u twee eindpunten: 
 
   **.NET SDK-URI** : deze waarde wordt gebruikt wanneer u verbinding maakt met het grafiek account met behulp van de bibliotheek micro soft. Azure. graphs. 

   **Gremlin-eindpunt**: deze waarde wordt gebruikt wanneer u verbinding met het grafiekaccount maakt met behulp van de Gremlin.Net-bibliotheek.

    ![Het eindpunt kopiëren](./media/create-graph-dotnet/endpoint.png)

   Als u dit voorbeeld wilt uitvoeren, moet u de waarde voor het **Gremlin-eindpunt** kopiëren, het poortnummer aan het eind verwijderen, zodat de URI `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com` wordt.

2. Plak in Program.cs de waarde over `your-endpoint` in de variabele `hostname` op regel 19. 

    `"private static string hostname = "<your cosmos db account name>.gremlin.cosmosdb.azure.com";`

    De eindpuntwaarde ziet er nu als volgt:

    `"private static string hostname = "testgraphacct.gremlin.cosmosdb.azure.com";`

3. Ga vervolgens naar het tabblad **Sleutels** en kopieer de waarde van **PRIMAIRE SLEUTEL** uit de portal en plak deze in de variabele `authkey`, waarbij de tijdelijke aanduiding `"your-authentication-key"` op regel 21 wordt vervangen. 

    `private static string authKey = "your-authentication-key";`

4. Plak met behulp van de gegevens van de hierboven gemaakte database de databasenaam in de variabele `database` op regel 22. 

    `private static string database = "your-database";`

5. Plak op dezelfde manier de container met behulp van de gegevens van de hierboven gemaakte verzameling (die ook de grafieknaam is) in de variabele `collection` op regel 23. 

    `private static string collection = "your-collection-or-graph";`

6. Sla het bestand Program.cs op. 

U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB. 

## <a name="run-the-console-app"></a>De app console uitvoeren

Klik op CTRL+F5 om de toepassing te starten. De toepassing drukt zowel de Gremlin-queryopdrachten als de -resultaten af in de console.

   In het consolevenster worden de hoekpunten en randen weergegeven die aan de graaf worden toegevoegd. Zodra het script is voltooid, drukt u op Enter om het consolevenster te sluiten.

## <a name="browse-using-the-data-explorer"></a>Bladeren met Data Explorer

U kunt nu teruggaan naar Data Explorer in Azure Portal en door uw nieuwe graafgegevens bladeren en er query’s op uitvoeren.

1. De nieuwe database wordt in Data Explorer weergegeven in het deelvenster Grafieken. Vouw de database en containerknooppunten uit en klik op **Graph**.

2. Klik op de knop **Filter toepassen** om de standaardquery te gebruiken om alle hoekpunten in de grafiek weer te geven. De gegevens die worden gegenereerd door de voorbeeldapp worden weergegeven in het deelvenster Grafen.

    U kunt op de grafiek in- en uitzoomen, u kunt u de grafiek uitvouwen voor meer ruimte, extra hoekpunten toevoegen en hoekpunten verplaatsen in de weergaveruimte.

    ![De grafiek weergeven in Data Explorer in Azure Portal](./media/create-graph-dotnet/graph-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos DB-account kunt maken, hebt u een graaf gemaakt met Data Explorer en hebt u een app uitgevoerd. U kunt nu complexere query's maken en met Gremlin krachtige logica implementeren om door een graaf te gaan. 

> [!div class="nextstepaction"]
> [Query’s uitvoeren met Gremlin](tutorial-query-graph.md)

