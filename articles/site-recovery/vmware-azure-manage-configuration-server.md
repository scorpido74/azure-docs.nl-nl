---
title: De configuratieserver beheren voor herstel na noodgevallen met Azure Site Recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: ramamill
ms.openlocfilehash: 56c53b9e2388cc0594076a5ef35b072216aec20d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672742"
---
# <a name="manage-the-configuration-server-for-vmware-vmphysical-server-disaster-recovery"></a>De configuratieserver voor VMware VM/physical server disaster recovery beheren

U stelt een on-premises configuratieserver in wanneer u [Azure Site Recovery](site-recovery-overview.md) gebruikt voor noodherstel van Vm's en fysieke servers in Azure. De configuratieserver coördineert de communicatie tussen on-premises VMware en Azure en beheert gegevensreplicatie. In dit artikel worden veelvoorkomende taken voor het beheren van de configuratieserver samengevat nadat deze is geïmplementeerd.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-windows-license"></a>Windows-licentie bijwerken

De licentie die bij de OVF-sjabloon is geleverd, is een evaluatielicentie die 180 dagen geldig is. Voor ononderbroken gebruik moet u Windows activeren met een aangeschafte licentie. Licentie-update kan worden gedaan via een standalone sleutel of KMS-standaardsleutel. Richtlijnen zijn beschikbaar op [de opdrachtregel van DISM Windows voor het uitvoeren van BE.](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-windows-edition-servicing-command-line-options) Voor het verkrijgen van sleutels, verwijzen wij u naar [KMS client set-up](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys).

## <a name="access-configuration-server"></a>Toegangsconfiguratieserver

U hebt als volgt toegang tot de configuratieserver:

* Meld u aan bij de VM waarop deze is geïmplementeerd en Azure **Site Recovery Configuration Manager** starten vanaf de snelkoppeling op het bureaublad.
* U ook op afstand toegang krijgen tot de configuratieserver vanaf https://*ConfigurationServerName*/:44315/. Meld u aan met beheerdersreferenties.

## <a name="modify-vmware-server-settings"></a>VMware-serverinstellingen wijzigen

1. Als u een andere VMware-server aan de configuratieserver wilt koppelen, selecteert u na aanmelding **de vCenter server/vSphere ESXi-server toevoegen.** [sign-in](#access-configuration-server)
2. Voer de details in en selecteer **OK**.

## <a name="modify-credentials-for-automatic-discovery"></a>Referenties wijzigen voor automatische detectie

1. Als u de referenties wilt bijwerken die worden gebruikt om verbinding te maken met de [VMware-server](#access-configuration-server)voor automatische detectie van VMware VM's, kiest u na aanmelding het account en klikt u op **Bewerken**.
2. Voer de nieuwe referenties in en selecteer **OK**.

    ![VMware wijzigen](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

U de referenties ook wijzigen via CSPSConfigtool.exe.

1. Log in op de configuratieserver en start CSPSConfigtool.exe
2. Kies het account dat u wilt wijzigen en klik op **Bewerken**.
3. Voer de gewijzigde referenties in en klik op **Ok**

## <a name="modify-credentials-for-mobility-service-installation"></a>Referenties wijzigen voor de installatie van Mobility Service

Wijzig de referenties die worden gebruikt om Mobility Service automatisch te installeren op de VMware VM's die u inschakelt voor replicatie.

1. Selecteer Na [aanmelding](#access-configuration-server)de optie **Referenties van virtuele machines beheren**
2. Kies het account dat u wilt wijzigen en klik op **Bewerken**
3. Voer de nieuwe referenties in en selecteer **OK**.

    ![Referenties van Mobility Service wijzigen](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

U ook referenties wijzigen via CSPSConfigtool.exe.

1. Log in op de configuratieserver en start CSPSConfigtool.exe
2. Kies het account dat u wilt wijzigen en klik op **Bewerken**
3. Voer de nieuwe referenties in en klik op **Ok**.

## <a name="add-credentials-for-mobility-service-installation"></a>Referenties toevoegen voor de installatie van de Mobiliteitsservice

Als u het toevoegen van referenties hebt gemist tijdens de OVF-implementatie van de configuratieserver,

1. Selecteer [Na aanmelding](#access-configuration-server)de optie **Virtuele machinereferenties beheren**.
2. Klik op **Referenties voor virtuele machines toevoegen**.
    ![add-mobility-referenties](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. Voer de nieuwe referenties in en klik op **Toevoegen**.

U ook referenties toevoegen via CSPSConfigtool.exe.

1. Log in op de configuratieserver en start CSPSConfigtool.exe
2. Klik **op Toevoegen,** voer de nieuwe referenties in en klik op **Ok**.

## <a name="modify-proxy-settings"></a>Proxy-instellingen wijzigen

Wijzig de proxy-instellingen die worden gebruikt door de configuratieservermachine voor internettoegang tot Azure. Als u naast de standaardprocesserver op de configuratieservermachine een processerver hebt, wijzigt u de instellingen op beide machines.

1. Nadat u zich bij de configuratieserver hebt [aangemeld,](#access-configuration-server) selecteert **u De verbinding beheren**.
2. Werk de proxywaarden bij. Selecteer vervolgens **Opslaan** om de instellingen bij te werken.

## <a name="add-a-network-adapter"></a>Een netwerkadapter toevoegen

Met de OVF-sjabloon (Open Virtualisatie Format) wordt de vm van de configuratieserver geïmplementeerd met één netwerkadapter.

- U [een extra adapter aan de VM toevoegen,](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter)maar u moet deze toevoegen voordat u de configuratieserver in de kluis registreert.
- Als u een adapter wilt toevoegen nadat u de configuratieserver in de kluis hebt geregistreerd, voegt u de adapter toe in de VM-eigenschappen. Dan moet u de server [opnieuw registreren](#reregister-a-configuration-server-in-the-same-vault) in de kluis.

## <a name="how-to-renew-ssl-certificates"></a>SSL-certificaten vernieuwen

De configuratieserver heeft een ingebouwde webserver, die activiteiten van de Mobiliteitsagents orkestreert op alle beveiligde machines, ingebouwde/scale-out processervers en hoofddoelservers die ermee zijn verbonden. De webserver gebruikt een SSL-certificaat om clients te verifiëren. Het certificaat verloopt na drie jaar en kan te huur worden verlengd.

### <a name="check-expiry"></a>Verlopen controleren

De vervaldatum wordt weergegeven onder **de status Configuratieserver**. Voor configuratieserverimplementaties vóór mei 2016 is het verlopen van het certificaat ingesteld op één jaar. Als u een certificaat hebt dat verloopt, vindt u het volgende:

- Wanneer de vervaldatum twee maanden of minder is, begint de service meldingen te verzenden in de portal en per e-mail (als u zich hebt geabonneerd op meldingen voor siteherstel).
- Er wordt een meldingsbanner weergegeven op de pagina met de bronvan de kluis. Selecteer de banner voor meer informatie.
- Als u een **knop Nu bijwerken** ziet, geeft dit aan dat sommige onderdelen in uw omgeving niet zijn geüpgraded naar 9.4.xxxx.x of hogere versies. Upgrade de onderdelen voordat u het certificaat verlengt. U niet verlengen op oudere versies.

### <a name="if-certificates-are-yet-to-expire"></a>Als certificaten nog niet verlopen

1. Open **Site Recovery Infrastructure** > **Configuration Server**in de kluis om te verlengen. Selecteer de vereiste configuratieserver.
2. Zorg ervoor dat alle componenten processervers, hoofddoelservers en mobiliteitsagents op alle beveiligde machines op de nieuwste versies uitvoeren en in verbonden staat zijn.
3. Selecteer nu **Certificaten vernieuwen**.
4. Volg de instructies op deze pagina zorgvuldig en klik op goed om certificaten te vernieuwen op geselecteerde configuratieserver en de bijbehorende componenten.

### <a name="if-certificates-have-already-expired"></a>Als certificaten al zijn verlopen

1. Na het verlopen kunnen certificaten **niet worden verlengd vanuit azure portal.** Voordat u verdergaat, moet u ervoor zorgen dat alle componenten processervers, hoofddoelservers en mobiliteitsagents op alle beveiligde machines op de nieuwste versies gebruiken en in verbonden toestand zijn.
2. **Volg deze procedure alleen als certificaten al zijn verlopen.** Log in bij configuratieserver, navigeer naar C-station > Programmagegevens > Site Recovery > thuis > svsystems > opslaglocatie en voer het executortool Vernieuwen certs uit als beheerder.
3. Er verschijnt een PowerShell-uitvoeringsvenster en activeert de verlenging van certificaten. Dit kan tot 15 minuten duren. Sluit het venster niet totdat de vernieuwing is voltooid.

:::image type="content" source="media/vmware-azure-manage-configuration-server/renew-certificates.png" alt-text="RenewCertificaten":::

## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Een configuratieserver opnieuw registreren in dezelfde kluis

U de configuratieserver opnieuw registreren in dezelfde kluis als dat nodig is. Als u een extra processerverhebt, u naast de standaardprocesserver die op de configuratieservermachine wordt uitgevoerd, beide machines opnieuw registreren.


1. Open in de kluis **De** > configuratieservers**voor siteherstelbeheer** > **Configuration Servers**beheren .
2. Selecteer **in Servers**de **registratiesleutel downloaden** om het bestand met vaultcredentials te downloaden.
3. Meld u aan bij de configuratieservermachine.
4. In **%ProgramData%\ASR\home\svsystems\bin**opent u **cspsconfigtool.exe**.
5. Selecteer op het tabblad **Vault Registration** de optie **Bladeren**en zoek het kluisgegevensbestand dat u hebt gedownload.
6. Geef indien nodig proxyservergegevens op. Selecteer vervolgens **Registreren**.
7. Open een powershell-opdrachtvenster voor beheerders en voer de volgende opdracht uit:
   ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

    >[!NOTE]
    >Als u de nieuwste certificaten van configuratieserver naar scale-outprocesserver wilt **halen,** voert u de opdracht *\<" Installatiestation\Microsoft Azure Site Recovery\agent\cdpcli.exe>"--registermt uit*

8. Start ten slotte de obengine opnieuw door de volgende opdracht uit te voeren.
   ```
        net stop obengine
        net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>Een configuratieserver registreren met een andere kluis

> [!WARNING]
> De volgende stap ontast de configuratieserver van de huidige kluis en de replicatie van alle beveiligde virtuele machines onder de configuratieserver wordt gestopt.

1. Log in bij de configuratieserver.
2. Open een powershell-opdrachtvenster voor beheerders en voer de volgende opdracht uit:

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. Start de browserportal van het configuratieservertoestel met de snelkoppeling op uw bureaublad.
4. Voer de registratiestappen uit die vergelijkbaar zijn met een nieuwe [configuratieserverregistratie](vmware-azure-tutorial.md#register-the-configuration-server).

## <a name="upgrade-the-configuration-server"></a>De configuratieserver bijwerken

U voert updaterollups uit om de configuratieserver bij te werken. Updates kunnen worden toegepast voor maximaal N-4-versies. Bijvoorbeeld:

- Als u 9,7, 9.8, 9.9 of 9.10 uitvoert, u direct upgraden naar 9,11.
- Als u een 9.6 of eerder uitvoert en u wilt upgraden naar 9.11, moet u eerst upgraden naar versie 9.7. vóór 9.11.

Voor gedetailleerde richtlijnen over de ondersteuningsverklaring voor Azure Site Recovery-componenten verwijzen we [hier](https://aka.ms/asr_support_statement)naar .
Koppelingen om rollups bij te werken voor het upgraden naar alle versies van de configuratieserver zijn [hier](https://aka.ms/asr_update_rollups)beschikbaar.

> [!IMPORTANT]
> Bij elke nieuwe versie 'N' van een Azure Site Recovery-component die wordt uitgebracht, worden alle versies onder 'N-4' als buiten de ondersteuning beschouwd. Het is altijd raadzaam om te upgraden naar de nieuwste versies beschikbaar.</br>
> Voor gedetailleerde richtlijnen over de ondersteuningsverklaring voor Azure Site Recovery-componenten verwijzen we [hier](https://aka.ms/asr_support_statement)naar .

Upgrade de server als volgt:

1. Ga in de kluis naar **Configuratieservers voor** > **siteherstelinfrastructuur** > **beheren.**
2. Als er een update beschikbaar is, wordt er een koppeling weergegeven in de > kolom **Agent Versie.**
    ![Update](./media/vmware-azure-manage-configuration-server/update2.png)
3. Download het updateinstallatiebestand naar de configuratieserver.

    ![Update](./media/vmware-azure-manage-configuration-server/update1.png)

4. Dubbelklik om het installatieprogramma uit te voeren.
5. Het installatieprogramma detecteert de huidige versie die op de machine wordt uitgevoerd. Klik **op Ja** om de upgrade te starten.
6. Wanneer de upgrade is voltooid, wordt de serverconfiguratie gevalideerd.

    ![Update](./media/vmware-azure-manage-configuration-server/update3.png)

7. Klik **op Voltooien** om het installatieprogramma te sluiten.
8. Als u de rest van de onderdelen siteherstel wilt upgraden, raadpleegt u onze [upgraderichtlijnen.](https://aka.ms/asr_vmware_upgrades)

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>Configuratieserver/processerver upgraden vanaf de opdrachtregel

Voer het installatiebestand als volgt uit:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Voorbeeldgebruik
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parameters

|Parameternaam| Type | Beschrijving| Waarden|
|-|-|-|-|
| /ServerMode|Vereist|Hiermee wordt aangegeven of zowel de configuratieserver als de processerver moet worden geïnstalleerd, of alleen de processerver|CS<br>PS|
|/InstallLocation|Vereist|De map waarin de onderdelen worden geïnstalleerd| Een map op de computer|
|/MySQLCredsFilePath|Vereist|Het bestandspad waarin de referenties voor de MySQL-server worden opgeslagen|Het bestand moet de indeling hebben die hieronder wordt aangegeven|
|/VaultCredsFilePath|Vereist|Het pad naar het bestand met kluisreferenties|Geldig bestandspad|
|/EnvType|Vereist|Type omgeving dat u wilt beschermen |VMware<br>NonVMware|
|/PSIP|Vereist|IP-adres van de NIC dat wordt gebruikt voor de overdracht van replicatiegegevens| Een geldig IP-adres|
|/CSIP|Vereist|Het IP-adres van de NIC waarop de configuratieserver luistert| Een geldig IP-adres|
|/PassphraseFilePath|Vereist|Het volledige pad naar het bestand met de wachtwoordzin|Geldig bestandspad|
|/BypassProxy|Optioneel|Hiermee wordt aangegeven dat de configuratieserver zonder proxy verbinding moet maken met Azure|Deze waarde moet van Venu worden opgehaald|
|/ProxySettingsFilePath|Optioneel|Proxy-instellingen (de standaardproxy vereist verificatie of een aangepaste proxy)|Het bestand moet de indeling hebben die hieronder wordt aangegeven|
|DataTransferSecurePort|Optioneel|Poortnummer van de PSIP dat wordt gebruikt voor replicatiegegevens| Geldig poortnummer (standaardwaarde is 9433)|
|/SkipSpaceCheck|Optioneel|Hiermee wordt aangegeven dat de controle op vrije ruimte in de cacheschijf moet worden overgeslagen| |
|/AcceptThirdpartyEULA|Vereist|Een vlag impliceert acceptatie van de gebruiksrechtovereenkomst van derden| |
|/ShowThirdpartyEULA|Optioneel|Hiermee wordt de gebruiksrechtovereenkomst van derden weergegeven. Indien opgegeven als invoer, worden alle andere parameters genegeerd| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Bestandsinvoer maken voor MYSQLCredsFilePath

De parameter MySQLCredsFilePath neemt een bestand als invoer. Maak het bestand met de volgende indeling en geef het door als parameter MySQLCredsFilePath.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Bestandsinvoer maken voor ProxySettingsFilePath
ProxySettingsFilePath parameter neemt een bestand als invoer. Maak het bestand met de volgende indeling en geef het door als parameter ProxySettingsFilePath.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```

## <a name="delete-or-unregister-a-configuration-server"></a>Een configuratieserver verwijderen of uitschrijven

1. [Schakel de beveiliging voor](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) alle VM's onder de configuratieserver uit.
2. [Alle replicatiebeleidsregels loskoppelen](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) en [verwijderen](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) van de configuratieserver.
3. [Verwijder](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) alle vCenter-servers/vSphere-hosts die zijn gekoppeld aan de configuratieserver.
4. Open **siteherstelconfiguratieservers** > **Configuration Servers**in de kluis .
5. Selecteer de configuratieserver die u wilt verwijderen. Selecteer vervolgens op de pagina **Details** de optie **Verwijderen**.

    ![Configuratieserver verwijderen](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Verwijderen met PowerShell

U de configuratieserver optioneel verwijderen met PowerShell.

1. [Installeer](https://docs.microsoft.com/powershell/azure/install-Az-ps) de Azure PowerShell-module.
2. Meld u aan bij uw Azure-account met deze opdracht:

    `Connect-AzAccount`
3. Selecteer het kluisabonnement.

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Stel de context van de kluis in.

    ```
    $vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Haal de configuratieserver op.

    `$fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Verwijder de configuratieserver.

    `Remove-AzSiteRecoveryFabric -Fabric $fabric [-Force]`

> [!NOTE]
> U de optie **-Force** gebruiken in Remove-AzSiteRecoveryFabric voor gedwongen verwijdering van de configuratieserver.

## <a name="generate-configuration-server-passphrase"></a>Wachtwoordzin voor configuratieserver genereren

1. Meld u aan bij uw configuratieserver en open vervolgens een opdrachtpromptvenster als beheerder.
2. Als u de map wilt wijzigen in de map met opslaglocatie, voert u de opdracht **cd %ProgramData%\ASR\home\svsystems\bin uit**
3. Voer **genpassphrase.exe -v > MobSvc.passphrase**uit om het wachtwoordzinsbestand te genereren.
4. Uw wachtwoordzin wordt opgeslagen in het bestand dat zich bevindt bij **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## <a name="refresh-configuration-server"></a>Configuratieserver vernieuwen

1. Navigeer in de Azure-portal naar **Recovery Services Vault** > **Manage** > **Site Recovery Infrastructure** > **For VMware & Physical machines** > **Configuration Servers**
2. Klik op de configuratieserver die u wilt vernieuwen.
3. Klik op het blad met details van de gekozen configuratieserver op **Meer** > **vernieuwenserver**.
4. Houd de voortgang van de taak in de gaten onder de taken **van vault** > **monitoring** > **site recovery van**Recovery Services .

## <a name="failback-requirements"></a>Vereisten voor failback

Tijdens reprotect en failback moet de on-premises configuratieserver worden uitgevoerd en in een verbonden status. Voor een succesvolle failback moet de virtuele machine die wordt mislukt, worden weergegeven in de configuratieserverdatabase.

Zorg ervoor dat u regelmatig geplande back-ups van uw configuratieserver maakt. Als er zich een ramp voordoet en de configuratieserver verloren gaat, moet u eerst de configuratieserver herstellen van een back-up en ervoor zorgen dat de herstelde configuratieserver hetzelfde IP-adres heeft waarmee deze is geregistreerd in de kluis. Failback werkt niet als er een ander IP-adres wordt gebruikt voor de herstelde configuratieserver.

## <a name="next-steps"></a>Volgende stappen

Bekijk de zelfstudies voor het instellen van noodherstel van [VMware VM's](vmware-azure-tutorial.md) op Azure.
