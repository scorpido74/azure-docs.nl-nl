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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73518435"
---
# <a name="what-is-a-secured-virtual-hub"></a>Wat is een beveiligde virtuele hub?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Een virtuele hub is een door Microsoft beheerd virtueel netwerk dat connectiviteit van andere bronnen mogelijk maakt. Wanneer een virtuele hub wordt gemaakt vanuit een virtueel WAN in de Azure-portal, worden een virtuele hub VNet en gateways (optioneel) gemaakt als componenten.

Een *beveiligde* virtuele hub is een [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) met bijbehorend beveiligings- en routeringsbeleid dat is geconfigureerd door Azure Firewall Manager. Gebruik beveiligde virtuele hubs om eenvoudig hub-and-spoke en transitieve architecturen te maken met native beveiligingsservices voor verkeersgovernance en -bescherming. 

U een beveiligde virtuele hub gebruiken als een beheerde centrale VNet zonder on-prem-connectiviteit. Het vervangt de centrale VNet die voorheen nodig was voor een Azure Firewall-implementatie. Aangezien de beveiligde virtuele hub geautomatiseerde routering biedt, is het niet nodig om uw eigen UDR's (door de gebruiker gedefinieerde routes) te configureren om verkeer door uw firewall te leiden.

Het is ook mogelijk om beveiligde virtuele hubs te gebruiken als onderdeel van een volledige Virtual WAN-architectuur. Deze architectuur biedt beveiligde, geoptimaliseerde en geautomatiseerde branch-connectiviteit naar en via Azure. U de services kiezen om uw netwerkverkeer te beschermen en te beheren, waaronder Azure Firewall en andere secaas-providers (Security as a Service) van derden.

## <a name="create-a-secured-virtual-hub"></a>Een beveiligde virtuele hub maken

Met Firewall Beheer in de Azure-portal u een nieuwe beveiligde virtuele hub maken of een bestaande virtuele hub converteren die u eerder hebt gemaakt met Azure Virtual WAN.

## <a name="next-steps"></a>Volgende stappen

Zie [Zelfstudie: Beveilig uw cloudnetwerk met Azure Firewall Manager met Azure Firewall Manager met Azure Firewall Manager.](secure-cloud-network.md)