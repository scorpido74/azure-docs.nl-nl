---
title: Azure Service Bus-beheer bibliotheken | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u Azure Service Bus-beheer bibliotheken kunt gebruiken om Service Bus naam ruimten en entiteiten dynamisch in te richten.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 042edcd1851f86dd2a660673bc87884b68410bfb
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85341711"
---
# <a name="service-bus-management-libraries"></a>Service Bus-beheerbibliotheken

De Azure Service Bus-beheer bibliotheken kunnen Service Bus naam ruimten en entiteiten dynamisch inrichten. Zo kunt u complexe implementaties en bericht scenario's maken en kunt u programmatisch bepalen welke entiteiten moeten worden ingericht. Deze bibliotheken zijn momenteel beschikbaar voor .NET.

## <a name="supported-functionality"></a>Ondersteunde functionaliteit

* Naam ruimte maken, bijwerken, verwijderen
* Wachtrij maken, bijwerken, verwijderen
* Onderwerp maken, bijwerken, verwijderen
* Abonnementen maken, bijwerken, verwijderen

## <a name="prerequisites"></a>Vereisten

Als u de Service Bus-beheer bibliotheken wilt gaan gebruiken, moet u zich verifiëren met de service Azure Active Directory (Azure AD). Voor Azure AD moet u worden geverifieerd als Service-Principal, waarmee u toegang hebt tot uw Azure-resources. Zie een van de volgende artikelen voor meer informatie over het maken van een Service-Principal:  

* [Gebruik de Azure Portal om Active Directory-toepassing en Service-Principal te maken die toegang hebben tot resources](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Azure PowerShell gebruiken om een service-principal te maken voor toegang tot resources](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Azure CLI gebruiken om een service-principal te maken voor toegang tot resources](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Deze zelf studies bieden u een `AppId` (client-id), `TenantId` , en `ClientSecret` (verificatie sleutel), die allemaal worden gebruikt voor verificatie door de beheer bibliotheken. U moet ten minste [**Azure Service Bus gegevens eigenaar**](/azure/role-based-access-control/built-in-roles#azure-service-bus-data-owner) of [**Inzender**](/azure/role-based-access-control/built-in-roles#contributor) machtigingen hebben voor de resource groep waarop u wilt uitvoeren.

## <a name="programming-pattern"></a>Programmerings patroon

Het patroon voor het bewerken van een Service Bus resource volgt een gemeen schappelijk Protocol:

1. Een token verkrijgen van Azure AD met behulp van de **micro soft. Identity model. clients. ActiveDirectory** -bibliotheek:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Maak het `ServiceBusManagementClient` object:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Stel de `CreateOrUpdate` para meters in op uw opgegeven waarden:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. De aanroep uitvoeren:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="complete-code-to-create-a-queue"></a>Volledige code voor het maken van een wachtrij
Dit is de volledige code voor het maken van een Service Bus wachtrij: 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

> [!IMPORTANT]
> Zie het voor [beeld van .net Management op github](https://github.com/Azure-Samples/service-bus-dotnet-management/)voor een volledig voor beeld. 

## <a name="next-steps"></a>Volgende stappen
[Naslag informatie voor micro soft. Azure. Management. ServiceBus API](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
