---
title: Problemen met de push-installatie van Mobility Service oplossen met Azure Site Recovery
description: Problemen met de installatiefouten van Mobility Services oplossen wanneer replicatie wordt ingesloten voor herstel na noodgevallen met Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 1afd931249d4dbeda2b4b25f822837e2a564f959
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656312"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Problemen met de push-installatie van mobility-services oplossen

De installatie van de Mobility-service is een belangrijke stap om replicatie mogelijk te maken. Het succes van deze stap hangt af van het voldoen aan de vereisten en het werken met ondersteunde configuraties. De meest voorkomende storingen die u tijdens de installatie van de Mobiliteitsservice ondervinden, zijn te wijten aan:

* [Fouten in referenties/bevoegdheden](#credentials-check-errorid-95107--95108)
* [Inlogfouten](#login-failures-errorid-95519-95520-95521-95522)
* [Connectiviteitsfouten](#connectivity-failure-errorid-95117--97118)
* [Fouten in het delen van bestanden en printers](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [WMI-fouten (Windows Management Instrumentation)](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Niet-ondersteunde besturingssystemen](#unsupported-operating-systems)
* [Niet-ondersteunde opstartconfiguraties](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [VSS-installatiefouten (Volume Shadow copy Service)](#vss-installation-failures)
* [Apparaatnaam in GRUB-configuratie in plaats van apparaat UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Lvm-volumevolume (Logical Volume Manager)](#lvm-support-from-920-version)
* [Waarschuwingen voor opnieuw opstarten](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Wanneer u replicatie inschakelt, probeert Azure Site Recovery de mobiliteitsserviceagent op uw virtuele machine (VM) te installeren. Als onderdeel van dit proces probeert de configuratieserver verbinding te maken met de virtuele machine en de agent te kopiëren. Volg de stapsgewijze richtlijnen voor het oplossen van problemen om een succesvolle installatie mogelijk te maken.

## <a name="credentials-check-errorid-95107--95108"></a>Referentiescontrole (Foutid: 95107 & 95108)

Controleer of het gebruikersaccount dat tijdens de replicatie is ingeschakeld, geldig en nauwkeurig is. Azure Site Recovery vereist het **hoofdaccount** of het gebruikersaccount met **beheerdersbevoegdheden** om een push-installatie uit te voeren. Anders wordt de duwinstallatie geblokkeerd op de bronmachine.

Controleer voor Windows **(fout 95107)** of het gebruikersaccount beheerderstoegang heeft op de broncomputer, met een lokaal account of domeinaccount. Als u geen domeinaccount gebruikt, moet u het besturingselement voor rasgebruikerstoegang op de lokale computer uitschakelen.

* Ga als lid of nieuw om handmatig een registersleutel toe te voegen waarmee het besturingselement voor externe gebruikerstoegang wordt uitgeschakeld:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * Voeg een `DWORD`nieuwe:`LocalAccountTokenFilterPolicy`
  * De waarde instellen op`1`

* Voer de volgende opdracht uit om de registersleutel toe te voegen vanuit een opdrachtprompt:

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

Voor Linux **(fout 95108)** moet u de **hoofdaccount** kiezen voor een succesvolle installatie van Mobility service agent. Bovendien moeten SSH File Transfer Protocol (SFTP)-services worden uitgevoerd. Ga als bedoeld als u het SFTP-subsysteem en de wachtwoordverificatie inschakelt in het _sshd_config_ bestand:

1. Meld u aan als **rootgebruiker**.
1. Ga naar _/ etc / ssh / sshd_config bestand,_ vind de lijn die begint met `PasswordAuthentication`.
1. De opmerking van de regel `yes`ongedaan maken en de waarde wijzigen in .
1. Zoek de regel `Subsystem`die begint met en geef geen commentaar op de regel.
1. Start `sshd` de service opnieuw.

Als u de referenties van het gekozen gebruikersaccount wilt wijzigen, volgt u [deze instructies.](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)

## <a name="insufficient-privileges-failure-errorid-95517"></a>Onvoldoende bevoegdheden mislukken (ErrorID: 95517)

Wanneer de gebruiker ervoor heeft gekozen om de Mobiliteitsagent te installeren, heeft hij geen beheerdersbevoegdheden, mag de processerver voor configuratieservers/scale-outs de software van de Mobiliteitsagent niet kopiëren naar de bronmachine. Deze fout is het gevolg van een weigering van de toegang. Controleer of het gebruikersaccount beheerdersrechten heeft.

Als u de referenties van het gekozen gebruikersaccount wilt wijzigen, volgt u [deze instructies.](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)

## <a name="insufficient-privileges-failure-errorid-95518"></a>Onvoldoende bevoegdheden mislukken (ErrorID: 95518)

Wanneer de domeinvertrouwensrelatie tussen het primaire domein en het werkstation mislukt terwijl u zich probeert aan te melden bij de bronmachine, mislukt de installatie van de mobiliteitsagent met fout-ID 95518. Controleer of het gebruikersaccount dat wordt gebruikt om de mobiliteitsagent te installeren, beheerdersbevoegdheden heeft om in te loggen via het primaire domein van de bronmachine.

Als u de referenties van het gekozen gebruikersaccount wilt wijzigen, volgt u de [volgende instructies.](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Inlogfouten (Foutnummer: 95519, 95520, 95521, 95522)

In deze sectie worden referenties en inlogfoutberichten beschreven.

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Referenties van het gebruikersaccount zijn uitgeschakeld (ErrorID: 95519)

Het gebruikersaccount dat tijdens de replicatie is gekozen, is uitgeschakeld. Als u het gebruikersaccount wilt inschakelen, verwijst u naar [dit artikel](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser) of voert u de volgende opdracht uit door _de_ gebruikersnaam van tekst te vervangen door de werkelijke gebruikersnaam.

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>Referenties vergrendeld vanwege meerdere mislukte inlogpogingen (ErrorID: 95520)

Meerdere mislukte pogingen om toegang te krijgen tot een machine vergrendelen het gebruikersaccount. De storing kan te wijten zijn aan:

* Referenties die tijdens de configuratie zijn opgegeven, zijn onjuist.
* Het gebruikersaccount dat tijdens het inschakelen van replicatie is gekozen, is onjuist.

Wijzig de referenties die zijn gekozen door [deze instructies](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) op te volgen en probeer de bewerking opnieuw.

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>Aanmeldingsservers zijn niet beschikbaar op de bronmachine (Foutid: 95521)

Deze fout treedt op wanneer de aanmeldingsservers niet beschikbaar zijn op de bronmachine. Als aanmeldingsservers niet beschikbaar zijn, mislukken aanmeldingsaanvragen en kan de mobiliteitsagent niet worden geïnstalleerd. Voor een succesvolle login moet u ervoor zorgen dat aanmeldingsservers beschikbaar zijn op de bronmachine en de Netlogon-service starten. Zie [Windows-aanmeldingsscenario's voor](/windows-server/security/windows-authentication/windows-logon-scenarios)meer informatie .

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Aanmeldingsservice wordt niet uitgevoerd op de bronmachine (Foutid: 95522)

De inlogservice wordt niet uitgevoerd op uw bronmachine en veroorzaakt het inlogverzoek. De Mobiliteitsagent kan niet worden geïnstalleerd. Als u de fout wilt oplossen, gebruikt `Netlogon` u een van de volgende methoden om de service op de bronmachine te starten:

* Als u `Netlogon` de service wilt starten `net start Netlogon`vanuit een opdrachtprompt, voert u de opdracht uit .
* Start de `Netlogon` service vanuit Taakbeheer.

## <a name="connectivity-failure-errorid-95117--97118"></a>Verbindingsfout (Fout-id: 95117 & 97118)

Configuratieserver/scale-out processerver probeert verbinding te maken met de bron-VM om de Mobility-agent te installeren. Deze fout treedt op wanneer de bronmachine niet bereikbaar is omdat er problemen zijn met de netwerkverbinding.

Ga als een besluit over de fout:

* Zorg ervoor dat u uw bronmachine pingen vanaf de configuratieserver. Als u de scale-outprocesserver hebt gekozen tijdens het inschakelen van replicatie, moet u ervoor zorgen dat u uw bronmachine pingen vanaf de processerver.

* Gebruik vanaf de opdrachtregel `Telnet` van de bronservermachine de configuratieserver of scale-out processerver op HTTPS-poort 135, zoals in de volgende opdracht wordt weergegeven. Met deze opdracht wordt gecontroleerd of er problemen zijn met de netwerkverbinding of problemen met het blokkeren van de firewallpoort.

  `telnet <CS/ scale-out PS IP address> <135>`

* Bovendien, voor een Linux VM:
  * Controleer of de nieuwste OpenSSH-, OpenSSH-server- en OpenSSL-pakketten zijn geïnstalleerd.
  * Controleer en zorg ervoor dat Secure Shell (SSH) is ingeschakeld en op poort 22 draait.
  * SFTP-services moeten worden uitgevoerd. Ga als bedoeld als _sshd_config-bestand_ in om sftp-subsysteem- en wachtwoordverificatie in te schakelen:

    1. Meld u aan als **rootgebruiker**.
    1. Ga naar _/ etc / ssh / sshd_config_ `PasswordAuthentication`bestand, vind de lijn die begint met .
    1. De opmerking van de regel `yes`ongedaan maken en de waarde wijzigen in .
    1. Zoek de regel `Subsystem`die begint met en de regel ongedaan maken
    1. Start `sshd` de service opnieuw.

* Een verbindingspoging kan zijn mislukt als er na verloop van tijd geen goede antwoorden zijn of als een gevestigde verbinding is mislukt omdat een verbonden host niet heeft gereageerd.
* Het kan een verbindings-/netwerk-/domeingerelateerd probleem zijn. Dit kan ook komen doordat dns-naam probleem of tcp-poortuitputtingsprobleem oplost. Controleer of er dergelijke bekende problemen in uw domein.

## <a name="connectivity-failure-errorid-95523"></a>Verbindingsfout (Fout-id: 95523)

Deze fout treedt op wanneer het netwerk dat de bronmachine zich bevindt niet is gevonden, mogelijk is verwijderd of niet meer beschikbaar is. De enige manier om de fout op te lossen is ervoor te zorgen dat het netwerk bestaat.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Services voor het delen van bestanden en printers controleren (Fout-id: 95105 & 95106)

Controleer na een connectiviteitscontrole of de service voor het delen van bestanden en printers is ingeschakeld op uw virtuele machine. Deze instellingen zijn vereist om de Mobiliteitsagent naar de bronmachine te kopiëren.

Voor **Windows 2008 R2 en eerdere versies:**

* Als u het delen van bestanden en afdrukken via Windows Firewall wilt inschakelen,
  1. Open **Configuratiescherm** > **Systeem en Beveiliging** > **Windows Firewall**. Selecteer in het linkerdeelvenster De optie **Geavanceerde instellingen** > **Binnenkomende regels** in de consolestructuur.
  1. Zoek regels Bestand en Printer sharing (NB-Session-In) en File and Printer Sharing (SMB-In).
  1. Klik voor elke regel met de rechtermuisknop op de regel en klik vervolgens op **Regel inschakelen**.

* Ga als het gaat om het delen van bestanden met groepsbeleid:
  1. Ga naar **Start,** typ `gpmc.msc` en zoek.
  1. Open in het navigatiedeelvenster de volgende mappen:**Gebruikersconfiguratiebeheersjablonen** > **Windows Components** > voor**gebruikersconfiguratie** > voor lokale **computers** > Windows Components**Network Sharing**.
  1. Dubbelklik in het detailvenster op **Voorkomen dat gebruikers bestanden delen in hun profiel.**

     Als u de instelling Groepsbeleid wilt uitschakelen en de mogelijkheid van de gebruiker om bestanden te delen wilt inschakelen, selecteert **u Uitgeschakeld**.

  1. Selecteer **OK** om uw wijzigingen op te slaan.

  Zie [Bestandsdelen inschakelen of uitschakelen met groepsbeleid](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))voor meer informatie .

Als u het delen van bestanden en printers voor latere versies van Windows of Linux wilt inschakelen, volgt u de instructies in [De mobiliteitsservice installeren voor noodherstel van Vm's en fysieke servers .](vmware-azure-install-mobility-service.md)

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Configuratiecontrole van Windows Management Instrumentation (WMI) (foutcode: 95103)

Nadat bestands- en printerservices zijn gecontroleerd, schakelt u de WMI-service in voor privé-, openbare en domeinprofielen via de firewall. Deze instellingen zijn vereist om de uitvoering op afstand op de bronmachine te voltooien.

Wmi inschakelen:

1. Ga naar **Beveiliging van het Configuratiescherm** > **en** selecteer Windows **Firewall**.
1. Selecteer **Instellingen wijzigen** en selecteer vervolgens het tabblad **Uitzonderingen.**
1. Schakel in het venster **Uitzonderingen** het selectievakje voor WMI (Windows Management Instrumentation) in om WMI-verkeer via de firewall in te schakelen.

U WMI-verkeer via de firewall ook inschakelen via de opdrachtprompt met de volgende opdracht:

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

Andere WMI-artikelen voor het oplossen van problemen kunnen worden gevonden in de volgende artikelen.

* [BasisWMI-tests](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI-probleemoplossing](/windows/win32/wmisdk/wmi-troubleshooting)
* [Problemen met WMI-scripts en WMI-services oplossen](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>Niet-ondersteunde besturingssystemen

Een andere veel voorkomende reden voor een storing kan zijn vanwege een niet-ondersteund besturingssysteem. Gebruik een ondersteund besturingssysteem en kernelversie voor een succesvolle installatie van de Mobility-service. Vermijd het gebruik van privépatches.

Zie het [ondersteuningsmatrixdocument](vmware-physical-azure-support-matrix.md#replicated-machines)om de lijst met besturingssystemen en kernelversies te bekijken die worden ondersteund door Azure Site Recovery.

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Niet-ondersteunde configuratie van opstartschijven (Fout-id: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>Opstart- en systeempartities / volumes zijn niet dezelfde schijf (ErrorID: 95309)

Vóór de 9.20-versie waren opstart- en systeempartities/volumes op verschillende schijven een niet-ondersteunde configuratie. Te beginnen met de [9.20 versie,](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)deze configuratie wordt ondersteund.

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>De opstartschijf is niet beschikbaar (Foutid: 95310)

Een virtuele machine zonder opstartschijf kan niet worden beschermd. Een opstartschijf zorgt voor een soepel herstel van een virtuele machine tijdens een failoveroperatie. Als er geen opstartschijf is, wordt de machine niet opgestart na een failover. Zorg ervoor dat de virtuele machine een opstartschijf bevat en probeer de bewerking opnieuw. Ook worden meerdere opstartschijven op dezelfde machine niet ondersteund.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Meerdere opstartschijven aanwezig op de bronmachine (ErrorID: 95311)

Een virtuele machine met meerdere opstartschijven is geen [ondersteunde configuratie.](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Systeempartitie op meerdere schijven (Foutid: 95313)

Vóór de 9.20-versie was een hoofdpartitie of volume-instelling op meerdere schijven een niet-ondersteunde configuratie. Te beginnen met de [9.20 versie,](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)deze configuratie wordt ondersteund.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Beveiliging inschakelen mislukt als apparaatnaam die in de GRUB-configuratie wordt genoemd in plaats van UUID (ErrorID: 95320)

### <a name="possible-cause"></a>Mogelijke oorzaak

De Configuratiebestanden van Grand Unified Bootloader (GRUB)_(/boot/grub/menu.lst_, _boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_, of _/etc/default/grub)_ kunnen de waarde voor de parameters **root** bevatten en **hervatten** als de werkelijke apparaatnamen in plaats van een universeel unieke id (UUID). Site Recovery mandaten de UUID-aanpak als het apparaat namen kunnen veranderen over reboot van de VM. De VM komt bijvoorbeeld niet online met dezelfde naam bij failover en dat resulteert in problemen.

Bijvoorbeeld:

- De volgende regel is uit het GRUB-bestand _/boot/grub2/grub.cfg:_

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- De volgende regel is van het GRUB-bestand _/boot/grub/menu.lst:_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> De GRUB-regels bevatten werkelijke apparaatnamen voor de parameters **die worden gebruikt** en **hervatten** in plaats van de UUID.

### <a name="how-to-fix"></a>Hoe op te lossen

De apparaatnamen moeten worden vervangen door de bijbehorende UUID.

1. Zoek de UUID van het apparaat `blkid \<device name>`door de opdracht uit te voeren.

   Bijvoorbeeld:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Vervang nu de naam van het apparaat `root=UUID=\<UUID>`door de UUID in het formaat zoals . Bijvoorbeeld, als we de apparaatnamen vervangen door UUID voor root en cv parameter genoemd in de bestanden _/ boot/grub2/grub.cfg_, _/ boot/grub2/grub.cfg_, of _/etc/default/grub_ dan zien de lijnen in de bestanden eruit als de volgende regel:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Start de beveiliging opnieuw.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Installatie Mobiliteitsservice voltooid met waarschuwing om opnieuw op te starten (ErrorID: 95265 & 95266)

Site Recovery Mobility service heeft vele componenten, waarvan er een filterdriver wordt genoemd. Het filterstuurprogramma wordt alleen tijdens een herstart van het systeem in het systeemgeheugen geladen. Filterstuurprogrammafixes kunnen alleen worden gerealiseerd wanneer een nieuw filterstuurprogramma wordt geladen op het moment van een herstart van het systeem.

> [!IMPORTANT]
> Dit is een waarschuwing en bestaande replicatie werkt zelfs na de nieuwe agentupdate. U ervoor kiezen om opnieuw op te starten wanneer u de voordelen van een nieuw filterstuurprogramma wilt, maar als u niet opnieuw opstart, blijft het oude filterstuurprogramma werken. Dus, na een update zonder een reboot, met uitzondering van de filter driver, **voordelen van andere verbeteringen en fixes in Mobility service worden gerealiseerd**. Hoewel aanbevolen, is het niet verplicht om opnieuw op te starten na elke upgrade. Voor informatie over wanneer een reboot verplicht is, stelt u de upgradesectie [Opnieuw opstarten na de upgrade van de mobiliteitsservice](service-updates-how-to.md#reboot-after-mobility-service-upgrade) in Service-updates in Azure Site Recovery in.

> [!TIP]
>Zie de [ondersteuning voor het nieuwste besturingssysteem/de beste](service-updates-how-to.md#support-for-latest-operating-systemskernels) procedures voor het plannen van upgrades tijdens uw onderhoudsvenster in Azure Site Recovery.

## <a name="lvm-support-from-920-version"></a>LVM-ondersteuning vanaf 9.20 versie

Vóór de 9.20-versie werd Logische Volume Manager (LVM) alleen voor gegevensschijven ondersteund. De `/boot` partitie moet zich op een schijfpartitie begeven en geen LVM-volume.

Vanaf de [9.20 versie](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)wordt de [OS-schijf op LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) ondersteund.

## <a name="insufficient-space-errorid-95524"></a>Onvoldoende ruimte (Foutid: 95524)

Wanneer de Mobiliteitsagent naar de bronmachine wordt gekopieerd, is ten minste 100 MB vrije ruimte vereist. Zorg ervoor dat uw bronmachine de vereiste hoeveelheid vrije ruimte heeft en probeer de bewerking opnieuw.

## <a name="vss-installation-failures"></a>VSS-installatiefouten

De Installatie van de Volume Shadow copy Service (VSS) maakt deel uit van de installatie van de Mobility agent. Deze service wordt in het proces gebruikt om toepassingsconsistente herstelpunten te genereren. Storingen tijdens de VSS-installatie kunnen optreden als gevolg van meerdere redenen. Als u de exacte fouten wilt identificeren, raadpleegt u _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_. Enkele van de veelvoorkomende fouten en de oplossingsstappen worden in de volgende sectie gemarkeerd.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS-fout -2147023170 [0x800706BE] - exitcode 511

Dit probleem wordt meestal gezien wanneer antivirussoftware de bewerkingen van Azure Site Recovery-services blokkeert.

Los dit probleem als volgt op:

1. Bekijk de lijst met uitsluitingen van [mappen van antivirusprogramma](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
1. Volg de richtlijnen die door uw antivirusprovider zijn gepubliceerd om de registratie van DLL in Windows te deblokkeren.

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS-fout 7 [0x7] - exitcode 511

Deze fout is een runtime-fout die wordt veroorzaakt omdat er onvoldoende geheugen is om VSS te installeren. Verhoog de schijfruimte voor een succesvolle voltooiing van deze bewerking.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS-fout -2147023824 [0x80070430] - exitcode 517

Deze fout treedt op wanneer de Azure Site Recovery VSS Provider-service is [gemarkeerd voor verwijdering.](/previous-versions/ms838153(v=msdn.10)) Probeer VSS handmatig op de bronmachine te installeren door de volgende opdracht uit te voeren:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS-fout -2147023841 [0x8007041F] - exitcode 512

Deze fout treedt op wanneer de Azure Site Recovery VSS Provider-servicedatabase is [vergrendeld.](/previous-versions/ms833798(v=msdn.10)) Probeer VSS handmatig op de bronmachine te installeren door de volgende opdracht uit te voeren vanuit een opdrachtprompt:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Wanneer er een storing optreedt, controleert u of een antivirusprogramma of andere services vast zitten in **een beginstatus.** Een proces in een **beginstatus** kan het slot op databaseservices behouden. Het zal leiden tot storingen in het installeren van VSS provider. Controleer of er geen service in **de beginstatus** staat en probeer de bovenstaande bewerking opnieuw.

### <a name="vss-exit-code-806"></a>VSS-exitcode 806

Deze fout treedt op wanneer het gebruikersaccount dat voor `CSScript` de installatie wordt gebruikt, geen machtigingen heeft om de opdracht uit te voeren. Geef de benodigde machtigingen aan het gebruikersaccount om het script uit te voeren en de bewerking opnieuw te proberen.

### <a name="other-vss-errors"></a>Andere VSS-fouten

Probeer de VSS-providerservice handmatig op de bronmachine te installeren door de volgende opdracht uit te voeren vanuit een opdrachtprompt:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>VSS-fout - 0x8004E00F

Deze fout treedt meestal op tijdens de installatie `DCOM` van `DCOM` de mobiliteitsagent vanwege problemen in en bevindt zich in een kritieke toestand.

Gebruik de volgende procedure om de oorzaak van de fout te bepalen.

### <a name="examine-the-installation-logs"></a>De installatielogboeken onderzoeken

1. Open het installatielogboek op _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_.
2. De aanwezigheid van de volgende fout geeft dit probleem aan:

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

Ga als ander op zoek naar het probleem:

Neem contact op met het [Microsoft Windows-platformteam](https://aka.ms/Windows_Support) om hulp te krijgen bij het oplossen van het DCOM-probleem.

Wanneer het DCOM-probleem is opgelost, installeert u de Azure Site Recovery VSS-provider handmatig met de volgende opdracht van een opdrachtprompt:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Als consistentie van de toepassing niet essentieel is voor uw vereisten voor noodherstel, u de installatie van de VSS-provider omzeilen.

Ga als het gaat om het omzeilen van de installatie van azure site recovery VSS-provider en het handmatig installeren van de installatie van Azure Site Recovery VSS Provider-post:

1. Installeer de Mobiliteitsservice. De installatie mislukt in de stap: **Installatieconfiguratie na installeren**.
1. Ga als een te werk om de VSS-installatie te omzeilen:
   1. Open de installatiemap azure site recovery mobility service op:

      _C:\Program Files (x86)\Microsoft Azure Site Recovery\agent_

   1. Wijzig de installatiescripts van Azure Site Recovery VSS Provider _InMageVSSProvider_Install_ en _InMageVSSProvider_Uninstall.cmd_ om altijd te slagen door de volgende regels toe te voegen:

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. Doe een handmatige installatie van de Mobiliteitsagent.
1. Wanneer de installatie slaagt en naar de volgende stap gaat, verwijdert **u**de regels die u hebt toegevoegd.
1. Als u de VSS-provider wilt installeren, opent u een opdrachtprompt als beheerder en voert u de volgende opdracht uit:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Controleer of de Azure Site Recovery VSS-provider is geïnstalleerd als service in Windows Services. Open de Component Service MMC om te controleren of de VSS-provider wordt vermeld.
1. Als de installatie van de VSS-provider blijft mislukken, werkt u met technische ondersteuning om de fouten in de Cryptographic Application Programming Interface (CAPI2) op te lossen.

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Installatie VSS-provider mislukt omdat de clusterservice is ingeschakeld op niet-clustermachine

Dit probleem zorgt ervoor dat de installatie van Azure Site Recovery Mobility Agent mislukt tijdens de installatie van Azure Site Recovery VSS Provider. De fout is omdat er `COM+` een probleem met dat voorkomt dat de VSS provider installatie.

### <a name="to-identify-the-issue"></a>Om het probleem te identificeren

In het logboek op de configuratieserver op _C:\ProgramData\ASRSetupLogs\UploadedLogs-datum>\<UA_InstallLogFile.log_ vindt u de volgende uitzondering:

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

Ga als ander op zoek naar het probleem:

1. Controleer of deze machine een niet-clustermachine is en of de clusteronderdelen niet worden gebruikt.
1. Als de onderdelen niet worden gebruikt, verwijdert u de clusteronderdelen uit de machine.

## <a name="drivers-are-missing-on-the-source-server"></a>Stuurprogramma's ontbreken op de bronserver

Als de installatie van de Mobiliteitsagent mislukt, controleert u de logboeken onder _C:\ProgramData\ASRSetupLogs_ om te bepalen of sommige van de vereiste stuurprogramma's ontbreken in sommige controlesets.

Ga als ander op zoek naar het probleem:

1. Met behulp van `regedit.msc`een registereditor zoals , open het register.
1. Open `HKEY_LOCAL_MACHINE\SYSTEM` het knooppunt.
1. Zoek `SYSTEM` in het knooppunt de besturingssets.
1. Open elke besturingselementset en controleer of de volgende Windows-stuurprogramma's aanwezig zijn:

   * Atapi
   * Vmbus (Vmbus)
   * Storflt Storflt
   * Storvsc Storvsc
   * Intelide (Intelide)

1. Installeer ontbrekende stuurprogramma's opnieuw.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie](vmware-azure-tutorial.md) over het instellen van noodherstel voor VMware VM's.
