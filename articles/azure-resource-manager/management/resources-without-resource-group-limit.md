---
title: Resources zonder limiet voor 800 tellingen
description: Hiermee worden de Azure-brontypen weergegeven die meer dan 800 exemplaren in een resourcegroep kunnen bevatten.
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 735cad0bfa936c41f603e42bdb9be77a1562cc1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937937"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Resources die niet zijn beperkt tot 800 exemplaren per resourcegroep

Standaard u in elke resourcegroep maximaal 800 exemplaren van een resourcetype implementeren. Sommige resourcetypen zijn echter vrijgesteld van de 800-instantielimiet. In dit artikel worden de Azure-brontypen weergegeven die meer dan 800 exemplaren in een resourcegroep kunnen bevatten. Alle andere resources typen zijn beperkt tot 800 exemplaren.

Voor sommige resourcetypen moet u contact opnemen met ondersteuning om de 800-instantielimiet te laten verwijderen. Deze resourcetypen worden in dit artikel vermeld.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automatiseringAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* Registraties
* registraties/klantAbonnementen
* registraties/producten
* verificatieToetsen

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices - Standaard beperkt tot 800 exemplaren. Die limiet kan worden verhoogd door contact op te nemen met ondersteuning.

## <a name="microsoftcompute"></a>Microsoft.Compute

* Schijven
* images
* momentopnamen
* virtueleMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroepen

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registers/buildTaken
* registers/buildTaken/listSourceRepositoryProperties
* registers/buildTaken/stappen
* registers/buildTaken/steps/listBuildArguments
* registers/eventGridFilters
* registers/replicaties
* registers/taken
* registers/webhooks

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* Servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* Servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* serverGroepen
* Servers
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* Clusters
* Naamruimten

## <a name="microsoftexperimentation"></a>Microsoft.Experimenteren

* experimentWerkruimten

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* configuratieProfielToewijzingen
* gastConfiguratieToewijzingen
* Software
* softwareUpdateProfiel
* softwareUpdates

## <a name="microsoftinsights"></a>Microsoft.Insights

* metrische waarschuwingen

## <a name="microsoftlogic"></a>Microsoft.Logic

* integratieAccounts
* Werkstromen

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capaciteitspools
* netAppAccounts/capaciteitspools/volumes
* netAppAccounts/capacityPools/volumes/mountTargets
* netAppAccounts/capacityPools/volumes/snapshots

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallBeleid
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans ddosProtectionPlans
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
* dnszones/recordsets
* netwerkIntentPolicies
* netwerkInterfaces
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
* privéeindpunten
* privateLinkServices
* publicIPAddresses - Standaard beperkt tot 800 exemplaren. Die limiet kan worden verhoogd door contact op te nemen met ondersteuning.
* serviceEndpointBeleid
* trafficmanagerprofielen
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* werkruimtecollecties - Standaard beperkt tot 800 exemplaren. Die limiet kan worden verhoogd door contact op te nemen met ondersteuning.

## <a name="microsoftrelay"></a>Microsoft.Relay

* Naamruimten

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollecties

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* Naamruimten

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

* toepassingen
* containerGroepen
* Gateways
* Netwerken
* geheimen
* volumes

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/api's
* sites

## <a name="next-steps"></a>Volgende stappen

Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](azure-subscription-service-limits.md)voor een volledige lijst met quota en limieten.
