---
title: Vertakking in Azure Data Factory-pijplijn
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
ms.openlocfilehash: 7ba921656d0dad059b1d15f443bcefeff03ade50
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75977389"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Activiteiten vertakken en koppelen in een Data Factory-pijplijn

In deze zelfstudie maakt u een Data Factory-pijplijn met enkele functies voor controlestromen. Deze pijplijn kopieert van een container in Azure Blob Storage naar een andere container in hetzelfde opslagaccount. Als de kopieeractiviteit slaagt, stuurt de pijplijn details van de geslaagde kopieerbewerking in een e-mail. Deze informatie kan de hoeveelheid geschreven gegevens omvatten. Als de kopieeractiviteit mislukt, worden details van de kopiefout, zoals het foutbericht, in een e-mail verzonden. In de zelfstudie ziet u hoe u parameters kunt doorgeven.

Deze afbeelding geeft een overzicht van het scenario:

![Overzicht](media/tutorial-control-flow/overview.png)

In deze zelfstudie ziet u hoe u de volgende taken uitvoert:

> [!div class="checklist"]
> * Een gegevensfactory maken
> * Een gekoppelde Azure Storage-service maken
> * Een Azure Blob-gegevensset maken
> * Een pijplijn met een kopieeractiviteit en een webactiviteit maken
> * Verzenden van uitvoer van activiteiten naar latere activiteiten
> * Parameterpasses en systeemvariabelen gebruiken
> * Een pijplijnuitvoering starten
> * De uitvoering van de pijplijn en van de activiteit controleren

In deze zelfstudie wordt .NET SDK gebruikt. U andere mechanismen gebruiken om te communiceren met Azure Data Factory. Zie [Quickstarts van 5 minuten](/azure/data-factory/quickstart-create-data-factory-portal)voor snel starten in Gegevensfabriek.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Azure Storage-account. U gebruikt blob-opslag als brongegevensarchief. Zie [Een opslagaccount maken](../storage/common/storage-account-create.md)als u geen Azure-opslagaccount hebt.
* Azure Storage Explorer. Zie Azure Storage [Explorer](https://storageexplorer.com/)als u dit hulpprogramma wilt installeren.
* Azure SQL Database. U gebruikt de database als sink-gegevensopslag. Zie [Een Azure SQL-database maken](../sql-database/sql-database-get-started-portal.md)als u geen Azure SQL-database hebt.
* Visual Studio. Dit artikel maakt gebruik van Visual Studio 2019.
* Azure .NET SDK. Download en installeer de [Azure .NET SDK](https://azure.microsoft.com/downloads/).

Zie [Producten die beschikbaar](https://azure.microsoft.com/global-infrastructure/services/)zijn per regio voor een lijst met Azure-regio's waarin momenteel Gegevensfabriek beschikbaar is. De gegevens opslaan en computes kunnen worden in andere regio's. De winkels omvatten Azure Storage en Azure SQL Database. De computes omvatten HDInsight, die Data Factory gebruikt.

Maak een toepassing zoals beschreven in [Een Azure Active Directory-toepassing maken](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Wijs de toepassing toe aan de rol **Inzender** door instructies in hetzelfde artikel te volgen. U hebt verschillende waarden nodig voor latere delen van deze zelfstudie, zoals **toepassings-id (client) en** **directory-id (tenant).**

### <a name="create-a-blob-table"></a>Een blobtabel maken

1. Open een teksteditor. Kopieer de volgende tekst en sla deze lokaal op als *input.txt*.

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. Open Azure Storage Explorer. Vouw uw opslagaccount uit. Klik met de rechtermuisknop op **Blob-containers** en selecteer **Blobcontainer maken**.
1. Geef de nieuwe container *adfv2branch een naam* en selecteer **Uploaden** om uw *input.txt-bestand* aan de container toe te voegen.

## <a name="create-visual-studio-project"></a>Een Visual Studio-project maken<a name="create-visual-studio-project"></a>

Een C#.NET-consoletoepassing maken:

1. Start Visual Studio en selecteer **Een nieuw project maken**.
1. Kies in **Een nieuw project maken**de optie Console App **(.NET Framework)** voor C# en selecteer **Volgende**.
1. Geef het project *ADFv2BranchTutorial een naam*.
1. Selecteer **.NET-versie 4.5.2** of hoger en selecteer **Vervolgens Maken**.

### <a name="install-nuget-packages"></a>NuGet-pakketten installeren

1. Selecteer **Tools** > **NuGet Package Manager** > **Package Manager Console**.
1. Voer in **Package Manager Console** de volgende opdrachten uit om pakketten te installeren. Raadpleeg [Microsoft.Azure.Management.DataFactory nuget-pakket](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/) voor meer informatie.

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>Een data factory-client maken

1. Open *Program.cs* en voeg de volgende instructies toe:

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

1. Voeg deze statische variabelen `Program` toe aan de klasse. Vervang de plaatsaanduidingen door uw eigen waarden.

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

1. Voeg de volgende code toe aan de methode `Main`. Met deze code `DataFactoryManagementClient` wordt een instantie van klasse aanmaken. Vervolgens gebruikt u dit object om gegevensfabriek, gekoppelde service, gegevenssets en pijplijn te maken. U dit object ook gebruiken om de details van de pijplijnrun te controleren.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Een `CreateOrUpdateDataFactory` methode toevoegen aan uw *Program.cs* bestand:

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

1. Voeg de volgende `Main` regel toe aan de methode waarmee een gegevensfabriek wordt gemaakt:

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service maken

1. Een `StorageLinkedServiceDefinition` methode toevoegen aan uw *Program.cs* bestand:

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

1. Voeg de volgende `Main` regel toe aan de methode waarmee een gekoppelde Azure Storage-service wordt gemaakt:

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

Zie [Gekoppelde serviceeigenschappen voor](connector-azure-blob-storage.md#linked-service-properties)meer informatie over ondersteunde eigenschappen en details.

## <a name="create-datasets"></a>Gegevenssets maken

In deze sectie maakt u twee gegevenssets, een voor de bron en een voor de gootsteen.

### <a name="create-a-dataset-for-a-source-azure-blob"></a>Een gegevensset maken voor een bron Azure Blob

Voeg een methode toe waarmee een *Azure blob-gegevensset wordt gemaakt.* Zie [Azure Blob-gegevensseteigenschappen](connector-azure-blob-storage.md#dataset-properties)voor meer informatie over ondersteunde eigenschappen en details.

Een `SourceBlobDatasetDefinition` methode toevoegen aan uw *Program.cs* bestand:

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

U definieert een gegevensset die de brongegevens in Azure Blob vertegenwoordigt. Deze Blob-gegevensset verwijst naar de azure storage-gekoppelde service die in de vorige stap wordt ondersteund. De blob-gegevensset beschrijft de locatie van de blob om te kopiëren van: *FolderPath* en *FileName*.

Let op het gebruik van parameters voor het *FolderPath*. `sourceBlobContainer`is de naam van de parameter en de expressie wordt vervangen door de waarden die in de pijplijnrun worden doorgegeven. De syntaxis voor het definiëren van parameters is`@pipeline().parameters.<parameterName>`

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>Een gegevensset maken voor een sink Azure Blob

1. Een `SourceBlobDatasetDefinition` methode toevoegen aan uw *Program.cs* bestand:

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

1. Voeg de volgende `Main` code toe aan de methode waarmee zowel Azure Blob-bron- als sinkgegevens worden gemaakt.

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>Maak een C#-klasse: EmailRequest

Maak in uw C#-project `EmailRequest`een klasse met de naam . Deze klasse definieert welke eigenschappen de pijplijn in de hoofdaanvraag verzendt bij het verzenden van een e-mail. In deze zelfstudie verzendt de pijplijn vier eigenschappen van de pijplijn naar het e-mailbericht:

* Bericht. Lichaam van de e-mail. Voor een geslaagde kopie bevat deze eigenschap de hoeveelheid geschreven gegevens. Voor een mislukte kopie bevat deze eigenschap details van de fout.
* Data fabriek naam. Naam van de gegevensfabriek.
* Naam van de pijpleiding. Naam van de pijplijn.
* Ontvanger. Parameter die passeert. Met deze eigenschap wordt de ontvanger van het e-mailbericht opgegeven.

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

Voor het activeren van het verzenden van een e-mail gebruikt u [Logic Apps](../logic-apps/logic-apps-overview.md) om de werkstroom te definiëren. Zie [Een logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md)voor meer informatie over het maken van een logic-apps-werkstroom.

### <a name="success-email-workflow"></a>Werkstroom voor e-mail met succesbericht

Maak in de [Azure-portal](https://portal.azure.com)een workflow voor Logic Apps met de naam *CopySuccessEmail*. Definieer de werkstroomtrigger als `When an HTTP request is received`. Als aanvraagtrigger vult u het `Request Body JSON Schema` met de volgende JSON:

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

Uw werkstroom ziet er ongeveer als het volgende voorbeeld uit:

![Werkstroom voor e-mail met succesbericht](media/tutorial-control-flow/success-email-workflow-trigger.png)

Deze JSON-inhoud komt `EmailRequest` overeen met de klasse die u in de vorige sectie hebt gemaakt.

Voeg een `Office 365 Outlook – Send an email`actie toe van . Pas voor de actie **Een e-mail verzenden** aan hoe u de e-mail wilt opmaken met behulp van de eigenschappen die zijn doorgegeven in het JSON-schema van de **aanvraaginstantie.** Hier volgt een voorbeeld:

![Logic app designer - e-mailactie verzenden](media/tutorial-control-flow/customize-send-email-action.png)

Nadat u de werkstroom hebt opgeslagen, kopieert en slaat u de **URL-waarde van HTTP POST** van de trigger op.

## <a name="fail-email-workflow"></a>Werkstroom voor e-mail met foutbericht

**Clone CopySuccessEmail** als een andere Logic Apps-werkstroom met de naam *CopyFailEmail*. In de aanvraagtrigger is het `Request Body JSON schema` hetzelfde. Wijzig de indeling van uw e-mailbericht net als het `Subject` om er een e-mail met de foutmelding van te maken. Hier volgt een voorbeeld:

![Logic app designer - fail e-mail workflow](media/tutorial-control-flow/fail-email-workflow.png)

Nadat u de werkstroom hebt opgeslagen, kopieert en slaat u de **URL-waarde van HTTP POST** van de trigger op.

U moet nu twee werkstroom-URL's hebben, zoals de volgende voorbeelden:

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>Een pijplijn maken

Ga terug naar uw project in Visual Studio. We voegen nu de code toe waarmee een `DependsOn` pijplijn met een kopieeractiviteit en -eigenschap wordt verbonden. In deze zelfstudie bevat de pijplijn één activiteit, een kopieeractiviteit, die de Blob-gegevensset als bron en een andere Blob-gegevensset als een sink inneemt. Als de kopieeractiviteit slaagt of mislukt, worden verschillende e-mailtaken aanroept.

In deze pijpelijn gebruikt u de volgende functies:

* Parameters
* Activiteit Web
* Afhankelijkheid van activiteiten
* Uitvoer van een activiteit gebruiken als input voor een andere activiteit

1. Voeg deze methode toe aan uw project. De volgende secties geven meer details.

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

1. Voeg de volgende `Main` regel toe aan de methode waarmee de pijplijn wordt gemaakt:

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>Parameters

Het eerste deel van onze pijplijncode definieert parameters.

* `sourceBlobContainer`. De gegevensset bronblob verbruikt deze parameter in de pijplijn.
* `sinkBlobContainer`. De sink blob-gegevensset verbruikt deze parameter in de pijplijn.
* `receiver`. De twee webactiviteiten in de pijplijn die e-mails met succes of falen naar de ontvanger verzenden, gebruiken deze parameter.

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Activiteit Web

Met de webactiviteit kan een aanroep naar een REST-eindpunt worden aangenomen. Zie [Webactiviteit in Azure Data Factory](control-flow-web-activity.md)voor meer informatie over de activiteit. Deze pijplijn gebruikt een webactiviteit om de e-mailwerkstroom van Logic Apps aan te roepen. U maakt twee webactiviteiten: een `CopySuccessEmail` die naar de `CopyFailWorkFlow`werkstroom roept en een die de .

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

Plak `Url` in de eigenschap de **HTTP POST URL-eindpunten** uit uw Logic Apps-werkstromen. Geef `Body` in de eigenschap een `EmailRequest` instantie van de klasse door. De e-mailaanvraag bevat de volgende eigenschappen:

* Bericht. Geeft de `@{activity('CopyBlobtoBlob').output.dataWritten`waarde van . Toegang tot een eigenschap van de vorige `dataWritten`kopieeractiviteit en geeft de waarde van . In het geval waarin het kopiëren mislukt, wordt de uitvoer van de fout doorgegeven in plaats van `@{activity('CopyBlobtoBlob').error.message`.
* Naam data factory. Hiermee kunt `@{pipeline().DataFactory}` u de waarde van deze systeemvariabele doorgeven, zodat u toegang hebt tot de bijbehorende naam van de gegevensfabriek. Zie [Systeemvariabelen](control-flow-system-variables.md)voor een lijst met systeemvariabelen.
* Naam van de pijplijn. Geeft de `@{pipeline().Pipeline}`waarde van . Met deze systeemvariabele hebt u toegang tot de bijbehorende pijplijnnaam.
* Ontvanger. Geeft de `"@pipeline().parameters.receiver"`waarde van . Toegang tot de pijplijnparameters.

Met deze code wordt een nieuwe activiteitsafhankelijkheid gemaakt die afhankelijk is van de vorige kopieeractiviteit.

## <a name="create-a-pipeline-run"></a>Een pijplijnuitvoering maken

Voeg de volgende `Main` code toe aan de methode die een pijplijnrun activeert.

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

Je `Main` uiteindelijke methode moet er zo uitzien.

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

1. Voeg de volgende code aan de `Main` methode toe:

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

    Deze code controleert continu de status van de run totdat de gegevens zijn gekopieerd.

1. Voeg de volgende `Main` code toe aan de methode waarmee gegevens over het uitvoeren van kopieeractiviteiten worden opgehaald, bijvoorbeeld de grootte van de gelezen/geschreven gegevens:

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

De toepassing geeft de voortgang weer van het maken van gegevensfabriek, gekoppelde service, gegevenssets, pijplijnen en pijplijnuitvoering. Vervolgens wordt de uitvoeringsstatus van de pijplijn gecontroleerd. Wacht totdat u details ziet van de uitvoering van de kopieeractiviteit, waaronder de omvang van de gelezen/weggeschreven gegevens. Gebruik vervolgens hulpprogramma's zoals Azure Storage Explorer om te controleren of de blob is gekopieerd naar *uitvoerBlobPath* van *invoerBlobPath* zoals u hebt opgegeven in variabelen.

Uw uitvoer moet op het volgende voorbeeld lijken:

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
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

U hebt de volgende taken uitgevoerd in deze zelfstudie:

> [!div class="checklist"]
> * Een gegevensfactory maken
> * Een gekoppelde Azure Storage-service maken
> * Een Azure Blob-gegevensset maken
> * Een pijplijn met een kopieeractiviteit en een webactiviteit maken
> * Verzenden van uitvoer van activiteiten naar latere activiteiten
> * Parameterpasses en systeemvariabelen gebruiken
> * Een pijplijnuitvoering starten
> * De uitvoering van de pijplijn en van de activiteit controleren

U nu verder gaan naar de sectie Concepten voor meer informatie over Azure Data Factory.
> [!div class="nextstepaction"]
>[Pijplijnen en activiteiten](concepts-pipelines-activities.md)
