---
title: 'Azure ExpressRoute: beveiligingsbesturingselementen'
description: Een checklist met beveiligingscontroles voor de evaluatie van Azure ExpressRoute
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: a288b44c07bc2df8529f07264dcee648f3af379a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74079931"
---
# <a name="security-controls-for-azure-expressroute"></a>Beveiligingsbesturingselementen voor Azure ExpressRoute

In dit artikel worden de beveiligingsbesturingselementen die zijn ingebouwd in Azure ExpressRoute documenteerd.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligingscontrole | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor serviceeindpunten| N.v.t. |  |
| Ondersteuning voor VNet-injectie| N.v.t. | |
| Ondersteuning voor netwerkisolatie en firewalling| Ja | Elke klant is opgenomen in zijn eigen routing domein en getunneld naar zijn eigen VNet |
| Ondersteuning voor gedwongen tunneling| N.v.t. | Via Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>Controle & logboekregistratie

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Azure-bewakingsondersteuning (logboekanalyses, app-inzichten, enz.)| Ja | Zie [ExpressRoute-bewaking, statistieken en waarschuwingen](expressroute-monitoring-metrics-alerts.md).|
| Logboekregistratie en audit van het controle- en beheervlak| Ja |  |
| Logboekregistratie en -audit van gegevensvliegtuigen| Nee |   |

## <a name="identity"></a>Identiteit

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja | Serviceaccount voor Gateway for Microsoft (GWM) (controller); Just in Time (JIT) toegang voor Dev en OP. |
| Autorisatie|  Ja |Serviceaccount voor Gateway for Microsoft (GWM) (controller); Just in Time (JIT) toegang voor Dev en OP. |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligingscontrole | Ja/Nee | Opmerkingen |
|---|---|--|
| Server-side encryptie in rust: door Microsoft beheerde sleutels |  N.v.t. | ExpressRoute slaat geen klantgegevens op. |
| Server-side encryptie in rust: door de klant beheerde sleutels (BYOK) | N.v.t. |  |
| Versleuteling op kolomniveau (Azure Data Services)| N.v.t. | |
| Versleuteling tijdens het transport (zoals ExpressRoute-versleuteling, vnet-versleuteling en VNet-VNet-versleuteling)| Nee | |
| API-aanroepen versleuteld| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml) en HTTPS. |


## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratiebeheer (versiebeheer van configuratie, enz.)| Ja | Via de Network Resource Provider (NRP). |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligingsbesturingselementen voor Azure-services](../security/fundamentals/security-controls.md).