---
title: Beveiligingsbesturingselementen voor Azure VPN-gateway
description: Een checklist met beveiligingscontroles voor de evaluatie van Azure VPN Gateway
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: cdf616b29a93e786ef26af83b5d3b3541f94d67c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972284"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Beveiligingsbesturingselementen voor Azure VPN-gateway

In dit artikel worden de beveiligingsbesturingselementen die zijn ingebouwd in Azure VPN Gateway, document.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligingscontrole | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor serviceeindpunten| N.v.t. | |
| Ondersteuning voor VNet-injectie| N.v.t. | |
| Ondersteuning voor Netwerkisolatie en Firewalling| Ja | VPN-gateways zijn speciale VM-exemplaren voor elke klant virtueel netwerk  |
| Ondersteuning voor gedwongen tunneling| Ja |  |

## <a name="monitoring--logging"></a>Controle & logboekregistratie

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Azure-bewakingsondersteuning (logboekanalyses, app-inzichten, enz.)| Ja | Zie [Azure Monitor Diagnostics Logs/alert](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & Azure Monitor[Metrics/alert](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |
| Logboekregistratie en audit van het controle- en beheervlak| Ja | Azure Resource Manager-activiteitenlogboek. |
| Logboekregistratie en -audit van gegevensvliegtuigen | Ja | [Diagnostische logboeken voor Azure Monitor](../azure-resource-manager/management/view-activity-logs.md) voor logboekregistratie en controle van VPN-connectiviteit. |

## <a name="identity"></a>Identiteit

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) voor het beheren van de service en het configureren van de Azure VPN-gateway. |
| Autorisatie| Ja | Ondersteuningsautorisatie via [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligingscontrole | Ja/Nee | Opmerkingen |
|---|---|--|
| Server-side encryptie in rust: door Microsoft beheerde sleutels | N.v.t. | VPN-gateway doorvoer klantgegevens, slaat geen klantgegevens op |
| Versleuteling tijdens het transport (zoals ExpressRoute-versleuteling, vnet-versleuteling en VNet-VNet-versleuteling)| Ja | VPN-gateway versleutelt klantpakketten tussen Azure VPN-gateways en on-premises VPN-apparaten (S2S) of VPN-clients (P2S). VPN-gateways ondersteunen ook VNet-to-VNet-versleuteling. |
| Server-side encryptie in rust: door de klant beheerde sleutels (BYOK) | Nee | Door de klant opgegeven vooraf gedeelde sleutels worden in rust versleuteld; maar nog niet geïntegreerd met CMK. |
| Versleuteling op kolomniveau (Azure Data Services)| N.v.t. | |
| API-aanroepen versleuteld| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml) en HTTPS  |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratiebeheer (versiebeheer van configuratie, enz.)| Ja | Voor beheerbewerkingen kan de status van een Azure VPN-gatewayconfiguratie worden geëxporteerd als een Azure Resource Manager-sjabloon en in de loop van de tijd worden geversioneerd. |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligingsbesturingselementen voor Azure-services](../security/fundamentals/security-controls.md).
