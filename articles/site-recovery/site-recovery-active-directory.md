---
title: Herstel na nood gevallen met Azure Site Recovery instellen Active Directory/DNS
description: In dit artikel wordt beschreven hoe u een nood herstel oplossing implementeert voor Active Directory en DNS met Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 8c1f85217db12b60cdcd8ea0bdb65792b8d02648
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362573"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Herstel na nood geval instellen voor Active Directory en DNS

Zakelijke toepassingen, zoals share point, Dynamics AX en SAP, zijn afhankelijk van Active Directory en een DNS-infra structuur die goed werkt. Wanneer u herstel na nood gevallen instelt voor toepassingen, moet u vaak Active Directory en DNS herstellen voordat u andere toepassings onderdelen herstelt, om ervoor te zorgen dat de functionaliteit van de toepassing correct werkt.

U kunt [site Recovery](site-recovery-overview.md) gebruiken om een herstel plan voor nood gevallen voor Active Directory te maken. Als er een onderbreking optreedt, kunt u een failover initiëren. U kunt binnen enkele minuten Active Directory actief zijn. Als u Active Directory hebt geïmplementeerd voor meerdere toepassingen op uw primaire site, bijvoorbeeld voor share point en SAP, wilt u mogelijk een failover uitvoeren voor de volledige site. U kunt een failover uitvoeren voor Active Directory met behulp van Site Recovery. Failover de andere toepassingen vervolgens met behulp van toepassingsspecifieke herstel plannen.

In dit artikel wordt uitgelegd hoe u een nood herstel oplossing voor Active Directory maakt. Het bevat vereisten en failover-instructies. Voordat u begint, moet u bekend zijn met Active Directory en Site Recovery.

## <a name="prerequisites"></a>Vereisten

* Als u repliceert naar Azure, [bereidt u Azure-resources](tutorial-prepare-azure.md)voor, met inbegrip van een abonnement, een Azure Virtual Network, een opslag account en een Recovery Services kluis.
* Raadpleeg de [ondersteuningsvereisten](site-recovery-support-matrix-to-azure.md) voor alle onderdelen.

## <a name="replicate-the-domain-controller"></a>De domein controller repliceren

- U moet Site Recovery replicatie instellen op ten minste één virtuele machine die als host fungeert voor een domein controller of DNS.
- Als u meerdere domein controllers in uw omgeving hebt, moet u ook een extra domein controller op de doel site instellen. De extra domein controller kan zich in azure of in een secundair on-premises Data Center bevindt.
- Als u slechts een paar toepassingen en één domein controller hebt, wilt u mogelijk een failover uitvoeren voor de hele site tegelijk. In dit geval kunt u het beste Site Recovery gebruiken om de domein controller te repliceren naar de doel site (in azure of in een secundair on-premises Data Center). U kunt dezelfde gerepliceerde domein controller of DNS-virtuele machine gebruiken [voor een testfailover.](#test-failover-considerations)
- - Als u veel toepassingen en meer dan één domein controller in uw omgeving hebt, of als u van plan bent om een failover uit te voeren voor een aantal toepassingen tegelijk, wordt u aangeraden de virtuele machine van de domein controller te repliceren met Site Recovery, maar u kunt ook het beste een extra domein controller op de doel site (in azure of in een secundair on-premises Data Center). Voor [een testfailover kunt](#test-failover-considerations)u een domein controller gebruiken die wordt gerepliceerd door site Recovery. Voor failover kunt u de extra domein controller op de doel site gebruiken.

## <a name="enable-protection-with-site-recovery"></a>Beveiliging met Site Recovery inschakelen

U kunt Site Recovery gebruiken om de virtuele machine te beveiligen die als host fungeert voor de domein controller of DNS.

### <a name="protect-the-vm"></a>De virtuele machine beveiligen
De domein controller die wordt gerepliceerd met behulp van Site Recovery wordt [gebruikt voor de testfailover.](#test-failover-considerations) Zorg ervoor dat het voldoet aan de volgende vereisten:

1. De domein controller is een globale catalogus server.
2. De domein controller moet de eigenaar van de FSMO-rol zijn voor rollen die nodig zijn tijdens een testfailover. Als dat niet het geval is, moeten deze rollen worden [overgenomen](https://aka.ms/ad_seize_fsmo) na de failover.

### <a name="configure-vm-network-settings"></a>VM-netwerk instellingen configureren
Voor de virtuele machine die als host fungeert voor de domein controller of DNS, in Site Recovery netwerk instellingen configureren onder de **berekenings-en netwerk** instellingen van de gerepliceerde virtuele machine. Dit zorgt ervoor dat de virtuele machine na een failover aan het juiste netwerk is gekoppeld.

## <a name="protect-active-directory"></a>Active Directory beveiligen

### <a name="site-to-site-protection"></a>Site-naar-site-beveiliging
Maak een domein controller op de secundaire site. Wanneer u de server naar een rol domein controller promo veren, geeft u de naam op van hetzelfde domein dat wordt gebruikt op de primaire site. U kunt de module **Active Directory sites en services** gebruiken om instellingen te configureren voor het site koppelings object waaraan de sites worden toegevoegd. Door instellingen op een site koppeling te configureren, kunt u bepalen wanneer replicatie tussen twee of meer sites plaatsvindt, en hoe vaak deze plaatsvindt. Zie [replicatie plannen tussen sites](https://technet.microsoft.com/library/cc731862.aspx)voor meer informatie.

### <a name="site-to-azure-protection"></a>Beveiliging van site-naar-Azure
Maak eerst een domein controller in een virtueel Azure-netwerk. Wanneer u de server naar een rol domein controller promo veren, geeft u dezelfde domein naam op als voor de primaire site.

Configureer vervolgens de DNS-server voor het virtuele netwerk opnieuw voor het gebruik van de DNS-server in Azure.

![Azure-netwerk](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Azure-naar-Azure-beveiliging
Maak eerst een domein controller in een virtueel Azure-netwerk. Wanneer u de server naar een rol domein controller promo veren, geeft u dezelfde domein naam op als voor de primaire site.

Configureer vervolgens de DNS-server voor het virtuele netwerk opnieuw voor het gebruik van de DNS-server in Azure.

## <a name="test-failover-considerations"></a>Overwegingen voor testfailover
Om te voor komen dat er gevolgen zijn voor de werk belasting van de productie, treedt er een testfailover op in een netwerk dat is geïsoleerd van het productie netwerk.

De meeste toepassingen moeten de aanwezigheid van een domein controller of een DNS-server hebben. Daarom moet u voordat de toepassing een failover uitvoeren een domein controller in het geïsoleerde netwerk maken die voor testfailover moet worden gebruikt. De eenvoudigste manier om dit te doen is door Site Recovery te gebruiken voor het repliceren van een virtuele machine die als host fungeert voor een domein controller of DNS. Voer vervolgens een testfailover van de virtuele machine van de domein controller uit voordat u een testfailover uitvoert van het herstel plan voor de toepassing. U doet dit als volgt:

1. Gebruik Site Recovery om de virtuele machine te [repliceren](vmware-azure-tutorial.md) die als host fungeert voor de domein controller of DNS.
2. Een geïsoleerd netwerk maken. Elk virtueel netwerk dat u in azure maakt, is standaard geïsoleerd van andere netwerken. U wordt aangeraden hetzelfde IP-adres bereik te gebruiken voor dit netwerk dat u gebruikt in uw productie netwerk. Schakel geen site-naar-site-connectiviteit in op dit netwerk.
3. Geef een DNS IP-adres op in het geïsoleerde netwerk. Gebruik het IP-adres dat u verwacht dat de virtuele DNS-machine wordt opgehaald. Als u repliceert naar Azure, geeft u het IP-adres op voor de virtuele machine die wordt gebruikt voor failover. Als u het IP-adres wilt invoeren, selecteert u in de **berekening-en netwerk** instellingen van de gerepliceerde virtuele machine de **doel-IP-** instellingen.

    ![Azure-test netwerk](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Site Recovery probeert virtuele test machines te maken in een subnet met dezelfde naam en met behulp van hetzelfde IP-adres dat is opgenomen in de **reken-en netwerk** instellingen van de virtuele machine. Als een subnet met dezelfde naam niet beschikbaar is in het virtuele netwerk van Azure dat wordt weer gegeven voor testfailover, wordt de virtuele test machine in het alfabetisch eerste subnet gemaakt.
    >
    > Als het doel-IP-adres deel uitmaakt van het geselecteerde subnet, Site Recovery probeert de virtuele testfailover te maken met behulp van het doel-IP-adres. Als het doel-IP-adres niet deel uitmaakt van het geselecteerde subnet, wordt de virtuele test machine gemaakt met behulp van het volgende beschik bare IP-adres in het geselecteerde subnet.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Failover naar een secundaire site testen

1. Als u repliceert naar een andere on-premises site en u DHCP gebruikt, [stelt u DNS en DHCP in voor](hyper-v-vmm-test-failover.md#prepare-dhcp)de testfailover.
2. Voer een testfailover uit van de virtuele machine van de domein controller die wordt uitgevoerd in het geïsoleerde netwerk. Gebruik het meest recente beschik bare *toepassings consistente* herstel punt van de virtuele machine van de domein controller om de testfailover uit te voeren.
3. Voer een testfailover uit voor het herstel plan dat virtuele machines bevat waarop de toepassing wordt uitgevoerd.
4. Wanneer het testen is voltooid, moet u de testfailover op de virtuele machine van de domein controller *opschonen* . Met deze stap wordt de domein controller verwijderd die is gemaakt voor de testfailover.


### <a name="remove-references-to-other-domain-controllers"></a>Verwijzingen naar andere domein controllers verwijderen
Wanneer u een testfailover initieert, neemt u niet alle domein controllers op in het test netwerk. Als u verwijzingen naar andere domein controllers die in uw productie omgeving bestaan wilt verwijderen, moet u mogelijk [FSMO-Active Directory rollen overnemen](https://aka.ms/ad_seize_fsmo) en [meta gegevens opruimen](https://technet.microsoft.com/library/cc816907.aspx) voor ontbrekende domein controllers.


### <a name="issues-caused-by-virtualization-safeguards"></a>Problemen die worden veroorzaakt door veiligheids maatregelen voor virtualisatie

> [!IMPORTANT]
> Enkele van de configuraties die in deze sectie worden beschreven, zijn geen standaard configuraties voor domein controllers. Als u deze wijzigingen niet wilt aanbrengen in een productie domein controller, kunt u een domein controller maken die is toegewezen voor Site Recovery voor het gebruik van een testfailover. Breng deze wijzigingen alleen aan in die domein controller.  
>
>

Vanaf Windows Server 2012 [zijn extra beveiligingen ingebouwd in Active Directory Domain Services (AD DS)](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Met deze beveiligings maatregelen kunt u gevirtualiseerde domein controllers beveiligen tegen USN-terugdraai acties als het onderliggende Hyper Visor platform **VM-GenerationID**ondersteunt. Azure biedt ondersteuning voor **VM-GenerationID**. Daarom hebben domein controllers met Windows Server 2012 of hoger op virtuele machines van Azure deze extra beveiliging.


Wanneer de **VM-GenerationID** opnieuw wordt ingesteld, wordt de **InvocationID** -waarde van de AD DS data base ook opnieuw ingesteld. Daarnaast wordt de RID-groep verwijderd en wordt de map SYSVOL gemarkeerd als niet-gemachtigd. Zie [Inleiding tot Active Directory Domain Services virtualisatie en het](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) [veilig virtualiseren van DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/)voor meer informatie.

Failover naar Azure kan ertoe leiden dat **VM-GenerationID** opnieuw wordt ingesteld. Als u de virtuele machine opnieuw instelt, worden er extra beveiligingen geactiveerd wanneer de domein controller **-** VM wordt gestart in Azure. Dit kan leiden tot een *aanzienlijke vertraging* in de mogelijkheid om u aan te melden bij de virtuele machine van de domein controller.

Omdat deze domein controller alleen wordt gebruikt in een testfailover, zijn er geen veiligheids maatregelen voor virtualisatie nodig. Om ervoor te zorgen dat de **VM-GenerationID-** waarde voor de virtuele machine van de domein controller niet verandert, kunt u de waarde van de volgende DWORD wijzigen in **4** op de on-premises domein controller:


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>Symptomen van de beveiliging van virtualisatie

Als de beveiliging van de virtualisatie wordt geactiveerd na een testfailover, ziet u mogelijk een of meer van de volgende symptomen:  

* De waarde van **GenerationID** wordt gewijzigd.

    ![Wijziging van generatie-ID](./media/site-recovery-active-directory/Event2170.png)

* De waarde van **InvocationID** wordt gewijzigd.

    ![Wijziging van aanroepen-ID](./media/site-recovery-active-directory/Event1109.png)

* SYSVOL-map en NETLOGON-shares zijn niet beschikbaar.

    ![Share SYSVOL-map](./media/site-recovery-active-directory/sysvolshare.png)

    ![Map NtFrs SYSVOL](./media/site-recovery-active-directory/Event13565.png)

* DFSR-data bases worden verwijderd.

    ![DFSR-data bases zijn verwijderd](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Problemen met de domein controller oplossen tijdens een testfailover

> [!IMPORTANT]
> Enkele van de configuraties die in deze sectie worden beschreven, zijn geen standaard configuraties voor domein controllers. Als u deze wijzigingen niet wilt aanbrengen in een productie domein controller, kunt u een domein controller maken die is toegewezen voor Site Recovery testfailover. Breng de gewenste wijzigingen aan in die specifieke domein controller.  
>
>

1. Voer bij de opdracht prompt de volgende opdracht uit om te controleren of de map SYSVOL en NETLOGON worden gedeeld:

    `NET SHARE`

2. Voer bij de opdracht prompt de volgende opdracht uit om ervoor te zorgen dat de domein controller goed werkt:

    `dcdiag /v > dcdiag.txt`

3. Zoek in het uitvoer logboek naar de volgende tekst. De tekst bevestigt dat de domein controller goed werkt.

    * geslaagde test connectiviteit
    * "geslaagde test advertenties"
    * "test MachineAccount geslaagd"

Als aan de voor waarden wordt voldaan, is het waarschijnlijk dat de domein controller goed werkt. Als dat niet het geval is, voert u de volgende stappen uit:

1. Een bindende terugzet bewerking uitvoeren voor de domein controller. Houd de volgende informatie in acht:
    * Hoewel we geen [FRS-replicatie](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/)aanraden, kunt u, als u FRS-replicatie gebruikt, de stappen voor een bindende terugzet bewerking volgen. Het proces wordt beschreven in [met behulp van de register sleutel BurFlags om de File Replication-service opnieuw te initialiseren](https://support.microsoft.com/kb/290762).

        Zie voor meer informatie over BurFlags het blog bericht [D2 en D4: wat is het voor?](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Als u DFSR-replicatie gebruikt, voltooit u de stappen voor een bindende terugzet bewerking. Het proces wordt beschreven in [een bindende en niet-bindende synchronisatie afdwingen voor de DFSR-gerepliceerde SYSVOL-map (zoals "D4/D2" voor FRS)](https://support.microsoft.com/kb/2218556).

        U kunt ook de Power shell-functies gebruiken. Zie voor meer informatie [DFSR-SYSVOL gezaghebbend/niet-bindende terugzet Power shell-functies](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/).

2. U kunt de eerste synchronisatie vereiste overs Laan door de volgende register sleutel in te stellen op **0** in de on-premises domein controller. Als de DWORD niet bestaat, kunt u deze maken onder het knoop punt **para meters** .

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    Zie voor meer informatie [problemen met de DNS-gebeurtenis-ID 4013 oplossen: de DNS-server kan AD geïntegreerde DNS-zones niet laden](https://support.microsoft.com/kb/2001093).

3. De vereiste uitschakelen dat een globale catalogus server beschikbaar is voor het valideren van de aanmelding van de gebruiker. Om dit te doen, stelt u op de on-premises domein controller de volgende register sleutel in op **1**. Als de DWORD niet bestaat, kunt u deze maken onder het **LSA** -knoop punt.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    Zie [de vereiste uitschakelen dat een globale-catalogus server beschikbaar is voor het valideren van gebruikers aanmeldingen](https://support.microsoft.com/kb/241789)voor meer informatie.

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS en domein controller op verschillende computers

Als u de domein controller en DNs op dezelfde VM uitvoert, kunt u deze procedure overs Laan.


Als DNS zich niet op dezelfde VM bevindt als de domein controller, moet u een DNS-VM maken voor de testfailover. U kunt een nieuwe DNS-server gebruiken en alle vereiste zones maken. Als uw Active Directory domein bijvoorbeeld contoso.com is, kunt u een DNS-zone maken met de naam contoso.com. De vermeldingen die overeenkomen met Active Directory moeten als volgt in DNS worden bijgewerkt:

1. Zorg ervoor dat deze instellingen aanwezig zijn voordat een andere virtuele machine in het herstel plan wordt gestart:
   * De zone moet de naam van de hoofd naam van het forest hebben.
   * De zone moet worden ondersteund door een bestand.
   * De zone moet zijn ingeschakeld voor beveiligde en niet-beveiligde updates.
   * De resolver van de virtuele machine die als host fungeert voor de domein controller moet verwijzen naar het IP-adres van de virtuele DNS-machine.

2. Voer de volgende opdracht uit op de VM die als host fungeert voor de domein controller:

    `nltest /dsregdns`

3. Voer de volgende opdrachten uit om een zone toe te voegen aan de DNS-server, niet-beveiligde updates toe te staan en een vermelding voor de zone toe te voegen aan DNS:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [beveiligen van zakelijke workloads met Azure site Recovery](site-recovery-workload.md).
