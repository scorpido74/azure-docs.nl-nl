---
title: Beveiligings controles voor Azure ExpressRoute
description: Een controle lijst met beveiligings controles voor het evalueren van Azure ExpressRoute
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ab73ba6dd4b78d3cd0be5f4c7f7a502e5c58e08
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886341"
---
# <a name="security-controls-for-azure-expressroute"></a>Beveiligings controles voor Azure ExpressRoute

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| N/A |  |
| Ondersteuning voor VNet-injectie| N/A | |
| Ondersteuning voor netwerk isolatie en firewalling| Ja | Elke klant is opgenomen in een eigen routerings domein en is getunneld naar het eigen VNet |
| Ondersteuning voor geforceerde tunneling| N/A | Via Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>& Logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Zie [ExpressRoute bewaking, metrische gegevens en waarschuwingen](expressroute-monitoring-metrics-alerts.md).|
| Logboek registratie en controle op het vlak van controle en beheer| Ja |  |
| Logboek registratie en controle van het gegevens vlak| Nee |   |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja | Service account voor de gateway voor micro soft (GWM) (controller); Just-in-time-toegang voor dev en OP. |
| Authorization|  Ja |Service account voor de gateway voor micro soft (GWM) (controller); Just-in-time-toegang voor dev en OP. |

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling aan server zijde bij rest: Door micro soft beheerde sleutels |  N/A | ExpressRoute slaat geen klant gegevens op. |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | N/A |  |
| Versleuteling op kolom niveau (Azure Data Services)| N/A | |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Nee | |
| Versleutelde API-aanroepen| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml) en HTTPS. |


## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja | Via de netwerk resource provider (NRP). |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).