---
title: Azure VM-replicatie oplossen in Azure-siteherstel
description: Problemen oplossen bij het repliceren van virtuele Azure-machines voor herstel na noodgevallen.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: rochakm
ms.openlocfilehash: c131609a5622061e2ea49db422bc4e9da96666d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276679"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Problemen met Azure-to-Azure VM-replicatiefouten oplossen

In dit artikel wordt beschreven hoe u veelvoorkomende fouten in Azure Site Recovery oplossen tijdens replicatie en herstel van Virtuele Azure-machines (VM's) van het ene gebied naar het andere. Zie de [ondersteuningsmatrix voor het repliceren van Azure VM's voor](site-recovery-support-matrix-azure-to-azure.md)meer informatie over ondersteunde configuraties.

## <a name="azure-resource-quota-issues-error-code-150097"></a><a name="azure-resource-quota-issues-error-code-150097"></a>Problemen met Azure-resourcesquota (foutcode 150097)

Zorg ervoor dat uw abonnement is ingeschakeld om Azure VM's te maken in het doelgebied dat u wilt gebruiken als uw regio voor noodherstel. Zorg er ook voor dat uw abonnement voldoende quotum heeft om VM's van de benodigde formaten te maken. Siteherstel kiest standaard een doel-VM-grootte die gelijk is aan de grootte van de bron-VM. Als de overeenkomende grootte niet beschikbaar is, kiest Siteherstel automatisch de dichtstbijzijnde beschikbare grootte.

Als er geen grootte is die de bron-VM-configuratie ondersteunt, wordt het volgende bericht weergegeven:

> "Replicatie kan niet worden ingeschakeld voor de virtuele machine *VmName."*

### <a name="possible-causes"></a>Mogelijke oorzaken

- Uw abonnements-ID is niet ingeschakeld om VM's te maken op de locatie van de doelregio.
- Uw abonnements-ID is niet ingeschakeld of heeft onvoldoende quota om specifieke VM-formaten te maken op de locatie van het doelgebied.
- Er wordt geen geschikte doel-VM-grootte gevonden die overeenkomt met het aantal NIC-gegevens (Network Interface)-telling (2) van de bron-VM voor de abonnements-id op de locatie van de doelregio.

### <a name="fix-the-problem"></a>Het probleem oplossen

Neem contact op met [Azure-factureringsondersteuning](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) om uw abonnement in staat te stellen VM's te maken met de vereiste grootte op de doellocatie. Probeer vervolgens de mislukte bewerking opnieuw.

Als de doellocatie een capaciteitsbeperking heeft, schakelt u replicatie uit. Schakel vervolgens replicatie in naar een andere locatie waar uw abonnement voldoende quotum heeft om VM's van de vereiste formaten te maken.

## <a name="trusted-root-certificates-error-code-151066"></a><a name="trusted-root-certificates-error-code-151066"></a>Vertrouwde rootcertificaten (foutcode 151066)

Als niet alle meest recente vertrouwde basiscertificaten op de VM aanwezig zijn, kan de taak 'Replicatie inschakelen' mislukken. Verificatie en autorisatie van siteherstelserviceoproepen van de VM mislukken zonder deze certificaten.

Als de taak Replicatie inschakelen mislukt, wordt het volgende bericht weergegeven:

> 'Siteherstelconfiguratie is mislukt'.

### <a name="possible-cause"></a>Mogelijke oorzaak

De vertrouwde basiscertificaten die nodig zijn voor autorisatie en verificatie zijn niet aanwezig op de virtuele machine.

### <a name="fix-the-problem"></a>Het probleem oplossen

#### <a name="windows"></a>Windows

Installeer voor een VM die het Windows-besturingssysteem uitvoert de nieuwste Windows-updates op de VM, zodat alle vertrouwde rootcertificaten op de machine aanwezig zijn. Volg het typische Windows-updatebeheer- of certificaatupdatebeheerproces in uw organisatie om de nieuwste basiscertificaten en de bijgewerkte lijst met certificaatintrekkingen op de VM's te krijgen.

Als u zich in een verbroken omgeving bevindt, volgt u het standaard Windows-updateproces in uw organisatie om de certificaten te ontvangen. Als de vereiste certificaten niet aanwezig zijn op de VM, mislukken de oproepen naar de siteherstelservice om veiligheidsredenen.

Als u wilt controleren of het probleem is opgelost, gaat u naar login.microsoftonline.com vanuit een browser in uw virtuele machine.

Zie [Vertrouwde wortels en niet-toegestane certificaten configureren](https://technet.microsoft.com/library/dn265983.aspx)voor meer informatie.

#### <a name="linux"></a>Linux

Volg de richtlijnen van de distributeur van uw Linux-besturingssysteemversie om de nieuwste vertrouwde rootcertificaten en de nieuwste lijst met certificaatintrekkingen op de VM te krijgen.

Omdat SUSE Linux symbolische koppelingen (of *symlinks)* gebruikt om een certificaatlijst bij te houden, voert u de volgende stappen uit:

1. Meld u aan als rootgebruiker.

1. Voer deze opdracht uit om de map te wijzigen:

    **# cd /etc/ssl/certs**

1. Controleer of het Symantec root CA-certificaat aanwezig is:

    **Ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. Als het hoofd-CA-certificaat van Symantec niet wordt gevonden, voert u de volgende opdracht uit om het bestand te downloaden. Controleer op eventuele fouten en volg aanbevolen acties op netwerkfouten.

    **# wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. Controleer of het Baltimore root CA-certificaat aanwezig is:

    **# ls Baltimore_CyberTrust_Root.pem**

1. Als het kwortel-CA-certificaat van Baltimore niet wordt gevonden, voert u deze opdracht uit om het certificaat te downloaden:

    **# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem**

1. Controleer of het DigiCert_Global_Root_CA certificaat aanwezig is:

    **# ls DigiCert_Global_Root_CA.pem**

1. Als de DigiCert_Global_Root_CA niet wordt gevonden, voert u de volgende opdrachten uit om het certificaat te downloaden:

    **# wgethttp://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem**

1. Voer het rehashscript uit om de hashes van het certificaatonderwerp voor de nieuw gedownloade certificaten bij te werken:

    **# c_rehash**

1. Voer deze opdrachten uit om te controleren of het onderwerp hashes als symlinks is gemaakt voor de certificaten:

    - Opdracht:

        **ls -l | grep Baltimore**

    - Uitvoer:

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - Opdracht:

        **ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5**

    - Uitvoer:

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - Opdracht:

        **ls -l | grep DigiCert_Global_Root**

    - Uitvoer:

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. Maak een kopie van het bestand VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem met bestandsnaam b204d74a.0:

    **# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0**

1. Maak een kopie van het bestand Baltimore_CyberTrust_Root.pem met bestandsnaam 653b494a.0:

    **# cp Baltimore_CyberTrust_Root.pem 653b494a.0**

1. Maak een kopie van het bestand DigiCert_Global_Root_CA.pem met bestandsnaam 3513523f.0:

    **# cp DigiCert_Global_Root_CA.pem 3513523f.0**

1. Controleer of de bestanden aanwezig zijn:

    - Opdracht:

        **# ls -l 653b494a.0 b204d74a.0 3513523f.0**

    - Uitvoer

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Uitgaande connectiviteit voor URL's of IP-bereiken voor siteherstel (foutcode 151037 of 151072)

Als replicatie van siteherstel werkt, is uitgaande connectiviteit met specifieke URL's vereist van de VM. Als uw vm zich achter een firewall bevindt of NSG-regels (Network Security Group) gebruikt om uitgaande connectiviteit te beheren, u met een van deze problemen worden geconfronteerd. Houd er rekening mee dat, hoewel we uitgaande toegang via URL's blijven ondersteunen, het gebruik van een lijst met IP-bereiken niet langer wordt ondersteund.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a><a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Probleem 1: Kan geen virtuele Azure-machine registreren met siteherstel (151195) </br>
- **Mogelijke oorzaak** </br>
  - Verbinding kan niet worden gemaakt met sitehersteleindpunten als gevolg van een STORING in de DNS-resolutie.
  - Dit komt vaker voor tijdens het opnieuw beveiligen, wanneer u een failover hebt uitgevoerd voor de virtuele machine maar de DNS-server niet bereikbaar is vanuit de DR-regio.

- **Afsluiting**
   - Als u aangepaste DNS gebruikt, controleert u of de DNS-server toegankelijk is vanuit het gebied Rampherstel. Ga naar het VM-> Disaster Recovery-netwerk> DNS-servers om te controleren of u een aangepaste DNS hebt. Probeer vanaf de virtuele machine toegang te krijgen tot de DNS-server. Als het niet toegankelijk is dan maken het toegankelijk door ofwel niet over de DNS-server of het creëren van de lijn van de site tussen DR-netwerk en DNS.

    ![com-fout](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Probleem 2: Configuratie van siteherstel is mislukt (151196)
- **Mogelijke oorzaak** </br>
  - Er kan geen verbinding worden gemaakt met IP4-eindpunten voor verificatie en identiteit van Office 365.

- **Afsluiting**
  - Azure Site Recovery vereiste toegang tot Office 365 IP-bereiken voor verificatie.
    Als u NSG-regels/firewallproxy (Azure Network Security Group) gebruikt om uitgaande netwerkconnectiviteit op de VM te beheren, moet u ervoor zorgen dat u de NSG-regel [(Azure Active Directory) gebruikt](../virtual-network/security-overview.md#service-tags) om toegang te verlenen tot AAD. We ondersteunen geen OP IP-adres gebaseerde NSG-regels meer.


### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Probleem 3: Configuratie van siteherstel is mislukt (151197)
- **Mogelijke oorzaak** </br>
  - Er kan geen verbinding worden gemaakt met eindpunten van de Azure Site Recovery-service.

- **Afsluiting**
  - Als u NSG-regels/firewallproxy (Azure Network Security Group) gebruikt om uitgaande netwerkconnectiviteit op de VM te beheren, moet u ervoor zorgen dat u servicetags gebruikt. We ondersteunen niet langer het gebruik van een lijst met IP-adressen via NSGs voor Azure Site Recovery (ASR).


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premise-proxy-server-151072"></a>Probleem 4: A2A-replicatie is mislukt wanneer het netwerkverkeer door on-premise proxyserver gaat (151072)
 - **Mogelijke oorzaak** </br>
   - De aangepaste proxy-instellingen zijn ongeldig en ASR Mobility Service-agent heeft de proxy-instellingen van IE niet automatisch gedetecteerd


 - **Afsluiting**
   1.    Mobility Service agent detecteert de proxy-instellingen van IE op Windows en / etc / omgeving op Linux.
   2.  Als u liever alleen proxy instelt voor ASR Mobility Service, dan u de proxy-gegevens opgeven in ProxyInfo.conf op:</br>
       - ``/usr/local/InMage/config/``op ***Linux***
       - ``C:\ProgramData\Microsoft Azure Site Recovery\Config``op ***Windows***
   3.    ProxyInfo.conf moet de proxy-instellingen in de volgende INI-indeling hebben.</br>
                   *[proxy]*</br>
                   *Adres=http://1.2.3.4*</br>
                   *Port=567*</br>
   4. ASR Mobility Service-agent ondersteunt alleen ***niet-geverifieerde proxy's.***


### <a name="fix-the-problem"></a>Het probleem oplossen

Als u [de vereiste URL's](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) wilt toevoegen aan een lijst met toegestane gegevens, voert u de stappen in het [netwerkrichtlijnendocument uit.](site-recovery-azure-to-azure-networking-guidance.md)


### <a name="more-information"></a>Meer informatie

Als u [vereiste URL's](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) of [vereiste IP-bereiken wilt opgeven,](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)volgt u de richtlijnen in [Over netwerken in Azure naar Azure-replicatie](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Schijf niet gevonden in de machine (foutcode 150039)

Een nieuwe schijf die aan de VM is gekoppeld, moet worden geïnitialiseerd. Als de schijf niet wordt gevonden, wordt het volgende bericht weergegeven:

> "Azure data *diskname* *DiskURI* met logisch eenheidsnummer *LUN* *LUNValue* is niet toegewezen aan een overeenkomstige schijf die wordt gerapporteerd vanuit de VM met dezelfde LUN-waarde.

### <a name="possible-causes"></a>Mogelijke oorzaken

- Er is een nieuwe gegevensschijf aan de VM gekoppeld, maar deze is niet geïnitialiseerd.
- De gegevensschijf in de VM rapporteert niet correct de logische eenheidsnummerwaarde (LUN) waartegen de schijf aan de VM is gekoppeld.

### <a name="fix-the-problem"></a>Het probleem oplossen

Controleer of de gegevensschijven zijn geïnitialiseerd en probeer de bewerking opnieuw.

- **Windows**: [Een nieuwe schijf koppelen en initialiseren](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).

- **Linux**: [Initialiseren van een nieuwe data schijf in Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Als het probleem blijft bestaan, neemt u contact op met ondersteuning.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>Een of meer schijven zijn beschikbaar voor bescherming (foutcode 153039)

### <a name="possible-causes"></a>Mogelijke oorzaken

- Een of meer schijven zijn onlangs toegevoegd aan de virtuele machine na bescherming.
- Een of meer schijven werden geïnitialiseerd na bescherming van de virtuele machine.

### <a name="fix-the-problem"></a>Het probleem oplossen

Als u de replicatiestatus van de virtuele machine weer in orde wilt maken, u ervoor kiezen de schijven te beschermen of de waarschuwing te verwijderen.

#### <a name="to-protect-the-disks"></a>Om de schijven te beschermen

1. Ga naar **VM-schijven met de** > *vm-naam* > **voor**gerepliceerde items .
1. Selecteer de onbeveiligde schijf en selecteer **Replicatie inschakelen:**

    ![Replicatie inschakelen op VM-schijven](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>De waarschuwing afwijzen

1. Ga naar de*vm-naam* **van gerepliceerde items** > .
1. Selecteer de waarschuwing in de sectie **Overzicht** en selecteer **OK**.

    ![Waarschuwing voor nieuwe schijven afwijzen](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>Verwijder de virtuele machine uit de kluis aangevuld met informatie (foutcode 150225)

Wanneer het de virtuele machine beschermt, maakt Site Recovery een aantal koppelingen op de virtuele bronmachine. Wanneer u de beveiliging verwijdert of replicatie uitschakelt, verwijdert Site recovery deze koppelingen als onderdeel van de opruimtaak. Als de virtuele machine een resourcevergrendeling heeft, wordt de opruimtaak voltooid met de informatie. De informatie zegt dat de virtuele machine is verwijderd uit de Recovery Services kluis, maar dat sommige van de verouderde links niet kon worden opgeruimd op de bronmachine.

U deze waarschuwing negeren als u nooit van plan bent deze virtuele machine opnieuw te beschermen. Maar als u deze virtuele machine later moet beschermen, volgt u de stappen onder "Fix the problem" om de koppelingen op te ruimen.

> [!WARNING]
> Als u de opruimactie niet doet:
>
> - Wanneer u replicatie inschakelt met de kluis Recovery Services, wordt de virtuele machine niet weergegeven.
> - Als u de VM probeert te beschermen met behulp van Herstel**na de ramp**met de virtuele**machine-instellingen,** >  **Virtual machine** > mislukt de bewerking met het bericht 'Replicatie kan niet worden ingeschakeld vanwege de bestaande verouderde resourcekoppelingen op de vm'.

### <a name="fix-the-problem"></a>Het probleem oplossen

> [!NOTE]
> Site herstel verwijdert de bron virtuele machine niet of beïnvloedt deze op geen enkele manier terwijl u deze stappen uitvoert.

1. Verwijder het slot uit de vm- of VM-brongroep. In de volgende afbeelding moet bijvoorbeeld het bronslot op de VM met de naam 'MoveDemo' worden verwijderd:

    ![Vergrendeling verwijderen uit VM](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Download het script om [een verouderde siteherstelconfiguratie](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)te verwijderen.
1. Voer het script uit, dat Cleanup-stale-asr-config-Azure-VM.ps1 wordt genoemd. Geef de abonnements-ID, VM Resource Group en VM-naam als parameters.
1. Als u om Azure-referenties wordt gevraagd, geeft u deze op. Controleer vervolgens of het script zonder fouten wordt uitgevoerd.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>Replicatie kan niet worden ingeschakeld vanwege verouderde resourcekoppelingen op de VM (foutcode 150226)

### <a name="possible-cause"></a>Mogelijke oorzaak

De virtuele machine heeft een verouderde configuratie van eerdere siteherstelbeveiliging.

Er kan een verouderde configuratie plaatsvinden op een Azure VM als u replicatie voor de Azure VM hebt ingeschakeld met behulp van Siteherstel en vervolgens:

- U hebt replicatie uitgeschakeld, maar de bron-VM had een bronvergrendeling.
- U hebt de kluis Siteherstel verwijderd zonder de replicatie op de vm expliciet uit te schakelen.
- U hebt de brongroep met de kluis Siteherstel verwijderd zonder de replicatie op de VM expliciet uit te schakelen.

### <a name="fix-the-problem"></a>Het probleem oplossen

> [!NOTE]
> Site herstel verwijdert de bron virtuele machine niet of beïnvloedt deze op geen enkele manier terwijl u deze stappen uitvoert.

1. Verwijder het slot uit de vm- of VM-brongroep. In de volgende afbeelding moet bijvoorbeeld het bronslot op de VM met de naam 'MoveDemo' worden verwijderd:

    ![Vergrendeling verwijderen uit VM](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Download het script om [een verouderde siteherstelconfiguratie](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)te verwijderen.
1. Voer het script uit, dat Cleanup-stale-asr-config-Azure-VM.ps1 wordt genoemd. Geef de abonnements-ID, VM Resource Group en VM-naam als parameters.
1. Als u om Azure-referenties wordt gevraagd, geeft u deze op. Controleer vervolgens of het script zonder fouten wordt uitgevoerd.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>Kan de Azure VM- of resourcegroep voor de selectie niet zien in de taak Replicatie inschakelen

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>Oorzaak 1: De brongroep en de virtuele bronmachine bevinden zich op verschillende locaties

Voor siteherstel moeten de bronregiobrongroep en virtuele machines zich op dezelfde locatie bevinden. Als dit niet het zo is, u de virtuele machine of resourcegroep niet vinden wanneer u bescherming probeert toe te passen.

Als tijdelijke oplossing u replicatie inschakelen vanuit de VM in plaats van de kluis Recovery Services. Ga naar **Bron VM** > **Properties** > **Disaster Recovery** en schakel de replicatie in.

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>Oorzaak 2: De resourcegroep maakt geen deel uit van het geselecteerde abonnement

Mogelijk u de brongroep op het moment van beveiliging niet vinden als de resourcegroep geen deel uitmaakt van het geselecteerde abonnement. Zorg ervoor dat de resourcegroep deel uitmaakt van het abonnement dat u gebruikt.

### <a name="cause-3-stale-configuration"></a>Oorzaak 3: Verouderde configuratie

Het is mogelijk dat u de VM die u wilt inschakelen voor replicatie niet ziet als er een verouderde configuratie van siteherstel is achtergelaten op de Azure VM. Deze voorwaarde kan optreden als u replicatie voor de Azure VM hebt ingeschakeld met siteherstel en vervolgens:

- U hebt de kluis Siteherstel verwijderd zonder de replicatie op de vm expliciet uit te schakelen.
- U hebt de brongroep met de kluis Siteherstel verwijderd zonder de replicatie op de VM expliciet uit te schakelen.
- U hebt replicatie uitgeschakeld, maar de bron-VM had een bronvergrendeling.

### <a name="fix-the-problem"></a>Het probleem oplossen

> [!NOTE]
> Zorg ervoor dat u de module 'AzureRM.Resources' bijwerkt voordat u het script gebruikt dat in deze sectie wordt genoemd.  Site herstel verwijdert de bron virtuele machine niet of beïnvloedt deze op geen enkele manier terwijl u deze stappen uitvoert.

1. Verwijder het slot, indien aanwezig, uit de VM- of VM-brongroep. In de volgende afbeelding moet bijvoorbeeld het bronslot op de VM met de naam 'MoveDemo' worden verwijderd:

    ![Vergrendeling verwijderen uit VM](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Download het script om [een verouderde siteherstelconfiguratie](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)te verwijderen.
1. Voer het script uit, dat Cleanup-stale-asr-config-Azure-VM.ps1 wordt genoemd. Geef de abonnements-ID, VM Resource Group en VM-naam als parameters.
1. Als u om Azure-referenties wordt gevraagd, geeft u deze op. Controleer vervolgens of het script zonder fouten wordt uitgevoerd.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>Kan geen virtuele machine selecteren voor bescherming

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>Oorzaak 1: De virtuele machine heeft een extensie geïnstalleerd in een defecte of niet-reagerende status

Ga naar**Settings** > **Instellingen instellingen** **voor virtuele machines** > en controleer of extensies in een mislukte status zijn. Verwijder elke mislukte extensie en probeer vervolgens opnieuw de virtuele machine te beschermen.

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>Oorzaak 2: De inrichtingsstatus van de VM is niet geldig

Zie de stappen voor het oplossen van problemen in [de inrichtingsstatus van De VM,](#the-vms-provisioning-state-is-not-valid-error-code-150019)later in dit artikel.

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>De inrichtingsstatus van de VM is niet geldig (foutcode 150019)

Als u replicatie op de vm wilt inschakelen, moet de instelstatus worden **uitgevoerd**. Volg de volgende stappen om de inrichtingsstatus te controleren:

1. Selecteer in de Azure-portal de **Resource Explorer** van **Alle services**.
1. Vouw de lijst **Abonnementen** uit en selecteer uw abonnement.
1. Vouw de lijst **Resourcegroepen** uit en selecteer de resourcegroep van de VM.
1. Vouw de lijst **Resources** uit en selecteer uw VM.
1. Controleer **het veld ProvisioningState** in de weergave Instantie aan de rechterkant.

### <a name="fix-the-problem"></a>Het probleem oplossen

- Als **provisioningState** **is mislukt,** neemt u contact op met ondersteuning met details om problemen op te lossen.
- Als **provisioningState** **wordt bijgewerkt,** kan een andere extensie worden geïmplementeerd. Controleer of er lopende bewerkingen op de VM zijn, wacht tot deze zijn voltooid en probeer vervolgens de mislukte taak 'Replicatie inschakelen inschakelen' opnieuw.

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>Kan doel-VM niet selecteren (tabblad netwerkselectie is niet beschikbaar)

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Oorzaak 1: Uw VM is gekoppeld aan een netwerk dat al is toegewezen aan een doelnetwerk

Als de bron-VM deel uitmaakt van een virtueel netwerk en een andere VM van hetzelfde virtuele netwerk al is toegewezen met een netwerk in de doelgroep, is het vervolgkeuzekader voor netwerkselectie standaard niet beschikbaar (wordt gedimd weergegeven).

![Lijst met netwerkselectie niet beschikbaar](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>Oorzaak 2: U hebt de vm eerder beveiligd met Siteherstel en vervolgens de replicatie uitgeschakeld

Als u de replicatie van een virtuele machine uitschakelt, wordt de netwerktoewijzing niet verwijderd. De toewijzing moet worden verwijderd uit de kluis Van Herstelservices waar de VM is beveiligd. Ga naar *Recovery Services vault* > **Site Recovery Infrastructure** > **Network Mapping**.

![Netwerktoewijzing verwijderen](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

Het doelnetwerk dat is geconfigureerd tijdens de noodherstel-instelling, kan worden gewijzigd nadat de vm is beveiligd:

![Netwerktoewijzing wijzigen](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

Houd er rekening mee dat het wijzigen van netwerktoewijzing van invloed is op alle beveiligde VM's die dezelfde netwerktoewijzing gebruiken.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>COM+ of Volume Shadow Copy-servicefout (foutcode 151025)

Wanneer deze fout optreedt, wordt het volgende bericht weergegeven:

> "Site herstel extensie kan niet worden geïnstalleerd"

### <a name="possible-causes"></a>Mogelijke oorzaken

- De COM+ System Application-service is uitgeschakeld.
- De volumeschaduwkopieerservice is uitgeschakeld.

### <a name="fix-the-problem"></a>Het probleem oplossen

Stel de COM+ System Application and Volume Shadow Copy-services in op de automatische of handmatige opstartmodus.

1. Open de Services-console in Windows.
1. Controleer of de COM+ Systeemtoepassing- en Volumeschaduwkopieerservices niet zijn ingesteld op **Uitgeschakeld** als **opstarttype**.

    ![Opstarttype COM+-systeemtoepassing en volumeschaduwkopieservices controleren](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Niet-ondersteunde beheerde schijfgrootte (foutcode 150172)

Wanneer deze fout optreedt, wordt het volgende bericht weergegeven:

> "Bescherming kan niet worden ingeschakeld voor de virtuele machine, omdat deze *DiskName* heeft met de grootte *DiskSize*)* die kleiner is dan de minimale ondersteunde grootte 1024 MB."

### <a name="possible-cause"></a>Mogelijke oorzaak

De schijf is kleiner dan de ondersteunde grootte van 1024 MB.

### <a name="fix-the-problem"></a>Het probleem oplossen

Controleer of de schijfgrootte zich binnen het bereik van de ondersteunde grootte bevindt en probeer de bewerking opnieuw.

## <a name="protection-was-not-enabled-because-the-grub-configuration-includes-the-device-name-instead-of-the-uuid-error-code-151126"></a><a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>Beveiliging is niet ingeschakeld omdat de GRUB-configuratie de naam van het apparaat bevat in plaats van de UUID (foutcode 151126)

### <a name="possible-cause"></a>Mogelijke oorzaak

De Linux GRUB configuratiebestanden (/boot/grub/menu.lst", /boot/grub/grub.cfg, /boot/grub2/grub.cfg, of /etc/default/grub) kunnen de werkelijke apparaatnamen opgeven in plaats van UUID-waarden voor de *root-* en *hervattingsparameters.* Siteherstel vereist uuids omdat apparaatnamen kunnen worden gewijzigd. Bij het opnieuw opstarten, een VM kan niet komen met dezelfde naam op failover, wat resulteert in problemen.

De volgende voorbeelden zijn regels uit GRUB-bestanden waarin apparaatnamen (vet weergegeven) worden weergegeven in plaats van de vereiste uuids:

- Bestand /boot/grub2/grub.cfg

  > linux /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2** ${extra_cmdline} **cv=/dev/sda1** splash=stille stille showopts

- Bestand: /boot/grub/menu.lst

  > kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314


### <a name="fix-the-problem"></a>Het probleem oplossen

Vervang elke apparaatnaam door de bijbehorende UUID:

1. Zoek de UUID van het apparaat door de naam van het **opdrachtblkid-apparaat** ***device name***uit te voeren. Bijvoorbeeld:

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Vervang de naam van het apparaat door de UUID, in de indelingen **root=UUID**=*UUID* en **cv=UUID**=*UUID*. Bijvoorbeeld, na vervanging, de lijn van / boot / grub / menu.lst (eerder besproken) zou er als volgt uitzien:

    > kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **cv=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314

1. Probeer de bescherming opnieuw.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>Beveiliging inschakelen is mislukt omdat het apparaat dat in de GRUB-configuratie wordt genoemd, niet bestaat (foutcode 151124)

### <a name="possible-cause"></a>Mogelijke oorzaak

De GRUB configuratiebestanden (/boot/grub/menu.lst, /boot/grub/grub.cfg, /boot/grub2/grub.cfg, of /etc/default/grub) kunnen de parameters *bevatten rd.lvm.lv* of *rd_LVM_LV*. Deze parameters identificeren de LVM-apparaten (Logical Volume Manager) die bij het opstarten moeten worden ontdekt. Als deze LVM-apparaten niet bestaan, wordt het beveiligde systeem zelf niet opgestart en blijft het vastzitten in het opstartproces. Hetzelfde probleem zal ook worden gezien met de failover VM. Hier zijn enkele voorbeelden:

- Bestand: /boot/grub2/grub.cfg op RHEL7:

    > linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet LANG=en_US. UTF-8

- Bestand: /etc/default/grub op RHEL7:

    > GRUB_CMDLINE_LINUX="crashkernel=auto **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet"

- Bestand: /boot/grub/menu.lst op RHEL6:

    > kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US. UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto **rd_LVM_LV=rootvg/lv_root** KEYBOARDTYPE=pc KEYTABLE=us **rd_LVM_LV=rootvg/lv_swap** rd_NO_DM rhgb quiet

In elk voorbeeld laat het gedeelte vet zien dat de GRUB twee LVM-apparaten moet detecteren met de namen 'root' en 'swap' van de volumegroep 'rootvg'.

### <a name="fix-the-problem"></a>Het probleem oplossen

Als het LVM-apparaat niet bestaat, maakt u het apparaat of verwijdert u de bijbehorende parameters uit de GRUB-configuratiebestanden. Probeer vervolgens opnieuw om bescherming in te schakelen.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>Een update voor de mobiliteitsservice voor siteherstel die is voltooid met waarschuwingen (foutcode 151083)

De Site Recovery Mobility Service heeft vele componenten, waarvan er één de filterdriver wordt genoemd. Het filterstuurprogramma wordt alleen tijdens het opnieuw opstarten van het systeem in het systeemgeheugen geladen. Wanneer een Mobility Service-update wijzigingen in het filterstuurprogramma bevat, wordt de machine bijgewerkt, maar ziet u nog steeds een waarschuwing dat sommige oplossingen een herstart vereisen. De waarschuwing wordt weergegeven omdat de filterstuurprogrammafixes alleen van kracht kunnen worden wanneer het nieuwe filterstuurprogramma wordt geladen, wat alleen gebeurt tijdens een herstart.

> [!NOTE]
> Dit is slechts een waarschuwing. Bestaande replicatie blijft werken, zelfs na de nieuwe agentupdate. U ervoor kiezen om opnieuw op te starten wanneer u de voordelen van het nieuwe filterstuurprogramma wilt, maar het oude filterstuurprogramma blijft werken als u niet opnieuw start.
>
> Afgezien van het filterstuurprogramma worden de voordelen van andere verbeteringen en oplossingen in de Mobility Service-update van kracht zonder dat een herstart nodig is.

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Beveiliging kan niet worden ingeschakeld omdat de door replica beheerde schijf al bestaat, zonder verwachte tags, in de doelgroep (foutcode 150161)

### <a name="possible-cause"></a>Mogelijke oorzaak

Dit probleem kan optreden als de virtuele machine eerder is beveiligd en wanneer replicatie is uitgeschakeld, de replicaschijf niet is schoongemaakt.

### <a name="fix-the-problem"></a>Het probleem oplossen

Verwijder de replicaschijf die in het foutbericht is geïdentificeerd en probeer de mislukte beveiligingstaak opnieuw.

## <a name="next-steps"></a>Volgende stappen

[Virtuele Azure-machines repliceren](site-recovery-replicate-azure-to-azure.md)
