---
title: Een testfailover uitvoeren (noodhersteldrill) naar Azure in Azure Site Recovery
description: Meer informatie over het uitvoeren van een testfailover van on-premises naar Azure, met behulp van de Azure Site Recovery-service.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 26c734b7a2e9f5592ee6d51dfee4650a3998ab1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257522"
---
# <a name="run-a-test-failover-disaster-recovery-drill-to-azure"></a>Een testfailover uitvoeren (noodherstelboor) naar Azure 


In dit artikel wordt beschreven hoe u een noodherstelboor uitvoert naar Azure, met behulp van een failovertest voor siteherstel.  

U voert een testfailover uit om uw replicatie- en disaster recovery-strategie te valideren, zonder verlies van gegevens of downtime. Een testfailover heeft geen invloed op de lopende replicatie of uw productieomgeving. U een testfailover uitvoeren op een specifieke virtuele machine (VM) of op een [herstelplan](site-recovery-create-recovery-plans.md) met meerdere VM's.


## <a name="run-a-test-failover"></a>Een testfailover uitvoeren
In deze procedure wordt beschreven hoe u een testfailover uitvoert voor een herstelplan. Als u een testfailover voor één virtuele virtuele machine wilt uitvoeren, voert u de [hier](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm) beschreven stappen uit

![Failover testen](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. Klik in Siteherstel in de Azure-portal op **Herstelplannen** > *recoveryplan_name* > **Failover test**.
2. Selecteer een **herstelpunt** waarnaar u wilt mislukken. U kunt een van de volgende opties gebruiken:
    - **Laatste verwerkt**: Deze optie mislukt over alle VM's in het plan om het laatste herstelpunt verwerkt door Site Recovery. Als u het nieuwste herstelpunt voor een specifieke virtuele machine wilt bekijken, schakelt u **De laatste herstelpunten** in de VM-instellingen in. Deze optie heeft een lage RTO (Recovery Time Objective), omdat er geen tijd wordt besteed aan het verwerken van niet-verwerkte gegevens.
    - **Laatste app-consistent:** deze optie mislukt over alle VM's in het plan naar het nieuwste toepassingsconsistente herstelpunt dat door Site Recovery is verwerkt. Als u het nieuwste herstelpunt voor een specifieke virtuele machine wilt bekijken, schakelt u **De laatste herstelpunten** in de VM-instellingen in.
    - **Laatste:** Deze optie verwerkt eerst alle gegevens die naar de siteherstelservice zijn verzonden, om een herstelpunt voor elke vm te maken voordat deze niet wordt verzonden. Deze optie biedt de laagste RPO (Herstelpuntdoelstelling), omdat de VM die na failover is gemaakt, alle gegevens wordt gerepliceerd naar Siteherstel wanneer de failover is geactiveerd.
    - **Laatste multi-VM verwerkt:** deze optie is beschikbaar voor herstelplannen met een of meer VM's waarop multi-VM-consistentie is ingeschakeld. VM's met de ingeschakelde instelling slagen niet over op het meest recente algemene consistent herstelpunt voor meerdere VM's. Andere VM's niet over naar de laatste verwerkte herstelpunt.  
    - **Nieuwste multi-VM app-consistent:** deze optie is beschikbaar voor herstelplannen met een of meer VM's waarop multi-VM-consistentie is ingeschakeld. VM's die deel uitmaken van een replicatiegroep, worden niet naar het meest recente algemene herstelpunt voor meerdere VM-toepassingen overgezet. Andere VM's slagen niet over hun nieuwste toepassingsconsistente herstelpunt.
    - **Aangepast:** gebruik deze optie om te mislukken over een specifieke vm naar een bepaald herstelpunt.
3. Selecteer een virtueel Azure-netwerk waarin test-VM's worden gemaakt.

    - Site recovery probeert testVM's te maken in een subnet met dezelfde naam en hetzelfde IP-adres als in de **compute- en netwerkinstellingen** van de VM.
    - Als een subnet met dezelfde naam niet beschikbaar is in het virtuele Azure-netwerk dat wordt gebruikt voor testfailover, wordt de test-vm alfabetisch gemaakt in het eerste subnet.
    - Als hetzelfde IP-adres niet beschikbaar is in het subnet, ontvangt de VM een ander beschikbaar IP-adres in het subnet. [Meer informatie](#create-a-network-for-test-failover).
4. Als u niet overgaat naar Azure en gegevensversleuteling is ingeschakeld, selecteert u in **Encryptiesleutel**het certificaat dat is uitgegeven toen u versleuteling inschakelt tijdens de installatie van de provider. U deze stap negeren als versleuteling niet is ingeschakeld.
5. De voortgang van failover bijhouden op het tabblad **Vacatures.** U moet de testreplicamachine kunnen zien in de Azure-portal.
6. Als u een RDP-verbinding met de Azure VM wilt starten, moet u [een openbaar IP-adres toevoegen](https://aka.ms/addpublicip) aan de netwerkinterface van de mislukte vm.
7. Wanneer alles werkt zoals verwacht, klikt u op **Failover opschonen.** Hiermee worden de VM's verwijderd die zijn gemaakt tijdens de failover van de test.
8. **Noteer**en sla eventuele waarnemingen in verband met de testfailover op en sla deze op.


![Failover testen](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Wanneer een testfailover wordt geactiveerd, treedt het volgende op:

1. **Voorwaarden**: Er wordt een vereiste controle uitgevoerd om ervoor te zorgen dat aan alle voorwaarden voor failover wordt voldaan.
2. **Failover:** De failover verwerkt en bereidde de gegevens voor, zodat er een Azure VM van kan worden gemaakt.
3. **Laatste:** Als u het laatste herstelpunt hebt gekozen, wordt een herstelpunt gemaakt op basis van de gegevens die naar de service zijn verzonden.
4. **Start:** met deze stap wordt een virtuele Azure-machine gemaakt met behulp van de gegevens die in de vorige stap zijn verwerkt.

### <a name="failover-timing"></a>Failovertiming

In de volgende scenario's vereist failover een extra tussenstap die meestal ongeveer 8 tot 10 minuten in beslag neemt:

* VMware VM's met een versie van de Mobiliteitsservice ouder dan 9.8
* Fysieke servers
* VMware Linux VM's
* Hyper-V VM beveiligd als fysieke servers
* VMware VM waarbij de volgende stuurprogramma's geen opstartstuurprogramma's zijn:
    * storvsc storvsc
    * vmbus
    * storflt storflt
    * intelide
    * Atapi
* VMware VM die DHCP niet heeft ingeschakeld, ongeacht of ze DHCP- of statische IP-adressen gebruiken.

In alle andere gevallen is geen tussenstap vereist en duurt failover aanzienlijk minder tijd.


## <a name="create-a-network-for-test-failover"></a>Een netwerk maken voor failovertest

Het wordt aangeraden om voor het testen van failover een netwerk te kiezen dat is geïsoleerd van het herstelnetwerk voor de productiesite dat is opgegeven in de instellingen voor **Berekening en netwerk** voor een VM. De standaardinstelling is dat bij het maken van een virtueel Azure-netwerk dit netwerk wordt geïsoleerd van andere netwerken. Het testnetwerk moet een exacte kopie zijn van uw productienetwerk:

- Het testnetwerk moet hetzelfde aantal subnetten hebben als uw productienetwerk. Subnetten moeten dezelfde naam hebben.
- Het testnetwerk moet hetzelfde IP-adresbereik gebruiken.
- Werk het DNS van het testnetwerk bij met het IP-adres dat voor de DNS-VM is opgegeven in de instellingen voor **Berekening en netwerk**. Lees [Testfailover-overwegingen voor Active Directory](site-recovery-active-directory.md#test-failover-considerations) voor meer informatie.


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Failover testen naar een productienetwerk op de herstelsite

Hoewel we u hebben aanbevolen een testnetwerk te gebruiken dat los staat van uw productienetwerk, moet u als u een noodherstelboor in uw productienetwerk wilt testen, het volgende noteren:

- Controleer of de primaire vm wordt afgesloten wanneer u de testfailover uitvoert. Anders zijn er twee VM's met dezelfde identiteit, die tegelijkertijd in hetzelfde netwerk worden uitgevoerd. Dit kan tot onverwachte gevolgen leiden.
- Wijzigingen in VM's die zijn gemaakt voor testfailover gaan verloren wanneer u de failover opruimt. Deze wijzigingen worden niet gerepliceerd naar de primaire vm.
- Testen in uw productieomgeving leidt tot een uitvaltijd van uw productietoepassing. Gebruikers mogen geen apps gebruiken die op VM's worden uitgevoerd wanneer de testfailover aan de gang is.  



## <a name="prepare-active-directory-and-dns"></a>Active Directory en DNS voorbereiden

Als u een testfailover wilt uitvoeren voor het testen van toepassingen, hebt u een kopie van uw productie-Active Directory-omgeving in uw testomgeving nodig. Lees [testfailoveroverwegingen voor Active Directory](site-recovery-active-directory.md#test-failover-considerations) voor meer informatie.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Voorbereiden op het verbinden met virtuele Azure-machines na een failover

Als u na failover verbinding wilt maken met Azure VM's via RDP/SSH, volgt u de vereisten die in de tabel zijn samengevat.

**Failover** | **Locatie** | **Acties**
--- | --- | ---
**Azure VM met Windows** | On-premises machine voor failover | Als u de Azure VM via internet wilt openen, schakelt u RDP in en controleert u of TCP- en UDP-regels voor **Openbaar**worden toegevoegd en dat RDP is toegestaan voor alle profielen in **windows firewall** > **toegestane apps**.<br/><br/> Als u toegang wilt krijgen tot de Azure VM via een site-to-site-verbinding, schakelt u RDP op de machine in en controleert u of RDP is toegestaan in de**apps en functies van**Windows **Firewall** -> toegestaan voor **domein- en privénetwerken.**<br/><br/>  Controleer of het SAN-beleid van het besturingssysteem is ingesteld op **OnlineAll.** [Meer informatie](https://support.microsoft.com/kb/3031135).<br/><br/> Zorg ervoor dat er geen Windows-updates in behandeling zijn op de VM wanneer u een failover activeert. Windows-update kan worden gestart wanneer u niet opnieuw bent en u zich pas aanmelden bij de virtuele machine als de update is voltooid.
**Azure VM met Windows** | Azure VM na failover |  [Voeg een openbaar IP-adres toe](https://aka.ms/addpublicip) voor de VM.<br/><br/> De regels voor de netwerkbeveiligingsgroep op de mislukte VM (en het Azure-subnet waarmee het is verbonden) moeten binnenkomende verbindingen met de RDP-poort toestaan.<br/><br/> Controleer **Diagnostische gegevens** opstarten om een schermafbeelding van de vm te verifiëren.<br/><br/> Als u geen verbinding maken, controleert u of de vm wordt uitgevoerd en controleert u deze [tips voor probleemoplossing.](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)
**Azure VM met Linux** | On-premises machine voor failover | Controleer of de Secure Shell-service op de VM is ingesteld om automatisch te starten bij het opstarten van het systeem.<br/><br/> Controleer of er in de firewallregels is ingesteld dat SSH-verbindingen zijn toegestaan.
**Azure VM met Linux** | Azure VM na failover | De regels voor de netwerkbeveiligingsgroep op de mislukte VM (en het Azure-subnet waarmee het is verbonden) moeten binnenkomende verbindingen met de SSH-poort toestaan.<br/><br/> [Voeg een openbaar IP-adres toe](https://aka.ms/addpublicip) voor de VM.<br/><br/> Controleer **Diagnostische gegevens** voor een schermafbeelding van de VM.<br/><br/>

Volg de stappen die [hier](site-recovery-failover-to-azure-troubleshoot.md) worden beschreven om eventuele verbindingsproblemen na een failover op te lossen.

## <a name="next-steps"></a>Volgende stappen
Nadat u een noodhersteloefening hebt voltooid, vindt u meer informatie over andere typen [failover.](site-recovery-failover.md)
