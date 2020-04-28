---
title: Beveiligings controles voor Azure Service Bus berichten
description: Een controle lijst met beveiligings controles voor het evalueren van Azure Service Bus berichten
services: service-bus-messaging
ms.service: service-bus-messaging
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: af119ef026b70fcb4a56b4f823d20c0e9eddddc8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75903252"
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

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Ondersteund via [Azure monitor en waarschuwingen](service-bus-metrics-azure-monitor.md). |  |
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Bewerkings logboeken zijn beschikbaar.  | [Diagnostische logboeken Service Bus](service-bus-diagnostic-logs.md) |
| Logboek registratie en controle van het gegevens vlak| Nee |  |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Verificatie| Ja | Beheerd via [Azure Active Directory Managed Service Identity](service-bus-managed-service-identity.md).| [Service Bus verificatie en autorisatie](service-bus-authentication-and-authorization.md). |
| Autorisatie| Ja | Ondersteunt verificatie via [RBAC](authenticate-application.md) en SAS-token. | [Service Bus verificatie en autorisatie](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligings beheer | Ja/Nee | Opmerkingen | Documentatie |
|---|---|--|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels |  Ja voor versleuteling aan de server zijde is standaard ingesteld op rest. |  |  |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Ja. | Een door de klant beheerde sleutel in azure-hoofd kluis kan worden gebruikt om de gegevens op de Service Bus naam ruimte op rest te versleutelen. | [Door de klant beheerde sleutels configureren voor het versleutelen van Azure Service Bus gegevens op rest door gebruik te maken van de Azure Portal](configure-customer-managed-key.md)  |
| Versleuteling op kolom niveau (Azure Data Services)| N.v.t. | |   |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Ja | Ondersteunt het standaard HTTPS/TLS-mechanisme. |   |
| Versleutelde API-aanroepen| Ja | API-aanroepen worden gedaan via [Azure Resource Manager](../azure-resource-manager/index.yml) en HTTPS. |   |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja | Ondersteunt versie beheer van de resource provider via de [Azure Resource Manager-API](/rest/api/resources/).|   |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).
