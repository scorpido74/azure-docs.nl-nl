---
title: 'Azure VMware-oplossingen (AVS): AVS-Privécloud gebruiken als nood site voor on-premises workloads'
description: Hierin wordt beschreven hoe u uw Privécloud als een nood herstel site instelt voor on-premises VMware-workloads
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5ee43af97e79f1e835787d61bd79cfb256ef445
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083129"
---
# <a name="set-up-avs-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>Een persoonlijke cloud van AVS instellen als een nood herstel site voor on-premises VMware-workloads

Uw AVS-Privécloud kan worden ingesteld als een herstel site voor on-premises toepassingen om bedrijfs continuïteit te bieden in het geval van een ramp. De herstel oplossing is gebaseerd op Zerto virtuele replicatie als het replicatie-en Orchestration-platform. Kritieke infra structuur en toepassings-virtuele machines kunnen continu worden gerepliceerd van uw on-premises vCenter naar de Privécloud van uw AVS. U kunt de Privécloud voor failover testen en de beschik baarheid van uw toepassing tijdens een nood geval controleren. Een soort gelijke aanpak kan worden gevolgd om de automatische AVS-Cloud in te stellen als een primaire site die wordt beveiligd door een herstel site op een andere locatie.

> [!NOTE]
> Raadpleeg de Zerto van de document [grootte voor Zerto Virtual Replication](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) voor richt lijnen over het aanpassen van de grootte van uw omgeving voor herstel na nood gevallen.

De AVS-oplossing:

* Elimineert de nood zaak om een Data Center in te stellen dat specifiek is voor herstel na nood gevallen (DR).
* Met kunt u gebruikmaken van de Azure-locaties waar AVS wordt geïmplementeerd voor wereld wijde geografische tolerantie.
* Biedt u een optie om implementatie kosten en total cost of ownership voor DR te reduceren.

Voor de oplossing moet u het volgende doen:

* Zerto in de Privécloud van uw AVS installeren, configureren en beheren.
* Geef uw eigen licenties voor Zerto op als de Privécloud de beveiligde site is. U kunt Zerto die op de AVS-site worden uitgevoerd, koppelen met uw on-premises site voor licentie verlening.

In de volgende afbeelding ziet u de architectuur van de Zerto-oplossing.

![Architectuur](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>De oplossing implementeren

In de volgende secties wordt beschreven hoe u een DR-oplossing implementeert met virtuele replicatie van Zerto in uw AVS-privécloud.

1. [Vereisten](#prerequisites)
2. [Optionele configuratie op uw AVS-Privécloud](#optional-configuration-on-your-avs-private-cloud)
3. [ZVM en VRA op uw AVS-Privécloud instellen](#set-up-zvm-and-vra-on-your-avs-private-cloud)
4. [Virtuele beveiligings groep Zerto instellen](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Vereisten

Als u virtuele Zerto-replicatie van uw on-premises omgeving naar uw AVS-Privécloud wilt inschakelen, moet u de volgende vereisten volt ooien.

1. [Stel een site-naar-site-VPN-verbinding in tussen uw on-premises netwerk en de privécloud van uw AVS](set-up-vpn.md).
2. [Stel de DNS-zoek opdracht in zodat uw beheer onderdelen van de Privécloud Private Cloud worden doorgestuurd naar de GEavse DNS-servers van de privécloud](on-premises-dns-setup.md). Als u het door sturen van DNS-lookup wilt inschakelen, maakt u een doorstuur zone vermelding in uw on-premises DNS-server voor `*.cloudsimple.io` om DNS-servers te AVS.
3. Stel de DNS-zoek opdracht zodanig in dat de on-premises vCenter-onderdelen worden doorgestuurd naar lokale DNS-servers. De DNS-servers moeten bereikbaar zijn vanuit uw automatische AVS-Cloud via site-naar-site-VPN. Voor hulp dient u een [ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)in te dienen, waarbij de volgende informatie wordt verstrekt. 

    * Naam van on-premises DNS-domein
    * IP-adressen van on-premises DNS-server

4. Installeer een Windows-Server in de Privécloud van uw AVS. De server wordt gebruikt om Zerto Virtual Manager te installeren.
5. [Uw AVS-bevoegdheden escaleren](escalate-private-cloud-privileges.md).
6. Maak een nieuwe gebruiker op uw AVS-Privécloud met de beheerdersrol die moet worden gebruikt als service account voor Zerto Virtual Manager.

### <a name="optional-configuration-on-your-avs-private-cloud"></a>Optionele configuratie op uw AVS-Privécloud

1. Maak een of meer resource groepen op uw AVS Privécloud-vCenter om te gebruiken als doel resource groepen voor Vm's uit uw on-premises omgeving.
2. Maak een of meer mappen in uw AVS Privécloud-vCenter om te gebruiken als doel mappen voor virtuele machines uit uw on-premises omgeving.
3. Maak VLAN'S voor het failover-netwerk en stel firewall regels in. Open een [ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) voor ondersteuning.
4. Maak gedistribueerde poort groepen voor het failover-netwerk en het test netwerk om de failover van Vm's te testen.
5. Installeer [DHCP-en DNS-servers](dns-dhcp-setup.md) of gebruik een Active Directory-domein controller in uw cloud omgeving van uw AVS.

### <a name="set-up-zvm-and-vra-on-your-avs-private-cloud"></a>ZVM en VRA op uw AVS-Privécloud instellen

1. Installeer Zerto Virtual Manager (ZVM) op de Windows-Server in de Privécloud van uw AVS.
2. Meld u aan bij ZVM met het service account dat u in de vorige stappen hebt gemaakt.
3. Stel de licentie verlening voor Zerto Virtual manager in.
4. Installeer Zerto Virtual Replication Appliance (VRA) op de ESXi-hosts van uw AVS-privécloud.
5. Uw ZVM-Cloud voor de Privécloud koppelen met uw on-premises ZVM.

### <a name="set-up-zerto-virtual-protection-group"></a>Virtuele beveiligings groep Zerto instellen

1. Maak een nieuwe virtuele beveiligings groep (VPG) en geef de prioriteit voor de VPG op.
2. Selecteer de virtuele machines die moeten worden beveiligd voor bedrijfs continuïteit en pas de opstart volgorde zo nodig aan.
3. Selecteer de herstel site als uw Privécloud en de standaard herstel server als het gehoste privécloud-Cloud cluster of de resource groep die u hebt gemaakt. Selecteer **vsanDatastore** voor de herstel gegevens opslag in de privécloud van uw AVS.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > U kunt de optie host voor afzonderlijke Vm's aanpassen onder de optie VM-instellingen.

4. Pas de opslag opties aan zoals vereist.
5. Geef de herstel netwerken op die moeten worden gebruikt voor het failover-netwerk en het test netwerk voor failover als de gedistribueerde poort groepen die eerder zijn gemaakt en pas de herstel scripts indien nodig aan.
6. Pas zo nodig de netwerk instellingen voor afzonderlijke Vm's aan en maak de VPG.
7. Testfailover wanneer de replicatie is voltooid.

## <a name="reference"></a>Naslaginformatie

[Documentatie voor Zerto](https://www.zerto.com/myzerto/technical-documentation/)
