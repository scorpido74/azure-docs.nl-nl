---
title: Beveiligings controles voor Azure Service Bus relay
description: Een controle lijst met beveiligings controles voor het evalueren van Azure Service Bus relay
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 7f4e2a31673905a7e28d1dbb5520650aefc6f368
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219985"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Beveiligings controles voor Azure Service Bus relay

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure Service Bus relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen | Documentatie |
|---|---|--|--|
| Ondersteuning voor service-eind punten| Nee |  |   |
| Ondersteuning voor netwerk isolatie en firewalling| Nee |  |   |
| Ondersteuning voor geforceerde tunneling| N/A | Relay is de TLS-tunnel  |   |

## <a name="monitoring--logging"></a>& Logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | |   |
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Logboek registratie en controle van het gegevens vlak| Ja | Geslaagde/mislukte verbindingen en Logboeken.  |   |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Authentication| Ja | Via SAS. | [Verificatie en autorisatie Azure Relay](relay-authentication-and-authorization.md) |
| Authorization|  Ja | Via SAS. | [Verificatie en autorisatie Azure Relay](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Opmerkingen | Documentatie |
|---|---|--|--|
| Versleuteling aan server zijde bij rest: Door micro soft beheerde sleutels |  N/A | Relay is een WebSocket en er worden geen gegevens bewaard. |   |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Nee | Maakt gebruik van alleen micro soft TLS-certificaten.  |   |
| Versleuteling op kolom niveau (Azure Data Services)| N/A | |   |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Ja | Voor service is TLS vereist. |   |
| Versleutelde API-aanroepen| Ja | HTTPS. |


## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).