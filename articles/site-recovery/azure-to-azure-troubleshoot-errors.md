---
title: Azure VM-replicatie oplossen in Azure-siteherstel
description: Problemen oplossen bij het repliceren van virtuele Azure-machines voor herstel na noodgevallen.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: 0882eaa8b54966c7a804cf78a3928771b238e056
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885001"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Problemen met Azure-to-Azure VM-replicatiefouten oplossen

In dit artikel wordt beschreven hoe u veelvoorkomende fouten in Azure Site Recovery oplossen tijdens replicatie en herstel van Virtuele Azure-machines (VM's) van het ene gebied naar het andere. Zie de [ondersteuningsmatrix voor het repliceren van Azure VM's voor](azure-to-azure-support-matrix.md)meer informatie over ondersteunde configuraties.

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemen met Azure-resourcesquota (foutcode 150097)

Controleer of uw abonnement is ingeschakeld om Azure VM's te maken in het doelgebied dat u wilt gebruiken als dr-regio (disaster recovery). Uw abonnement heeft voldoende quota nodig om VM's van de benodigde formaten te maken. Siteherstel kiest standaard een doel-VM-grootte die gelijk is aan de grootte van de bron-VM. Als de overeenkomende grootte niet beschikbaar is, kiest Siteherstel automatisch de dichtstbijzijnde beschikbare grootte.

Als er geen grootte is die de bron-VM-configuratie ondersteunt, wordt het volgende bericht weergegeven:

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>Mogelijke oorzaken

- Uw abonnements-ID is niet ingeschakeld om VM's te maken op de locatie van de doelregio.
- Uw abonnements-ID is niet ingeschakeld of heeft onvoldoende quota om specifieke VM-formaten te maken op de locatie van het doelgebied.
- Er wordt geen geschikte doel-VM-grootte gevonden die overeenkomt met het aantal NIC-gegevens (Network Interface)-telling (2) van de bron-VM voor de abonnements-id op de locatie van de doelregio.

### <a name="fix-the-problem"></a>Het probleem oplossen

Neem contact op met [Azure-factureringsondersteuning](/azure/azure-portal/supportability/resource-manager-core-quotas-request) om uw abonnement in staat te stellen VM's te maken met de vereiste grootte op de doellocatie. Probeer vervolgens de mislukte bewerking opnieuw.

Als de doellocatie een capaciteitsbeperking heeft, schakelt u replicatie uit naar die locatie. Schakel vervolgens replicatie in naar een andere locatie waar uw abonnement voldoende quotum heeft om VM's van de vereiste formaten te maken.

## <a name="trusted-root-certificates-error-code-151066"></a>Vertrouwde rootcertificaten (foutcode 151066)

Als niet alle meest recente vertrouwde rootcertificaten aanwezig zijn op de VM, kan uw taak om replicatie in te schakelen voor Site Recovery mislukken. Verificatie en autorisatie van siteherstelserviceoproepen van de VM mislukken zonder deze certificaten.

Als de replicatietaak inschakelen mislukt, wordt het volgende bericht weergegeven:

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>Mogelijke oorzaak

De vertrouwde basiscertificaten die nodig zijn voor autorisatie en verificatie zijn niet aanwezig op de virtuele machine.

### <a name="fix-the-problem"></a>Het probleem oplossen

#### <a name="windows"></a>Windows

Installeer voor een VM met het Windows-besturingssysteem de nieuwste Windows-updates, zodat alle vertrouwde rootcertificaten op de VM aanwezig zijn. Volg het typische Windows-updatebeheer- of certificaatupdatebeheerproces in uw organisatie om de nieuwste basiscertificaten en de bijgewerkte certificaatintrekkingslijst op de VM's te krijgen.

- Als u zich in een verbroken omgeving bevindt, volgt u het standaard Windows-updateproces in uw organisatie om de certificaten te ontvangen.
- Als de vereiste certificaten niet aanwezig zijn op de VM, mislukken de oproepen naar de siteherstelservice om veiligheidsredenen.

Ga naar vanuit een browser in `login.microsoftonline.com` uw virtuele machine om te controleren of het probleem is opgelost.

Zie [Vertrouwde wortels en niet-toegestane certificaten configureren](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11))voor meer informatie.

#### <a name="linux"></a>Linux

Volg de richtlijnen van de distributeur van uw Linux-besturingssysteemversie om de nieuwste vertrouwde rootcertificaten en de nieuwste certificaatintrekkingslijst op de VM te krijgen.

Omdat SUSE Linux symbolische koppelingen of symlinks gebruikt om een certificaatlijst bij te houden, voert u de volgende stappen uit:

1. Meld u aan als **rootgebruiker.** Het hashsymbool`#`( ) is de standaardopdrachtprompt.

1. Voer de opdracht uit om de map te wijzigen:

   `cd /etc/ssl/certs`

1. Controleer of het Symantec root CA-certificaat aanwezig is:

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - Als het hoofd-CA-certificaat van Symantec niet wordt gevonden, voert u de volgende opdracht uit om het bestand te downloaden. Controleer op eventuele fouten en volg aanbevolen acties op netwerkfouten.

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. Controleer of het Baltimore root CA-certificaat aanwezig is:

   `ls Baltimore_CyberTrust_Root.pem`

   - Als het kissingscertificaat van Baltimore niet wordt gevonden, voert u deze opdracht uit om het certificaat te downloaden:

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. Controleer of het DigiCert_Global_Root_CA certificaat aanwezig is:

   `ls DigiCert_Global_Root_CA.pem`

    - Als de DigiCert_Global_Root_CA niet wordt gevonden, voert u de volgende opdrachten uit om het certificaat te downloaden:

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. Voer het rehashscript uit om de hashes van het certificaatonderwerp voor de nieuw gedownloade certificaten bij te werken:

   `c_rehash`

1. Voer de volgende opdrachten uit om te controleren of het onderwerp hashes als symlinks voor de certificaten is gemaakt:

   ```shell
   ls -l | grep Baltimore
   ```

   ```Output
   lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem

   -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem
   ```

   ```shell
   ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

   lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
   ```

   ```shell
   ls -l | grep DigiCert_Global_Root
   ```

   ```Output
   lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem

   -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem
   ```

1. Maak een kopie van het bestand _VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem_ met bestandsnaam _b204d74a.0:_

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. Maak een kopie van het bestand _Baltimore_CyberTrust_Root.pem_ met bestandsnaam _653b494a.0:_

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. Maak een kopie van het bestand _DigiCert_Global_Root_CA.pem_ met bestandsnaam _3513523f.0:_

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. Controleer of de bestanden aanwezig zijn:

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Uitgaande connectiviteit voor URL's of IP-bereiken voor siteherstel (foutcode 151037 of 151072)

Als replicatie van siteherstel werkt, is uitgaande connectiviteit met specifieke URL's vereist van de VM. Als uw vm zich achter een firewall bevindt of NSG-regels (Network Security Group) gebruikt om uitgaande connectiviteit te beheren, u met een van deze problemen worden geconfronteerd. Hoewel we uitgaande toegang via URL's blijven ondersteunen, wordt het gebruik van een lijst met IP-bereiken niet langer ondersteund.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>Probleem 1: Kan geen virtuele Azure-machine registreren met siteherstel (151195)

#### <a name="possible-causes"></a>Mogelijke oorzaken

- Er kan geen verbinding worden gemaakt met eindpunten voor siteherstel vanwege een DNS-oplossingsfout (Domain Name System).
- Dit probleem komt vaker voor tijdens herbescherming wanneer u bent mislukt via de virtuele machine, maar de DNS-server is niet bereikbaar vanuit het DR-gebied (Disaster Recovery).

#### <a name="fix-the-problem"></a>Het probleem oplossen

Als u aangepaste DNS gebruikt, controleert u of de DNS-server toegankelijk is vanuit het gebied voor noodherstel.

Ga als u controleren of de VM een aangepaste DNS-instelling gebruikt:

1. Open **virtuele machines** en selecteer de VM.
1. Navigeer naar de **VM-instellingen** en selecteer **Netwerken**.
1. Selecteer **in Virtueel netwerk/subnet**de koppeling om de bronpagina van het virtuele netwerk te openen.
1. Ga naar **Instellingen** en selecteer **DNS-servers**.

Probeer toegang te krijgen tot de DNS-server vanaf de virtuele machine. Als de DNS-server niet toegankelijk is, maakt u deze toegankelijk door te falen via de DNS-server of door de sitelijn tussen het DR-netwerk en DNS te maken.

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-fout.":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Probleem 2: Configuratie van siteherstel is mislukt (151196)

#### <a name="possible-cause"></a>Mogelijke oorzaak

Er kan geen verbinding worden gemaakt met IP4-eindpunten voor verificatie en identiteit van Office 365.

#### <a name="fix-the-problem"></a>Het probleem oplossen

Azure Site Recovery vereiste toegang tot Office 365 IP-bereiken voor verificatie.
Als u NSG-regels/firewallproxy (Azure Network Security Group) gebruikt om uitgaande netwerkconnectiviteit op de VM te beheren, controleert u of u de NSG-regel [(Azure Active Directory) gebruikt](/azure/virtual-network/security-overview#service-tags) om toegang tot AAD toe te staan. We ondersteunen geen OP IP-adres gebaseerde NSG-regels meer.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Probleem 3: Configuratie van siteherstel is mislukt (151197)

#### <a name="possible-cause"></a>Mogelijke oorzaak

Er kan geen verbinding worden gemaakt met eindpunten van de Azure Site Recovery-service.

#### <a name="fix-the-problem"></a>Het probleem oplossen

Als u NSG-regels/firewallproxy (Azure Network Security Group) gebruikt om uitgaande netwerkconnectiviteit op de VM te beheren, controleert u of u servicetags gebruikt. We ondersteunen niet langer het gebruik van een lijst met IP-adressen via NSG's voor Azure Site Recovery.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Probleem 4: Azure naar Azure-replicatie is mislukt wanneer het netwerkverkeer door de on-premises proxyserver gaat (151072)

#### <a name="possible-cause"></a>Mogelijke oorzaak

De aangepaste proxy-instellingen zijn ongeldig en de serviceagent Mobility heeft de proxy-instellingen van Internet Explorer (IE) niet automatisch gedetecteerd.

#### <a name="fix-the-problem"></a>Het probleem oplossen

1. De Mobiliteitsserviceagent detecteert de proxy-instellingen `/etc/environment` van IE op Windows en op Linux.
1. Als u liever alleen proxy instelt voor de Mobiliteitsservice, u de proxygegevens opgeven in _ProxyInfo.conf_ op:

   - **Linux**:`/usr/local/InMage/config/`
   - **Windows**:`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. De _ProxyInfo.conf_ moet de proxy-instellingen in de volgende _INI-indeling_ hebben.

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> De Mobiliteitsserviceagent ondersteunt alleen **niet-geverifieerde proxy's.**

### <a name="more-information"></a>Meer informatie

Als u de [vereiste URL's](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) of de [vereiste IP-bereiken wilt](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)opgeven, volgt u de richtlijnen in Over netwerken in Azure naar [Azure-replicatie](azure-to-azure-about-networking.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Schijf niet gevonden in de machine (foutcode 150039)

Een nieuwe schijf die aan de VM is gekoppeld, moet worden geïnitialiseerd. Als de schijf niet wordt gevonden, wordt het volgende bericht weergegeven:

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>Mogelijke oorzaken

- Er is een nieuwe gegevensschijf aan de VM gekoppeld, maar deze is niet geïnitialiseerd.
- De gegevensschijf in de VM rapporteert niet correct de logische lun-waarde (logical unit number) waartegen de schijf aan de VM is gekoppeld.

### <a name="fix-the-problem"></a>Het probleem oplossen

Controleer of de gegevensschijven zijn geïnitialiseerd en probeer de bewerking opnieuw.

- **Windows**: [Een nieuwe schijf koppelen en initialiseren](/azure/virtual-machines/windows/attach-managed-disk-portal).
- **Linux**: [Initialiseren van een nieuwe data schijf in Linux](/azure/virtual-machines/linux/add-disk).

Als het probleem blijft bestaan, neemt u contact op met ondersteuning.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>Een of meer schijven zijn beschikbaar voor bescherming (foutcode 153039)

### <a name="possible-causes"></a>Mogelijke oorzaken

- Een of meer schijven zijn onlangs toegevoegd aan de virtuele machine na bescherming.
- Een of meer schijven werden geïnitialiseerd na bescherming van de virtuele machine.

### <a name="fix-the-problem"></a>Het probleem oplossen

Als u de replicatiestatus van de virtuele machine weer in orde wilt maken, u ervoor kiezen de schijven te beschermen of de waarschuwing te verwijderen.

#### <a name="to-protect-the-disks"></a>Om de schijven te beschermen

1. Ga naar **VM-schijven met de** > _vm-naam_ > **voor**gerepliceerde items .
1. Selecteer de onbeveiligde schijf en selecteer **Replicatie inschakelen:**

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="Replicatie inschakelen op VM-schijven.":::

#### <a name="to-dismiss-the-warning"></a>De waarschuwing afwijzen

1. Ga naar de_vm-naam_ **van gerepliceerde items** > .
1. Selecteer de waarschuwing in de sectie **Overzicht** en selecteer **OK**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="Waarschuwing voor nieuwe schijven afwijzen.":::

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>Verwijder de virtuele machine uit de kluis aangevuld met informatie (foutcode 150225)

Wanneer Site Recovery de virtuele machine beschermt, worden koppelingen gemaakt op de virtuele bronmachine. Wanneer u de beveiliging verwijdert of replicatie uitschakelt, verwijdert Site recovery deze koppelingen als onderdeel van de opruimtaak. Als de virtuele machine een resourcevergrendeling heeft, wordt de opruimtaak voltooid met de informatie. De informatie zegt dat de virtuele machine is verwijderd uit de Recovery Services kluis, maar dat sommige van de verouderde links niet kon worden opgeruimd op de bronmachine.

U deze waarschuwing negeren als u nooit van plan bent deze virtuele machine opnieuw te beschermen. Maar als u deze virtuele machine later moet beschermen, volgt u de stappen in deze sectie om de koppelingen op te schonen.

> [!WARNING]
> Als u de opruimactie niet doet:
>
> - Wanneer u replicatie inschakelt met de kluis Recovery Services, wordt de virtuele machine niet weergegeven.
> - Als u de VM probeert te beschermen met behulp van Herstel**na de ramp**met de virtuele**machine-instellingen,** >  **Virtual machine** > kan de bewerking niet worden ingeschakeld met de replicatie van het bericht **kan niet worden ingeschakeld vanwege de bestaande verouderde resourcekoppelingen op de VM**.

### <a name="fix-the-problem"></a>Het probleem oplossen

> [!NOTE]
> Site herstel verwijdert de bron virtuele machine niet of beïnvloedt deze op geen enkele manier terwijl u deze stappen uitvoert.

1. Verwijder het slot uit de vm- of VM-brongroep. In de volgende afbeelding moet bijvoorbeeld het bronslot op de benoemde `MoveDemo` vm worden verwijderd:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Vergrendeling verwijderen van VM.":::

1. Download het script om [een verouderde siteherstelconfiguratie](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)te verwijderen.
1. Voer het script uit, _Cleanup-stale-asr-config-Azure-VM.ps1_. Geef de **abonnements-id,** **VM Resource Group**en **vm-naam** op als parameters.
1. Als u wordt gevraagd om Azure-referenties, geeft u deze op. Controleer vervolgens of het script zonder fouten wordt uitgevoerd.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>Replicatie kan niet worden ingeschakeld vanwege verouderde resourcekoppelingen op de VM (foutcode 150226)

### <a name="possible-causes"></a>Mogelijke oorzaken

De virtuele machine heeft een verouderde configuratie van eerdere siteherstelbeveiliging.

Er kan een verouderde configuratie plaatsvinden op een Azure VM als u replicatie voor de Azure VM hebt ingeschakeld met behulp van Siteherstel en vervolgens:

- U hebt replicatie uitgeschakeld, maar de bron-VM had een bronvergrendeling.
- U hebt de kluis Siteherstel verwijderd zonder de replicatie op de vm expliciet uit te schakelen.
- U hebt de brongroep met de kluis Siteherstel verwijderd zonder de replicatie op de VM expliciet uit te schakelen.

### <a name="fix-the-problem"></a>Het probleem oplossen

> [!NOTE]
> Site herstel verwijdert de bron virtuele machine niet of beïnvloedt deze op geen enkele manier terwijl u deze stappen uitvoert.

1. Verwijder het slot uit de vm- of VM-brongroep. In de volgende afbeelding moet bijvoorbeeld het bronslot op de benoemde `MoveDemo` vm worden verwijderd:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Vergrendeling verwijderen van VM.":::

1. Download het script om [een verouderde siteherstelconfiguratie](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)te verwijderen.
1. Voer het script uit, _Cleanup-stale-asr-config-Azure-VM.ps1_. Geef de **abonnements-id,** **VM Resource Group**en **vm-naam** op als parameters.
1. Als u wordt gevraagd om Azure-referenties, geeft u deze op. Controleer vervolgens of het script zonder fouten wordt uitgevoerd.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>Kan de Azure-vm of -brongroep voor de selectie niet zien in de replicatietaak inschakelen

### <a name="issue-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>Probleem 1: De resourcegroep en de virtuele bronmachine bevinden zich op verschillende locaties

Voor siteherstel moeten de bronregiobrongroep en virtuele machines zich op dezelfde locatie bevinden. Als dit niet het zo is, u de virtuele machine of resourcegroep niet vinden wanneer u bescherming probeert toe te passen.

Als tijdelijke oplossing u replicatie inschakelen vanuit de VM in plaats van de kluis Recovery Services. Ga naar **Bron VM** > **Properties** > **Disaster Recovery** en schakel de replicatie in.

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>Probleem 2: De resourcegroep maakt geen deel uit van het geselecteerde abonnement

U de resourcegroep mogelijk niet vinden op het moment van beveiliging als de resourcegroep geen deel uitmaakt van het geselecteerde abonnement. Zorg ervoor dat de resourcegroep deel uitmaakt van het abonnement dat u gebruikt.

### <a name="issue-3-stale-configuration"></a>Probleem 3: Verouderde configuratie

Het is mogelijk dat u de VM die u wilt inschakelen voor replicatie niet ziet als er een verouderde configuratie van siteherstel bestaat op de Azure VM. Deze voorwaarde kan optreden als u replicatie voor de Azure VM hebt ingeschakeld met siteherstel en vervolgens:

- U hebt de kluis Siteherstel verwijderd zonder de replicatie op de vm expliciet uit te schakelen.
- U hebt de brongroep met de kluis Siteherstel verwijderd zonder de replicatie op de VM expliciet uit te schakelen.
- U hebt replicatie uitgeschakeld, maar de bron-VM had een bronvergrendeling.

### <a name="fix-the-problem"></a>Het probleem oplossen

> [!NOTE]
> Zorg ervoor dat `AzureRM.Resources` u de module bijwerkt voordat u het script gebruikt dat in deze sectie wordt genoemd. Site herstel verwijdert de bron virtuele machine niet of beïnvloedt deze op geen enkele manier terwijl u deze stappen uitvoert.

1. Verwijder het slot, indien aanwezig, uit de VM- of VM-brongroep. In de volgende afbeelding moet bijvoorbeeld het bronslot op de benoemde `MoveDemo` vm worden verwijderd:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Vergrendeling verwijderen van VM.":::

1. Download het script om [een verouderde siteherstelconfiguratie](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)te verwijderen.
1. Voer het script uit, _Cleanup-stale-asr-config-Azure-VM.ps1_. Geef de **abonnements-id,** **VM Resource Group**en **vm-naam** op als parameters.
1. Als u wordt gevraagd om Azure-referenties, geeft u deze op. Controleer vervolgens of het script zonder fouten wordt uitgevoerd.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>Kan geen virtuele machine selecteren voor bescherming

### <a name="possible-cause"></a>Mogelijke oorzaak

De virtuele machine heeft een extensie geïnstalleerd in een defecte of niet-responsieve status

### <a name="fix-the-problem"></a>Het probleem oplossen

Ga naar**Settings** > **Instellingen instellingen** **voor virtuele machines** > en controleer of extensies in een mislukte status zijn. Verwijder elke mislukte extensie en probeer vervolgens opnieuw de virtuele machine te beschermen.

## <a name="the-vms-provisioning-state-isnt-valid-error-code-150019"></a>De inrichtingsstatus van de vm is niet geldig (foutcode 150019)

Als u replicatie op de vm wilt inschakelen, moet de instelstatus worden **uitgevoerd**. Volg de volgende stappen om de inrichtingsstatus te controleren:

1. Selecteer in de Azure-portal de **Resource Explorer** van **Alle services**.
1. Vouw de lijst **Abonnementen** uit en selecteer uw abonnement.
1. Vouw de lijst **Resourcegroepen** uit en selecteer de resourcegroep van de VM.
1. Vouw de lijst **Resources** uit en selecteer uw VM.
1. Controleer **het veld ProvisioningState** in de instantieweergave aan de rechterkant.

### <a name="fix-the-problem"></a>Het probleem oplossen

- Als de **provisioningState** **is mislukt,** neemt u contact op met ondersteuning met details om problemen op te lossen.
- Als de **provisioningState** **wordt bijgewerkt,** wordt mogelijk een andere extensie geïmplementeerd. Controleer of er lopende bewerkingen op de VM zijn, wacht tot deze zijn voltooid en probeer vervolgens de mislukte taak Siteherstel opnieuw om replicatie in te schakelen.

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>Kan doel-VM niet selecteren (tabblad netwerkselectie is niet beschikbaar)

### <a name="issue-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Probleem 1: Uw vm is gekoppeld aan een netwerk dat al is toegewezen aan een doelnetwerk

Als de bron-VM deel uitmaakt van een virtueel netwerk en een andere VM van hetzelfde virtuele netwerk al is toegewezen met een netwerk in de doelgroep, is de vervolgkeuzelijst voor netwerkselectie standaard niet beschikbaar (lijkt gedimd).

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="Lijst met netwerkselectie is niet beschikbaar.":::

### <a name="issue-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>Probleem 2: U hebt de vm eerder beveiligd met siteherstel en vervolgens de replicatie uitgeschakeld

Als u de replicatie van een virtuele machine uitschakelt, wordt de netwerktoewijzing niet verwijderd. De toewijzing moet worden verwijderd uit de kluis Van Herstelservices waar de VM is beveiligd. Ga naar **Recovery Services vault** > **Site Recovery Infrastructure** > **Network Mapping**.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="Netwerktoewijzing verwijderen.":::

Het doelnetwerk dat is geconfigureerd tijdens de instelling voor noodherstel kan worden gewijzigd na de eerste installatie en nadat de VM is beveiligd:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="Netwerktoewijzing wijzigen.":::

Het wijzigen van netwerktoewijzing is van invloed op alle beveiligde VM's die dezelfde netwerktoewijzing gebruiken.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>COM+ of Volume Shadow Copy Service-fout (foutcode 151025)

Wanneer deze fout optreedt, wordt het volgende bericht weergegeven:

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>Mogelijke oorzaken

- De COM+ System Application-service is uitgeschakeld.
- De Volume Shadow Copy Service is uitgeschakeld.

### <a name="fix-the-problem"></a>Het probleem oplossen

Stel de COM+ System Application and Volume Shadow Copy Service in op de automatische of handmatige opstartmodus.

1. Open de Services-console in Windows.
1. Controleer of de COM+ Systeemtoepassing en Volume Shadow Copy Service niet zijn ingesteld **op Uitgeschakeld** als **opstarttype.**

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="Controleer het opstarttype COM plus Systeemtoepassing en Volume Shadow Copy Service.":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Niet-ondersteunde beheerde schijfgrootte (foutcode 150172)

Wanneer deze fout optreedt, wordt het volgende bericht weergegeven:

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>Mogelijke oorzaak

De schijf is kleiner dan de ondersteunde grootte van 1024 MB.

### <a name="fix-the-problem"></a>Het probleem oplossen

Controleer of de schijfgrootte zich binnen het bereik van de ondersteunde grootte bevindt en probeer de bewerking opnieuw.

## <a name="protection-wasnt-enabled-because-the-grub-configuration-includes-the-device-name-instead-of-the-uuid-error-code-151126"></a>Beveiliging is niet ingeschakeld omdat de GRUB-configuratie de naam van het apparaat bevat in plaats van de UUID (foutcode 151126)

### <a name="possible-causes"></a>Mogelijke oorzaken

De Linux Grand Unified Bootloader (GRUB) configuratiebestanden _(/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_, of _/etc/default/grub)_ kunnen de werkelijke `root` `resume` apparaatnamen opgeven in plaats van universeel unieke id(UUID) waarden voor de en parameters. Siteherstel vereist uuids omdat apparaatnamen kunnen worden gewijzigd. Bij het opnieuw opstarten, een VM kan niet komen met dezelfde naam op failover, wat resulteert in problemen.

De volgende voorbeelden zijn regels uit GRUB-bestanden waarin apparaatnamen worden weergegeven in plaats van de vereiste uuids:

- Bestand _/boot/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Bestand: _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>Het probleem oplossen

Vervang elke apparaatnaam door de bijbehorende UUID:

1. Zoek de UUID van het apparaat `blkid <device name>`door de opdracht uit te voeren. Bijvoorbeeld:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Vervang de naam van het apparaat door `root=UUID=<UUID>` `resume=UUID=<UUID>`de UUID, in de indelingen en . Bijvoorbeeld, na vervanging, de lijn van _/ boot / grub / menu.lst_ zou eruit zien als de volgende regel:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Probeer de bescherming opnieuw.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>Beveiliging inschakelen is mislukt omdat het apparaat dat in de GRUB-configuratie wordt genoemd, niet bestaat (foutcode 151124)

### <a name="possible-cause"></a>Mogelijke oorzaak

De GRUB configuratie bestanden (_/ boot / grub / menu.lst_, / boot / _grub/grub.cfg_, _/ boot /grub2 /grub.cfg_, of _/ etc / standaard / grub_) kan de parameters `rd.lvm.lv` bevatten of `rd_LVM_LV`. Deze parameters identificeren de LVM-apparaten (Logical Volume Manager) die bij het opstarten moeten worden ontdekt. Als deze LVM-apparaten niet bestaan, wordt het beveiligde systeem zelf niet opgestart en blijft het vastzitten in het opstartproces. Hetzelfde probleem zal ook worden gezien met de failover VM. Hier zijn enkele voorbeelden:

- Bestand: _/boot/grub2/grub.cfg_ op RHEL7:

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- Bestand: _/etc/default/grub_ op RHEL7:

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- Bestand: _/boot/grub/menu.lst_ op RHEL6:

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

In elk voorbeeld moet GRUB twee LVM-apparaten `swap` detecteren met `rootvg`de namen `root` en uit de volumegroep.

### <a name="fix-the-problem"></a>Het probleem oplossen

Als het LVM-apparaat niet bestaat, maakt u het apparaat of verwijdert u de bijbehorende parameters uit de GRUB-configuratiebestanden. Probeer vervolgens opnieuw om bescherming in te schakelen.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>Een update voor de mobiliteitsservice siteherstel die is voltooid met waarschuwingen (foutcode 151083)

De Site Recovery Mobility-service heeft vele componenten, waarvan er één de filterdriver wordt genoemd. Het filterstuurprogramma wordt alleen tijdens het opnieuw opstarten van het systeem in het systeemgeheugen geladen. Wanneer een mobiliteitsservice-update wijzigingen in het filterstuurprogramma bevat, wordt de machine bijgewerkt, maar ziet u nog steeds een waarschuwing dat sommige oplossingen een herstart vereisen. De waarschuwing wordt weergegeven omdat de filterstuurprogrammafixes alleen van kracht kunnen worden wanneer het nieuwe filterstuurprogramma wordt geladen, wat alleen gebeurt tijdens een herstart.

> [!NOTE]
> Dit is slechts een waarschuwing. De bestaande replicatie blijft werken, zelfs na de nieuwe agentupdate. U ervoor kiezen om opnieuw op te starten wanneer u de voordelen van het nieuwe filterstuurprogramma wilt, maar het oude filterstuurprogramma blijft werken als u niet opnieuw start.
>
> Afgezien van het filterstuurprogramma worden de voordelen van andere verbeteringen en oplossingen in de mobiliteitsservice-update van kracht zonder dat een herstart nodig is.

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Beveiliging kan niet worden ingeschakeld omdat de door replica beheerde schijf al bestaat, zonder verwachte tags, in de doelgroep (foutcode 150161)

### <a name="possible-cause"></a>Mogelijke oorzaak

Dit probleem kan optreden als de virtuele machine eerder is beveiligd en wanneer replicatie is uitgeschakeld, de replicaschijf niet is verwijderd.

### <a name="fix-the-problem"></a>Het probleem oplossen

Verwijder de replicaschijf die in het foutbericht is geïdentificeerd en probeer de mislukte beveiligingstaak opnieuw.

## <a name="next-steps"></a>Volgende stappen

[Azure VM's repliceren naar een andere Azure-regio](azure-to-azure-how-to-enable-replication.md)
