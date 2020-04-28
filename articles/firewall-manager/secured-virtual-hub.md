---
title: Wat is een beveiligde virtuele hub?
description: Meer informatie over beveiligde virtuele hubs
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: d93c3b47c518962bf45762b846eaf53e490ae8f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73518435"
---
# <a name="what-is-a-secured-virtual-hub"></a>Wat is een beveiligde virtuele hub?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Een virtuele hub is een virtueel netwerk dat door micro soft wordt beheerd en waarmee u verbinding kunt maken vanuit andere bronnen. Wanneer een virtuele hub wordt gemaakt op basis van een virtueel WAN in de Azure Portal, worden er een virtuele-hub VNet en gateways (optioneel) gemaakt als de bijbehorende onderdelen.

Een *beveiligde* virtuele hub is een [virtuele WAN-hub van Azure](../virtual-wan/virtual-wan-about.md#resources) met bijbehorende beveiligings-en routerings beleidsregels die door Azure firewall Manager zijn geconfigureerd. Gebruik beveiligde virtuele hubs om eenvoudig hub-en-spoke en transitieve architecturen te maken met systeem eigen beveiligings Services voor verkeers beheer en-beveiliging. 

U kunt een beveiligde virtuele hub gebruiken als een beheerd centraal VNet zonder on-premises connectiviteit. Het vervangt het centrale VNet dat eerder is vereist voor een Azure Firewall-implementatie. Omdat de beveiligde virtuele hub geautomatiseerde route ring biedt, is het niet nodig om uw eigen Udr's (door de gebruiker gedefinieerde routes) te configureren om verkeer via uw firewall te routeren.

Het is ook mogelijk om beveiligde virtuele hubs te gebruiken als onderdeel van een volledige virtuele WAN-architectuur. Deze architectuur biedt beveiligde, geoptimaliseerde en geautomatiseerde filiaal connectiviteit met en via Azure. U kunt de Services kiezen om uw netwerk verkeer te beveiligen en te beheren, met inbegrip van Azure Firewall en andere SECaaS-providers (Security as a Service) van derden.

## <a name="create-a-secured-virtual-hub"></a>Een beveiligde virtuele hub maken

Met firewall manager in de Azure Portal kunt u een nieuwe beveiligde virtuele hub maken of een bestaande virtuele hub die u eerder hebt gemaakt met behulp van Azure Virtual WAN converteren.

## <a name="next-steps"></a>Volgende stappen

Zie [zelf studie: uw Cloud netwerk beveiligen met Azure firewall Manager met behulp van de Azure Portal](secure-cloud-network.md)om een beveiligde virtuele hub te maken en deze te gebruiken om een hub-en spoke-netwerk te beveiligen en te beheren.