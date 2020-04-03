---
title: Virtual Machine reageert niet tijdens het toepassen van het beleid 'Groepsbeleid lokale gebruikers & groepen'
description: In dit artikel worden stappen weergegeven om problemen op te lossen waarbij het laadscherm vastzit bij het toepassen van een beleid tijdens het opstarten in een Virtuele Azure Machine (VM).
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
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80620855"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>Virtual Machine reageert niet tijdens het toepassen van het beleid 'Groepsbeleid lokale gebruikers & groepen'

In dit artikel worden stappen weergegeven om problemen op te lossen waarbij het laadscherm vastzit bij het toepassen van een beleid tijdens het opstarten in een Virtuele Azure Machine (VM).

## <a name="symptom"></a>Symptoom

Wanneer u [Opstartdiagnostiek](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) gebruikt om de schermafbeelding van de vm weer te geven, ziet u dat het scherm vastzit aan het bericht: Beleid *groepsbeleid lokaal toepassen*.

![Alternatieve tekst: scherm met het laden van groepsbeleid lokale gebruikers en groepen (Windows Server 2012).](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![Alternatieve tekst: scherm met het laden van groepsbeleid lokale gebruikers en groepen (Windows Server 2012 R2).](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>Oorzaak

De symptomen van deze bevriezing worden veroorzaakt door een codedefect in de Windows Profile Service Dynamic Link Library *(profsvc.dll).*

> [!NOTE]
> Dit defect is alleen van toepassing op Windows Server 2012 en Windows Server 2012 R2.

### <a name="the-policy-in-question"></a>Het beleid in kwestie

Het beleid dat wordt toegepast dat de processen niet zal voltooien, is:

* *Computerconfiguratie\Beleid\Beheersjablonen\Systeem/gebruikersprofielen\Gebruikersprofielen verwijderen die ouder zijn dan een bepaald aantal dagen bij het opnieuw opstarten van het systeem*

Dit beleid blijft alleen hangen als de volgende zes voorwaarden waar zijn:

* Het *gebruikersprofiel verwijderen dat ouder is dan een bepaald aantal dagen voor het* beleid voor het opnieuw opstarten van het systeem, is ingeschakeld.
* U hebt profielen die voldoen aan de leeftijdsvereisten om opteis te moeten worden opgeschoond.
* U hebt onderdelen die zijn geregistreerd voor verwijderingsmelding voor profielen.
* De componenten maken oproepen (direct of indirect) die gegevens moeten verkrijgen van de SCM-onderdelen (Service Control Manager) van Windows, zoals Start,Stop of Query-informatie over een service.
* U hebt een service geconfigureerd om te starten als *automatisch*.
* Deze service is ingesteld om te worden uitgevoerd onder de context van een domeinaccount (in tegenstelling tot het gebruik van een ingebouwd account, zoals een lokaal systeem).

### <a name="the-code-defect"></a>Het codedefect

Het codedefect is te wijten aan de Service Control Manager (SCM) en de Profielservices die tegelijkertijd vergrendelingen op elkaar proberen toe te passen. Er bestaan vergrendelingen om te voorkomen dat meerdere services tegelijkertijd wijzigingen aanbrengen op dezelfde gegevens, wat corruptie zou veroorzaken. Normaal gesproken zouden meerdere vergrendelingsaanvragen geen probleem veroorzaken. Echter, omdat dit gebeurt tijdens het opstarten, kan geen van beide service hun processen voltooien, omdat ze op elkaar wachten.

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>OS Bug 5880648 - Service Control Manager impasses met de "Gebruikersprofielen verwijderen bij opnieuw opstarten" beleid

Er zijn twee acties die elkaar overlappen, zodat:

* Action 1 verwerft het profielslot, maar heeft het SCM-slot nog niet overgenomen.

  **En**

* Action 2 verwerft het SCM-slot, maar heeft de profielvergrendeling nog niet verworven.

Zodra deze impasse optreedt, de poging om de tweede vereiste slot te verwerven hangt de actie.

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>Actie 1 - Melding voor het verwijderen van oude profielen (heeft **Profielvergrendeling**, heeft **SCM-vergrendeling**nodig)

1. Ten eerste krijgt het beleid voor het verwijderen van oude profielen een intern profielserviceslot.

   * Dit slot is er om te voorkomen dat twee threads interactie hebben met de profielen terwijl de *verwijderingsbewerking* vordert.

2. In het beleid worden profielen gevonden die oud genoeg zijn om te worden verwijderd.
3. Als onderdeel van het verwijderen van het profiel probeert een onderdeel dat is geregistreerd voor meldingen van het verwijderen van een profiel een service te **starten.**
4. Voordat de service wordt gestart, moet de Service Control Manager (SCM) een **intern SCM-slot** aanschaffen dat wordt vastgehouden door threads in **Actie 2.**

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>Actie 2 - Profielbelasting/-creatie voor gebruikersspecifieke gegevens (heeft **SCM-vergrendeling**, heeft **profielvergrendeling**nodig )

1. Bij het opstarten moet SCM alle *automatische startservices* per hun groep bestellen, evenals alle services waarvan deze services afhankelijk zijn.

2. **SCM verwerft een intern SCM-slot** dat wordt gebruikt om de toegang tot het starten, stoppen of configureren van services te beheren terwijl het de services bestelt.

3. Zodra de services in orde zijn, loopt de SCM door elke service en start deze.

4. Als de service wordt uitgevoerd onder de context van een domeinaccount, moet een profiel worden geladen of gemaakt voor het domeinaccount, zodat het gebruikersspecifieke gegevens kan opslaan.

5. Dit verzoek wordt verzonden naar de **Profielservice.**

6. De profielservice heeft toegang nodig tot het **interne slot** dat is verkregen in **actie 1**.

## <a name="solution"></a>Oplossing

### <a name="process-overview"></a>Overzicht van proces

1. Een reparatievm maken en openen
2. Seriële console- en geheugendumpverzameling inschakelen
3. De VM opnieuw opbouwen
4. Het geheugendumpbestand verzamelen

   > [!NOTE]
   > Bij het tegenkomen van deze opstartfout is het besturingssysteem van de gast niet operationeel. U lost problemen op in de offlinemodus om dit probleem op te lossen.

### <a name="create-and-access-a-repair-vm"></a>Een reparatievm maken en openen

1. Gebruik [stap 1-3 van de VM-reparatieopdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) om een reparatie-vm voor te bereiden.
2. Verbinding met extern bureaublad gebruiken verbinding maken met de VM Repareren.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Seriële console- en geheugendumpverzameling inschakelen

Voer het script hieronder uit om geheugendumpverzameling en seriële console in te schakelen:

1. Open een opdrachtpromptsessie met verhoogde bevoegdheid (Uitvoeren als beheerder).
2. Voer de volgende opdrachten uit:

   * Seriële console inschakelen:

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Controleer of de vrije ruimte op de OS-schijf evenveel is als de geheugengrootte (RAM) op de VM.

   * Als er niet genoeg ruimte op de OS-schijf is, moet u de locatie wijzigen waar het geheugendumpbestand wordt gemaakt en verwijzen naar een gegevensschijf die is gekoppeld aan de VM die voldoende vrije ruimte heeft. Als u de `%SystemRoot%` locatie wilt wijzigen, vervangt u de stationsletter (zoals 'F:') van de gegevensschijf in de onderstaande opdrachten.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Voorgestelde configuratie om OS Dump in te schakelen

**Kapotte OS-schijf laden:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Inschakelen op ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Inschakelen op ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>De VM opnieuw opbouwen

Gebruik [stap 5 van de VM-reparatieopdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) om de VM opnieuw in elkaar te zetten.

### <a name="collect-the-memory-dump-file"></a>Het geheugendumpbestand verzamelen

Om dit probleem op te lossen, moet u eerst het geheugendumpbestand verzamelen voor de crash en contact opnemen met ondersteuning voor het geheugendumpbestand. Voer de volgende stappen uit om het dumpbestand te verzamelen:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>De OS-schijf koppelen aan een nieuwe reparatie-vm

1. Gebruik stap [1-3 van de VM-reparatieopdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) om een nieuwe reparatie-vm voor te bereiden.

2. Verbinding met extern bureaublad gebruiken verbinding maken met de VM Repareren.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Zoek het dumpbestand en stuur een ondersteuningsticket in

1. Ga op de reparatie-vm naar de Windows-map in de bijgevoegde OS-schijf. Als de stuurprogrammaletter die is toegewezen aan de gekoppelde osschijf F is, moet u naar F:\Windows gaan.

2. Zoek het memory.dmp-bestand en [dien vervolgens een ondersteuningsticket in](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) bij het geheugendumpbestand.

3. Als u problemen ondervindt bij het lokaliseren van het memory.dmp-bestand, u in plaats daarvan [niet-maskerbare interrupt-oproepen (NMI) in de seriële console](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) gebruiken. U de handleiding volgen om [een kernel of volledig crashdumpbestand](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) te genereren met BEHULP van NMI-aanroepen.
