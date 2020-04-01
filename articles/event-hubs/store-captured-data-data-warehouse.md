---
title: 'Zelfstudie: Gebeurtenisgegevens migreren naar SQL Data Warehouse - Azure Event Hubs'
description: 'Zelfstudie: In deze zelfstudie ziet u hoe u gegevens van uw gebeurtenishub vastleggen in een SQL-gegevensmagazijn met behulp van een Azure-functie die wordt geactiveerd door een gebeurtenisraster.'
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
ms.author: shvija
ms.custom: seodec18
ms.date: 01/15/2020
ms.topic: tutorial
ms.service: event-hubs
ms.openlocfilehash: 28fa9dddda94845511ead7d8fb7481aff6b6b044
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80130853"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-a-sql-data-warehouse-using-event-grid-and-azure-functions"></a>Zelfstudie: Vastgelegde gebeurtenishubsgegevens migreren naar een SQL Data Warehouse met gebeurtenisraster en Azure-functies

Event Hubs [Capture](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) is de eenvoudigste manier om automatisch gestreamde gegevens in Event Hubs te verzenden naar een Azure Blob Storage of Azure Data Lake Store. U kunt de gegevens vervolgens verwerken en verzenden naar andere opslagdoelen van uw keuze, zoals SQL Data Warehouse of Cosmos DB. In deze zelfstudie leest u hoe u gegevens uit uw Event Hub vastlegt in een SQL Data Warehouse met behulp van een Azure-functie die wordt geactiveerd door een [Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

![Visual Studio](./media/store-captured-data-data-warehouse/EventGridIntegrationOverview.PNG)

*   U maakt eerst een Event Hub, waarbij de functie **Capture** is ingeschakeld, en stelt een Azure Blob Storage in als doel. Gegevens die worden gegenereerd door WindTurbineGenerator, worden gestreamd naar de Event Hub en automatisch vastgelegd in Azure Storage als Avro-bestanden. 
*   Vervolgens maakt u een Azure Event Grid-abonnement met de Event Hubs-naamruimte als bron en het Azure Functions-eindpunt als doel.
*   Wanneer een nieuw Avro-bestand wordt verzonden naar de Azure Storage Blob met de functie Event Hubs Capture, stelt Event Grid Azure Functions op de hoogte met de blob-URI. Vervolgens worden de gegevens gemigreerd van de blob naar een SQL Data Warehouse.

In deze zelfstudie voert u de volgende acties uit: 

> [!div class="checklist"]
> * De infrastructuur implementeren
> * Code publiceren naar een Azure Functions-app
> * Event Grid-abonnement maken vanuit de Azure Functions-app
> * Voorbeeldgegevens streamen naar Event Hub. 
> * Vastgelegde gegevens verifiëren in SQL Data Warehouse

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- [Visual studio 2019](https://www.visualstudio.com/vs/). Zorg ervoor dat u tijdens de installatie de volgende werkbelastingen installeert: .NET-desktopontwikkeling, Azure-ontwikkeling, ASP.NET- en webontwikkeling, Node.js-ontwikkeling en Python-ontwikkeling
- Download het [Git-voorbeeld](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/EventHubsCaptureEventGridDemo) De voorbeeldoplossing bevat de volgende componenten:
    - *WindTurbineDataGenerator*: een eenvoudige uitgever waarmee voorbeeldgegevens van windturbines worden verzonden naar een Event Hub waarvoor Capture is ingeschakeld
    - *FunctionDWDumper*: een Azure-functie die een Event Grid-melding ontvangt wanneer een Avro-bestand wordt vastgelegd in de Azure Storage Blob. Het URI-pad van de blob wordt ontvangen en de inhoud wordt gelezen, waarna deze gegevens worden doorgestuurd naar een SQL Data Warehouse.

    In dit voorbeeld wordt gebruik gemaakt van het nieuwste Azure.Messaging.EventHubs-pakket. U vindt [hier](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)het oude voorbeeld dat het microsoft.Azure.EventHubs-pakket gebruikt. 

### <a name="deploy-the-infrastructure"></a>De infrastructuur implementeren
Gebruik Azure PowerShell of de Azure CLI om de infrastructuur te implementeren die nodig is voor deze zelfstudie met behulp van deze [Azure Resource Manager-sjabloon](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json). Met deze sjabloon maakt u de volgende bronnen:

-   Event Hub waarbij de functie Capture is ingeschakeld
-   Opslagaccount voor de vastgelegde gebeurtenisgegevens
-   Azure App Service-plan voor het hosten van de Functions-app
-   Functions-app voor het verwerken van vastgelegde gebeurtenisbestanden
-   SQL Server voor het hosten van het datawarehouse
-   SQL Data Warehouse voor het opslaan van de gemigreerde gegevens

In de volgende secties vindt u Azure CLI- en Azure PowerShell-opdrachten voor het implementeren van de infrastructuur die is vereist voor de zelfstudie. Werk de namen van de volgende objecten bij voordat u de opdrachten uitvoert: 

- Azure-resourcegroep 
- Regio van de resourcegroup
- Event Hubs-naamruimte
- Event Hub
- Azure SQL Server
- SQL-gebruiker (en wachtwoord)
- Azure SQL-database
- Azure Storage 
- Azure Functions-app

Het duurt even voordat alle Azure-artefacten zijn gemaakt met deze scripts. Wacht totdat het script is voltooid voordat u doorgaat. Als de implementatie mislukt, verwijdert u de resourcegroep, lost u het gerapporteerde probleem op en voert u de opdracht opnieuw uit. 

#### <a name="azure-cli"></a>Azure CLI
Voer de volgende opdrachten uit om de sjabloon te implementeren met Azure CLI:

```azurecli-interactive
az group create -l westus -n rgDataMigrationSample

az group deployment create `
  --resource-group rgDataMigrationSample `
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json `
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

#### <a name="azure-powershell"></a>Azure PowerShell
Voer de volgende opdrachten uit om de sjabloon te implementeren met PowerShell:

```powershell
New-AzResourceGroup -Name rgDataMigration -Location westcentralus

New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```


### <a name="create-a-table-in-sql-data-warehouse"></a>Een tabel maken in SQL Data Warehouse 
Maak een tabel in uw SQL Data Warehouse door het script [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) uit te voeren met [Visual Studio](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-query-visual-studio.md), [SQL Server Management Studio](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-query-ssms.md) of de Query-editor in de portal. 

```sql
CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
    [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
    [MeasureTime] datetime NULL, 
    [GeneratedPower] float NULL, 
    [WindSpeed] float NULL, 
    [TurbineSpeed] float NULL
)
WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
```

## <a name="publish-code-to-the-functions-app"></a>Code publiceren naar de Functions-app

1. Open de oplossing *EventHubsCaptureEventGridDemo.sln* in Visual Studio 2019.

1. Klik in Solution Explorer met de rechtermuisknop op *FunctionEGDWDumper* en selecteer **Publish**.

   ![Functie-app publiceren](./media/store-captured-data-data-warehouse/publish-function-app.png)

1. Selecteer **Azure Function App** en **Select Existing**. Selecteer **Publiceren**.

   ![Bestaande functie-app](./media/store-captured-data-data-warehouse/pick-target.png)

1. Selecteer de functie-app die u via de sjabloon hebt geïmplementeerd. Selecteer **OK**.

   ![Functie-app selecteren](./media/store-captured-data-data-warehouse/select-function-app.png)

1. Als Visual Studio het profiel heeft geconfigureerd, selecteert u **Publish**.

   ![Selecteer Publish](./media/store-captured-data-data-warehouse/select-publish.png)

Nadat de functie is gepubliceerd, kunt u zich abonneren op de vastleggebeurtenis vanuit Event Hubs.


## <a name="create-an-event-grid-subscription-from-the-functions-app"></a>Event Grid-abonnement maken vanuit de Azure Functions-app
 
1. Ga naar de [Azure-portal.](https://portal.azure.com/) Selecteer de resourcegroep en de functie-app.

   ![Functie-app weergeven](./media/store-captured-data-data-warehouse/view-function-app.png)

1. Selecteer de functie.

   ![Functie selecteren](./media/store-captured-data-data-warehouse/select-function.png)

1. Selecteer **Add Event Grid subscription**.

   ![Abonnement toevoegen](./media/store-captured-data-data-warehouse/add-event-grid-subscription.png)

1. Geef een naam op voor het Event Grid-abonnement. Gebruik **Event Hubs Namespaces** als het gebeurtenistype. Geef waarden op om uw exemplaar van de Event Hubs-naamruimte te selecteren. Laat de waarde voor het eindpunt ongewijzigd (Subscriber Endpoint). Selecteer **Maken**.

   ![Abonnement maken](./media/store-captured-data-data-warehouse/set-subscription-values.png)

## <a name="generate-sample-data"></a>Voorbeeldgegevens genereren  
U bent nu klaar met het instellen van uw Event Hub, SQL Data Warehouse, Azure Functions-app en Event Grid-abonnement. U kunt WindTurbineDataGenerator.exe uitvoeren om gegevensstromen te genereren naar de Event Hub nadat u de verbindingsreeks en de naam van uw Event Hub hebt bijgewerkt in de broncode. 

1. Selecteer in de portal de naamruimte van uw gebeurtenishub. Selecteer **Verbindingstekenreeksen**.

   ![Selecteer Verbindingsreeksen](./media/store-captured-data-data-warehouse/event-hub-connection.png)

2. **RootmanageSharedAccessKey selecteren**

   ![Sleutel selecteren](./media/store-captured-data-data-warehouse/show-root-key.png)

3. Kopieer **Verbindingsreeks-primaire sleutel**.

   ![Sleutel kopiëren](./media/store-captured-data-data-warehouse/copy-key.png)

4. Ga terug naar het Visual Studio-project. Open *program.cs* in het project *WindTurbineDataGenerator*.

5. Werk de waarden voor **EventHubConnectionString** en **EventHubName** bij met verbindingsreeks en de naam van uw Event Hub. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Maak de oplossing en voer de toepassing WindTurbineGenerator.exe uit. 

## <a name="verify-captured-data-in-data-warehouse"></a>Vastgelegde gegevens verifiëren in het datawarehouse
Voer na een paar minuten een query uit op de tabel in uw SQL Data Warehouse. U ziet dat de gegevens die zijn gegenereerd door WindTurbineDataGenerator, zijn gestreamd naar uw Event Hub, zijn vastgelegd in een Azure Storage-container en vervolgens door Azure Functions zijn gemigreerd naar de SQL Data Warehouse-tabel.  

## <a name="next-steps"></a>Volgende stappen 
U kunt krachtige hulpmiddelen voor gegevensvisualisatie gebruiken met uw datawarehouse om bruikbare inzichten te verkrijgen.

In dit artikel leest u hoe u [Power BI met SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-integrate-power-bi) gebruikt



