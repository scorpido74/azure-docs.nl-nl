---
title: Citrix XenDesktop/XenApp disaster recovery instellen met Azure Site Recovery
description: In dit artikel wordt beschreven hoe u noodherstel instellen voor Citrix XenDesktop- en XenApp-implementaties met Azure Site Recovery.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 29fbe5389da924a2ecc660aa5ce5c4bb0a0902b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74084543"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>disaster recovery instellen voor een multi-tier Citrix XenApp en XenDesktop implementatie



Citrix XenDesktop is een desktopvirtualisatieoplossing die desktops en applicaties als ondemand-service levert aan elke gebruiker, waar dan ook. Met FlexCast-leveringstechnologie kan XenDesktop snel en veilig applicaties en desktops leveren aan gebruikers.
Tegenwoordig biedt Citrix XenApp geen mogelijkheden voor noodherstel.

Een goede oplossing voor noodherstel, moet modellering van herstelplannen rond de bovenstaande complexe toepassingsarchitecturen mogelijk maken en ook de mogelijkheid hebben om aangepaste stappen toe te voegen om toepassingstoewijzingen tussen verschillende lagen te verwerken, vandaar een enkele klik te bieden sure shot oplossing in het geval van een ramp leidt tot een lagere RTO.

Dit document biedt stapsgewijze richtlijnen voor het bouwen van een oplossing voor noodherstel voor uw on-premises Citrix XenApp-implementaties op Hyper-V- en VMware-vSphere-platforms. In dit document wordt ook beschreven hoe u een testfailover (disaster recovery drill) en ongeplande failover naar Azure uitvoeren met behulp van herstelplannen, de ondersteunde configuraties en vereisten.


## <a name="prerequisites"></a>Vereisten

Controleer voordat u begint het volgende:

1. [Een virtuele machine repliceren naar Azure](site-recovery-vmware-to-azure.md)
1. Een [herstelnetwerk ontwerpen](site-recovery-network-design.md)
1. [Een testfailover naar Azure](site-recovery-test-failover-to-azure.md)
1. [Een failover naar Azure](site-recovery-failover.md)
1. Een [domeincontroller repliceren](site-recovery-active-directory.md)
1. SQL [Server repliceren](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Implementatiepatronen

Een Citrix XenApp- en XenDesktop-farm hebben doorgaans het volgende implementatiepatroon:

**Implementatiepatroon**

Citrix XenApp en XenDesktop implementatie met AD DNS server, SQL database server, Citrix Delivery Controller, StoreFront server, XenApp Master (VDA), Citrix XenApp License Server

![Implementatiepatroon 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Ondersteuning voor Site Recovery

Voor de toepassing van dit artikel werden Citrix-implementaties op Virtuele VMware-machines beheerd door vSphere 6.0 / System Center VMM 2012 R2 gebruikt om DR in te stellen.

### <a name="source-and-target"></a>Bron en doel

**Scenario** | **Op een secundaire site** | **Naar Azure**
--- | --- | ---
**Hyper-V** | Niet in het toepassingsgebied | Ja
**Vmware** | Niet in het toepassingsgebied | Ja
**Fysieke server** | Niet in het toepassingsgebied | Ja

### <a name="versions"></a>Versies
Klanten kunnen XenApp-componenten implementeren als virtuele machines die draaien op Hyper-V of VMware of als fysieke servers. Azure Site Recovery kan zowel fysieke als virtuele implementaties naar Azure beschermen.
Aangezien XenApp 7.7 of hoger wordt ondersteund in Azure, kunnen alleen implementaties met deze versies worden uitgevoerd naar Azure voor disaster recovery of migratie.

### <a name="things-to-keep-in-mind"></a>Zaken om rekening mee te houden

1. Ondersteuning voor bescherming en herstel van on-premises implementaties met serveros-machines om xenApp-gepubliceerde apps te leveren en xenApp-gepubliceerde desktops worden ondersteund.

2. Bescherming en herstel van on-premises implementaties met desktop-os-machines om desktop-VDI te leveren voor virtuele clientdesktops, waaronder Windows 10, wordt niet ondersteund. Dit komt omdat Site Recovery het herstel van machines met desktop OS'es niet ondersteunt.  Ook een aantal client virtuele desktop besturingssystemen (bijv. Windows 7) worden nog niet ondersteund voor licenties in Azure. [Meer informatie](https://azure.microsoft.com/pricing/licensing-faq/) over licentieverlening voor clientbureaubladen en serverdesktops in Azure.

3.  Azure Site Recovery kan bestaande on-premises MCS- of PVS-klonen niet repliceren en beveiligen.
U moet deze klonen opnieuw maken met Azure RM-inrichting van de Delivery-controller.

4. NetScaler kan niet worden beveiligd met Azure Site Recovery, omdat NetScaler is gebaseerd op FreeBSD en Azure Site Recovery geen ondersteuning biedt voor bescherming van FreeBSD OS. U moet een nieuw NetScaler-toestel implementeren en configureren vanaf Azure Market-plaats na een fail-over naar Azure.


## <a name="replicating-virtual-machines"></a>Virtuele machines repliceren

De volgende onderdelen van de Citrix XenApp-implementatie moeten worden beschermd om replicatie en herstel mogelijk te maken.

* Beveiliging van AD DNS-server
* Beveiliging van SQL-databaseserver
* Bescherming van Citrix Delivery Controller
* Beveiliging van StoreFront-server.
* Bescherming van XenApp Master (VDA)
* Bescherming van Citrix XenApp License Server


**AD DNS-serverreplicatie**

Raadpleeg [Active Directory en DNS met Azure Site Recovery](site-recovery-active-directory.md) op richtlijnen voor het repliceren en configureren van een domeincontroller in Azure.

**SQL-databaseserverreplicatie**

Raadpleeg [SQL Server beveiligen met SQL Server disaster recovery en Azure Site Recovery](site-recovery-sql.md) voor gedetailleerde technische richtlijnen over de aanbevolen opties voor het beveiligen van SQL-servers.

Volg [deze richtlijnen](site-recovery-vmware-to-azure.md) om te beginnen met het repliceren van de andere virtuele onderdelenmachines naar Azure.

![Bescherming van XenApp-componenten](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Reken- en netwerkinstellingen**

Nadat de machines zijn beveiligd (status wordt weergegeven als 'Beveiligd' onder gerepliceerde items), moeten de compute- en netwerkinstellingen worden geconfigureerd.
In de eigenschappen Compute en Network > Compute u de naam en de doelgrootte van Azure VM opgeven.
Wijzig indien nodig de naam om te voldoen aan de Azure-vereisten. U ook informatie weergeven en toevoegen over het doelnetwerk, subnet en IP-adres dat wordt toegewezen aan de Azure VM.

Houd rekening met het volgende:

* U kunt het doel-IP-adres instellen. Als u geen adres opgeeft, maakt de machine waarvoor een failover is uitgevoerd, gebruik van DHCP. Als u een adres instelt dat niet beschikbaar is bij failover, werkt de failover niet. Hetzelfde doel-IP-adres kan worden gebruikt voor een testfailover als het adres beschikbaar is in het testfailovernetwerk.

* Als u voor de AD/DNS-server het on-premises adres behoudt, u hetzelfde adres opgeven als de DNS-server voor het Azure Virtual-netwerk.

Het aantal netwerkadapters wordt bepaald door de grootte die u voor de virtuele doelmachine opgeeft. Dat werkt als volgt:

*   Als het aantal netwerkadapters op de bronmachine kleiner is dan of gelijk is aan het aantal adapters dat is toegestaan voor de grootte van de doelmachine, heeft het doel hetzelfde aantal adapters als de bron.
*   Als het aantal adapters voor de virtuele bronmachine meer is dan is toegestaan voor de doelgrootte, wordt het maximum voor de doelgrootte gebruikt.
* Als een bronmachine bijvoorbeeld twee netwerkadapters heeft en met de grootte van de doelmachine vier netwerkadapters worden ondersteund, heeft de doelcomputer twee adapters. Als de bronmachine twee adapters heeft, maar de grootte van de doelmachine slechts één adapter ondersteunt, heeft de doelmachine één adapter.
*   Als de virtuele machine meerdere netwerkadapters heeft, maken ze allemaal verbinding met hetzelfde netwerk.
*   Als de virtuele machine meerdere netwerkadapters heeft, wordt de eerste in de lijst de standaardnetwerkadapter in de virtuele Azure-machine.


## <a name="creating-a-recovery-plan"></a>Een herstelplan maken

Nadat replicatie is ingeschakeld voor de VM's van xenApp-component, is de volgende stap het maken van een herstelplan.
Een herstelplan groepeert virtuele machines met vergelijkbare vereisten voor failover en herstel.  

**Stappen om een herstelplan te maken**

1. Voeg de virtuele machines van de XenApp-component toe aan het herstelplan.
2. Klik op Herstelplannen -> + Herstelplan. Geef een intuïtieve naam voor het herstelplan.
3. Voor virtuele VMware-machines: selecteer bron als VMware-processerver, target als Microsoft Azure en implementatiemodel als Resource Manager en klik op Items selecteren.
4. Voor virtuele machines van Hyper-V: Selecteer bron als VMM-server, target als Microsoft Azure en implementatiemodel als Resource Manager en klik op Items selecteren en selecteer vervolgens de VM's voor xenApp-implementatie.

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuele machines toevoegen aan failovergroepen

Herstelplannen kunnen worden aangepast om failovergroepen toe te voegen voor specifieke opstartvolgorde, scripts of handmatige acties. De volgende groepen moeten worden toegevoegd aan het herstelplan.

1. Failovergroep1: AD DNS
2. Failovergroep2: SQL Server VM's
2. Failovergroep3: VDA Master Image VM
3. Failover Group4: Delivery Controller en StoreFront server VM's


### <a name="adding-scripts-to-the-recovery-plan"></a>Scripts toevoegen aan het herstelplan

Scripts kunnen worden uitgevoerd voor of na een specifieke groep in een herstelplan. Handmatige acties kunnen ook worden opgenomen en uitgevoerd tijdens failover.

Het aangepaste herstelplan ziet er als volgt uit:

1. Failovergroep1: AD DNS
2. Failovergroep2: SQL Server VM's
3. Failovergroep3: VDA Master Image VM

   >[!NOTE]     
   >Stap 4, 6 en 7 met handmatige of scriptacties zijn alleen van toepassing op een on-premises XenApp->omgeving met MCS/PVS-catalogi.

4. Handleiding- of scriptactie groep 3: VDA VM afsluiten.
De Master VDA VM wanneer deze niet is overgegaan naar Azure, is in een status van uitvoeren. Als u nieuwe MCS-catalogi wilt maken met Azure-hosting, moet de master-VDA VM zich in de status Gestopt (toegewezen) bevinden. Sluit de VM af vanuit Azure-portal.

5. Failover Group4: Delivery Controller en StoreFront server VM's
6. Group3-handleiding of scriptactie 1:

    ***Azure RM-hostverbinding toevoegen***

    Maak Azure-hostverbinding in de machine delivery controller om nieuwe MCS-catalogi in Azure te voorzien. Volg de stappen zoals uitgelegd in dit [artikel](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. Group3-handleiding of scriptactie 2:

    ***McS-catalogi opnieuw maken in Azure***

    De bestaande MCS- of PVS-klonen op de primaire site worden niet gerepliceerd naar Azure. U moet deze klonen opnieuw maken met behulp van de gerepliceerde master VDA en Azure-inrichting van de Delivery-controller. Volg de stappen zoals uitgelegd in dit [artikel](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) om MCS-catalogi in Azure te maken.

![Herstelplan voor XenApp-componenten](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >U scripts op [locatie](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) gebruiken om de DNS bij te werken met de nieuwe IP's van de mislukte >virtuele machines of om indien nodig een load balancer op de defecte virtuele machine te bevestigen.


## <a name="doing-a-test-failover"></a>Het doen van een test failover

Volg [deze richtlijnen](site-recovery-test-failover-to-azure.md) om een testfailover te doen.

![Herstelplan](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Het doen van een failover

Volg [deze richtlijnen](site-recovery-failover.md) wanneer u bezig bent met een failover.

## <a name="next-steps"></a>Volgende stappen

In deze whitepaper [vindt u meer informatie](https://aka.ms/citrix-xenapp-xendesktop-with-asr) over het repliceren van Citrix XenApp- en XenDesktop-implementaties. Bekijk de richtlijnen voor het repliceren van [andere toepassingen](site-recovery-workload.md) met Site Recovery.
