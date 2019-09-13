---
title: Beveiligings controles voor Azure VPN Gateway
description: Een controle lijst met beveiligings controles voor het evalueren van Azure VPN Gateway
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 1babb892063da6d460ea2bc4c567da954731956f
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886790"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Beveiligings controles voor Azure VPN Gateway

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in azure VPN Gateway.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| N/A | |
| Ondersteuning voor VNet-injectie| N/A | |
| Ondersteuning voor netwerk isolatie en firewalling| Ja | VPN-gateways zijn toegewezen VM-exemplaren voor elke klant Virtual Network  |
| Ondersteuning voor geforceerde tunneling| Ja |  |

## <a name="monitoring--logging"></a>& Logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Zie [Azure monitor Diagnostische logboeken/waarschuwing](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure monitor metrische gegevens/waarschuwing](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Azure Resource Manager activiteiten logboek. |
| Logboek registratie en controle van het gegevens vlak | Ja | [Diagnostische logboeken Azure monitor](../azure-resource-manager/resource-group-audit.md) voor logboek registratie en controle van de VPN-verbinding. |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) voor het beheren van de service en het configureren van de Azure VPN-gateway. |
| Authorization| Ja | Ondersteuning voor autorisatie via [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling aan server zijde bij rest: Door micro soft beheerde sleutels | N/A | Klant gegevens van VPN-gateway-door voer worden niet opgeslagen |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Ja | VPN-gateway versleutelt klant pakketten tussen Azure VPN-gateways en klant-on-premises VPN-apparaten (S2S) of VPN-clients (P2S). VPN-gateways ondersteunen ook VNet-naar-VNet-versleuteling. |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Nee | Door de klant opgegeven vooraf gedeelde sleutels worden op rest versleuteld. maar is nog niet geïntegreerd met CMK. |
| Versleuteling op kolom niveau (Azure Data Services)| N/A | |
| Versleutelde API-aanroepen| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml) en https  |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja | Voor beheer bewerkingen kan de status van een configuratie van een Azure VPN-gateway worden geëxporteerd als een Azure Resource Manager sjabloon en in de loop van de tijd. | 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).