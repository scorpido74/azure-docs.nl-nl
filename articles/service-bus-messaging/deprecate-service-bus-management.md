---
title: Azure-berichtenservices - Servicebeheer naar Resourcebeheer
description: In dit artikel worden afgeschafte Azure Service Manager REST API-& PowerShell-cmdlets in kaart gebracht voor Rest API-& PowerShell-cmdlets.
services: service-bus-messaging, event-hubs, event-grid
documentationcenter: na
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2020
ms.author: spelluru
ms.openlocfilehash: d263381667319b98a28ee6168e2de75c4041b58a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589907"
---
# <a name="deprecation-of-azure-service-manager-support-for-azure-service-bus-relay-and-event-hubs"></a>Afschrijving van Azure Service Manager-ondersteuning voor Azure Service Bus-, Relay- en EventHubs

Resource Manager, onze cloudinfrastructuurstack van de volgende generatie, vervangt volledig het 'klassieke' Azure Service Management-model (klassiek implementatiemodel). Als gevolg hiervan worden de klassieke implementatiemodel REST API's en ondersteuning voor Service Bus, Relay en Event Hubs op 1 november 2021 buiten gebruik gesteld. Deze afschaffing werd voor het eerst aangekondigd op een [Microsoft Tech Community aankondiging](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Deprecating-Service-Management-support-for-Azure-Service-Bus/ba-p/370909), maar we hebben onlangs besloten om de afschaffing periode twee jaar te verlengen vanaf het moment van de oorspronkelijke aankondiging. Voor eenvoudige identificatie hebben `management.core.windows.net` deze API's in hun URI. Raadpleeg de volgende tabel voor een lijst met de afgeschafte API's en hun Azure Resource Manager API-versie die u nu moet gebruiken.

Als u ServiceBus,Relay en Event Hubs wilt blijven gebruiken, u vtegen 31 oktober 2021 de overstap maken naar Resource Manager. We moedigen alle klanten die nog steeds oude API's gebruiken aan om snel de overstap te maken om te profiteren van de extra voordelen van Resource Manager, waaronder resourcegrouping, tags, een gestroomlijnd implementatie- en beheerproces en fijnkorrelige toegang controle met behulp van role-based access control (RBAC).

Zie het [TechNet-blog](https://blogs.technet.microsoft.com/meamcs/2016/12/22/difference-between-azure-service-manager-and-azure-resource-manager/)voor meer informatie over Azure Resource Manager vs Azure Service Manager.

Zie onze REST API-documentatie voor meer informatie over API's voor ServiceManager en Resource Manager voor Azure Service Bus, Relay en Event Hubs:

- [Azure Service Bus](/rest/api/servicebus/)
- [Azure Event Hubs](/rest/api/eventhub/)
- [Azure Relay](/rest/api/relay/)

## <a name="service-manager-rest-api---resource-manager-rest-api"></a>Service Manager REST API - Resource Manager REST API

| API's voor servicebeheer (afgeschaft) | Resource Manager - Api servicebus | Resource Manager - Gebeurtenishub-API | Resource Manager - Relay API |
| --------------- | ----------------- | ----------------- | ----------------- | 
| **Naamruimten-GetNamespaceAsync** <br/>[Servicebus Ontvang naamruimte](/rest/api/servicebus/get-namespace)<br/>[Naamruimte voor gebeurtenishub optehalen](/rest/api/eventhub/get-event-hub)<br/>[Relay Get Namespace](/rest/api/servicebus/get-relays)<br/> ```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [ophalen](/rest/api/servicebus/namespaces/get) | [ophalen](/rest/api/eventhub/namespaces/get) | [ophalen](/rest/api/relay/namespaces/get) |
| **Details van ConnectionDetails-GetConnection**<br/>Service Bus/Event Hub/Relay GetConnectionDetals<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/ConnectionDetails``` | [lijstsleutels](/rest/api/servicebus/namespaces/listkeys) | [lijstsleutels](/rest/api/eventhub/namespaces/listkeys) | [lijstsleutels](/rest/api/relay/namespaces/listkeys) |
| **Onderwerpen-GetTopicsAsync**<br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics? $skip={skip}&$top={top}``` | [list](/rest/api/servicebus/topics/listbynamespace) | &nbsp; | &nbsp; | 
| **Wachtrijen-GetQueueAsync** <br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/queues/{queueName}``` | [ophalen](/rest/api/servicebus/queues/get) | &nbsp; | &nbsp; | 
| **Relays-GetRelaysAsync**<br/>[Ontvang relais](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/relays? $skip={skip}&$top={top}```| &nbsp; | &nbsp; | [list](/rest/api/relay/wcfrelays/listbynamespace) | 
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRuleAsync**<br/>Servicebus/Gebeurtenishub/Relay GetNamespaceAuthRule<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/authorizationrules?``` | [getauthorizationregel](/rest/api/servicebus/namespaces/getauthorizationrule) |[getauthorizationregel](/rest/api/eventhub/namespaces/getauthorizationrule) | [getauthorizationregel](/rest/api/relay/namespaces/getauthorizationrule) |
| **Naamruimten-DeleteNamespaceAsync**<br/>[Naamruimte voor servicebus verwijderen](/rest/api/servicebus/delete-namespace)<br/>[Naamruimte voor gebeurtenishubs verwijderen](/rest/api/eventhub/delete-event-hub)<br/>[Naamruimte voor het verwijderen van relays doorsturen](/rest/api/servicebus/delete-namespace)<br/> ```DELETE  https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [Verwijderen](/rest/api/servicebus/namespaces/delete) | [Verwijderen](/rest/api/eventhub/namespaces/delete) | [Verwijderen](/rest/api/relay/namespaces/delete) | 
| **MessagingSKUPlan-GetPlanAsync**<br/>Servicebus/gebeurtenishub/relay ontvang naamruimte<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [ophalen](/rest/api/servicebus/namespaces/get) | [ophalen](/rest/api/eventhub/namespaces/get) | [ophalen](/rest/api/relay/namespaces/get) |
| **MessagingSKUPlan-UpdatePlanAsync**<br/>Servicebus/gebeurtenishub/relay ontvang naamruimte<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **NamespaceAuthorizationRules-UpdateNameSpaceAuthorizationRuleAsync**<br/>Servicebus/gebeurtenishub/relay ontvang naamruimte<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) | 
| **NamespaceAuthorizationRules-CreateNamespaceAuthorizationRuleAsync**<br/> 
Servicebus/gebeurtenishub/relais<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` |[createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) |
| **NaamruimteProperties-GetNamespacePropertiesAsync**<br/>[Servicebus Ontvang naamruimte](/rest/api/servicebus/get-namespace)<br/>[Gebeurtenishubs krijgen naamruimte](/rest/api/eventhub/get-event-hub)<br/>[Relay Get Namespace](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [ophalen](/rest/api/servicebus/namespaces/get) | [ophalen](/rest/api/eventhub/namespaces/get) | [ophalen](/rest/api/relay/namespaces/get) |
| **RegioCodes-GetRegionCodesAsync**<br/>ServiceBus/EventHub/Relay Ontvang naamruimte<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [listbysku](/rest/api/servicebus/regions/listbysku) | [listbysku](/rest/api/eventhub/regions/listbysku) | &nbsp; | 
| **NamespaceProperties-UpdateNameSpacePropertyAsync**<br/>Servicebus/EventHub/Relay<br/>```GET    https://management.core.windows.net/{subscription ID}/services/ServiceBus/Regions/``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **EventHubsCrud-ListEventHubsAsync**<br/>[Gebeurtenishubs aanbieden](/rest/api/eventhub/list-event-hubs)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs?$skip={skip}&$top={top}``` | &nbsp; | [list](/rest/api/eventhub/eventhubs/listbynamespace) | &nbsp; | 
| **EventHubsCrud-GetEventHubAsync**<br/>[Gebeurtenishubs ophalen](/rest/api/eventhub/get-event-hub)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs/{eventHubPath}``` | &nbsp; | [ophalen](/rest/api/eventhub/eventhubs/get) | &nbsp; | 
| **NamespaceAuthorizationRules-DeleteNameSpaceAuthorizationRuleAsync**<br/>Servicebus/gebeurtenishub/relais<br/>```DELETE https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [schrappingsregel](/rest/api/servicebus/namespaces/deleteauthorizationrule) | [schrappingsregel](/rest/api/eventhub/namespaces/deleteauthorizationrule) | [schrappingsregel](/rest/api/relay/namespaces/deleteauthorizationrule) |
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRulesAsync**<br/>Servicebus/EventHub/Relay<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules``` | [lijstautorisatieregels](/rest/api/servicebus/namespaces/listauthorizationrules) | [lijstautorisatieregels](/rest/api/eventhub/namespaces/listauthorizationrules) | [lijstautorisatieregels](/rest/api/relay/namespaces/listauthorizationrules) |
| **NamespaceAvailability-IsNamespaceBeschikbaar**<br/>[Beschikbaarheid servicebusnaamruimte](/rest/api/servicebus/check-namespace-availability)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/CheckNamespaceAvailability/?namespace=<namespaceValue>``` | [beschikbaarheid van checkname](/rest/api/servicebus/namespaces/checknameavailability) | [beschikbaarheid van checkname](/rest/api/eventhub/namespaces/checknameavailability) | [beschikbaarheid van checkname](/rest/api/relay/namespaces/checknameavailability) |
| **Naamruimten-CreateOrUpdateNamespaceAsync**<br/>Servicebus/gebeurtenishub/relais<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) | 
| **Onderwerpen-GetTopicAsync**<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics/{topicPath}``` | [ophalen](/rest/api/servicebus/topics/get) | &nbsp; | &nbsp; |

## <a name="service-manager-powershell---resource-manager-powershell"></a>Service Manager PowerShell - Resource Manager PowerShell
| Service Manager PowerShell, opdracht (afgeschaft) | Nieuwe resourcebeheeropdrachten | Nieuwere resourcebeheer, opdracht |
| ----- | ----- | ----- | 
| [Get-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Get-azservicebusautorisatieregel](/powershell/module/az.servicebus/get-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Get-AzureSBLocation](/powershell/module/servicemanagement/azure/get-azuresblocation?view=azuresmps-4.0.0) | [Get-AzurermServiceBusGeoDR-configuratie](/powershell/module/azurerm.servicebus/get-azurermservicebusgeodrconfiguration?view=azurermps-6.13.0) | [Get-AzServiceBusGeoDR-configuratie](/powershell/module/az.servicebus/get-azservicebusgeodrconfiguration?view=azps-1.6.0) |
| [Get-AzureSBNamespace](/powershell/module/servicemanagement/azure/get-azuresbnamespace?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace?view=azurermps-6.13.0) | [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace?view=azps-1.6.0) |
| [New-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/new-azuresbauthorizationrule?view=azuresmps-4.0.0) | [New-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Nieuwe azservicebusauthorizationregel](/powershell/module/az.servicebus/new-azservicebusauthorizationrule?view=azps-1.6.0) |
| [New-AzureSBNamespace](/powershell/module/servicemanagement/azure/new-azuresbnamespace?view=azuresmps-4.0.0) | [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace?view=azurermps-6.13.0) | [Nieuw-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace?view=azps-1.6.0) |
| [Machtigingregel voor verwijderen-azurermrelay](/powershell/module/azurerm.relay/remove-azurermrelayauthorizationrule?view=azurermps-6.13.0) | [Remove-AzureRmEventHubAuthorizationRule](/powershell/module/azurerm.eventhub/remove-azurermeventhubauthorizationrule?view=azurermps-6.13.0) | [Regels voor verwijdering-azservicebusautorisatie](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Remove-AzureSBNamespace](/powershell/module/servicemanagement/azure/remove-azuresbnamespace?view=azuresmps-4.0.0) | [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace?view=azurermps-6.13.0) | [Verwijderen-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace?view=azps-1.6.0) |
| [Set-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/set-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Set-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Set-azservicebusautorisatieregel](/powershell/module/az.servicebus/set-azservicebusauthorizationrule?view=azps-1.6.0) |

## <a name="next-steps"></a>Volgende stappen
Zie de volgende documentatie: 

- Laatste REST API-documentatie
    - [Azure Service Bus](/rest/api/servicebus/)
    - [Azure Event Hubs](/rest/api/eventhub/)
    - [Azure Relay](/rest/api/relay/)
- Nieuwste PowerShell-documentatie
    - [Azure Service Bus](/powershell/module/azurerm.servicebus/?view=azurermps-6.13.0#service_bus)
    - [Azure Event Hubs](/powershell/module/azurerm.eventhub/?view=azurermps-6.13.0#event_hub)
    - [Azure Event Grid](/powershell/module/azurerm.eventgrid/?view=azurermps-6.13.0#event_grid)
