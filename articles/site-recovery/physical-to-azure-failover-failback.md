---
title: Failover en failback instellen voor fysieke servers met Site Recovery
description: Meer informatie over het mislukken van fysieke servers naar Azure en niet terug naar de on-premises site voor herstel na noodgevallen met Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.author: raynew
ms.openlocfilehash: ea5893f45962d67f4b6f3e9a261c65aa0ec926bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75497857"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Fail over and fail back physical servers replicated to Azure

In deze zelfstudie wordt beschreven hoe u falen over on-premises fysieke servers die worden gerepliceerd naar Azure met [Azure Site Recovery](site-recovery-overview.md). Nadat het niet meer is gelukt, mislukt u terug van Azure naar uw on-premises site wanneer deze beschikbaar is.

## <a name="before-you-start"></a>Voordat u begint

- [Meer informatie](failover-failback-overview.md) over het failoverproces bij noodherstel.
- Als u wilt falen boven meerdere machines, [leest u hoe u](recovery-plan-overview.md) machines verzamelen in een herstelplan.
- Voordat u een volledige failover uitvoert, voert u een [noodherstelboor uit](site-recovery-test-failover-to-azure.md) om ervoor te zorgen dat alles werkt zoals verwacht.
- Volg [deze instructies](site-recovery-failover.md#prepare-to-connect-after-failover) om u voor te bereiden op verbinding met Azure VM's na een failover.



## <a name="run-a-failover"></a>Een failover uitvoeren

### <a name="verify-server-properties"></a>Servereigenschappen verifiëren

Controleer de servereigenschappen en controleer of deze voldoet aan [azure-vereisten](vmware-physical-azure-support-matrix.md#replicated-machines) voor Azure VM's.

1. Klik in **Beveiligde items**op **Gerepliceerde items**en selecteer de machine.
2. In het **deelvenster Gerepliceerde items** vindt u een overzicht van machine-informatie, status status en de meest recente beschikbare herstelpunten. Klik op **Eigenschappen** om meer details te bekijken.
3. In **Compute en Network**u de Azure-naam, resourcegroep, doelgrootte, [beschikbaarheidsset](../virtual-machines/windows/tutorial-availability-sets.md)en beheerde schijfinstellingen wijzigen
4. U kunt de netwerkinstellingen bekijken en wijzigen, inclusief het netwerk-/subnet waarin de Azure VM zich na failover bevindt en het IP-adres dat eraan wordt toegewezen.
5. In **schijven**u informatie zien over het besturingssysteem en gegevensschijven van de machine.

### <a name="fail-over-to-azure"></a>Failover naar Azure

1. Klik in**Gerepliceerde items** **op** > de machine > **failover**.
2. Selecteer in **Failover** een **Herstelpunt** waarnaar u de failover wilt uitvoeren. U kunt een van de volgende opties gebruiken:
   - **Laatste**: met deze optie worden eerst alle gegevens naar Site Recovery verzonden gegevens verwerkt. Dit biedt het laagste RPO (Recovery Point Objective), omdat de na de failover gemaakte Azure-VM alle gegevens heeft die naar Site Recovery is gerepliceerd toen de failover werd geactiveerd.
   - **Laatste verwerkt**: Deze optie mislukt over de machine naar het nieuwste herstelpunt verwerkt door Site Recovery. Deze optie heeft een lage RTO (Recovery Time Objective), omdat er geen tijd wordt besteed aan het verwerken van niet-verwerkte gegevens.
   - **Laatste app-consistent:** Deze optie mislukt over de machine naar het nieuwste app-consistente herstelpunt dat door Site Recovery is verwerkt.
   - **Aangepast**: geef een herstelpunt op.

3. Selecteer De machine afsluiten voordat u **begint met failover** als u wilt dat Siteherstel probeert de bronmachine uit te schakelen voordat de failover wordt geactiveerd. De failover wordt voortgezet zelfs als het afsluiten is mislukt. U de failovervoortgang volgen op de pagina **Vacatures.**
4. Als u de verbinding met de Azure VM hebt voorbereid, maakt u na de failover verbinding om deze te valideren.
5. Na het verifiëren kunt u de failover **Doorvoeren**. Hiermee verwijdert u alle beschikbare herstelpunten.

> [!WARNING]
> Annuleer een mislukte mislukte uitvoering niet. Voordat de failover begint, stopt de machinereplicatie. Als u de failover annuleert, stopt deze, maar wordt de machine niet opnieuw gerepliceerd.
> Voor fysieke servers kan het ongeveer acht tot tien minuten duren voordat extra failoververwerking en -verwerking is voltooid.

## <a name="automate-actions-during-failover"></a>Acties automatiseren tijdens failover

U acties automatiseren tijdens failover. Hiervoor u scripts of Azure-automatiseringsrunbooks gebruiken in herstelplannen.

- [Meer informatie](site-recovery-create-recovery-plans.md) over het maken en aanpassen van herstelplannen, inclusief het toevoegen van scripts.
- [Lees ook:](site-recovery-runbook-automation.md) Azure Automation-runbooks toevoegen aan herstelplannen.

## <a name="configure-settings-after-failover"></a>Instellingen configureren na failover

Na failover moet u [Azure-instellingen configureren](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover) om verbinding te maken met de gerepliceerde Azure VM's. Daarnaast interne [en openbare](site-recovery-failover.md#set-up-ip-addressing) IP-adressering opzetten.

## <a name="prepare-for-reprotection-and-failback"></a>Voorbereiden op herbescherming en failback

Nadat u niet overhebt naar Azure, beschermt u Azure VM's opnieuw door ze te repliceren naar de on-premises site. Nadat ze zijn gerepliceerd, u ze niet meer op locatie uitvoeren door een failover van Azure naar uw on-premises site uit te voeren.

1. Fysieke servers die met Site Recovery naar Azure zijn gerepliceerd, kunnen alleen maar worden teruggestuurd als VMware VM's. Je hebt een VMware-infrastructuur nodig om terug te falen. Volg de stappen in [dit artikel](vmware-azure-prepare-failback.md) om u voor te bereiden op herbescherming en failback, waaronder het instellen van een processerver in Azure en een on-premises hoofddoelserver, en het configureren van een site-to-site VPN, of ExpressRoute private peering, voor failback.
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
    - VM's die [zijn gemigreerd](migrate-overview.md#what-do-we-mean-by-migration).
    - Een vm die is verplaatst naar een andere resourcegroep.
    - Een replica Azure VM die is verwijderd.
    - Een replica azure vm die niet is beveiligd (repliceren naar de on-premises site).
10. [Bekijk de typen failback die](concepts-types-of-failback.md) u gebruiken - herstel van de oorspronkelijke locatie en het herstel van alternatieve locaties.


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>Azure VM's opnieuw beveiligen naar een alternatieve locatie

Deze procedure gaat ervan uit dat de on-premises VM niet beschikbaar is.

1. Klik in de kluis >**gerepliceerde items** **met** > de rechtermuisknop op de machine die is mislukt boven > Opnieuw **beveiligen**.
2. Controleer in **Opnieuw beveiligen** of **Azure naar on-premises** is geselecteerd.
3. Geef de on-premises hoofddoelserver en de processerver op.
4. Selecteer in **Gegevensopslag** de gegevensopslag van het hoofddoel waarnaar u de on-premises schijven wilt herstellen.
       - Gebruik deze optie als de on-premises VM is verwijderd of niet bestaat en u nieuwe schijven moet maken.
       - Deze instelling wordt genegeerd als de schijven al bestaan, maar u moet wel een waarde opgeven.
5. Selecteer het bewaarstation van het hoofddoel. Het failback-beleid wordt automatisch geselecteerd.
6. Klik op **OK** om te beginnen met opnieuw beveiligen. Een taak begint de Azure VM te repliceren naar de on-premises site. U kunt de voortgang volgen op het tabblad **Taken**.

> [!NOTE]
> Als u de Azure-VM wilt herstellen naar een bestaande on-premises VM, koppelt u de gegevensopslag van de virtuele machine met lees-/schrijf-toegang aan de ESXi-host van de hoofddoelserver.


## <a name="fail-back-from-azure"></a>Failback vanaf Azure

Voer de failover als volgt uit:

1. Klik op de pagina **Gerepliceerde items** met de rechtermuisknop op de computer > **Niet-geplande failover**.
2. Controleer in **Failover bevestigen** of de failoverrichting van Azure is.
3.Selecteer het herstelpunt dat u wilt gebruiken voor de failover.
    - We raden u aan het laatste herstelpunt **te** gebruiken. Het app-consistente punt ligt achter het laatste punt in de tijd en veroorzaakt wat gegevensverlies.
    - **Laatste** is een crash-consistent herstelpunt.
    - Wanneer failover wordt uitgevoerd, schakelt Site Recovery de Azure VM's uit en start de on-premises VM op. Er zal wat downtime zijn, dus kies een geschikte tijd.
4. Klik met de rechtermuisknop op de computer en klik op **Doorvoeren**. Dit activeert een taak waardoor de Azure VM's worden verwijderd.
5. Controleer of de Azure VM's zijn afgesloten zoals verwacht.


## <a name="reprotect-on-premises-machines-to-azure"></a>On-premises computers opnieuw beveiligen naar Azure

De gegevens moeten nu weer terug zijn op uw on-premises site, maar ze worden niet gerepliceerd naar Azure. U kunt als volgt weer beginnen met repliceren naar Azure:

1. Selecteer in de kluis > **Instellingen** >**Gerepliceerde items** de failback-VM's waarvoor een failback is uitgevoerd en klik op **Opnieuw beveiligen**.
2. Selecteer de processerver die wordt gebruikt om de gerepliceerde gegevens naar Azure te verzenden en klik op **OK**.


## <a name="next-steps"></a>Volgende stappen

Nadat de taak reprotect is voltooid, wordt de on-premises VM gerepliceerd naar Azure. Indien nodig u [een andere failover naar](site-recovery-failover.md) Azure uitvoeren.
