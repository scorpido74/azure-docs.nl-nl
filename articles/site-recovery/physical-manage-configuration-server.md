---
title: De configuratieserver voor fysieke servers beheren in Azure Site Recovery
description: In dit artikel wordt beschreven hoe u de configuratieserver azure site recovery voor herstel van fysieke servers naar Azure beheert.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: eb7e891c031be5ac01295905d5c3304dc6818737
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478965"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>De configuratieserver beheren voor herstel van de fysieke serverramp

U stelt een on-premises configuratieserver in wanneer u de [Azure Site Recovery-service](site-recovery-overview.md) gebruikt voor het herstel van fysieke servers naar Azure. De configuratieserver coördineert de communicatie tussen on-premises machines en Azure en beheert gegevensreplicatie. In dit artikel worden veelvoorkomende taken voor het beheren van de configuratieserver samengevat nadat deze is geïmplementeerd.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

De tabel bevat een overzicht van de vereisten voor het implementeren van de on-premises configuratieservermachine.

| **Component** | **Vereiste** |
| --- |---|
| CPU-kernen| 8 |
| RAM | 16 GB|
| Aantal schijven | 3, inclusief de osschijf, de servercacheschijf en het bewaarstation voor failback |
| Vrije schijfruimte (cache van de processerver) | 600 GB
| Vrije schijfruimte (bewaarschijf) | 600 GB|
| Besturingssysteem  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Landinstelling van het besturingssysteem | Engels (VS)|
| VMware vSphere PowerCLI-versie | Niet vereist|
| Windows Server-functies | Schakel deze rollen niet in: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Groepsbeleid| Schakel dit groepsbeleid niet in: <br> - Toegang tot de opdrachtprompt voorkomen <br> - Toegang tot registerbewerkingstools voorkomen <br> - Logica voor bestandsbijlagen vertrouwen <br> - Script-uitvoering inschakelen <br> [Meer informatie](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | - Geen reeds bestaande standaardwebsite <br> - [Anonieme verificatie](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) inschakelen <br> - [FastCGI-instelling](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) inschakelen  <br> - Geen reeds bestaande website/applicatie luisteren op poort 443<br>|
| NIC-type | VMXNET3 (wanneer ingezet als VMware VM) |
| Type IP-adres | Statisch |
| Internettoegang | De server heeft toegang nodig tot deze URL's: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - `https://management.azure.com` <br> - *.services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi(niet vereist voor scale-out processervers) <br> - time.nist.gov <br> - time.windows.com |
| Poorten | 443 (Orchestration-besturingselement)<br>9443 (Gegevenstransport)|

## <a name="download-the-latest-installation-file"></a>Download het nieuwste installatiebestand

De nieuwste versie van het installatiebestand van de configuratieserver is beschikbaar in de portal SiteRecovery. Bovendien kan het rechtstreeks worden gedownload van het [Microsoft Download Center.](https://aka.ms/unifiedsetup)

1. Meld u aan bij de Azure-portal en blader naar uw Recovery Services Vault.
2. Blader naar **configuratieservers voor siteherstelinfrastructuur** > **Configuration Servers** (onder Voor VMware-& Fysieke machines).
3. Klik op de knop **+servers.**
4. Klik **op** de pagina Server toevoegen op de knop Downloaden om de registratiesleutel te downloaden. U hebt deze sleutel nodig tijdens de configuratieserverinstallatie om deze te registreren bij de Azure Site Recovery-service.
5. Klik **op de** koppeling Microsoft Azure Site Recovery Unified Setup downloaden om de nieuwste versie van de configuratieserver te downloaden.

   ![Pagina downloaden](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>De server installeren en registreren

1. Voer het installatiebestand voor de geïntegreerde Setup uit.
2. Selecteer **de configuratieserver en processerver installeren** **in Voordat u begint.**

    ![Voordat u begint](./media/physical-manage-configuration-server/combined-wiz1.png)

3. Klik bij **Licentievoorwaarden voor software van derden** op **Ik ga akkoord** om MySQL te downloaden en te installeren.
4. Geef bij **Internetinstellingen** op hoe de provider die op de configuratieserver wordt uitgevoerd, via internet verbinding moet maken met Azure Site Recovery. Zorg ervoor dat u de vereiste URL's hebt toegestaan.

    - Als u verbinding wilt maken met de proxy die momenteel op de machine is ingesteld, selecteert u **Verbinding maken met Azure Site Recovery met een proxyserver.**
    - Als u wilt dat de provider rechtstreeks verbinding maakt, selecteert u **Rechtstreeks verbinding maken met Azure Site Recovery zonder proxyserver.**
    - Als de bestaande proxy verificatie vereist of als u een aangepaste proxy voor de Provider-verbinding wilt gebruiken, selecteert u **Verbinding maken met aangepaste proxy-instellingen**en geeft u het adres, de poort en de referenties op.
     ![Firewall](./media/physical-manage-configuration-server/combined-wiz4.png)
6. Tijdens Setup wordt in **Controle op vereisten** gecontroleerd of de installatie kan worden uitgevoerd. Als er een waarschuwing wordt weergegeven over **Synchronisatiecontrole voor algemene tijd**, moet u controleren of de tijd op de systeemklok (instellingen voor **datum en tijd**) overeenkomt met de tijdzone.

    ![Vereisten](./media/physical-manage-configuration-server/combined-wiz5.png)
7. Maak bij **MySQL-configuratie** referenties voor aanmelden bij de MySQL-serverinstantie die is geïnstalleerd.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. Selecteer bij **Details van de omgeving** of u virtuele VMware-machines wilt repliceren. Als dat zo is, controleert Setup of PowerCLI 6.0 is geïnstalleerd.
9. Selecteer bij **Installatielocatie** waar u de binaire bestanden wilt installeren en de cache wilt opslaan. Het station dat u selecteert, moet ten minste 5 GB vrije schijfruimte bevatten, maar wij raden u aan een cachestation te gebruiken met minstens 600 GB vrije ruimte.

    ![Installatielocatie](./media/physical-manage-configuration-server/combined-wiz8.png)
10. Selecteer **in Netwerkselectie**eerst de NIC die de ingebouwde processerver gebruikt voor detectie en pushinstallatie van mobiliteitsservice op bronmachines en selecteer vervolgens de NIC die configuratieserver gebruikt voor connectiviteit met Azure. Poort 9443 is de standaardpoort voor het verzenden en ontvangen van replicatieverkeer, maar u kunt dit poortnummer aanpassen aan de vereisten van de omgeving. Naast poort 9443 wordt ook poort 443 geopend. Deze wordt door een webserver gebruikt om replicatiebewerkingen in te delen. Gebruik poort 443 niet voor het verzenden of ontvangen van replicatieverkeer.

    ![Netwerk selecteren](./media/physical-manage-configuration-server/combined-wiz9.png)


11. Lees de informatie bij **Samenvatting** en klik op **Installeren**. Wanneer de installatie is voltooid, wordt er een wachtwoordzin gegenereerd. U hebt deze nodig bij het inschakelen van de replicatie. Kopieer de wachtwoordzin daarom en bewaar deze op een veilige locatie.


Nadat de registratie is voltooid, wordt de server weergegeven op het blade **Van de instellingenservers** > **Servers** in de kluis.


## <a name="install-from-the-command-line"></a>Installeren vanaf de opdrachtregel

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
MySQLRootPassword = "Password"
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
## <a name="modify-proxy-settings"></a>Proxy-instellingen wijzigen

U de proxy-instellingen voor de configuratieservermachine als volgt wijzigen:

1. Meld u aan bij de configuratieserver.
2. Start de cspsconfigtool.exe met behulp van de snelkoppeling op uw bureaublad.
3. Klik op het tabblad **Kluisregistratie.**
4. Download een nieuw kluisregistratiebestand van de portal en geef het als input voor het hulpprogramma.

   ![registerconfiguratie-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Geef de nieuwe proxygegevens op en klik op de knop **Registreren.**
6. Open een opdrachtvenster voor Beheer PowerShell.
7. Voer de volgende opdracht uit:

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > Als er extra processervers zijn gekoppeld aan de configuratieserver, moet u [de proxy-instellingen op alle scale-outprocesservers](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) in uw implementatie herstellen.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Een configuratieserver opnieuw registreren met dezelfde kluis
1. Meld u aan bij uw configuratieserver.
2. Start de cspsconfigtool.exe met behulp van de snelkoppeling op uw bureaublad.
3. Klik op het tabblad **Kluisregistratie.**
4. Download een nieuw registratiebestand van de portal en geef het als input voor de tool.
      ![registerconfiguratie-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Geef de gegevens van de proxyserver op en klik op de knop **Registreren.**  
6. Open een opdrachtvenster voor Beheer PowerShell.
7. Voer de volgende opdracht uit

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > Als u meerdere processerverhebt, moet u [deze opnieuw registreren.](vmware-azure-manage-process-server.md#reregister-a-process-server)

## <a name="register-a-configuration-server-with-a-different-vault"></a>Een configuratieserver registreren met een andere kluis

> [!WARNING]
> De volgende stap ontast de configuratieserver van de huidige kluis en de replicatie van alle beveiligde virtuele machines onder de configuratieserver wordt gestopt.

1. Inloggen op de configuratieserver
2. voer de opdracht uit vanuit een opdrachtprompt voor beheerders:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Start de cspsconfigtool.exe met behulp van de snelkoppeling op uw bureaublad.
4. Klik op het tabblad **Kluisregistratie.**
5. Download een nieuw registratiebestand van de portal en geef het als input voor de tool.
6. Geef de gegevens van de proxyserver op en klik op de knop **Registreren.**  
7. Open een opdrachtvenster voor Beheer PowerShell.
8. Voer de volgende opdracht uit
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Een configuratieserver bijwerken

U voert updaterollups uit om de configuratieserver bij te werken. Updates kunnen worden toegepast voor maximaal N-4-versies. Bijvoorbeeld:

- Als u een 9,7, 9,8, 9,9 of 9,10 draait, u direct upgraden naar 9,11.
- Als u een 9.6 of eerder uitvoert en u wilt upgraden naar 9.11, moet u eerst upgraden naar versie 9.7. vóór 9.11.

Koppelingen om rollups bij te werken voor het upgraden naar alle versies van de configuratieserver zijn beschikbaar op de [wiki-updatespagina.](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx)

Upgrade de server als volgt:

1. Download het updateinstallatiebestand naar de configuratieserver.
2. Dubbelklik om het installatieprogramma uit te voeren.
3. Het installatieprogramma detecteert de huidige versie die op de machine wordt uitgevoerd.
4. Klik op **OK** om de upgrade te bevestigen en voer de upgrade uit. 


## <a name="delete-or-unregister-a-configuration-server"></a>Een configuratieserver verwijderen of uitschrijven

> [!WARNING]
> Controleer het volgende voordat u begint met het ontmantelen van uw configuratieserver.
> 1. [Schakel de beveiliging](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) voor alle virtuele machines onder deze configuratieserver uit.
> 2. Alle replicatiebeleidsregels [loskoppelen](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) en [verwijderen](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) van de configuratieserver.
> 3. [Verwijder](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) alle vCenters-servers/vSphere-hosts die zijn gekoppeld aan de configuratieserver.


### <a name="delete-the-configuration-server-from-azure-portal"></a>De configuratieserver verwijderen uit Azure-portal
1. Blader in de Azure-portal naar**configuratieservers** voor **siteherstelinfrastructuur** > in het vault-menu.
2. Klik op de configuratieserver die u wilt ontmantelen.
3. Klik op de pagina Configuratieserver op de knop **Verwijderen.**
4. Klik **op Ja** om de verwijdering van de server te bevestigen.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>De configuratieserver en de afhankelijkheden ervan verwijderen
> [!TIP]
>   Als u van plan bent de configuratieserver opnieuw te gebruiken met Azure Site Recovery, u direct overgaan tot stap 4

1. Meld u aan bij de configuratieserver als beheerder.
2. Configuratiescherm openen > programma > Programma verwijderen
3. Verwijder de programma's in de volgende reeks:
   * Microsoft Azure Recovery Services-agent
   * Microsoft Azure Site Recovery Mobility Service/Master Target-server
   * Microsoft Azure-siteherstelprovider
   * Microsoft Azure-configuratieserver/processerver voor herstel van microsoft Azure-siteherstel
   * Afhankelijken van microsoft Azure-siteherstelconfiguratieserver
   * MySQL Server 5.5
4. Voer de volgende opdracht uit en opdrachtprompt voor beheerders uit.
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Een configuratieserver verwijderen of uitschrijven (PowerShell)

1. [Installeren](https://docs.microsoft.com/powershell/azure/install-Az-ps) Azure PowerShell-module
2. Inloggen op uw Azure-account met de opdracht
    
    `Connect-AzAccount`
3. Selecteer het abonnement waaronder de kluis aanwezig is

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Stel nu uw kluiscontext in
    
    ```powershell
    $Vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. Selecteer uw configuratieserver selecteren

    `$Fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. De configuratieserver verwijderen

    `Remove-AzSiteRecoveryFabric -Fabric $Fabric [-Force]`

> [!NOTE]
> De optie **-Force** in de Remove-AzSiteRecoveryFabric kan worden gebruikt om het verwijderen/verwijderen van de configuratieserver te forceren.

## <a name="renew-tlsssl-certificates"></a>TLS/SSL-certificaten verlengen
De configuratieserver heeft een ingebouwde webserver, die activiteiten van de Mobiliteitsservice, processervers en hoofddoelservers die ermee zijn verbonden orkestreert. De webserver gebruikt een TLS/SSL-certificaat om clients te verifiëren. Het certificaat verloopt na drie jaar en kan te huur worden verlengd.

### <a name="check-expiry"></a>Verlopen controleren

Voor configuratieserverimplementaties vóór mei 2016 is het verlopen van het certificaat ingesteld op één jaar. Als u een certificaat hebt dat verloopt, gebeurt het volgende:

- Wanneer de vervaldatum twee maanden of minder is, begint de service meldingen te verzenden in de portal en per e-mail (als u zich hebt geabonneerd op Azure Site Recovery-meldingen).
- Er wordt een meldingsbanner weergegeven op de pagina met de bronvan de kluis. Klik op de banner voor meer informatie.
- Als u een **knop Nu upgraden** ziet, geeft dit aan dat er een aantal onderdelen in uw omgeving zijn die niet zijn geüpgraded naar 9.4.xxxx.x of hogere versies. Upgrade onderdelen voordat u het certificaat verlengt. U niet verlengen op oudere versies.

### <a name="renew-the-certificate"></a>Het certificaat verlengen

1. Open in de kluis de**configuratieserver** **voor siteherstelinfrastructuur** > en klik op de vereiste configuratieserver.
2. De vervaldatum wordt weergegeven onder **de status Configuratieserver**
3. Klik **op Certificaten vernieuwen**. 




## <a name="common-issues"></a>Algemene problemen
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk de zelfstudies voor het instellen van noodherstel van [fysieke servers](tutorial-physical-to-azure.md) in Azure.

