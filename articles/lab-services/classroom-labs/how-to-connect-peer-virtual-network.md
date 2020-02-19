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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 5e013011f81542aa279ba9276a6a1aac01eb9e41
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443181"
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
Tijdens het maken van het nieuwe lab-account kunt u een bestaand virtueel netwerk kiezen dat wordt weer gegeven in de vervolg keuzelijst van het **virtuele peer netwerk** op het tabblad **Geavanceerd** . Het geselecteerde virtuele netwerk is verbonden (gekoppeld aan) met de laboratoria die zijn gemaakt met het lab-account. Alle virtuele machines in Labs die zijn gemaakt na het maken van deze wijziging, hebben toegang tot de resources in het gekoppelde virtuele netwerk. 

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

- [Lab-Maker toestaan locatie van test omgeving te kiezen](allow-lab-creator-pick-lab-location.md)
- [Een galerie met gedeelde afbeeldingen koppelen aan een Lab](how-to-attach-detach-shared-image-gallery.md)
- [Een gebruiker toevoegen als een Lab-eigenaar](how-to-add-user-lab-owner.md)
- [Firewall instellingen voor een Lab weer geven](how-to-configure-firewall-settings.md)
- [Andere instellingen voor een lab configureren](how-to-configure-lab-accounts.md)