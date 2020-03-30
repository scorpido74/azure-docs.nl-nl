---
title: Beveiligingsbesturingselementen voor Azure API-beheer
description: Een checklist met beveiligingscontroles voor de evaluatie van API-beheer
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: 670050efe01fb658fab52a43914f193e9798b828
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751140"
---
# <a name="security-controls-for-api-management"></a>Beveiligingsbesturingselementen voor API-beheer

In dit artikel worden de beveiligingsbesturingselementen die zijn ingebouwd in API-beheer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligingscontrole | Ja/Nee | Opmerkingen | Documentatie |
|---|---|--|--|
| Ondersteuning voor serviceeindpunten| Nee | |  |
| Ondersteuning voor VNet-injectie| Ja | |  |
| Ondersteuning voor netwerkisolatie en firewalling| Ja | Respectievelijk netwerkbeveiligingsgroepen (NSG) en Azure Application Gateway (of ander softwaretoestel) gebruiken. |  |
| Ondersteuning voor gedwongen tunneling| Ja | Azure-netwerken bieden gedwongen tunneling. |  |

## <a name="monitoring--logging"></a>Controle & logboekregistratie

| Beveiligingscontrole | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Azure-bewakingsondersteuning (logboekanalyses, app-inzichten, enz.)| Ja | | |
| Logboekregistratie en audit van het controle- en beheervlak| Ja | [Azure Monitor-activiteitslogboeken](../azure-monitor/platform/platform-logs-overview.md) | |
| Logboekregistratie en -audit van gegevensvliegtuigen| Ja | [Diagnostische logboeken van Azure Monitor](../azure-monitor/platform/platform-logs-overview.md) en (optioneel) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  | |


## <a name="identity"></a>Identiteit

| Beveiligingscontrole | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Authentication| Ja | |  |
| Autorisatie| Ja | |  |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligingscontrole | Ja/Nee | Opmerkingen | Documentatie |
|---|---|--|--|
| Server-side encryptie in rust: door Microsoft beheerde sleutels | Ja | Gevoelige gegevens zoals certificaten, sleutels en waarden met geheime naam worden versleuteld met servicebeheerde, per service-instantiesleutels. |  |
| Server-side encryptie in rust: door de klant beheerde sleutels (BYOK) | Nee | Alle versleutelingssleutels zijn per service-instantie en worden servicebeheerd. |  |
| Versleuteling op kolomniveau (Azure Data Services)| N.v.t. | |  |
| Versleuteling tijdens het transport (zoals ExpressRoute-versleuteling, vnet-versleuteling en VNet-VNet-versleuteling)| Ja | [Express Route-](../expressroute/index.yml) en VNet-versleuteling wordt geleverd door [Azure-netwerken.](../virtual-network/index.yml) |  |
| API-aanroepen versleuteld| Ja | Management plane calls worden gevoerd via [Azure Resource Manager](../azure-resource-manager/index.yml) via TLS. Een geldig JSON-webtoken (JWT) is vereist.  Gegevensvliegtuigoproepen kunnen worden beveiligd met TLS en een van de ondersteunde verificatiemechanismen (bijvoorbeeld clientcertificaat of JWT). |   |
 |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligingscontrole | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Ondersteuning voor configuratiebeheer (versiebeheer van configuratie, enz.)| Ja | De [Azure API Management DevOps Resource Kit gebruiken](https://aka.ms/apimdevops) |  |

## <a name="vulnerability-scans-false-positives"></a>Kwetsbaarheid scant false positives

In deze sectie worden veelvoorkomende kwetsbaarheden die geen invloed hebben op Azure API Management.

| Beveiligingsprobleem               | Beschrijving                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed is kwetsbaarheid in de implementatie van de TLS SessionTicket extensie gevonden in sommige F5-producten. Het maakt de lekkage ("bloeden") van maximaal 31 bytes aan gegevens uit niet-geïninitialiseerd geheugen. Dit wordt veroorzaakt door de TLS-stack die een sessie-id opvulling, doorgegeven van de client, met gegevens om het 32 bits lang te maken. |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligingsbesturingselementen voor Azure-services](../security/fundamentals/security-controls.md).