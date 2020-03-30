---
title: Azure VMware-oplossing door CloudSimple - Gebruik Private Cloud als rampsite voor on-premises workloads
description: Beschrijft hoe u uw CloudSimple Private Cloud instellen als een rampherstelsite voor on-premises VMware-workloads
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e019a9229b671be2fb73e758bd39f33657bc2d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083129"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>CloudSimple Private Cloud instellen als een rampherstelsite voor on-premises VMware-workloads

Uw CloudSimple Private Cloud kan worden ingesteld als een herstelsite voor on-premises toepassingen om bedrijfscontinuïteit te bieden in geval van een ramp. De hersteloplossing is gebaseerd op Zerto Virtual Replication als replicatie- en orchestration-platform. Virtuele kritieke infrastructuur- en toepassingsvirtuele machines kunnen continu worden gerepliceerd van uw on-premises vCenter naar uw Private Cloud. U uw Private Cloud gebruiken voor failovertests en om de beschikbaarheid van uw toepassing tijdens een ramp te garanderen. Een soortgelijke aanpak kan worden gevolgd om de Private Cloud in te stellen als een primaire site die wordt beschermd door een herstelsite op een andere locatie.

> [!NOTE]
> Raadpleeg de zerto-document [grootteoverwegingen voor virtuele replicatie](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) van Zerto voor richtlijnen voor het aanpassen van uw omgeving voor noodherstel.

De CloudSimple-oplossing:

* Elimineert de noodzaak om een datacenter speciaal voor disaster recovery (DR) in te stellen.
* Hiermee u gebruikmaken van de Azure-locaties waar CloudSimple wordt ingezet voor wereldwijde geografische veerkracht.
* Biedt u de mogelijkheid om de implementatiekosten en de totale eigendomskosten voor DR te verlagen.

De oplossing vereist dat u:

* Installeer, configureer en beheer Zerto in uw Private Cloud.
* Geef uw eigen licenties voor Zerto op wanneer de Private Cloud de beveiligde site is. U Zerto die op de CloudSimple-site wordt uitgevoerd, koppelen aan uw on-premises site voor licenties.

De volgende figuur toont de architectuur voor de Zerto-oplossing.

![Architectuur](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>De oplossing implementeren

In de volgende secties wordt beschreven hoe u een DR-oplossing implementeert met Zerto Virtual Replication in uw private cloud.

1. [Vereisten](#prerequisites)
2. [Optionele configuratie op CloudSimple Private Cloud](#optional-configuration-on-your-private-cloud)
3. [ZVM en VRA instellen op CloudSimple Private Cloud](#set-up-zvm-and-vra-on-your-private-cloud)
4. [Zerto-groep voor virtuele beveiliging instellen](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Vereisten

Als u Zerto Virtual Replication wilt inschakelen vanuit uw on-premises omgeving naar uw private cloud, voert u de volgende vereisten in.

1. [Stel een Site-to-Site VPN-verbinding in tussen uw on-premises netwerk en uw CloudSimple Private Cloud.](set-up-vpn.md)
2. [Stel DNS-lookup in zodat uw Private Cloud-beheercomponenten worden doorgestuurd naar Private Cloud DNS-servers.](on-premises-dns-setup.md)  Als u het doorsturen van DNS-lookup wilt inschakelen, `*.cloudsimple.io` maakt u een doorstuurzonevermelding op uw on-premises DNS-server voor cloudsimple DNS-servers.
3. Stel DNS-lookup zo in dat on-premises vCenter-componenten worden doorgestuurd naar on-premises DNS-servers.  De DNS-servers moeten bereikbaar zijn vanuit uw CloudSimple Private Cloud via Site-to-Site VPN. Voor bijstand dient u een [ondersteuningsverzoek](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)in, met de volgende informatie.  

    * On-premises DNS-domeinnaam
    * Ip-adressen van on-premises DNS-servers

4. Installeer een Windows-server op uw Private Cloud. De server wordt gebruikt om Zerto Virtual Manager te installeren.
5. [Escaleer uw CloudSimple-bevoegdheden.](escalate-private-cloud-privileges.md)
6. Maak een nieuwe gebruiker op uw Private Cloud vCenter met de administratieve rol te gebruiken als service-account voor Zerto Virtual Manager.

### <a name="optional-configuration-on-your-private-cloud"></a>Optionele configuratie op uw Private Cloud

1. Maak een of meer resourcepools op uw Private Cloud vCenter om te gebruiken als doelbronpools voor VM's uit uw on-premises omgeving.
2. Maak een of meer mappen in uw Private Cloud vCenter om te gebruiken als doelmappen voor VM's uit uw on-premises omgeving.
3. Vlan's maken voor failovernetwerk en firewallregels instellen. Open een [ondersteuningsverzoek](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) voor hulp.
4. Maak gedistribueerde poortgroepen voor failovernetwerk en testnetwerk voor het testen van failover van VM's.
5. Installeer [DHCP- en DNS-servers](dns-dhcp-setup.md) of gebruik een Active Directory-domeincontroller in uw Private Cloud-omgeving.

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>STEL ZVM en VRA in op uw Private Cloud

1. Installeer Zerto Virtual Manager (ZVM) op de Windows-server in uw Private Cloud.
2. Meld u aan bij ZVM met het serviceaccount dat in eerdere stappen is gemaakt.
3. Licenties instellen voor Zerto Virtual Manager.
4. Installeer Zerto Virtual Replication Appliance (VRA) op de ESXi-hosts van uw Private Cloud.
5. Koppel uw Private Cloud ZVM aan uw on-premises ZVM.

### <a name="set-up-zerto-virtual-protection-group"></a>Zerto-groep voor virtuele beveiliging instellen

1. Maak een nieuwe Virtual Protection Group (VPG) en geef de prioriteit voor de VPG op.
2. Selecteer de virtuele machines die bescherming nodig hebben voor de bedrijfscontinuïteit en pas indien nodig de opstartvolgorde aan.
3. Selecteer de herstelsite als uw private cloud en de standaardherstelserver als het Private Cloud-cluster of de brongroep die u hebt gemaakt. Selecteer **vsanDatastore** voor het herstelgegevensarchief op uw Private Cloud.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > U de hostoptie voor afzonderlijke VM's aanpassen onder de optie VM-instellingen.

4. Pas de opslagopties naar behoefte aan.
5. Geef de herstelnetwerken op die moeten worden gebruikt voor failovernetwerk- en failovertestnetwerk als de eerder gemaakte gedistribueerde poortgroepen en pas de herstelscripts naar behoefte aan.
6. Pas indien nodig de netwerkinstellingen voor afzonderlijke VM's aan en maak de VPG.
7. Test failover zodra de replicatie is voltooid.

## <a name="reference"></a>Naslaginformatie

[Zerto-documentatie](https://www.zerto.com/myzerto/technical-documentation/)
