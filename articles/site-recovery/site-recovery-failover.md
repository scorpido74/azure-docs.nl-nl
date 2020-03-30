---
title: Een failover uitvoeren tijdens noodherstel met Azure Site Recovery
description: Hoe u niet meer dan VM's/fysieke servers naar Azure met Azure Site Recovery.
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 99a197e8f5ebac8a3b0be1b567ee41b43a2c4476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471265"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>Een failover uitvoeren van on-premises naar Azure

In dit artikel wordt beschreven hoe u falen over on-premises machines naar Azure in [Azure Site Recovery](site-recovery-overview.md)

## <a name="before-you-start"></a>Voordat u begint

- [Meer informatie](failover-failback-overview.md) over het failoverproces bij noodherstel.
- Als u wilt falen boven meerdere machines, [leest u hoe u](recovery-plan-overview.md) machines verzamelen in een herstelplan.
- Voordat u een volledige failover uitvoert, voert u een [noodherstelboor uit](site-recovery-test-failover-to-azure.md) om ervoor te zorgen dat alles werkt zoals verwacht.

## <a name="prepare-to-connect-after-failover"></a>Voorbereiden om verbinding te maken na failover

Als u ervoor wilt zorgen dat u verbinding maken met de Azure VM's die zijn gemaakt na een failover, u dit doen voordat u een fail-over maakt.


### <a name="prepare-on-premises-to-connect-after-failover"></a>On-premises voorbereiden om verbinding te maken na failover

Als u verbinding wilt maken met Azure VM's via RDP/SSH na failover, zijn er een aantal dingen die u on-premises moet doen voordat u failover.

**Na failover** | **Locatie** | **Acties**
--- | --- | ---
**Azure VM met Windows** | On-premises machine voor failover | Als u de Azure VM via internet wilt openen, schakelt u RDP in en controleert u of TCP- en UDP-regels voor **Openbaar**worden toegevoegd en dat RDP is toegestaan voor alle profielen in **windows firewall** > **toegestane apps**.<br/><br/> Als u toegang wilt krijgen tot de Azure VM via een site-to-site-verbinding, schakelt u RDP op de machine in en controleert u of RDP is toegestaan in de**apps en functies van**Windows **Firewall** -> toegestaan voor **domein- en privénetwerken.**<br/><br/> <br/><br/> Verwijder statische permanente routes en WinHTTP-proxy. Controleer of het SAN-beleid van het besturingssysteem is ingesteld op **OnlineAll.** [Meer informatie](https://support.microsoft.com/kb/3031135).<br/><br/> Zorg ervoor dat er geen Windows-updates in behandeling zijn op de VM wanneer u een failover activeert. Windows-update kan worden gestart wanneer u niet opnieuw bent en u zich pas aanmelden bij de virtuele machine als de update is voltooid.
**Azure VM met Linux** | On-premises machine voor failover | Controleer of de Secure Shell-service op de VM is ingesteld om automatisch te starten bij het opstarten van het systeem.<br/><br/> Controleer of er in de firewallregels is ingesteld dat SSH-verbindingen zijn toegestaan.


## <a name="run-a-failover"></a>Een failover uitvoeren

In deze procedure wordt beschreven hoe u een failover voor een [herstelplan uitvoert.](site-recovery-create-recovery-plans.md) Als u een failover voor één VM wilt uitvoeren, volgt u de instructies voor een [VMware VM,](vmware-azure-tutorial-failover-failback.md)een [fysieke server](physical-to-azure-failover-failback.md)of een [Hyper-V VM.](hyper-v-azure-failover-failback-tutorial.md)


Voer de failover van het herstelplan als volgt uit:

1. Selecteer **herstelplannen** > *recoveryplan_name*in de kluis Siteherstel.
2. Klik **op Failover**.

    ![Failover](./media/site-recovery-failover/Failover.png)

3. Laat in > **failoverfailoverrichting**de standaardinstelling als u re-replica's naar Azure. **Failover**
4. Selecteer **in Failover**een **herstelpunt** waarop u moet mislukken.

    - **Laatste :** Gebruik het laatste punt. Hiermee worden alle gegevens verwerkt die naar de siteherstelservice zijn verzonden en wordt voor elke machine een herstelpunt gemaakt. Deze optie biedt de laagste RPO (Herstelpuntdoelstelling) omdat de VM die na failover is gemaakt, alle gegevens bevat die zijn gerepliceerd naar Siteherstel wanneer de failover is geactiveerd.
   - **Laatste verwerkt**: Gebruik deze optie om te mislukken over VM's naar het laatste herstelpunt dat al is verwerkt door Site Recovery. U het laatste verwerkte herstelpunt zien in de **nieuwste herstelpunten**van de VM. Deze optie biedt een lage RTO omdat er geen tijd wordt besteed aan het verwerken van de onverwerkte gegevens
   - **Laatste app-consistent:** Gebruik deze optie om VM's te mislukken naar het nieuwste toepassingsconsistente herstelpunt dat is verwerkt door Site Recovery.
   - **Laatste multi-VM verwerkt:** met deze optie VM's die deel uitmaken van een replicatiegroep failover naar de nieuwste gemeenschappelijke multi-VM consistent recovery point. Andere virtuele machines niet over naar hun nieuwste verwerkte herstelpunt. Deze optie is alleen voor herstelplannen waarvoor ten minste één VM is ingeschakeld met consistentie met meerdere VM's.
   - **Laatste multi-VM app-consistent:** Met deze optie VM's die deel uitmaken van een replicatiegroep niet over naar de nieuwste gemeenschappelijke multi-VM applicatie-consistent herstelpunt. Andere virtuele machines mislukken naar hun nieuwste applicatie-consistente herstelpunt. Alleen voor herstelplannen waarvoor ten minste één VM is ingeschakeld met multi-VM-consistentie.
   - **Aangepast:** niet beschikbaar voor herstelplannen. Deze optie is alleen voor failover van individuele VM's.

5. Selecteer **Sluitmachine voordat u failover begint** als u wilt dat siteherstel bronVM's afsluit voordat de failover wordt gestart. De failover wordt voortgezet zelfs als het afsluiten is mislukt.  

    > [!NOTE]
    > Als u niet over Hyper-V VM's nagaat, probeert afsluiten de on-premises gegevens die nog niet naar de service zijn verzonden, te synchroniseren en te repliceren voordat de failover wordt geactiveerd. 

6. Volg de voortgang van failover op de pagina **Vacatures.** Zelfs als er fouten optreden, wordt het herstelplan uitgevoerd totdat het is voltooid.
7. Meld u na de failover aan bij de VM om deze te valideren. 
8. Als u wilt overschakelen naar een ander herstelpunt dat u wilt gebruiken voor de failover, gebruikt u **Herstelpunt wijzigen**.
9. Als je er klaar voor bent, kun je de failover plegen. De actie **Commit** verwijdert alle herstelpunten die beschikbaar zijn met de service. De optie **Herstelpunt wijzigen** is niet langer beschikbaar.

## <a name="run-a-planned-failover-hyper-v"></a>Een geplande failover uitvoeren (Hyper-V)

U een geplande failover uitvoeren voor Hyper-V VM's.

- Een geplande failover is een zero data loss failover optie.
- Wanneer een geplande failover wordt geactiveerd, worden eerst de bronvirtuele machines uitgeschakeld, worden de nieuwste gegevens gesynchroniseerd en wordt een failover geactiveerd.
- U voert een geplande failover uit met de optie **Geplande failover.** Het loopt op een vergelijkbare manier als een regelmatige failover.
 
## <a name="track-failovers"></a>Failovers bijhouden

Er zijn een aantal taken in verband met failover.

![Failover](./media/site-recovery-failover/FailoverJob.png)

- **Voorwaarden controleren**: Zorgt ervoor dat aan alle voorwaarden voor failover wordt voldaan.
- **Failover:** verwerkt de gegevens zodat er een Azure VM van kan worden gemaakt. Als u **Laatste** herstelpunt hebt gekozen, wordt een herstelpunt gemaakt op basis van de gegevens die naar de service zijn verzonden.
- **Start:** Hiermee maakt u een Azure VM met de gegevens die in de vorige stap zijn verwerkt.

> [!WARNING]
> **Een failover in uitvoering niet annuleren:** voordat failover wordt gestart, is replicatie voor de VM gestopt. Als u een lopende taak annuleert, stopt failover, maar begint de VM niet te repliceren. Replicatie kan niet opnieuw worden gestart.


### <a name="extra-failover-time"></a>Extra failovertijd

In sommige gevallen vereist VM-failover tussentijdse stap die meestal ongeveer acht tot tien minuten in beslag neemt. Dit zijn de machines die worden beïnvloed door deze extra stap / tijd:

* VMware virtuele machines met een Mobility service versie ouder dan 9.8.
* Fysieke servers en Hyper-V VM's worden beveiligd als fysieke servers.
* VMware Linux VM's.
* VMware VM's waarop deze stuurprogramma's niet aanwezig zijn als boot drivers:
    * storvsc storvsc
    * vmbus
    * storflt storflt
    * intelide
    * Atapi
* VMware VM's die dhcp niet hebben ingeschakeld, ongeacht of ze DHCP- of statische IP-adressen gebruiken.


## <a name="automate-actions-during-failover"></a>Acties automatiseren tijdens failover

U acties automatiseren tijdens failover. Hiervoor u scripts of Azure-automatiseringsrunbooks gebruiken in herstelplannen.

- [Meer informatie](site-recovery-create-recovery-plans.md) over het maken en aanpassen van herstelplannen, inclusief het toevoegen van scripts.
- [Meer informatie](site-recovery-runbook-automation.md) over het toevoegen van Azure Automation-runbooks aan herstelplannen.


## <a name="configure-settings-after-failover"></a>Instellingen configureren na failover

### <a name="retain-drive-letters-after-failover"></a>Schijfletters behouden na failover

Site Recovery verwerkt het bewaren van aandrijfletters. Als u schijven uitsluit tijdens vm-replicatie, [leest u een voorbeeld](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) van hoe dit werkt.

### <a name="prepare-in-azure-to-connect-after-failover"></a>Voorbereiden in Azure om verbinding te maken na failover

Als u verbinding wilt maken met Azure VM's die zijn gemaakt na failover met RDP of SSH, volgt u de vereisten die in de tabel zijn samengevat.

**Failover** | **Locatie** | **Acties**
--- | --- | ---
**Azure VM met Windows** | Azure VM na failover |  [Voeg een openbaar IP-adres toe](https://aka.ms/addpublicip) voor de VM.<br/><br/> De regels voor de netwerkbeveiligingsgroep op de mislukte VM (en het Azure-subnet waarmee het is verbonden) moeten binnenkomende verbindingen met de RDP-poort toestaan.<br/><br/> Controleer **Diagnostische gegevens** opstarten om een schermafbeelding van de vm te verifiëren.<br/><br/> Als u geen verbinding maken, controleert u of de vm wordt uitgevoerd en controleert u deze [tips voor probleemoplossing.](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)
**Azure VM met Linux** | Azure VM na failover | De regels voor de netwerkbeveiligingsgroep op de mislukte VM (en het Azure-subnet waarmee het is verbonden) moeten binnenkomende verbindingen met de SSH-poort toestaan.<br/><br/> [Voeg een openbaar IP-adres toe](https://aka.ms/addpublicip) voor de VM.<br/><br/> Controleer **Diagnostische gegevens** voor een schermafbeelding van de VM.<br/><br/>

Volg de stappen die [hier](site-recovery-failover-to-azure-troubleshoot.md) worden beschreven om eventuele verbindingsproblemen na een failover op te lossen.

## <a name="set-up-ip-addressing"></a>IP-adres instellen

- **Interne IP-adressen:** Als u het interne IP-adres van een Azure VM na failover wilt instellen, hebt u een aantal opties:
    - Behoudt hetzelfde IP-adres: u hetzelfde IP-adres op de Azure-vm gebruiken als het adres dat is toegewezen aan de on-premises machine.
    - Gebruik een ander IP-adres: u een ander IP-adres gebruiken voor de Azure VM.
    - [Meer informatie](concepts-on-premises-to-azure-networking.md#assign-an-internal-address) over het instellen van interne IP-adressen.
- **Externe IP-adressen**: U openbare IP-adressen behouden bij failover. Azure VM's die zijn gemaakt als onderdeel van het failoverproces, moeten een Openbaar IP-adres van Azure toegewezen krijgen dat beschikbaar is in de Azure-regio. U een openbaar IP-adres handmatig toewijzen of door het proces te automatiseren met een herstelplan. [Meer informatie](concepts-public-ip-address-with-site-recovery.md).


## <a name="next-steps"></a>Volgende stappen

Nadat het niet meer is gelukt, moet u opnieuw beveiligen om de Azure VM's terug te repliceren naar de on-premises site. Nadat de replicatie is uitgevoerd, u on-premises weer mislukken wanneer u er klaar voor bent.

- [Meer informatie](failover-failback-overview.md#reprotectionfailback) over herbescherming en failback.
- [Bereid u](vmware-azure-reprotect.md) voor op VMware herbescherming en failback.
- [Terugfalen](hyper-v-azure-failback.md) Hyper-V VM's.
- [Meer informatie over](physical-to-azure-failover-failback.md) het failover- en failbackproces voor fysieke servers.

