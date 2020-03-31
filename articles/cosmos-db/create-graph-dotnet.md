---
title: Een Azure Cosmos DB .NET Framework, Core-toepassing bouwen met de Gremlin-API
description: Presenteert een .NET Framework-/Core-codevoorbeeld dat u kunt gebruiken om verbinding te maken met Azure Cosmos DB en een query uit te voeren
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/21/2020
ms.author: lbosq
ms.openlocfilehash: f700b06e6ade0d72178777b67cb734f3120b36dc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240189"
---
# <a name="quickstart-build-a-net-framework-or-core-application-using-the-azure-cosmos-db-gremlin-api-account"></a>Snelstart: een .NET Framework- of Core-toepassing bouwen met het Azure Cosmos DB Gremlin API-account

> [!div class="op_single_selector"]
> * [Gremlin-console](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [Php](create-graph-php.md)
>  

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U kunt snel databases maken van documenten, sleutel/waarde-paren en grafen en hier query’s op uitvoeren. Deze databases genieten allemaal het voordeel van de wereldwijde distributie en horizontale schaalmogelijkheden die ten grondslag liggen aan Azure Cosmos DB. 

Deze quickstart laat zien hoe u een Azure Cosmos DB [Gremlin API-account,](graph-introduction.md) database en grafiek (container) maakt met behulp van de Azure-portal. U gaat vervolgens een console-app ontwikkelen en uitvoeren met behulp van het opensourcestuurprogramma [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet).  

## <a name="prerequisites"></a>Vereisten

Als je Visual Studio 2019 nog niet hebt geïnstalleerd, kun je de **gratis** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.

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

* Stel uw verbindingsparameters in op basis van het account dat hierboven is gemaakt: 

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="configureConnectivity":::

* De uit te voeren Gremlin-opdrachten worden vermeld in een woordenboek:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineQueries":::

* Maak een `GremlinServer` `GremlinClient` nieuwe en verbindingsobjecten met de bovenstaande parameters:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineClientandServerObjects":::

* Voer elke Gremlin-query uit met behulp van het `GremlinClient` object met een async-taak. U de Gremlin-query's uit het woordenboek lezen dat in de vorige stap is gedefinieerd en deze uitvoeren. Later krijgen het resultaat en lees de waarden, die `JsonSerializer` zijn opgemaakt als een woordenboek, met behulp van de klasse van Newtonsoft.Json pakket:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="executeQueries":::

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app.

1. Vanuit [Azure Portal](https://portal.azure.com/) gaat u naar uw grafiekdatabaseaccount. In het tabblad **Overzicht** ziet u twee eindpunten: 
 
   **.NET SDK URI** - Deze waarde wordt gebruikt wanneer u verbinding maakt met het grafiekaccount met behulp van de bibliotheek Microsoft.Azure.Graphs. 

   **Gremlin-eindpunt**: deze waarde wordt gebruikt wanneer u verbinding met het grafiekaccount maakt met behulp van de Gremlin.Net-bibliotheek.

    ![Het eindpunt kopiëren](./media/create-graph-dotnet/endpoint.png)

   Als u dit voorbeeld wilt uitvoeren, kopieert u de waarde **Van Gremlin Endpoint,** verwijdert u het poortnummer aan het einde, dat wil zeggen de URI wordt `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com`. De eindpuntwaarde moet eruit zien`testgraphacct.gremlin.cosmosdb.azure.com`

1. Navigeer vervolgens naar het tabblad **Sleutels** en kopieer de primaire **sleutelwaarde** vanuit de Azure-portal. 

1. Nadat u de URI- en PRIMAIRE SLEUTEL van uw account hebt gekopieerd, slaat u deze op in een nieuwe omgevingsvariabele op de lokale machine waarop de toepassing wordt uitgevoerd. Als u de omgevingsvariabele wilt instellen, opent u een opdrachtpromptvenster en voert u de volgende opdracht uit. Zorg ervoor dat u <Your_Azure_Cosmos_account_URI> en Your_Azure_Cosmos_account_PRIMARY_KEY> waarden <Your_Azure_Cosmos_account_PRIMARY_KEY.

   ```console
   setx EndpointUrl "https://<your cosmos db account name>.gremlin.cosmosdb.azure.com"
   setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
   ```

1. Open het *Program.cs* bestand en werk de "database en "container" variabelen met de database en container (dat is ook de grafiek naam) namen hierboven gemaakt.

    `private static string database = "your-database-name";` `private static string container = "your-container-or-graph-name";`

1. Sla het bestand Program.cs op. 

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

