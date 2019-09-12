---
title: Beveiligings controles voor Azure Service Bus berichten
description: Een controle lijst met beveiligings controles voor het evalueren van Azure Service Bus berichten
services: service-bus-messaging
ms.service: service-bus-messaging
author: axisc
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: aschhab
ms.openlocfilehash: ac931ac7cf6c8609019b120388e1b6ca836f72b8
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886369"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Beveiligings controles voor Azure Service Bus berichten

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure Service Bus berichten.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| Ja (alleen Premium-laag) | VNet-service-eind punten worden alleen ondersteund voor [Service Bus-laag Premium](service-bus-premium-messaging.md) . |
| Ondersteuning voor VNet-injectie| Nee | |
| Ondersteuning voor netwerk isolatie en firewalling| Ja (alleen Premium-laag) |  |
| Ondersteuning voor geforceerde tunneling| Nee |  |

## <a name="monitoring--logging"></a>& Logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Ondersteund via [Azure monitor en waarschuwingen](service-bus-metrics-azure-monitor.md). |
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Er zijn bewerkings logboeken beschikbaar. Zie [Service Bus Diagnostische logboeken](service-bus-diagnostic-logs.md).  |
| Logboek registratie en controle van het gegevens vlak| Nee |  |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja | Beheerd via [Azure Active Directory Managed Service Identity](service-bus-managed-service-identity.md); Zie [Service Bus verificatie en autorisatie](service-bus-authentication-and-authorization.md).|
| Authorization| Ja | Ondersteunt verificatie via [RBAC](authenticate-application.md) en SAS-token; Zie [Service Bus verificatie en autorisatie](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling aan server zijde bij rest: Door micro soft beheerde sleutels |  Ja voor versleuteling aan de server zijde is standaard ingesteld op rest. | Door de klant beheerde sleutels en BYOK worden nog niet ondersteund. Versleuteling aan client zijde is de verantwoordelijkheid van de client |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Nee |   |
| Versleuteling op kolom niveau (Azure Data Services)| N/A | |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Ja | Ondersteunt het standaard HTTPS/TLS-mechanisme. |
| Versleutelde API-aanroepen| Ja | API-aanroepen worden gedaan via [Azure Resource Manager](../azure-resource-manager/index.yml) en HTTPS. |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja | Ondersteunt versie beheer van de resource provider via de [Azure Resource Manager-API](/rest/api/resources/).|

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).