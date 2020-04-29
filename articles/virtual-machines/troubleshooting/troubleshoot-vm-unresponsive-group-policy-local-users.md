---
title: De virtuele machine reageert niet tijdens het Toep assen van ' groepsbeleid lokale gebruikers & groeps beleid
description: Dit artikel bevat stappen voor het oplossen van problemen waarbij het laad scherm is vastgelopen tijdens het opstarten van een Azure virtual machine (VM).
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ff113268-f5bf-4e6a-986e-63b9b0ceff20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.author: v-mibufo
ms.openlocfilehash: 085880122e9a80e976cfe59686748b58aeba1922
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80620855"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>De virtuele machine reageert niet tijdens het Toep assen van ' groepsbeleid lokale gebruikers & groeps beleid

In dit artikel worden de stappen beschreven voor het oplossen van problemen waarbij het laad scherm is vastgelopen tijdens het opstarten, in een virtuele machine van Azure (VM).

## <a name="symptom"></a>Symptoom

Wanneer u [Diagnostische gegevens over opstarten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) gebruikt om de scherm opname van de virtuele machine weer te geven, ziet u dat het scherm is geladen met het volgende bericht: *Toep assen Groepsbeleid beleid voor lokale gebruikers en groepen*.

![ALT-tekst: scherm met het Toep assen van groepsbeleid lokale gebruikers en groeps beleid laden (Windows Server 2012).](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![ALT-tekst: scherm met het Toep assen van groepsbeleid lokale gebruikers en groeps beleid laden (Windows Server 2012 R2).](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>Oorzaak

De symptomen van deze blok kering worden veroorzaakt door een code fout in de Dynamic Link Library (*profsvc. dll*) van de Windows-profiel service.

> [!NOTE]
> Dit defect geldt alleen voor Windows Server 2012 en Windows Server 2012 R2.

### <a name="the-policy-in-question"></a>Het desbetreffende beleid

Het beleid dat wordt toegepast waarbij de processen niet worden voltooid, is:

* *Computerconfiguratie\beleid\beheersjablonen\windows-onderdelen\Extern sjablonen \ systeem/gebruiker Profiles\Delete gebruikers profielen ouder dan het opgegeven aantal dagen bij het opnieuw opstarten van het systeem*

Dit beleid loopt alleen vast als aan de volgende zes voor waarden wordt voldaan:

* De *gebruikers profielen verwijderen die ouder zijn dan het opgegeven aantal dagen voor het beleid voor het opnieuw opstarten van het systeem* is ingeschakeld.
* U hebt profielen die voldoen aan de leeftijds vereisten om opschonen te vereisen.
* U hebt onderdelen waarvoor een melding voor het verwijderen van profielen is geregistreerd.
* De onderdelen maken aanroepen (direct of indirect) die gegevens moeten ophalen uit de SCM-onderdelen (Service Control Manager) van Windows, zoals het starten, stoppen of opvragen van informatie over een service.
* U hebt een service geconfigureerd om te starten als *automatisch*.
* Deze service wordt ingesteld om te worden uitgevoerd onder de context van een domein account (in plaats van een ingebouwd account, zoals een lokaal systeem).

### <a name="the-code-defect"></a>Het code defect

Het defecte code wordt veroorzaakt door de Service Control Manager (SCM) en de profiel services proberen gelijktijdig vergren delingen op elkaar toe te passen. Er bestaan vergren delingen om te voor komen dat meerdere services tegelijkertijd wijzigingen aanbrengen op dezelfde gegevens, wat een beschadiging zou veroorzaken. Normaal gesp roken veroorzaken meerdere vergrendelings aanvragen een probleem. Omdat dit tijdens het opstarten plaatsvindt, kan de service echter hun processen volt ooien, omdat ze niet meer op elkaar wachten.

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>OS-fout 5880648-Service Control Manager deadlocks met het beleid ' gebruikers profielen verwijderen bij opnieuw opstarten '

Er zijn twee acties die elkaar overlappen:

* Actie 1 heeft de profiel vergrendeling opgehaald, maar heeft de SCM-vergren deling nog niet verkregen.

  **MAAR**

* Met actie 2 wordt de SCM-vergren deling opgehaald, maar de profiel vergrendeling nog niet is verkregen.

Zodra deze deadlock zich voordoet, wordt de poging om de tweede vereiste vergren deling te verkrijgen, de actie verloopt.

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>Actie 1-melding van oude verwijdering van profiel (heeft **profiel vergrendeling**vereist **SCM-vergren deling**)

1. Eerst wordt met het beleid dat is ingesteld om oude profielen te verwijderen, een interne profiel service vergrendeling verkregen.

   * Deze vergren deling is er om te voor komen dat twee threads communiceren met de profielen terwijl de *Verwijder bewerking* wordt uitgevoerd.

2. Het beleid zoekt naar profielen die oud genoeg zijn om te worden verwijderd.
3. Als onderdeel van het verwijderen van het profiel probeert een onderdeel dat is geregistreerd voor meldingen van de verwijderingen van een profiel, een **service te starten**.
4. Voordat de service wordt gestart, moet de Service Control Manager (SCM) een **interne SCM-vergrendeling** verkrijgen die wordt gehouden door threads in **actie 2**.

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>Actie 2: een profiel laden/maken voor gebruikersspecifieke gegevens (heeft **SCM-vergren deling**, **profiel vergrendeling**vereist)

1. Bij het opstarten moet SCM alle services voor *automatisch starten* door hun groep best Ellen, evenals alle services waarvan deze services afhankelijk zijn.

2. **SCM verkrijgt een interne SCM-vergrendeling** die wordt gebruikt voor het beheren van de toegang tot het starten, stoppen of configureren van services terwijl de services worden gesorteerd.

3. Zodra de services in orde zijn, wordt de SCM door elke service door lopen en wordt deze gestart.

4. Als de service wordt uitgevoerd onder de context van een domein account, moet een profiel worden geladen of gemaakt voor het domein account, zodat de gebruikersspecifieke gegevens kunnen worden opgeslagen.

5. Deze aanvraag wordt verzonden naar de **profiel service**.

6. De profiel service moet toegang hebben tot de **interne vergren deling** die is verkregen in **actie 1**.

## <a name="solution"></a>Oplossing

### <a name="process-overview"></a>Overzicht van het proces

1. Een herstel-VM maken en openen
2. Seriële console-en geheugen dump verzameling inschakelen
3. De virtuele machine opnieuw bouwen
4. Het geheugen dump bestand verzamelen

   > [!NOTE]
   > Wanneer deze opstart fout optreedt, is het gast besturingssysteem niet operationeel. U kunt problemen oplossen in de offline modus om dit probleem op te lossen.

### <a name="create-and-access-a-repair-vm"></a>Een herstel-VM maken en openen

1. Gebruik [stap 1-3 van de VM-reparatie opdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) om een herstel-VM voor te bereiden.
2. Gebruik Verbinding met extern bureaublad verbinding maken met de herstel-VM.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Seriële console-en geheugen dump verzameling inschakelen

Voer het volgende script uit om geheugen dump verzameling en seriële console in te scha kelen:

1. Open een opdracht prompt sessie met verhoogde bevoegdheden (als administrator uitvoeren).
2. Voer de volgende opdrachten uit:

   * Seriële console inschakelen:

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Controleer of de beschik bare ruimte op de besturingssysteem schijf net zo groot is als de geheugen grootte (RAM) op de virtuele machine.

   * Als er onvoldoende ruimte beschikbaar is op de besturingssysteem schijf, wijzigt u de locatie waar het geheugen dump bestand wordt gemaakt en verwijst naar alle gegevens schijven die zijn gekoppeld aan de VM met voldoende vrije ruimte. Als u de locatie wilt wijzigen `%SystemRoot%` , vervangt u door de stationsletter (zoals ' F: ') van de gegevens schijf in de onderstaande opdrachten.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Voorgestelde configuratie voor het inschakelen van de dump van het besturings systeem

**Beschadigde besturingssysteem schijf laden:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Inschakelen op ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Inschakelen op ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>De virtuele machine opnieuw bouwen

Gebruik [stap 5 van de opdrachten voor het herstellen van de virtuele machine](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) om de virtuele machine opnieuw samen te stellen.

### <a name="collect-the-memory-dump-file"></a>Het geheugen dump bestand verzamelen

Als u dit probleem wilt oplossen, moet u eerst het geheugen dump bestand voor de crash verzamelen en contact opnemen met ondersteuning met het geheugen dump bestand. Voer de volgende stappen uit om het dump bestand te verzamelen:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>De besturingssysteem schijf koppelen aan een nieuwe herstel-VM

1. Gebruik stap [1-3 van de VM-reparatie opdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) om een nieuwe herstel-VM voor te bereiden.

2. Gebruik Verbinding met extern bureaublad verbinding maken met de herstel-VM.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Het dump bestand zoeken en een ondersteunings ticket verzenden

1. Ga op de virtuele machine herstellen naar de map Windows in de gekoppelde besturingssysteem schijf. Als de stuur programma-letter die is toegewezen aan de gekoppelde besturingssysteem schijf F is, moet u naar F:\Windows.

2. Zoek het bestand Memory. dmp en [Verzend een ondersteunings ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) met het geheugen dump bestand.

3. Als u problemen ondervindt bij het vinden van het bestand Memory. dmp, wilt u in plaats daarvan mogelijk [niet-maskeer bare interrupt-aanroepen (NMI) gebruiken in de seriële console](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) . U kunt de gids volgen voor het [genereren van een kernel of het volt ooien van een crash dump](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) bestand met behulp van NMI-aanroepen.
