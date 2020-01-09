---
title: Management .NET-SDK voor Azure Stream Analytics
description: Aan de slag met Stream Analytics Management .NET SDK. Informatie over het instellen en analytics-taken uitvoeren. Maak een project, invoer, uitvoer en transformaties.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 20be2c56635faa4f77ae8e8e6afc3c1ece6d4942
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426260"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Management .NET SDK: Instellen en uitvoeren van analysetaken met behulp van de Azure Stream Analytics-API voor .NET
Informatie over het instellen en uitvoeren van analysetaken met behulp van de Stream Analytics-API voor .NET met behulp van de Management .NET-SDK. Instellen van een project, invoer en uitvoer bronnen, transformaties en start maken en taken stoppen. Voor de analytics-taken, kunt u gegevens uit Blob storage of van een event hub streamen.

Zie de [management referentiedocumentatie voor de Stream Analytics-API voor .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Azure Stream Analytics is een volledig beheerde service met lage latentie, maximaal beschikbare, schaalbare, complexe gebeurtenisverwerking bieden over het streamen van gegevens in de cloud. Stream Analytics kan klanten voor het instellen van streamingtaken gegevensstromen te analyseren, en kan ze om bijna realtime analyses te stimuleren.  

> [!NOTE]
> We hebben de voorbeeldcode in dit artikel bijgewerkt met Azure Stream Analytics Management .NET SDK v2.x versie. Zie voor voorbeelden van code met behulp van de gebruikt lagecy (1.x) SDK-versie kunt [de v1.x Management .NET SDK gebruiken voor Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk-v1).

## <a name="prerequisites"></a>Vereisten
Voordat u aan dit artikel begint, moet u aan de volgende vereisten voldoen:

* Installeer Visual Studio 2019 of 2015.
* Download en installeer [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Maak een Azure-resourcegroep in uw abonnement. Het volgende voor beeld is een voor beeld-Azure PowerShell script. Zie voor meer informatie voor Azure PowerShell, [installeren en configureren van Azure PowerShell](/powershell/azure/overview);  

   ```powershell
   # Log in to your Azure account
   Add-AzureAccount
   
   # Select the Azure subscription you want to use to create the resource group
   Select-AzureSubscription -SubscriptionName <subscription name>
   
   # If Stream Analytics has not been registered to the subscription, remove the remark    symbol (#) to run the Register-AzProvider cmdlet to register the provider namespace
   #Register-AzProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
   
   # Create an Azure resource group
   New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
   ```

* Instellen van een invoer bron en bestemming voor uitvoer voor de taak verbinding maken met.

## <a name="set-up-a-project"></a>Een project instellen
Als u een analyse taak wilt maken, gebruikt u de Stream Analytics-API voor .NET, moet u uw project eerst instellen.

1. Maak een Visual Studio C# .NET-consoletoepassing.
2. Voer de volgende opdrachten om de NuGet-pakketten te installeren in de Package Manager-Console. De eerste is de Azure Stream Analytics Management .NET SDK. De tweede waarde is voor verificatie van Azure.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Voeg de volgende **appSettings** sectie aan het bestand App.config:
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    Vervang de waarden in **SubscriptionId** en **ActiveDirectoryTenantId** met uw Azure-abonnement en tenant-id. U kunt deze waarden kunt krijgen door de volgende Azure PowerShell-cmdlet:

   ```powershell
      Get-AzureAccount
   ```

4. De volgende verwijzing toevoegen in uw .csproj-bestand:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Voeg de volgende **met behulp van** instructies toe aan het bronbestand (Program.cs) in het project:
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Threading;
   using System.Threading.Tasks;
   
   using Microsoft.Azure.Management.StreamAnalytics;
   using Microsoft.Azure.Management.StreamAnalytics.Models;
   using Microsoft.Rest.Azure.Authentication;
   using Microsoft.Rest;
   ```

6. Toevoegen van een Help-methode voor verificatie:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Een Stream Analytics management-client maken
Een **StreamAnalyticsManagementClient** object kunt u voor het beheren van de taak en de onderdelen van de taak, zoals invoer, uitvoer en transformatie.

Voeg de volgende code toe aan het begin van de **Main** methode:

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamingJobName = "<YOUR STREAMING JOB NAME>";
    string inputName = "<YOUR JOB INPUT NAME>";
    string transformationName = "<YOUR JOB TRANSFORMATION NAME>";
    string outputName = "<YOUR JOB OUTPUT NAME>";
    
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
    // Get credentials
    ServiceClientCredentials credentials = GetCredentials().Result;
    
    // Create Stream Analytics management client
    StreamAnalyticsManagementClient streamAnalyticsManagementClient = new StreamAnalyticsManagementClient(credentials)
    {
        SubscriptionId = ConfigurationManager.AppSettings["SubscriptionId"]
    };
   ```

De **resourceGroupName** van de variabele waarde moet gelijk zijn aan de naam van de resourcegroep die u hebt gemaakt of opgenomen in de vereiste stappen.

Voor het automatiseren van de referentie-presentatie aspect van de taak te maken, Raadpleeg [verifiëren van een service-principal met Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

De rest van dit artikel wordt ervan uitgegaan dat deze code aan het begin van de **Main** methode.

## <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken
De volgende code maakt u een Stream Analytics-taak onder de resourcegroep die u hebt gedefinieerd. Aan de job wordt u een invoer, uitvoer en transformatie later toevoegen.

   ```csharp
   // Create a streaming job
   StreamingJob streamingJob = new StreamingJob()
   {
       Tags = new Dictionary<string, string>()
       {
           { "Origin", ".NET SDK" },
           { "ReasonCreated", "Getting started tutorial" }
       },
       Location = "West US",
       EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Drop,
       EventsOutOfOrderMaxDelayInSeconds = 5,
       EventsLateArrivalMaxDelayInSeconds = 16,
       OutputErrorPolicy = OutputErrorPolicy.Drop,
       DataLocale = "en-US",
       CompatibilityLevel = CompatibilityLevel.OneFullStopZero,
       Sku = new Sku()
       {
           Name = SkuName.Standard
       }
   };
   StreamingJob createStreamingJobResult = streamAnalyticsManagementClient.StreamingJobs.CreateOrReplace(streamingJob, resourceGroupName, streamingJobName);
   ```

## <a name="create-a-stream-analytics-input-source"></a>Een Stream Analytics-invoerbron maken
De volgende code maakt een Stream Analytics-invoerbron met de blob-invoerbron type en de CSV-serialisatie. Gebruik voor het maken van een event hub-invoerbron **EventHubStreamInputDataSource** in plaats van **BlobStreamInputDataSource**. Op deze manier kunt u het serialisatietype van de invoerbron aanpassen.

   ```csharp
   // Create an input
   StorageAccount storageAccount = new StorageAccount()
   {
       AccountName = "<YOUR STORAGE ACCOUNT NAME>",
       AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
   };
   Input input = new Input()
   {
       Properties = new StreamInputProperties()
       {
           Serialization = new CsvSerialization()
           {
               FieldDelimiter = ",",
               Encoding = Encoding.UTF8
           },
           Datasource = new BlobStreamInputDataSource()
           {
               StorageAccounts = new[] { storageAccount },
               Container = "<YOUR STORAGE BLOB CONTAINER>",
               PathPattern = "{date}/{time}",
               DateFormat = "yyyy/MM/dd",
               TimeFormat = "HH",
               SourcePartitionCount = 16
           }
       }
   };
   Input createInputResult = streamAnalyticsManagementClient.Inputs.CreateOrReplace(input, resourceGroupName, streamingJobName, inputName);
   ```

Invoerbronnen, zijn hetzij van Blob-opslag of een event hub, gekoppeld aan een specifieke taak. U moet voor het gebruik van de dezelfde invoerbron voor verschillende taken, roept u de methode opnieuw en geef een andere taaknaam.

## <a name="test-a-stream-analytics-input-source"></a>Een Stream Analytics-invoerbron testen
De **TestConnection** methode wordt gecontroleerd of de Stream Analytics-taak is geen verbinding maken met de invoerbron, evenals andere aspecten die specifiek zijn voor het type invoerbron. Bijvoorbeeld, in de blob-invoerbron die u in een eerdere stap hebt gemaakt, wordt de methode gecontroleerd dat de Storage-accountnaam en het sleutelpaar kunnen worden gebruikt voor verbinding maken met de Storage-account, evenals controleren of de opgegeven container bestaat.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Maak het doel van een Stream Analytics-uitvoer
Het maken van een uitvoer doel is vergelijkbaar met het maken van een Stream Analytics invoer bron. Uitvoer doelen zijn, zoals invoerbronnen gekoppeld aan een specifieke taak. U moet voor het gebruik van de dezelfde bestemming voor uitvoer voor verschillende taken, roept u de methode opnieuw en geef een andere taaknaam.

De volgende code maakt een bestemming voor uitvoer (Azure SQL database). U kunt het gegevenstype van het uitvoerdoel en/of dit serialisatietype aanpassen.

   ```csharp
   // Create an output
   Output output = new Output()
   {
       Datasource = new AzureSqlDatabaseOutputDataSource()
       {
           Server = "<YOUR DATABASE SERVER NAME>",
           Database = "<YOUR DATABASE NAME>",
           User = "<YOUR DATABASE LOGIN>",
           Password = "<YOUR DATABASE LOGIN PASSWORD>",
           Table = "<YOUR DATABASE TABLE NAME>"
       }
   };
   Output createOutputResult = streamAnalyticsManagementClient.Outputs.CreateOrReplace(output, resourceGroupName, streamingJobName, outputName);
   ```

## <a name="test-a-stream-analytics-output-target"></a>Een Stream Analytics-bestemming voor uitvoer testen
Een Stream Analytics-bestemming voor uitvoer heeft ook de **TestConnection** methode voor het testen van verbindingen.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Maken van een Stream Analytics-transformatie
De volgende code maakt u een Stream Analytics-transformatie met de query ' Selecteer * uit invoer ' en geeft u voor het toewijzen van één streaming-eenheid voor de Stream Analytics-taak. Zie voor meer informatie over het aanpassen van streaming-eenheden [Scale Azure Stream Analytics-taken](stream-analytics-scale-jobs.md).

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Net als invoer en uitvoer, is ook een transformatie gekoppeld aan de specifieke Stream Analytics-taak die is gemaakt onder.

## <a name="start-a-stream-analytics-job"></a>Een Stream Analytics-taak starten
Na het maken van een Stream Analytics-taak en de invoerwaarde(n), output(s) en -transformatie, kunt u de taak starten door het aanroepen van de **Start** methode.

Voorbeeld van de volgende code wordt gestart een Stream Analytics-taak met een begintijd aangepaste uitvoer ingesteld op 12 December 2012 12:12:12 UTC:

   ```csharp
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Een Stream Analytics-taak stoppen
U kunt een actieve Stream Analytics-taak stoppen door het aanroepen van de **stoppen** methode.

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Een Stream Analytics-taak verwijderen
De **verwijderen** methode de taak, evenals de onderliggende onderliggende resources, met inbegrip van invoerwaarde(n) output(s) en transformatie van de taak wordt verwijderd.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Krijg ondersteuning
Voor verdere ondersteuning kunt u proberen onze [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Volgende stappen
De basisbeginselen van het gebruik van een .NET-SDK maken en uitvoeren van analysetaken, hebt u geleerd. Raadpleeg de volgende artikelen voor meer informatie:

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Management .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx).
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: https://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: https://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: https://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
