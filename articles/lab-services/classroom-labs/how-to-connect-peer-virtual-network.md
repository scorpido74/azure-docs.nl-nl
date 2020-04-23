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
ms.openlocfilehash: 6ed0d743f9b9cdc136b8f52f4d9d02583fc63eb9
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870180"
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
> Bij het maken van een Azure Virtual Network dat wordt gekeken met een labaccount, is het belangrijk om te begrijpen hoe de regio van het virtuele netwerk van invloed is op de plaats waar klaslokalen worden gemaakt.  Zie de sectie beheerdershandleiding over [regio's\locaties](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)voor meer informatie.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configureren op het moment van het maken van labaccount

Tijdens het maken van een nieuw [labaccount](tutorial-setup-lab-account.md)u een bestaand virtueel netwerk kiezen dat wordt weergegeven in de vervolgkeuzelijst **Voor virtuele netwerken peer** op het tabblad **Geavanceerd.**  De lijst toont alleen virtuele netwerken in dezelfde regio als het labaccount. Het geselecteerde virtuele netwerk is verbonden (peered) met laboratoria die onder het labaccount zijn gemaakt.  Alle virtuele machines in laboratoria die zijn gemaakt na het maken van deze wijziging hebben toegang tot de bronnen op het peered virtuele netwerk.

![VNet selecteren om te peeren](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>Adresbereik

Er is ook een optie om **adresbereik** voor virtuele machines voor de laboratoria te bieden.  De eigenschap **Adresbereik** is alleen van toepassing als **het virtuele netwerk Peer** is ingeschakeld voor het lab.  Als het adresbereik wordt opgegeven, worden alle virtuele machines in de labs onder het labaccount in dat adresbereik gemaakt. Het adresbereik moet zich in CIDR-notatie bevinden (bijvoorbeeld 10.20.0.0/20) en niet overlappen met bestaande adresbereiken.  Bij het verstrekken van een adresbereik is het belangrijk om na te denken over het aantal *labs* dat wordt gemaakt en een adresbereik te bieden om dat mogelijk te maken. Lab Services gaat uit van maximaal 512 virtuele machines per lab.  Een ip-bereik met '/23' kan bijvoorbeeld slechts één lab creëren.  Een bereik met een '/21' maakt het mogelijk om vier labs te creëren.

Als het **adresbereik** niet is opgegeven, gebruikt Lab Services het standaardadresbereik dat Azure aan het adres heeft gegeven bij het maken van het virtuele netwerk dat moet worden gekeken met uw virtuele netwerk.  Het bereik is vaak iets van 10.x.0.0/16.  Dit kan leiden tot overlap tussen ip-bereiken, dus zorg ervoor dat u het bereik in de labinstellingen opgeeft en adresbereik opgeeft of het adresbereik controleert van uw virtuele netwerk dat wordt peered.

## <a name="configure-after-the-lab-is-created"></a>Configureren nadat het lab is gemaakt

Dezelfde eigenschap kan worden ingeschakeld via het **tabblad Labs-configuratie** van de pagina **Lab-account** als u op het moment van het maken van een labaccount geen peer-netwerk hebt ingesteld. Wijzigingen in deze instelling zijn alleen van toepassing op de labs die na de wijziging zijn gemaakt. Zoals u in de afbeelding zien, u **het virtuele netwerk Peer** in- of uitschakelen voor labs in het labaccount.

![VNet-peering inschakelen of uitschakelen nadat het lab is gemaakt](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

Wanneer u een virtueel netwerk selecteert voor het **virtuele netwerkveld Peer,** is de optie **Lab-locatie toestaan om de locatievan het lab te kiezen,** uitgeschakeld. Dat komt omdat labs in het lab account moet worden in dezelfde regio als het lab account voor hen om verbinding te maken met middelen in de peer virtuele netwerk.

> [!IMPORTANT]
> De peered virtuele netwerk instelling is alleen van toepassing op labs die zijn gemaakt nadat de wijziging is gemaakt, niet op de bestaande labs.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen:

- [Toestaan dat de labmaker de lablocatie kiest](allow-lab-creator-pick-lab-location.md)
- [Een gedeelde afbeeldingsgalerie aan een lab koppelen](how-to-attach-detach-shared-image-gallery.md)
- [Een gebruiker toevoegen als eigenaar van een lab](how-to-add-user-lab-owner.md)
- [Firewall-instellingen voor een lab weergeven](how-to-configure-firewall-settings.md)
- [Andere instellingen voor een lab configureren](how-to-configure-lab-accounts.md)
