---
title: Problemen met de push-installatie van Mobility Service oplossen met Azure Site Recovery f
description: Problemen met de installatiefouten van Mobility Services oplossen wanneer replicatie wordt ingesloten voor herstel na noodgevallen met Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 09/11/2019
ms.openlocfilehash: 3646499ad2104566cb82f3f26c6b55d05f84dc7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953780"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Problemen met de push-installatie van Mobility Service oplossen 

De installatie van mobility service is een belangrijke stap tijdens Replicatie inschakelen. Het succes van deze stap hangt uitsluitend af van het voldoen aan de vereisten en het werken met ondersteunde configuraties. De meest voorkomende storingen die u tegenkomt tijdens de installatie van de Mobiliteitsservice zijn te wijten aan:

* [Fouten in referenties/bevoegdheden](#credentials-check-errorid-95107--95108)
* [Inlogfouten](#login-failures-errorid-95519-95520-95521-95522)
* [Connectiviteitsfouten](#connectivity-failure-errorid-95117--97118)
* [Fouten in het delen van bestanden en printers](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [WMI-fouten](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Niet-ondersteunde besturingssystemen](#unsupported-operating-systems)
* [Niet-ondersteunde opstartconfiguraties](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [VSS-installatiefouten](#vss-installation-failures)
* [Apparaatnaam in GRUB-configuratie in plaats van apparaat UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [LVM-volume](#lvm-support-from-920-version)
* [Waarschuwingen voor opnieuw opstarten](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Wanneer u replicatie inschakelt, probeert Azure Site Recovery de medewerker voor de installatievan de mobiliteitsservice op uw virtuele machine te pushen. Als onderdeel hiervan probeert de configuratieserver verbinding te maken met de virtuele machine en de agent te kopiëren. Als u een succesvolle installatie wilt inschakelen, volgt u de volgende stapsgewijze richtlijnen voor het oplossen van problemen.

## <a name="credentials-check-errorid-95107--95108"></a>Referentiescontrole (Foutid: 95107 & 95108)

* Controleer of het gebruikersaccount dat tijdens de replicatie is **ingeschakeld, geldig en nauwkeurig**is.
* Azure Site Recovery vereist **ROOT-account** of gebruikersaccount met **beheerdersbevoegdheden** om push-installatie uit te voeren. Anders wordt de push-installatie geblokkeerd op de bronmachine.
  * Controleer voor Windows **(fout 95107)** of het gebruikersaccount beheerderstoegang heeft, lokaal of domein, op de bronmachine.
  * Als u geen domeinaccount gebruikt, moet u het besturingselement voor externe gebruikerstoegang op de lokale computer uitschakelen.
    * Als u extern besturingselement voor gebruikerstoegang wilt uitschakelen, voegt u onder HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System registry key een nieuwe DWORD: LocalAccountTokenFilterPolicy toe. Stel de waarde in op 1. Voer de volgende opdracht uit opdrachtprompt uit om deze stap uit te voeren:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Voor Linux (**fout 95108**), moet u kiezen voor de root-account voor een succesvolle installatie van mobiliteitsagent. Daarnaast moeten SFTP-services worden uitgevoerd. Ga als bedoeld als u sftp-subsysteem- en wachtwoordverificatie inschakelt in het sshd_config bestand:
    1. Meld u aan als rootgebruiker.
    2. Ga naar /etc/ssh/sshd_config bestand, zoek de regel die begint met PasswordAuthentication.
    3. Geef geen commentaar op de regel en wijzig de waarde in ja.
    4. Zoek de regel die begint met Subsysteem en geef geen commentaar op de regel.
    5. Start de service sshd opnieuw.

Als u de referenties van het gekozen gebruikersaccount wilt wijzigen, volgt u de [hier](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)gegeven instructies.

## <a name="insufficient-privileges-failure-errorid-95517"></a>Onvoldoende bevoegdheden mislukken (ErrorID: 95517)

Wanneer de gebruiker die ervoor heeft gekozen om een mobiliteitsagent te installeren geen beheerdersbevoegdheden heeft, mag de processerver configuration server/scale-out process server de software van de mobiliteitsagent niet kopiëren naar de bronmachine. Deze fout is dus een gevolg van geweigerde toegang. Controleer of het gebruikersaccount beheerdersrechten heeft.

Als u de referenties van het gekozen gebruikersaccount wilt wijzigen, volgt u de [hier](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)gegeven instructies.

## <a name="insufficient-privileges-failure-errorid-95518"></a>Onvoldoende bevoegdheden mislukken (ErrorID: 95518)

Wanneer de domeinvertrouwensrelatie tussen het primaire domein en het werkstation mislukt terwijl u zich probeert aan te melden bij de bronmachine, mislukt de installatie van mobiliteitsagenten met fout-ID 95518. Zorg er dus voor dat het gebruikersaccount dat wordt gebruikt om een mobiliteitsagent te installeren, beheerdersbevoegdheden heeft om in te loggen via het primaire domein van de bronmachine.

Als u de referenties van het gekozen gebruikersaccount wilt wijzigen, volgt u de [hier](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)gegeven instructies.

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Inlogfouten (Fout-id: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Referenties van het gebruikersaccount zijn uitgeschakeld (ErrorID: 95519)

Het gebruikersaccount dat tijdens Replicatie inschakelen is gekozen, is uitgeschakeld. Als u het gebruikersaccount wilt inschakelen, raadpleegt u [het](https://aka.ms/enable_login_user) artikel hier of voert u de volgende opdracht uit door *de* gebruikersnaam van tekst te vervangen door de werkelijke gebruikersnaam.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>Referenties vergrendeld vanwege meerdere mislukte inlogpogingen (ErrorID: 95520)

Meerdere mislukte pogingen om toegang te krijgen tot een machine vergrendelen het gebruikersaccount. De storing kan te wijten zijn aan:

* Referenties die zijn verstrekt tijdens de configuratie-instelling zijn onjuist of
* Het gebruikersaccount dat tijdens Replicatie inschakelen is, is verkeerd

Dus, wijzigen van de referenties gekozen door het volgen van de instructies [hier](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) gegeven en opnieuw proberen de operatie na enige tijd.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>Aanmeldingsservers zijn niet beschikbaar op de bronmachine (ErrorID: 95521)

Deze fout treedt op wanneer de aanmeldingsservers niet beschikbaar zijn op de bronmachine. Onbeschikbaarheid van logon servers zal leiden tot het mislukken van login aanvraag en dus mobiliteitsagent kan niet worden geïnstalleerd. Zorg ervoor dat logonservers beschikbaar zijn op de bronmachine en voor een succesvolle aanmeldingsservice. Zie voor gedetailleerde instructies de KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) Err Msg: Er zijn momenteel geen aanmeldingsservers beschikbaar.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Aanmeldingsservice wordt niet uitgevoerd op de bronmachine (Foutid: 95522)

De inlogservice wordt niet uitgevoerd op uw bronmachine en veroorzaakt het uitgevallen van aanmeldingsverzoek. Mobiliteitsagent kan dus niet worden geïnstalleerd. Als u dit wilt oplossen, moet u ervoor zorgen dat de aanmeldingsservice wordt uitgevoerd op de bronmachine voor een succesvolle aanmelding. Als u de aanmeldingsservice wilt starten, voert u de opdracht 'net start Logon' uit vanaf opdrachtprompt of start u de service 'NetLogon' van taakbeheer.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Verbindingsfout (Fout-id: 95117 & 97118)**

Configuratieserver/ scale-out processerver probeert verbinding te maken met de bron-VM om Mobility-agent te installeren. Deze fout treedt op wanneer de bronmachine niet bereikbaar is vanwege problemen met de netwerkverbinding. Om op te lossen,

* Zorg ervoor dat u uw bronmachine pingen vanaf de configuratieserver. Als u tijdens het inschakelen van replicatie een scale-outprocesserver hebt gekozen, moet u ervoor zorgen dat u uw bronmachine vanaf de processerver pingen.
  * Gebruik Telnet vanaf de opdrachtregel sourceserver om de configuratieserver/ scale-outprocesserver met https-poort (135) te pingen, zoals hieronder wordt weergegeven om te zien of er problemen zijn met de netwerkconnectiviteit of problemen met het blokkeren van firewallpoorten.

     `telnet <CS/ scale-out PS IP address> <135>`
* Bovendien, voor **Linux VM**,
  * Controleer of de nieuwste openssh-, openssh-server en openssl-pakketten zijn geïnstalleerd.
  * Controleer en zorg ervoor dat Secure Shell (SSH) is ingeschakeld en op poort 22 draait.
  * SFTP-services moeten worden uitgevoerd. SFTP-subsysteem- en wachtwoordverificatie inschakelen in het sshd_config bestand,
    * Meld u aan als rootgebruiker.
    * Ga naar /etc/ssh/sshd_config bestand, zoek de regel die begint met PasswordAuthentication.
    * Geef de opmerking van de regel ongedaan en wijzig de waarde in ja
    * Zoek de regel die begint met Subsysteem en geef geen commentaar op de regel
    * Start de service sshd opnieuw.
* Een verbindingspoging kan zijn mislukt als er na verloop van tijd geen goede reactie is of als de verbinding is mislukt omdat de verbonden host niet heeft gereageerd.
* Het kan een probleem zijn met connectiviteit/netwerk/domein. Het kan ook te wijten zijn aan DNS-naam oplossen probleem of TCP-poort uitputting probleem. Controleer of er dergelijke bekende problemen in uw domein.

## <a name="connectivity-failure-errorid-95523"></a>Verbindingsfout (Fout-id: 95523)

Deze fout treedt op wanneer het netwerk waarin de bronmachine zich bevindt, niet wordt gevonden of mogelijk is verwijderd of niet meer beschikbaar is. De enige manier om de fout op te lossen is door ervoor te zorgen dat het netwerk bestaat.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Services voor het delen van bestanden en printers controleren (Fout-id: 95105 & 95106)

Controleer na de controle van de verbinding of de service voor het delen van bestanden en printers is ingeschakeld op uw virtuele machine. Deze instellingen zijn vereist om de Mobiliteitsagent naar de bronmachine te kopiëren.

Voor **windows 2008 R2 en eerdere versies**,

* Als u het delen van bestanden en afdrukken via Windows Firewall wilt inschakelen,
  * Open het configuratiescherm -> Systeem en beveiliging -> Windows Firewall -> in het linkerdeelvenster, klik op Geavanceerde instellingen > klik op Binnenkomende regels in de consolestructuur.
  * Zoek regels Bestand en Printer sharing (NB-Session-In) en File and Printer Sharing (SMB-In). Klik voor elke regel met de rechtermuisknop op de regel en klik vervolgens op **Regel inschakelen**.
* Het delen van bestanden met groepsbeleid inschakelen,
  * Ga naar Start, typ gpmc.msc en zoek.
  * Open in het navigatiedeelvenster de volgende mappen: Lokaal computerbeleid, gebruikersconfiguratie, beheersjablonen, Windows-componenten en Delen van netwerken.
  * Dubbelklik in het detailvenster op **Voorkomen dat gebruikers bestanden delen in hun profiel.** Als u de instelling Groepsbeleid wilt uitschakelen en de mogelijkheid van de gebruiker om bestanden te delen wilt inschakelen, klikt u op Uitgeschakeld. Klik op OK om uw wijzigingen op te slaan. Zie [Bestandsdelen inschakelen of uitschakelen met groepsbeleid](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))voor meer informatie .

Volg **voor latere versies**de instructies in De [mobiliteitsservice installeren voor noodherstel van Vm's en fysieke servers](vmware-azure-install-mobility-service.md) om het delen van bestanden en printers mogelijk te maken.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Configuratiecontrole van Windows Management Instrumentation (WMI) (foutcode: 95103)

Nadat bestands- en printerservices zijn gecontroleerd, schakelt u WMI-service in voor privé-, openbare en domeinprofielen via firewall. Deze instellingen zijn vereist om de uitvoering op afstand op de bronmachine te voltooien. Om in te schakelen,

* Ga naar het Configuratiescherm, klik op Beveiliging en klik vervolgens op Windows Firewall.
* Klik op Instellingen wijzigen en klik vervolgens op het tabblad Uitzonderingen.
* Schakel in het venster Uitzonderingen het selectievakje voor WMI (Windows Management Instrumentation) in om WMI-verkeer via de firewall in te schakelen. 

U wmi-verkeer ook via de firewall inschakelen bij de opdrachtprompt. De volgende opdracht gebruiken`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Andere WMI-artikelen voor het oplossen van problemen kunnen worden gevonden in de volgende artikelen.

* [BasisWMI-tests](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI-probleemoplossing](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Problemen met WMI-scripts en WMI-services oplossen](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Niet-ondersteunde besturingssystemen

Een andere meest voorkomende reden voor een storing kan te wijten zijn aan niet-ondersteunde besturingssysteem. Zorg ervoor dat u op de ondersteunde System/Kernel-versie staat voor een succesvolle installatie van Mobility-service. Vermijd het gebruik van privépatch.
Als u de lijst met besturingssystemen en kernelversies wilt bekijken die worden ondersteund door Azure Site Recovery, raadpleegt u ons [ondersteuningsmatrixdocument](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Niet-ondersteunde configuratie van opstartschijven (Fout-id: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Opstart- en systeempartities/volumes zijn niet dezelfde schijf (ErrorID: 95309)

Vóór 9.20 versie, boot en systeempartities / volumes op verschillende schijven was een niet-ondersteunde configuratie. Vanaf [9.20 versie](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), deze configuratie wordt ondersteund. Gebruik de nieuwste versie voor deze ondersteuning.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>De opstartschijf is niet beschikbaar (Foutid: 95310)

Een virtuele machine zonder opstartschijf kan niet worden beveiligd. Dit is om te zorgen voor een soepel herstel van de virtuele machine tijdens failover werking. Als u geen opstartschijf hebt, kan de machine niet worden opgestart na een failover. Zorg ervoor dat de virtuele machine opstartschijf bevat en probeer de bewerking opnieuw. Houd er ook rekening mee dat meerdere opstartschijven op dezelfde machine niet worden ondersteund.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Meerdere opstartschijven aanwezig op de bronmachine (ErrorID: 95311)

Een virtuele machine met meerdere opstartschijven is geen [ondersteunde configuratie.](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Systeempartitie op meerdere schijven (Foutid: 95313)

Vóór 9.20 versie, root partitie of volume gelegd op meerdere schijven was een niet-ondersteunde configuratie. Vanaf [9.20 versie](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), deze configuratie wordt ondersteund. Gebruik de nieuwste versie voor deze ondersteuning.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Beveiliging inschakelen mislukt als apparaatnaam die in de GRUB-configuratie wordt genoemd in plaats van UUID (ErrorID: 95320)

**Mogelijke oorzaak:** </br>
De GRUB configuratiebestanden ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" of "/etc/default/grub") kunnen de waarde voor de parameters **wortel** bevatten en **hervatten** als de werkelijke apparaatnamen in plaats van UUID. Site Recovery mandaten UUID aanpak als apparaten naam kan veranderen over reboot van de VM als VM mag niet komen met dezelfde naam op failover resulteert in problemen. Bijvoorbeeld: </br>


- De volgende regel is van de GRUB bestand **/ boot / grub2 /grub.cfg**. <br>
  *linux /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2** ${extra_cmdline} **cv=/dev/sda1** splash=stille stille showopts*


- De volgende regel is van het GRUB-bestand **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

Als u de bovenstaande vettekenreeks waarneemt, heeft GRUB werkelijke apparaatnamen voor de parameters "root" en "hervatten" in plaats van UUID.
 
**Hoe vast te stellen:**<br>
De apparaatnamen moeten worden vervangen door de bijbehorende UUID.<br>


1. Zoek de UUID van het apparaat door \<de opdracht 'blkid-apparaatnaam>' uit te voeren. Bijvoorbeeld:<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. Vervang nu de naam van het apparaat door de UUID\<in het formaat als "root=UUID= UUID>". Bijvoorbeeld, als we de apparaatnamen vervangen door UUID voor root en cv parameter hierboven vermeld in de bestanden "/boot/grub2/grub.cfg", "/boot/grub2/grub.cfg" of "/etc/default/grub: dan zien de lijnen in de bestanden eruit. <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **cv=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
3. De beveiliging opnieuw starten

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Installatie Mobiliteitsservice voltooid met waarschuwing om opnieuw op te starten (ErrorID: 95265 & 95266)

Site Recovery mobiliteitsservice heeft vele componenten, waarvan er een filterdriver wordt genoemd. Filterstuurprogramma wordt alleen in het systeemgeheugen geladen op een moment dat het systeem opnieuw wordt opgestart. Dit betekent dat de filterdriverfixes alleen kunnen worden gerealiseerd wanneer een nieuw filterstuurprogramma wordt geladen; wat alleen kan gebeuren op het moment van het opnieuw opstarten van het systeem.

**Houd er rekening mee** dat dit een waarschuwing is en dat de bestaande replicatie ook na de update van de nieuwe agent werkt. U ervoor kiezen om opnieuw op te starten wanneer u de voordelen van een nieuw filterstuurprogramma wilt krijgen, maar als u het oude filterstuurprogramma niet opnieuw opstart, blijft het werken. Dus, na een update zonder reboot, afgezien van filter driver, **voordelen van andere verbeteringen en oplossingen in de mobiliteitsservice wordt gerealiseerd.** Dus, hoewel aanbevolen, is het niet verplicht om opnieuw op te starten na elke upgrade. Voor informatie over wanneer een reboot verplicht is, stelt u de sectie Reboot van de [bronmachine na de upgrade van de mobiliteitsagent](https://aka.ms/v2a_asr_reboot) in Service-updates in Azure Site Recovery in.

> [!TIP]
>Zie de [ondersteuning voor de nieuwste OS/kernelversies](https://aka.ms/v2a_asr_upgrade_practice) in Service-updates in Azure Site Recovery voor aanbevolen procedures voor het plannen van upgrades tijdens uw onderhoudsvenster.

## <a name="lvm-support-from-920-version"></a>LVM-ondersteuning vanaf 9.20 versie

Vóór 9.20 versie, lvm werd ondersteund voor data schijven alleen. /boot moet op een schijfpartitie staan en mag geen LVM-volume zijn.

Vanaf [9.20 versie](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), [OS schijf op LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) wordt ondersteund. Gebruik de nieuwste versie voor deze ondersteuning.

## <a name="insufficient-space-errorid-95524"></a>Onvoldoende ruimte (Foutid: 95524)

Wanneer mobiliteitsagent wordt gekopieerd naar de bronmachine, is ten minste 100 MB vrije ruimte vereist. Zorg er dus voor dat uw bronmachine vrije ruimte nodig heeft en probeer de bediening opnieuw.

## <a name="vss-installation-failures"></a>VSS-installatiefouten

VSS-installatie maakt deel uit van de installatie van de Mobility-agent. Deze service wordt gebruikt bij het genereren van toepassingsconsistente herstelpunten. Storingen tijdens de VSS-installatie kunnen optreden als gevolg van meerdere redenen. Als u de exacte fouten wilt identificeren, raadpleegt u **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. In de volgende sectie worden weinig veelvoorkomende fouten en de oplossingsstappen gemarkeerd.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS-fout -2147023170 [0x800706BE] - exitcode 511

Dit probleem wordt meestal gezien wanneer antivirussoftware de bewerkingen van Azure Site Recovery-services blokkeert. Los dit probleem als volgt op:

1. Sluit alle [hier](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)genoemde mappen uit.
2. Volg de richtlijnen die door uw antivirusprovider zijn gepubliceerd om de registratie van DLL in Windows te deblokkeren.

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS-fout 7 [0x7] - exitcode 511

Dit is een runtime-fout en wordt veroorzaakt door onvoldoende geheugen om VSS te installeren. Zorg ervoor dat u de schijfruimte vergroot voor een succesvolle voltooiing van deze bewerking.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS-fout -2147023824 [0x80070430] - exitcode 517

Deze fout treedt op wanneer de Azure Site Recovery VSS Provider-service is [gemarkeerd voor verwijdering.](https://msdn.microsoft.com/library/ms838153.aspx) Probeer VSS handmatig op de bronmachine te installeren door de volgende opdrachtregel uit te voeren

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS-fout -2147023841 [0x8007041F] - exitcode 512

Deze fout treedt op wanneer de Azure Site Recovery VSS Provider-servicedatabase is [vergrendeld.](https://msdn.microsoft.com/library/ms833798.aspx) Probeer VSS handmatig op de bronmachine te installeren door de volgende opdrachtregel uit te voeren

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Controleer in geval van een storing of er antivirusprogramma's of andere services vastzitten in de status 'Starten'. Dit kan het slot op databaseservices behouden. Het zal leiden tot storingen in het installeren van VSS provider. Zorg ervoor dat er geen service in de status 'Starten' staat en probeer de bovenstaande bewerking opnieuw.

### <a name="vss-exit-code-806"></a>VSS-exitcode 806

Deze fout treedt op wanneer het gebruikersaccount dat voor de installatie wordt gebruikt, geen machtigingen heeft om de csscript-opdracht uit te voeren. Geef de benodigde machtigingen aan het gebruikersaccount om het script uit te voeren en de bewerking opnieuw te proberen.

### <a name="other-vss-errors"></a>Andere VSS-fouten

Probeer de VSS-providerservice handmatig op de bronmachine te installeren door de volgende opdrachtregel uit te voeren

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>VSS-fout - 0x8004E00F

Deze fout wordt meestal ondervonden tijdens de installatie van de mobiliteitsagent als gevolg van problemen in DCOM en DCOM is in kritieke toestand.

Gebruik de volgende procedure om de oorzaak van de fout te bepalen.

**De installatielogboeken onderzoeken**

1. Open het installatielogboek op c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
2. De aanwezigheid van de volgende fout geeft dit probleem aan:

    Het registreren van de bestaande toepassing uit...  Het catalogusobject maken De verzameling toepassingen ophalen 

    Fout:

    - Foutcode: -2147164145 [0x8004E00F]
    - Exitcode: 802

Ga als ander op zoek naar het probleem:

Neem contact op met het [Microsoft Windows-platformteam](https://aka.ms/Windows_Support) om hulp te krijgen bij het oplossen van het DCOM-probleem.

Wanneer het DCOM-probleem is opgelost, installeert u de Azure Site Recovery VSS-provider handmatig met de volgende opdracht:
 
**C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd**
  
Als toepassingsconsistentie niet essentieel is voor uw vereisten voor herstel na noodgevallen, u de installatie van de VSS-provider omzeilen. 

Ga als het gaat om het omzeilen van de installatie van azure site recovery VSS-provider en het handmatig installeren van de installatie van Azure Site Recovery VSS Provider-post:

1. Installeer de mobiliteitsservice. 
   > [!Note]
   > 
   > De installatie mislukt bij de stap 'Installatieconfiguratie naplaatsen'. 
2. Ga als een te werk om de VSS-installatie te omzeilen:
   1. Open de installatiemap azure site recovery mobility service op:
   
      C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
   2. Wijzig de installatiescripts van Azure Site Recovery VSS Provider **nMageVSSProvider_Install** en **InMageVSSProvider_Uninstall.cmd** om altijd te slagen door de volgende regels toe te voegen:
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. Voer de Mobility Agent-installatie handmatig opnieuw uit. 
4. Wanneer de installatie slaagt en naar de volgende stap gaat, verwijdert **u**de regels die u hebt toegevoegd.
5. Als u de VSS-provider wilt installeren, opent u een opdrachtprompt als administrator en voert u de volgende opdracht uit:
   
    **C:\Program Files (x86)\Microsoft Azure Site Recovery\agent> .\InMageVSSProvider_Install.cmd**

9. Controleer of de ASR VSS-provider is geïnstalleerd als een service in Windows Services en open de MMC van componentservice om te controleren of de ASR VSS-provider wordt vermeld.
10. Als de installatie van de VSS-provider blijft mislukken, werkt u samen met CX om de fouten in de machtigingen in CAPI2 op te lossen.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>De installatie van VSS-provider mislukt omdat de clusterservice is ingeschakeld op niet-clustermachine

Dit probleem zorgt ervoor dat de installatie van azure site recovery mobility agent mislukt tijdens de installatiestap VAN ASAzure Site RecoveryR VSS Provider vanwege een probleem met COM+ dat de installatie van de VSS-provider verhindert.
 
### <a name="to-identify-the-issue"></a>Om het probleem te identificeren

In het logboek op de configuratieserver op C:\ProgramData\ASRSetupLogs\UploadedLogs-datumtijd\<>UA_InstallLogFile.log vindt u de volgende uitzondering:

COM+ kon niet praten met de Microsoft Distributed Transaction Coordinator (uitzondering van HRESULT: 0x8004E00F)

Ga als ander op zoek naar het probleem:

1.  Controleer of deze machine een niet-clustermachine is en of de clusteronderdelen niet worden gebruikt.
3.  Als de onderdelen niet worden gebruikt, verwijdert u de clusteronderdelen uit de machine.

## <a name="drivers-are-missing-on-the-source-server"></a>Stuurprogramma's ontbreken op de bronserver

Als de installatie van de Mobiliteitsagent mislukt, controleert u de logboeken onder C:\ProgramData\ASRSetupLogs om te bepalen of sommige van de vereiste stuurprogramma's ontbreken in sommige controlesets.
 
Ga als ander op zoek naar het probleem:
  
1. Open het register met behulp van een registereditor zoals regedit.msc.
2. Open het HKEY_LOCAL_MACHINE\SYSTEEMknooppunt.
3. Zoek in het systeemknooppunt de besturingselementsets.
4. Open elke besturingselementset en controleer of de volgende Windows-stuurprogramma's aanwezig zijn:

   - Atapi
   - Vmbus (Vmbus)
   - Storflt Storflt
   - Storvsc Storvsc
   - intelide
 
Installeer ontbrekende stuurprogramma's opnieuw.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het](vmware-azure-tutorial.md) instellen van noodherstel voor VMware VM's.
