---
title: 'Azure-ExpressRoute: beveiligings controles'
description: Meer informatie over beveiligings controles in azure ExpressRoute, die kenmerken of functies zijn die helpen beveiligings problemen te voor komen, te detecteren en op te lossen.
services: expressroute
ms.service: expressroute
author: duongau
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: duau
ms.openlocfilehash: 24057de44f3d28df96bcb93e89af9c3afa6fa3c6
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89394904"
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

## <a name="data-protection"></a>Gegevensbeveiliging

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