---
title: Active Directory/DNS-noodherstel instellen met Azure Site Recovery
description: In dit artikel wordt beschreven hoe u een oplossing voor noodherstel voor Active Directory en DNS implementeert met Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mayg
ms.openlocfilehash: 2cf4f22be2a4407d73fcc7bb340fad647c8aa145
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546523"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Disaster recovery instellen voor Active Directory en DNS

Bedrijfstoepassingen zoals SharePoint, Dynamics AX en SAP zijn afhankelijk van Active Directory en een DNS-infrastructuur om correct te kunnen functioneren. Wanneer u noodherstel voor toepassingen instelt, moet u vaak Active Directory en Domain Name System (DNS) herstellen voordat u andere toepassingsonderdelen herstelt, om de juiste toepassingsfunctionaliteit te garanderen.

U [Siteherstel](site-recovery-overview.md) gebruiken om een noodherstelplan voor Active Directory te maken. Wanneer er een verstoring optreedt, u een failover starten. U kunt Active Directory al in enkele minuten operationeel krijgen. Als u Active Directory hebt geïmplementeerd voor meerdere toepassingen op uw primaire site, bijvoorbeeld voor SharePoint en SAP, wilt u mogelijk de volledige site niet overnemen. U eerst mislukken via Active Directory met siteherstel. Vervolgens u de andere toepassingen niet overlaten met toepassingsspecifieke herstelplannen.

In dit artikel wordt uitgelegd hoe u een oplossing voor noodherstel voor Active Directory maakt. Het bevat vereisten en failoverinstructies. U moet bekend zijn met Active Directory en Site Recovery voordat u begint.

## <a name="prerequisites"></a>Vereisten

- Als u een repliceren naar Azure, [stelt u Azure-bronnen voor,](tutorial-prepare-azure.md)waaronder een abonnement, een Azure Virtual Network, een opslagaccount en een kluis van Recovery Services.
- Raadpleeg de [ondersteuningsvereisten](site-recovery-support-matrix-to-azure.md) voor alle onderdelen.

## <a name="replicate-the-domain-controller"></a>De domeincontroller repliceren

- U moet siteherstelreplicatie instellen op ten minste één virtuele machine (VM) die een domeincontroller of DNS host.
- Als u meerdere domeincontrollers in uw omgeving hebt, moet u ook een extra domeincontroller instellen op de doelsite. De extra domeincontroller kan zich in Azure bevinden of in een secundair on-premises datacenter.
- Als u slechts een paar toepassingen en één domeincontroller hebt, wilt u misschien samen de hele site beheren. In dit geval raden we u aan siteherstel te gebruiken om de domeincontroller te repliceren naar de doelsite, in Azure of in een secundair on-premises datacenter. U dezelfde gerepliceerde domeincontroller of DNS-virtuele machine gebruiken voor [testfailover.](#test-failover-considerations)
- Als u veel toepassingen en meer dan één domeincontroller in uw omgeving hebt, of als u van plan bent om een paar toepassingen tegelijk te mislukken, naast het repliceren van de virtuele machine van de domeincontroller met Site Recovery, raden we u aan een extra domeincontroller in te stellen op de doelsite (in Azure of in een secundair on-premises datacenter). Voor [testfailover](#test-failover-considerations)u een domeincontroller gebruiken die is gerepliceerd door Site Recovery. Voor failover u de extra domeincontroller op de doelsite gebruiken.

## <a name="enable-protection-with-site-recovery"></a>Beveiliging inschakelen met siteherstel

U Siteherstel gebruiken om de virtuele machine te beschermen die de domeincontroller of DNS host.

### <a name="protect-the-vm"></a>De VM beveiligen

De domeincontroller die wordt gerepliceerd met behulp van Site Recovery wordt gebruikt voor [testfailover.](#test-failover-considerations) Zorg ervoor dat het voldoet aan de volgende eisen:

1. De domeincontroller is een globale catalogusserver.
1. De domeincontroller moet de roleigenaar van de Flexible Single Master Operations (FSMO) zijn voor rollen die nodig zijn tijdens een testfailover. Anders zullen deze rollen moeten worden [inbeslaggenomen](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) na de failover.

### <a name="configure-vm-network-settings"></a>VM-netwerkinstellingen configureren

Configureer netwerkinstellingen voor de virtuele machine die de domeincontroller of DNS host in Site Recovery onder de **compute- en netwerkinstellingen** van de gerepliceerde virtuele machine. Dit zorgt ervoor dat de virtuele machine na een failover aan het juiste netwerk is bevestigd.

## <a name="protect-active-directory"></a>Active Directory beveiligen

### <a name="site-to-site-protection"></a>Site-to-site beveiliging

Maak een domeincontroller op de secundaire site. Wanneer u de server promoot naar een domeincontrollerrol, geeft u de naam op van hetzelfde domein dat op de primaire site wordt gebruikt. U de module **Active Directory Sites and Services** gebruiken om instellingen te configureren op het sitekoppelingsobject waaraan de sites zijn toegevoegd. Door instellingen op een sitekoppeling te configureren, u bepalen wanneer replicatie plaatsvindt tussen twee of meer sites en hoe vaak deze plaatsvindt. Zie [Replicatie plannen tussen sites voor](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731862(v=ws.11))meer informatie.

### <a name="site-to-azure-protection"></a>Beveiliging van site naar Azure

Maak eerst een domeincontroller in een virtueel Azure-netwerk. Wanneer u de server promoot naar een domeincontrollerrol, geeft u dezelfde domeinnaam op die op de primaire site wordt gebruikt.

Configureer vervolgens de DNS-server voor het virtuele netwerk om de DNS-server in Azure te gebruiken.

:::image type="content" source="./media/site-recovery-active-directory/azure-network.png" alt-text="Azure-netwerk":::

### <a name="azure-to-azure-protection"></a>Azure-naar-Azure-beveiliging

Maak eerst een domeincontroller in een virtueel Azure-netwerk. Wanneer u de server promoot naar een domeincontrollerrol, geeft u dezelfde domeinnaam op die op de primaire site wordt gebruikt.

Configureer vervolgens de DNS-server voor het virtuele netwerk om de DNS-server in Azure te gebruiken.

## <a name="test-failover-considerations"></a>Overwegingen voor failovertesten

Om impact op productieworkloads te voorkomen, vindt de testfailover plaats in een netwerk dat is geïsoleerd van het productienetwerk.

De meeste toepassingen vereisen de aanwezigheid van een domeincontroller of een DNS-server. Daarom moet u, voordat de toepassing mislukt, een domeincontroller maken in het geïsoleerde netwerk dat moet worden gebruikt voor testfailover. De eenvoudigste manier om dit te doen is door Site Recovery te gebruiken om een virtuele machine te repliceren die een domeincontroller of DNS host. Voer vervolgens een testfailover van de virtuele machine van de domeincontroller uit voordat u een testfailover uitvoert van het herstelplan voor de toepassing.

1. Gebruik Siteherstel om de virtuele machine te [repliceren](vmware-azure-tutorial.md) die de domeincontroller of DNS host.
1. Maak een geïsoleerd netwerk. Elk virtueel netwerk dat u in Azure maakt, is standaard geïsoleerd van andere netwerken. We raden u aan hetzelfde IP-adresbereik te gebruiken voor dit netwerk dat u in uw productienetwerk gebruikt. Schakel geen site-to-site-connectiviteit in dit netwerk in.
1. Geef een DNS-IP-adres op in het geïsoleerde netwerk. Gebruik het IP-adres dat u verwacht dat de virtuele DNS-machine krijgt. Als u reproduceert naar Azure, geeft u het IP-adres op voor de virtuele machine die wordt gebruikt bij failover. Als u het IP-adres wilt invoeren, selecteert u in de gerepliceerde virtuele machine in de **instellingen voor gegevensbekabelen en netwerken** de **IP-instellingen voor targeten.**

   :::image type="content" source="./media/site-recovery-active-directory/azure-test-network.png" alt-text="Azure-testnetwerk":::

   > [!TIP]
   > Site recovery probeert virtuele testmachines te maken in een subnet met dezelfde naam en met hetzelfde IP-adres dat is opgegeven in de **compute- en netwerkinstellingen** van de virtuele machine. Als een subnet met dezelfde naam niet beschikbaar is in het virtuele Azure-netwerk dat is voorzien voor testfailover, wordt de virtuele testmachine gemaakt in het alfabetische eerste subnet.
   >
   > Als het doel-IP-adres deel uitmaakt van het geselecteerde subnet, probeert Site Recovery de testfailover virtuele machine te maken met behulp van het doel-IP-adres. Als het doel-IP geen deel uitmaakt van het geselecteerde subnet, wordt de virtuele machine van de testfailover gemaakt met behulp van het volgende beschikbare IP in het geselecteerde subnet.

### <a name="test-failover-to-a-secondary-site"></a>Failover testen naar een secundaire site

1. Als u re-replica's naar een andere on-premises site en u DHCP gebruikt, [stelt u DNS en DHCP in voor testfailover.](hyper-v-vmm-test-failover.md#prepare-dhcp)
1. Doe een testfailover van de virtuele machine van de domeincontroller die in het geïsoleerde netwerk wordt uitgevoerd. Gebruik het nieuwste _beschikbare toepassingsconsistente_ herstelpunt van de virtuele machine van de domeincontroller om de testfailover uit te halen.
1. Voer een testfailover uit voor het herstelplan dat virtuele machines bevat waarop de toepassing wordt uitgevoerd.
1. Wanneer het testen is voltooid, _ruimt u de testfailover_ op de virtuele machine van de domeincontroller op. Met deze stap wordt de domeincontroller verwijderd die is gemaakt voor een testfailover.

### <a name="remove-references-to-other-domain-controllers"></a>Verwijzingen naar andere domeincontrollers verwijderen

Wanneer u een testfailover start, neemt u niet alle domeincontrollers op in het testnetwerk. Als u verwijzingen wilt verwijderen naar andere domeincontrollers die in uw productieomgeving bestaan, moet u mogelijk [FSMO Active Directory-rollen grijpen](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) en [metagegevens opschonen](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc816907(v=ws.10)) voor ontbrekende domeincontrollers.

### <a name="issues-caused-by-virtualization-safeguards"></a>Problemen veroorzaakt door virtualisatiewaarborgen

> [!IMPORTANT]
> Sommige configuraties die in deze sectie worden beschreven, zijn geen standaardconfiguratie van de domeincontroller. Als u deze wijzigingen niet wilt aanbrengen in een productiedomeincontroller, u een domeincontroller maken die is toegewezen voor Site Recovery om te gebruiken voor testfailover. Breng deze wijzigingen alleen aan die domeincontroller aan.

Vanaf Windows Server 2012 [zijn aanvullende beveiligingen ingebouwd in Ad DS (Active Directory Domain Services).](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) Deze beveiligingen helpen gevirtualiseerde domeincontrollers te beschermen tegen updatesequentienummer (USN) rollbacks als het onderliggende hypervisorplatform **VM-GenerationID**ondersteunt. Azure ondersteunt **VM-GenerationID**. Hierdoor hebben domeincontrollers die Windows Server 2012 of hoger op Azure virtuele machines uitvoeren, deze extra beveiligingen.

Wanneer **VM-GenerationID** wordt gereset, wordt ook de **AanroepID-waarde** van de AD DS-database gereset. Bovendien wordt de groep relatieve ID (RID) `SYSVOL` verwijderd en wordt de map gemarkeerd als niet-gezaghebbend. Zie [Inleiding tot active directory domain services-virtualisatie](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) en het veilig [virtualiseren van DFSR (Distributed File System Replication)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/safely-virtualizing-dfsr/ba-p/424671)voor meer informatie.

Als u niet overgaat naar Azure, kan **VM-GenerationID** opnieuw worden ingesteld. Als u **VM-GenerationID** opnieuw instelt, worden extra beveiligingen geactiveerd wanneer de virtuele machine van de domeincontroller wordt gestart in Azure. Dit kan leiden tot een aanzienlijke vertraging in de mogelijkheid om in te loggen bij de virtuele machine van de domeincontroller.

Omdat deze domeincontroller alleen wordt gebruikt in een testfailover, zijn virtualisatiebeveiligingen niet nodig. Als u ervoor wilt zorgen dat de **VM-GenerationID-waarde** voor de virtuele `DWORD` machine van de domeincontroller niet verandert, u de waarde van volgende wijzigen in **4** in de on-premises domeincontroller:

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`

#### <a name="symptoms-of-virtualization-safeguards"></a>Symptomen van virtualisatiewaarborgen

Als virtualisatiebeveiligingen worden geactiveerd na een mislukte test, ziet u mogelijk een of meer van de volgende symptomen:

- De **GenerationID-waarde** verandert:

  :::image type="content" source="./media/site-recovery-active-directory/Event2170.png" alt-text="Generatie-ID-wijziging":::

- De **waarde van de Aanroep-ID** verandert:

  :::image type="content" source="./media/site-recovery-active-directory/Event1109.png" alt-text="Inroepen-id wijzigen":::

- `SYSVOL`map `NETLOGON` en shares zijn niet beschikbaar.

  :::image type="content" source="./media/site-recovery-active-directory/sysvolshare.png" alt-text="SYSVOL-mapdelen":::

  :::image type="content" source="./media/site-recovery-active-directory/Event13565.png" alt-text="NtFrs SYSVOL-map":::

- DFSR-databases worden verwijderd.

  :::image type="content" source="./media/site-recovery-active-directory/Event2208.png" alt-text="DFSR-databases worden verwijderd":::

### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Problemen met domeincontroller oplossen tijdens testfailover

> [!IMPORTANT]
> Sommige configuraties die in deze sectie worden beschreven, zijn geen standaardconfiguratie van de domeincontroller. Als u deze wijzigingen niet wilt aanbrengen in een productiedomeincontroller, u een domeincontroller maken die is toegewezen aan failover van de sitehersteltest. Breng de wijzigingen alleen aan die speciale domeincontroller.

1. Voer bij de opdrachtprompt de volgende `SYSVOL` opdracht `NETLOGON` uit om te controleren of map en map worden gedeeld:

    `NET SHARE`

1. Voer bij de opdrachtprompt de volgende opdracht uit om ervoor te zorgen dat de domeincontroller naar behoren functioneert:

    `dcdiag /v > dcdiag.txt`

1. Zoek in het uitvoerlogboek naar de volgende tekst. De tekst bevestigt dat de domeincontroller correct functioneert.

    - `passed test Connectivity`
    - `passed test Advertising`
    - `passed test MachineAccount`

Als aan de voorgaande voorwaarden is voldaan, is het waarschijnlijk dat de domeincontroller correct functioneert. Als dit niet het is, voert u de volgende stappen uit:

1. Doe een gezaghebbend herstel van de domeincontroller. Houd rekening met de volgende informatie:

    - Hoewel we replicatie met de [File Replication Service (FRS)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/the-end-is-nigh-for-frs-8211-updated-for-ws2016/ba-p/425379)niet aanbevelen, voert u de stappen voor een gezaghebbend herstel uit als u FRS-replicatie gebruikt. Het proces wordt beschreven in [Het gebruik van de registersleutel BurFlags om de bestandsreplicatieservice opnieuw te initialiseren.](https://support.microsoft.com/kb/290762)

      Voor meer informatie over BurFlags, zie de blog post [D2 en D4: Waar is het voor?](/archive/blogs/janelewis/d2-and-d4-what-is-it-for).

    - Als u DFSR-replicatie gebruikt, voert u de stappen voor een gezaghebbend herstel uit. Het proces wordt beschreven in [Force een gezaghebbende en niet-gezaghebbende synchronisatie voor DFSR-gerepliceerde SYSVOL-map (zoals "D4/D2" voor FRS)](https://support.microsoft.com/kb/2218556).

      U ook de PowerShell-functies gebruiken. Zie [DFSR-SYSVOL gezaghebbende/niet-gezaghebbende PowerShell-functies herstellen](/archive/blogs/thbouche/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions)voor meer informatie.

1. Omzeil de eerste synchronisatievereiste door de volgende registersleutel in te stellen op **0** in de on-premises domeincontroller. Als `DWORD` deze niet bestaat, u deze maken onder het knooppunt **Parameters.**

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

   Zie [Problemen met DNS-gebeurtenis-id 4013 oplossen: de DNS-server kan geen ad-geïntegreerde DNS-zones laden.](https://support.microsoft.com/kb/2001093)

1. Schakel de vereiste uit dat er een globale catalogusserver beschikbaar is om de aanmelding van de gebruiker te valideren. Stel hiervoor in de on-premises domeincontroller de volgende registersleutel in op **1**. Als `DWORD` het niet bestaat, u deze maken onder het **Lsa-knooppunt.**

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

   Zie [Hoe de globale catalogus werkt](/previous-versions/windows/it-pro/windows-server-2003/cc737410(v=ws.10))voor meer informatie .

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS- en domeincontroller op verschillende machines

Als u de domeincontroller en DN's op dezelfde vm uitvoert, u deze procedure overslaan.

Als DNS niet op dezelfde VM staat als de domeincontroller, moet u een DNS-vm maken voor de testfailover. U een nieuwe DNS-server gebruiken en alle vereiste zones maken. Als uw Active Directory-domein `contoso.com`bijvoorbeeld is, u `contoso.com`een DNS-zone maken met de naam. De vermeldingen die overeenkomen met Active Directory moeten als volgt worden bijgewerkt in DNS:

1. Controleer of deze instellingen zijn ingevoerd voordat een andere virtuele machine in het herstelplan wordt gestart:

   - De zone moet worden vernoemd naar de wortelnaam van het bos.
   - De zone moet worden ondersteund door een bestand.
   - De zone moet zijn ingeschakeld voor veilige en onveilige updates.
   - De resolver van de virtuele machine die de domeincontroller host, moet naar het IP-adres van de virtuele DNS-machine wijzen.

1. Voer de volgende opdracht uit op de VM die de domeincontroller host:

   `nltest /dsregdns`

1. Voer de volgende opdrachten uit om een zone op de DNS-server toe te voegen, niet-beveiligde updates toe te staan en een vermelding voor de zone toe te voegen aan DNS:

   ```cmd
   dnscmd /zoneadd contoso.com  /Primary

   dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1

   dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>

   dnscmd /config contoso.com /allowupdate 1
   ```

## <a name="next-steps"></a>Volgende stappen

[Meer informatie](site-recovery-workload.md) over het beveiligen van bedrijfsworkloads met Azure Site Recovery.
