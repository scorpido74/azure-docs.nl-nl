---
title: Beveiligings controles voor Azure Service Bus relay
description: Een controle lijst met beveiligings controles voor het evalueren van Azure Service Bus relay
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a131313f6c270debd9509a934fbf3ce74918ed42
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886431"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Beveiligings controles voor Azure Service Bus relay

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure Service Bus relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| Nee |  |
| Ondersteuning voor netwerk isolatie en firewalling| Nee |  |
| Ondersteuning voor geforceerde tunneling| N/A | Relay is de TLS-tunnel  |

## <a name="monitoring--logging"></a>& Logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | |
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |
| Logboek registratie en controle van het gegevens vlak| Ja | Geslaagde/mislukte verbindingen en Logboeken.  |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja | Via SAS. |
| Authorization|  Ja | Via SAS. |

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling aan server zijde bij rest: Door micro soft beheerde sleutels |  N/A | Relay is een WebSocket en er worden geen gegevens bewaard. |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Nee | Maakt gebruik van alleen micro soft TLS-certificaten.  |
| Versleuteling op kolom niveau (Azure Data Services)| N/A | |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Ja | Voor service is TLS vereist. |
| Versleutelde API-aanroepen| Ja | HTTPS. |


## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml).|

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).