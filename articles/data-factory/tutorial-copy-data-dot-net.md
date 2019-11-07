---
title: 'Gegevens kopiëren van Azure-Blob Storage naar SQL Database '
description: Deze zelfstudie bevat stapsgewijze instructies voor het kopiëren van gegevens uit Azure Blob Storage naar Azure SQL Database.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jingwang
ms.openlocfilehash: 96b1e5a9633ee141fd4aa369468a8866d87f27f2
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683645"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Gegevens met Azure Data Factory kopiëren van Azure Blob Storage naar SQL Database

In deze zelfstudie maakt u een data factory met een pijplijn die gegevens kopieert van Azure Blob Storage naar Azure SQL Database. Het configuratiepatroon in deze zelfstudie geldt voor het kopiëren van een gegevensarchief op basis van bestanden naar een relationeel gegevensarchief. Zie de tabel [Ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die worden ondersteund als bron en als sink.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Maak een gegevensfactory.
> * Gekoppelde Azure Storage- en Azure SQL Database-services maken.
> * Gegevenssets voor Azure Blob Storage en Azure SQL Database maken.
> * Een pijplijn met een kopieeractiviteit maken
> * Start een pijplijnuitvoering.
> * De uitvoering van de pijplijn en van de activiteit controleren.

In deze zelfstudie wordt .NET SDK gebruikt. U kunt andere methoden gebruiken voor interactie met Azure Data Factory. Kijk voor voorbeelden onder 'Quickstarts'.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* **Een Azure Storage-account**. U gebruikt de blob-opslag als **bron**-gegevensopslag. Als u geen Azure Storage-account hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-quickstart-create-account.md) voor de stappen voor het maken van een account.
* **Azure SQL-database**. U gebruikt de database als **sink**-gegevensopslag. Als u geen Azure SQL-database hebt, raadpleegt u het artikel [Een Azure SQL-database maken](../sql-database/sql-database-get-started-portal.md) voor de stappen voor het maken van een account.
* **Visual Studio** 2015, of 2017. De procedures in dit artikel zijn gebaseerd op Visual Studio 2017.
* **Download en installeer [Azure .NET SDK](https://azure.microsoft.com/downloads/)** .
* **Maak een toepassing in Azure Active Directory** aan de hand van [deze instructie](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Noteer de volgende waarden voor gebruik in latere stappen: **toepassings-id**, **verificatiesleutel** en **tenant-id**. Wijs de toepassing toe aan de rol '**Inzender**' door de instructies in hetzelfde artikel te volgen.

### <a name="create-a-blob-and-a-sql-table"></a>Een blob en een SQL-tabel maken

Voer nu de volgende stappen uit om Azure Blob Storage en Azure SQL Database voor te bereiden voor gebruik in deze zelfstudie:

#### <a name="create-a-source-blob"></a>Een bron-blob maken

1. Start Kladblok. Kopieer de volgende tekst en sla deze op schijf op in het bestand **inputEmp.txt**.

    ```
    John|Doe
    Jane|Doe
    ```

2. Gebruik hulpprogramma's zoals [Azure Opslagverkenner](https://storageexplorer.com/) om de container **adfv2tutorial** te maken en om het bestand **inputEmp.txt** te uploaden naar de container.

#### <a name="create-a-sink-sql-table"></a>Een SQL-sink-tabel maken

1. Gebruik het volgende SQL-script om de tabel **dbo.emp** te maken in de Azure SQL-database.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Geef Azure-services toegang tot de SQL-server. Zorg ervoor dat **Toegang tot Azure-services toestaan** op **AAN** staat voor uw Azure SQL-server, zodat de Data Factory-service gegevens kan wegschrijven naar uw Azure SQL-server. Voer de volgende stappen uit om dit te controleren en de instelling in te schakelen:

    1. Klik op de hub **Meer services** aan de linkerkant en klik op **SQL-servers**.
    2. Selecteer uw server en klik op **Firewall** onder **INSTELLINGEN**.
    3. Klik op de pagina **Firewallinstellingen** op **AAN** bij **Toegang tot Azure-services toestaan**.


## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

Maak met behulp van Visual Studio 2015/2017 een C# .NET-consoletoepassing.

1. Start **Visual Studio**.
2. Klik op **File**, houd de muisaanwijzer op **New** en klik op **Project**.
3. Selecteer **Visual C#**  -> **Console App (.NET Framework)** in de lijst met projecttypen aan de rechterkant. .NET versie 4.5.2 of hoger is vereist.
4. Voer **ADFv2Tutorial** in als naam.
5. Klik op **OK** om het project aan te maken.

## <a name="install-nuget-packages"></a>NuGet-pakketten installeren

1. Klik op **Hulpprogramma's** -> **NuGet Package Manager** -> **Package Manager-console**.
2. Voer in **Package Manager Console** de volgende opdrachten uit om pakketten te installeren. Raadpleeg voor meer informatie het [NuGet-pakket Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Een data factory-client maken

1. Open **Program.cs** en neem de volgende instructies op om verwijzingen naar naamruimten toe te voegen.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

    
2. Voeg de volgende code toe aan de methode **Main** waarmee variabelen worden ingesteld. Vervang de plaatsaanduidingen door uw eigen waarden. Voor een lijst met Azure-regio’s waarin Data Factory momenteel beschikbaar is, selecteert u op de volgende pagina de regio’s waarin u geïnteresseerd bent, vouwt u vervolgens **Analytics** uit en gaat u naar **Data Factory**: [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/). De gegevensopslagexemplaren (Azure Storage, Azure SQL Database, enzovoort) en berekeningen (HDInsight, enzovoort) die worden gebruikt in Data Factory, kunnen zich in andere regio's bevinden.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "East US";
    string dataFactoryName = "<specify the name of a data factory to create. It must be globally unique.>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString = "Server=tcp:<your server name>.database.windows.net,1433;Database=<your database name>;User ID=<your username>@<your server name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Voeg de volgende code toe aan de methode **Main** om een instantie van de klasse **DataFactoryManagementClient** te maken. U gebruikt dit object om een data factory, een gekoppelde service, gegevenssets en een pijplijn te maken. U kunt dit object ook gebruiken om de details van de pijplijnuitvoering te controleren.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Een data factory maken

Voeg de volgende code toe aan de methode **Main** om een **data factory** te maken.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()

};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Gekoppelde services maken

In deze zelfstudie maakt u twee gekoppelde services voor respectievelijk bron en sink:

### <a name="create-an-azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service maken

Voeg de volgende code toe aan de methode **Main** om een **gekoppelde Azure Storage-service** te maken. Zie het onderwerp over [gekoppelde service-eigenschappen van Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) voor meer informatie over ondersteunde eigenschappen en details.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

### <a name="create-an-azure-sql-database-linked-service"></a>Een gekoppelde Azure SQL Database-service maken

Voeg de volgende code toe aan de methode **Main** om een **gekoppelde Azure SQL Database-service** te maken. Zie het onderwerp over [gekoppelde service-eigenschappen van Azure SQL Database](connector-azure-sql-database.md#linked-service-properties) voor meer informatie over ondersteunde eigenschappen en details.

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings));
```

## <a name="create-datasets"></a>Gegevenssets maken

In deze sectie maakt u twee gegevenssets: één voor de bron en de andere voor de sink. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Een gegevensset maken voor de brongegevens in Azure Blob

Voeg de volgende code toe aan de methode **Main** om een **Azure Blob Storage-gegevensset** te maken. Zie het onderwerp over [eigenschappen van Azure Blob Storage-gegevenssets](connector-azure-blob-storage.md#dataset-properties) voor meer informatie over ondersteunde eigenschappen en details.

U definieert een gegevensset die de brongegevens in Azure Blob vertegenwoordigt. Deze Blob-gegevensset verwijst naar de gekoppelde Azure Storage-service die u in de vorige stap hebt gemaakt en beschrijft het volgende:

- De locatie van de blob waaruit moet worden gekopieerd: **FolderPath** en **FileName**;
- De blob-indeling die aangeeft hoe de inhoud moet worden geparseerd: **TextFormat** en de bijbehorende instellingen (bijvoorbeeld het kolomscheidingsteken).
- De gegevensstructuur, inclusief kolomnamen en gegevenstypen die in dit geval worden gekoppeld aan de SQL-sink-tabel.

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement
            {
                Name = "FirstName",
                Type = "String"
            },
            new DatasetDataElement
            {
                Name = "LastName",
                Type = "String"
            }
        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Een gegevensset maken voor de sink-gegevens in Azure SQL Database

Voeg de volgende code toe aan de methode **Main** om een **Azure SQL Database-gegevensset** te maken. Zie het onderwerp over [eigenschappen van Azure SQL Database-gegevenssets](connector-azure-sql-database.md#dataset-properties) voor meer informatie over ondersteunde eigenschappen en details.

U definieert een gegevensset die de sink-gegevens in Azure SQL Database vertegenwoordigt. Deze gegevensset verwijst naar de gekoppelde Azure SQL Database-service die u in de vorige stap hebt gemaakt. De gegevensset geeft ook aan in welke SQL-tabel de gekopieerde gegevens worden opgeslagen. 

```csharp
// Create an Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Een pijplijn maken

Voeg de volgende code toe aan de methode **Main** om **een pijplijn met een kopieeractiviteit te maken**. In deze zelfstudie bevat deze pijplijn maar één activiteit: een kopieeractiviteit waarbij de blob-gegevensset als bron wordt gebruikt en de SQL-gegevensset als sink. Zie het Engelstalige onderwerp over de [kopieeractiviteit](copy-activity-overview.md) voor meer informatie.

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = sqlDatasetName
                }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Een pijplijnuitvoering maken

Voeg de volgende code toe aan de methode **Main** om een **pijplijnuitvoering te activeren**.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Een pijplijnuitvoering controleren

1. Voeg de volgende code toe aan de methode **Main** om continu de status van de pijplijnuitvoering te controleren totdat deze klaar is met het kopiëren van de gegevens.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Voeg de volgende code toe aan de methode **Main** om details van de kopieeractiviteit weer te geven, zoals de omvang van de gelezen of weggeschreven gegevens.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
 
    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
    }
    else
        Console.WriteLine(activityRuns.First().Error);
    
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>De code uitvoeren

Bouw en start de toepassing en controleer vervolgens de uitvoering van de pijplijn.

In de console ziet u de voortgang van het maken van een data factory, een gekoppelde service, gegevenssets, pijplijn en pijplijnuitvoering. Vervolgens wordt de uitvoeringsstatus van de pijplijn gecontroleerd. Wacht totdat u details ziet van de uitvoering van de kopieeractiviteit, waaronder de omvang van de gelezen/weggeschreven gegevens. Vervolgens gebruikt u hulpprogramma's zoals SSMS (SQL Server Management Studio) of Visual Studio om verbinding te maken met de database van Azure SQL Database en te controleren of de gegevens naar de opgegeven tabel zijn gekopieerd.

### <a name="sample-output"></a>Voorbeelduitvoer

```json
Creating a data factory AdfV2Tutorial...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedDataIntegrationUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```


## <a name="next-steps"></a>Volgende stappen

Met de pijplijn in dit voorbeeld worden gegevens gekopieerd van de ene locatie naar een andere locatie in een Azure Blob-opslag. U hebt geleerd hoe u: 

> [!div class="checklist"]
> * Maak een gegevensfactory.
> * Gekoppelde Azure Storage- en Azure SQL Database-services maken.
> * Gegevenssets voor Azure Blob Storage en Azure SQL Database maken.
> * Een pijplijn met een kopieeractiviteit maken
> * Start een pijplijnuitvoering.
> * De uitvoering van de pijplijn en van de activiteit controleren.


Ga verder met de volgende zelfstudie als u wilt weten hoe u on-premises gegevens kopieert naar de cloud: 

> [!div class="nextstepaction"]
>[On-premises gegevens kopiëren naar de cloud](tutorial-hybrid-copy-powershell.md)
