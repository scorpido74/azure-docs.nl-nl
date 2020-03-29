---
title: Azure Stream Analytics-taken programmatisch bewaken en beheren
description: In dit artikel wordt beschreven hoe u streamanalytics-taken die zijn gemaakt via REST API's, Azure SDK of PowerShell, programmatisch controleren.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 23c0cc0d0e4a007fdf46021f857b559266f6a193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431673"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Programmatisch een Functiemonitor van Stream Analytics maken

In dit artikel wordt uitgelegd hoe u monitoring inschakelt voor een Stream Analytics-taak. Stream Analytics-taken die zijn gemaakt via REST API's, Azure SDK of PowerShell, hebben geen standaardbewaking ingeschakeld. U het handmatig inschakelen in de Azure-portal door naar de monitorpagina van de taak te gaan en op de knop Inschakelen te klikken of u dit proces automatiseren door de stappen in dit artikel te volgen. De bewakingsgegevens worden weergegeven in het gebied Metriek van de Azure-portal voor uw Stream Analytics-taak.

## <a name="prerequisites"></a>Vereisten

Voordat u met dit proces begint, moet u de volgende vereisten hebben:

* Visual Studio 2019 of 2015
* [Azure .NET SDK](https://azure.microsoft.com/downloads/) gedownload en geïnstalleerd
* Een bestaande Stream Analytics-taak die monitoring moet hebben ingeschakeld

## <a name="create-a-project"></a>Een project maken

1. Maak een Visual Studio C# .NET-consoletoepassing.
2. Voer in de Package Manager-console de volgende opdrachten uit om de NuGet-pakketten te installeren. De eerste is het Azure Stream Analytics Management .NET SDK. De tweede is de Azure Monitor SDK die wordt gebruikt om monitoring in te schakelen. De laatste is de Azure Active Directory-client die wordt gebruikt voor verificatie.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Voeg de volgende sectie appInstellingen toe aan het app.config-bestand.
   
   ```csharp
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
     <add key="JobName" value="YOUR JOB NAME" />
     <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
     <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
     <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```
   Vervang waarden voor *SubscriptionId* en *ActiveDirectoryTenantId* door uw Azure-abonnement en tenant-id's. U deze waarden krijgen door de volgende PowerShell-cmdlet uit te voeren:
   
   ```powershell
   Get-AzureAccount
   ```
4. Voeg de volgende using-instructies toe aan het bronbestand (Program.cs) in het project.
   
   ```csharp
     using System;
     using System.Configuration;
     using System.Threading;
     using Microsoft.Azure;
     using Microsoft.Azure.Management.Insights;
     using Microsoft.Azure.Management.Insights.Models;
     using Microsoft.Azure.Management.StreamAnalytics;
     using Microsoft.Azure.Management.StreamAnalytics.Models;
     using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```
5. Voeg een verificatiehelpermethode toe.

   ```csharp   
   public static string GetAuthorizationHeader()
   {
      AuthenticationResult result = null;
      var thread = new Thread(() =>
      {
         try
         {
             var context = new AuthenticationContext(
                ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
             result = context.AcquireToken(
                 resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                 clientId: ConfigurationManager.AppSettings["AsaClientId"],
                 redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                 promptBehavior: PromptBehavior.Always);
         }
         catch (Exception threadEx)
         {
             Console.WriteLine(threadEx.Message);
         }
     });

     thread.SetApartmentState(ApartmentState.STA);
     thread.Name = "AcquireTokenThread";
     thread.Start();
     thread.Join();
   
     if (result != null)
     {
         return result.AccessToken;
     }
         throw new InvalidOperationException("Failed to acquire token");
   }
   ```

## <a name="create-management-clients"></a>Beheerclients maken

De volgende code zal het opzetten van de nodige variabelen en management clients.

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);
   ```

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Monitoring inschakelen voor een bestaande Stream Analytics-taak

Met de volgende code u controleren voor een **bestaande** Stream Analytics-taak. In het eerste deel van de code wordt een GET-aanvraag uitgevoerd tegen de Stream Analytics-service om informatie over de specifieke Stream Analytics-taak op te halen. Het gebruikt de eigenschap *ID* (opgehaald uit de GET-aanvraag) als parameter voor de methode Put in de tweede helft van de code, die een PUT-verzoek naar de Insights-service verzendt om monitoring voor de streamanalytics-taak mogelijk te maken.

> [!WARNING]
> Als u monitoring eerder hebt ingeschakeld voor een andere Stream Analytics-taak, via de Azure-portal of programmatisch via de onderstaande code, **raden we u aan dezelfde naam van het opslagaccount op te geven dat u hebt gebruikt toen u de bewaking eerder in- of toestond.**
> 
> Het opslagaccount is gekoppeld aan de regio waarin u uw Stream Analytics-taak hebt gemaakt, niet specifiek aan de taak zelf.
> 
> Alle Stream Analytics-taken (en alle andere Azure-bronnen) in dezelfde regio delen dit opslagaccount om bewakingsgegevens op te slaan. Als u een ander opslagaccount opgeeft, kan dit onbedoelde bijwerkingen veroorzaken bij het controleren van uw andere Stream Analytics-taken of andere Azure-bronnen.
> 
> De naam van het opslagaccount dat u in de volgende code wilt vervangen, `<YOUR STORAGE ACCOUNT NAME>` moet een opslagaccount zijn dat zich in hetzelfde abonnement bevindt als de Stream Analytics-taak waarvoor u bewaking inschakelt.
> 
> 
>    ```csharp
>    // Get an existing Stream Analytics job
>     JobGetParameters jobGetParameters = new JobGetParameters()
>     {
>         PropertiesToExpand = "inputs,transformation,outputs"
>     };
>     JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);
>
>    // Enable monitoring
>    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
>    {
>            Properties = new ServiceDiagnosticSettings()
>           {
>               StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
>           }
>    };
>   insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);
>   ```


## <a name="get-support"></a>Ondersteuning krijgen

Probeer ons Azure [Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)voor meer hulp.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)
