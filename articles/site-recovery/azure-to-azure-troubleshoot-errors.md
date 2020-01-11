---
title: Problemen met Azure VM-replicatie in Azure Site Recovery oplossen
description: Los fouten op bij het repliceren van virtuele Azure-machines voor herstel na nood gevallen.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: asgangal
ms.openlocfilehash: efa05f5769bcd174992a7a91a522c5f30ce6e448
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895015"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Replicatie fouten van Azure naar Azure-VM oplossen

In dit artikel wordt beschreven hoe u veelvoorkomende fouten in Azure Site Recovery oplost tijdens de replicatie en het herstel van virtuele Azure-machines (Vm's) van de ene regio naar de andere. Zie voor meer informatie over ondersteunde configuraties, de [ondersteuningsmatrix voor het repliceren van virtuele Azure-machines](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemen met Azure-resource quota (fout code 150097)

Zorg ervoor dat uw abonnement is ingeschakeld voor het maken van virtuele Azure-machines in de doel regio die u wilt gebruiken als uw regio voor herstel na nood gevallen. Zorg er ook voor dat uw abonnement voldoende quotum heeft voor het maken van Vm's met de benodigde grootten. Site Recovery kiest standaard een doel-VM-grootte die gelijk is aan de grootte van de bron-VM. Als de overeenkomende grootte niet beschikbaar is, kiest Site Recovery automatisch de dichtstbijzijnde beschik bare grootte.

Als er geen grootte is die de configuratie van de bron-VM ondersteunt, wordt het volgende bericht weer gegeven:

> Replicatie kan niet worden ingeschakeld voor de *VmName*van de virtuele machine.

### <a name="possible-causes"></a>Mogelijke oorzaken

- Uw abonnements-ID is niet ingeschakeld voor het maken van virtuele machines in de doel regio locatie.
- Uw abonnements-ID is niet ingeschakeld, of heeft onvoldoende quotum, om specifieke VM-grootten in de doel regio locatie te maken.
- Er is geen geschikte VM-grootte gevonden die overeenkomt met de netwerk interface kaart (NIC) van de bron-VM (2) voor de abonnements-ID in de doel regio locatie.

### <a name="fix-the-problem"></a>Het probleem wordt opgelost

Neem contact op met de [ondersteuning voor Azure-facturering](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) om uw abonnement in te scha kelen voor het maken van vm's van de vereiste grootten op de doel locatie. Voer de mislukte bewerking opnieuw uit.

Als de doel locatie een capaciteits beperking heeft, schakelt u de replicatie hiervan uit. Schakel vervolgens replicatie naar een andere locatie in waar uw abonnement voldoende quota heeft om Vm's van de vereiste grootten te maken.

## <a name="trusted-root-certificates-error-code-151066"></a>Vertrouwde basis certificaten (fout code 151066)

Als niet alle meest recente vertrouwde basis certificaten aanwezig zijn op de virtuele machine, kan de taak voor het inschakelen van replicatie Site Recovery mislukken. Verificatie en autorisatie van Site Recovery service aanroepen van de virtuele machine mislukt zonder deze certificaten. 

Als de taak replicatie inschakelen mislukt, wordt het volgende bericht weer gegeven:

> De configuratie van de Site Recovery is mislukt.

### <a name="possible-cause"></a>Mogelijke oorzaak

De vertrouwde basis certificaten die vereist zijn voor verificatie en verificatie zijn niet aanwezig op de virtuele machine.

### <a name="fix-the-problem"></a>Het probleem wordt opgelost

#### <a name="windows"></a>Windows

Voor een virtuele machine waarop het Windows-besturings systeem wordt uitgevoerd, installeert u de meest recente Windows-updates op de VM zodat alle vertrouwde basis certificaten aanwezig zijn op de computer. Volg de gebruikelijke Windows Update-Management-of certificaat update-Management-procedure in uw organisatie om de meest recente basis certificaten en de bijgewerkte lijst met certificaat intrekkingen op de Vm's te verkrijgen.

Als u zich in een omgeving zonder verbinding, volgt u de standaard Windows update-proces in uw organisatie om op te halen van de certificaten. Als de vereiste certificaten niet aanwezig op de virtuele machine, mislukt de aanroepen naar de Site Recovery-service voor opmaaktalen wordt om beveiligingsredenen.

Om te controleren of het probleem opgelost is, gaat u naar login.microsoftonline.com vanuit een browser op uw virtuele machine.

Zie [vertrouwde hoofd mappen en niet-toegestane certificaten configureren](https://technet.microsoft.com/library/dn265983.aspx)voor meer informatie.

#### <a name="linux"></a>Linux

Volg de richt lijnen van de Distributor van de versie van uw Linux-besturings systeem om de meest recente vertrouwde basis certificaten en de meest recente lijst met certificaat intrekkingen op de VM op te halen.

Omdat SuSE Linux symbolische koppelingen (of *symlinks*) gebruikt om een certificaat lijst te onderhouden, volgt u deze stappen:

1. Meld u aan als een hoofdgebruiker.

1. Voer deze opdracht uit om de map te wijzigen:

    **# cd-/etc/ssl/certs**

1. Controleer of het certificaat van de Symantec-basis certificerings instantie aanwezig is:

    **# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem**

1. Als het certificaat van de Symantec-basis certificerings instantie niet wordt gevonden, voert u de volgende opdracht uit om het bestand te downloaden. Controleer op eventuele fouten en volg de aanbevolen acties voor netwerk fouten.

    **# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem**

1. Controleer of het Baltimore basis-CA-certificaat aanwezig is:

    **# ls Baltimore_CyberTrust_Root. pem**

1. Als het basis-CA-certificaat van de Baltimore niet wordt gevonden, voert u deze opdracht uit om het certificaat te downloaden:

    **# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root. pem**

1. Controleer of het DigiCert_Global_Root_CA certificaat aanwezig is:

    **# ls DigiCert_Global_Root_CA. pem**

1. Als de DigiCert_Global_Root_CA niet wordt gevonden, voert u de volgende opdrachten uit om het certificaat te downloaden:

    **# wget- http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# openssl x509-in DigiCertGlobalRootCA. CRT: de PEM van der-out-out informeren DigiCert_Global_Root_CA. pem**

1. Voer het opnieuw hash-script uit om de certificaat onderwerp-hashes bij te werken voor de zojuist gedownloade certificaten:

    **# c_rehash**

1. Voer deze opdrachten uit om te controleren of de onderwerp-hashes als symlinks zijn gemaakt voor de certificaten:

    - Opdracht:

        **# ls-l | grep-Baltimore**

    - Uitvoer:

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - Opdracht:

        **# ls-l | grep-VeriSign_Class_3_Public_Primary_Certification_Authority_G5**

    - Uitvoer:

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - Opdracht:

        **# ls-l | grep-DigiCert_Global_Root**

    - Uitvoer:

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. Maak een kopie van het bestand VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem met de naam b204d74a. 0:

    **# CP VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem b204d74a. 0**

1. Maak een kopie van het bestand Baltimore_CyberTrust_Root. pem met de naam 653b494a. 0:

    **# CP Baltimore_CyberTrust_Root. pem 653b494a. 0**

1. Maak een kopie van het bestand DigiCert_Global_Root_CA. pem met de naam 3513523f. 0:

    **# CP DigiCert_Global_Root_CA. pem 3513523f. 0**

1. Controleer of de bestanden aanwezig zijn:

    - Opdracht:

        **# ls-l 653b494a. 0 b204d74a. 0 3513523f. 0**

    - Uitvoer

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Uitgaande connectiviteit voor Site Recovery-URL's of IP-adresbereiken (foutcode 151037 of 151072)

Voor een goede werking van Site Recovery replicatie is uitgaande verbinding van de VM vereist voor specifieke Url's of IP-adresbereiken. Als uw VM zich achter een firewall bevindt of regels voor network security group (NSG) gebruikt voor het beheren van uitgaande connectiviteit, kunt u een van deze problemen kan tegenkomen.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Probleem 1: de virtuele Azure-machine kan niet worden geregistreerd met Site Recovery (fout code 151195)

#### <a name="possible-cause"></a>Mogelijke oorzaak 

De verbinding met Site Recovery-eind punten kan niet tot stand worden gebracht vanwege een DNS-omzettings fout.

Dit probleem treedt het meest op tijdens de herbeveiliging, wanneer u de virtuele machine hebt overgenomen, maar de DNS-server niet bereikbaar is vanuit de regio voor nood herstel (DR).

#### <a name="fix-the-problem"></a>Het probleem wordt opgelost

Als u een aangepaste DNS gebruikt, moet u ervoor zorgen dat de DNS-server toegankelijk is vanuit de regio voor herstel na nood gevallen. Als u wilt weten of u een aangepaste DNS-server hebt, gaat u op de VM naar *nood herstel netwerk* > **DNS-servers**.

![Lijst met aangepaste DNS-servers](./media/azure-to-azure-troubleshoot-errors/custom_dns.PNG)

Probeer vanaf de virtuele machine toegang te krijgen tot de DNS-server. Als de server niet toegankelijk is, kunt u deze toegankelijk maken door een failover van de DNS-server uit te voeren of door de regel van de site te maken tussen het DR-netwerk en de DNS.

### <a name="issue-2-site-recovery-configuration-failed-error-code-151196"></a>Probleem 2: configuratie van Site Recovery is mislukt (fout code 151196)

#### <a name="possible-cause"></a>Mogelijke oorzaak

Er kan geen verbinding tot stand worden gebracht met Office 365-verificatie en identiteits IP4-eind punten.

#### <a name="fix-the-problem"></a>Het probleem wordt opgelost

Voor de verificatie is toegang tot Office 365-IP-adresbereiken vereist. Site Recovery
Als u Azure NSG-regels of firewall proxy gebruikt voor het beheren van uitgaande netwerk connectiviteit op de VM, moet u communicatie met Office 365 IP-bereiken toestaan. Maak een NSG-regel op basis van een servicetag van een [Azure Active Directory (Azure AD)](../virtual-network/security-overview.md#service-tags), zodat u toegang krijgt tot alle IP-adressen die overeenkomen met Azure AD. Als nieuwe adressen in de toekomst worden toegevoegd aan Azure AD, moet u nieuwe NSG-regels maken.

> [!NOTE]
> Als Vm's achter een *standaard* interne Load Balancer, heeft de Load Balancer standaard geen toegang tot Office 365 IP-bereiken (dat wil zeggen, login.microsoftonline.com). Wijzig het interne load balancer type in *Basic* of maak een uitgaande toegang zoals beschreven in het artikel [taak verdeling en regels voor uitgaande verbindingen configureren](https://aka.ms/lboutboundrulescli).

### <a name="issue-3-site-recovery-configuration-failed-error-code-151197"></a>Probleem 3: configuratie van Site Recovery is mislukt (fout code 151197)

#### <a name="possible-cause"></a>Mogelijke oorzaak

Er kan geen verbinding tot stand worden gebracht met Site Recovery service-eind punten.

#### <a name="fix-the-problem"></a>Het probleem wordt opgelost

Voor Site Recovery is toegang tot [site Recovery IP-bereiken](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)vereist, afhankelijk van de regio. Zorg ervoor dat de vereiste IP-bereiken toegankelijk zijn vanaf de virtuele machine.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-an-on-premises-proxy-server-error-code-151072"></a>Probleem 4: Azure-naar-Azure-replicatie is mislukt wanneer het netwerk verkeer via een on-premises proxy server gaat (fout code 151072)

#### <a name="possible-cause"></a>Mogelijke oorzaak

De aangepaste proxy instellingen zijn ongeldig en de proxy-instellingen van Internet Explorer zijn niet automatisch gedetecteerd door de Site Recovery Mobility Service-agent.

#### <a name="fix-the-problem"></a>Het probleem wordt opgelost

De Mobility Service-agent detecteert de proxy-instellingen van Internet Explorer in Windows en van/etc/environment in Linux.

Als u de proxy alleen voor de Mobility-service wilt instellen, kunt u de proxy gegevens opgeven in het bestand ProxyInfo. conf op de volgende locaties:

- **Linux**:/usr/local/InMage/config/
- **Windows**: C:\ProgramData\Microsoft Azure site Recovery\Config

In ProxyInfo. conf geeft u de proxy-instellingen op in de volgende initialisatie-bestands indeling:

> [*proxy*]

> Adres = *http://1.2.3.4*

> Poort =*567*


> [!NOTE]
> De Site Recovery Mobility Service-agent ondersteunt alleen *niet-geverifieerde proxy's*.

### <a name="more-information"></a>Meer informatie

Volg de richt lijnen in [over netwerken in azure naar Azure-replicatie](site-recovery-azure-to-azure-networking-guidance.md)om de [vereiste url's](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) of de [vereiste IP-bereiken](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)op te geven.

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Schijf niet gevonden in de machine (foutcode 150039)

Een nieuwe schijf die is gekoppeld aan de virtuele machine moet worden geïnitialiseerd. Als de schijf niet wordt gevonden, wordt het volgende bericht weer gegeven:

> ' Azure *Data Disk-* *DiskURI* met Logical Unit Number *LUN* *LUNValue* is niet toegewezen aan een overeenkomende schijf die wordt gerapporteerd vanuit de VM met dezelfde LUN-waarde.

### <a name="possible-causes"></a>Mogelijke oorzaken

- Er is een nieuwe gegevens schijf aan de virtuele machine gekoppeld, maar deze is niet geïnitialiseerd.
- De gegevens schijf in de virtuele machine rapporteert niet de Logical Unit Number (LUN)-waarde waarmee de schijf is gekoppeld aan de virtuele machine.

### <a name="fix-the-problem"></a>Het probleem wordt opgelost

Zorg ervoor dat de gegevens schijven zijn geïnitialiseerd en voer de bewerking vervolgens opnieuw uit.

- **Windows**: [een nieuwe schijf koppelen en initialiseren](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).

- **Linux**: [Initialiseer een nieuwe gegevens schijf in Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Als het probleem zich blijft voordoen, neem dan contact op met ondersteuning.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>Er zijn een of meer schijven beschikbaar voor beveiliging (fout code 153039)

### <a name="possible-causes"></a>Mogelijke oorzaken

- Er zijn onlangs een of meer schijven aan de virtuele machine toegevoegd na de beveiliging.
- Een of meer schijven zijn geïnitialiseerd na de beveiliging van de virtuele machine.

### <a name="fix-the-problem"></a>Het probleem wordt opgelost

Als u de replicatie status van de virtuele machine weer in orde wilt maken, kunt u kiezen of u de schijven wilt beveiligen of de waarschuwing wilt negeren.

#### <a name="to-protect-the-disks"></a>De schijven beveiligen

1. Ga naar **gerepliceerde Items** > *VM-naam* > **schijven**.
1. Selecteer de niet-beveiligde schijf en selecteer vervolgens **replicatie inschakelen**:

    ![Replicatie op VM-schijven inschakelen](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>De waarschuwing negeren

1. Ga naar **gerepliceerde items** > *VM-naam*.
1. Selecteer de waarschuwing in het gedeelte **overzicht** en selecteer vervolgens **OK**.

    ![Waarschuwing nieuwe schijf negeren](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>Verwijderen van de virtuele machine uit de kluis is voltooid met informatie (fout code 150225)

Wanneer de virtuele machine wordt beveiligd door Site Recovery, worden er een aantal koppelingen gemaakt op de virtuele bron machine. Wanneer u de beveiliging verwijdert of replicatie uitschakelt, Site Recovery deze koppelingen verwijderen als onderdeel van de opschoon taak. Als de virtuele machine een resource vergrendeling heeft, wordt de opschoon taak voltooid met de informatie. De informatie geeft aan dat de virtuele machine is verwijderd uit de Recovery Services kluis, maar dat een aantal van de verouderde koppelingen niet kan worden opgeruimd op de bron machine.

U kunt deze waarschuwing negeren als u deze virtuele machine nooit opnieuw wilt beveiligen. Als u deze virtuele machine later moet beveiligen, volgt u de stappen onder ' het probleem oplossen ' om de koppelingen op te schonen.

> [!WARNING]
> Als u het opschonen niet doet:
>
> - Wanneer u replicatie inschakelt met behulp van de Recovery Services kluis, wordt de virtuele machine niet weer gegeven.
> - Als u de **virtuele machine** probeert te beveiligen met behulp van de **instellingen** voor de > voor het maken van een > **herstel na nood gevallen**, mislukt de bewerking met het bericht ' de replicatie kan niet worden ingeschakeld vanwege de bestaande verlopen bron koppelingen op de virtuele machine '.

### <a name="fix-the-problem"></a>Het probleem wordt opgelost

> [!NOTE]
> Site Recovery de virtuele bron machine niet verwijdert of op enigerlei wijze beïnvloed tijdens het uitvoeren van deze stappen.

1. Verwijder de vergren deling van de virtuele machine of VM-resource groep. Zo moet de resource vergrendeling op de virtuele machine met de naam ' MoveDemo ' in de volgende afbeelding worden verwijderd:

    ![Vergren deling van virtuele machine verwijderen](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Down load het script om [een verouderde site Recovery configuratie te verwijderen](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Voer het script uit dat Cleanup-stale-asr-config-Azure-VM. ps1 wordt genoemd. Geef de abonnements-ID, VM-resource groep en VM-naam op als para meters.
1. Als u wordt gevraagd om Azure-referenties, geeft u deze op. Controleer vervolgens of het script zonder storingen wordt uitgevoerd.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>Replicatie kan niet worden ingeschakeld vanwege verlopen bron koppelingen op de VM (fout code 150226)

### <a name="possible-cause"></a>Mogelijke oorzaak

De virtuele machine heeft een verouderde configuratie van de vorige Site Recovery beveiliging.

Een verouderde configuratie kan optreden op een Azure-VM als u replicatie voor de Azure-VM hebt ingeschakeld met behulp van Site Recovery en vervolgens:

- U hebt replicatie uitgeschakeld, maar de bron-VM had een resource vergrendeling.
- U hebt de Site Recovery kluis verwijderd zonder dat de replicatie op de virtuele machine expliciet wordt uitgeschakeld.
- U hebt de resource groep met de Site Recovery kluis verwijderd zonder dat de replicatie op de virtuele machine expliciet wordt uitgeschakeld.

### <a name="fix-the-problem"></a>Het probleem wordt opgelost

> [!NOTE]
> Site Recovery de virtuele bron machine niet verwijdert of op enigerlei wijze beïnvloed tijdens het uitvoeren van deze stappen.

1. Verwijder de vergren deling van de virtuele machine of VM-resource groep. Zo moet de resource vergrendeling op de virtuele machine met de naam ' MoveDemo ' in de volgende afbeelding worden verwijderd:

    ![Vergren deling van virtuele machine verwijderen](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Down load het script om [een verouderde site Recovery configuratie te verwijderen](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Voer het script uit dat Cleanup-stale-asr-config-Azure-VM. ps1 wordt genoemd. Geef de abonnements-ID, VM-resource groep en VM-naam op als para meters.
1. Als u wordt gevraagd om Azure-referenties, geeft u deze op. Controleer vervolgens of het script zonder storingen wordt uitgevoerd.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>De Azure-VM of resource groep kan niet worden weer geven voor de selectie in de taak replicatie inschakelen

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>Oorzaak 1: de resource groep en de virtuele bron machine bevinden zich op verschillende locaties

Site Recovery moeten de resource groep en de virtuele machines van de bron regio momenteel op dezelfde locatie zijn. Als dat niet het geval is, kunt u de virtuele machine of resource groep niet vinden wanneer u probeert beveiliging toe te passen.

Als tijdelijke oplossing kunt u de replicatie van de virtuele machine inschakelen in plaats van de Recovery Services kluis. Ga naar de > **Eigenschappen** van de **bron-VM** > **nood herstel te herstellen** en de replicatie in te scha kelen.

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>Oorzaak 2: de resource groep maakt geen deel uit van het geselecteerde abonnement

U kunt de resource groep mogelijk niet vinden op het moment van de beveiliging als de resource groep geen deel uitmaakt van het geselecteerde abonnement. Zorg ervoor dat de resource groep deel uitmaakt van het abonnement dat u gebruikt.

### <a name="cause-3-stale-configuration"></a>Oorzaak 3: verouderde configuratie

Mogelijk ziet u de virtuele machine die u wilt inschakelen voor replicatie als er een verouderde Site Recovery configuratie op de virtuele machine van Azure is gebleven. Dit probleem kan optreden als u replicatie voor de Azure-VM hebt ingeschakeld met behulp van Site Recovery en vervolgens:

- U hebt de Site Recovery kluis verwijderd zonder dat de replicatie op de virtuele machine expliciet wordt uitgeschakeld.
- U hebt de resource groep met de Site Recovery kluis verwijderd zonder dat de replicatie op de virtuele machine expliciet wordt uitgeschakeld.
- U hebt replicatie uitgeschakeld, maar de bron-VM had een resource vergrendeling.

### <a name="fix-the-problem"></a>Het probleem wordt opgelost

> [!NOTE]
> Zorg ervoor dat u de module ' AzureRM. resources ' bijwerkt voordat u het script gebruikt dat in deze sectie wordt genoemd.  Site Recovery de virtuele bron machine niet verwijdert of op enigerlei wijze beïnvloed tijdens het uitvoeren van deze stappen.

1. Verwijder de vergren deling, indien van toepassing, van de virtuele machine of VM-resource groep. Zo moet de resource vergrendeling op de virtuele machine met de naam ' MoveDemo ' in de volgende afbeelding worden verwijderd:

    ![Vergren deling van virtuele machine verwijderen](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Down load het script om [een verouderde site Recovery configuratie te verwijderen](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Voer het script uit dat Cleanup-stale-asr-config-Azure-VM. ps1 wordt genoemd. Geef de abonnements-ID, VM-resource groep en VM-naam op als para meters.
1. Als u wordt gevraagd om Azure-referenties, geeft u deze op. Controleer vervolgens of het script zonder storingen wordt uitgevoerd.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>Kan geen virtuele machine voor beveiliging selecteren

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>Oorzaak 1: de virtuele machine heeft een uitbrei ding die is geïnstalleerd met een mislukte of niet-reagerende status

Ga naar **virtuele machines** > **instellingen** > **extensies** en controleer op uitbrei dingen met de status mislukt. Verwijder een mislukte uitbrei ding en probeer het opnieuw om de virtuele machine te beveiligen.

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>Oorzaak 2: de inrichtings status van de virtuele machine is niet geldig

Zie de stappen voor probleem oplossing in [de inrichtings status van de virtuele machine is niet geldig](#the-vms-provisioning-state-is-not-valid-error-code-150019), verderop in dit artikel.

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>De inrichtings status van de virtuele machine is niet geldig (fout code 150019)

Als u de replicatie op de virtuele machine wilt inschakelen, moet de inrichtings status zijn **geslaagd**. Volg deze stappen om de inrichtings status te controleren:

1. Selecteer in de Azure Portal de **resource Verkenner** van **alle services**.
1. Vouw de **abonnementen** lijst en selecteer uw abonnement.
1. Vouw de **ResourceGroups** lijst en selecteer de resourcegroep van de virtuele machine.
1. Vouw de lijst met **resources** uit en selecteer de virtuele machine.
1. Controleer het veld **provisioningState** in de weer gave instantie aan de rechter kant.

### <a name="fix-the-problem"></a>Het probleem wordt opgelost

- Als **provisioningState** is **mislukt**, neem contact op met ondersteuning voor meer informatie om op te lossen.
- Als **provisioningState** wordt **bijgewerkt**, wordt mogelijk een andere uitbrei ding geïmplementeerd. Controleer of er actieve bewerkingen zijn op de VM, wacht totdat ze zijn voltooid en voer de mislukte Site Recovery taak replicatie inschakelen opnieuw uit.

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>Kan geen doel-VM selecteren (het tabblad netwerk selectie is niet beschikbaar)

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Oorzaak 1: uw VM is gekoppeld aan een netwerk dat al is toegewezen aan een doelnet netwerk

Als de bron-VM deel uitmaakt van een virtueel netwerk en een andere VM van hetzelfde virtuele netwerk al is toegewezen aan een netwerk in de doel resource groep, is de vervolg keuzelijst voor het selecteren van het netwerk niet beschikbaar (grijs weer gegeven) standaard.

![De lijst met netwerk selectie is niet beschikbaar](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>Oorzaak 2: u hebt de virtuele machine eerder beveiligd met Site Recovery en vervolgens de replicatie uitgeschakeld

Als u de replicatie van een virtuele machine uitschakelt, wordt de netwerk toewijzing niet verwijderd. De toewijzing moet worden verwijderd uit de Recovery Services kluis waar de virtuele machine is beveiligd. Ga naar *Recovery Services kluis* > **Site Recovery infra structuur** > **netwerk toewijzing**.

![Netwerk toewijzing verwijderen](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

Het doelnet-netwerk dat tijdens de installatie van nood herstel is geconfigureerd, kan na de eerste installatie worden gewijzigd nadat de virtuele machine is beveiligd:

![Netwerk toewijzing wijzigen](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

Houd er rekening mee dat het wijzigen van de netwerk toewijzing van invloed is op alle beveiligde Vm's die dezelfde netwerk toewijzing gebruiken.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>Fout in COM+ of Volume Shadow Copy-service (fout code 151025)

Wanneer deze fout optreedt, wordt het volgende bericht weer gegeven:

> ' Site Recovery-extensie kan niet worden geïnstalleerd '

### <a name="possible-causes"></a>Mogelijke oorzaken

- De COM+-systeem toepassings service is uitgeschakeld.
- De Volume Shadow Copy-service is uitgeschakeld.

### <a name="fix-the-problem"></a>Het probleem wordt opgelost

Stel de COM+-systeem toepassing en Volume Shadow Copy-Services in op automatische of hand matige opstart modus.

1. Open de console Services in Windows.
1. Zorg ervoor dat de COM+-systeem toepassing en de Volume Shadow Copy-Services niet zijn ingesteld op **uitgeschakeld** als **opstart type**.

    ![Het opstart type van de COM+-systeem toepassing en Volume Shadow Copy-Services controleren](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Niet-ondersteunde beheerde schijf grootte (fout code 150172)

Wanneer deze fout optreedt, wordt het volgende bericht weer gegeven:

> "De beveiliging kan niet worden ingeschakeld voor de virtuele machine omdat deze een *schijf* naam heeft met een grootte van *DiskSize*) * die kleiner is dan de mini maal ondersteunde grootte van 1024 MB."

### <a name="possible-cause"></a>Mogelijke oorzaak

De schijf is kleiner dan de ondersteunde grootte van 1024 MB.

### <a name="fix-the-problem"></a>Het probleem wordt opgelost

Zorg ervoor dat de schijf grootte binnen het ondersteunde bereik ligt en voer de bewerking opnieuw uit.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>De beveiliging is niet ingeschakeld omdat de GRUB-configuratie de apparaatnaam in plaats van de UUID bevat (fout code 151126)

### <a name="possible-cause"></a>Mogelijke oorzaak

De Linux GRUB-configuratie bestanden (/boot/grub/menu.lst,/boot/grub/grub.cfg,/boot/grub2/grub.cfg of/etc/default/grub) geven mogelijk de werkelijke apparaatnamen in plaats van de UUID waarden voor de para meters *root* en *Resume* . Site Recovery vereist UUID, omdat de apparaatnamen kunnen veranderen. Bij het opnieuw opstarten is het mogelijk dat een virtuele machine niet dezelfde naam heeft als de failover, waardoor er problemen ontstaan.

De volgende voor beelden zijn regels uit GRUB-bestanden waarbij apparaatnamen (vet weer gegeven) worden weer gegeven in plaats van de vereiste UUID:

- Bestands/boot/grub2/grub.cfg

  > linux/boot/vmlinuz-3.12.49-11-default **root =/dev/sda2** $ {extra_cmdline} **Resume =/dev/sda1is** = Silent quiet showopts

- Bestand:/boot/grub/menu.lst

  > kernel/boot/vmlinuz-3.0.101-63-default **root =/dev/sda2** **Resume =/dev/sda1** begin = Silent crashkernel = 256M-: 128M showopts VGA = 0x314


### <a name="fix-the-problem"></a>Het probleem wordt opgelost

Vervang elke naam van het apparaat door de bijbehorende UUID:

1. Zoek de UUID van het apparaat door de naam van de opdracht **blkid** ***apparaat***uit te voeren. Bijvoorbeeld:

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Vervang de naam van het apparaat door de bijbehorende UUID in de indelingen **root = uuid**=*uuid* en **Resume = uuid**=*uuid*. De regel van/boot/grub/menu.lst (eerder besproken) ziet er bijvoorbeeld als volgt uit:

    > kernel/boot/vmlinuz-3.0.101-63-default **root = uuid = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **Resume = uuid = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b** begin = Silent crashkernel = 256M-: 128M showopts VGA = 0x314

1. Voer de beveiliging opnieuw uit.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>Het inschakelen van de beveiliging is mislukt, omdat het apparaat dat wordt vermeld in de GRUB-configuratie niet bestaat (fout code 151124).

### <a name="possible-cause"></a>Mogelijke oorzaak

De GRUB-configuratie bestanden (/boot/grub/menu.lst,/boot/grub/grub.cfg,/boot/grub2/grub.cfg of/etc/default/grub) kunnen de para meters *rd.lvm.lv* of *rd_LVM_LV*bevatten. Met deze para meters worden de LVM-apparaten (Logical Volume Manager) geïdentificeerd die tijdens de opstart tijd moeten worden gedetecteerd. Als deze LVM-apparaten niet bestaan, wordt het beveiligde systeem zelf niet gestart en blijft het in het opstart proces zitten. Hetzelfde probleem wordt ook weer gegeven met de failover-VM. Hier volgen enkele voor beelden:

- Bestand:/boot/grub2/grub.cfg op RHEL7:

    > linux16/vmlinuz-3.10.0-957.EL7. x86_64 root =/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel = 128M\@64M **rd. lvm. lv = rootvg/root RD. lvm. lv = rootvg/swap** RHGB quiet LANG = en_US. UTF-8

- Bestand:/etc/default/grub op RHEL7:

    > GRUB_CMDLINE_LINUX = "crashkernel = auto **rd. lvm. lv = rootvg/root RD. lvm. lv = rootvg/swap** rhgb quiet"

- Bestand:/boot/grub/menu.lst op RHEL6:

    > kernel/vmlinuz-2.6.32-754.el6. x86_64 ro root = UUID = 36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG = en_US. UTF-8 rd_NO_MD SYSFONT = latarcyrheb-sun16 crashkernel = auto **rd_LVM_LV = rootvg/lv_root** KEYBOARDTYPE = PC-tabel = US **rd_LVM_LV = rootvg/lv_swap** rd_NO_DM rhgb quiet

In elk voor beeld ziet u het deel in vet dat de GRUB twee LVM-apparaten moet detecteren met de namen ' root ' en ' swap ' van de volume groep ' rootvg '.

### <a name="fix-the-problem"></a>Het probleem wordt opgelost

Als het LVM-apparaat niet bestaat, maakt u het of verwijdert u de overeenkomstige para meters uit de GRUB-configuratie bestanden. Probeer het vervolgens opnieuw om de beveiliging in te scha kelen.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>De update van een Site Recovery Mobility-service is voltooid met waarschuwingen (fout code 151083)

De Site Recovery Mobility-service heeft veel onderdelen, een van de het filter stuur programma genoemd. Het filter stuur programma wordt alleen in het systeem geheugen geladen tijdens het opnieuw opstarten van het systeem. Telkens wanneer een update van de Mobility-service wijzigingen in het filter stuur programma bevat, wordt de computer bijgewerkt, maar wordt er nog steeds een waarschuwing weer gegeven dat het opnieuw opstarten van sommige oplossingen is vereist. De waarschuwing wordt weer gegeven omdat de correcties van het filter stuur programma alleen van kracht worden wanneer het nieuwe filter stuur programma wordt geladen. dit gebeurt alleen tijdens het opnieuw opstarten.

> [!NOTE]
> Dit is slechts een waarschuwing. Bestaande replicatie blijft werken, zelfs na het bijwerken van de nieuwe agent. U kunt ervoor kiezen om opnieuw op te starten wanneer u de voor delen van het nieuwe filter stuur programma wilt, maar het oude filter stuur programma blijft actief als u de computer niet opnieuw opstart.
>
> Naast het filter stuur programma zijn de voor delen van andere verbeteringen en oplossingen in de update van de Mobility-service van kracht zonder dat de computer opnieuw hoeft te worden opgestart.  

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>De beveiliging kan niet worden ingeschakeld omdat de door de replica beheerde schijf al bestaat, zonder de verwachte Tags in de doel resource groep (fout code 150161)

### <a name="possible-cause"></a>Mogelijke oorzaak

Dit probleem kan optreden als de virtuele machine eerder is beveiligd en wanneer replicatie is uitgeschakeld, de replica schijf niet is opgeruimd.

### <a name="fix-the-problem"></a>Het probleem wordt opgelost

Verwijder de replica schijf die in het fout bericht wordt vermeld en voer de mislukte beveiligings taak opnieuw uit.

## <a name="next-steps"></a>Volgende stappen

[Virtuele Azure-machines repliceren](site-recovery-replicate-azure-to-azure.md)
