---
title: Over failover en failback in Azure Site Recovery
description: Meer informatie over failover en failable in Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: d9b54f3c452212e12419a5ffd67b116c8660308d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281806"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback"></a>Over on-premises disaster recovery failover/failback

Dit artikel biedt een overzicht van failover en failback tijdens noodherstel van on-premises machines naar Azure met [Azure Site Recovery](site-recovery-overview.md).

## <a name="recovery-stages"></a>Herstelfasen

Failover en failback in Site Recovery heeft vier fasen:

- **Fase 1: Fail over van on-premises:** Na het instellen van replicatie naar Azure voor on-premises machines, wanneer uw on-premises site uitvalt, u niet deze machines over naar Azure. Na failover worden Azure VM's gemaakt op basis van gerepliceerde gegevens.
- **Fase 2: Azure VM's opnieuw beveiligen:** in Azure beschermt u de Azure VM's opnieuw, zodat ze beginnen te repliceren naar de on-premises site. De on-premises VM (indien beschikbaar) wordt uitgeschakeld tijdens de herbescherming, om de consistentie van de gegevens te garanderen.
- **Fase 3: Fail over van Azure:** Wanneer uw on-premises site weer normaal wordt uitgevoerd, voert u een nieuwe failover uit, dit keer om Azure VM's terug te laten mislukken naar uw on-premises site. U niet terug naar de oorspronkelijke locatie van waaruit u hebt gefaald, of naar een alternatieve locatie.
- **Fase 4: On-premises machines opnieuw beveiligen:** na het mislukken van de back, u opnieuw de replicatie van de on-premises machines naar Azure inschakelen.

## <a name="failover"></a>Failover

U voert een failover uit als onderdeel van uw business continuity and disaster recovery (BCDR) strategie.

- Als eerste stap in uw BCDR-strategie repliceert u uw on-premises machines voortdurend naar Azure. Gebruikers hebben toegang tot workloads en apps die worden uitgevoerd op de on-premises bronmachines.
- Als het nodig is, bijvoorbeeld als er een storing on-premises is, mislukt u de replicerende machines naar Azure. Azure VM's worden gemaakt met behulp van de gerepliceerde gegevens.
- Voor bedrijfscontinuïteit kunnen gebruikers toegang blijven krijgen tot apps in de Azure VM's.

Failover is een activiteit in twee fasen:

- **Failover:** de failover die een Azure VM maakt en naar boven brengt met behulp van het geselecteerde herstelpunt.
- **Commit:** Na failover controleert u de VM in Azure:
    - Je de failover vervolgens vastleggen op het geselecteerde herstelpunt of een ander punt voor de commit selecteren.
    - Na het plegen van de failover kan het herstelpunt niet worden gewijzigd.


## <a name="connect-to-azure-after-failover"></a>Verbinding maken met Azure na failover

Als u verbinding wilt maken met de Azure VM's die zijn gemaakt na failover met RDP/SSH, zijn er een aantal vereisten.

**Failover** | **Locatie** | **Acties**
--- | --- | ---
**Azure VM (Windows)** | Op de on-premises machine voor failover | **Toegang via internet:** RDP inschakelen. Zorg ervoor dat TCP- en UDP-regels voor **Openbaar**worden toegevoegd en dat RDP is toegestaan voor alle profielen in **Windows Firewall** > **Toegestane apps.**<br/><br/> **Toegang via site-to-site VPN:** RdP inschakelen op de machine. Controleer of RDP is toegestaan in de windows **firewall** -> **toegestane apps en functies**voor **domein- en privénetwerken.**<br/><br/>  Controleer of het SAN-beleid van het besturingssysteem is ingesteld op **OnlineAll.** [Meer informatie](https://support.microsoft.com/kb/3031135).<br/><br/> Zorg ervoor dat er geen Windows-updates in behandeling zijn op de VM wanneer u een failover activeert. Windows Update kan starten wanneer u niet opnieuw bent en u zich niet aanmelden bij de virtuele machine totdat updates zijn uitgevoerd.
**Azure VM met Windows** | Op de Azure VM na failover |  [Voeg een openbaar IP-adres toe](https://aka.ms/addpublicip) voor de VM.<br/><br/> De netwerkbeveiligingsgroep regels op de mislukte over VM (en het Azure-subnet waarmee het is aangesloten) moeten binnenkomende verbindingen met de RDP-poort toestaan.<br/><br/> Controleer **Diagnostische gegevens** opstarten om een schermafbeelding van de vm te verifiëren. Als u geen verbinding maken, controleert u of de virtuele machine wordt uitgevoerd en controleert u tips voor [probleemoplossing.](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)
**Azure VM met Linux** | Op de on-premises machine voor failover | Controleer of de Secure Shell-service op de VM is ingesteld om automatisch te starten bij het opstarten van het systeem.<br/><br/> Controleer of er in de firewallregels is ingesteld dat SSH-verbindingen zijn toegestaan.
**Azure VM met Linux** | Op de Azure VM na failover | De regels voor de netwerkbeveiligingsgroep op de mislukte VM (en het Azure-subnet waarmee het is verbonden) moeten binnenkomende verbindingen met de SSH-poort toestaan.<br/><br/> [Voeg een openbaar IP-adres toe](https://aka.ms/addpublicip) voor de VM.<br/><br/> Controleer **Diagnostische gegevens** voor een schermafbeelding van de VM.<br/><br/>

## <a name="types-of-failover"></a>Typen failover

Siteherstel biedt verschillende failoveropties.

**Failover** | **Details** | **Herstel** | **Werkstroom**
--- | --- | --- | ---
**Testfailover** | Wordt gebruikt om een drill uit te voeren die uw BCDR-strategie valideert, zonder gegevensverlies of downtime.| Hiermee maakt u een kopie van de VM in Azure, zonder dat dit gevolgen heeft voor de lopende replicatie of op uw productieomgeving. | 1. Voer een testfailover uit op één vm of op meerdere VM's in een herstelplan.<br/><br/> 2. Selecteer een herstelpunt dat u wilt gebruiken voor de testfailover.<br/><br/> 3. Selecteer een Azure-netwerk waarin de Azure VM zich bevindt wanneer deze is gemaakt na een failover. Het netwerk wordt alleen gebruikt voor de testfailover.<br/><br/> 4. Controleer of de boor werkte zoals verwacht. Siteherstel ruimt automatisch VM's op die tijdens de drill in Azure zijn gemaakt.
**Geplande failover-Hyper-V**  | Meestal gebruikt voor geplande downtime.<br/><br/> Bron VM's zijn stilgelegd. De meest recente gegevens worden gesynchroniseerd voordat de failover wordt gestart. | Nul gegevensverlies voor de geplande werkstroom. | 1. Plan een downtime onderhoudsvenster en gebruikers op de hoogte.<br/><br/> 2. Haal apps die door de gebruiker worden geconfronteerd offline.<br/><br/> 3. Start een geplande failover met het laatste herstelpunt. De failover wordt niet uitgevoerd als de machine niet is uitgeschakeld of als er fouten zijn aangetroffen.<br/><br/> 4. Controleer na de failover of de replica Azure VM actief is in Azure.<br/><br/> 5. Commit de failover te voltooien. De commit actie verwijdert alle herstelpunten.
**Failover-Hyper-V** | Meestal uitgevoerd als er een ongeplande storing is of als de primaire site niet beschikbaar is.<br/><br/> Sluit de VM optioneel af en synchroniseer de laatste wijzigingen voordat u de failover start.  | Minimaal gegevensverlies voor apps. | 1. Start uw BCDR-plan. <br/><br/> 2. Start een failover. Geef op of Site Recovery de VM moet afsluiten en de laatste wijzigingen moet synchroniseren/repliceren voordat de failover wordt geactiveerd.<br/><br/> 3. U niet over naar een aantal opties voor herstelpunten, samengevat in de onderstaande tabel.<br/><br/> Als u de optie niet inschakelt om de vm af te sluiten of als Site Recovery deze niet kan afsluiten, wordt het laatste herstelpunt gebruikt.<br/>De failover loopt zelfs als de machine niet kan worden uitgeschakeld.<br/><br/> 4. Na failover controleert u of de replica Azure VM actief is in Azure.<br/> Indien nodig u een ander herstelpunt selecteren in het bewaarvenster van 24 uur.<br/><br/> 5. Commit de failover te voltooien. De commit actie verwijdert alle beschikbare herstelpunten.
**Failover-VMware** | Meestal uitgevoerd als er een ongeplande storing is of als de primaire site niet beschikbaar is.<br/><br/> Geef eventueel op dat Site recovery moet proberen een afsluiting van de VM te activeren en definitieve wijzigingen te synchroniseren en te repliceren voordat de failover wordt gestart.  | Minimaal gegevensverlies voor apps. | 1. Start uw BCDR-plan. <br/><br/> 2. Start een failover van Site Recovery. Geef op of siteherstel moet proberen vm-uitschakeling te activeren en te synchroniseren voordat de failover wordt uitgevoerd.<br/> De failover loopt zelfs als de machines niet kunnen worden uitgeschakeld.<br/><br/> 3. Controleer na de failover of de replica Azure VM actief is in Azure. <br/>Indien nodig u een ander herstelpunt selecteren in het bewaarvenster van 72 uur.<br/><br/> 5. Commit de failover te voltooien. De commit actie verwijdert alle herstelpunten.<br/> Voor Windows VM's schakelt Site Recovery de VMware-hulpprogramma's uit tijdens failover.

## <a name="failover-processing"></a>Failoververwerking

In sommige scenario's vereist failover extra verwerking die ongeveer 8 tot 10 minuten in beslag neemt. U langere testfailovertijden opmerken voor:

* VMware VM's met een Mobility-serviceversie ouder dan 9.8.
* Fysieke servers.
* VMware Linux VM's.
* Hyper-V VM's beschermd als fysieke servers.
* VMware VM's die de DHCP-service niet hebben ingeschakeld.
* VMware VM's die niet de volgende boot drivers hebben: storvsc, vmbus, storflt, intelide, atapi.

## <a name="recovery-point-options"></a>Opties voor herstelpunten

Tijdens failover u een aantal opties voor herstelpunten selecteren.

**Optie** | **Details**
--- | ---
**Laatste (laagste RPO)** | Deze optie biedt de laagste doelstelling voor herstelpunten (RPO). Het verwerkt eerst alle gegevens die naar de siteherstelservice zijn verzonden, om een herstelpunt voor elke VM te maken, voordat deze niet wordt verzonden. Op dit herstelpunt worden alle gegevens gerepliceerd naar Site recovery wanneer de failover is geactiveerd.
**Laatste verwerkte**  | Deze optie mislukt over VM's naar het laatste herstelpunt dat door Site Recovery is verwerkt. Als u het nieuwste herstelpunt voor een specifieke virtuele machine wilt bekijken, schakelt u **De laatste herstelpunten** in de VM-instellingen in. Deze optie heeft een lage RTO (Recovery Time Objective), omdat er geen tijd wordt besteed aan het verwerken van niet-verwerkte gegevens.
**Nieuwste app-consistent** |  Deze optie mislukt ten opzichte van VM's naar het nieuwste toepassingsconsistente herstelpunt dat is verwerkt door Site Recovery, als app-consistente herstelpunten zijn ingeschakeld. Controleer het laatste herstelpunt in de VM-instellingen.
**Laatste multi-VM verwerkt** | Deze optie is beschikbaar voor herstelplannen met een of meer VM's waarvoor consistentie met meerdere VM's is ingeschakeld. VM's met de ingeschakelde instelling slagen niet over op het meest recente algemene consistent herstelpunt voor meerdere VM's. Alle andere VM's in het plan niet over naar de meest recente verwerkte herstelpunt.
**Nieuwste multi-VM app-consistent** |  Deze optie is beschikbaar voor herstelplannen met een of meer VM's waarvoor consistentie met meerdere VM's is ingeschakeld. VM's die deel uitmaken van een replicatiegroep, worden niet naar het meest recente algemene herstelpunt voor meerdere VM-toepassingen overgezet. Andere VM's slagen niet over hun nieuwste toepassingsconsistente herstelpunt.
**Aangepast** | Gebruik deze optie om een specifieke vm op een bepaald herstelpunt te laten mislukken. Deze optie is niet beschikbaar voor herstelplannen.

> [!NOTE]
> Herstelpunten kunnen niet worden gemigreerd naar een andere vault van Recovery Services.

## <a name="reprotectionfailback"></a>Herbescherming/failback

Na een fail-over naar Azure bevinden de gerepliceerde Azure VM's zich in een onbeveiligde status.

- Als eerste stap om niet terug te keren naar uw on-premises site, moet u de Azure VM's repliceren naar on-premises. Het herbeschermingsproces is afhankelijk van het type machines waar u niet over hebt gefaald.
- Nadat machines zijn gerepliceerd van Azure naar on-premises, u een failover uitvoeren van Azure naar uw on-premises site.
- Nadat machines opnieuw on-premises worden uitgevoerd, u replicatie inschakelen zodat ze worden gerepliceerd naar Azure voor herstel na noodgevallen.

Failback werkt als volgt:

- Om terug te mislukken, heeft een VM ten minste één herstelpunt nodig om terug te mislukken. In een herstelplan hebben alle VM's in het plan ten minste één herstelpunt nodig.
- We raden u aan het **laatste** herstelpunt te gebruiken om terug te mislukken (dit is een crash-consistent punt).
    - Er is een app-consistente herstelpunt optie. In dit geval herstelt één virtuele virtuele machine naar het nieuwste beschikbare app-consistente herstelpunt. Voor een herstelplan met een replicatiegroep herstelt elke replicatiegroep naar het gemeenschappelijke beschikbare herstelpunt.
    - App-consistente herstelpunten kunnen op tijd achterlopen en er kan verlies zijn aan gegevens.
- Tijdens failover van Azure naar de on-premises site worden de Azure VM's afgesloten. Wanneer u de failover pleegt, verwijdert Site Recovery de mislukte Azure VM's in Azure.


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/fysieke herbescherming/failback

Als u VMware-machines en fysieke servers opnieuw wilt beschermen en teruglaten, van Azure naar on-premises, hebt u een failback-infrastructuur nodig en zijn er een aantal vereisten.

- **Tijdelijke processerver in Azure:** Als u niet terugwilt van Azure, stelt u een Azure VM in om te fungeren als een processerver om replicatie vanuit Azure te verwerken. U kunt deze virtuele machine verwijderen wanneer de failback is voltooid.
- **VPN-verbinding**: Om terug te mislukken, hebt u een VPN-verbinding (of ExpressRoute) nodig van het Azure-netwerk naar de on-premises site.
- **Afzonderlijke hoofddoelserver:** standaard verwerkt de hoofddoelserver die is geïnstalleerd met de configuratieserver op de on-premises VMware VM failback. Als u grote hoeveelheden verkeer moet terugwerken, stelt u hiervoor een afzonderlijke on-premises hoofddoelserver in.
- **Failbackbeleid**: als u wilt repliceren naar de on-premises site, hebt u een failbackbeleid nodig. Dit beleid wordt automatisch gemaakt wanneer u een replicatiebeleid maakt van on-premises naar Azure.
    - Dit beleid wordt automatisch gekoppeld aan de configuratieserver.
    - U dit beleid niet bewerken.
    - Beleidswaarden: RPO-drempel - 15 minuten; Herstelpunt retentie - 24 uur; App-consistente momentopnamefrequentie - 60 minuten.

Meer informatie over VMware/fysieke herbescherming en failback:
- [Bekijk](vmware-azure-reprotect.md#before-you-begin) aanvullende vereisten voor herbescherming en failback.
- [Een](vmware-azure-prepare-failback.md#deploy-a-process-server-in-azure) processerver implementeren in Azure.
- [Implementeer](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) een afzonderlijke hoofddoelserver.

Wanneer u Azure VM's opnieuw beschermt naar on-premises, u opgeven dat u wilt falen naar de oorspronkelijke locatie of naar een alternatieve locatie.

- **Herstel van**de oorspronkelijke locatie : dit mislukt terug van Azure naar dezelfde on-premises boordmachine als deze bestaat. In dit scenario worden alleen wijzigingen gerepliceerd naar on-premises.
- **Alternatieve locatieherstel:** Als de on-premises machine niet bestaat, u niet terugschakelen van Azure naar een alternatieve locatie. Wanneer u de Azure VM opnieuw beschermt naar on-premises, wordt de on-premises machine gemaakt. Volledige gegevensreplicatie vindt plaats van Azure naar on-premises. - [- Bekijk](concepts-types-of-failback.md) de vereisten en beperkingen voor locatiefailback.



## <a name="hyper-v-reprotectionfailback"></a>Hyper-V herbescherming/failback

HyperV VM's opnieuw beveiligen en terugwerken van Azure naar on-premises:

- U alleen niet terug Hyper-V VM's repliceren met behulp van een opslagaccount. Failback van Hyper-VVM's die repliceren met beheerde schijven wordt niet ondersteund.
- On-premises Hyper-V-hosts (of System Center VMM indien gebruikt) moeten zijn verbonden met Azure.
- U voert een geplande failback uit van Azure naar on-premises.
- Er hoeven geen specifieke onderdelen te worden ingesteld voor Hyper-V VM-failback.
- Tijdens de geplande failover u opties selecteren om gegevens te synchroniseren voordat u failback:
    - **Gegevens synchroniseren voordat de failover mislukt:** deze optie minimaliseert downtime voor virtuele machines omdat deze machines synchroniseert zonder ze af te sluiten.
        - Fase 1: Maakt een momentopname van de Azure VM en kopieert deze naar de on-premises Hyper-V-host. De machine blijft draaien in Azure.
        - Fase 2: Hiermee wordt de Azure VM uitgeschakeld, zodat daar geen nieuwe wijzigingen optreden. De uiteindelijke set deltawijzigingen wordt overgebracht naar de on-premises server en de on-premises VM wordt opgestart.
    - **Alleen gegevens synchroniseren tijdens failover:** deze optie is sneller omdat we verwachten dat het grootste deel van de schijf is gewijzigd en dus geen checksumberekeningen uitvoert. Het voert een download van de schijf. We raden u aan deze optie te gebruiken als de VM al een tijdje (een maand of langer) in Azure wordt uitgevoerd of als de on-premises VM is verwijderd.

[Meer informatie](hyper-v-azure-failback.md) over Hyper-V herbescherming en failback.

Wanneer u Azure VM's opnieuw beschermt naar on-premises, u opgeven dat u wilt falen naar de oorspronkelijke locatie of naar een alternatieve locatie.

- **Herstel van**de oorspronkelijke locatie : dit mislukt terug van Azure naar dezelfde on-premises boordmachine als deze bestaat. In dit scenario selecteert u een van de synchronisatieopties die in de vorige procedure zijn beschreven.
- **Alternatieve locatieherstel:** Als de on-premises machine niet bestaat, u niet terugschakelen van Azure naar een alternatieve locatie. Wanneer u de Azure VM opnieuw beschermt naar on-premises, wordt de on-premises machine gemaakt. Met deze optie raden we u aan de optie te selecteren om gegevens te synchroniseren voordat failover
- [Bekijk](hyper-v-azure-failback.md) de vereisten en beperkingen voor locatiefailback.


Nadat u niet terug bent gegaan naar de on-premises site, schakelt u **Reverse Replicate** in om de VM te repliceren naar Azure, waarbij de cyclus is voltooid.




## <a name="next-steps"></a>Volgende stappen
- Mislukt ten opzichte [van specifieke VMware VM's](vmware-azure-tutorial-failover-failback.md)
- Fail over [specifieke Hyper-V VM's](hyper-v-azure-failover-failback-tutorial.md).
- [Maak](site-recovery-create-recovery-plans.md) een herstelplan.
- Fail over [VM's in een herstelplan](site-recovery-failover.md).
- [Bereid je voor op](vmware-azure-failback.md) VMware herbescherming en failback.
- Fail back [Hyper-V VM's](hyper-v-azure-failback.md).

