---
title: Azure VMware-oplossingen (AVS)-Connect AVS Privécloud to Azure Network using ExpressRoute
description: Hierin wordt beschreven hoe u een persoonlijke cloud omgeving van uw AVS verbindt met het virtuele Azure-netwerk met behulp van ExpressRoute
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3d487794e219f63150142db8df4b0c1abf112947
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015232"
---
# <a name="connect-your-avs-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Uw cloud omgeving van uw AVS verbinden met het virtuele Azure-netwerk met behulp van ExpressRoute

Uw AVS-Privécloud kan worden verbonden met uw virtuele Azure-netwerk met behulp van Azure ExpressRoute. Met deze verbinding met hoge band breedte en lage latentie kunt u toegang krijgen tot services die worden uitgevoerd in uw Azure-abonnement vanuit de privécloud-omgeving van uw Cloud.

Met virtuele netwerk verbinding kunt u:

* Gebruik Azure als back-updoel voor virtuele machines in de privécloud van uw AVS.
* Implementeer KMS-servers in uw Azure-abonnement om uw vSAN-gegevens opslag van uw AVS te versleutelen.
* Gebruik hybride toepassingen waarbij de weblaag van de toepassing wordt uitgevoerd in de open bare Cloud terwijl de lagen van de toepassing en de data base in de privécloud van uw AVS worden uitgevoerd.

![Azure ExpressRoute-verbinding met virtueel netwerk](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Een virtuele netwerk verbinding instellen

Als u de virtuele netwerk verbinding wilt instellen op de privécloud van uw AVS, hebt u uw autorisatie sleutel, peer-circuit-URI en toegang tot uw Azure-abonnement nodig. Deze informatie is beschikbaar op de pagina Virtual Network verbinding in de AVS-Portal. Zie voor instructies [informatie over peering voor het virtuele netwerk van Azure verkrijgen](virtual-network-connection.md)voor de AVS-verbinding. Indien u problemen hebt met het verkrijgen van de informatie, dient u een <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">ondersteunings aanvraag</a>in.

> [!TIP]
> Als u al een virtueel Azure-netwerk, gateway-subnet en virtuele netwerk gateway hebt, kunt u door gaan naar stap 4.

1. Maak een virtueel netwerk op uw Azure-abonnement en controleer of de adres ruimte die u selecteert, afwijkt van de adres ruimte van de privécloud van uw AVS. Als u al een virtueel Azure-netwerk hebt, kunt u het bestaande gebruiken. Zie [een virtueel netwerk maken met behulp van de Azure Portal](../virtual-network/quick-create-portal.md)voor meer informatie.
2. Maak het gateway-subnet op uw virtuele Azure-netwerk. Als u al een gateway-subnet in uw virtuele Azure-netwerk hebt, kunt u de bestaande gebruiken. Zie [het subnet gateway maken](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet)voor meer informatie.
3. Maak de virtuele netwerk gateway in het virtuele netwerk. Als u een bestaande virtuele netwerk gateway hebt, kunt u deze gebruiken. Zie [de gateway van het virtuele netwerk maken](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)voor meer informatie.
4. Maak de verbinding tussen uw virtuele netwerk en de privécloud van uw AVS door de autorisatie sleutel opnieuw in te stellen zoals beschreven in [een virtueel netwerk verbinden met een circuit-ander abonnement](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

> [!WARNING]
> Als u een bestaande virtuele netwerk gateway gebruikt en er een ExpressRoute-verbinding is met dezelfde locatie als het ExpressRoute-circuit van de AVS, wordt de verbinding niet tot stand gebracht. Maak een nieuw virtueel netwerk en volg de vorige stappen.

## <a name="test-the-virtual-network-connection"></a>De virtuele netwerk verbinding testen

Nadat de verbinding is gemaakt, kunt u de status van de verbinding controleren door **Eigenschappen** te selecteren onder **instellingen**. Status en de inrichtings status moeten **worden weer gegeven**.

![Verbindings status](media/azure-expressroute-connection.png)

De virtuele netwerk verbinding testen:

1. Maak een virtuele machine in uw Azure-abonnement.
2. Zoek het IP-adres van uw AVS Private Cloud vCenter (Raadpleeg uw welkomst bericht).
3. Ping uw Cloud vCenter van de virtuele machine die is gemaakt in uw virtuele Azure-netwerk.
4. Ping uw virtuele Azure-machine vanaf een virtuele machine die wordt uitgevoerd in uw AVS privécloud-vCenter.

Als er problemen zijn met het tot stand brengen van de verbinding, dient u een <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">ondersteunings aanvraag</a>in.
