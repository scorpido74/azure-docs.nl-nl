---
title: Netwerkconfiguraties aanpassen voor een failover-VM | Microsoft Documenten
description: Biedt een overzicht van het aanpassen van netwerkconfiguraties voor een failover-vm in de replicatie van Azure VM's met Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: rajanaki
ms.openlocfilehash: 96ffa34166797945afc04c66b03fe151d26c65bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76292855"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Netwerkconfiguraties van de beoogde Azure-VM aanpassen

In dit artikel vindt u richtlijnen voor het aanpassen van netwerkconfiguraties op de virtuele doelmachine (VM) van Azure wanneer u Azure VM's van de ene regio naar het andere repliceert en herstelt met [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Voordat u begint

Meer informatie over hoe siteherstel voor [dit scenario](azure-to-azure-architecture.md)een rampherstel biedt.

## <a name="supported-networking-resources"></a>Ondersteunde netwerkbronnen

U de volgende belangrijke resourceconfiguraties voor de failover-VM leveren terwijl u Azure VM's repliceert:

- [Interne load balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
- [Openbaar IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Netwerkbeveiligingsgroep](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) zowel voor het subnet als voor de NIC

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat u uw herstelzijdeconfiguraties van tevoren plant.
- Maak de netwerkbronnen van tevoren. Geef het als een invoer, zodat Azure Site Recovery-service deze instellingen kan nakomen en ervoor kan zorgen dat de failover-VM zich aan deze instellingen houdt.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>Failover aanpassen en failover-netwerkconfiguraties testen

1. Ga naar **gerepliceerde items**. 
2. Selecteer de gewenste Azure VM.
3. Selecteer **Berekenen en netwerk** en selecteer **Bewerken**. De NIC-configuratie-instellingen bevatten de bijbehorende bronnen bij de bron. 

     ![De failover-netwerkconfiguraties aanpassen](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Selecteer een testfailover virtueel netwerk. U ervoor kiezen om het leeg te laten en selecteer een op het moment van de test failover.
5. Failovernetwerk is Bewerken **selecteren** in de buurt van de NIC die u wilt configureren. Selecteer in het volgende blad dat wordt geopend de overeenkomstige vooraf gemaakte resources in de failover- en failoverlocatie van de test.

    ![De NIC-configuratie bewerken](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. Selecteer **OK**.

Site recovery zal nu deze instellingen eren en ervoor zorgen dat de VM op failover is verbonden met de geselecteerde resource via de bijbehorende NIC.

Wanneer u de testfailover activeert via herstelplan, wordt het altijd gevraagd aan het virtuele Azure-netwerk. Dit virtuele netwerk wordt gebruikt voor testfailover voor de machines die geen vooraf geconfigureerde failoverinstellingen voor tests hadden.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="unable-to-view-or-select-a-resource"></a>Kan een resource niet weergeven of selecteren

Als u een netwerkbron niet selecteren of bekijken, gaat u de volgende controles en voorwaarden door:

- Het doelveld voor een netwerkbron is alleen ingeschakeld als de bron-VM een overeenkomstige invoer had. Dit is gebaseerd op het principe dat voor een scenario voor noodherstel, u de exacte of een verkleinde versie van uw bron wilt.
- Voor elke netwerkbron worden sommige filters toegepast in de vervolgkeuzelijst om ervoor te zorgen dat de failover-VM zich kan hechten aan de geselecteerde resource en de betrouwbaarheid van de failover wordt gehandhaafd. Deze filters zijn gebaseerd op dezelfde netwerkvoorwaarden die zouden zijn geverifieerd toen u de bron-VM configureerde.

Interne validaties van load balancer:

- Het abonnement en de regio van de load balancer en de doel-VM moeten hetzelfde zijn.
- Het virtuele netwerk dat is gekoppeld aan de interne load balancer en dat van de doel-VM moet hetzelfde zijn.
- De openbare IP-SKU van de doel-VM en de SKU van de interne load balancer moeten hetzelfde zijn.
- Als de doel-VM is geconfigureerd om in een beschikbaarheidszone te worden geplaatst, controleert u of de load balancer zoneredundant is of onderdeel is van een beschikbaarheidszone. (BasisSKU-load balancers ondersteunen geen zones en worden in dit geval niet weergegeven in de vervolgkeuzelijst.)
- Zorg ervoor dat de interne load balancer een vooraf gemaakte back-end pool en front-end configuratie heeft.

Openbaar IP-adres:

- Het abonnement en de regio van het openbare IP-adres en de doel-VM moeten hetzelfde zijn.
- De openbare IP-SKU van de doel-VM en de SKU van de interne load balancer moeten hetzelfde zijn.

Netwerkbeveiligingsgroep:
- Het abonnement en de regio van de netwerkbeveiligingsgroep en de doel-VM moeten hetzelfde zijn.


> [!WARNING]
> Als de doel-VM is gekoppeld aan een beschikbaarheidsset, moet u de openbare IP- en interne load balancer van dezelfde SKU koppelen aan die van de openbare IP- en interne loadbalancer van de andere VM in de beschikbaarheidsset. Als u dat niet doet, kan failover niet slagen.
