---
title: Beveiligings controles voor Azure Relay
description: In deze artikelen vindt u een controle lijst met ingebouwde beveiligings controles voor het evalueren van Azure Relay.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 64abee031bb20e2bdb10bf1cc3cd77e135713550
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85316615"
---
# <a name="security-controls-for-azure-relay"></a>Beveiligings controles voor Azure Relay

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Notities | Documentatie |
|---|---|--|--|
| Ondersteuning voor service-eind punten| No |  |   |
| Ondersteuning voor netwerk isolatie en firewalling| No |  |   |
| Ondersteuning voor geforceerde tunneling| N.v.t. | Relay is de TLS-tunnel  |   |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Notities| Documentatie |
|---|---|--|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Yes | |   |
| Logboek registratie en controle op het vlak van controle en beheer| Yes | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Logboek registratie en controle van het gegevens vlak| Yes | Geslaagde/mislukte verbindingen en Logboeken.  |   |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Notities| Documentatie |
|---|---|--|--|
| Verificatie| Yes | Via SAS. | [Verificatie en autorisatie Azure Relay](relay-authentication-and-authorization.md) |
| Autorisatie|  Yes | Via SAS. | [Verificatie en autorisatie Azure Relay](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligings beheer | Ja/Nee | Notities | Documentatie |
|---|---|--|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels |  N.v.t. | Relay is een WebSocket en er worden geen gegevens bewaard. |   |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | No | Maakt gebruik van alleen micro soft TLS-certificaten.  |   |
| Versleuteling op kolom niveau (Azure Data Services)| N.v.t. | |   |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Yes | Voor service is TLS vereist. |   |
| Versleutelde API-aanroepen| Yes | HTTPS. |


## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Notities| Documentatie |
|---|---|--|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Yes | Via [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).