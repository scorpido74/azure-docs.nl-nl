---
title: VMware VM's voorbereiden op herbescherming en failback met Azure Site Recovery
description: Voorbereiden op fail back van VMware VM's na failover met Azure Site Recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 5a330f8cba31640d0116ca3d5ccab352ce5b3509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257184"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>Voorbereiden op herbescherming en failback van VMware VM's

Na [een failover](site-recovery-failover.md) van on-premises Vm's vmware of fysieke servers naar Azure, beschermt u de Azure VM's die na failover zijn gemaakt opnieuw, zodat ze worden gerepliceerd naar de on-premises site. Met replicatie van Azure naar on-premises u vervolgens opnieuw mislukken door een failover van Azure naar on-premises uit te voeren wanneer u er klaar voor bent.

Voordat u verdergaat, krijgt u een snel overzicht met deze video over hoe u falen van Azure naar een on-premises site.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>Onderdelen voor herbescherming/failback

U hebt een aantal onderdelen en instellingen nodig voordat u azure opnieuw beschermen en teruglaten.

**Component**| **Details**
--- | ---
**On-premises configuratieserver** | De on-premises configuratieserver moet worden uitgevoerd en verbonden met Azure.<br/><br/> De VM die u niet meer wilt gebruiken, moet bestaan in de configuratieserverdatabase. Als de configuratieserver wordt getroffen, herstelt u deze met hetzelfde IP-adres om ervoor te zorgen dat failback werkt.<br/><br/>  Als IP-adressen van gerepliceerde machines bij failover zijn bewaard, moet site-to-site-connectiviteit (of ExpressRoute-connectiviteit) worden vastgesteld tussen Azure VM's-machines en de failback-NIC van de configuratieserver. Voor bewaarde IP-adressen heeft de configuratieserver twee NIC's nodig: één voor bronmachineconnectiviteit en één voor Azure-failback-connectiviteit. Dit voorkomt overlapping van subnetadresbereiken voor de bron en mislukt ten opzichte van VM's.
**Processerver in Azure** | U hebt een processerver in Azure nodig voordat u niet meer op uw on-premises site reageren.<br/><br/> De processerver ontvangt gegevens van de beveiligde Azure VM en stuurt deze naar de on-premises site.<br/><br/> U hebt een netwerk met lage latentie nodig tussen de processerver en de beveiligde vm, dus we raden u aan de processerver in Azure te implementeren voor hogere replicatieprestaties.<br/><br/> Voor proof-of-concept u de on-premises processerver en ExpressRoute gebruiken met private peering.<br/><br/> De processerver moet zich in het Azure-netwerk bevinden waarin de fout boven vm zich bevindt. De processerver moet ook kunnen communiceren met de on-premises configuratieserver en hoofddoelserver.
**Afzonderlijke hoofddoelserver** | De hoofddoelserver ontvangt failbackgegevens en standaard wordt een Windows-hoofddoelserver uitgevoerd op de on-premises configuratieserver.<br/><br/> Aan een hoofddoelserver kan maximaal 60 schijven zijn gekoppeld. VM's die zijn mislukt, hebben meer dan een gezamenlijk totaal van 60 schijven, of als u grote hoeveelheden verkeer niet meer hebt, maakt u een afzonderlijke hoofddoelserver voor failback.<br/><br/> Als machines worden verzameld in een replicatiegroep voor multi-VM-consistentie, moeten de VM's allemaal Windows zijn of moeten ze allemaal Linux zijn. Hoe komt dat? Omdat alle VM's in een replicatiegroep dezelfde hoofddoelserver moeten gebruiken en de hoofddoelserver hetzelfde besturingssysteem moet hebben (met dezelfde of een hogere versie) dan die van de gerepliceerde machines.<br/><br/> De hoofddoelserver mag geen momentopnamen op de schijven hebben, anders werkt herbescherming en failback niet.<br/><br/> Het hoofddoel kan geen Paravirtual SCSI-controller hebben. De controller kan alleen een LSI Logic-controller zijn. Zonder een LSI Logic-controller mislukt herbescherming.
**Failback-replicatiebeleid** | Als u terug wilt repliceren naar de on-premises site, hebt u een failback-beleid nodig. Dit beleid wordt automatisch gemaakt wanneer u een replicatiebeleid maakt voor Azure.<br/><br/> Het beleid wordt automatisch gekoppeld aan de configuratieserver. Het is ingesteld op een RPO-drempel van 15 minuten, herstelpuntretentie van 24 uur en een app-consistente momentopnamefrequentie van 60 minuten. Het beleid kan niet worden bewerkt. 
**Site-to-site VPN/ExpressRoute private peering** | Herbescherming en failback heeft een site-to-site VPN-verbinding nodig, of ExpressRoute private peering om gegevens te repliceren. 


## <a name="ports-for-reprotectionfailback"></a>Poorten voor herbescherming/failback

Een aantal poorten moet open staan voor herbescherming/failback. De volgende afbeelding illustreert de poorten en reprotect/failback-stroom.

![Poorten voor failover en failback](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>Een processerver implementeren in Azure

1. [Stel een processerver](vmware-azure-set-up-process-server-azure.md) in Azure in voor failback.
2. Controleer of Azure VM's de processerver kunnen bereiken. 
3. Zorg ervoor dat de site-to-site VPN-verbinding of expressroute private peering-netwerk voldoende bandbreedte heeft om gegevens van de processerver naar de on-premises site te verzenden.

## <a name="deploy-a-separate-master-target-server"></a>Een afzonderlijke hoofddoelserver implementeren

1. Let op de [vereisten en beperkingen](#reprotectionfailback-components)van de hoofddoelserver.
2. Maak een [Windows-](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) of Linux-hoofddoelserver, om het besturingssysteem van de VM's die u opnieuw wilt beschermen en terug te laten mislukken, aan te passen. [Linux](vmware-azure-install-linux-master-target.md)
3. Zorg ervoor dat u Storage vMotion niet gebruikt voor de hoofddoelserver, of failback kan mislukken. De VM-machine kan niet worden gestart omdat de schijven niet beschikbaar zijn.
    - Sluit de hoofddoelserver uit in uw vMotion-lijst om dit te voorkomen.
    - Als een hoofddoel een opslagvMotion-taak ondergaat na herbescherming, migreren de beveiligde VM-schijven die zijn gekoppeld aan de hoofddoelserver naar het doel van de vMotion-taak. Als u hierna probeert terug te falen, mislukt schijfloslating omdat de schijven niet worden gevonden. Het is dan moeilijk om de schijven in uw opslagaccounts te vinden. Als dit gebeurt, vindt u ze handmatig en bevestigt u ze aan de VM. Daarna kan de on-premises VM worden opgestart.

4. Voeg een bewaarstation toe aan de bestaande Windows-hoofddoelserver. Voeg een nieuwe schijf toe en maak het station op. Het bewaarstation wordt gebruikt om de punten in de tijd te stoppen wanneer de VM terugrepliceert naar de on-premises site. Let op deze criteria. Als deze niet worden gehaald, wordt het station niet weergegeven voor de hoofddoelserver:
    - Het volume wordt niet gebruikt voor andere doeleinden, zoals een replicatiedoel, en het is niet in de vergrendelingsmodus.
    - Het volume is geen cachevolume. Het aangepaste installatievolume voor de processerver en het hoofddoel komt niet in aanmerking voor een bewaarvolume. Wanneer de processerver en het hoofddoel op een volume zijn geïnstalleerd, is het volume een cachevolume van het hoofddoel.
    - Het type bestandssysteem van het volume is niet FAT of FAT32.
    - De volumecapaciteit is niet nul.
    - Het standaardretentievolume voor Windows is het R-volume.
    - Het standaard retentievolume voor Linux is /mnt/retention.

5. Voeg een station toe als u een bestaande processerver gebruikt. De nieuwe schijf moet voldoen aan de eisen in de laatste stap. Als het bewaarstation niet aanwezig is, wordt deze niet weergegeven in de vervolgkeuzelijst selectie op de portal. Nadat u een station hebt toegevoegd aan het on-premises hoofddoel, duurt het maximaal 15 minuten voordat het station wordt weergegeven in de selectie op de portal. U de configuratieserver vernieuwen als het station na 15 minuten niet wordt weergegeven.
6. Installeer VMware-hulpprogramma's of open-vm-tools op de hoofddoelserver. Zonder de tools kunnen de datastores op de ESXi-host van het hoofddoel niet worden gedetecteerd.
7. Stel de schijf in. Schakel de instelling UUID=true in in de configuratieparameters van de hoofddoel-VM in VMware. Als deze rij niet bestaat, voeg deze dan toe. Deze instelling is vereist om een consistente UUID aan de VMDK te verstrekken, zodat deze correct wordt gemonteerd.
8. Controleer vCenter Server-toegangsvereisten:
    - Als de VM waarop u niet teruggaat zich op een ESXi-host bevindt die wordt beheerd door VMware vCenter Server, heeft de hoofddoelserver toegang nodig tot het vm-bestand VMDK (On-premises VMDK) om de gerepliceerde gegevens naar de schijven van de virtuele machine te schrijven. Zorg ervoor dat het on-premises VM-gegevensarchief is gemonteerd op de hoofddoelhost met lees-/schrijftoegang.
    - Als de VM zich niet op een ESXi-host bevindt die wordt beheerd door een VMware vCenter Server, maakt Site Recovery een nieuwe VM tijdens herbeveiliging. Deze VM is gemaakt op de ESXi-host waarop u de hoofddoelserverVM maakt. Kies de ESXi-host zorgvuldig om de VM te maken op de gewenste host. De harde schijf van de VM moet zich in een gegevensopslag bevinden die toegankelijk is voor de host waarop de hoofddoelserver wordt uitgevoerd.
    - Een andere optie, als de on-premises VM al bestaat voor failback, is om deze te verwijderen voordat u een failback doet. Failback maakt vervolgens een nieuwe VM op dezelfde host als de master target ESXi-host. Wanneer u niet terugkeert naar een alternatieve locatie, worden de gegevens hersteld naar dezelfde gegevensarchief en dezelfde ESXi-host als die welke wordt gebruikt door de on-premises hoofddoelserver.
9. Voor fysieke machines die niet teruggaan naar VMware-VM's, moet u de detectie van de host waarop de hoofddoelserver wordt uitgevoerd voltooien, voordat u de machine opnieuw beveiligen.
10. Controleer of de ESXi-host waarop de master target VM ten minste één VMFS-gegevensarchief (Virtual Machine File System) heeft, eraan is gekoppeld. Als er geen VMFS-gegevensopslag is gekoppeld, is de gegevensarchiefinvoer in de instellingen voor herbeveiliging leeg en u niet verder.


## <a name="next-steps"></a>Volgende stappen

Bescherm een vm [opnieuw.](vmware-azure-reprotect.md)
