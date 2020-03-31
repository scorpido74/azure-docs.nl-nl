---
title: Verbinding maken met een peer-netwerk in Azure Lab Services | Microsoft Documenten
description: Meer informatie over het verbinden van uw labnetwerk met een ander netwerk als peer. Sluit bijvoorbeeld uw on-premises school/universiteitsnetwerk aan met het virtuele netwerk van Lab in Azure.
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
ms.openlocfilehash: d2115b1dc7e9f3150e44eb5ee9417e88ebeaa279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370835"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Het netwerk van uw lab verbinden met een virtueel netwerk van collega's in Azure Lab Services 
In dit artikel vindt u informatie over het peering van uw labsnetwerk met een ander netwerk. 

## <a name="overview"></a>Overzicht
Met virtuele netwerkpeering u virtuele Azure-netwerken naadloos met elkaar verbinden. Nadat de virtuele netwerken zijn gekoppeld via peering, worden ze voor verbindingsdoeleinden weergegeven als één netwerk. Het verkeer tussen virtuele machines in de peered virtuele netwerken wordt gerouteerd via de Microsoft backbone infrastructuur, net als het verkeer wordt gerouteerd tussen virtuele machines in hetzelfde virtuele netwerk, door middel van prive-IP-adressen alleen. Zie [Virtueel netwerkpeeren](../../virtual-network/virtual-network-peering-overview.md)voor meer informatie.

Mogelijk moet u het netwerk van uw lab verbinden met een virtueel netwerk van collega's in sommige scenario's, waaronder de volgende:

- De virtuele machines in het lab hebben software die verbinding maakt met on-premises licentieservers om licentie te verkrijgen
- De virtuele machines in het lab hebben toegang nodig tot datasets (of andere bestanden) op de netwerkaandelen van de universiteit. 

Bepaalde on-premises netwerken zijn verbonden met Azure Virtual Network via [ExpressRoute](../../expressroute/expressroute-introduction.md) of [Virtual Network Gateway.](../../vpn-gateway/vpn-gateway-about-vpngateways.md) Deze services moeten buiten Azure Lab Services worden ingesteld. Zie [ExpressRoute-overzicht](../../expressroute/expressroute-introduction.md)voor meer informatie over het verbinden van een on-premises netwerk met Azure via ExpressRoute. Voor on-premises connectiviteit met behulp van een Virtual Network Gateway moeten de gateway, het opgegeven virtuele netwerk en het labaccount zich allemaal in dezelfde regio bevinden.

> [!NOTE]
> Bij het maken van een Azure Virtual Network dat wordt gekeken met een labaccount, is het belangrijk om te begrijpen hoe de regio van het virtuele netwerk van invloed is op de plaats waar klaslokalen worden gemaakt.  Zie voor meer informatie de sectie beheerdershandleiding over [regio's\locaties](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regions-or-locations).

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configureren op het moment van het maken van labaccount
Tijdens het maken van een nieuw labaccount u een bestaand virtueel netwerk kiezen dat wordt weergegeven in de vervolgkeuzelijst **Peer virtueel netwerk** op het tabblad **Geavanceerd.** Het geselecteerde virtuele netwerk is verbonden (peered) met laboratoria die onder het labaccount zijn gemaakt. Alle virtuele machines in laboratoria die zijn gemaakt na het maken van deze verandering zou toegang hebben tot de middelen op de peered virtuele netwerk. 

![VNet selecteren om te peeren](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Zie [Een labaccount instellen](tutorial-setup-lab-account.md) voor gedetailleerde stapsgewijze instructies voor het maken van een labaccount


## <a name="configure-after-the-lab-is-created"></a>Configureren nadat het lab is gemaakt
Dezelfde eigenschap kan worden ingeschakeld via het **tabblad Labs-configuratie** van de pagina **Lab-account** als u op het moment van het maken van een labaccount geen peer-netwerk hebt ingesteld. Wijzigingen in deze instelling zijn alleen van toepassing op de labs die na de wijziging zijn gemaakt. Zoals u in de afbeelding zien, u **het virtuele netwerk Peer** in- of uitschakelen voor labs in het labaccount. 

![VNet-peering inschakelen of uitschakelen nadat het lab is gemaakt](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Wanneer u een virtueel netwerk selecteert voor het **virtuele netwerkveld Peer,** is de optie **Lab-locatie toestaan om de locatievan het lab te kiezen,** uitgeschakeld. Het is omdat labs in het lab account moet worden in dezelfde regio als het lab account voor hen om verbinding te maken met middelen in de peer virtuele netwerk. 

> [!IMPORTANT]
> Deze instellingswijziging is alleen van toepassing op laboratoria die zijn gemaakt nadat de wijziging is aangebracht, niet op de bestaande labs. 


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Toestaan dat de labmaker de lablocatie kiest](allow-lab-creator-pick-lab-location.md)
- [Een gedeelde afbeeldingsgalerie aan een lab koppelen](how-to-attach-detach-shared-image-gallery.md)
- [Een gebruiker toevoegen als eigenaar van een lab](how-to-add-user-lab-owner.md)
- [Firewall-instellingen voor een lab weergeven](how-to-configure-firewall-settings.md)
- [Andere instellingen voor een lab configureren](how-to-configure-lab-accounts.md)