---
title: Beveiligings controles voor Azure Event Hubs
description: Een controle lijst met beveiligings controles voor het evalueren van Azure Event Hubs
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 3f02315012840b20dc4aa8639ade954f23a5526e
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886687"
---
# <a name="security-controls-for-azure-event-hubs"></a>Beveiligings controles voor Azure Event Hubs

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in azure Event Hubs.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| Ja |  |
| Ondersteuning voor VNet-injectie| Nee | |
| Ondersteuning voor netwerk isolatie en firewalling| Ja |  |
| Ondersteuning voor geforceerde tunneling| Nee |  |

## <a name="monitoring--logging"></a>& Logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | |
| Logboek registratie en controle op het vlak van controle en beheer| Ja |  |
| Logboek registratie en controle van het gegevens vlak| Ja |   |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja | |
| Authorization|  Ja | |

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling aan server zijde bij rest: Door micro soft beheerde sleutels |  Ja | |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Nee |  |
| Versleuteling op kolom niveau (Azure Data Services)| N/A | |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Ja | |
| Versleutelde API-aanroepen| Ja |  |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja | |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).
