---
title: Beveiligings controles voor Azure Service Bus berichten
description: Een controle lijst met beveiligings controles voor het evalueren van Azure Service Bus berichten
services: service-bus-messaging
ms.service: service-bus-messaging
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: d2b92759384a9a0b63d784a8cb1afb3d18d55aeb
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219326"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Beveiligings controles voor Azure Service Bus berichten

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure Service Bus berichten.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen | Documentatie |
|---|---|--|--|
| Ondersteuning voor service-eind punten| Ja (alleen Premium-laag) | VNet-service-eind punten worden alleen ondersteund voor [Service Bus-laag Premium](service-bus-premium-messaging.md) . |  |
| Ondersteuning voor VNet-injectie| Nee | |  |
| Ondersteuning voor netwerk isolatie en firewalling| Ja (alleen Premium-laag) |  |  |
| Ondersteuning voor geforceerde tunneling| Nee |  |  |

## <a name="monitoring--logging"></a>& Logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Ondersteund via [Azure monitor en waarschuwingen](service-bus-metrics-azure-monitor.md). |  |
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Bewerkings logboeken zijn beschikbaar.  | [Diagnostische logboeken Service Bus](service-bus-diagnostic-logs.md) |
| Logboek registratie en controle van het gegevens vlak| Nee |  |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Authentication| Ja | Beheerd via [Azure Active Directory Managed Service Identity](service-bus-managed-service-identity.md).| [Service Bus verificatie en autorisatie](service-bus-authentication-and-authorization.md). |
| Authorization| Ja | Ondersteunt verificatie via [RBAC](authenticate-application.md) en SAS-token. | [Service Bus verificatie en autorisatie](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Opmerkingen | Documentatie |
|---|---|--|--|
| Versleuteling aan server zijde bij rest: Door micro soft beheerde sleutels |  Ja voor versleuteling aan de server zijde is standaard ingesteld op rest. | Door de klant beheerde sleutels en BYOK worden nog niet ondersteund. Versleuteling aan client zijde is de verantwoordelijkheid van de client |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Nee |   |   |
| Versleuteling op kolom niveau (Azure Data Services)| N/A | |   |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Ja | Ondersteunt het standaard HTTPS/TLS-mechanisme. |   |
| Versleutelde API-aanroepen| Ja | API-aanroepen worden gedaan via [Azure Resource Manager](../azure-resource-manager/index.yml) en HTTPS. |   |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja | Ondersteunt versie beheer van de resource provider via de [Azure Resource Manager-API](/rest/api/resources/).|   |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).