---
title: Active Directory/DNS-noodherstel instellen met Azure Site Recovery
description: In dit artikel wordt beschreven hoe u een oplossing voor noodherstel voor Active Directory en DNS implementeert met Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 8c1f85217db12b60cdcd8ea0bdb65792b8d02648
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257808"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Disaster recovery instellen voor Active Directory en DNS

Bedrijfstoepassingen zoals SharePoint, Dynamics AX en SAP zijn afhankelijk van Active Directory en een DNS-infrastructuur om correct te kunnen functioneren. Wanneer u noodherstel voor toepassingen instelt, moet u Active Directory en DNS vaak herstellen voordat u andere toepassingsonderdelen herstelt om de juiste toepassingsfunctionaliteit te garanderen.

U [Siteherstel](site-recovery-overview.md) gebruiken om een noodherstelplan voor Active Directory te maken. Wanneer er een verstoring optreedt, u een failover starten. U kunt Active Directory al in enkele minuten operationeel krijgen. Als u Active Directory hebt geïmplementeerd voor meerdere toepassingen op uw primaire site, bijvoorbeeld voor SharePoint en SAP, wilt u mogelijk de volledige site niet overnemen. U eerst mislukken via Active Directory met siteherstel. Vervolgens u de andere toepassingen niet overlaten met toepassingsspecifieke herstelplannen.

In dit artikel wordt uitgelegd hoe u een oplossing voor noodherstel voor Active Directory maakt. Het bevat vereisten en failoverinstructies. U moet bekend zijn met Active Directory en Site Recovery voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Als u een repliceren naar Azure, [stelt u Azure-bronnen voor,](tutorial-prepare-azure.md)waaronder een abonnement, een Azure Virtual Network, een opslagaccount en een kluis van Recovery Services.
* Raadpleeg de [ondersteuningsvereisten](site-recovery-support-matrix-to-azure.md) voor alle onderdelen.

## <a name="replicate-the-domain-controller"></a>De domeincontroller repliceren

- U moet siteherstelreplicatie instellen op ten minste één vm die een domeincontroller of DNS host.
- Als u meerdere domeincontrollers in uw omgeving hebt, moet u ook een extra domeincontroller instellen op de doelsite. De extra domeincontroller kan zich in Azure bevinden of in een secundair on-premises datacenter.
- Als u slechts een paar toepassingen en één domeincontroller hebt, wilt u misschien samen de hele site beheren. In dit geval raden we u aan siteherstel te gebruiken om de domeincontroller te repliceren naar de doelsite (in Azure of in een secundair on-premises datacenter). U dezelfde gerepliceerde domeincontroller of DNS-virtuele machine gebruiken voor [testfailover.](#test-failover-considerations)
- - Als u veel toepassingen en meer dan één domeincontroller in uw omgeving hebt, of als u van plan bent om over een paar toepassingen tegelijk te falen, naast het repliceren van de virtuele machine van de domeincontroller met Site Recovery, raden we u aan een extra domeincontroller op de doelsite (in Azure of in een secundair on-premises datacenter). Voor [testfailover](#test-failover-considerations)u domeincontroller gebruiken die is gerepliceerd door Site Recovery. Voor failover u de extra domeincontroller op de doelsite gebruiken.

## <a name="enable-protection-with-site-recovery"></a>Beveiliging inschakelen met siteherstel

U Siteherstel gebruiken om de virtuele machine te beschermen die de domeincontroller of DNS host.

### <a name="protect-the-vm"></a>De VM beveiligen
De domeincontroller die wordt gerepliceerd met behulp van Site Recovery wordt gebruikt voor [testfailover.](#test-failover-considerations) Zorg ervoor dat het voldoet aan de volgende eisen:

1. De domeincontroller is een globale catalogusserver.
2. De domeincontroller moet de FSMO-roleigenaar zijn voor rollen die nodig zijn tijdens een testfailover. Anders zullen deze rollen moeten worden [inbeslaggenomen](https://aka.ms/ad_seize_fsmo) na de failover.

### <a name="configure-vm-network-settings"></a>VM-netwerkinstellingen configureren
Configureer netwerkinstellingen voor de virtuele machine die de domeincontroller of DNS host in Site Recovery onder de **compute- en netwerkinstellingen** van de gerepliceerde virtuele machine. Dit zorgt ervoor dat de virtuele machine na een failover aan het juiste netwerk is bevestigd.

## <a name="protect-active-directory"></a>Active Directory beveiligen

### <a name="site-to-site-protection"></a>Site-to-site beveiliging
Maak een domeincontroller op de secundaire site. Wanneer u de server promoot naar een domeincontrollerrol, geeft u de naam op van hetzelfde domein dat op de primaire site wordt gebruikt. U de module **Active Directory Sites and Services** gebruiken om instellingen te configureren op het sitekoppelingsobject waaraan de sites zijn toegevoegd. Door instellingen op een sitekoppeling te configureren, u bepalen wanneer replicatie plaatsvindt tussen twee of meer sites en hoe vaak deze plaatsvindt. Zie [Replicatie plannen tussen sites voor](https://technet.microsoft.com/library/cc731862.aspx)meer informatie.

### <a name="site-to-azure-protection"></a>Beveiliging van site naar Azure
Maak eerst een domeincontroller in een virtueel Azure-netwerk. Wanneer u de server promoot naar een domeincontrollerrol, geeft u dezelfde domeinnaam op die op de primaire site wordt gebruikt.

Configureer vervolgens de DNS-server voor het virtuele netwerk om de DNS-server in Azure te gebruiken.

![Azure-netwerk](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Azure-naar-Azure-beveiliging
Maak eerst een domeincontroller in een virtueel Azure-netwerk. Wanneer u de server promoot naar een domeincontrollerrol, geeft u dezelfde domeinnaam op die op de primaire site wordt gebruikt.

Configureer vervolgens de DNS-server voor het virtuele netwerk om de DNS-server in Azure te gebruiken.

## <a name="test-failover-considerations"></a>Overwegingen voor failovertesten
Om impact op productieworkloads te voorkomen, vindt testfailover plaats in een netwerk dat is geïsoleerd van het productienetwerk.

De meeste toepassingen vereisen de aanwezigheid van een domeincontroller of een DNS-server. Daarom moet u, voordat de toepassing mislukt, een domeincontroller maken in het geïsoleerde netwerk dat moet worden gebruikt voor testfailover. De eenvoudigste manier om dit te doen is door Site Recovery te gebruiken om een virtuele machine te repliceren die een domeincontroller of DNS host. Voer vervolgens een testfailover van de virtuele machine van de domeincontroller uit voordat u een testfailover uitvoert van het herstelplan voor de toepassing. Zo doe je dat:

1. Gebruik Siteherstel om de virtuele machine te [repliceren](vmware-azure-tutorial.md) die de domeincontroller of DNS host.
2. Maak een geïsoleerd netwerk. Elk virtueel netwerk dat u in Azure maakt, is standaard geïsoleerd van andere netwerken. We raden u aan hetzelfde IP-adresbereik te gebruiken voor dit netwerk dat u in uw productienetwerk gebruikt. Schakel geen site-to-site-connectiviteit in dit netwerk in.
3. Geef een DNS-IP-adres op in het geïsoleerde netwerk. Gebruik het IP-adres dat u verwacht dat de virtuele DNS-machine krijgt. Als u reproduceert naar Azure, geeft u het IP-adres op voor de virtuele machine die wordt gebruikt bij failover. Als u het IP-adres wilt invoeren, selecteert u in de gerepliceerde virtuele machine in de **instellingen voor gegevensbekabelen en netwerken** de **IP-instellingen voor targeten.**

    ![Azure-testnetwerk](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Site recovery probeert virtuele testmachines te maken in een subnet met dezelfde naam en met hetzelfde IP-adres dat is opgegeven in de **compute- en netwerkinstellingen** van de virtuele machine. Als een subnet met dezelfde naam niet beschikbaar is in het virtuele Azure-netwerk dat is voorzien voor testfailover, wordt de virtuele testmachine gemaakt in het alfabetische eerste subnet.
    >
    > Als het doel-IP-adres deel uitmaakt van het geselecteerde subnet, probeert Site Recovery de testfailover virtuele machine te maken met behulp van het doel-IP-adres. Als het doel-IP geen deel uitmaakt van het geselecteerde subnet, wordt de virtuele machine van de testfailover gemaakt met behulp van het volgende beschikbare IP in het geselecteerde subnet.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Failover testen naar een secundaire site

1. Als u re-replica's naar een andere on-premises site en u DHCP gebruikt, [stelt u DNS en DHCP in voor testfailover.](hyper-v-vmm-test-failover.md#prepare-dhcp)
2. Doe een testfailover van de virtuele machine van de domeincontroller die in het geïsoleerde netwerk wordt uitgevoerd. Gebruik het nieuwste *beschikbare toepassingsconsistente* herstelpunt van de virtuele machine van de domeincontroller om de testfailover uit te halen.
3. Voer een testfailover uit voor het herstelplan dat virtuele machines bevat waarop de toepassing wordt uitgevoerd.
4. Wanneer het testen is voltooid, *ruimt u de testfailover* op de virtuele machine van de domeincontroller op. Met deze stap wordt de domeincontroller verwijderd die is gemaakt voor een testfailover.


### <a name="remove-references-to-other-domain-controllers"></a>Verwijzingen naar andere domeincontrollers verwijderen
Wanneer u een testfailover start, neemt u niet alle domeincontrollers op in het testnetwerk. Als u verwijzingen wilt verwijderen naar andere domeincontrollers die in uw productieomgeving bestaan, moet u mogelijk [FSMO Active Directory-rollen grijpen](https://aka.ms/ad_seize_fsmo) en [metagegevens opschonen](https://technet.microsoft.com/library/cc816907.aspx) voor ontbrekende domeincontrollers.


### <a name="issues-caused-by-virtualization-safeguards"></a>Problemen veroorzaakt door virtualisatiewaarborgen

> [!IMPORTANT]
> Sommige configuraties die in deze sectie worden beschreven, zijn geen standaardconfiguratie van de domeincontroller. Als u deze wijzigingen niet wilt aanbrengen in een productiedomeincontroller, u een domeincontroller maken die is toegewezen voor Site Recovery om te gebruiken voor testfailover. Breng deze wijzigingen alleen aan die domeincontroller aan.  
>
>

Vanaf Windows Server 2012 [zijn aanvullende beveiligingen ingebouwd in Ad DS (Active Directory Domain Services).](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) Deze beveiligingen helpen gevirtualiseerde domeincontrollers te beschermen tegen USN-rollbacks als het onderliggende hypervisor-platform **VM-GenerationID**ondersteunt. Azure ondersteunt **VM-GenerationID**. Hierdoor hebben domeincontrollers die Windows Server 2012 of hoger op Azure virtuele machines uitvoeren, deze extra beveiligingen.


Wanneer **VM-GenerationID** wordt gereset, wordt ook de **AanroepID-waarde** van de AD DS-database gereset. Bovendien wordt de RID-groep verwijderd en is de map Sysvol gemarkeerd als niet-gezaghebbend. Zie [Inleiding tot active directory-domeinservices virtualisatie](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) en veilig [virtualiseren van DFSR voor](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/)meer informatie.

Als u niet overgaat naar Azure, kan **VM-GenerationID** opnieuw worden ingesteld. Als u **VM-GenerationID** opnieuw instelt, worden extra beveiligingen geactiveerd wanneer de virtuele machine van de domeincontroller wordt gestart in Azure. Dit kan leiden tot een *aanzienlijke vertraging* in de mogelijkheid om in te loggen bij de virtuele machine van de domeincontroller.

Omdat deze domeincontroller alleen wordt gebruikt in een testfailover, zijn virtualisatiebeveiligingen niet nodig. Als u ervoor wilt zorgen dat de **VM-GenerationID-waarde** voor de virtuele machine van de domeincontroller niet wordt gewijzigd, u de waarde van het volgende DWORD wijzigen in **4** in de on-premises domeincontroller:


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>Symptomen van virtualisatiewaarborgen

Als virtualisatiebeveiligingen worden geactiveerd na een mislukte test, ziet u mogelijk een of meer van de volgende symptomen:  

* De **GenerationID-waarde** verandert.

    ![Generatie-ID-wijziging](./media/site-recovery-active-directory/Event2170.png)

* De **waarde van de Aanroep-ID** verandert.

    ![Inroepen-id wijzigen](./media/site-recovery-active-directory/Event1109.png)

* Sysvol-map- en NETLOGON-shares zijn niet beschikbaar.

    ![Sysvol-mapdelen](./media/site-recovery-active-directory/sysvolshare.png)

    ![NtFrs sysvol map](./media/site-recovery-active-directory/Event13565.png)

* DFSR-databases worden verwijderd.

    ![DFSR-databases worden verwijderd](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Problemen met domeincontroller oplossen tijdens testfailover

> [!IMPORTANT]
> Sommige configuraties die in deze sectie worden beschreven, zijn geen standaardconfiguratie van de domeincontroller. Als u deze wijzigingen niet wilt aanbrengen in een productiedomeincontroller, u een domeincontroller maken die is toegewezen aan failover van de sitehersteltest. Breng de wijzigingen alleen aan die speciale domeincontroller.  
>
>

1. Voer bij de opdrachtprompt de volgende opdracht uit om te controleren of de map Sysvol en de MAP NETLOGON worden gedeeld:

    `NET SHARE`

2. Voer bij de opdrachtprompt de volgende opdracht uit om ervoor te zorgen dat de domeincontroller naar behoren functioneert:

    `dcdiag /v > dcdiag.txt`

3. Zoek in het uitvoerlogboek naar de volgende tekst. De tekst bevestigt dat de domeincontroller correct functioneert.

    * "geslaagd voor test Connectiviteit"
    * "geslaagd voor test Reclame"
    * "geslaagd voor test MachineAccount"

Als aan de voorgaande voorwaarden is voldaan, is het waarschijnlijk dat de domeincontroller correct functioneert. Als dit niet het is, voert u de volgende stappen uit:

1. Doe een gezaghebbend herstel van de domeincontroller. Houd rekening met de volgende informatie:
    * Hoewel we [FRS-replicatie](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/)niet aanbevelen, volgt u de stappen voor een gezaghebbend herstel als u FRS-replicatie gebruikt. Het proces wordt beschreven in [Het gebruik van de registersleutel BurFlags om de bestandsreplicatieservice opnieuw te initialiseren.](https://support.microsoft.com/kb/290762)

        Voor meer informatie over BurFlags, zie de blog post [D2 en D4: Waar is het voor?](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Als u DFSR-replicatie gebruikt, voert u de stappen voor een gezaghebbend herstel uit. Het proces wordt beschreven in [Force een gezaghebbende en niet-gezaghebbende synchronisatie voor DFSR-gerepliceerde sysvol map (zoals "D4/D2" voor FRS)](https://support.microsoft.com/kb/2218556).

        U ook de PowerShell-functies gebruiken. Zie [DFSR-SYSVOL gezaghebbende/niet-gezaghebbende PowerShell-functies herstellen](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/)voor meer informatie.

2. Omzeil de eerste synchronisatievereiste door de volgende registersleutel in te stellen op **0** in de on-premises domeincontroller. Als de DWORD niet bestaat, u deze maken onder het knooppunt **Parameters.**

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    Zie [Problemen met DNS-gebeurtenis-id 4013 oplossen: de DNS-server kan geen ad-geïntegreerde DNS-zones laden.](https://support.microsoft.com/kb/2001093)

3. Schakel de vereiste uit dat er een globale catalogusserver beschikbaar is om de aanmelding van de gebruiker te valideren. Stel hiervoor in de on-premises domeincontroller de volgende registersleutel in op **1**. Als de DWORD niet bestaat, u deze maken onder het **Lsa-knooppunt.**

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    Zie [De vereiste uitschakelen dat een globale catalogusserver beschikbaar is om gebruikersaanmeldingen te valideren](https://support.microsoft.com/kb/241789)voor meer informatie.

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS- en domeincontroller op verschillende machines

Als u de domeincontroller en DN's op dezelfde vm uitvoert, u deze procedure overslaan.


Als DNS niet op dezelfde VM staat als de domeincontroller, moet u een DNS-vm maken voor de testfailover. U een nieuwe DNS-server gebruiken en alle vereiste zones maken. Als uw Active Directory-domein bijvoorbeeld contoso.com is, u een DNS-zone maken met de naam contoso.com. De vermeldingen die overeenkomen met Active Directory moeten als volgt worden bijgewerkt in DNS:

1. Controleer of deze instellingen zijn ingevoerd voordat een andere virtuele machine in het herstelplan wordt gestart:
   * De zone moet worden vernoemd naar de wortelnaam van het bos.
   * De zone moet worden ondersteund door een bestand.
   * De zone moet zijn ingeschakeld voor veilige en onveilige updates.
   * De resolver van de virtuele machine die de domeincontroller host, moet naar het IP-adres van de virtuele DNS-machine wijzen.

2. Voer de volgende opdracht uit op de VM die de domeincontroller host:

    `nltest /dsregdns`

3. Voer de volgende opdrachten uit om een zone op de DNS-server toe te voegen, niet-beveiligde updates toe te staan en een vermelding voor de zone toe te voegen aan DNS:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het beveiligen van bedrijfsworkloads met Azure Site Recovery](site-recovery-workload.md).
