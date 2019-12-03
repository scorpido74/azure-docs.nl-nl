---
title: Verbinding maken met een peer netwerk in Azure Lab Services | Microsoft Docs
description: Informatie over het verbinden van uw Lab-netwerk met een ander netwerk als een peer. Verbind uw on-premises school/universiteit-netwerk bijvoorbeeld met het virtuele netwerk van het lab in Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: d3f6acef7491a07f94eec0b2c3b2f3bcd9c01a33
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74701671"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Uw Lab-netwerk verbinden met een virtueel peer-netwerk in Azure Lab Services 
Dit artikel bevat informatie over het peeren van uw Labs-netwerk met een ander netwerk. 

## <a name="overview"></a>Overzicht
Met peering op virtueel netwerk kunt u naadloos verbinding maken met virtuele netwerken van Azure. Nadat de virtuele netwerken zijn gekoppeld via peering, worden ze voor verbindingsdoeleinden weergegeven als één netwerk. Het verkeer tussen virtuele machines in de gekoppelde virtuele netwerken wordt doorgestuurd via de micro soft backbone-infra structuur, vergelijkbaar met het verkeer dat wordt gerouteerd tussen virtuele machines in hetzelfde virtuele netwerk, via alleen particuliere IP-adressen. Zie [peering van virtuele netwerken](../../virtual-network/virtual-network-peering-overview.md)voor meer informatie.

Mogelijk moet u uw Lab-netwerk verbinden met een virtueel peer netwerk in sommige scenario's, waaronder de volgende:

- De virtuele machines in het Lab hebben software die verbinding maakt met on-premises licentie servers om een licentie aan te schaffen
- De virtuele machines in het lab moeten toegang hebben tot gegevens sets (of andere bestanden) op de netwerk shares van de University. 

Bepaalde on-premises netwerken zijn verbonden met Azure Virtual Network via [ExpressRoute](../../expressroute/expressroute-introduction.md) of [Virtual Network gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Deze services moeten buiten Azure Lab Services worden ingesteld. Zie [overzicht van ExpressRoute](../../expressroute/expressroute-introduction.md)voor meer informatie over het verbinden van een on-premises netwerk met Azure met behulp van ExpressRoute. Voor on-premises connectiviteit met een Virtual Network gateway, de gateway, het opgegeven virtuele netwerk en het lab-account moeten zich allemaal in dezelfde regio bevinden.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configureren op het moment dat het lab-account wordt gemaakt
Tijdens het maken van het nieuwe lab-account kunt u een bestaand virtueel netwerk kiezen dat wordt weer gegeven in de vervolg keuzelijst van het **virtuele peer netwerk** . Het geselecteerde virtuele netwerk is verbonden (gekoppeld aan) met de laboratoria die zijn gemaakt met het lab-account. Alle virtuele machines in Labs die zijn gemaakt na het maken van deze wijziging, hebben toegang tot de resources in het gekoppelde virtuele netwerk. 

![VNet naar peer selecteren](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Zie [een Lab-account instellen](tutorial-setup-lab-account.md) voor gedetailleerde stapsgewijze instructies voor het maken van een Lab-account


## <a name="configure-after-the-lab-is-created"></a>Configureren nadat het lab is gemaakt
Deze eigenschap kan worden ingeschakeld op het tabblad **configuratie** van de **test omgeving** van de pagina Lab-account als u geen peer netwerk hebt ingesteld op het moment dat het account wordt gemaakt. De wijziging van deze instelling is alleen van toepassing op de Labs die na de wijziging zijn gemaakt. Zoals u kunt zien in de afbeelding, kunt u het **virtuele peer netwerk** voor Labs in-of uitschakelen in het lab-account. 

![VNet-peering in-of uitschakelen nadat het lab is gemaakt](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Wanneer u een virtueel netwerk voor het veld **virtuele peer netwerk** selecteert, is de optie **Lab maken voor het kiezen van Lab-locatie** is uitgeschakeld. De reden hiervoor is dat Labs in het lab-account zich in dezelfde regio bevinden als het lab-account om verbinding te maken met resources in het virtuele peer netwerk. 

> [!IMPORTANT]
> Deze instelling is alleen van toepassing op Labs die zijn gemaakt nadat de wijziging is aangebracht, niet op de bestaande Labs. 


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Labaccounts maken en beheren als beheerder](how-to-manage-lab-accounts.md)
- [Labs maken en beheren als labeigenaar](how-to-manage-classroom-labs.md)
- [Sjablonen instellen en publiceren als labeigenaar](how-to-create-manage-template.md)
- [Als test gebruiker toegang krijgen tot klassikale Labs](how-to-use-classroom-lab.md)

