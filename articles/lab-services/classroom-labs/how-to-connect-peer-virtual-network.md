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
ms.date: 03/31/2020
ms.author: spelluru
ms.openlocfilehash: 56c31e03eeec0c81207dc402e864eadec2d768bd
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474071"
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

Er is ook een bepaling om **adres bereik** van virtuele machines voor de laboratoria te bieden. Als het adresbereik wordt opgegeven, worden alle virtuele machines in de labs onder het labaccount in dat adresbereik gemaakt. Het adresbereik moet zich in CIDR-notatie bevinden (bijvoorbeeld 10.20.0.0/20) en niet overlappen met bestaande adresbereiken. Bij het verstrekken van een adresbereik is het belangrijk om na te denken over het aantal virtuele machines dat in de laboratoria wordt gemaakt en een adresbereik te bieden om dat mogelijk te maken. Voor een bepaald bereik wordt het aantal laboratoria dat het kan huisvesten weergegeven.

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
