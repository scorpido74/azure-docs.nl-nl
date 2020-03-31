---
title: Beheerbibliotheken - Azure Event Hubs| Microsoft Documenten
description: In dit artikel vindt u informatie over de bibliotheek die u gebruiken om naamruimten en entiteiten van Azure Event Hubs te beheren vanaf .NET.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60746655"
---
# <a name="event-hubs-management-libraries"></a>Event Hubs-beheerbibliotheken

U de beheerbibliotheken van Azure Event Hubs gebruiken om naamruimten en entiteiten voor gebeurtenishubs dynamisch in te richten. Deze dynamische aard maakt complexe implementaties en messaging-scenario's mogelijk, zodat u programmatisch bepalen welke entiteiten u wilt inrichten. Deze bibliotheken zijn momenteel beschikbaar voor .NET.

## <a name="supported-functionality"></a>Ondersteunde functionaliteit

* Namespace maken, bijwerken, verwijderen
* Event Hubs maken, bijwerken, verwijderen
* Creatie, update, verwijdering van consumentengroep

## <a name="prerequisites"></a>Vereisten

Als u aan de slag wilt met de beheerbibliotheken van gebeurtenishubs, moet u verifiëren met Azure Active Directory (AAD). AAD vereist dat u zich verifieert als serviceprincipal, die toegang biedt tot uw Azure-bronnen. Zie een van de volgende artikelen voor informatie over het maken van een serviceprincipal:  

* [De Azure-portal gebruiken om Active Directory-toepassing en serviceprincipal te maken die toegang hebben tot bronnen](../active-directory/develop/howto-create-service-principal-portal.md)
* [Azure PowerShell gebruiken om een service-principal te maken voor toegang tot resources](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Azure CLI gebruiken om een service-principal te maken voor toegang tot resources](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Deze zelfstudies bieden `AppId` u een `TenantId`(client-ID) en `ClientSecret` (verificatiesleutel), die allemaal worden gebruikt voor verificatie door de beheerbibliotheken. U moet **eigenaarmachtigingen** hebben voor de resourcegroep waarop u wilt uitvoeren.

## <a name="programming-pattern"></a>Programmeerpatroon

Het patroon om een gebeurtenishubs-bron te manipuleren, volgt een algemeen protocol:

1. Een token verkrijgen van `Microsoft.IdentityModel.Clients.ActiveDirectory` AAD met behulp van de bibliotheek.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Maak `EventHubManagementClient` het object.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Stel `CreateOrUpdate` de parameters in op de opgegeven waarden.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Voer het gesprek uit.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Volgende stappen
* [.NET Management voorbeeld](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Microsoft.Azure.Management.EventHub-referentie](/dotnet/api/Microsoft.Azure.Management.EventHub) 
