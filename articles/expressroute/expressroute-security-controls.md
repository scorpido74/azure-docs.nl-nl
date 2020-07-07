---
title: 'Azure-ExpressRoute: beveiligings controles'
description: Een controle lijst met beveiligings controles voor het evalueren van Azure ExpressRoute
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: a288b44c07bc2df8529f07264dcee648f3af379a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74079931"
---
# <a name="security-controls-for-azure-expressroute"></a>Beveiligings controles voor Azure ExpressRoute

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Notities |
|---|---|--|
| Ondersteuning voor service-eind punten| N.v.t. |  |
| Ondersteuning voor VNet-injectie| N.v.t. | |
| Ondersteuning voor netwerk isolatie en firewalling| Yes | Elke klant is opgenomen in een eigen routerings domein en is getunneld naar het eigen VNet |
| Ondersteuning voor geforceerde tunneling| N.v.t. | Via Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Yes | Zie [ExpressRoute bewaking, metrische gegevens en waarschuwingen](expressroute-monitoring-metrics-alerts.md).|
| Logboek registratie en controle op het vlak van controle en beheer| Yes |  |
| Logboek registratie en controle van het gegevens vlak| No |   |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Verificatie| Yes | Service account voor de gateway voor micro soft (GWM) (controller); Just-in-time-toegang voor dev en OP. |
| Autorisatie|  Yes |Service account voor de gateway voor micro soft (GWM) (controller); Just-in-time-toegang voor dev en OP. |

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Notities |
|---|---|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels |  N.v.t. | ExpressRoute slaat geen klant gegevens op. |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | N.v.t. |  |
| Versleuteling op kolom niveau (Azure Data Services)| N.v.t. | |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| No | |
| Versleutelde API-aanroepen| Yes | Via [Azure Resource Manager](../azure-resource-manager/index.yml) en HTTPS. |


## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Yes | Via de netwerk resource provider (NRP). |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).