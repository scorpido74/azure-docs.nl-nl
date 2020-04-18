---
title: Een door het systeem toegewezen beheerde identiteit gebruiken om toegang te krijgen tot Azure Cosmos DB-gegevens
description: Meer informatie over het configureren van een Azure Active Directory (Azure AD) systeemtoegewezen beheerde identiteit (managed service identity) om toegang te krijgen tot sleutels vanuit Azure Cosmos DB.
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 8136ad7a1fe29bc3394e959c10aafc52988c0a23
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641236"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Beheerde identiteiten met het systeem gebruiken om toegang te krijgen tot Azure Cosmos DB-gegevens

In dit artikel stelt u een *robuuste, toetsrotatieagnostische* oplossing in om toegang te krijgen tot Azure Cosmos DB-sleutels met behulp van [beheerde identiteiten.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) In het voorbeeld in dit artikel wordt Azure-functies gebruikt, maar u elke service gebruiken die beheerde identiteiten ondersteunt. 

U leert hoe u een functie-app maakt die toegang heeft tot Azure Cosmos DB-gegevens zonder dat u Azure Cosmos DB-sleutels hoeft te kopiëren. De functie-app wordt elke minuut wakker en registreert de huidige temperatuur van een aquariumaquarium. Zie de functie Maken in Azure die wordt geactiveerd door een functie-app die wordt geactiveerd door een timer-artikel voor meer informatie over het instellen van een functie-app die door timers [wordt geactiveerd.](../azure-functions/functions-create-scheduled-function.md)

Om het scenario te vereenvoudigen, is een [time-to-live-instelling](./time-to-live.md) al geconfigureerd om oudere temperatuurdocumenten op te schonen. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Een door het systeem toegewezen beheerde identiteit toewijzen aan een functie-app

In deze stap wijst u een door het systeem toegewezen beheerde identiteit toe aan uw functie-app.

1. Open in de [Azure-portal](https://portal.azure.com/)het deelvenster **Azure-functie** en ga naar de functie-app. 

1. Open het tabblad**Identiteit van** **platformfuncties:** >  

   ![Schermafbeelding met platformfuncties en identiteitsopties voor de functie-app.](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. Schakel op het tabblad **Identiteit** de **status** van de systeemidentiteit **in** en selecteer **Opslaan**. Het **identiteitsvenster** moet er als volgt uitzien:  

   ![Schermafbeelding van de status van de systeemidentiteit ingesteld op Aan.](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-access-to-your-azure-cosmos-account"></a>Toegang verlenen tot uw Azure Cosmos-account

In deze stap wijst u een rol toe aan de door het systeem toegewezen beheerde identiteit van de functie-app. Azure Cosmos DB heeft meerdere ingebouwde rollen die u toewijzen aan de beheerde identiteit. Voor deze oplossing gebruikt u de volgende twee rollen:

|Ingebouwde rol  |Beschrijving  |
|---------|---------|
|[DocumentDB-accountbijdrager](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Kan Azure Cosmos DB-accounts beheren. Hiermee u lees-/schrijftoetsen ophalen. |
|[Cosmos DB-accountlezer](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Kan Azure Cosmos DB-accountgegevens lezen. Hiermee u leessleutels ophalen. |

> [!IMPORTANT]
> Ondersteuning voor op rollen gebaseerde toegangscontrole in Azure Cosmos DB is alleen van toepassing op beheervlakbewerkingen. Gegevensvlakbewerkingen worden beveiligd via hoofdsleutels of resourcetokens. Zie het artikel [Beveiligde toegang tot gegevens](secure-access-to-data.md) voor meer informatie.

> [!TIP] 
> Wanneer u rollen toewijst, wijst u alleen de benodigde toegang toe. Als uw service alleen leesgegevens vereist, wijst u de rol **Cosmos DB-accountlezer** toe aan de beheerde identiteit. Zie het artikel Lagere blootstelling van bevoegde accounts voor meer informatie over het belang van de minste toegang tot [bevoegdheden.](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)

In dit scenario leest de functie-app de temperatuur van het aquarium en schrijft deze gegevens terug naar een container in Azure Cosmos DB. Omdat de functie-app de gegevens moet schrijven, moet u de rol **documentdb-accountinzender toewijzen.** 

1. Meld u aan bij de Azure-portal en ga naar uw Azure Cosmos DB-account. Open het deelvenster **Toegangsbesturingselement (IAM)** en vervolgens het tabblad **Toewijzingen van rollen:**

   ![Schermafbeelding van het toegangsbesturingselement en het tabblad Toewijzingen van rollen.](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. Selecteren **+ Roltoewijzing** > **toevoegen**.

1. Het deelvenster **Roltoewijzing toevoegen** wordt rechts geopend:

   ![Schermafbeelding van het deelvenster Roltoewijzing toevoegen.](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **Functie**: **DocumentDB-accountbijdrager selecteren**
   * **Selecteer** **Functie-app**onder de onderafdeling Beheerde identiteit selecteren onder de onderafdeling **Beheerde identiteit selecteren** .
   * **Selecteer**: Het deelvenster wordt gevuld met alle functie-apps in uw abonnement met een **beheerde systeemidentiteit.** Selecteer in dit geval de **functie-app FishTankTemperatureService:** 

      ![Schermafbeelding van het deelvenster Roltoewijzing toevoegen dat is gevuld met voorbeelden.](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. Nadat u de functie-app hebt geselecteerd, selecteert u **Opslaan**.

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Programmatisch toegang tot de Azure Cosmos DB-toetsen

Nu hebben we een functie-app met een door het systeem toegewezen beheerde identiteit met de rol **DocumentDB-accountbijdrage in** de Azure Cosmos DB-machtigingen. De volgende functie-app-code krijgt de Azure Cosmos DB-toetsen, maakt een CosmosClient-object, krijgt de temperatuur van het aquarium en slaat deze op in Azure Cosmos DB.

In dit voorbeeld wordt de [API voor lijstsleutels](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) gebruikt om toegang te krijgen tot uw Azure Cosmos DB-accountsleutels.

> [!IMPORTANT] 
> Als u de rol [Cosmos DB-accountlezer](#grant-access-to-your-azure-cosmos-account) wilt toewijzen, moet u de API [Voor alleen-lezen lijst](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys)gebruiken. Dit vult alleen de alleen-lezen toetsen.

De API Lijstsleutels `DatabaseAccountListKeysResult` retourneert het object. Dit type is niet gedefinieerd in de C#-bibliotheken. De volgende code toont de implementatie van deze klasse:  

```csharp 
namespace Monitor 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

In het voorbeeld wordt ook gebruik gemaakt van een eenvoudig document genaamd 'TemperatureRecord', dat als volgt wordt gedefinieerd:

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

U gebruikt de [Microsoft.Azure.Services.AppAuthentication-bibliotheek](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) om het door het systeem toegewezen beheerde identiteitstoken te krijgen. Zie het artikel [service-to-service-verificatie](../key-vault/general/service-to-service-authentication.md) voor `Microsoft.Azure.Service.AppAuthentication` meer informatie over het token en meer informatie over de bibliotheek.


```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class FishTankTemperatureService
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

U bent nu klaar om [uw functie-app](../azure-functions/functions-create-first-function-vs-code.md)te implementeren.

## <a name="next-steps"></a>Volgende stappen

* [Verificatie op basis van certificaten met Azure Cosmos DB en Azure Active Directory](certificate-based-authentication.md)
* [Azure Cosmos DB-sleutels beveiligen met Azure Key Vault](access-secrets-from-keyvault.md)
* [Beveiligingsbasislijn voor Azure Cosmos DB](security-baseline.md)
