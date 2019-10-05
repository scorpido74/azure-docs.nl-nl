---
title: Azure-resources zonder limiet van 800
description: Geeft een lijst van de Azure-resource typen die meer dan 800 exemplaren in een resource groep kunnen bevatten.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/4/2019
ms.author: tomfitz
ms.openlocfilehash: 6677290999049dead40f39e9a840735810c7c763
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973308"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Resources die niet beperkt zijn tot 800 exemplaren per resource groep

Standaard kunt u Maxi maal 800 exemplaren van een resource type implementeren in elke resource groep. Sommige resource typen zijn echter uitgezonderd van de limiet van 800-exemplaren. In dit artikel vindt u een overzicht van de Azure-resource typen die meer dan 800 exemplaren in een resource groep kunnen bevatten. Alle andere typen resources zijn beperkt tot 800 exemplaren.

Voor sommige resource typen moet u contact opnemen met ondersteuning om de limiet van 800-exemplaren te verwijderen. Deze resource typen worden in dit artikel vermeld.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* registraties
* registrations/customerSubscriptions
* registraties/producten

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices: Neem contact op met de ondersteuning om de limiet uit te breiden.

## <a name="microsoftcompute"></a>Microsoft.Compute

* schijven
* afbeeldingen
* momentopnamen
* Informatie

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registers/buildTasks
* registers/buildTasks/listSourceRepositoryProperties
* registers/buildTasks/stappen
* registers/buildTasks/stappen/listBuildArguments
* registries/eventGridFilters
* registers/replicaties
* registers/taken
* registers/webhooks

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* serverGroups
* servers
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>Micro soft. EnterpriseKnowledgeGraph

* services

## <a name="microsoftguestconfiguration"></a>Micro soft. GuestConfiguration

* guestConfigurationAssignments
* software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* stroom

## <a name="microsoftnetapp"></a>Microsoft.NetApp

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
* dnszones/all
* dnszones/recordsets
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
* publicIPAddresses: Neem contact op met de ondersteuning om de limiet uit te breiden.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections: Neem contact op met de ondersteuning om de limiet uit te breiden.

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

* toepassingen
* containerGroups
* gateways
* netwerken
* geheimen
* volumes

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* sites

## <a name="next-steps"></a>Volgende stappen

Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-subscription-service-limits.md)voor een volledige lijst met quota's en limieten.
