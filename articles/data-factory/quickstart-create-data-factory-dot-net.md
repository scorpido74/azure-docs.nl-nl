---
title: Azure-data factory maken met behulp van .NET SDK
description: Maak een Azure data factory om gegevens te kopiëren van de ene locatie in Azure Blob Storage naar de andere.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/24/2019
ms.author: jingwang
ms.openlocfilehash: 71a256b0350742f0e7a7b95519fafff5bc32f58b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928794"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-net-sdk"></a>Snelstartgids: een data factory en pijp lijn maken met behulp van .NET SDK

> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Huidige versie](quickstart-create-data-factory-dot-net.md)

In deze snelstartgids wordt beschreven hoe u .NET SDK kunt gebruiken om een Azure data factory te maken. Met de pijplijn die u in deze data factory maakt, worden gegevens **gekopieerd** van één map naar een andere map in een Azure Blob Storage. Zie [Zelfstudie: Gegevens transformeren met Spark](tutorial-transform-data-spark-portal.md) voor meer informatie over het **transformeren** van gegevens met Azure Data Factory.

> [!NOTE]
> Dit artikel is geen gedetailleerde introductie tot de Data Factory-service. Zie [Inleiding tot Azure Data Factory](introduction.md) voor een inleiding tot Azure Data Factory-service.

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="visual-studio"></a>Visual Studio

In de procedure in dit artikel wordt gebruikgemaakt van Visual Studio 2019. De procedures voor Visual Studio 2013, 2015 en 2017 verschillen enigszins.

### <a name="azure-net-sdk"></a>Azure .NET SDK

Download en installeer [Azure .NET SDK](https://azure.microsoft.com/downloads/) op uw computer.

## <a name="create-an-application-in-azure-active-directory"></a>Een toepassing maken in Azure Active Directory

In de secties in *How to: gebruik de portal om een Azure AD-toepassing en Service-Principal te maken die toegang hebben tot resources*. Volg de instructies voor het uitvoeren van deze taken:

1. Maak in [een Azure Active Directory-toepassing maken](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)een toepassing die de .NET-toepassing vertegenwoordigt die u in deze zelf studie maakt. Voor de aanmeldings-URL kunt u een dummy-URL opgeven, zoals wordt getoond in het artikel (`https://contoso.org/exampleapp`).
2. Haal de **toepassings-id** en **Tenant-id**op in [waarden ophalen voor aanmelden](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)en noteer deze waarden die u later in deze zelf studie gebruikt. 
3. Haal in [certificaten en geheimen](../active-directory/develop/howto-create-service-principal-portal.md#certificates-and-secrets)de **verificatie sleutel**op en noteer deze waarde die u later in deze zelf studie gebruikt.
4. Wijs in [de toepassing toewijzen aan een rol](../active-directory/develop/howto-create-service-principal-portal.md#assign-the-application-to-a-role)de toepassing toe aan de rol **Inzender** op het abonnements niveau zodat de toepassing gegevens fabrieken kan maken in het abonnement.

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

Vervolgens maakt u een C# .net-console toepassing in Visual Studio:

1. Start **Visual Studio**.
2. Selecteer in het Start venster **een nieuw project maken** > **console-app (.NET Framework)** . .NET versie 4.5.2 of hoger is vereist.
3. Voer in **project naam** **ADFv2QuickStart**in.
4. Selecteer **Maken** om het project te maken.

## <a name="install-nuget-packages"></a>NuGet-pakketten installeren

1. Selecteer **Extra** > **NuGet-pakketbeheer** > **Package Manager Console**.
2. Voer in het deel venster **Package Manager-console** de volgende opdrachten uit om pakketten te installeren. Zie het [micro soft. Azure. Management. DataFactory nuget-pakket](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/)voor meer informatie.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
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

2. Voeg de volgende code toe aan de methode **Main** waarmee de variabelen worden ingesteld. Vervang de tijdelijke aanduidingen door uw eigen waarden. Voor een lijst met Azure-regio’s waarin Data Factory momenteel beschikbaar is, selecteert u op de volgende pagina de regio’s waarin u geïnteresseerd bent, vouwt u vervolgens **Analytics** uit en gaat u naar **Data Factory**: [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/). De gegevens archieven (Azure Storage, Azure SQL Database en meer) en berekeningen (HDInsight en anderen) die door data factory worden gebruikt, kunnen zich in andere regio's bevindt.

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string dataFactoryName = 
       "<specify the name of data factory to create. It must be globally unique.>";
   string storageAccount = "<your storage account name to copy data>";
   string storageKey = "<your storage account key>";
   // specify the container and input folder from which all files 
   // need to be copied to the output folder. 
   string inputBlobPath =
       "<path to existing blob(s) to copy data from, e.g. containername/inputdir>";
   //specify the contains and output folder where the files are copied
   string outputBlobPath =
       "<the blob path to copy data to, e.g. containername/outputdir>";

   // name of the Azure Storage linked service, blob dataset, and the pipeline
   string storageLinkedServiceName = "AzureStorageLinkedService";
   string blobDatasetName = "BlobDataset";
   string pipelineName = "Adfv2QuickStartPipeline";
   ```

3. Voeg de volgende code toe aan de methode **Main** om een instantie van de klasse **DataFactoryManagementClient** te maken. U gebruikt dit object om een data factory, een gekoppelde service, gegevenssets en een pijplijn te maken. U kunt dit object ook gebruiken om de details van de pijplijnuitvoering te controleren.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
       "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) {
       SubscriptionId = subscriptionId };
   ```

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

Voeg de volgende code toe aan de methode **Main** om een **data factory** te maken. 

```csharp
// Create a data factory
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState ==
       "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-a-linked-service"></a>Een gekoppelde service maken

Voeg de volgende code toe aan de methode **Main** om een **gekoppelde Azure Storage-service** te maken.

U maakt gekoppelde services in een gegevensfactory om uw gegevensarchieven en compute-services aan de gegevensfactory te koppelen. In deze Snelstartgids hoeft u slechts één Azure Storage gekoppelde service te maken voor zowel de Kopieer bron als de Sink-Store. het heeft de naam ' AzureStorageLinkedService ' in het voor beeld.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(
    storageLinkedService, client.SerializationSettings));
```

## <a name="create-a-dataset"></a>Een gegevensset maken

Voeg de volgende code toe aan de methode **Main** om een **Azure Blob Storage-gegevensset** te maken.

U definieert een gegevensset die de gegevens vertegenwoordigt die van een bron naar een sink moeten worden gekopieerd. In dit voorbeeld verwijst deze Blob-gegevensset naar de gekoppelde Azure Storage-service die u in de vorige stap gemaakt hebt. De gegevensset heeft een parameter nodig waarvan de waarde wordt ingesteld in een activiteit die de gegevensset verbruikt. De para meter wordt gebruikt om de ' folderPath ' te maken die verwijst naar de locatie waar de gegevens zich bevinden of worden opgeslagen.

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
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }
        }
    }
);
client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Een pijplijn maken

Voeg de volgende code toe aan de methode **Main** om **een pijplijn met een kopieeractiviteit te maken**.

In dit voor beeld bevat deze pijp lijn één activiteit en worden twee para meters gebruikt: het pad van de invoer-Blob en het pad naar de uitvoer-blob. De waarden voor deze parameters worden ingesteld wanneer de pijplijn wordt geactiveerd of uitgevoerd. De kopieeractiviteit verwijst naar dezelfde blobgegevensset die u in de vorige stap heb gemaakt als invoer en uitvoer. Wanneer de dataset wordt gebruikt als invoergegevensset, wordt het invoerpad opgegeven. En wanneer de dataset wordt gebruikt als uitvoergegevensset, wordt het uitvoerpad opgegeven. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Een pijplijnuitvoering maken

Voeg de volgende code toe aan de methode **Main** om een **pijplijnuitvoering te activeren**.

Met deze code worden ook waarden van de para meters **inputPath** en **outputPath** ingesteld die zijn opgegeven in de pijp lijn met de werkelijke waarden van de bron-en Sink-BLOB-paden.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName, parameters: parameters
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Een pijplijnuitvoering controleren

1. Voeg de volgende code toe aan de methode **Main** om continu de status te controleren totdat het kopiëren van de gegevens is voltooid.

   ```csharp
   // Monitor the pipeline run
   Console.WriteLine("Checking pipeline run status...");
   PipelineRun pipelineRun;
   while (true)
   {
       pipelineRun = client.PipelineRuns.Get(
           resourceGroup, dataFactoryName, runResponse.RunId);
       Console.WriteLine("Status: " + pipelineRun.Status);
       if (pipelineRun.Status == "InProgress" || pipelineRun.Status == "Queued")
           System.Threading.Thread.Sleep(15000);
       else
           break;
   }
   ```

2. Voeg de volgende code toe aan de methode **Main** waarmee details van de uitvoer van de Kopieer activiteit worden opgehaald, zoals de grootte van de gegevens die worden gelezen of geschreven.

   ```csharp
   // Check the copy activity run details
   Console.WriteLine("Checking copy activity run details...");

   RunFilterParameters filterParams = new RunFilterParameters(
       DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10));
   ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
       resourceGroup, dataFactoryName, runResponse.RunId, filterParams);
   if (pipelineRun.Status == "Succeeded")
       Console.WriteLine(queryResponse.Value.First().Output);
   else
       Console.WriteLine(queryResponse.Value.First().Error);
   Console.WriteLine("\nPress any key to exit...");
   Console.ReadKey();
   ```

## <a name="run-the-code"></a>De code uitvoeren

Bouw en start de toepassing en controleer vervolgens de uitvoering van de pijplijn.

In de console wordt de voortgang weergegeven van het maken van een data factory, een gekoppelde service, gegevenssets, pijplijn en pijplijnuitvoering. Vervolgens wordt de uitvoeringsstatus van de pijplijn gecontroleerd. Wacht totdat de details van de Kopieer activiteit worden weer gegeven met de grootte van de gegevens voor lezen/schrijven. Gebruik vervolgens hulpprogram ma's als [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) om te controleren of de BLOB (s) is gekopieerd naar ' inputblobpath ' in ' outputblobpath ' zoals u hebt opgegeven in de variabelen.

### <a name="sample-output"></a>Voorbeelduitvoer

```json
Creating data factory SPv2Factory0907...
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
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@{dataset().path}",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "AzureStorageLinkedService",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "path": {
        "type": "String"
      }
    }
  }
}
Creating pipeline Adfv2QuickStartPipeline...
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
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.inputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.outputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "name": "CopyFromBlobToBlob"
      }
    ],
    "parameters": {
      "inputPath": {
        "type": "String"
      },
      "outputPath": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 2,
    "billedDuration": 23
}

Press any key to exit...
```

## <a name="verify-the-output"></a>De uitvoer controleren

Met de pijp lijn wordt automatisch de uitvoermap gemaakt in de BLOB-container **adftutorial** . Vervolgens wordt het bestand **EMP. txt** vanuit de map invoer gekopieerd naar de uitvoermap. 

1. Selecteer in de Azure Portal op de pagina **adftutorial** -container die u hebt gestopt in het gedeelte [een map voor invoer en het bestand voor de BLOB-container toevoegen](#add-an-input-folder-and-file-for-the-blob-container) de optie **vernieuwen** om de uitvoermap weer te geven. 
2. Selecteer in de lijst met mappen **uitvoer**.
3. Controleer of het bestand **emp.txt** naar de uitvoermap is gekopieerd. 

## <a name="clean-up-resources"></a>Resources opschonen

Als u de data factory programmatisch wilt verwijderen, voegt u de volgende regels code toe aan het programma: 

```csharp
Console.WriteLine("Deleting the data factory");
client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>Volgende stappen

Met de pijplijn in dit voorbeeld worden gegevens gekopieerd van de ene locatie naar een andere locatie in een Azure Blob-opslag. Doorloop de [zelfstudies](tutorial-copy-data-dot-net.md) voor meer informatie over het gebruiken van Data Factory in andere scenario's. 
