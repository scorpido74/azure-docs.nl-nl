---
title: Beveiligings controles voor Azure API Management
description: Een controle lijst met beveiligings controles voor het evalueren van API Management
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: 670050efe01fb658fab52a43914f193e9798b828
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75751140"
---
# <a name="security-controls-for-api-management"></a>Beveiligings controles voor API Management

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in API Management.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Notities | Documentatie |
|---|---|--|--|
| Ondersteuning voor service-eind punten| No | |  |
| Ondersteuning voor VNet-injectie| Yes | |  |
| Ondersteuning voor netwerk isolatie en firewalling| Yes | Respectievelijk netwerk beveiligings groepen (NSG) en Azure-toepassing gateway (of ander software apparaat) gebruiken. |  |
| Ondersteuning voor geforceerde tunneling| Yes | Azure-netwerken bieden geforceerde tunneling. |  |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Notities| Documentatie |
|---|---|--|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Yes | | |
| Logboek registratie en controle op het vlak van controle en beheer| Yes | [Azure Monitor activiteiten logboeken](../azure-monitor/platform/platform-logs-overview.md) | |
| Logboek registratie en controle van het gegevens vlak| Yes | [Azure monitor Diagnostische logboeken](../azure-monitor/platform/platform-logs-overview.md) en (optioneel) [Azure-toepassing Insights](../azure-monitor/app/app-insights-overview.md).  | |


## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Notities| Documentatie |
|---|---|--|--|
| Verificatie| Yes | |  |
| Autorisatie| Yes | |  |

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Notities | Documentatie |
|---|---|--|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | Yes | Gevoelige gegevens zoals certificaten, sleutels en geheime naam waarden worden versleuteld met Service-beheerde exemplaren per service-exemplaar. |  |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | No | Alle versleutelings sleutels zijn per service-exemplaar en worden beheerd door de service. |  |
| Versleuteling op kolom niveau (Azure Data Services)| N.v.t. | |  |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Yes | [Express route](../expressroute/index.yml) en VNet-versleuteling worden door [Azure-netwerken](../virtual-network/index.yml)verschaft. |  |
| Versleutelde API-aanroepen| Yes | Aanroepen van beheer vlak worden gedaan via [Azure Resource Manager](../azure-resource-manager/index.yml) via TLS. Er is een geldig JSON-webtoken (JWT) vereist.  Aanroepen voor gegevens vlak kunnen worden beveiligd met TLS en een van de ondersteunde verificatie mechanismen (bijvoorbeeld client certificaat of JWT). |   |
 |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Notities| Documentatie |
|---|---|--|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Yes | De [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) gebruiken |  |

## <a name="vulnerability-scans-false-positives"></a>Beveiligings probleem scant onjuiste positieven

In deze sectie worden veelvoorkomende beveiligings problemen gedocumenteerd die niet van invloed zijn op Azure API Management.

| Beveiligingsprobleem               | Description                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed is een beveiligings probleem in de implementatie van de TLS SessionTicket-extensie die is gevonden in sommige F5-producten. Hiermee kan het lekken (' verbloeden ') van Maxi maal 31 bytes aan gegevens van niet-geïnitialiseerd geheugen. Dit wordt veroorzaakt door de TLS-stack opvulling van een sessie-ID, door gegeven van de client, met gegevens om deze 32 bits lang te maken. |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).