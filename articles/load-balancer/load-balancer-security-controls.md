---
title: Beveiligings controles voor Azure Load Balancer
description: Een controle lijst met beveiligings controles voor het evalueren van Load Balancer
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: e0be6635a0916183e1dfe776bef4c547578383dc
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886669"
---
# <a name="security-controls-for-azure-load-balancer"></a>Beveiligings controles voor Azure Load Balancer

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| N/A | |
| Ondersteuning voor VNet-injectie| N/A | |
| Ondersteuning voor netwerk isolatie en firewalling| N/A |  |
| Ondersteuning voor geforceerde tunneling| N/A | |

## <a name="monitoring--logging"></a>& Logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Zie [Azure monitor-logboeken voor open bare basis Load Balancer](load-balancer-monitor-log.md). |
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Zie [Azure monitor-logboeken voor open bare basis Load Balancer](load-balancer-monitor-log.md). |
| Logboek registratie en controle van het gegevens vlak | N/A |  |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| N/A |  |
| Authorization| N/A |  |

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling aan server zijde bij rest: Door micro soft beheerde sleutels | N/A | |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| N/A | |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | N/A | |
| Versleuteling op kolom niveau (Azure Data Services)| N/A | |
| Versleutelde API-aanroepen| Ja | Via de [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| N/A |  | 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).