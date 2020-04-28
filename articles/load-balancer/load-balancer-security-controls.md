---
title: Beveiligings controles voor Azure Load Balancer
description: Een controle lijst met beveiligings controles voor het evalueren van Load Balancer
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74214901"
---
# <a name="security-controls-for-azure-load-balancer"></a>Beveiligings controles voor Azure Load Balancer

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| N.v.t. | |
| Ondersteuning voor VNet-injectie| N.v.t. | |
| Ondersteuning voor netwerk isolatie en firewalling| N.v.t. |  |
| Ondersteuning voor geforceerde tunneling| N.v.t. | |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Zie [Azure monitor-logboeken voor open bare basis Load Balancer](load-balancer-monitor-log.md). |
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Zie [Azure monitor-logboeken voor open bare basis Load Balancer](load-balancer-monitor-log.md). |
| Logboek registratie en controle van het gegevens vlak | N.v.t. |  |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Verificatie| N.v.t. |  |
| Autorisatie| N.v.t. |  |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | N.v.t. | |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| N.v.t. | |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | N.v.t. | |
| Versleuteling op kolom niveau (Azure Data Services)| N.v.t. | |
| Versleutelde API-aanroepen| Ja | Via de [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| N.v.t. |  | 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).