---
title: Kan niet op afstand verbinding maken met Azure Virtual Machines omdat de DHCP is uitgeschakeld| Microsoft Documenten
description: Meer informatie over het oplossen van RDP-problemen die worden veroorzaakt door dhcp-clientservice is uitgeschakeld in Microsoft Azure.| Microsoft Documenten
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
ms.openlocfilehash: 2c5b0556554d280e57b2df51875e1b057b5fb4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749891"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Kan RDP niet naar Azure Virtual Machines omdat de DHCP-clientservice is uitgeschakeld

In dit artikel wordt een probleem beschreven waarbij u geen extern bureaublad naar Azure Windows Virtual Machines (VM's) gebruiken nadat de DHCP-clientservice is uitgeschakeld in de VM.


## <a name="symptoms"></a>Symptomen
U geen RDP-verbinding maken in Azure omdat de DHCP-clientservice is uitgeschakeld in de VM. Wanneer u de schermafbeelding in de [opstartdiagnose](../troubleshooting/boot-diagnostics.md) in de Azure-portal controleert, ziet u de VM-laarzen normaal en wacht u op referenties in het aanmeldingsscherm. U bekijkt op afstand de gebeurtenislogboeken in de VM met Logboeken. U ziet dat de DHCP-clientservice niet is gestart of niet wordt gestart. Het volgende voorbeeldlogboek:

**Lognaam**: Systeem </br>
**Bron**: Service Control Manager </br>
**Datum**: 12/16/2015 11:19:36 </br>
**Gebeurtenis-id**: 7022 </br>
**Taakcategorie**: Geen </br>
**Niveau**: Fout </br>
**Trefwoorden**: Klassiek</br>
**Gebruiker**: N/A </br>
**Computer**: myvm.cosotos.com</br>
**Beschrijving**: De DHCP-clientservice is bij het starten opgehangen.</br>

Voor VM's voor Resourcebeheer u de functie Seriële toegang console gebruiken om de gebeurtenislogboeken 7022 op te vragen met de volgende opdracht:

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

Voor klassieke VM's moet u in de OFFLINE-modus werken en de logboeken handmatig verzamelen.

## <a name="cause"></a>Oorzaak

De DHCP-clientservice wordt niet uitgevoerd op de VM.

> [!NOTE]
> Dit artikel is alleen van toepassing op de DHCP-clientservice en niet op DHCP Server.

## <a name="solution"></a>Oplossing

Voordat u deze stappen volgt, maakt u een momentopname van de OS-schijf van de betreffende VM als back-up. Zie [Momentopname een schijf voor](../windows/snapshot-copy-managed-disk.md)meer informatie .

Als u dit probleem wilt oplossen, gebruikt u Seriële besturingselementom DHCP in te schakelen of [de netwerkinterface](reset-network-interface.md) voor de VM opnieuw in te schakelen.

### <a name="use-serial-control"></a>Seriële besturingselement gebruiken

1. Maak verbinding [met seriële console en open CMD-instantie](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
). Zie [Netwerkinterface opnieuw instellen](reset-network-interface.md)als de seriële console niet is ingeschakeld op uw vm.
2. Controleer of de DHCP is uitgeschakeld op de netwerkinterface:

        sc query DHCP
3. Als de DHCP is gestopt, probeert u de service te starten

        sc start DHCP

4. Bevraag de service opnieuw om ervoor te zorgen dat de service wordt gestart.

        sc query DHCP

    Probeer verbinding te maken met de VM en kijk of het probleem is opgelost.
5. Als de service niet wordt gestart, gebruikt u de volgende geschikte oplossing op basis van het foutbericht dat u hebt ontvangen:

    | Fout  |  Oplossing |
    |---|---|
    | 5- TOEGANG GEWEIGERD  | Zie [DHCP-clientservice is gestopt vanwege een fout toegang geweigerd](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | Zie [DHCP-clientservice loopt vast of loopt vast](#dhcp-client-service-crashes-or-hangs).  |
    | 1058 - ERROR_SERVICE_DISABLED  | Zie [DHCP-clientservice is uitgeschakeld](#dhcp-client-service-is-disabled).  |
    | 1059 - ERROR_CIRCULAR_DEPENDENCY  |[Neem contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.   |
    | 1067 - ERROR_PROCESS_ABORTED |Zie [DHCP-clientservice loopt vast of loopt vast](#dhcp-client-service-crashes-or-hangs).   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | [Neem contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.  |
    |1069 - ERROR_SERVICE_LOGON_FAILED   |  Zie [DHCP-clientservice mislukt door aanmeldingsfout](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070 - ERROR_SERVICE_START_HANG  | Zie [DHCP-clientservice loopt vast of loopt vast](#dhcp-client-service-crashes-or-hangs).  |
    | 1077 - ERROR_SERVICE_NEVER_STARTED  | Zie [DHCP-clientservice is uitgeschakeld](#dhcp-client-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Neem contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.  |
    |1053 | [Neem contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>DHCP-clientservice is gestopt vanwege een fout toegang geweigerd

1. Maak verbinding [met Serial Console](serial-console-windows.md) en open een PowerShell-exemplaar.
2. Download het gereedschap Procesmonitor door het volgende script uit te voeren:

   ```powershell
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. Start nu een **procmon** trace:

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. Het probleem reproduceren door de service te starten die het bericht **Toegang geweigerd** genereert:

   ```
   sc start DHCP
   ```

   Als dit niet lukt, beëindigt u de tracering van de procesmonitor:

   ```
   procmon /Terminate
   ```
5. Verzamel het **bestand c:\temp\ProcMonTrace.PML:**

    1. [Een gegevensschijf aan de vm koppelen](../windows/attach-managed-disk-portal.md
).
    2. Gebruik Serial Console u het bestand kopiëren naar het nieuwe station. Bijvoorbeeld `copy C:\temp\ProcMonTrace.PML F:\`. In deze opdracht is F de stuurprogrammaletter van de bijgevoegde gegevensschijf. Vervang de letter naar gelang van het geval door de juiste waarde.
    3. Maak het gegevensstation los en bevestig deze vervolgens aan een werkende VM waarop procesmonitor ubstakke is geïnstalleerd.

6. Open **ProcMonTrace.PML** met procesmonitor op de werkende VM. Vervolgens filter op **resultaat is toegang geweigerd,** zoals weergegeven in de volgende screenshot:

    ![Filteren op resultaat in procesmonitor](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Repareer de registersleutels, mappen of bestanden die zich in de uitvoer bevinden. Meestal wordt dit probleem veroorzaakt wanneer het aanmeldingsaccount dat op de service wordt gebruikt, geen ACL-toestemming heeft om toegang te krijgen tot deze objecten. Als u de juiste ACL-toestemming voor het aanmeldingsaccount wilt bepalen, u een gezonde vm controleren.

#### <a name="dhcp-client-service-is-disabled"></a>DHCP-clientservice is uitgeschakeld

1. De service herstellen naar de standaardopstartwaarde:

   ```
   sc config DHCP start= auto
   ```

2. Start de service:

   ```
   sc start DHCP
   ```

3. Vraag de servicestatus opnieuw op om te controleren of deze wordt uitgevoerd:

   ```
   sc query DHCP
   ```

4. Probeer verbinding te maken met de vm met Extern bureaublad.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>DHCP-clientservice mislukt door aanmeldingsfout

1. Omdat dit probleem optreedt als het opstartaccount van deze service is gewijzigd, wordt het account teruggezet naar de standaardstatus:

        sc config DHCP obj= 'NT Authority\Localservice'
2. Start de service:

        sc start DHCP
3. Probeer verbinding te maken met de vm met Extern bureaublad.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>DHCP-clientservice loopt vast of loopt vast

1. Als de servicestatus vastzit in de status **Starten** of **Stoppen,** probeert u de service te stoppen:

        sc stop DHCP
2. Isoleer de service op zijn eigen 'svchost'-container:

        sc config DHCP type= own
3. Start de service:

        sc start DHCP
4. Als de service nog steeds niet wordt gestart, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>De VM offline herstellen

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>De OS-schijf koppelen aan een herstel-vm

1. [Koppel de OS-schijf aan een herstelvm](../windows/troubleshoot-recovery-disks-portal.md).
2. Start een verbinding met Extern bureaublad met de herstel-vm. Controleer of de aangesloten schijf is gemarkeerd als **Online** in de schijfbeheerconsole. Let op de stationsletter die is toegewezen aan de gekoppelde osschijf.
3.  Een instantie met een opdracht met verhoogde bevoegdheid openen **(Uitvoeren als beheerder).** Voer vervolgens het volgende script uit. In dit script wordt ervan uitgegaan dat de stationsletter die is toegewezen aan de gekoppelde OS-schijf **F**is. Vervang de letter naar gelang van het geval door de waarde in uw vm.

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

4. [Maak de OS-schijf los en maak de VM opnieuw](../windows/troubleshoot-recovery-disks-portal.md). Controleer vervolgens of het probleem is opgelost.

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds hulp nodig hebt, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem op te lossen.