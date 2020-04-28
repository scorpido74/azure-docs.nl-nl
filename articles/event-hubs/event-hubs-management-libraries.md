---
title: Beheer bibliotheken-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat informatie over de bibliotheek die u kunt gebruiken voor het beheren van Azure Event Hubs-naam ruimten en-entiteiten van .NET.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 431fe04461f422274697d1e91c4b56e914ce2d4e
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "60746655"
---
# <a name="event-hubs-management-libraries"></a>Event Hubs-beheerbibliotheken

U kunt de Azure Event Hubs-beheer bibliotheken gebruiken om Event Hubs naam ruimten en entiteiten dynamisch in te richten. Deze dynamische aard maakt complexe implementaties en bericht scenario's mogelijk, zodat u programmatisch kunt bepalen welke entiteiten moeten worden ingericht. Deze bibliotheken zijn momenteel beschikbaar voor .NET.

## <a name="supported-functionality"></a>Ondersteunde functionaliteit

* Naam ruimte maken, bijwerken, verwijderen
* Event Hubs maken, bijwerken, verwijderen
* Consumenten groep maken, bijwerken, verwijderen

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan met de Event Hubs-beheer Bibliotheken, moet u verifiëren met Azure Active Directory (AAD). Voor AAD moet u worden geverifieerd als Service-Principal, waarmee u toegang hebt tot uw Azure-resources. Zie een van de volgende artikelen voor meer informatie over het maken van een Service-Principal:  

* [Gebruik de Azure Portal om Active Directory-toepassing en Service-Principal te maken die toegang hebben tot resources](../active-directory/develop/howto-create-service-principal-portal.md)
* [Azure PowerShell gebruiken om een service-principal te maken voor toegang tot resources](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Azure CLI gebruiken om een service-principal te maken voor toegang tot resources](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Deze zelf studies bieden u een `AppId` (client-id) `TenantId`,, `ClientSecret` en (verificatie sleutel), die allemaal worden gebruikt voor verificatie door de beheer bibliotheken. U moet **eigenaars** machtigingen hebben voor de resource groep waarvoor u wilt uitvoeren.

## <a name="programming-pattern"></a>Programmerings patroon

Het patroon voor het bewerken van een Event Hubs resource volgt een gemeen schappelijk Protocol:

1. Een token verkrijgen van AAD met behulp van de `Microsoft.IdentityModel.Clients.ActiveDirectory` -bibliotheek.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Maak het `EventHubManagementClient` object.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Stel de `CreateOrUpdate` para meters in op uw opgegeven waarden.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. De aanroep uitvoeren.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Volgende stappen
* [Voor beeld van .NET-beheer](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Naslag informatie over micro soft. Azure. Management. EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 
