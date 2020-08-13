---
title: 'Azure-ExpressRoute: beveiligings controles'
description: Meer informatie over beveiligings controles in azure ExpressRoute, die kenmerken of functies zijn die helpen beveiligings problemen te voor komen, te detecteren en op te lossen.
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 6a4589d1aa768548f2ae9ffa01f289c823c0b2e5
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192413"
---
# <a name="security-controls-for-azure-expressroute"></a>Beveiligings controles voor Azure ExpressRoute

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Notities |
|---|---|--|
| Ondersteuning voor service-eind punten| N.v.t. |  |
| Ondersteuning voor VNet-injectie| N.v.t. | |
| Ondersteuning voor netwerk isolatie en firewalling| Ja | Elke klant is opgenomen in een eigen routerings domein en is getunneld naar het eigen VNet |
| Ondersteuning voor geforceerde tunneling| N.v.t. | Via Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Zie [ExpressRoute bewaking, metrische gegevens en waarschuwingen](expressroute-monitoring-metrics-alerts.md).|
| Logboek registratie en controle op het vlak van controle en beheer| Ja |  |
| Logboek registratie en controle van het gegevens vlak| Nee |   |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Verificatie| Ja | Service account voor de gateway voor micro soft (GWM) (controller); Just-in-time-toegang voor dev en OP. |
| Autorisatie|  Ja |Service account voor de gateway voor micro soft (GWM) (controller); Just-in-time-toegang voor dev en OP. |

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Notities |
|---|---|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels |  N.v.t. | ExpressRoute slaat geen klant gegevens op. |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | N.v.t. |  |
| Versleuteling op kolom niveau (Azure Data Services)| N.v.t. | |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Nee | |
| Versleutelde API-aanroepen| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml) en HTTPS. |


## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja | Via de netwerk resource provider (NRP). |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).