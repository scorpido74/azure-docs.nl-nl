---
title: Vertakking in Azure Data Factory-pijplijn | Microsoft Docs
description: Leer de stroom van gegevens in Azure Data Factory beheren door activiteiten te vertakken en te koppelen.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 02/20/2019
ms.openlocfilehash: 264d8e049cc7b714e00aaa77441cdc81a1e0a0c9
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140739"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Activiteiten vertakken en koppelen in een Data Factory-pijplijn

In deze zelfstudie maakt u een Data Factory-pijplijn die enkele van de stroombeheerfuncties demonstreert. Deze pijplijn voert een eenvoudige kopieerbewerking uit van een container in Azure Blob Storage naar een andere container in hetzelfde opslagaccount. Als de kopieerbewerking is geslaagd, wilt u details over de geslaagde kopieerbewerking (zoals de hoeveelheid geschreven gegevens) verzenden in een e-mail met een succesbericht. Als de kopieerbewerking is mislukt, wilt u details over de mislukte kopieerbewerking (zoals de foutmelding) verzenden in een e-mailbericht met een foutmelding. In de zelfstudie ziet u hoe u parameters kunt doorgeven.

Een overzicht van het scenario: ![Overzicht](media/tutorial-control-flow/overview.png)

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een data factory maken.
> * Een gekoppelde Azure Storage-service maken
> * Een Azure Blob-gegevensset maken
> * Een pijplijn met een kopieeractiviteit en een webactiviteit maken
> * Verzenden van uitvoer van activiteiten naar latere activiteiten
> * Parameters doorgeven en systeemvariabelen gebruiken
> * Een pijplijnuitvoering starten
> * De uitvoering van de pijplijn en van de activiteit controleren

In deze zelfstudie wordt .NET SDK gebruikt. U kunt andere methoden gebruiken voor interactie met Azure Data Factory. Kijk voor voorbeelden onder 'Quickstarts' (Snelstartgidsen) in de inhoudsopgave.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* **Een Azure Storage-account**. U gebruikt de blob-opslag als **bron**-gegevensopslag. Als u geen Azure-opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-quickstart-create-account.md) om een account te maken.
* **Azure SQL-database**. U gebruikt de database als **sink**-gegevensopslag. Als u geen Azure SQL-database hebt, raadpleegt u het artikel [Een Azure SQL-database maken](../sql-database/sql-database-get-started-portal.md) om een database te maken.
* **Visual Studio** 2013, 2015, of 2017. De procedures in dit artikel zijn gebaseerd op Visual Studio 2017.
* **Download en installeer [Azure .NET SDK](https://azure.microsoft.com/downloads/)** .
* **Maak een toepassing in Azure Active Directory** aan de hand van [deze instructie](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Noteer de volgende waarden voor gebruik in latere stappen: **toepassings-id**, **verificatiesleutel** en **tenant-id**. Wijs de toepassing toe aan de rol '**Inzender**' door de instructies in hetzelfde artikel te volgen.

### <a name="create-blob-table"></a>Blobtabel maken

1. Start Kladblok. Kopieer de volgende tekst en sla deze op schijf op in het bestand **input.txt**.

    ```
    John|Doe
    Jane|Doe
    ```

2. Gebruik hulpprogramma's zoals [Azure Opslagverkenner](https://storageexplorer.com/) om de container **adfv2branch** te maken en om het bestand **input.txt** te uploaden naar de container.

## <a name="create-visual-studio-project"></a>Een Visual Studio-project maken

Maak met behulp van Visual Studio 2015/2017 een C# .NET-consoletoepassing.

1. Start **Visual Studio**.
2. Klik op **File**, houd de muisaanwijzer op **New** en klik op **Project**. .NET versie 4.5.2 of hoger is vereist.
3. Selecteer **Visual C#**  -> **Console App (.NET Framework)** in de lijst met projecttypen aan de rechterkant.
4. Voer **ADFv2BranchTutorial** in als naam.
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

2. Voeg deze statische variabelen toe aan de **klasse Programma**. Vervang de plaatsaanduidingen door uw eigen waarden. Voor een lijst met Azure-regio's waarin Data Factory momenteel beschikbaar is, selecteert u op de volgende pagina de regio's waarin u geïnteresseerd bent, vouwt u vervolgens **Analytics** uit en gaat u naar **Data Factory**: [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/). De gegevensopslagexemplaren (Azure Storage, Azure SQL Database, enzovoort) en berekeningen (HDInsight, enzovoort) die worden gebruikt in Data Factory, kunnen zich in andere regio's bevinden.

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

3. Voeg de volgende code toe aan de methode **Main** om een instantie van de klasse **DataFactoryManagementClient** te maken. U gebruikt dit object voor het maken van een data factory, een gekoppelde service, gegevenssets en een pijplijn. U kunt dit object ook gebruiken om de details van de pijplijnuitvoering te controleren.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Data factory maken

Maak een 'CreateOrUpdateDataFactory'-functie in het bestand Program.cs:

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



Voeg de volgende code toe aan de methode **Main** om een **data factory** te maken. 

```csharp
Factory df = CreateOrUpdateDataFactory(client);
```

## <a name="create-an-azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service maken

Maak een 'StorageLinkedServiceDefinition'-functie in het bestand Program.cs:

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

Voeg de volgende code toe aan de methode **Main** om een **gekoppelde Azure Storage-service** te maken. Zie het onderwerp over [gekoppelde service-eigenschappen van Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) voor meer informatie over ondersteunde eigenschappen en details.

```csharp
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
```

## <a name="create-datasets"></a>Gegevenssets maken

In deze sectie maakt u twee gegevenssets: één voor de bron en de andere voor de sink. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Een gegevensset maken voor de brongegevens in Azure Blob

Voeg de volgende code toe aan de methode **Main** om een **Azure Blob Storage-gegevensset** te maken. Zie het onderwerp over [eigenschappen van Azure Blob Storage-gegevenssets](connector-azure-blob-storage.md#dataset-properties) voor meer informatie over ondersteunde eigenschappen en details.

U definieert een gegevensset die de brongegevens in Azure Blob vertegenwoordigt. Deze Blob-gegevensset verwijst naar de gekoppelde Azure Storage-service die u in de vorige stap hebt gemaakt en beschrijft het volgende:

- De locatie van de blob waaruit moet worden gekopieerd: **FolderPath** en **FileName**;
- Let op het gebruik van parameters voor FolderPath. 'sourceBlobContainer' is de naam van de parameter en de expressie wordt vervangen door de waarden die worden doorgegeven in de pijplijnuitvoering. De syntaxis voor het definiëren van parameters is`@pipeline().parameters.<parameterName>`

Maak een 'SourceBlobDatasetDefinition'-functie in het bestand Program.cs

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

### <a name="create-a-dataset-for-sink-azure-blob"></a>Een gegevensset maken voor de sinkgegevens in Azure Blob

Maak een 'SourceBlobDatasetDefinition'-functie in het bestand Program.cs

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

Voeg de volgende code toe aan de methode **Main** om een Azure Blob Storage-bron- en -sinkgegevensset te maken. 

```csharp
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
```

## <a name="create-a-c-class-emailrequest"></a>Een C#-klasse maken: EmailRequest

Maak in uw C#-project een klasse met de naam **EmailRequest**. Hiermee definieert u welke eigenschappen de pijplijn in de hoofdtekstaanvraag verzendt bij het verzenden van een e-mailbericht. In deze zelfstudie verzendt de pijplijn vier eigenschappen van de pijplijn naar het e-mailbericht:

- **Bericht**: hoofdtekst van het e-mailbericht. Bij een geslaagde kopieerbewerking bevat deze eigenschap gegevens over de uitvoering (aantal geschreven gegevens). In het geval van een mislukte kopieerbewerking bevat deze eigenschap details over de fout.
- **Naam data factory**: naam van de data factory
- **Naam pijplijn**: de naam van de pijplijn
- **Ontvanger**: parameter die wordt doorgegeven. Met deze eigenschap wordt de ontvanger van het e-mailbericht opgegeven.

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

Voor het activeren van het verzenden van een e-mail gebruikt u [Logic Apps](../logic-apps/logic-apps-overview.md) om de werkstroom te definiëren. Zie voor meer informatie over het maken van een Logic App-werkstroom [How to create a logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md) (Het maken van een logische app). 

### <a name="success-email-workflow"></a>Werkstroom voor e-mail met succesbericht 

Maak een Logic App-werkstroom met de naam `CopySuccessEmail`. Definieer de werkstroomtrigger als `When an HTTP request is received`, en voeg de actie `Office 365 Outlook – Send an email` toe.

![Werkstroom voor e-mail met succesbericht](media/tutorial-control-flow/success-email-workflow.png)

Als aanvraagtrigger vult u het `Request Body JSON Schema` met de volgende JSON:

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

Deze correspondeert met de **EmailRequest**-klasse die u in de vorige sectie hebt gemaakt. 

Uw aanvraag moet er in Logic App Designer zo uitzien:

![Logic App Designer - aanvraag](media/tutorial-control-flow/logic-app-designer-request.png)

Pas voor de actie **E-mail verzenden** de opmaak van de e-mail aan met behulp van de eigenschappen die in het JSON-schema voor de hoofdtekst van de aanvraag worden doorgegeven. Hier volgt een voorbeeld:

![Logic App Designer - actie e-mail verzenden](media/tutorial-control-flow/send-email-action.png)

Noteer de URL van de HTTP POST-aanvraag voor uw werkstroom voor de e-mail met het succesbericht:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="fail-email-workflow"></a>Werkstroom voor e-mail met foutbericht 

Kloon **CopySuccessEmail** en maak een andere Logic Apps-werkstroom, **CopyFailEmail**. In de aanvraagtrigger is het `Request Body JSON schema` hetzelfde. Wijzig de indeling van uw e-mailbericht net als het `Subject` om er een e-mailbericht met een foutmelding van te maken. Hier volgt een voorbeeld:

![Logic App designer - werkstroom voor e-mailbericht met foutmelding](media/tutorial-control-flow/fail-email-workflow.png)

Noteer de URL van de HTTP POST-aanvraag voor uw werkstroom voor de e-mail met het foutbericht:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

U hebt nu twee werkstroom-URL's:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>Een pijplijn maken

Voeg de volgende code toe aan de methode Main om een pijplijn met een kopieeractiviteit en de eigenschap dependsOn te maken. In deze zelfstudie bevat deze pijplijn maar één activiteit: een kopieeractiviteit waarbij de Blob-gegevensset als bron wordt gebruikt en een andere Blob-gegevensset als sink. Wanneer de kopieeractiviteit slaagt of mislukt, worden verschillende e-mailtaken aangeroepen.

In deze pijpelijn gebruikt u de volgende functies:

- Parameters
- Web Activity
- Afhankelijkheid van activiteiten
- De uitvoer van een activiteit gebruiken als invoer voor de volgende activiteit

We nemen de volgende pijplijn per sectie door:

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

Voeg de volgende code toe aan de methode **Main** om de pijplijn te maken:

```
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
```

### <a name="parameters"></a>Parameters

In de eerste sectie van onze pijplijn worden parameters gedefinieerd. 

- sourceBlobContainer - parameter in the pijplijn die door de bronblobgegevensset wordt verbruikt
- sinkBlobContainer - parameter in the pijplijn die door de sinkblobgegevensset wordt verbruikt
- ontvanger - deze parameter wordt gebruikt door de twee webactiviteiten in de pijplijn waarmee een e-mail met een succesbericht of een e-mail met een foutmelding wordt gestuurd aan de ontvanger die door deze parameter wordt gespecificeerd.


```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Web Activity

De Web Activity (webactiviteit) staat een aanroep toe naar elk REST-eindpunt. Zie voor meer informatie over de activiteit [Webactiviteit](control-flow-web-activity.md). Deze pijplijn gebruikt een webactiviteit voor het aanroepen van de Logic Apps-e-mailwerkstroom. U maakt twee webactiviteiten: een die de werkstroom **CopySuccessEmail** aanroept, en één die **CopyFailWorkFlow** aanroept.

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

Plak de eindpunten van de aanvraag-URL uit uw Logic Apps-werkstroom in de overeenkomstige eigenschap 'Url'. Geef in de eigenschap 'Body' een instantie van de klasse 'EmailRequest' door. De e-mailaanvraag bevat de volgende eigenschappen:

- Bericht: geeft de waarde van `@{activity('CopyBlobtoBlob').output.dataWritten` door. Leest een eigenschap van de vorige kopieeractiviteit en geeft de waarde van dataWritten door. In het geval waarin het kopiëren mislukt, wordt de uitvoer van de fout doorgegeven in plaats van `@{activity('CopyBlobtoBlob').error.message`.
- Naam data factory: geeft de waarde van `@{pipeline().DataFactory}` door. Dit is een systeemvariabele, zodat u toegang hebt tot de bijbehorende data factory-naam. Zie voor een lijst van systeemvariabelen het artikel [System Variables](control-flow-system-variables.md) (Systeemvariabelen).
- Naam pijplijn: geeft de waarde van `@{pipeline().Pipeline}` door. Dit is ook een systeemvariabele, zodat u toegang hebt tot de bijbehorende pijplijnnaam. 
- Ontvanger: geeft de waarde van "\@pipeline().parameters.receiver") door. Toegang tot de pijplijnparameters.
 
Deze code maakt een nieuwe activiteitafhankelijkheid, die afhankelijk is van de vorige kopieeractiviteit waarop hij volgt.

## <a name="create-a-pipeline-run"></a>Een pijplijnuitvoering maken

Voeg de volgende code toe aan de methode **Main** om een **pijplijnuitvoering te activeren**.

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

Uw laatste Main-methode moet er zo uitzien. Bouw het programma en voer het uit om een pijplijnuitvoering te activeren.

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
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>De code uitvoeren

Bouw en start de toepassing en controleer vervolgens de uitvoering van de pijplijn.
In de console wordt de voortgang weergegeven van het maken van een data factory, een gekoppelde service, gegevenssets, pijplijn en pijplijnuitvoering. Vervolgens wordt de uitvoeringsstatus van de pijplijn gecontroleerd. Wacht totdat u details ziet van de uitvoering van de kopieeractiviteit, waaronder de omvang van de gelezen/weggeschreven gegevens. Gebruik vervolgens hulpprogramma's als Azure Storage Explorer om te controleren of de blob(s) is/zijn gekopieerd van het 'inputBlobPath' naar het 'outputBlobPath' zoals u hebt opgegeven in de variabelen.

**Voorbeelduitvoer:**

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

In deze zelfstudie hebt u de volgende stappen uitgevoerd: 

> [!div class="checklist"]
> * Een data factory maken.
> * Een gekoppelde Azure Storage-service maken
> * Een Azure Blob-gegevensset maken
> * Een pijplijn met een kopieeractiviteit en een webactiviteit maken
> * Verzenden van uitvoer van activiteiten naar latere activiteiten
> * Parameters doorgeven en systeemvariabelen gebruiken
> * Een pijplijnuitvoering starten
> * De uitvoering van de pijplijn en van de activiteit controleren

U kunt nu doorgaan naar de sectie Concepten voor meer informatie over Azure Data Factory.
> [!div class="nextstepaction"]
>[Pijplijnen en activiteiten](concepts-pipelines-activities.md)