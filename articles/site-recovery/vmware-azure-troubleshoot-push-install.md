---
title: Problemen met push-installatie van de Mobility-service oplossen bij het inschakelen van replicatie voor nood herstel | Microsoft Docs
description: Problemen met de installatie van Mobility Services oplossen bij het inschakelen van replicatie voor herstel na nood gevallen
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 09/11/2019
ms.openlocfilehash: 4aa18379962c289f5094795988a247f4c7e35df2
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910645"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Problemen met push-installatie van Mobility service oplossen

De installatie van de Mobility-service is een belang rijke stap tijdens het inschakelen van de replicatie. Het slagen van deze stap is alleen afhankelijk van de vereisten van de vergadering en het werken met ondersteunde configuraties. De meest voorkomende fouten die u tijdens de installatie van de Mobility-service krijgt, zijn als volgt:

* [Referentie-en bevoegdheids fouten](#credentials-check-errorid-95107--95108)
* [Mislukte aanmeldingen](#login-failures-errorid-95519-95520-95521-95522)
* [Connectiviteitsfouten](#connectivity-failure-errorid-95117--97118)
* [Fouten bij bestands-en printer deling](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [WMI-fouten](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Niet-ondersteunde besturings systemen](#unsupported-operating-systems)
* [Niet-ondersteunde opstartconfiguraties](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [VSS-installatiefouten](#vss-installation-failures)
* [De apparaatnaam in de GRUB-configuratie in plaats van de UUID van het apparaat](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [LVM volume](#lvm-support-from-920-version)
* [Waarschuwingen opnieuw opstarten](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Wanneer u replicatie inschakelt, probeert Azure Site Recovery de installatie van de Mobility Service-agent op uw virtuele machine te pushen. Als onderdeel hiervan probeert de configuratie server verbinding te maken met de virtuele machine en de agent te kopiëren. Als u een geslaagde installatie wilt inschakelen, volgt u de stapsgewijze richt lijnen voor probleem oplossing hieronder.

## <a name="credentials-check-errorid-95107--95108"></a>Controle van referenties (ErrorID: 95107 & 95108)

* Controleer of het gebruikers account dat u tijdens replicatie inschakelen hebt gekozen **, nauw keurig geldig**is.
* Voor Azure Site Recovery is een **hoofd** account of gebruikers account met **beheerders bevoegdheden** vereist om een push-installatie uit te voeren. Anders kan de push-installatie worden geblokkeerd op de bron machine.
  * Voor Windows (**fout 95107**) controleert u of het gebruikers account beheerders toegang, lokaal of domein, op de bron computer heeft.
  * Als u geen domein account gebruikt, moet u toegangs beheer voor externe gebruikers uitschakelen op de lokale computer.
    * Als u toegangs beheer voor externe gebruikers wilt uitschakelen, voegt u onder HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System register sleutel een nieuwe DWORD toe: LocalAccountTokenFilterPolicy. Stel de waarde in op 1. Als u deze stap wilt uitvoeren, voert u de volgende opdracht uit vanaf de opdracht prompt:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Voor Linux (**fout 95108**), moet u het hoofd account kiezen voor een geslaagde installatie van de Mobility-agent. Daarnaast moeten SFTP-services worden uitgevoerd. SFTP-subsysteem en-wachtwoord verificatie inschakelen in het sshd_config-bestand:
    1. Meld u aan als root.
    2. Ga naar het/etc/ssh/sshd_config-bestand en zoek de regel die begint met PasswordAuthentication.
    3. Verwijder de opmerking bij de regel en wijzig de waarde in Ja.
    4. Zoek de regel die begint met subsysteem en verwijder de opmerking over de regel.
    5. Start de sshd-service opnieuw.

Als u de referenties van het gekozen gebruikers account wilt wijzigen, volgt u de instructies die [hier](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)worden gegeven.

## <a name="insufficient-privileges-failure-errorid-95517"></a>Onvoldoende privileges-fouten (ErrorID: 95517)

Als de gebruiker die de Mobility-agent heeft gekozen geen beheerders bevoegdheden heeft, kan de configuratie server/scale-out proces server de software van de Mobility-agent niet naar de bron computer kopiëren. Deze fout is dus het gevolg van een fout in de toegang geweigerd. Zorg ervoor dat het gebruikers account beheerders rechten heeft.

Als u de referenties van het gekozen gebruikers account wilt wijzigen, volgt u de instructies die [hier](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)worden gegeven.

## <a name="insufficient-privileges-failure-errorid-95518"></a>Onvoldoende privileges-fouten (ErrorID: 95518)

Wanneer het instellen van een vertrouwens relatie tussen het primaire domein en werk station mislukt tijdens het aanmelden bij de bron machine, mislukt de installatie van de Mobility-agent met fout-ID 95518. Zorg er daarom voor dat het gebruikers account dat wordt gebruikt voor het installeren van de Mobility-agent, beheerders bevoegdheden heeft om zich aan te melden via het primaire domein van de bron machine.

Als u de referenties van het gekozen gebruikers account wilt wijzigen, volgt u de instructies die [hier](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)worden gegeven.

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Aanmeldings fouten (ErrorID: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>De referenties van het gebruikers account zijn uitgeschakeld (ErrorID: 95519)

Het gebruikers account dat is gekozen tijdens het inschakelen van replicatie, is uitgeschakeld. Als u het gebruikers account wilt inschakelen, raadpleegt u [het artikel of](https://aka.ms/enable_login_user) voert u de volgende opdracht uit door de *naam van de* gebruiker te vervangen door de feitelijke gebruikers namen.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>Referenties zijn vergrendeld vanwege meerdere mislukte aanmeldings pogingen (ErrorID: 95520)

Meerdere mislukte pogingen om toegang te krijgen tot een computer, vergren delen het gebruikers account. De fout kan worden veroorzaakt door:

* De referenties die tijdens de configuratie-instellingen zijn gegeven, zijn onjuist of
* Het gebruikers account dat is gekozen tijdens het inschakelen van de replicatie is onjuist

Wijzig daarom de gekozen referenties door de instructies te volgen die [hier](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) worden gegeven en probeer de bewerking na enige tijd opnieuw uit te voeren.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>Er zijn geen aanmeldings servers beschikbaar op de bron machine (ErrorID: 95521)

Deze fout treedt op wanneer de aanmeldings servers niet beschikbaar zijn op de bron machine. Niet-beschik baarheid van aanmeldings servers leidt ertoe dat de aanmeldings aanvraag niet kan worden uitgevoerd. de Mobility-agent kan daarom niet worden geïnstalleerd. Zorg ervoor dat aanmeldings servers beschikbaar zijn op de bron machine en start de aanmeldings service voor geslaagde aanmelding. Raadpleeg het KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) -fout bericht voor gedetailleerde instructies: Er zijn momenteel geen aanmeldings servers beschikbaar.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Logon-service wordt niet uitgevoerd op de bron machine (ErrorID: 95522)

De aanmeldings service wordt niet uitgevoerd op de bron machine en er is een fout opgetreden bij het aanmelden. Mobility agent kan daarom niet worden geïnstalleerd. Als u wilt oplossen, moet u ervoor zorgen dat de aanmeldings service wordt uitgevoerd op de bron computer voor een geslaagde aanmelding. Als u de aanmeldings service wilt starten, voert u de opdracht net start logon uit vanaf de opdracht prompt of start u de service NetLogon vanuit taak beheer.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Connectiviteits fout (ErrorID: 95117 & 97118)**

De configuratie server/scale-out proces server probeert verbinding te maken met de bron-VM om de Mobility-agent te installeren. Deze fout treedt op wanneer de bron machine niet bereikbaar is vanwege problemen met de netwerk verbinding. Om het probleem op te lossen,

* Zorg ervoor dat u uw bron machine kunt pingen vanaf de configuratie server. Als u scale-out proces server hebt gekozen tijdens het inschakelen van replicatie, moet u ervoor zorgen dat u uw bron machine kunt pingen vanaf de proces server.
  * Gebruik Telnet van de bron Server computer opdracht regel om de configuratie server/scale-out proces server met HTTPS-poort (135) te pingen, zoals hieronder wordt weer gegeven om te zien of er problemen met de netwerk verbinding of problemen met de firewall poort worden geblokkeerd.

     `telnet <CS/ scale-out PS IP address> <135>`
* Bovendien, voor **Linux VM**,
  * Controleer of de meest recente OpenSSH, openssh-server en openssl-pakketten zijn geïnstalleerd.
  * Controleer en of Secure Shell (SSH) is ingeschakeld en wordt uitgevoerd op poort 22.
  * SFTP-services moeten worden uitgevoerd. SFTP-subsysteem en-wachtwoord verificatie inschakelen in het sshd_config-bestand
    * Meld u aan als root.
    * Ga naar het/etc/ssh/sshd_config-bestand en zoek de regel die begint met PasswordAuthentication.
    * Verwijder de opmerking bij de regel en wijzig de waarde in Ja
    * Zoek de regel die begint met subsysteem en verwijder de opmerking over de regel
    * Start de sshd-service opnieuw.
* Een verbindings poging kan zijn mislukt als er geen juiste reactie is na een bepaalde periode, of als de verbinding tot stand is gebracht, omdat de verbonden host niet heeft gereageerd.
* Dit kan een probleem zijn met de verbinding/het netwerk/het domein. Dit kan ook worden veroorzaakt door het oplossen van problemen met de DNS-naam of het probleem met de TCP-poort. Controleer of er bekende problemen in uw domein zijn.

## <a name="connectivity-failure-errorid-95523"></a>Connectiviteits fout (ErrorID: 95523)

Deze fout treedt op wanneer het netwerk waarin de bron machine zich bevindt, niet is gevonden of is verwijderd of niet langer beschikbaar is. De enige manier om de fout op te lossen is door ervoor te zorgen dat het netwerk bestaat.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Controle van bestands-en printer share services (ErrorID: 95105 & 95106)

Controleer na de connectiviteits controle of de service voor bestands-en printer deling is ingeschakeld op de virtuele machine. Deze instellingen zijn vereist voor het kopiëren van de Mobility-agent op de bron machine.

Voor **windows 2008 R2 en eerdere versies**

* Om bestands-en printer deling via Windows Firewall in te scha kelen,
  * Open configuratie scherm-> systeem en beveiliging-> Windows Firewall-> in het linkerdeel venster op geavanceerde instellingen: > Klik op regels voor binnenkomende verbindingen in de console structuur.
  * Regels bestand en printer deling (NB-session-in) en bestands-en printer deling (SMB-in) zoeken. Voor elke regel klikt u met de rechter muisknop op de regel en klikt u vervolgens op **regel inschakelen**.
* Als u het delen van bestanden met groepsbeleid wilt inschakelen,
  * Ga naar Start, typ gpmc. msc en zoeken.
  * Open in het navigatie deel venster de volgende mappen: Beleid voor lokale computers, gebruikers configuratie, Beheersjablonen, Windows-onderdelen en delen van netwerken.
  * Dubbel klik in het detail venster op **voor komen dat gebruikers bestanden binnen hun profiel delen**. Als u de instelling groepsbeleid wilt uitschakelen en de gebruiker de mogelijkheid wilt bieden bestanden te delen, klikt u op uitgeschakeld. Klik op OK om uw wijzigingen op te slaan. Zie voor meer informatie over het [delen van bestanden in-of uitschakelen met Groepsbeleid](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Volg voor **latere versies**de instructies in [de Mobility-service installeren voor nood herstel van virtuele VMware-machines en fysieke servers](vmware-azure-install-mobility-service.md) om bestands-en printer deling in te scha kelen.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Controle van configuratie van Windows Management Instrumentation (WMI) (fout code: 95103)

Nadat u de bestands-en printer services hebt gecontroleerd, schakelt u de WMI-service in voor privé-, open bare en domein profielen via firewall. Deze instellingen zijn vereist voor het volt ooien van externe uitvoering op de bron machine. Om in te scha kelen,

* Ga naar configuratie scherm, klik op beveiliging en klik vervolgens op Windows Firewall.
* Klik op instellingen wijzigen en klik vervolgens op het tabblad uitzonde ringen.
* Schakel in het venster uitzonde ringen het selectie vakje voor Windows Management Instrumentation (WMI) in om WMI-verkeer via de firewall in te scha kelen. 

U kunt ook WMI-verkeer via de firewall inschakelen vanaf de opdracht prompt. Gebruik de volgende opdracht`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Andere artikelen over het oplossen van problemen met WMI vindt u in de volgende artikelen.

* [Eenvoudige WMI-tests](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI-problemen oplossen](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Problemen met WMI-scripts en WMI-services oplossen](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Niet-ondersteunde besturings systemen

De meest voorkomende reden voor de fout is mogelijk veroorzaakt door een niet-ondersteund besturings systeem. Zorg ervoor dat u zich in de ondersteunde versie van het besturings systeem/de kernel bevindt voor een geslaagde installatie van Mobility service. Vermijd het gebruik van een persoonlijke patch.
Raadpleeg het document over de [ondersteunings matrix](vmware-physical-azure-support-matrix.md#replicated-machines)om de lijst met besturings systemen en kernel-versies weer te geven die door Azure site Recovery worden ondersteund.

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Niet-ondersteunde configuraties voor opstart schijven (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Opstart-en systeem partities/-volumes bevinden zich niet op dezelfde schijf (ErrorID: 95309)

De opstart-en systeem partities/-volumes op verschillende schijven zijn vóór 9,20 versie een niet-ondersteunde configuratie. Deze configuratie wordt ondersteund van [9,20-versie](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery). De meest recente versie gebruiken voor deze ondersteuning.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>De opstart schijf is niet beschikbaar (ErrorID: 95310)

Een virtuele machine zonder een opstart schijf kan niet worden beveiligd. Dit is om ervoor te zorgen dat de virtuele machine probleemloos kan worden hersteld tijdens een failover-bewerking. Als er geen opstart schijf is, kan de computer niet worden opgestart na een failover. Zorg ervoor dat de virtuele machine een opstart schijf bevat en voer de bewerking opnieuw uit. Houd er ook rekening mee dat meerdere opstart schijven op dezelfde computer niet worden ondersteund.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Er zijn meerdere opstart schijven op de bron machine (ErrorID: 95311)

Een virtuele machine met meerdere opstart schijven is geen [ondersteunde configuratie](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Systeem partitie op meerdere schijven (ErrorID: 95313)

Vóór 9,20 versie werd een niet-ondersteunde configuratie van de hoofd partitie of het volume op meerdere schijven. Deze configuratie wordt ondersteund van [9,20-versie](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery). De meest recente versie gebruiken voor deze ondersteuning.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Het inschakelen van de beveiliging is mislukt als de apparaatnaam die wordt vermeld in de GRUB-configuratie in plaats van UUID (ErrorID: 95320)

**Mogelijke oorzaak:** </br>
De GRUB-configuratie bestanden ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" of "/etc/default/grub") bevatten mogelijk de waarde voor de **root** van de para meters en worden **hervat** als de werkelijke APPARAATNAMEN in plaats van uuid. Site Recovery is een UUID-benadering omdat de naam van apparaten kan veranderen tijdens het opnieuw opstarten van de virtuele machine, omdat VM mogelijk niet wordt bereikt met dezelfde naam als de failover, waardoor er problemen ontstaan. Bijvoorbeeld: </br>


- De volgende regel is afkomstig uit het GRUB-bestand **/boot/grub2/grub.cfg**. <br>
  *linux/boot/vmlinuz-3.12.49-11-default **root =/dev/sda2** $ {extra_cmdline} **Resume =/dev/sda1** begin = Silent quiet showopts*


- De volgende regel wordt uit het bestand WORMGATEN **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **hoofdmap = / dev/sda2** **= / dev/sda1 hervatten** splash = op de achtergrond crashkernel 256M-:128M showopts vga = 0x314 =*

Als u de vetgedrukte teken reeks hierboven ziet, heeft GRUB werkelijke apparaatnamen voor de para meters ' root ' en ' Resume ' in plaats van UUID.
 
**Oplossen:**<br>
De apparaatnamen moeten worden vervangen door de bijbehorende UUID.<br>


1. Zoek de UUID van het apparaat door de opdracht ' blkid \<device name > ' uit te voeren. Bijvoorbeeld:<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. Vervang nu de naam van het apparaat door de bijbehorende uuid in de notatie zoals root =\<uuid = uuid >. Als we bijvoorbeeld de apparaatnaam vervangen door UUID voor de para meter root en resume die hierboven wordt genoemd in de bestanden "/boot/grub2/grub.cfg", "/boot/grub2/grub.cfg" of "/etc/default/grub:, zien de regels in de bestanden er als volgt uit. <br>
   *kernel/boot/vmlinuz-3.0.101-63-default **root = uuid = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **Resume = uuid = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b** begin = Silent crashkernel = 256M-: 128M showopts VGA = 0x314*
3. De beveiliging opnieuw starten

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>De installatie van de Mobility-service is voltooid met een waarschuwing voor opnieuw opstarten (ErrorID: 95265 & 95266)

Site Recovery Mobility-service heeft veel onderdelen, een van de het filter stuur programma genoemd. Filter stuur programma wordt in het systeem geheugen alleen geladen op het moment dat het systeem opnieuw wordt opgestart. Dit betekent dat de correcties van het filter stuur programma alleen kunnen worden gerealiseerd wanneer een nieuw filter stuur programma wordt geladen. Dit kan gebeuren wanneer het systeem opnieuw wordt opgestart.

**Houd er rekening mee** dat dit een waarschuwing is en dat bestaande replicaties werken, zelfs na de nieuwe update van de agent. U kunt ervoor kiezen om op elk gewenst moment opnieuw op te starten om de voor delen van het nieuwe filter stuur programma te krijgen, maar als u het oude filter stuur programma niet opnieuw opstart, blijft het actief. Dus na een update zonder opnieuw opstarten, naast het filter stuur programma, worden de **voor delen van andere verbeteringen en oplossingen in Mobility service gerealiseerd**. Daarom is het echter niet verplicht om opnieuw op te starten na elke upgrade. Voor informatie over wanneer de computer opnieuw moet worden opgestart, stelt u de sectie [opnieuw opstarten van bron computer na de upgrade van de Mobility-agent](https://aka.ms/v2a_asr_reboot) in bij service-updates in azure site Recovery.

> [!TIP]
>Voor aanbevolen procedures voor het plannen van upgrades tijdens het onderhouds venster, zie de [ondersteuning voor de meest recente versies van besturings systemen/kernel](https://aka.ms/v2a_asr_upgrade_practice) in service-updates in azure site Recovery.

## <a name="lvm-support-from-920-version"></a>LVM-ondersteuning van 9,20-versie

Voor de 9,20-versie werd LVM alleen ondersteund voor gegevens schijven. /boot moet zich op een schijf partitie benemen en geen LVM-volume zijn.

Van [9,20-versie](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)wordt de [besturingssysteem schijf op LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) ondersteund. De meest recente versie gebruiken voor deze ondersteuning.

## <a name="insufficient-space-errorid-95524"></a>Onvoldoende ruimte (ErrorID: 95524)

Wanneer Mobility agent wordt gekopieerd naar de bron machine, is ten minste 100 MB vrije ruimte vereist. Zorg er daarom voor dat op de bron machine voldoende vrije ruimte beschikbaar is en voer de bewerking opnieuw uit.

## <a name="vss-installation-failures"></a>VSS-installatie fouten

VSS-installatie maakt deel uit van de installatie van de Mobility-agent. Deze service wordt gebruikt bij het genereren van toepassings consistente herstel punten. Fouten tijdens de installatie van VSS kunnen om verschillende redenen optreden. Raadpleeg **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**om de exacte fouten te identificeren. Enkele veelvoorkomende fouten en de oplossings stappen worden in de volgende sectie gemarkeerd.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS-fout-2147023170 [0x800706BE]-afsluit code 511

Dit probleem wordt doorgaans weer gegeven wanneer antivirus software de bewerkingen van Azure Site Recovery Services blokkeert. Los dit probleem als volgt op:

1. Sluit alle [hier](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)vermelde mappen uit.
2. Volg de richt lijnen die zijn gepubliceerd door uw antivirus provider om de registratie van de DLL in Windows te blok keren.

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS-fout 7 [0x7]-afsluit code 511

Dit is een runtime-fout en wordt veroorzaakt door onvoldoende geheugen om VSS te installeren. Zorg ervoor dat u de schijf ruimte verg root voor het volt ooien van deze bewerking.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS-fout-2147023824 [0x80070430]-afsluit code 517

Deze fout treedt op wanneer Azure Site Recovery VSS-Provider service is [gemarkeerd voor verwijdering](https://msdn.microsoft.com/library/ms838153.aspx). Probeer VSS hand matig te installeren op de bron machine door de volgende opdracht regel uit te voeren

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS-fout-2147023841 [0x8007041F]-afsluit code 512

Deze fout treedt op wanneer Azure Site Recovery VSS Provider service-data base is [vergrendeld](https://msdn.microsoft.com/library/ms833798.aspx). Probeer VSS hand matig te installeren op de bron machine door de volgende opdracht regel uit te voeren

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Als er een fout optreedt, controleert u of een antivirus programma of andere services zijn vastgelopen in de status ' wordt gestart '. Dit kan de vergren deling van database services behouden. Dit leidt tot fouten bij het installeren van de VSS-provider. Zorg ervoor dat de status van een service niet wordt gestart en voer de bovenstaande bewerking vervolgens opnieuw uit.

### <a name="vss-exit-code-806"></a>VSS-afsluit code 806

Deze fout treedt op wanneer het gebruikers account dat wordt gebruikt voor de installatie, niet over de machtigingen beschikt om de CSScript-opdracht uit te voeren. Geef de benodigde machtigingen voor het gebruikers account op om het script uit te voeren en voer de bewerking opnieuw uit.

### <a name="other-vss-errors"></a>Andere VSS-fouten

Probeer de VSS-Provider service hand matig te installeren op de bron machine door de volgende opdracht regel uit te voeren

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>VSS-fout-0x8004E00F

Deze fout treedt doorgaans op tijdens de installatie van de Mobility-agent als gevolg van problemen in DCOM en DCOM in een kritieke status.

Gebruik de volgende procedure om de oorzaak van de fout te bepalen.

**De installatie logboeken controleren**

1. Open het installatie logboek op c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
2. De aanwezigheid van de volgende fout wijst op dit probleem:

    De registratie van de bestaande toepassing ongedaan maken...  Het object Catalog maken de verzameling van toepassingen ophalen 

    OPTREEDT

    - Fout code:-2147164145 [0x8004E00F]
    - Afsluit code: 802

Om het probleem op te lossen:

Neem contact op met het [micro soft Windows-platform](https://aka.ms/Windows_Support) om hulp te krijgen bij het oplossen van het probleem met DCOM.

Wanneer het DCOM-probleem is opgelost, installeert u de Azure Site Recovery VSS-provider hand matig met behulp van de volgende opdracht:
 
**C:\Program Files (x86) \Microsoft Azure site Recovery\agent. > ' C:\Program Files (x86) \Microsoft Azure site Recovery\agent\InMageVSSProvider_Install.cmd**
  
Als toepassings consistentie niet essentieel is voor uw vereisten voor nood herstel, kunt u de installatie van de VSS-provider overs Laan. 

Als u de installatie van de Azure Site Recovery VSS-provider wilt overs Laan en hand matig wilt installeren Azure Site Recovery VSS-provider na de installatie:

1. De Mobility-service installeren. 
   > [!Note]
   > 
   > De installatie mislukt met de stap configuratie na installatie. 
2. De VSS-installatie overs Laan:
   1. Open de installatie directory van de Azure Site Recovery Mobility-service op de locatie:
   
      C:\Program Files (x86) \Microsoft Azure site Recovery\agent.
   2. Wijzig de Azure Site Recovery VSS-provider installatie scripts **nMageVSSProvider_Install** en **InMageVSSProvider_Uninstall. cmd** om altijd slagen door de volgende regels toe te voegen:
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. Voer de installatie van de Mobility-agent hand matig opnieuw uit. 
4. Wanneer de installatie is voltooid en naar de volgende **stap wordt verplaatst**, moet u de regels die u hebt toegevoegd, verwijderen.
5. Als u de VSS-provider wilt installeren, opent u een opdracht prompt als beheerder en voert u de volgende opdracht uit:
   
    **C:\Program Files (x86) \Microsoft Azure site Recovery\agent. > .\InMageVSSProvider_Install.cmd**

9. Controleer of de ASR VSS-provider is geïnstalleerd als een service in Windows Services en open de component service MMC om te controleren of de ASR VSS-provider wordt weer gegeven.
10. Als de VSS-provider niet kan worden geïnstalleerd, werkt u met CX om de machtigingen fouten in diagnostische CAPI2 op te lossen.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>De installatie van de VSS-provider is mislukt omdat de Cluster service is ingeschakeld op de niet-cluster machine

Dit probleem heeft tot gevolg dat de installatie van de Azure Site Recovery Mobility-agent mislukt tijdens de installatie stap van de VSS-provider van de ASAzure-site Recovery, vanwege een probleem met COM+ dat de installatie van de VSS-provider voor komt.
 
### <a name="to-identify-the-issue"></a>Het probleem identificeren

In het logboek op de configuratie server op C:\ProgramData\ASRSetupLogs\UploadedLogs\<datum/tijd > UA_InstallLogFile. log, vindt u de volgende uitzonde ring:

COM+ kan niet communiceren met de micro soft-Distributed Transaction Coordinator (uitzonde ring van HRESULT: 0x8004E00F)

Om het probleem op te lossen:

1.  Controleer of deze computer een niet-cluster machine is en of de cluster onderdelen niet worden gebruikt.
3.  Als de onderdelen niet worden gebruikt, verwijdert u de cluster onderdelen van de machine.

## <a name="drivers-are-missing-on-the-source-server"></a>Er ontbreken Stuur Programma's op de bron server

Als de installatie van de Mobility-agent mislukt, raadpleegt u de Logboeken onder C:\ProgramData\ASRSetupLogs om te bepalen of sommige van de vereiste Stuur Programma's in sommige besturings sets ontbreken.
 
Om het probleem op te lossen:
  
1. Open het REGI ster met behulp van een REGI ster-editor, zoals Regedit. msc.
2. Open het knoop punt HKEY_LOCAL_MACHINE\SYSTEM.
3. Zoek de besturings sets in het knoop punt systeem.
4. Open elke set besturings elementen en controleer of de volgende Windows-Stuur Programma's aanwezig zijn:

   - ATAPI
   - Vmbus
   - storflt
   - storvsc
   - Intelide
 
Installeer de ontbrekende Stuur Programma's opnieuw.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het](vmware-azure-tutorial.md) instellen van herstel na nood geval voor virtuele VMware-machines.
