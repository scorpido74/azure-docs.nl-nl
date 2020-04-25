---
title: Beveiligings controles voor Azure VPN Gateway
description: Een controle lijst met beveiligings controles voor het evalueren van Azure VPN Gateway
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 6fc5b4c901254decdb2d34281a10ababd4d79d45
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82127857"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Beveiligings controles voor Azure VPN Gateway

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in azure VPN Gateway.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| N.v.t. | |
| Ondersteuning voor VNet-injectie| N.v.t. | |
| Ondersteuning voor netwerk isolatie en firewalling| Ja | VPN-gateways zijn toegewezen VM-exemplaren voor elke klant Virtual Network  |
| Ondersteuning voor geforceerde tunneling| Ja |  |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Zie [Azure monitor waarschuwing voor logboek registratie](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure monitor metrische gegevens](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Azure Resource Manager activiteiten logboek. |
| Logboek registratie en controle van het gegevens vlak | Ja | [Azure monitor activiteiten logboeken](../azure-resource-manager/management/view-activity-logs.md) voor logboek registratie en controle van de VPN-verbinding. |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Verificatie| Ja | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) voor het beheren van de service en het configureren van de Azure VPN-gateway. |
| Autorisatie| Ja | Ondersteuning voor autorisatie via [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | N.v.t. | Klant gegevens van VPN-gateway-door voer worden niet opgeslagen |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Ja | VPN-gateway versleutelt klant pakketten tussen Azure VPN-gateways en klant-on-premises VPN-apparaten (S2S) of VPN-clients (P2S). VPN-gateways ondersteunen ook VNet-naar-VNet-versleuteling. |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Nee | Door de klant opgegeven vooraf gedeelde sleutels worden op rest versleuteld. maar is nog niet geïntegreerd met CMK. |
| Versleuteling op kolom niveau (Azure Data Services)| N.v.t. | |
| Versleutelde API-aanroepen| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml) en https  |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja | Voor beheer bewerkingen kan de status van een configuratie van een Azure VPN-gateway worden geëxporteerd als een Azure Resource Manager sjabloon en in de loop van de tijd. |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).
