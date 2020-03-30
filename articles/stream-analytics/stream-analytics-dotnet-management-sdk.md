---
title: Beheer .NET SDK voor Azure Stream Analytics
description: Ga aan de slag met Stream Analytics Management .NET SDK. Meer informatie over het instellen en uitvoeren van analysetaken. Maak een project, ingangen, uitvoer en transformaties.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 20be2c56635faa4f77ae8e8e6afc3c1ece6d4942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426260"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Beheer .NET SDK: Analytics-taken instellen en uitvoeren met behulp van de Azure Stream Analytics API voor .NET
Meer informatie over het instellen en uitvoeren van analysetaken met behulp van de Stream Analytics API voor .NET met behulp van de Management .NET SDK. Stel een project in, maak invoer- en uitvoerbronnen, transformaties en start en stop taken. Voor uw analysetaken u gegevens streamen vanuit Blob-opslag of vanuit een gebeurtenishub.

Zie de [beheerreferentiedocumentatie voor de Stream Analytics API voor .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Azure Stream Analytics is een volledig beheerde service die lage latentie, zeer beschikbare, schaalbare, complexe gebeurtenisverwerking via streaminggegevens in de cloud biedt. Stream Analytics stelt klanten in staat om streaming taken in te stellen om gegevensstromen te analyseren, en stelt hen in staat om bijna realtime analyses te genereren.  

> [!NOTE]
> We hebben de voorbeeldcode in dit artikel bijgewerkt met Azure Stream Analytics Management .NET SDK v2.x-versie. Zie [Beheer .NET SDK v1.x gebruiken voor](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk-v1)voorbeeldcode met de SDK-versie voor gebruik (1.x).

## <a name="prerequisites"></a>Vereisten
Voordat u met dit artikel begint, moet u de volgende vereisten hebben:

* Installeer Visual Studio 2019 of 2015.
* Azure [.NET SDK](https://azure.microsoft.com/downloads/)downloaden en installeren .
* Maak een Azure Resource Group in uw abonnement. Het volgende voorbeeld is een voorbeeld van Azure PowerShell-script. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview)voor Azure PowerShell-informatie ;  

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

* Stel een invoerbron- en uitvoerdoel in voor de taak waarmee u verbinding maken.

## <a name="set-up-a-project"></a>Een project instellen
Als u een analysetaak wilt maken, gebruikt u de Stream Analytics API voor .NET, die eerst uw project instelt.

1. Maak een Visual Studio C# .NET-consoletoepassing.
2. Voer in de Package Manager-console de volgende opdrachten uit om de NuGet-pakketten te installeren. De eerste is het Azure Stream Analytics Management .NET SDK. De tweede is voor Azure-clientverificatie.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Voeg de volgende sectie **appInstellingen** toe aan het bestand App.config:
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    Vervang waarden voor **SubscriptionId** en **ActiveDirectoryTenantId** door uw Azure-abonnement en tenant-id's. U deze waarden krijgen door de volgende Azure PowerShell-cmdlet uit te voeren:

   ```powershell
      Get-AzureAccount
   ```

4. Voeg de volgende verwijzing toe in het .csproj-bestand:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Voeg de volgende **instructies** toe aan het bronbestand (Program.cs) in het project:
   
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

6. Een verificatiehelpermethode toevoegen:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Een Stream Analytics-beheerclient maken
Met **een Object StreamAnalyticsManagementClient** u de taak en de taakonderdelen beheren, zoals invoer, uitvoer en transformatie.

Voeg de volgende code toe aan het begin van de **hoofdmethode:**

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

De waarde van de variabele **resourceGroupName** moet dezelfde zijn als de naam van de resourcegroep die u hebt gemaakt of gekozen in de vereiste stappen.

Als u het presentatieaspect van het maken van taken wilt automatiseren, raadpleegt u [Het verifiëren van een serviceprincipal met Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

In de overige delen van dit artikel wordt ervan uitgegaan dat deze code zich aan het begin van de **hoofdmethode bevindt.**

## <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken
Met de volgende code wordt een Stream Analytics-taak uitgevoerd onder de resourcegroep die u hebt gedefinieerd. U voegt later een invoer, uitvoer en transformatie toe aan de taak.

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

## <a name="create-a-stream-analytics-input-source"></a>Een inputbron van Stream Analytics maken
Met de volgende code wordt een Inputbron van Stream Analytics met het type blob-invoerbron en CSV-serialisatie. Als u een bron voor gebeurtenishub-invoer wilt maken, gebruikt u **EventHubStreamInputDataSource** in plaats van **BlobStreamInputDataSource**. Op dezelfde manier u het serialisatietype van de invoerbron aanpassen.

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

Invoerbronnen, of het nu afkomstig is van Blob-opslag of een gebeurtenishub, zijn gekoppeld aan een specifieke taak. Als u dezelfde invoerbron wilt gebruiken voor verschillende taken, moet u de methode opnieuw aanroepen en een andere taaknaam opgeven.

## <a name="test-a-stream-analytics-input-source"></a>Een Inputbron van Stream Analytics testen
De **TestConnection-methode** test of de taak Stream Analytics verbinding kan maken met de invoerbron en andere aspecten die specifiek zijn voor het invoerbrontype. In de blob-invoerbron die u in een eerdere stap hebt gemaakt, controleert de methode bijvoorbeeld of de naam van het opslagaccount en het sleutelpaar kunnen worden gebruikt om verbinding te maken met het opslagaccount en of de opgegeven container bestaat.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Een uitvoerdoel voor Stream Analytics maken
Het maken van een uitvoerdoel is vergelijkbaar met het maken van een Stream Analytics-invoerbron. Net als invoerbronnen zijn uitvoerdoelen gekoppeld aan een specifieke taak. Als u hetzelfde uitvoerdoel wilt gebruiken voor verschillende taken, moet u de methode opnieuw aanroepen en een andere taaknaam opgeven.

Met de volgende code wordt een uitvoerdoel (Azure SQL-database) getarget. U het gegevenstype en/of serialisatietype van het uitvoerdoel aanpassen.

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

## <a name="test-a-stream-analytics-output-target"></a>Een uitvoerdoel voor Stream Analytics testen
Een Uitvoerdoel voor Stream Analytics heeft ook de **TestConnection-methode** voor het testen van verbindingen.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Een Stream Analytics-transformatie maken
Met de volgende code wordt een Stream Analytics-transformatie gemaakt met de query 'selecteer * uit invoer' en wordt opgegeven dat er één streaming-eenheid wordt toegewezen voor de taak Stream Analytics. Zie [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)voor meer informatie over het aanpassen van streamingeenheden.

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Net als input en output is een transformatie ook gekoppeld aan de specifieke Stream Analytics-taak die is gemaakt onder.

## <a name="start-a-stream-analytics-job"></a>Een Stream Analytics-taak starten
Nadat u een Stream Analytics-taak hebt gemaakt en de invoer(en), uitvoer(s) en transformatie ervan, u de taak starten door de **methode Start** aan te roepen.

Met de volgende voorbeeldcode wordt een Stream Analytics-taak gestart met een aangepaste begintijd van de uitvoer ingesteld op 12 december 2012, 12:12:12 UTC:

   ```csharp
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Een streamanalytics-taak stoppen
U een lopende Stream Analytics-taak stoppen door de **methode Stoppen** aan te roepen.

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Een functie Stream Analytics verwijderen
Met de methode **Verwijderen** worden de taak en de onderliggende subresources, waaronder invoer(en), uitvoer(en) en transformatie van de taak, verwijderd.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Ondersteuning krijgen
Probeer ons Azure [Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)voor meer hulp.

## <a name="next-steps"></a>Volgende stappen
Je hebt de basisgeleerd van het gebruik van een .NET SDK om analytics-taken te maken en uit te voeren. Zie voor meer informatie de volgende artikelen:

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
