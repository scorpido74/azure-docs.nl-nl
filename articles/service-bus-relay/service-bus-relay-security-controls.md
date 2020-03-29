---
title: Beveiligingsbesturingselementen voor Azure Service Bus Relay
description: In dit artikel vindt u een checklist met ingebouwde beveiligingsbesturingselementen voor de evaluatie van Azure Service Bus Relay.
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 28d3ba14aa7769ac4f3fc22bd2b5bd7acd30557c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514014"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Beveiligingsbesturingselementen voor Azure Service Bus Relay

In dit artikel worden de beveiligingsbesturingselementen die zijn ingebouwd in Azure Service Bus Relay document.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligingscontrole | Ja/Nee | Opmerkingen | Documentatie |
|---|---|--|--|
| Ondersteuning voor serviceeindpunten| Nee |  |   |
| Ondersteuning voor netwerkisolatie en firewalling| Nee |  |   |
| Ondersteuning voor gedwongen tunneling| N.v.t. | Relais is de TLS-tunnel  |   |

## <a name="monitoring--logging"></a>Controle & logboekregistratie

| Beveiligingscontrole | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Azure-bewakingsondersteuning (logboekanalyses, app-inzichten, enz.)| Ja | |   |
| Logboekregistratie en audit van het controle- en beheervlak| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Logboekregistratie en -audit van gegevensvliegtuigen| Ja | Verbindingssucces / mislukking en fouten en geregistreerd.  |   |

## <a name="identity"></a>Identiteit

| Beveiligingscontrole | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Authentication| Ja | Via SAS. | [Azure Relay-verificatie en -autorisatie](relay-authentication-and-authorization.md) |
| Autorisatie|  Ja | Via SAS. | [Azure Relay-verificatie en -autorisatie](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligingscontrole | Ja/Nee | Opmerkingen | Documentatie |
|---|---|--|--|
| Server-side encryptie in rust: door Microsoft beheerde sleutels |  N.v.t. | Relay is een websocket en houdt geen gegevens voort. |   |
| Server-side encryptie in rust: door de klant beheerde sleutels (BYOK) | Nee | Maakt alleen gebruik van Microsoft TLS-certs.  |   |
| Versleuteling op kolomniveau (Azure Data Services)| N.v.t. | |   |
| Versleuteling tijdens het transport (zoals ExpressRoute-versleuteling, vnet-versleuteling en VNet-VNet-versleuteling)| Ja | Service vereist TLS. |   |
| API-aanroepen versleuteld| Ja | HTTPS. |


## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligingscontrole | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Ondersteuning voor configuratiebeheer (versiebeheer van configuratie, enz.)| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligingsbesturingselementen voor Azure-services](../security/fundamentals/security-controls.md).