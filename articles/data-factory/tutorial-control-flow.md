---
title: Vertakking in Azure Data Factory pijp lijn
description: Leer de stroom van gegevens in Azure Data Factory beheren door activiteiten te vertakken en te koppelen.
services: data-factory
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 9/27/2019
ms.openlocfilehash: 277616d9fcd15affc7ddc8ede5d9af3ff68c62f8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926604"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Activiteiten vertakken en koppelen in een Data Factory-pijplijn

In deze zelf studie maakt u een Data Factory pijp lijn die sommige controle stroom functies laat zien. Deze pijp lijn kopieert van een container in Azure Blob Storage naar een andere container in hetzelfde opslag account. Als de Kopieer activiteit slaagt, verzendt de pijp lijn Details over de geslaagde Kopieer bewerking in een e-mail bericht. Deze informatie kan de hoeveelheid geschreven gegevens omvatten. Als de Kopieer activiteit mislukt, worden de details van de Kopieer fout, zoals het fout bericht, verzonden in een e-mail. In de zelfstudie ziet u hoe u parameters kunt doorgeven.

Deze afbeelding biedt een overzicht van het scenario:

![Overzicht](media/tutorial-control-flow/overview.png)

Deze zelf studie laat zien hoe u de volgende taken kunt uitvoeren:

> [!div class="checklist"]
> * Een gegevensfactory maken
> * Een gekoppelde Azure Storage-service maken
> * Een Azure Blob-gegevensset maken
> * Een pijplijn met een kopieeractiviteit en een webactiviteit maken
> * Verzenden van uitvoer van activiteiten naar latere activiteiten
> * Para meters en systeem variabelen gebruiken
> * Een pijplijnuitvoering starten
> * De uitvoering van de pijplijn en van de activiteit controleren

In deze zelfstudie wordt .NET SDK gebruikt. U kunt andere mechanismen gebruiken om met Azure Data Factory te communiceren. Zie Quick starts voor [5 minuten](/azure/data-factory/quickstart-create-data-factory-portal)voor Data Factory Quick starts.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Azure Storage-account. U gebruikt Blob Storage als een brongegevens opslag. Zie [een opslag account maken](../storage/common/storage-quickstart-create-account.md)als u geen Azure Storage-account hebt.
* Azure Storage Explorer. Zie [Azure Storage Explorer](https://storageexplorer.com/)als u dit hulp programma wilt installeren.
* Azure SQL Database. U gebruikt de Data Base als Sink-gegevens archief. Als u geen Azure SQL Database hebt, raadpleegt u [een Azure SQL database maken](../sql-database/sql-database-get-started-portal.md).
* Visual Studio. In dit artikel wordt gebruikgemaakt van Visual Studio 2019.
* Azure .NET SDK. Down load en installeer de [Azure .NET SDK](https://azure.microsoft.com/downloads/).

Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/)voor een lijst met Azure-regio's waarin Data Factory momenteel beschikbaar is. De gegevens archieven en berekeningen kunnen zich in andere regio's bevindt. De winkels bevatten Azure Storage en Azure SQL Database. De berekeningen bevatten HDInsight, die Data Factory gebruikt.

Maak een toepassing zoals beschreven in [een Azure Active Directory-toepassing maken](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Wijs de toepassing toe aan de rol **Inzender** door de instructies in hetzelfde artikel te volgen. U hebt verschillende waarden nodig voor latere delen van deze zelf studie, zoals de **toepassing (client) ID** en **Directory (Tenant)-ID**.

### <a name="create-a-blob-table"></a>Een BLOB-tabel maken

1. Open een teksteditor. Kopieer de volgende tekst en sla deze lokaal op als *input. txt*.

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. Open Azure Storage Explorer. Breid uw opslag account uit. Klik met de rechtermuisknop op **Blobcontainers** en selecteer **Blobcontainer maken**.
1. Geef de nieuwe container *adfv2branch* een naam en selecteer **uploaden** om het bestand *input. txt* toe te voegen aan de container.

## Visual Studio-project maken<a name="create-visual-studio-project"></a>

Een C# .net-console toepassing maken:

1. Start Visual Studio en selecteer **een nieuw project maken**.
1. Kies in **een nieuw project maken de**optie **console-app (.NET Framework)** voor C# en selecteer **volgende**.
1. Geef het project de naam *ADFv2BranchTutorial*.
1. Selecteer **.net-versie 4.5.2** of hoger en selecteer vervolgens **maken**.

### <a name="install-nuget-packages"></a>NuGet-pakketten installeren

1. Selecteer **Extra** > **NuGet-pakketbeheer** > **Package Manager Console**.
1. Voer in **Package Manager Console** de volgende opdrachten uit om pakketten te installeren. Raadpleeg het [pakket micro soft. Azure. Management. DataFactory nuget](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/) voor meer informatie.

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>Een data factory-client maken

1. Open *Program.cs* en voeg de volgende-instructies toe:

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

1. Voeg deze statische variabelen toe aan de klasse `Program`. Vervang de plaatsaanduidingen door uw eigen waarden.

   ```csharp
   // Set variables
   static string tenantID = "<tenant ID>";
   static string applicationId = "<application ID>";
   static string authenticationKey = "<Authentication key for your application>";
   static string subscriptionId = "<Azure subscription ID>";
   static string resourceGroup = "<Azure resource group name>";

   static string region = "East US";
   static string dataFactoryName = "<Data factory name>";

   // Specify the source Azure Blob information
   static string storageAccount = "<Azure Storage account name>";
   static string storageKey = "<Azure Storage account key>";
   // confirm that you have the input.txt file placed in th input folder of the adfv2branch container. 
   static string inputBlobPath = "adfv2branch/input";
   static string inputBlobName = "input.txt";
   static string outputBlobPath = "adfv2branch/output";
   static string emailReceiver = "<specify email address of the receiver>";

   static string storageLinkedServiceName = "AzureStorageLinkedService";
   static string blobSourceDatasetName = "SourceStorageDataset";
   static string blobSinkDatasetName = "SinkStorageDataset";
   static string pipelineName = "Adfv2TutorialBranchCopy";

   static string copyBlobActivity = "CopyBlobtoBlob";
   static string sendFailEmailActivity = "SendFailEmailActivity";
   static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
   ```

1. Voeg de volgende code toe aan de methode `Main`. Deze code maakt een instantie van `DataFactoryManagementClient` klasse. Vervolgens gebruikt u dit object om data factory, een gekoppelde service, gegevens sets en een pijp lijn te maken. U kunt dit object ook gebruiken om de details van de pijplijn uitvoering te controleren.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Voeg een `CreateOrUpdateDataFactory` methode toe aan uw *Program.cs* -bestand:

   ```csharp
   static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating data factory " + dataFactoryName + "...");
       Factory resource = new Factory
       {
           Location = region
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

       Factory response;
       {
           response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
       }

       while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
       {
           System.Threading.Thread.Sleep(1000);
       }
       return response;
   }
   ```

1. Voeg de volgende regel toe aan de `Main` methode waarmee een data factory wordt gemaakt:

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service maken

1. Voeg een `StorageLinkedServiceDefinition` methode toe aan uw *Program.cs* -bestand:

   ```csharp
   static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
   {
      Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
      AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
      {
          ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
      };
      Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
      LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
      return linkedService;
   }
   ```

1. Voeg de volgende regel toe aan de `Main` methode waarmee een Azure Storage gekoppelde service wordt gemaakt:

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

Zie [Eigenschappen van gekoppelde service](connector-azure-blob-storage.md#linked-service-properties)voor meer informatie over ondersteunde eigenschappen en Details.

## <a name="create-datasets"></a>Gegevenssets maken

In deze sectie maakt u twee gegevens sets: een voor de bron en een voor de sink.

### <a name="create-a-dataset-for-a-source-azure-blob"></a>Een gegevensset maken voor een Azure-bron-BLOB

Voeg een methode toe waarmee een *Azure Blob-gegevensset*wordt gemaakt. Zie [Eigenschappen van Azure Blob-gegevensset](connector-azure-blob-storage.md#dataset-properties)voor meer informatie over ondersteunde eigenschappen en Details.

Voeg een `SourceBlobDatasetDefinition` methode toe aan uw *Program.cs* -bestand:

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    { 
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

U definieert een gegevensset die de brongegevens in Azure Blob vertegenwoordigt. Deze BLOB-gegevensset verwijst naar de Azure Storage gekoppelde service die in de vorige stap wordt ondersteund. De BLOB-gegevensset beschrijft de locatie van de BLOB waaruit moet worden gekopieerd: *FolderPath* en *filename*.

Let op het gebruik van para meters voor de *FolderPath*. `sourceBlobContainer` is de naam van de para meter en de expressie wordt vervangen door de waarden die zijn door gegeven in de pijplijn uitvoering. De syntaxis voor het definiëren van parameters is`@pipeline().parameters.<parameterName>`

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>Een gegevensset maken voor een Sink Azure-Blob

1. Voeg een `SourceBlobDatasetDefinition` methode toe aan uw *Program.cs* -bestand:

   ```csharp
   static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
       AzureBlobDataset blobDataset = new AzureBlobDataset
       {
           FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
           LinkedServiceName = new LinkedServiceReference
           {
               ReferenceName = storageLinkedServiceName
           }
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
       DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
       return dataset;
   }
   ```

1. Voeg de volgende code toe aan de `Main` methode waarmee zowel de bron-als Sink-gegevens sets van Azure Blob worden gemaakt.

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>Maak een C#-klasse: EmailRequest

Maak in C# uw project een klasse met de naam `EmailRequest`. Deze klasse definieert welke eigenschappen de pijp lijn in de hoofd aanvraag verzendt wanneer een e-mail wordt verzonden. In deze zelfstudie verzendt de pijplijn vier eigenschappen van de pijplijn naar het e-mailbericht:

* Bericht. De hoofd tekst van het e-mail bericht. Voor een geslaagde kopie bevat deze eigenschap de hoeveelheid gegevens die is geschreven. Voor een mislukte Kopieer bewerking bevat deze eigenschap Details van de fout.
* Naam van Data Factory. De naam van de data factory.
* Naam van de pijp lijn. Naam van de pijplijn.
* Ontvangst. Para meter die wordt door gegeven. Met deze eigenschap wordt de ontvanger van het e-mailbericht opgegeven.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```

## <a name="create-email-workflow-endpoints"></a>Eindpunten voor de e-mailwerkstroom maken

Voor het activeren van het verzenden van een e-mail gebruikt u [Logic Apps](../logic-apps/logic-apps-overview.md) om de werkstroom te definiëren. Zie [een logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md)voor meer informatie over het maken van een Logic apps werk stroom.

### <a name="success-email-workflow"></a>Werkstroom voor e-mail met succesbericht

Maak in de [Azure Portal](https://portal.azure.com)een Logic apps werk stroom met de naam *CopySuccessEmail*. Definieer de werk stroom trigger als `When an HTTP request is received`. Als aanvraagtrigger vult u het `Request Body JSON Schema` met de volgende JSON:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

Uw werk stroom ziet er ongeveer als volgt uit:

![Werkstroom voor e-mail met succesbericht](media/tutorial-control-flow/success-email-workflow-trigger.png)

Deze JSON-inhoud wordt uitgelijnd met de `EmailRequest`-klasse die u in de vorige sectie hebt gemaakt.

Een actie van `Office 365 Outlook – Send an email`toevoegen. Voor de actie **een E-mail verzenden** kunt u aanpassen hoe u het e-mail bericht wilt opmaken, met behulp van de eigenschappen die worden door gegeven in het JSON-schema van de aanvraag **tekst** . Hier volgt een voorbeeld:

![Logic app Designer-actie e-mail verzenden](media/tutorial-control-flow/customize-send-email-action.png)

Nadat u de werk stroom hebt opgeslagen, kopieert u de waarde van de **http post-URL** uit de trigger en slaat u deze op.

## <a name="fail-email-workflow"></a>Werkstroom voor e-mail met foutbericht

Kloon **CopySuccessEmail** als een andere Logic apps werk stroom met de naam *CopyFailEmail*. In de aanvraagtrigger is het `Request Body JSON schema` hetzelfde. Wijzig de indeling van uw e-mailbericht net als het `Subject` om er een e-mail met de foutmelding van te maken. Hier volgt een voorbeeld:

![Logic app Designer-werk stroom voor mislukte e-mail](media/tutorial-control-flow/fail-email-workflow.png)

Nadat u de werk stroom hebt opgeslagen, kopieert u de waarde van de **http post-URL** uit de trigger en slaat u deze op.

U moet nu twee werk stroom-Url's hebben, zoals in de volgende voor beelden:

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>Een pijplijn maken

Ga terug naar uw project in Visual Studio. We voegen nu de code toe die een pijp lijn maakt met een Kopieer activiteit en `DependsOn` eigenschap. In deze zelf studie bevat de pijp lijn één activiteit, een Kopieer activiteit, die in de BLOB-gegevensset als bron wordt gebruikt en een andere blob-gegevensset als sink. Als de Kopieer activiteit slaagt of mislukt, worden verschillende e-mail taken aangeroepen.

In deze pijpelijn gebruikt u de volgende functies:

* Parameters
* Webactiviteit
* Afhankelijkheid van activiteiten
* Uitvoer van een activiteit gebruiken als invoer voor een andere activiteit

1. Voeg deze methode toe aan uw project. In de volgende secties vindt u meer informatie.

    ```csharp
    static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
            {
                Console.WriteLine("Creating pipeline " + pipelineName + "...");
                PipelineResource resource = new PipelineResource
                {
                    Parameters = new Dictionary<string, ParameterSpecification>
                    {
                        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                    },
                    Activities = new List<Activity>
                    {
                        new CopyActivity
                        {
                            Name = copyBlobActivity,
                            Inputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSourceDatasetName
                                }
                            },
                            Outputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSinkDatasetName
                                }
                            },
                            Source = new BlobSource { },
                            Sink = new BlobSink { }
                        },
                        new WebActivity
                        {
                            Name = sendSuccessEmailActivity,
                            Method = WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Succeeded" }
                                }
                            }
                        },
                        new WebActivity
                        {
                            Name = sendFailEmailActivity,
                            Method =WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Failed" }
                                }
                            }
                        }
                    }
                };
                Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
                return resource;
            }
    ```

1. Voeg de volgende regel toe aan de `Main` methode waarmee de pijp lijn wordt gemaakt:

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>Parameters

In de eerste sectie van de pijplijn code worden para meters gedefinieerd.

* `sourceBlobContainer`. De bron-BLOB-gegevensset gebruikt deze para meter in de pijp lijn.
* `sinkBlobContainer`. De Sink-BLOB-gegevensset gebruikt deze para meter in de pijp lijn.
* `receiver`. De twee webactiviteiten in de pijp lijn die e-mail berichten met een geslaagde of mislukte verzen ding naar de ontvanger verzenden, gebruiken deze para meter.

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Webactiviteit

Met de webactiviteit kan een aanroep naar een wille keurig REST-eind punt worden aangeroepen. Zie [webactiviteit in azure Data Factory](control-flow-web-activity.md)voor meer informatie over de activiteit. Deze pijp lijn gebruikt een webactiviteit om de werk stroom van Logic Apps e-mail aan te roepen. U maakt twee webactiviteiten: een die de `CopySuccessEmail`-werk stroom aanroept en een die de `CopyFailWorkFlow`aanroept.

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```

Plak in de eigenschap `Url` de eind punten van de **http-post-URL** uit uw Logic apps werk stromen. Geef in de eigenschap `Body` een exemplaar van de klasse `EmailRequest` op. De e-mailaanvraag bevat de volgende eigenschappen:

* Bericht. Hiermee wordt de waarde van `@{activity('CopyBlobtoBlob').output.dataWritten`door gegeven. Hiermee wordt een eigenschap van de vorige Kopieer activiteit geopend en wordt de waarde van `dataWritten`door gegeven. In het geval waarin het kopiëren mislukt, wordt de uitvoer van de fout doorgegeven in plaats van `@{activity('CopyBlobtoBlob').error.message`.
* Data Factory naam. Hiermee wordt de waarde van `@{pipeline().DataFactory}` door gegeven aan deze systeem variabele kunt u toegang krijgen tot de bijbehorende data factory naam. Zie [systeem variabelen](control-flow-system-variables.md)voor een lijst met systeem variabelen.
* Naam van de pijp lijn. Hiermee wordt de waarde van `@{pipeline().Pipeline}`door gegeven. Met deze systeem variabele kunt u toegang krijgen tot de bijbehorende naam van de pijp lijn.
* Ontvangst. Hiermee wordt de waarde van `"@pipeline().parameters.receiver"`door gegeven. Toegang tot de pijplijn parameters.

Met deze code wordt een nieuwe activiteit afhankelijkheid gemaakt die afhankelijk is van de vorige Kopieer activiteit.

## <a name="create-a-pipeline-run"></a>Een pijplijnuitvoering maken

Voeg de volgende code toe aan de `Main` methode waarmee een pijplijn uitvoering wordt geactiveerd.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};
 
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Main-klasse

De laatste `Main` methode moet er als volgt uitzien.

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

Bouw het programma en voer het uit om een pijplijnuitvoering te activeren.

## <a name="monitor-a-pipeline-run"></a>Een pijplijnuitvoering controleren

1. Voeg de volgende code toe aan de methode `Main`:

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

    Met deze code wordt doorlopend de status van de uitvoering gecontroleerd totdat deze klaar is met het kopiëren van de gegevens.

1. Voeg de volgende code toe aan de `Main` methode waarmee details van de uitvoer van de Kopieer activiteit worden opgehaald, bijvoorbeeld de grootte van de gegevens die zijn gelezen/geschreven:

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
 
    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>De code uitvoeren

Bouw en start de toepassing en controleer vervolgens de uitvoering van de pijplijn.

De toepassing geeft de voortgang weer van het maken van data factory, gekoppelde service, gegevens sets, pijp lijn en pijplijn uitvoering. Vervolgens wordt de uitvoeringsstatus van de pijplijn gecontroleerd. Wacht totdat u details ziet van de uitvoering van de kopieeractiviteit, waaronder de omvang van de gelezen/weggeschreven gegevens. Gebruik vervolgens hulpprogram ma's als Azure Storage Explorer om te controleren of de blob is gekopieerd naar *inputblobpath* van *outputblobpath* , zoals u hebt opgegeven in variabelen.

De uitvoer moet er ongeveer uitzien als in het volgende voor beeld:

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": {
      "type": "SecureString",
      "value": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
    }
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
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
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u de volgende taken doorstaan:

> [!div class="checklist"]
> * Een gegevensfactory maken
> * Een gekoppelde Azure Storage-service maken
> * Een Azure Blob-gegevensset maken
> * Een pijplijn met een kopieeractiviteit en een webactiviteit maken
> * Verzenden van uitvoer van activiteiten naar latere activiteiten
> * Para meters en systeem variabelen gebruiken
> * Een pijplijnuitvoering starten
> * De uitvoering van de pijplijn en van de activiteit controleren

U kunt nu door gaan naar de sectie concepten voor meer informatie over Azure Data Factory.
> [!div class="nextstepaction"]
>[Pijplijnen en activiteiten](concepts-pipelines-activities.md)