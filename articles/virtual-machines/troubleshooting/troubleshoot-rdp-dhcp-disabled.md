---
title: Kan geen verbinding maken op afstand naar Azure Virtual Machines omdat de DHCP is uitgeschakeld. | Microsoft Docs
description: Informatie over het oplossen van RDP probleem dat wordt veroorzaakt door DHCP client-service is uitgeschakeld in Microsoft Azure. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: ef44931cc3b36bcab64a2de840d9264c1b8fdedb
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058021"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Niet van RDP-verbinding naar Azure Virtual Machines, omdat de DHCP Client-service is uitgeschakeld

In dit artikel beschrijft een probleem waarbij u kunt geen extern bureaublad naar Azure Windows Virtual Machines (VM's) nadat de DHCP Client-service is uitgeschakeld in de virtuele machine.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="symptoms"></a>Symptomen
U maken niet een RDP-verbinding een virtuele machine in Azure omdat de DHCP Client-service is uitgeschakeld in de virtuele machine. Wanneer u de schermafbeelding controleren in de [diagnostische gegevens over opstarten](../troubleshooting/boot-diagnostics.md) in Azure portal, ziet u de virtuele machine normaal worden opgestart en wacht tot de referenties in het aanmeldingsscherm. U weergeven op afstand de gebeurtenislogboeken op de virtuele machine met behulp van Logboeken. U ziet dat de DHCP Client-Service is niet gestart of niet kan worden gestart. De volgende logboek voor een voorbeeld:

**Logboek naam**: Systeem </br>
**Bron**: Service besturings beheer </br>
**Datum**: 12/16/2015 11:19:36 UUR </br>
**Gebeurtenis-id**: 7022 </br>
**Taak categorie**: Geen </br>
**Niveau**: Fout </br>
**Tref woorden**: Klassiek</br>
**Gebruiker**: N/A </br>
**Computer**: myvm.cosotos.com</br>
**Beschrijving**: De DHCP client-service is bij het starten vastgelopen.</br>

Voor Resource Manager-VM's, kunt u toegang tot de seriële Console-functie aan query gebruiken voor de gebeurtenis-logboeken 7022 met de volgende opdracht:

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

Voor klassieke virtuele machines moet u voor het werken in de OFFLINEMODUS bevindt en de logboeken handmatig te verzamelen.

## <a name="cause"></a>Oorzaak

De DHCP Client-Service wordt niet uitgevoerd op de virtuele machine.

> [!NOTE]
> Dit artikel geldt alleen voor de DHCP Client-service en niet de DHCP-Server.

## <a name="solution"></a>Oplossing

Voordat u deze stappen hebt uitgevoerd, maakt u een momentopname van de besturingssysteemschijf van de betrokken virtuele machine als een back-up. Zie voor meer informatie, [momentopname maken van een schijf](../windows/snapshot-copy-managed-disk.md).

U lost dit probleem, kunt u seriële besturingselement gebruiken om DHCP of [opnieuw instellen van de netwerkinterface](reset-network-interface.md) voor de virtuele machine.

### <a name="use-serial-control"></a>Seriële besturingselement gebruiken

1. Verbinding maken met [seriële Console- en CMD-instantie openen](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
). Als de seriële Console is niet ingeschakeld op de virtuele machine, Zie [opnieuw instellen van de netwerkinterface](reset-network-interface.md).
2. Controleer als de DHCP is uitgeschakeld op de netwerkinterface:

        sc query DHCP
3. Als de DHCP is gestopt, probeert de service te starten

        sc start DHCP

4. Query uitvoeren op de service opnieuw om het ervoor te zorgen dat de service is gestart.

        sc query DHCP

    Probeer verbinding maken met de virtuele machine en zien als het probleem opgelost is.
5. Als de service niet wordt gestart, gebruikt u de volgende geschikte oplossing, gebaseerd op het foutbericht dat u hebt ontvangen:

    | Fout  |  Oplossing |
    |---|---|
    | 5 - TOEGANG IS GEWEIGERD  | Zie [DHCP Client-service is gestopt vanwege een fout toegang geweigerd](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | Zie [DHCP Client-service vastloopt of loopt vast](#dhcp-client-service-crashes-or-hangs).  |
    | 1058 - ERROR_SERVICE_DISABLED  | Zie [DHCP Client-service is uitgeschakeld](#dhcp-client-service-is-disabled).  |
    | 1059 - ERROR_CIRCULAR_DEPENDENCY  |[Neem contact op met ondersteuning voor](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om op te halen van uw probleem op te lossen snel.   |
    | 1067 - ERROR_PROCESS_ABORTED |Zie [DHCP Client-service vastloopt of loopt vast](#dhcp-client-service-crashes-or-hangs).   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | [Neem contact op met ondersteuning voor](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om op te halen van uw probleem op te lossen snel.  |
    |1069 - ERROR_SERVICE_LOGON_FAILED   |  Zie [DHCP Client-service is mislukt vanwege fout met aanmelding](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070 - ERROR_SERVICE_START_HANG  | Zie [DHCP Client-service vastloopt of loopt vast](#dhcp-client-service-crashes-or-hangs).  |
    | 1077 - ERROR_SERVICE_NEVER_STARTED  | Zie [DHCP Client-service is uitgeschakeld](#dhcp-client-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Neem contact op met ondersteuning voor](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om op te halen van uw probleem op te lossen snel.  |
    |1053 | [Neem contact op met ondersteuning voor](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om op te halen van uw probleem op te lossen snel.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>DHCP-Client-service is gestopt vanwege een fout toegang geweigerd

1. Verbinding maken met [seriële Console](serial-console-windows.md) en open een PowerShell-exemplaar.
2. Download het hulpprogramma procesmonitor het volgende script uit te voeren:

   ```powershell
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. Nu beginnen met een **procmon** trace:

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. Het probleem te reproduceren door de service die wordt gegenereerd vanaf de **toegang geweigerd** bericht:

   ```
   sc start DHCP
   ```

   Wanneer deze is mislukt, de trace-Monitor voor processen beëindigd:

   ```
   procmon /Terminate
   ```
5. Verzamelen de **c:\temp\ProcMonTrace.PML** bestand:

    1. [Een gegevensschijf koppelen aan de virtuele machine](../windows/attach-managed-disk-portal.md
).
    2. Seriële Console kunt u het bestand kopiëren naar het nieuwe station gebruiken. Bijvoorbeeld `copy C:\temp\ProcMonTrace.PML F:\`. In deze opdracht is F de stationsletter van de gekoppelde gegevensschijf. De stationsletter vervangen door de juiste waarde.
    3. Loskoppelen van de schijf en deze vervolgens koppelen aan een werkende virtuele machine waarvoor Process Monitor ubstakke geïnstalleerd.

6. Open **ProcMonTrace.PML** met behulp van de Monitor voor processen op de werkende virtuele machine. Vervolgens filteren op **resultaat is toegang geweigerd**, zoals weergegeven in de volgende schermafbeelding:

    ![Filteren op resultaat in proces bewaken](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Corrigeer de registersleutels, mappen of bestanden die zich op de uitvoer. Dit probleem wordt meestal veroorzaakt wanneer de aanmeldingsaccount die wordt gebruikt op de service beschikt niet over ACL-machtiging voor toegang tot deze objecten. Om te bepalen van de juiste ACL-machtiging voor het account aanmelden, kunt u controleren op een VM in orde.

#### <a name="dhcp-client-service-is-disabled"></a>DHCP-Client-service is uitgeschakeld

1. Herstellen van de service op de standaardwaarde voor opstarten:

   ```
   sc config DHCP start= auto
   ```

2. Start de service:

   ```
   sc start DHCP
   ```

3. De status van de service opnieuw uit om te controleren of deze query wordt uitgevoerd:

   ```
   sc query DHCP
   ```

4. Probeer verbinding maken met de virtuele machine met behulp van extern bureaublad.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>DHCP-Client-service is mislukt vanwege fout met aanmelding

1. Omdat dit probleem treedt op als het account voor het opstarten van deze service is gewijzigd, terugkeren u het account naar de standaardstatus:

        sc config DHCP obj= 'NT Authority\Localservice'
2. Start de service:

        sc start DHCP
3. Probeer verbinding maken met de virtuele machine met behulp van extern bureaublad.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>DHCP-Client-service vastloopt of loopt vast

1. Als de status van de service is mislukt de **vanaf** of **stoppen** heeft, probeert de service te stoppen:

        sc stop DHCP
2. De service op een eigen container 'svchost' isoleren:

        sc config DHCP type= own
3. Start de service:

        sc start DHCP
4. Als de service nog steeds niet wordt gestart, [contact op met ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Herstel de virtuele machine offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>De besturingssysteemschijf koppelen aan een virtuele machine voor herstel

1. [De besturingssysteemschijf koppelen aan een virtuele machine voor herstel](../windows/troubleshoot-recovery-disks-portal.md).
2. Start een externe bureaubladverbinding met de virtuele machine voor herstel. Zorg ervoor dat de gekoppelde schijf is gemarkeerd als **Online** in de Schijfbeheer-console. Houd er rekening mee de stationsletter die toegewezen aan de gekoppelde besturingssysteemschijf.
3.  Open een opdrachtprompt met verhoogde bevoegdheid-exemplaar (**als administrator uitvoeren**). Voer het volgende script. Met dit script wordt ervan uitgegaan dat de stationsletter die toegewezen aan de gekoppelde besturingssysteemschijf **F**. De stationsletter vervangen door de waarde in uw virtuele machine.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Set default values back on the broken service
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v type /t REG_DWORD /d 16 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v type /t REG_DWORD /d 16 /f

    reg unload HKLM\BROKENSYSTEM
    ```

4. [De OS-schijf loskoppelen en opnieuw maken van de virtuele machine](../windows/troubleshoot-recovery-disks-portal.md). Controleer vervolgens of het probleem is opgelost.

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds hulp nodig hebt, [contact op met ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om op te halen van uw probleem op te lossen.