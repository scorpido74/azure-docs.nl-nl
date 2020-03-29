---
title: Azure Service Bus-beheerbibliotheken| Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u Azure Service Bus-beheerbibliotheken gebruiken om naamruimten en entiteiten van servicebussen dynamisch in te richten.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: d0e90d9278ede97de04ad8efeaa59d94a4567f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756263"
---
# <a name="service-bus-management-libraries"></a>Service Bus-beheerbibliotheken

De azure servicebusbeheerbibliotheken kunnen servicebusnaamruimten en -entiteiten dynamisch inrichten. Dit maakt complexe implementaties en messaging-scenario's mogelijk en maakt het mogelijk om programmatisch te bepalen welke entiteiten moeten worden ingerichte. Deze bibliotheken zijn momenteel beschikbaar voor .NET.

## <a name="supported-functionality"></a>Ondersteunde functionaliteit

* Namespace maken, bijwerken, verwijderen
* Wachtrij maken, bijwerken, verwijderen
* Onderwerp maken, bijwerken, verwijderen
* Het maken, bijwerken, verwijderen van een abonnement

## <a name="prerequisites"></a>Vereisten

Als u aan de slag wilt met de beheerbibliotheken van ServiceBus, moet u zich verifiëren met de Azure AD-service (Azure Directory). Azure AD vereist dat u verifieert als serviceprincipal, die toegang biedt tot uw Azure-bronnen. Zie een van de volgende artikelen voor informatie over het maken van een serviceprincipal:  

* [De Azure-portal gebruiken om Active Directory-toepassing en serviceprincipal te maken die toegang hebben tot bronnen](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Azure PowerShell gebruiken om een service-principal te maken voor toegang tot resources](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Azure CLI gebruiken om een service-principal te maken voor toegang tot resources](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Deze zelfstudies bieden `AppId` u een `TenantId`(client-ID) en `ClientSecret` (verificatiesleutel), die allemaal worden gebruikt voor verificatie door de beheerbibliotheken. U moet **eigenaarmachtigingen** hebben voor de resourcegroep waarop u wilt uitvoeren.

## <a name="programming-pattern"></a>Programmeerpatroon

Het patroon om een Service Bus-bron te manipuleren volgt een algemeen protocol:

1. Een token verkrijgen bij Azure AD met de **Microsoft.IdentityModel.Clients.ActiveDirectory-bibliotheek:**
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Maak `ServiceBusManagementClient` het object:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Stel `CreateOrUpdate` de parameters in op de opgegeven waarden:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Voer de oproep uit:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="complete-code-to-create-a-queue"></a>Code voltooien om een wachtrij te maken
Hier is de volledige code om een servicebuswachtrij te maken: 

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
> Zie voor een volledig voorbeeld het [voorbeeld van .NET-beheer op GitHub](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

## <a name="next-steps"></a>Volgende stappen
[Microsoft.Azure.Management.ServiceBus API-verwijzing](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
