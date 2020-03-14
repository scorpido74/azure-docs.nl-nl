---
title: 'Quickstart: Table-API met .NET - Azure Cosmos DB'
description: In deze quickstart ziet u hoe u de Table-API in Azure Cosmos DB gebruikt om een toepassing te maken met Azure Portal en .NET
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: 629adfe558aec71e156e50c75aa0891eac5a8bcf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240168"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>Snelstartgids: een Table-API-app bouwen met .NET SDK en Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
>  

Deze quickstart laat zien hoe u .NET en de [Table-API](table-introduction.md) van Azure Cosmos DB gebruikt voor het compileren van een app door een voorbeeld uit GitHub te klonen. In deze quickstart ziet u ook hoe u een Azure Cosmos DB-account maakt en hoe u Data Explorer gebruikt om tabellen en entiteiten te maken in Azure Portal op internet.

## <a name="prerequisites"></a>Vereisten

Als Visual Studio 2019 nog niet is geïnstalleerd, kunt u de **gratis** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Een databaseaccount maken

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Een tabel toevoegen

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Voorbeeldgegevens toevoegen

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een Table-app klonen vanaf GitHub, de verbindingsreeks instellen en de app uitvoeren. U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken. 

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
   git clone https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started.git
   ```

## <a name="open-the-sample-application-in-visual-studio"></a>De voorbeeldtoepassing openen in Visual Studio

1. In Visual Studio opent u het menu **Bestand**, kiest u **Openen** en vervolgens **Project/oplossing**. 

   ![De oplossing openen](media/create-table-dotnet/azure-cosmosdb-open-solution.png) 

2. Navigeer naar de map waar u de voorbeeld toepassing hebt gekloond en open het bestand TableStorage. SLN.

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app. Hierdoor kan de app communiceren met de gehoste database. 

1. Klik in [Azure Portal](https://portal.azure.com/) op **Verbindingsreeks**. Gebruik de kopieerknop aan de rechterkant van het venster om de **PRIMARY CONNECTION STRING** te kopiëren.

   ![De PRIMARY CONNECTION STRING in het deelvenster Verbindingsreeks weergeven en kopiëren](./media/create-table-dotnet/connection-string.png)

2. Open in Visual Studio het bestand **Settings. json** . 

3. Plak de **primaire VERBINDINGS reeks** vanuit de portal in de waarde StorageConnectionString. Plak de tekenreeks tussen de aanhalingstekens.

   ```csharp
   {
      "StorageConnectionString": "<Primary connection string from Azure portal>"
   }
   ```

4. Druk op CTRL + S om het bestand **Settings. json** op te slaan.

U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB. 

## <a name="build-and-deploy-the-app"></a>De app compileren en implementeren

1. Klik in Visual Studio met de rechter muisknop op het **CosmosTableSamples** -project in **Solution Explorer** en klik vervolgens op **NuGet-pakketten beheren**. 

   ![NuGet-pakketten beheren](media/create-table-dotnet/azure-cosmosdb-manage-nuget.png)

2. Typ in het NuGet-vak **Bladeren** micro soft. Azure. Cosmos. table. Daarmee wordt de clientbibliotheek Cosmos DB-tabel API gevonden. Houd er rekening mee dat deze bibliotheek momenteel beschikbaar is voor .NET Framework en .NET Standard. 
   
   ![Tabblad NuGet bladeren](media/create-table-dotnet/azure-cosmosdb-nuget-browse.png)

3. Klik op **installeren** om de bibliotheek **micro soft. Azure. Cosmos. table** te installeren. Hiermee worden het pakket voor de Azure Cosmos DB-tabel API en alle daarvoor vereiste onderdelen geïnstalleerd.

4. Wanneer u de hele app uitvoert, worden er voorbeeld gegevens in de tabel entiteit ingevoegd en aan het eind verwijderd, zodat er geen gegevens worden weer gegeven als u het hele voor beeld uitvoert. U kunt echter een aantal onderbrekings punten invoegen om de gegevens weer te geven. Open BasicSamples.cs-bestand en klik met de rechter muisknop op regel 52, selecteer **onderbrekings punt**en selecteer **onderbrekings punt invoegen**. Voeg nog een onderbrekingspunt in op regel 55.

   ![Een onderbrekingspunt toevoegen](media/create-table-dotnet/azure-cosmosdb-breakpoint.png) 

5. Druk op F5 om de toepassing uit te voeren. In het console venster wordt de naam weer gegeven van de nieuwe tabel database (in dit geval demoa13b1) in Azure Cosmos DB. 
    
   ![Console-uitvoer](media/create-table-dotnet/azure-cosmosdb-console.png)

   Als u het eerste onderbrekingspunt bereikt, gaat u terug naar de Data Explorer in Azure Portal. Klik op de knop **Vernieuwen**, vouw de demo*-tabel uit en klik op **Entiteiten**. Het tabblad **Entiteiten** aan de rechterkant geeft de nieuwe entiteit weer die is toegevoegd voor Walter Harp. U ziet dat het telefoonnummer voor de nieuwe entiteit 425-555-0101 is.

   ![Nieuwe entiteit](media/create-table-dotnet/azure-cosmosdb-entity.png)
    
   Als er een fout bericht wordt weer gegeven met de tekst instellingen. JSON-bestand is niet gevonden bij het uitvoeren van het project, kunt u dit oplossen door de volgende XML-vermelding toe te voegen aan de project instellingen. Klik met de rechter muisknop op CosmosTableSamples, selecteer Bewerken CosmosTableSamples. csproj en voeg de volgende itemGroup toe: 

   ```csharp
     <ItemGroup>
       <None Update="Settings.json">
         <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
       </None>
     </ItemGroup>
   ```

6. Sluit het tabblad **Entiteiten** in Data Explorer.
    
7. Druk op F5 om de app uit te voeren tot het volgende onderbrekingspunt wordt bereikt. 

    Wanneer u het onderbrekingspunt bereikt, gaat u terug naar Azure Portal en klikt u opnieuw op **Entiteiten** om het tabblad **Entiteiten** te openen. U ziet dat het telefoonnummer is bijgewerkt naar 425-555-0105.

8. Druk op F5 om de app uit te voeren. 
 
   De app voegt entiteiten toe voor gebruik in een geavanceerde voorbeeld-app die momenteel niet door de tabel-API wordt ondersteund. De app verwijdert vervolgens de tabel die door de voorbeeld-app is gemaakt.

9. Druk in het consolevenster op Enter om te stoppen met de uitvoering van de app. 
  

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos DB-account kunt maken, hebt u een tabel gemaakt met de Data Explorer en hebt u een app uitgevoerd.  Nu kunt u een query uitvoeren op uw gegevens met de Table-API.  

> [!div class="nextstepaction"]
> [Tabelgegevens importeren in de Table-API](table-import.md)

