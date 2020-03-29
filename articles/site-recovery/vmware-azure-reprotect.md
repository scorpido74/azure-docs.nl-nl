---
title: VMware VM's opnieuw beveiligen naar een on-premises site met Azure Site Recovery
description: Meer informatie over het opnieuw beveiligen van VMware VM's na een fail-over naar Azure met Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 976888f57269cc9fe6107a38e30d78c73eb5c124
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257171"
---
# <a name="reprotect-from-azure-to-on-premises"></a>Opnieuw beveiligen van Azure naar on-premises

Na [een failover](site-recovery-failover.md) van on-premises Vm's vmware of fysieke servers naar Azure, is de eerste stap om niet terug te keren naar uw on-premises site, om de Azure VM's die tijdens failover zijn gemaakt, opnieuw te beschermen. In dit artikel wordt beschreven hoe u dit doen. 

## <a name="before-you-begin"></a>Voordat u begint

1. Volg de stappen in [dit artikel](vmware-azure-prepare-failback.md) om u voor te bereiden op herbescherming en failback, waaronder het instellen van een processerver in Azure en een on-premises hoofddoelserver, en het configureren van een site-to-site VPN, of ExpressRoute private peering, voor failback.
2. Controleer of de on-premises configuratieserver wordt uitgevoerd en verbonden is met Azure. Tijdens failover naar Azure is de on-premises site mogelijk niet toegankelijk en is de configuratieserver mogelijk niet beschikbaar of wordt deze afgesloten. Tijdens failback moet de VM in de configuratieserverdatabase bestaan. Anders is failback mislukt.
3. Verwijder alle momentopnamen op de on-premises hoofddoelserver. Reprotection werkt niet als er momentopnamen zijn.  De momentopnamen op de VM worden automatisch samengevoegd tijdens een taak voor herbeveiliging.
4. Als u VM's die zijn verzameld in een replicatiegroep opnieuw beschermt voor multi-VM-consistentie, controleert u of ze allemaal hetzelfde besturingssysteem hebben (Windows of Linux) en controleert u of de hoofddoelserver die u implementeert hetzelfde type besturingssysteem heeft. Alle VM's in een replicatiegroep moeten dezelfde hoofddoelserver gebruiken.
5. Open [de vereiste poorten](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) voor failback.
6. Controleer of de vCenter Server is verbonden voordat de failback wordt mislukt. Anders, het loskoppelen van schijven en het bevestigen van hen terug op de virtuele machine mislukt.
7. Als een vCenter-server de VM's beheert waaraan u niet terugkeert, moet u ervoor zorgen dat u over de vereiste machtigingen beschikt. Als u een alleen-lezen vCenter-detectie van gebruikers uitvoert en virtuele machines beschermt, slaagt beveiliging en werkt failover. Tijdens de herbescherming mislukt failover echter omdat de gegevensbestanden niet kunnen worden gedetecteerd en niet worden vermeld tijdens herbescherming. Als u dit probleem wilt oplossen, u de vCenter-referenties bijwerken met een [geschikt account/machtigingen](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)en de taak opnieuw proberen. 
8. Als u een sjabloon hebt gebruikt om uw virtuele machines te maken, moet u ervoor zorgen dat elke VM zijn eigen UUID voor de schijven heeft. Als de on-premises VM UUID botst met de UUID van de hoofddoelserver omdat beide zijn gemaakt op basis van dezelfde sjabloon, mislukt de beveiliging van opnieuw. Implementeren vanuit een andere sjabloon.
9. Als u niet teruggaat naar een alternatieve vCenter-server, controleert u of de nieuwe vCenter-server en de hoofddoelserver worden gedetecteerd. Meestal als dit niet de gegevenswinkels zijn die niet toegankelijk zijn of niet zichtbaar zijn in **Reprotect**.
10. Controleer de volgende scenario's waarin u niet falen:
    - Als je de ESXi 5.5 gratis editie of de vSphere 6 Hypervisor gratis editie gebruikt. Upgrade naar een andere versie.
    - Als u een fysieke windows server van Windows Server 2008 R2 SP1 hebt.
    - VMware VM's kunnen niet terug naar Hyper-V.
    - VM's die [zijn gemigreerd](migrate-overview.md#what-do-we-mean-by-migration).
    - Een vm die is verplaatst naar een andere resourcegroep.
    - Een replica Azure VM die is verwijderd.
    - Een replica azure vm die niet is beveiligd (repliceren naar de on-premises site).
10. [Bekijk de typen failback die](concepts-types-of-failback.md) u gebruiken - herstel van de oorspronkelijke locatie en het herstel van alternatieve locaties.


## <a name="enable-reprotection"></a>Herbescherming inschakelen

Schakel replicatie in. U specifieke VM's of een herstelplan opnieuw beveiligen:

- Als u een herstelplan opnieuw beschermt, moet u de waarden voor elke beveiligde machine opgeven.
- Als VM's deel uitmaken van een replicatiegroep voor multi-VM-consistentie, kunnen ze alleen opnieuw worden beveiligd met een herstelplan. VM's in een replicatiegroep moeten dezelfde hoofddoelserver gebruiken

### <a name="before-you-start"></a>Voordat u begint

- Nadat een VM na failover in Azure is opgestart, duurt het enige tijd voordat de agent zich opnieuw registreert op de configuratieserver (tot 15 minuten). Gedurende deze periode u de beveiliging niet opnieuw beveiligen en een foutbericht geeft aan dat de agent niet is geïnstalleerd. Als dit gebeurt, wacht dan een paar minuten en bescherm deze opnieuw.
- Als u de Azure VM wilt terugzetten naar een bestaande on-premises VM, monteert u de on-premises VM-gegevensarchieven met lees-/schrijftoegang op de ESXi-host van de hoofddoelserver.
- Als u wilt uitvallen naar een alternatieve locatie, bijvoorbeeld als de on-premises VM niet bestaat, selecteert u het bewaarstation en het gegevensarchief dat is geconfigureerd voor de hoofddoelserver. Wanneer u niet teruggaat naar de on-premises site, gebruiken de virtuele VMware-machines in het failback-beveiligingsplan hetzelfde gegevensarchief als de hoofddoelserver. Vervolgens wordt een nieuwe vm gemaakt in vCenter.

Herbescherming als volgt inschakelen:

1. Selecteer > **Gewaargewaargezeidse gerepliceerde items**. **Vault** Klik met de rechtermuisknop op de virtuele machine die is mislukt en selecteer **Opnieuw beveiligen**. Of selecteer de machine op de opdrachtknoppen en selecteer **Opnieuw beveiligen**.
2. Controleer of de beveiligingsrichting azure **naar on-premises** is geselecteerd.
3. Selecteer **in Hoofddoelserver** en **processerver**de on-premises hoofddoelserver en de processerver.  
4. Selecteer **voor Datastore**het datastore waaru de schijven on-premises wilt herstellen. Deze optie wordt gebruikt wanneer de on-premises virtuele machine wordt verwijderd en u nieuwe schijven moet maken. Deze optie wordt genegeerd als de schijven al bestaan. U moet nog steeds een waarde opgeven.
5. Selecteer het bewaarstation.
6. Het failback-beleid wordt automatisch geselecteerd.
7. Selecteer **OK** om opnieuw te worden beschermd.

    ![Dialoogvenster Opnieuw beveiligen](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Een taak begint de Azure VM te repliceren naar de on-premises site. U kunt de voortgang volgen op het tabblad **Taken**.
    - Wanneer de herbeveiliging slaagt, voert de VM een beveiligde status in.
    - De on-premises VM wordt uitgeschakeld tijdens het opnieuw beveiligen. Dit helpt ervoor te zorgen dat gegevens tijdens de replicatie consistent blijven.
    - Schakel de on-premises VM niet in nadat de beveiliging is voltooid.
   

## <a name="next-steps"></a>Volgende stappen

- Als u problemen ondervindt, bekijkt u het [artikel over het oplossen van problemen.](vmware-azure-troubleshoot-failback-reprotect.md)
- Nadat de Azure VM's zijn beveiligd, u [een failback uitvoeren.](vmware-azure-failback.md) Failback schakelt de Azure VM uit en start de on-premises VM op. Verwacht wat downtime voor de toepassing en kies een failback-tijd dienovereenkomstig.


