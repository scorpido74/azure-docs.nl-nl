---
title: Azure VMware Solution verbinden met uw on-premises omgeving
description: Leer Azure VMware Solution verbinden met uw on-premises omgeving.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 2a0cb641df00f3e580e87e38aff382d8e8101fc7
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578765"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Azure VMware Solution verbinden met uw on-premises omgeving

In dit artikel gaat u door met het gebruik van de [informatie verzameld tijden de planning](production-ready-deployment-steps.md) om Azure VMware Solution te verbinden met uw on-premises omgeving.

Voordat u begint, zijn er twee vereisten om Azure VMware Solution te verbinden met uw on-premises omgeving:

- Een ExpressRoute-circuit van uw on-premises omgeving naar Azure.
- Een/29 niet-overlappend blok met netwerkadressen voor de ExpressRoute Global Reach-peering, dat u heeft gedefinieerd tijdens de [planningsfase](production-ready-deployment-steps.md).

>[!NOTE]
> U kunt verbinding maken via VPN, maar dat wordt niet besproken in deze snelstartgids.

## <a name="establish-an-expressroute-global-reach-connection"></a>Een ExpressRoute Global Reach-verbinding maken

Als u een on-premises verbinding tot stand wilt brengen met uw VMware Solution-privécloud met behulp van ExpressRoute Global Reach, volg dan de zelfstudie [On-premises omgevingen peeren met een privécloud](tutorial-expressroute-global-reach-private-cloud.md).



## <a name="verify-on-premises-network-connectivity"></a>On-premises netwerkverbinding controleren

U ziet nu in uw **on-premises edge-router** waar de ExpressRoute de NSX-T-netwerksegmenten en de Azure VMware Solution-beheersegmenten verbindt.

>[!NOTE]
>Iedereen heeft een andere omgeving en sommigen moeten deze routes toestaan om door te geven in het on-premises netwerk.  

Sommige omgevingen hebben firewalls die het ExpressRoute-circuit beveiligen.  Als er geen firewalls of route pruning worden uitgevoerd, kunt u uw Azure VMware Solution vCenter-server of een [VM](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) op het NSX-T-segment pingen vanuit uw on-premises omgeving.

Daarnaast kunt u vanuit de virtuele machine op het NSX-T-segment bronnen in uw on-premises omgeving bereiken.

## <a name="next-steps"></a>Volgende stappen

Ga door naar de volgende sectie om VMware HCX te implementeren en te configureren

> [!div class="nextstepaction"]
> [VMware HCX implementeren en configureren](tutorial-deploy-vmware-hcx.md)