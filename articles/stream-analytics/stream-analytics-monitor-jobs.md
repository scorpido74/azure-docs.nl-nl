---
title: Controleren en beheren van Azure Stream Analytics-taken via een programma
description: In dit artikel wordt beschreven hoe u programmatisch bewaken Stream Analytics-taken die zijn gemaakt via de REST-API's, Azure-SDK of PowerShell.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 23c0cc0d0e4a007fdf46021f857b559266f6a193
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431673"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Een Stream Analytics-taak monitor via een programma maken

In dit artikel ziet u hoe u bewaking voor een Stream Analytics-taak inschakelen. Stream Analytics-taken die zijn gemaakt via een REST-API's, Azure-SDK of PowerShell geen bewaking standaard ingeschakeld. U kunt deze functie handmatig inschakelen in Azure portal door te gaan naar de pagina van de Monitor van de taak en te klikken op de knop inschakelen of u kunt dit proces automatiseren door de stappen in dit artikel te volgen. De bewakingsgegevens worden weergegeven in het gebied van de metrische gegevens van de Azure-portal voor uw Stream Analytics-taak.

## <a name="prerequisites"></a>Vereisten

Voordat u met dit proces begint, moet u beschikken over de volgende vereisten:

* Visual Studio 2019 of 2015
* [Azure SDK voor .NET](https://azure.microsoft.com/downloads/) gedownload en geïnstalleerd
* Een bestaande Stream Analytics-taak die moet worden ingeschakeld controleren

## <a name="create-a-project"></a>Een project maken

1. Maak een Visual Studio C# .NET-consoletoepassing.
2. Voer de volgende opdrachten om de NuGet-pakketten te installeren in de Package Manager-Console. De eerste is de Azure Stream Analytics Management .NET SDK. De tweede waarde is de SDK van Azure Monitor die wordt gebruikt voor het inschakelen van bewaking. Het laatste item is de Azure Active Directory-client die wordt gebruikt voor verificatie.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Voeg de volgende sectie appSettings aan het bestand App.config.
   
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
   Vervang de waarden in *SubscriptionId* en *ActiveDirectoryTenantId* met uw Azure-abonnement en tenant-id. U kunt deze waarden kunt krijgen door het uitvoeren van de volgende PowerShell-cmdlet:
   
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
5. Toevoegen van een Help-methode voor verificatie.

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

## <a name="create-management-clients"></a>Maken van beheer van clients

De volgende code stelt de benodigde variabelen en beheer van clients.

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

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Schakel bewaking voor een bestaande Stream Analytics-taak

De volgende code wordt de bewaking voor een **bestaande** Stream Analytics-taak. Het eerste deel van de code wordt een GET-aanvraag indient voor de Stream Analytics-service informatie ophalen over de specifieke Stream Analytics-taak uitgevoerd. De eigenschap *id* (opgehaald van de GET-aanvraag) wordt gebruikt als een para meter voor de put-methode in de tweede helft van de code, waarmee een put-aanvraag wordt verzonden naar de Insights-service om controle voor de stream Analytics-taak in te scha kelen.

> [!WARNING]
> Als u eerder hebt ingeschakeld bewaking voor een andere taak van Stream Analytics, via Azure portal of programmatisch via de onderstaande code, **wordt aangeraden dat u dezelfde naam van het opslagaccount dat u hebt gebruikt tijdens leveren u eerder hebt ingeschakeld voor bewaking.**
> 
> Het opslagaccount dat is gekoppeld aan de regio die u hebt gemaakt met uw Stream Analytics-taak in, niet specifiek voor de taak zelf.
> 
> Alle Stream Analytics-taken (en alle andere Azure-resources) in die dezelfde regio delen dit opslagaccount wordt gebruikt voor het opslaan van gegevens. Als u een ander opslagaccount opgeeft, kan dit ertoe leiden dat onbedoelde neveneffecten in de bewaking van uw Stream Analytics-taken of andere Azure-resources.
> 
> Naam van het opslagaccount dat u gebruiken om te vervangen `<YOUR STORAGE ACCOUNT NAME>` in de volgende code moet een opslagaccount die zich in hetzelfde abonnement als de Stream Analytics-taak die u inschakelen wilt voor bewaking.
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


## <a name="get-support"></a>Krijg ondersteuning

Voor verdere ondersteuning kunt u proberen onze [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)
