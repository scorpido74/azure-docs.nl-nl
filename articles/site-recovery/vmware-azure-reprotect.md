---
title: Vm's van Azure naar een on-premises site opnieuw beveiligen tijdens nood herstel van virtuele VMware-machines en fysieke servers | Microsoft Docs
description: Na een failover naar Azure tijdens het herstel na nood gevallen van virtuele VMware-machines en fysieke servers, leert u hoe u een failback kunt uitvoeren van Azure naar de on-premises site.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: mayg
ms.openlocfilehash: cf1ccdf953781ca9b9bd17152f2cf32677997d12
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791808"
---
# <a name="reprotect-and-fail-back-machines-to-an-on-premises-site-after-failover-to-azure"></a>Machines opnieuw beveiligen en failback naar een on-premises site na een failover naar Azure

Na een [failover](site-recovery-failover.md) van on-premises virtuele VMware-machines of fysieke servers naar Azure, is de eerste stap bij het uitvoeren van een failback naar uw on-premises site het opnieuw beveiligen van de Azure-vm's die zijn gemaakt tijdens de failover. In dit artikel wordt beschreven hoe u dit doet. 

Bekijk de volgende video over het uitvoeren van een failover van Azure naar een on-premises site voor een beknopt overzicht.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-begin"></a>Voordat u begint

Als u een sjabloon hebt gebruikt om uw virtuele machines te maken, moet u ervoor zorgen dat elke virtuele machine een eigen UUID voor de schijven heeft. Als de UUID van de on-premises virtuele machine strijdig is met de UUID van het hoofd doel, omdat beide zijn gemaakt op basis van dezelfde sjabloon, mislukt de beveiliging. Implementeer een ander hoofd doel dat niet is gemaakt op basis van dezelfde sjabloon. Let op de volgende informatie:
- Als u een failback wilt uitvoeren naar een andere vCenter, moet u ervoor zorgen dat uw nieuwe vCenter en de hoofddoel server worden gedetecteerd. Een typisch symptoom is dat de gegevens opslag niet toegankelijk is of niet zichtbaar is in het dialoog venster opnieuw **beveiligen** .
- Als u terug wilt repliceren naar on-premises, hebt u een beleid voor failback nodig. Dit beleid wordt automatisch gemaakt wanneer u een doorstuur richtings beleid maakt. Let op de volgende informatie:
    - Dit beleid wordt automatisch gekoppeld aan de configuratie server tijdens het maken.
    - Dit beleid kan niet worden bewerkt.
    - De ingestelde waarden van het beleid zijn (RPO-drempel waarde = 15 minuten, Bewaar periode van het herstel punt = 24 uur, app-consistentie frequentie voor moment opnamen = 60 minuten).
- Tijdens het opnieuw beveiligen en failback moet de on-premises configuratie server actief en verbonden zijn.
- Als een vCenter-Server de virtuele machines beheert waarvoor u een failback wilt uitvoeren, moet u ervoor zorgen dat u over de [vereiste machtigingen](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) beschikt voor de detectie van Vm's op vCenter-servers.
- Verwijder moment opnamen op de hoofddoel server voordat u opnieuw beveiligt. Als moment opnamen aanwezig zijn op het on-premises hoofd doel of op de virtuele machine, mislukt de beveiliging. De moment opnamen op de virtuele machine worden automatisch samengevoegd tijdens een taak voor opnieuw beveiligen.
- Alle virtuele machines van een replicatie groep moeten van hetzelfde type besturings systeem (alle Windows of alle Linux) zijn. Een replicatie groep met gemengde besturings systemen wordt momenteel niet ondersteund voor opnieuw beveiligen en failback naar on-premises. Dit komt doordat het hoofd doel van hetzelfde besturings systeem als de virtuele machine moet zijn. Alle virtuele machines van een replicatie groep moeten hetzelfde hoofd doel hebben. 
- Het hoofd doel moet dezelfde of een hogere versie van het besturings systeem hebben dan de versies van het besturings systeem van de gerepliceerde items.
- Een configuratie server is on-premises vereist wanneer er een failback wordt uitgevoerd. Tijdens het failback moet de virtuele machine aanwezig zijn in de data base van de configuratie server. Anders mislukt de failback. Zorg ervoor dat u regel matig geplande back-ups maakt van uw configuratie server. Als er een nood geval is, herstelt u de server met hetzelfde IP-adres, zodat failback werkt. 
- Voor het opnieuw beveiligen en failback is een site-naar-site (S2S) VPN-of ExpressRoute-peering vereist om gegevens te repliceren. Geef het netwerk op zodat de virtuele machines waarvoor failover is uitgevoerd in azure, de on-premises configuratie server kunnen bereiken. U moet een proces server implementeren in het Azure-netwerk van de virtuele machine (s) waarvoor failover is uitgevoerd. Deze proces server moet ook kunnen communiceren met de on-premises configuratie server en de hoofddoel server.
- Als de IP-adressen van gerepliceerde items op failover worden bewaard, moeten er een S2S-of ExpressRoute-verbinding tot stand worden gebracht tussen virtuele Azure-machines en de failback-NIC van de configuratie server. Houd er rekening mee dat voor het bewaren van IP-adressen twee Nic's moeten worden geconfigureerd voor de connectiviteit van de bron machines en één voor Azure-failback-connectiviteit. Dit is om te voor komen dat de adresbereiken van de bron en failover van virtuele machines overlappen.
- Zorg ervoor dat u de volgende poorten voor failover en failback opent:

    ![Poorten voor failover en failback](./media/vmware-azure-reprotect/failover-failback.png)

- Lees alle [vereisten voor poorten en URL-white list](vmware-azure-deploy-configuration-server.md#prerequisites).

## <a name="deploy-a-process-server-in-azure"></a>Een proces server in azure implementeren

U hebt een proces server in azure nodig voordat u een failback naar uw on-premises site hebt:

- De proces server ontvangt gegevens van de beveiligde virtuele machine (s) in azure, waarna gegevens worden verzonden naar de on-premises site.
- Er is een netwerk met lage latentie vereist tussen de proces server en de beveiligde virtuele machine. Daarom is het raadzaam een proces server in azure te implementeren. De prestaties van de replicatie zijn hoger als de proces server dichter bij de virtuele machine die wordt gerepliceerd (de failover-machine in Azure). 
- Voor een proef van het concept kunt u de on-premises proces server en ExpressRoute met privé-peering gebruiken.

Een proces server in azure implementeren:

1. Als u een proces server in azure wilt implementeren, raadpleegt u [een proces server in azure instellen voor failback](vmware-azure-set-up-process-server-azure.md).
2. De Azure-Vm's verzenden replicatie gegevens naar de proces server. Configureer netwerken zodat de virtuele machines van Azure de proces server kunnen bereiken.
3. Houd er rekening mee dat replicatie van Azure naar on-premises alleen kan plaatsvinden via de S2S VPN of via de persoonlijke peering van uw ExpressRoute-netwerk. Zorg ervoor dat er voldoende band breedte beschikbaar is via dat netwerk kanaal.

## <a name="deploy-a-separate-master-target-server"></a>Een afzonderlijke hoofddoel server implementeren

De hoofddoelserver ontvangt failbackgegevens. De master target-server wordt standaard uitgevoegd op de on-premises configuratieserver. Afhankelijk van het volume van mislukte-back-verkeer moet u echter mogelijk een afzonderlijke hoofddoel server voor failback maken. U maakt als volgt een:

* [Maak een Linux-hoofddoel server](vmware-azure-install-linux-master-target.md) voor failback van virtuele Linux-machines. Dit is vereist. Houd er rekening mee dat de hoofddoel server op LVM niet wordt ondersteund.
* U kunt eventueel een afzonderlijke hoofddoel server maken voor failback van Windows-VM'S. Hiervoor voert u Unified Setup opnieuw uit en selecteert u om een hoofddoel server te maken. [Meer informatie](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers). 

Nadat u een hoofddoel server hebt gemaakt, voert u de volgende taken uit:

- Als de virtuele machine on-premises aanwezig is op de vCenter-Server, moet de hoofddoel server toegang hebben tot het VMDK-bestand (virtuele-machine schijf) van de on-premises virtuele machine. Toegang is vereist om de gerepliceerde gegevens naar de schijven van de virtuele machine te schrijven. Controleer of de gegevens opslag van de on-premises virtuele machine is gekoppeld op de host van het hoofd doel met lees-en schrijf toegang.
- Als de virtuele machine niet on-premises op de vCenter-Server aanwezig is, moet de Site Recovery-service een nieuwe virtuele machine maken tijdens het opnieuw beveiligen. Deze virtuele machine wordt gemaakt op de ESX-host waarop u het hoofd doel maakt. Kies zorgvuldig de ESX-host, zodat de virtuele machine voor failback wordt gemaakt op de host die u wilt.
- U kunt Storage vMotion niet gebruiken voor de hoofddoel server. Het gebruik van Storage vMotion voor de hoofddoel server kan ertoe leiden dat de failback mislukt. De virtuele machine kan niet worden gestart omdat de schijven niet beschikbaar zijn. Als u wilt voor komen dat dit gebeurt, sluit u de hoofddoel servers uit vanuit uw vMotion-lijst.
- Als een Master doel een Storage vMotion-taak ondergaat na het opnieuw beveiligen, worden de beveiligde virtuele-machine schijven die zijn gekoppeld aan het hoofd doel gemigreerd naar het doel van de vMotion-taak. Als u een failback-bewerking wilt uitvoeren, mislukt de ontkoppeling van de schijf omdat de schijven niet zijn gevonden. Vervolgens wordt het lastig om de schijven in uw opslag accounts te vinden. U moet deze hand matig zoeken en koppelen aan de virtuele machine. Daarna kan de on-premises virtuele machine worden opgestart.
- Voeg een Bewaar station toe aan uw bestaande Windows-hoofddoel server. Voeg een nieuwe schijf toe en Format teer het station. Het Bewaar station wordt gebruikt om de tijdstippen te stoppen waarop de virtuele machine terugkeert naar de on-premises site. Hier volgen enkele criteria van een Bewaar station. Als niet aan deze criteria wordt voldaan, wordt het station niet vermeld voor de hoofddoel server:
    - Het volume wordt niet gebruikt voor andere doel einden, zoals een doel van replicatie.
    - Het volume bevindt zich niet in de vergrendelings modus.
    - Het volume is geen cache volume. De Master doel installatie kan niet bestaan op dat volume. Het aangepaste installatie volume voor de proces server en het hoofd doel komt niet in aanmerking voor een Bewaar volume. Wanneer de proces server en het hoofd doel op een volume zijn geïnstalleerd, is het volume een cache volume van het hoofd doel.
    - Het bestandssysteem type van het volume is niet FAT of FAT32.
    - De volume capaciteit is niet nul.
    - Het standaard retentie volume voor Windows is het R-volume.
    - Het standaard Bewaar volume voor Linux is/mnt/retention.
- U moet een nieuw station toevoegen als u een bestaande proces server/configuratie server machine of een schaal-of proces server/Master doel server machine gebruikt. Het nieuwe station moet voldoen aan de voor gaande vereisten. Als het Bewaar station niet aanwezig is, wordt het niet weer gegeven in de vervolg keuzelijst selectie in de portal. Nadat u een station aan het on-premises hoofd doel hebt toegevoegd, duurt het Maxi maal 15 minuten voordat het station wordt weer gegeven in de selectie in de portal. U kunt de configuratie server ook vernieuwen als het station niet meer dan 15 minuten wordt weer gegeven.
- Installeer VMware-hulpprogram ma's of open-vm-hulpprogram ma's op de hoofddoel server. Zonder de hulpprogram ma's kunnen de gegevens opslag op de ESXi-host van het hoofd doel niet worden gedetecteerd.
- Stel de instelling `disk.EnableUUID=true` in de configuratie parameters van de virtuele Master doel machine in VMware in. Als deze rij niet bestaat, voegt u deze toe. Deze instelling is vereist om een consistente UUID naar de VMDK te geven zodat deze correct wordt gekoppeld.
- Aan de ESX-host waarop het hoofd doel wordt gemaakt, moet ten minste één VMFS-Data Store (Virtual Machine File System) zijn gekoppeld. Als er geen VMFS-gegevens opslag zijn gekoppeld, is de **gegevens opslag** invoer op de pagina opnieuw beveiligen leeg en kunt u niet door gaan.
- De hoofddoel server kan geen moment opnamen op de schijven hebben. Als er moment opnamen zijn, mislukt het opnieuw beveiligen en failback.
- Het hoofd doel kan geen Pvscsi SCSI-controller hebben. De controller kan alleen een LSI Logic-Controller zijn. Zonder een LSI Logic-Controller mislukt het opnieuw beveiligen.
- Voor elk exemplaar kan aan het hoofd doel Maxi maal 60 schijven zijn gekoppeld. Als het aantal virtuele machines dat opnieuw wordt beveiligd voor het on-premises hoofd doel meer dan een totaal aantal 60 schijven heeft, wordt de beveiliging van het hoofd doel opnieuw beveiligd. Zorg ervoor dat u voldoende Master doel schijf sleuven hebt of extra hoofddoel servers implementeert.
    

## <a name="enable-reprotection"></a>Opnieuw beveiligen inschakelen

Nadat een virtuele machine is opgestart in azure, kan het enige tijd duren voordat de agent de configuratie server opnieuw registreert (Maxi maal 15 minuten). Gedurende deze tijd kunt u niet opnieuw beveiligen en wordt een fout bericht weer gegeven dat de agent niet is geïnstalleerd. Als dit het geval is, wacht u een paar minuten en probeert u opnieuw te beveiligen:


1. Selecteer **kluis** > **gerepliceerde items**. Klik met de rechter muisknop op de virtuele machine waarvoor een failover is uitgevoerd en selecteer vervolgens **opnieuw beveiligen**. U kunt ook op de opdracht knoppen de machine selecteren en vervolgens **opnieuw beveiligen**selecteren.
2. Controleer of de **Azure to on-premises** richting van beveiliging is geselecteerd.
3. In **hoofddoel server** en **proces server**selecteert u de on-premises hoofddoel server en de proces server.  
4. Selecteer voor **gegevens opslag**de gegevens opslag waarnaar u de schijven on-premises wilt herstellen. Deze optie wordt gebruikt wanneer de on-premises virtuele machine wordt verwijderd en u moet nieuwe schijven maken. Deze optie wordt genegeerd als de schijven al bestaan. U moet nog een waarde opgeven.
5. Selecteer het Bewaar station.
6. Het failback-beleid wordt automatisch geselecteerd.
7. Selecteer **OK** om te beginnen met opnieuw beveiligen. Er wordt een taak gestart voor het repliceren van de VM van Azure naar de on-premises site. U kunt de voortgang volgen op het tabblad **taken** . Wanneer de beveiliging is geslaagd, wordt de status van de virtuele machine beveiligd.

Let op de volgende informatie:
- Als u wilt herstellen naar een andere locatie (wanneer de on-premises virtuele machine wordt verwijderd), selecteert u het Bewaar station en de gegevens opslag die zijn geconfigureerd voor de hoofddoel server. Wanneer u een failback naar de on-premises site maakt, gebruiken de virtuele VMware-machines in het failback-beveiligings plan dezelfde gegevens opslag als de hoofddoel server. Er wordt vervolgens een nieuwe virtuele machine gemaakt in vCenter.
- Als u de virtuele machine op Azure wilt herstellen naar een bestaande on-premises virtuele machine, koppelt u de gegevens opslag van de on-premises virtuele machine met lees-en schrijf toegang op de ESXi-host van de hoofddoel server.

    ![Het dialoog venster opnieuw beveiligen](./media/vmware-azure-reprotect/reprotectinputs.png)

- U kunt ook opnieuw beveiligen op het niveau van een herstel plan. Een replicatie groep kan alleen opnieuw worden beveiligd via een herstel plan. Wanneer u een herstel plan opnieuw beveiligt, moet u de waarden voor elke beveiligde machine opgeven.
- Gebruik de dezelfde hoofddoelserver om een replicatiegroep opnieuw te beveiligen. Als u een andere hoofddoel server gebruikt om een replicatie groep opnieuw te beveiligen, kan de server geen gemeen schappelijke punt in de tijd bieden.
- De on-premises virtuele machine is uitgeschakeld tijdens het opnieuw beveiligen. Dit helpt ervoor te zorgen dat gegevens tijdens de replicatie consistent blijven. Schakel de virtuele machine niet in nadat de beveiliging is voltooid.



## <a name="common-issues"></a>Algemene problemen

- Als u een alleen-lezen gebruikers-vCenter-detectie uitvoert en virtuele machines beveiligt, is de beveiliging geslaagd en werkt failover. Tijdens het opnieuw beveiligen mislukt de failover omdat de gegevens opslag niet kan worden gedetecteerd. Een symptoom is dat de gegevens opslag niet wordt weer gegeven tijdens het opnieuw beveiligen. U kunt dit probleem oplossen door de vCenter-referenties bij te werken met een geschikt account dat machtigingen heeft en de taak vervolgens opnieuw uit te voeren. 
- Wanneer u een back-up van een virtuele Linux-machine maakt en deze on-premises uitvoert, kunt u zien dat het pakket met de netwerk beheerder is verwijderd van de computer. Dit wordt veroorzaakt doordat het pakket voor netwerk beheer wordt verwijderd wanneer de virtuele machine wordt hersteld in Azure.
- Wanneer een virtuele Linux-machine is geconfigureerd met een statisch IP-adres en failover naar Azure wordt uitgevoerd, wordt het IP-adres verkregen van DHCP. Wanneer u een failover naar on-premises doorvoert, blijft de virtuele machine DHCP gebruiken voor het verkrijgen van het IP-adres. Meld u hand matig aan bij de computer en stel het IP-adres vervolgens weer in op een statisch adres, indien nodig. Een virtuele Windows-machine kan het vaste IP-adres opnieuw verkrijgen.
- Als u de ESXi 5,5 Free Edition of de gratis versie van vSphere 6 Hyper Visor gebruikt, mislukt de failover, maar failback mislukt. Als u failback wilt inschakelen, moet u een upgrade uitvoeren naar de evaluatie licentie van het programma.
- Als u de configuratie server niet kunt bereiken vanaf de proces server, gebruikt u Telnet om de verbinding met de configuratie server op poort 443 te controleren. U kunt ook proberen de configuratie server te pingen vanaf de proces server. Een proces server moet ook een heartbeat hebben als deze is verbonden met de configuratie server.
- Een Windows Server 2008 R2 SP1-server die wordt beveiligd als een fysieke on-premises server kan niet worden teruggezet van Azure naar een on-premises site.
- U kunt geen failback uitvoeren in de volgende situaties:
    - U hebt computers gemigreerd naar Azure. [Meer informatie](migrate-overview.md#what-do-we-mean-by-migration).
    - U hebt een virtuele machine naar een andere resource groep verplaatst.
    - U hebt de Azure-VM verwijderd.
    - U hebt de beveiliging van de virtuele machine uitgeschakeld.
    - U hebt de virtuele machine hand matig in azure gemaakt. De machine moet eerst on-premises en failover naar Azure worden uitgevoerd voordat de beveiliging opnieuw wordt beschermd.
    - U kunt alleen een failover uitvoeren naar een ESXi-host. U kunt geen failback uitvoeren voor virtuele VMware-machines of fysieke servers naar Hyper-V-hosts, fysieke machines of VMware-werk stations.


## <a name="next-steps"></a>Volgende stappen

Nadat de virtuele machine een beveiligde status heeft ingevoerd, kunt u [een failback initiëren](vmware-azure-failback.md). De failback wordt op de virtuele machine in azure afgesloten en de on-premises virtuele machine wordt opgestart. Verwachte uitval tijd voor de toepassing. Kies een tijd voor failback wanneer de toepassing downtime kan verdragen.


