---
title: Azure VMware-oplossing per cloudSimple - Private Cloud verbinden met Azure-netwerk via ExpressRoute
description: Beschrijft hoe u uw CloudSimple Private Cloud-omgeving verbinden met het virtuele Azure-netwerk via ExpressRoute
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9bb68ec68f4de646239477ceeaac50a7a33989fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015232"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Verbind uw CloudSimple Private Cloud-omgeving met het virtuele Azure-netwerk via ExpressRoute

Uw CloudSimple Private Cloud kan met Azure ExpressRoute worden verbonden met uw virtuele Azure-netwerk.  Met deze verbinding met hoge bandbreedte met lage bandbreedte hebt u toegang tot services die in uw Azure-abonnement worden uitgevoerd vanuit uw Private Cloud-omgeving.

Met een virtuele netwerkverbinding u:

* Gebruik Azure als back-updoel voor virtuele machines in uw Private Cloud.
* Implementeer KMS-servers in uw Azure-abonnement om uw Private Cloud vSAN-gegevensarchief te versleutelen.
* Gebruik hybride toepassingen waarbij de weblaag van de toepassing wordt uitgevoerd in de openbare cloud terwijl de toepassings- en databaselagen worden uitgevoerd in uw Private Cloud.

![Azure ExpressRoute-verbinding met virtueel netwerk](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Een virtuele netwerkverbinding instellen

Als u de virtuele netwerkverbinding met uw Private Cloud wilt instellen, hebt u uw autorisatiesleutel, peer circuit URI en toegang tot uw Azure-abonnement nodig. Deze informatie is beschikbaar op de pagina Virtual Network Connection in de CloudSimple-portal. Zie [Peering-informatie voor Azure virtual network voor CloudSimple-verbinding verkrijgen voor](virtual-network-connection.md)instructies. Als u problemen hebt met het verkrijgen van de informatie, dient u een <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">ondersteuningsverzoek</a>in.

> [!TIP]
> Als u al een Extern Azure-netwerk, subnet voor gateway's en virtuele netwerkgateway hebt, u doorgaan naar stap 4.

1. Maak een virtueel netwerk op uw Azure-abonnement en controleer of de adresruimte die u selecteert, verschilt van de adresruimte van uw Private Cloud.  Als u al een virtueel Azure-netwerk hebt, u het bestaande netwerk gebruiken.  Zie Een [virtueel netwerk maken met de Azure-portal](../virtual-network/quick-create-portal.md)voor meer informatie.
2. Maak het gatewaysubnet op uw virtuele Azure-netwerk.  Als u al een gateway-subnet in uw virtuele Azure-netwerk hebt, u het bestaande subnet gebruiken. Zie Het [subnet gateway maken voor](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet)meer informatie.
3. Maak de virtuele netwerkgateway op uw virtuele netwerk.  Als u een bestaande virtuele netwerkgateway hebt, u de bestaande gateway gebruiken. Zie De [virtuele netwerkgateway maken](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)voor meer informatie.
4. Maak de verbinding tussen uw virtuele netwerk en uw Private Cloud door de autorisatiesleutel in te wisselen zoals beschreven in [Een virtueel netwerk verbinden met een circuit - ander abonnement](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

> [!WARNING]
> Als u een bestaande virtuele netwerkgateway gebruikt en deze een ExpressRoute-verbinding heeft naar dezelfde locatie als het CloudSimple ExpressRoute-circuit, wordt de verbinding niet tot stand gebracht.  Maak een nieuw virtueel netwerk en volg de vorige stappen.

## <a name="test-the-virtual-network-connection"></a>De virtuele netwerkverbinding testen

Nadat de verbinding is gemaakt, u de status van de verbinding controleren door **Eigenschappen** te selecteren onder **Instellingen**.  Status en provisioning state moeten **opgevolgd .**

![Verbindingsstatus](media/azure-expressroute-connection.png)

Ga als een proef met de virtuele netwerkverbinding:

1. Maak een virtuele machine in uw Azure-abonnement.
2. Zoek het IP-adres van uw Private Cloud vCenter (zie uw welkomste-mail).
3. Ping uw Cloud vCenter vanaf de virtuele machine die is gemaakt in uw virtuele Azure-netwerk.
4. Ping uw Azure virtuele machine vanaf een virtuele machine die wordt uitgevoerd in uw Private Cloud vCenter.

Als u problemen hebt met het tot stand brengen van de verbinding, dient u een <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">ondersteuningsverzoek</a>in.
