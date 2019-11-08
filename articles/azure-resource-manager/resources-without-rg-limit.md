---
title: Azure-resources zonder limiet van 800
description: Geeft een lijst van de Azure-resource typen die meer dan 800 exemplaren in een resource groep kunnen bevatten.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: tomfitz
ms.openlocfilehash: c08b80a841199ed2737f6fe8d7f2b76943c5b269
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795655"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Resources die niet beperkt zijn tot 800 exemplaren per resource groep

Standaard kunt u Maxi maal 800 exemplaren van een resource type implementeren in elke resource groep. Sommige resource typen zijn echter uitgezonderd van de limiet van 800-exemplaren. In dit artikel vindt u een overzicht van de Azure-resource typen die meer dan 800 exemplaren in een resource groep kunnen bevatten. Alle andere typen resources zijn beperkt tot 800 exemplaren.

Voor sommige resource typen moet u contact opnemen met ondersteuning om de limiet van 800-exemplaren te verwijderen. Deze resource typen worden in dit artikel vermeld.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Micro soft. AzureStack

* registraties
* registraties/customerSubscriptions
* registraties/producten

## <a name="microsoftbotservice"></a>Micro soft. BotService

* botServices: standaard beperkt tot 800 exemplaren. Deze limiet kan worden verhoogd door contact op te nemen met de ondersteuning.

## <a name="microsoftcompute"></a>Microsoft.Compute

* cd's
* images
* momentopnamen
* Informatie

## <a name="microsoftcontainerinstance"></a>Micro soft. ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registers/buildTasks
* registers/buildTasks/listSourceRepositoryProperties
* registers/buildTasks/stappen
* registers/buildTasks/stappen/listBuildArguments
* registers/eventGridFilters
* registers/replicaties
* registers/taken
* registers/webhooks

## <a name="microsoftdbformariadb"></a>Micro soft. DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Micro soft. DBforMySQL

* servers

## <a name="microsoftdbforpostgresql"></a>Micro soft. DBforPostgreSQL

* serverGroups
* servers
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>Micro soft. EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* clusters
* naam ruimten

## <a name="microsoftexperimentation"></a>Micro soft. experimenten

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Micro soft. GuestConfiguration

* configurationProfileAssignments
* guestConfigurationAssignments
* software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* stroom

## <a name="microsoftnetapp"></a>Micro soft. NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/volumes
* netAppAccounts/capacityPools/volumes/mountTargets
* netAppAccounts/capacityPools/volumes/moment opnamen

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/alle
* dnszones/record sets
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/alle
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses: standaard beperkt tot 800 exemplaren. Deze limiet kan worden verhoogd door contact op te nemen met de ondersteuning.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Micro soft. PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Micro soft. PowerBI

* workspaceCollections: standaard beperkt tot 800 exemplaren. Deze limiet kan worden verhoogd door contact op te nemen met de ondersteuning.

## <a name="microsoftrelay"></a>Microsoft.Relay

* naam ruimten

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* naam ruimten

## <a name="microsoftservicefabricmesh"></a>Micro soft. ServiceFabricMesh

* toepassingen
* containerGroups
* gateways
* netwerken
* geheimen
* volumes

## <a name="microsoftstorage"></a>Microsoft.Storage

* Storage accounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/api's
* sites

## <a name="next-steps"></a>Volgende stappen

Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-subscription-service-limits.md)voor een volledige lijst met quota's en limieten.
