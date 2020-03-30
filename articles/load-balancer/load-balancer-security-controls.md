---
title: Beveiligingsbesturingselementen voor Azure Load Balancer
description: Een checklist met beveiligingscontroles voor het evalueren van Load Balancer
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74214901"
---
# <a name="security-controls-for-azure-load-balancer"></a>Beveiligingsbesturingselementen voor Azure Load Balancer

In dit artikel worden de beveiligingsbesturingselementen die zijn ingebouwd in Azure Load Balancer, documenteer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligingscontrole | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor serviceeindpunten| N.v.t. | |
| Ondersteuning voor VNet-injectie| N.v.t. | |
| Ondersteuning voor Netwerkisolatie en Firewalling| N.v.t. |  |
| Ondersteuning voor gedwongen tunneling| N.v.t. | |

## <a name="monitoring--logging"></a>Controle & logboekregistratie

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Azure-bewakingsondersteuning (logboekanalyses, app-inzichten, enz.)| Ja | Zie [Azure Monitor-logboeken voor openbare basislastbalansr](load-balancer-monitor-log.md). |
| Logboekregistratie en audit van het controle- en beheervlak| Ja | Zie [Azure Monitor-logboeken voor openbare basislastbalansr](load-balancer-monitor-log.md). |
| Logboekregistratie en -audit van gegevensvliegtuigen | N.v.t. |  |

## <a name="identity"></a>Identiteit

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| N.v.t. |  |
| Autorisatie| N.v.t. |  |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligingscontrole | Ja/Nee | Opmerkingen |
|---|---|--|
| Server-side encryptie in rust: door Microsoft beheerde sleutels | N.v.t. | |
| Versleuteling tijdens het transport (zoals ExpressRoute-versleuteling, vnet-versleuteling en VNet-VNet-versleuteling)| N.v.t. | |
| Server-side encryptie in rust: door de klant beheerde sleutels (BYOK) | N.v.t. | |
| Versleuteling op kolomniveau (Azure Data Services)| N.v.t. | |
| API-aanroepen versleuteld| Ja | Via Azure [Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratiebeheer (versiebeheer van configuratie, enz.)| N.v.t. |  | 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligingsbesturingselementen voor Azure-services](../security/fundamentals/security-controls.md).