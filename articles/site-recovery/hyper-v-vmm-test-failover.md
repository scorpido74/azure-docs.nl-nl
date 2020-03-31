---
title: Een NHyper-V-noodherstelboor uitvoeren naar een secundaire site met Azure Site Recovery
description: Meer informatie over het uitvoeren van een DR-drill voor Hyper-V VM's in VMM-clouds naar een secundair on-premises datacenter met Azure Site Recovery.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 0363911574a076b13cb72591fb2564364e096c76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257964"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Een DR-drill voor Hyper-V VM's uitvoeren naar een secundaire site


In dit artikel wordt beschreven hoe u een DR-drill (Disaster Recovery) doen voor Hyper-V VM's die worden beheerd in System Center Virtual Machine Manager V(MM)-clouds, naar een secundaire on-premises site met [Azure Site Recovery](site-recovery-overview.md).

U voert een testfailover uit om uw replicatiestrategie te valideren en een DR-drill uit te voeren zonder gegevensverlies of downtime. Een testfailover heeft geen invloed op de lopende replicatie of op uw productieomgeving. 

## <a name="how-do-test-failovers-work"></a>Hoe werken testfailovers?

U voert een testfailover uit van de primaire naar de secundaire site. Als u gewoon wilt controleren of een VM mislukt, u een testfailover uitvoeren zonder iets in te stellen op de secundaire site. Als u wilt controleren of de failovervan apps werkt zoals verwacht, moet u netwerken en infrastructuur instellen op de secundaire locatie.
- U een testfailover uitvoeren op één virtuele virtuele machine of op een [herstelplan.](site-recovery-create-recovery-plans.md)
- U een testfailover uitvoeren zonder netwerk, met een bestaand netwerk of met een automatisch gemaakt netwerk. Meer details over deze opties vindt u in de onderstaande tabel.
    - U een testfailover uitvoeren zonder netwerk. Deze optie is handig als u gewoon wilt controleren of een VM niet is geslaagd, maar u geen netwerkconfiguratie verifiëren.
    - Voer de failover uit met een bestaand netwerk. We raden u aan geen productienetwerk te gebruiken.
    - Voer de failover uit en laat Site Recovery automatisch een testnetwerk maken. In dit geval maakt Site Recovery het netwerk automatisch en ruimt het op wanneer de testfailover is voltooid.
- U moet een herstelpunt selecteren voor de testfailover: 
    - **Laatste verwerkt:** Met deze optie wordt een VM niet overgezet naar het laatste herstelpunt dat door Site Recovery is verwerkt. Deze optie heeft een lage RTO (Recovery Time Objective), omdat er geen tijd wordt besteed aan het verwerken van niet-verwerkte gegevens.
    - **Laatste app-consistent:** deze optie mislukt over een VM naar het nieuwste toepassingsconsistente herstelpunt dat is verwerkt door Site Recovery. 
    - **Laatste:** Deze optie verwerkt eerst alle gegevens die naar de siteherstelservice zijn verzonden, om een herstelpunt voor elke vm te maken voordat deze niet wordt verzonden. Deze optie biedt de laagste RPO (Herstelpuntdoelstelling), omdat de VM die na failover is gemaakt, alle gegevens wordt gerepliceerd naar Siteherstel wanneer de failover is geactiveerd.
    - **Laatste multi-VM verwerkt:** beschikbaar voor herstelplannen die een of meer VM's bevatten waarop multi-VM-consistentie is ingeschakeld. VM's met de ingeschakelde instelling slagen niet over op het meest recente algemene consistent herstelpunt voor meerdere VM's. Andere VM's niet over naar de laatste verwerkte herstelpunt.
    - **Nieuwste multi-VM app-consistent:** deze optie is beschikbaar voor herstelplannen met een of meer VM's waarop multi-VM-consistentie is ingeschakeld. VM's die deel uitmaken van een replicatiegroep, worden niet naar het meest recente algemene herstelpunt voor meerdere VM-toepassingen overgezet. Andere VM's slagen niet over hun nieuwste toepassingsconsistente herstelpunt.
    - **Aangepast:** gebruik deze optie om te mislukken over een specifieke vm naar een bepaald herstelpunt.



## <a name="prepare-networking"></a>Netwerknetwerken voorbereiden

Wanneer u een testfailover uitvoert, wordt u gevraagd netwerkinstellingen voor testreplicamachines te selecteren, zoals samengevat in de tabel.

| **Optie** | **Details** | |
| --- | --- | --- |
| **Geen** | De test-VM wordt gemaakt op de host waarop de replica-VM zich bevindt. Het wordt niet toegevoegd aan de cloud en is niet verbonden met een netwerk.<br/><br/> U de machine aansluiten op een VM-netwerk nadat deze is gemaakt.| |
| **Bestaande gebruiken** | De test-VM wordt gemaakt op de host waarop de replica-VM zich bevindt. Het wordt niet toegevoegd aan de cloud.<br/><br/>Maak een VM-netwerk dat is geïsoleerd van uw productienetwerk.<br/><br/>Als u een VLAN-netwerk gebruikt, raden we u aan hiervoor een apart logisch netwerk (niet in productie) in VMM te maken. Dit logische netwerk wordt gebruikt om VM-netwerken te maken voor testfailovers.<br/><br/>Het logische netwerk moet worden gekoppeld aan ten minste één van de netwerkadapters van alle Hyper-V-servers die virtuele machines hosten.<br/><br/>Voor logische VLAN-netwerken moeten de netwerksites die u aan het logische netwerk toevoegt, worden geïsoleerd.<br/><br/>Als u een logisch netwerk op basis van Windows Network-virtualisatie gebruikt, maakt Azure Site Recovery automatisch geïsoleerde VM-netwerken. | |
| **Een netwerk maken** | Er wordt automatisch een tijdelijk testnetwerk gemaakt op basis van de instelling die u opgeeft in **Logisch netwerk** en de bijbehorende netwerksites.<br/><br/> Failover controleert of VM's zijn gemaakt.<br/><br/> U moet deze optie gebruiken als een herstelplan meer dan één VM-netwerk gebruikt.<br/><br/> Als u Windows Network Virtualization-netwerken gebruikt, kan deze optie automatisch VM-netwerken maken met dezelfde instellingen (subnetten en IP-adresgroepen) in het netwerk van de virtuele replicamachine. Deze VM-netwerken worden automatisch opgeschoond nadat de testfailover is voltooid.<br/><br/> De test-VM wordt gemaakt op de host waarop de virtuele replicamachine bestaat. Het wordt niet toegevoegd aan de cloud.|

### <a name="best-practices"></a>Aanbevolen procedures

- Het testen van een productienetwerk zorgt voor downtime voor productieworkloads. Vraag uw gebruikers om gerelateerde apps niet te gebruiken wanneer de noodhersteloefening aan de gang is.

- Het testnetwerk hoeft niet overeen te komen met het logische netwerktype VMM dat wordt gebruikt voor een testfailover. Maar sommige combinaties werken niet:

     - Als de replica dhcp- en VLAN-isolatie gebruikt, heeft het VM-netwerk voor de replica geen statische IP-adresgroep nodig. Dus het gebruik van Windows Network Virtualisatie voor de test failover zal niet werken omdat er geen adres pools beschikbaar zijn. 
        
     - Testfailover werkt niet als het replicanetwerk geen isolatie gebruikt en het testnetwerk Windows Network Virtualization gebruikt. Dit komt omdat het no-isolation netwerk niet over de subnetten beschikt die nodig zijn om een Windows Network Virtualization network te maken.
        
- We raden u aan het netwerk dat u hebt geselecteerd voor netwerktoewijzing niet te gebruiken voor een testfailover.

- Hoe replica virtuele machines na een failover zijn verbonden met gekoppelde VM-netwerken, hangt af van de manier waarop het VM-netwerk is geconfigureerd in de VMM-console.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>VM-netwerk geconfigureerd zonder isolatie of VLAN-isolatie

Als een VM-netwerk is geconfigureerd in VMM zonder isolatie of VLAN-isolatie, moet u het volgende noteren:

- Als DHCP is gedefinieerd voor het VM-netwerk, is de virtuele replica-machine verbonden met de VLAN-id via de instellingen die zijn opgegeven voor de netwerksite in het bijbehorende logische netwerk. De virtuele machine ontvangt zijn IP-adres van de beschikbare DHCP-server.
- U hoeft geen statische IP-adresgroep voor het doel-VM-netwerk te definiëren. Als een statische IP-adresgroep wordt gebruikt voor het VM-netwerk, is de virtuele replica-machine verbonden met de VLAN-id via de instellingen die zijn opgegeven voor de netwerksite in het bijbehorende logische netwerk.
- De virtuele machine ontvangt zijn IP-adres uit de groep die is gedefinieerd voor het VM-netwerk. Als een statische IP-adresgroep niet is gedefinieerd op het doel-VM-netwerk, mislukt de toewijzing van IP-adres. Maak de IP-adresgroep op zowel de bron- als de doelVMM-servers die u gebruikt voor bescherming en herstel.

### <a name="vm-network-with-windows-network-virtualization"></a>VM-netwerk met Windows Network-virtualisatie

Als een VM-netwerk is geconfigureerd in VMM met Windows Network Virtualization, houdt u het volgende op:

- U moet een statische groep definiëren voor het doelVM-netwerk, ongeacht of het bron-VM-netwerk is geconfigureerd om DHCP of een statische IP-adresgroep te gebruiken. 
- Als u DHCP definieert, fungeert de doelvmm-server als DHCP-server en geeft het een IP-adres uit de groep die is gedefinieerd voor het doelVM-netwerk.
- Als het gebruik van een statische IP-adresgroep is gedefinieerd voor de bronserver, wijst de doel-VMM-server een IP-adres uit de groep toe. In beide gevallen mislukt de toewijzing van IP-adres als een statische IP-adresgroep niet is gedefinieerd.



## <a name="prepare-the-infrastructure"></a>De infrastructuur voorbereiden

Als u gewoon wilt controleren of een VM kan mislukken, u een testfailover uitvoeren zonder een infrastructuur. Als u een volledige DR-drill wilt doen om de failover van apps te testen, moet u de infrastructuur op de secundaire site voorbereiden:

- Als u een testfailover uitvoert met een bestaand netwerk, bereidt u Active Directory, DHCP en DNS in dat netwerk voor.
- Als u een testfailover uitvoert met de optie om automatisch een VM-netwerk te maken, moet u infrastructuurbronnen toevoegen aan het automatisch gemaakte netwerk voordat u de testfailover uitvoert. In een herstelplan u dit vergemakkelijken door een handmatige stap toe te voegen voor Groep-1 in het herstelplan dat u gaat gebruiken voor de failover van de test. Voeg vervolgens de infrastructuurbronnen toe aan het automatisch gemaakte netwerk voordat u de testfailover uitvoert.


### <a name="prepare-dhcp"></a>DHCP voorbereiden
Als de virtuele machines die betrokken zijn bij testfailover DHCP gebruiken, maakt u een test DHCP-server binnen het geïsoleerde netwerk met het oog op een testfailover.


### <a name="prepare-active-directory"></a>Active Directory voorbereiden
Als u een testfailover wilt uitvoeren voor het testen van toepassingen, hebt u een kopie van de productie-Active Directory-omgeving in uw testomgeving nodig. Controleer de overwegingen voor de [failover van](site-recovery-active-directory.md#test-failover-considerations)de test voor Active Directory voor meer informatie.

### <a name="prepare-dns"></a>DNS voorbereiden
Bereid een DNS-server als volgt voor op de testfailover:

* **DHCP**: Als virtuele machines DHCP gebruiken, moet het IP-adres van de test-DNS worden bijgewerkt op de test DHCP-server. Als u een netwerktype Windows Network-virtualisatie gebruikt, fungeert de VMM-server als dhcp-server. Daarom moet het IP-adres van DNS worden bijgewerkt in het failovernetwerk van de test. In dit geval registreren de virtuele machines zich bij de betreffende DNS-server.
* **Statisch adres:** Als virtuele machines een statisch IP-adres gebruiken, moet het IP-adres van de test-DNS-server worden bijgewerkt in het failovernetwerk van de test. Mogelijk moet u DNS bijwerken met het IP-adres van de virtuele testmachines. U hiervoor het volgende voorbeeldscript gebruiken:

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>Een testfailover uitvoeren

In deze procedure wordt beschreven hoe u een testfailover uitvoert voor een herstelplan. U de failover voor één virtuele machine ook uitvoeren op het tabblad **Virtuele machines.**

1. Selecteer **Herstelplannen** > *recoveryplan_name*. Klik op > **Failovertestfailover**. **Failover**
2. Geef op het **failoverblad test** aan hoe replica-VM's moeten worden aangesloten op netwerken na de failover van de test.
3. De voortgang van failover bijhouden op het tabblad **Vacatures.**
4. Nadat failover is voltooid, controleert u of de VM's met succes zijn gestart.
5. Wanneer u klaar bent, klikt u op **Failover opopruimen** voor het herstelplan. **Noteer**en sla eventuele waarnemingen in verband met de testfailover op en sla deze op. Met deze stap worden alle VM's en netwerken verwijderd die zijn gemaakt door Site Recovery tijdens de failover van de test. 

![Testfailover](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> Het IP-adres dat tijdens de failover aan een virtuele machine wordt gegeven, is hetzelfde IP-adres dat de virtuele machine zou ontvangen voor een geplande of ongeplande failover (ervan uitgaande dat het IP-adres beschikbaar is in het failovernetwerk van de test). Als hetzelfde IP-adres niet beschikbaar is in het failovernetwerk van de test, ontvangt de virtuele machine een ander IP-adres dat beschikbaar is in het failovernetwerk van de test.



### <a name="run-a-test-failover-to-a-production-network"></a>Een testfailover uitvoeren naar een productienetwerk

We raden u aan geen testfailover uit te voeren naar uw productieherstelsitenetwerk dat u hebt opgegeven tijdens het toewijzen van het netwerk. Maar als u de end-to-end netwerkconnectiviteit in een mislukte VM moet valideren, moet u de volgende punten noteren:

* Zorg ervoor dat de primaire vm wordt afgesloten wanneer u de testfailover doet. Als u dit niet doet, worden twee virtuele machines met dezelfde identiteit tegelijkertijd in hetzelfde netwerk uitgevoerd. Die situatie kan tot ongewenste gevolgen leiden.
* Wijzigingen die u aanbrengt in de vm's van de testfailover, gaan verloren wanneer u de testfailovervirtuele machines opruimt. Deze wijzigingen worden niet gerepliceerd naar de primaire VM's.
* Testen als dit leidt tot downtime voor uw productietoepassing. Vraag gebruikers van de toepassing de toepassing niet te gebruiken wanneer de DR-drill aan de gang is.  


## <a name="next-steps"></a>Volgende stappen
Nadat u een DR-drill hebt uitgevoerd, u [een volledige failover uitvoeren.](site-recovery-failover.md)



