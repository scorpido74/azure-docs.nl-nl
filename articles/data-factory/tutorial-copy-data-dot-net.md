---
title: Gegevens kopiëren van Azure-Blob Storage naar Azure SQL Database
description: Deze zelfstudie bevat stapsgewijze instructies voor het kopiëren van gegevens uit Azure Blob Storage naar Azure SQL Database.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 11/08/2019
ms.author: jingwang
ms.openlocfilehash: 93c4f71c762cff3e3f5a01f0e2595f3498f9d38d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977315"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Gegevens met Azure Data Factory kopiëren van Azure Blob Storage naar SQL Database

In deze zelfstudie maakt u een data factory met een pijplijn die gegevens kopieert van Azure Blob Storage naar Azure SQL Database. Het configuratiepatroon in deze zelfstudie geldt voor het kopiëren van een gegevensarchief op basis van bestanden naar een relationeel gegevensarchief. Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en Sinks.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Gekoppelde Azure Storage- en Azure SQL Database-services maken.
> * Gegevenssets voor Azure Blob Storage en Azure SQL Database maken.
> * Een pijplijn met een kopieeractiviteit maken
> * Een pijplijnuitvoering starten.
> * De uitvoering van de pijplijn en van de activiteit controleren

In deze zelfstudie wordt .NET SDK gebruikt. U kunt andere mechanismen gebruiken om te communiceren met Azure Data Factory; Raadpleeg de voor beelden onder **Quick**starts.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* *Azure Storage-account*. U gebruikt de blob-opslag als *bron*-gegevensopslag. Als u geen Azure Storage-account hebt, raadpleegt u [een opslag account voor algemeen gebruik maken](../storage/common/storage-account-create.md).
* *Azure SQL-database*. U gebruikt de database als *sink*-gegevensopslag. Als u geen Azure SQL Database hebt, raadpleegt u [een Azure SQL database maken](../sql-database/sql-database-single-database-get-started.md).
* *Visual Studio*. In de procedure in dit artikel wordt gebruikgemaakt van Visual Studio 2019.
* *[Azure SDK voor .net](/dotnet/azure/dotnet-tools)* .
* *Azure Active Directory-toepassing*. Als u geen Azure Active Directory toepassing hebt, raadpleegt u het gedeelte [een Azure Active Directory toepassing maken](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) van [How to: gebruik de portal om een Azure AD-toepassing te maken](../active-directory/develop/howto-create-service-principal-portal.md). Kopieer de volgende waarden voor gebruik in latere stappen: **toepassings-id**, **verificatie sleutel**en **Directory-id (Tenant)** . Wijs de toepassing toe aan de rol **Inzender** door de instructies in hetzelfde artikel te volgen.

### <a name="create-a-blob-and-a-sql-table"></a>Een blob en een SQL-tabel maken

Bereid nu uw Azure-Blob en Azure SQL Database voor de zelf studie voor door een bron blog en een Sink SQL-tabel te maken.

#### <a name="create-a-source-blob"></a>Een bron-blob maken

Maak eerst een bron-BLOB door een container te maken en een invoer tekst bestand te uploaden:

1. Open Kladblok. Kopieer de volgende tekst en sla deze lokaal op in een bestand met de naam *inputEmp. txt*.

    ```inputEmp.txt
    John|Doe
    Jane|Doe
    ```

2. Gebruik een hulp programma zoals [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) om de *adfv2tutorial* -container te maken en om het bestand *inputEmp. txt* naar de container te uploaden.

#### <a name="create-a-sink-sql-table"></a>Een SQL-sink-tabel maken

Maak vervolgens een Sink SQL-tabel:

1. Gebruik het volgende SQL-script om de tabel *dbo.emp* te maken in de Azure SQL-database.

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

2. Geef Azure-services toegang tot de SQL-server. Zorg ervoor dat u toegang verleent tot Azure-Services in uw Azure SQL-Server, zodat de Data Factory-service gegevens naar uw Azure SQL-Server kan schrijven. Voer de volgende stappen uit om dit te controleren en de instelling in te schakelen:

    1. Ga naar de [Azure Portal](https://portal.azure.com) om uw SQL-Server te beheren. Zoek en selecteer **SQL-servers**.

    2. Selecteer uw server.

    3. Selecteer in de **beveiligings** titel van het menu SQL Server de optie **firewalls en virtuele netwerken**.

    4. Selecteer **op**de pagina **firewall en virtuele netwerken** onder **Azure-Services en-bronnen toestaan om toegang te krijgen tot deze server**.

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

Maak met behulp van Visual C# Studio een .net-console toepassing.

1. Open Visual Studio.
2. Selecteer in het **Start** venster **een nieuw project maken**.
3. Kies in het venster **een nieuw project maken** de C# versie van de **console-app (.NET Framework)** in de lijst met project typen. Selecteer vervolgens **Volgende**.
4. Voer in het venster **uw nieuwe project configureren** de **project naam** *ADFv2Tutorial*in. Voor **locatie**, bladert u naar en/of maakt u de Directory voor het opslaan van het project in. Selecteer vervolgens **Maken**. Het nieuwe project wordt weer gegeven in de Visual Studio IDE.

## <a name="install-nuget-packages"></a>NuGet-pakketten installeren

Installeer vervolgens de vereiste bibliotheek pakketten met behulp van de NuGet Package Manager.

1. Kies in de menu balk **Hulpprogram ma's** > **NuGet package manager** > **Package Manager-console**.
2. Voer in het deel venster **Package Manager-console** de volgende opdrachten uit om pakketten te installeren. Zie voor informatie over het Azure Data Factory NuGet-pakket [micro soft. Azure. Management. DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```package manager console
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -PreRelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Een data factory-client maken

Volg deze stappen om een data factory-client te maken.

1. Open *Program.cs*en vervang de bestaande `using`-instructies door de volgende code om verwijzingen naar naam ruimten toe te voegen.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Voeg de volgende code toe aan de `Main`-methode waarmee variabelen worden ingesteld. Vervang de 14 tijdelijke aanduidingen door uw eigen waarden.

    Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/)voor een overzicht van de Azure-regio's waarin Data Factory momenteel beschikbaar is. Kies in de vervolg keuzelijst **producten** **Bladeren** > **Analytics** - > **Data Factory**. Kies vervolgens in de vervolg keuzelijst **regio's** de regio's die u interesseren. Er wordt een raster met de beschikbaarheids status van Data Factory producten voor de geselecteerde regio's weer gegeven.

    > [!NOTE]
    > Gegevens archieven, zoals Azure Storage en Azure SQL Database, en reken processen, zoals HDInsight, die Data Factory gebruikt, kunnen zich in andere regio's bevindt dan wat u voor Data Factory kiest.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "<location to create the data factory in, such as East US>";
    string dataFactoryName = "<name of data factory to create (must be globally unique)>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString =
        "Server=tcp:<your server name>.database.windows.net,1433;" +
        "Database=<your database name>;" +
        "User ID=<your username>@<your server name>;" +
        "Password=<your password>;" +
        "Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Voeg de volgende code toe aan de `Main` methode waarmee een instantie van `DataFactoryManagementClient` klasse wordt gemaakt. U gebruikt dit object om een data factory, een gekoppelde service, gegevenssets en een pijplijn te maken. U kunt dit object ook gebruiken om de details van de pijplijnuitvoering te controleren.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync(
        "https://management.azure.com/", cc
    ).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Een data factory maken

Voeg de volgende code toe aan de `Main` methode waarmee een *Data Factory*wordt gemaakt.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};

client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings)
);

while (
    client.Factories.Get(
        resourceGroup, dataFactoryName
    ).ProvisioningState == "PendingCreation"
)
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Gekoppelde services maken

In deze zelf studie maakt u twee gekoppelde services voor respectievelijk de bron en Sink.

### <a name="create-an-azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service maken

Voeg de volgende code toe aan de `Main` methode waarmee een *Azure Storage gekoppelde service*wordt gemaakt. Zie [Eigenschappen van gekoppelde Azure Blob-service](connector-azure-blob-storage.md#linked-service-properties)voor meer informatie over ondersteunde eigenschappen en Details.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey
        )
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings)
);
```

### <a name="create-an-azure-sql-database-linked-service"></a>Een gekoppelde Azure SQL Database-service maken

Voeg de volgende code toe aan de `Main` methode waarmee een *Azure SQL database gekoppelde service*wordt gemaakt. Zie [Azure SQL database eigenschappen van gekoppelde service](connector-azure-sql-database.md#linked-service-properties)voor meer informatie over ondersteunde eigenschappen en Details.

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings)
);
```

## <a name="create-datasets"></a>Gegevenssets maken

In deze sectie maakt u twee gegevens sets: een voor de bron, de andere voor de sink.

### <a name="create-a-dataset-for-source-azure-blob"></a>Een gegevensset maken voor de brongegevens in Azure Blob

Voeg de volgende code toe aan de `Main` methode waarmee een *Azure Blob-gegevensset*wordt gemaakt. Zie [Eigenschappen van Azure Blob-gegevensset](connector-azure-blob-storage.md#dataset-properties)voor meer informatie over ondersteunde eigenschappen en Details.

U definieert een gegevensset die de brongegevens in Azure Blob vertegenwoordigt. Deze Blob-gegevensset verwijst naar de gekoppelde Azure Storage-service die u in de vorige stap hebt gemaakt en beschrijft het volgende:

- De locatie van de BLOB waaruit moet worden gekopieerd: `FolderPath` en `FileName`
- De BLOB-indeling die aangeeft hoe de inhoud moet worden geparseerd: `TextFormat` en de bijbehorende instellingen, zoals kolom scheidings teken
- De gegevens structuur, inclusief kolom namen en gegevens typen, die in dit voor beeld worden toegewezen aan de SQL-Sink-tabel

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement { Name = "FirstName", Type = "String" },
            new DatasetDataElement { Name = "LastName", Type = "String" }
        }
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings)
);
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Een gegevensset maken voor de sink-gegevens in Azure SQL Database

Voeg de volgende code toe aan de `Main` methode waarmee een *Azure SQL database-gegevensset*wordt gemaakt. Zie [Eigenschappen van Azure SQL database-gegevensset](connector-azure-sql-database.md#dataset-properties)voor meer informatie over ondersteunde eigenschappen en Details.

U definieert een gegevensset die de sink-gegevens in Azure SQL Database vertegenwoordigt. Deze gegevensset verwijst naar de Azure SQL Database gekoppelde service die u in de vorige stap hebt gemaakt. De gegevensset geeft ook aan in welke SQL-tabel de gekopieerde gegevens worden opgeslagen.

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

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings)
);
```

## <a name="create-a-pipeline"></a>Een pijplijn maken

Voeg de volgende code toe aan de `Main` methode waarmee een *pijp lijn met een Kopieer activiteit*wordt gemaakt. In deze zelf studie bevat deze pijp lijn één activiteit: `CopyActivity`, die in de BLOB-gegevensset als bron en de SQL-gegevensset als Sink neemt. Zie [activiteit kopiëren in azure Data Factory](copy-activity-overview.md)voor meer informatie over de details van de Kopieer activiteit.

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
                new DatasetReference() { ReferenceName = blobDatasetName }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference { ReferenceName = sqlDatasetName }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings)
);
```

## <a name="create-a-pipeline-run"></a>Een pijplijnuitvoering maken

Voeg de volgende code toe aan de `Main` methode waarmee *een pijplijn uitvoering wordt geactiveerd*.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Een pijplijnuitvoering controleren

Voeg nu de code in om de status van de pijplijn uitvoeringen te controleren en om meer informatie over de uitvoering van de Kopieer activiteit te krijgen.

1. Voeg de volgende code toe aan de methode `Main` om voortdurend de statussen van de pijplijn uitvoering te controleren totdat deze klaar is met het kopiëren van de gegevens.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(
            resourceGroup, dataFactoryName, runResponse.RunId
        );
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Voeg de volgende code toe aan de `Main` methode waarmee details van de uitvoer van de Kopieer activiteit worden opgehaald, zoals de grootte van de gegevens die zijn gelezen of geschreven.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    RunFilterParameters filterParams = new RunFilterParameters(
        DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)
    );

    ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
        resourceGroup, dataFactoryName, runResponse.RunId, filterParams
    );

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(queryResponse.Value.First().Output);
    }
    else
        Console.WriteLine(queryResponse.Value.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>De code uitvoeren

Bouw de toepassing op door **build** > **Build-oplossing**te kiezen. Start vervolgens de toepassing door **Debug** > **Start Debugging**te kiezen en de uitvoering van de pijp lijn te controleren.

In de console ziet u de voortgang van het maken van een data factory, een gekoppelde service, gegevenssets, pijplijn en pijplijnuitvoering. Vervolgens wordt de uitvoeringsstatus van de pijplijn gecontroleerd. Wacht totdat u de details van de Kopieer activiteit ziet met de grootte van de gelezen/geschreven gegevens. Met hulpprogram ma's als SQL Server Management Studio (SSMS) of Visual Studio kunt u verbinding maken met uw doel Azure SQL Database en controleren of de opgegeven doel tabel de gekopieerde gegevens bevat.

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

Met de pijplijn in dit voorbeeld worden gegevens gekopieerd van de ene naar de andere locatie in Azure Blob Storage. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Gekoppelde Azure Storage- en Azure SQL Database-services maken.
> * Gegevenssets voor Azure Blob Storage en Azure SQL Database maken.
> * Maak een pijp lijn met een Kopieer activiteit.
> * Een pijplijnuitvoering starten.
> * De uitvoering van de pijplijn en van de activiteit controleren

Ga verder met de volgende zelfstudie als u wilt weten hoe u on-premises gegevens kopieert naar de cloud:

> [!div class="nextstepaction"]
>[On-premises gegevens kopiëren naar de cloud](tutorial-hybrid-copy-powershell.md)
