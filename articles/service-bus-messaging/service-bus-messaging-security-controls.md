---
title: Beveiligingsbesturingselementen voor Azure Service Bus Messaging
description: Een checklist met beveiligingscontroles voor het evalueren van Azure Service Bus Messaging
services: service-bus-messaging
ms.service: service-bus-messaging
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: af119ef026b70fcb4a56b4f823d20c0e9eddddc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75903252"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Beveiligingsbesturingselementen voor Azure Service Bus Messaging

In dit artikel worden de beveiligingsbesturingselementen die zijn ingebouwd in Azure Service Bus Messaging document.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligingscontrole | Ja/Nee | Opmerkingen | Documentatie |
|---|---|--|--|
| Ondersteuning voor serviceeindpunten| Ja (alleen premiumlaag) | VNet-serviceeindpunten worden alleen ondersteund voor [servicebuspremiumniveaus.](service-bus-premium-messaging.md) |  |
| Ondersteuning voor VNet-injectie| Nee | |  |
| Ondersteuning voor netwerkisolatie en firewalling| Ja (alleen premiumlaag) |  |  |
| Ondersteuning voor gedwongen tunneling| Nee |  |  |

## <a name="monitoring--logging"></a>Controle & logboekregistratie

| Beveiligingscontrole | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Azure-bewakingsondersteuning (logboekanalyses, app-inzichten, enz.)| Ja | Ondersteund via [Azure Monitor en Alerts](service-bus-metrics-azure-monitor.md). |  |
| Logboekregistratie en audit van het controle- en beheervlak| Ja | Operations logs zijn beschikbaar.  | [Diagnostische logboeken van servicebus](service-bus-diagnostic-logs.md) |
| Logboekregistratie en -audit van gegevensvliegtuigen| Nee |  |

## <a name="identity"></a>Identiteit

| Beveiligingscontrole | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Authentication| Ja | Beheerd via [Azure Active Directory Managed Service Identity](service-bus-managed-service-identity.md).| [Verificatie en autorisatie van servicebus](service-bus-authentication-and-authorization.md). |
| Autorisatie| Ja | Ondersteunt autorisatie via [RBAC](authenticate-application.md) en SAS-token. | [Verificatie en autorisatie van servicebus](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligingscontrole | Ja/Nee | Opmerkingen | Documentatie |
|---|---|--|--|
| Server-side encryptie in rust: door Microsoft beheerde sleutels |  Ja voor standaard server-side encryptie-at-rest. |  |  |
| Server-side encryptie in rust: door de klant beheerde sleutels (BYOK) | Ja. | Een door de klant beheerde sleutel in Azure KeyVault kan worden gebruikt om de gegevens in de servicebusnaamruimte in rust te versleutelen. | [Door de klant beheerde sleutels configureren voor het versleutelen van Azure Service Bus-gegevens in rust met behulp van de Azure-portal](configure-customer-managed-key.md)  |
| Versleuteling op kolomniveau (Azure Data Services)| N.v.t. | |   |
| Versleuteling tijdens het transport (zoals ExpressRoute-versleuteling, vnet-versleuteling en VNet-VNet-versleuteling)| Ja | Ondersteunt standaard HTTPS/TLS-mechanisme. |   |
| API-aanroepen versleuteld| Ja | API-aanroepen worden uitgevoerd via [Azure Resource Manager](../azure-resource-manager/index.yml) en HTTPS. |   |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligingscontrole | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Ondersteuning voor configuratiebeheer (versiebeheer van configuratie, enz.)| Ja | Ondersteunt versieversies van resourcesprovider via de [Azure Resource Manager API](/rest/api/resources/).|   |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligingsbesturingselementen voor Azure-services](../security/fundamentals/security-controls.md).
