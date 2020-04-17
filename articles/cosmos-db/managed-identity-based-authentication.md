---
title: Een door het systeem toegewezen beheerde identiteit gebruiken om toegang te krijgen tot Azure Cosmos DB-gegevens
description: Meer informatie over het configureren van een door Azure AD-systeem toegewezen beheerde identiteit voor toegangssleutels vanuit Azure Cosmos DB. msi, managed service identity, aad, azure active directory, identiteit
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 37e5cb817db2c54a07ab04c4dcc31b1976fdf03d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450051"
---
# <a name="how-to-use-a-system-assigned-managed-identity-to-access-azure-cosmos-db-data"></a>Een door het systeem toegewezen beheerde identiteit gebruiken om toegang te krijgen tot Azure Cosmos DB-gegevens

In dit artikel stelt u een **robuuste, belangrijke rotatieagnostische oplossing** in om toegang te krijgen tot Azure Cosmos DB-sleutels door gebruik te maken van [beheerde identiteiten.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) In het voorbeeld in dit artikel wordt een Azure-functie gebruikt. U deze oplossing echter bereiken door elke service te gebruiken die beheerde identiteiten ondersteunt. 

U leert hoe u een Azure-functie maakt die toegang heeft tot Azure Cosmos DB zonder dat u Azure Cosmos DB-sleutels hoeft te kopiëren. De functie wordt elke minuut wakker en registreert de huidige temperatuur van een aquariumaquarium. Zie de functie Een functie maken in Azure die wordt geactiveerd door een timerin te stellen voor meer informatie over het instellen van een azure-functie [die wordt geactiveerd door een timerartikel.](../azure-functions/functions-create-scheduled-function.md)

Als u het scenario wilt vereenvoudigen, wordt het opschonen van oudere temperatuurdocumenten verwerkt door een reeds geconfigureerde [time-to-live-instelling.](./time-to-live.md) 

## <a name="assign-a-system-assigned-managed-identity-to-an-azure-function"></a>Een door het systeem toegewezen beheerde identiteit toewijzen aan een Azure-functie

In deze stap wijst u een door het systeem toegewezen beheerde identiteit toe aan uw Azure-functie.

1. Open in de [Azure-portal](https://portal.azure.com/)het deelvenster **Azure-functie** en navigeer naar de functie-app. 

1. Open het tabblad**Identiteit van** **platformfuncties:** >  

   ![Tabblad Identiteit](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. Schakel op het tabblad **Identiteit** **de** status **Systeemidentiteit in.** Selecteer **Opslaan**en controleer of u de systeemidentiteit wilt inschakelen. Aan het einde moet het deelvenster **Systeemidentiteit** er als volgt uitzien:  

   ![Systeemidentiteit ingeschakeld](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-the-managed-identity-access-to-your-azure-cosmos-account"></a>De beheerde identiteitstoegang verlenen tot uw Azure Cosmos-account

In deze stap wijst u een rol toe aan de door het systeem toegewezen beheerde identiteit van de Azure-functie. Azure Cosmos DB heeft meerdere ingebouwde rollen die u toewijzen aan de beheerde identiteit. Voor deze oplossing gebruikt u de volgende twee rollen:

|Ingebouwde rol  |Beschrijving  |
|---------|---------|
|[DocumentDB-accountbijdrager](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Kan Azure Cosmos DB-accounts beheren. Hiermee u lees-/schrijftoetsen ophalen. |
|[Cosmos DB-accountlezer](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Kan Azure Cosmos DB-accountgegevens lezen. Hiermee u leessleutels ophalen. |

> [!IMPORTANT]
> RBAC-ondersteuning in Azure Cosmos DB is alleen van toepassing op bewerkingen van besturingsvliegtuigen. Gegevensvlakbewerkingen worden beveiligd met hoofdsleutels of resourcetokens. Zie het artikel [Beveiligde toegang tot gegevens](secure-access-to-data.md) voor meer informatie.

> [!TIP] 
> Bij het toewijzen van rollen alleen de benodigde toegang toewijzen. Als uw service alleen leesgegevens vereist, wijst u de beheerde identiteit toe aan de rol **Cosmos DB Account Reader.** Zie het artikel over de lagere blootstelling van bevoegde accounts voor meer informatie over het belang van de minste toegang tot [bevoegdheden.](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)

Voor uw scenario leest u de temperatuur en schrijft u die gegevens terug naar een container in Azure Cosmos DB. Omdat u de gegevens moet schrijven, gebruikt u de rol **DocumentDB-accountbijdrage.** 

1. Meld u aan bij de Azure-portal en navigeer naar uw Azure Cosmos DB-account. Open het **deelvenster Toegangsbeheer (IAM)** en vervolgens het tabblad **Roltoewijzingen:**

   ![IAM-deelvenster](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. Selecteer de + **knop Toevoegen** en **voeg roltoewijzing toe.**

1. Het deelvenster **Roltoewijzing toevoegen** wordt rechts geopend:

   ![Rol toevoegen](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **Rol** - **DocumentDB-accountbijdrager selecteren**
   * **Toegang toewijzen tot** - Selecteer onder de onderafdeling Beheerde identiteit selecteren onder de onderafdeling **Beheerde identiteit** selecteren de optie **Functie-app**.
   * **Selecteer** - Het deelvenster wordt gevuld met alle functie-apps in uw abonnement met een **beheerde systeemidentiteit.** In ons geval selecteer ik de **functie-app SummaryService:** 

      ![Toewijzing selecteren](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. Nadat de identiteit van de functie-app is geselecteerd, klikt u op **Opslaan**.

## <a name="programmatically-access-the-azure-cosmos-db-keys-from-the-azure-function"></a>Programmatisch toegang tot de Azure Cosmos DB-sleutels vanuit de Azure-functie

Nu hebben we een functie-app die een door het systeem toegewezen beheerde identiteit heeft. Die identiteit krijgt de rol **documentdb-accountbijdrager** in de Azure Cosmos DB-machtigingen. De volgende functie-app code krijgt de Azure Cosmos DB-toetsen, maakt een CosmosClient-object, krijgt de temperatuur en slaat dit op in Cosmos DB.

In dit voorbeeld wordt de [API voor lijstsleutels](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) gebruikt om toegang te krijgen tot uw Azure Cosmos DB-accountsleutels.

> [!IMPORTANT] 
> Als u de rol [ **Cosmos DB-accountlezer** ](#grant-the-managed-identity-access-to-your-azure-cosmos-account) wilt toewijzen, moet u de api alleen-lezen [lijstsleutels](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys)gebruiken. Hiermee worden alleen de alleen-lezen toetsen ingevuld.

De API Lijstsleutels `DatabaseAccountListKeysResult` retourneert het object. Dit type is niet gedefinieerd in de C#-bibliotheken. De volgende code toont de implementatie van deze klasse:  

```csharp 
namespace SummarizationService 
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

In het voorbeeld wordt ook gebruik gemaakt van een eenvoudig document genaamd "TemperatureRecord", dat als volgt wordt gedefinieerd:

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

U gebruikt de [Microsoft.Azure.Services.AppAuthentication-bibliotheek](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) om het door het systeem toegewezen beheerde identiteitstoken te krijgen. Zie het artikel Service To Service Authentication `Microsoft.Azure.Service.AppAuthentication` voor meer informatie over andere manieren om het token en meer informatie over de bibliotheek [te](../key-vault/general/service-to-service-authentication.md) krijgen.

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
    public static class TemperatureMonitor
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

        [FunctionName("TemperatureMonitor")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // In order to get the Service Managed token we need to authenticate to the Azure Resource Manager.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // To get the Azure Cosmos DB keys setup the List Keys API:
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the Result back as a DatabaseAccountListKeysResult.
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
            // fake the temperature sensor for this demo
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

U bent nu klaar om [uw Azure-functie](../azure-functions/functions-create-first-function-vs-code.md)te implementeren.

## <a name="next-steps"></a>Volgende stappen

* [Verificatie op basis van certificaten met Azure Cosmos DB en Active Directory](certificate-based-authentication.md)
* [Azure Cosmos-sleutels beveiligen met Azure Key Vault](access-secrets-from-keyvault.md)
* [Beveiligingsbasislijn voor Azure Cosmos DB](security-baseline.md)
