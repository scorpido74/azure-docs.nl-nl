---
title: 'Scenario: Azure Firewall aangepaste route ring voor virtuele WAN'
titleSuffix: Azure Virtual WAN
description: Scenario's voor het direct routeren van verkeer tussen VNets, maar het gebruik van Azure Firewall voor VNet->Internet/Branch en branch naar VNet-verkeer stromen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: cdaa594a87d960688638591e6edd525aa3b048f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568841"
---
# <a name="scenario-azure-firewall---custom"></a>Scenario: Azure Firewall-aangepast

Wanneer u werkt met virtuele WAN-hub routering, zijn er heel veel beschik bare scenario's. In dit scenario is het doel om verkeer tussen VNets rechtstreeks te routeren, maar Azure Firewall voor VNet-naar-Internet/Branch-en vertakking-naar-VNet-verkeer. Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over route ring van virtuele hub.

## <a name="scenario-workflow"></a><a name="workflow"></a>Scenario werk stroom

In dit scenario wilt u verkeer routeren via de Azure Firewall voor VNet-naar-Internet-, VNet-naar-vertakking-of vertakkings-naar-VNet-verkeer, maar graag direct voor VNet-naar-VNet-verkeer. Als u Azure Firewall Manager hebt gebruikt, worden de route-instellingen automatisch ingevuld in de **standaard route tabel**. Privé verkeer is van toepassing op VNet en filialen, Internet verkeer is van toepassing op 0.0.0.0/0.

VPN-, ExpressRoute-en gebruikers-VPN-verbindingen worden gezamenlijk vertakkingen genoemd en aan dezelfde (standaard) route tabel gekoppeld. Alle VPN-, ExpressRoute-en gebruikers-VPN-verbindingen sturen routes door naar dezelfde set route tabellen. Als u dit scenario wilt configureren, voert u de volgende stappen uit:

1. Een aangepaste route tabel maken **RT_VNET**.
1. Maak een route om VNet-naar-Internet en VNet-naar-Branch: 0.0.0.0/0 te activeren met de volgende hop die naar Azure Firewall wijst. In het gedeelte doorgifte kunt u ervoor zorgen dat VNets zijn geselecteerd die ervoor zorgen dat er meer specifieke routes zijn, waardoor het VNet-naar-VNet direct-verkeers stroom wordt toegestaan.

   * In **Association:** Selecteer VNets die het doel van VNets bereikt volgens de routes van deze route tabel.
   * Bij **doorgifte:** Selecteer VNets. Dit betekent dat de VNets worden door gegeven aan deze route tabel. met andere woorden, meer specifieke routes worden door gegeven aan deze route tabel, waardoor direct verkeer tussen VNet en VNet wordt gegarandeerd.

1. Voeg een geaggregeerde statische route voor VNets toe aan de **standaard route tabel** om de vertakking-naar-VNet-stroom via de Azure firewall te activeren. 

   * Houd er rekening mee dat branches zijn gekoppeld aan en worden door gegeven aan de standaard route tabel.
   * Vertakkingen worden niet door gegeven aan RT_VNET route tabel. Dit zorgt voor de overdracht van het VNet-naar-vertakkings verkeer via de Azure Firewall.

Dit heeft tot gevolg dat de routerings configuratie wordt gewijzigd, zoals weer gegeven in **afbeelding 1**.

**Afbeelding 1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="Afbeelding 1":::

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
* Zie [virtuele hub-route ring configureren](how-to-virtual-hub-routing.md)voor meer informatie over het configureren van virtuele hub-route ring.
