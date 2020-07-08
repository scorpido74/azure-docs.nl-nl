---
title: Kan niet extern verbinding maken met Azure Virtual Machines omdat de DHCP is uitgeschakeld | Microsoft Docs
description: Informatie over het oplossen van problemen met RDP-problemen dat wordt veroorzaakt door de DHCP-client service is uitgeschakeld in Microsoft Azure. | Microsoft Docs
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
ms.openlocfilehash: 278d976f044deb8a7387763306cf07f8b6b55d90
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86087789"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Kan geen RDP-naar-Azure-Virtual Machines omdat de DHCP client-service is uitgeschakeld

In dit artikel wordt een probleem beschreven waarbij u geen extern bureau blad-naar-Azure-Windows Virtual Machines (Vm's) kunt, nadat de DHCP-client service is uitgeschakeld in de virtuele machine.


## <a name="symptoms"></a>Symptomen
U kunt geen RDP-verbinding maken met een virtuele machine in azure omdat de DHCP-client service is uitgeschakeld in de virtuele machine. Wanneer u de scherm opname van de [Diagnostische gegevens over opstarten](../troubleshooting/boot-diagnostics.md) in de Azure Portal controleert, ziet u dat de VM normaal wordt opgestart en wacht op referenties in het aanmeldings scherm. U kunt de gebeurtenis logboeken op afstand weer geven in de virtuele machine met behulp van Logboeken. U ziet dat de DHCP client-service niet is gestart of niet kan worden gestart. In het volgende voor beeld van een logboek:

**Logboek naam**: systeem </br>
**Bron**: Service besturings beheer </br>
**Datum**: 12/16/2015 11:19:36 uur </br>
**Gebeurtenis-id**: 7022 </br>
**Taak categorie**: geen </br>
**Niveau**: fout </br>
**Tref woorden**: klassiek</br>
**Gebruiker**: n.v.t. </br>
**Computer**: myvm.cosotos.com</br>
**Beschrijving**: de DHCP-client service is bij het starten vastgelopen.</br>

Voor virtuele machines van Resource Manager kunt u met behulp van de console van de seriële toegang zoeken naar de gebeurtenis logboeken 7022 met de volgende opdracht:

```console
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

Voor klassieke Vm's moet u in de OFFLINE modus werken en de logboeken hand matig verzamelen.

## <a name="cause"></a>Oorzaak

De DHCP client-service wordt niet uitgevoerd op de VM.

> [!NOTE]
> Dit artikel is alleen van toepassing op de DHCP client-service en niet op de DHCP-server.

## <a name="solution"></a>Oplossing

Voordat u deze stappen volgt, moet u een moment opname maken van de besturingssysteem schijf van de betrokken VM als back-up. Zie [snap shot a disk](../windows/snapshot-copy-managed-disk.md)(Engelstalig) voor meer informatie.

U kunt dit probleem oplossen met behulp van seriële controle om DHCP in te scha kelen of de [netwerk interface opnieuw](reset-network-interface.md) in te stellen voor de virtuele machine.

### <a name="use-serial-control"></a>Serieel besturings element gebruiken

1. Verbinding maken met de [seriële console en het Open cmd-exemplaar](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
). Als de seriële console niet op uw virtuele machine is ingeschakeld, raadpleegt u de [netwerk interface opnieuw instellen](reset-network-interface.md).
2. Controleer of DHCP is uitgeschakeld op de netwerk interface:

    ```console
    sc query DHCP
    ```

3. Als de DHCP is gestopt, probeert u de service te starten

    ```console
    sc start DHCP
    ```

4. Voer de query opnieuw uit om te controleren of de service is gestart.

    ```console
    sc query DHCP
    ```

    Probeer verbinding te maken met de virtuele machine om te zien of het probleem is opgelost.
5. Als de service niet wordt gestart, gebruikt u de volgende geschikte oplossing op basis van het fout bericht dat u hebt ontvangen:

    | Fout  |  Oplossing |
    |---|---|
    | 5-TOEGANG GEWEIGERD  | Zie [de DHCP-client service is gestopt vanwege een fout bij de toegang geweigerd](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053-ERROR_SERVICE_REQUEST_TIMEOUT   | Zie de [DHCP-client service loopt vast of loopt vast](#dhcp-client-service-crashes-or-hangs).  |
    | 1058-ERROR_SERVICE_DISABLED  | Zie de [DHCP client-service is uitgeschakeld](#dhcp-client-service-is-disabled).  |
    | 1059-ERROR_CIRCULAR_DEPENDENCY  |[Neem contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.   |
    | 1067-ERROR_PROCESS_ABORTED |Zie de [DHCP-client service loopt vast of loopt vast](#dhcp-client-service-crashes-or-hangs).   |
    |1068-ERROR_SERVICE_DEPENDENCY_FAIL   | [Neem contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.  |
    |1069-ERROR_SERVICE_LOGON_FAILED   |  Zie de [DHCP-client service is mislukt vanwege een fout bij de aanmelding](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070-ERROR_SERVICE_START_HANG  | Zie de [DHCP-client service loopt vast of loopt vast](#dhcp-client-service-crashes-or-hangs).  |
    | 1077-ERROR_SERVICE_NEVER_STARTED  | Zie de [DHCP client-service is uitgeschakeld](#dhcp-client-service-is-disabled).  |
    |1079-ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Neem contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.  |
    |1053 | [Neem contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>De DHCP-client service is gestopt vanwege een fout bij de toegang geweigerd

1. Maak verbinding met de [seriële console](serial-console-windows.md) en open een Power shell-exemplaar.
2. Down load het hulp programma Process Monitor door het volgende script uit te voeren:

   ```powershell
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. Begin nu met een **ProcMon** -tracering:

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. Reproduceer het probleem door de service te starten waardoor het bericht **toegang geweigerd wordt** gegenereerd:

   ```
   sc start DHCP
   ```

   Als dit mislukt, beëindigt u de proces monitor tracering:

   ```
   procmon /Terminate
   ```
5. Het **c:\temp\ProcMonTrace.PML** -bestand verzamelen:

    1. [Een gegevens schijf koppelen aan de virtuele machine](../windows/attach-managed-disk-portal.md
).
    2. Seriële console gebruiken u kunt het bestand naar het nieuwe station kopiëren. Bijvoorbeeld `copy C:\temp\ProcMonTrace.PML F:\`. In deze opdracht is F de letter van het stuur programma van de gekoppelde gegevens schijf. Vervang de letter naar wens door de juiste waarde.
    3. Ontkoppel het gegevens station en koppel het vervolgens aan een werkende VM waarop Process Monitor-ubstakke is geïnstalleerd.

6. Open **ProcMonTrace. PML** met behulp van Process Monitor op de werkende VM. Vervolgens filteren op **resultaat is toegang geweigerd**, zoals wordt weer gegeven in de volgende scherm afbeelding:

    ![Filteren op resultaat in proces monitor](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Herstel de register sleutels,-mappen of-bestanden die zich in de uitvoer bevinden. Dit probleem treedt meestal op wanneer het aanmeldings account dat wordt gebruikt voor de service geen ACL-machtiging heeft voor toegang tot deze objecten. Als u de juiste ACL-machtiging voor het aanmeldings account wilt bepalen, kunt u controleren op een goede VM.

#### <a name="dhcp-client-service-is-disabled"></a>De DHCP-client service is uitgeschakeld

1. Herstel de standaard opstart waarde van de service:

   ```
   sc config DHCP start= auto
   ```

2. Start de service:

   ```
   sc start DHCP
   ```

3. Voer een query uit op de service status om er zeker van te zijn dat deze wordt uitgevoerd:

   ```
   sc query DHCP
   ```

4. Probeer verbinding te maken met de virtuele machine met behulp van Extern bureaublad.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>De DHCP-client service is mislukt vanwege een aanmeldings fout

1. Omdat dit probleem optreedt als het opstart account van deze service is gewijzigd, herstelt u de standaard status van het account:

    ```console
    sc config DHCP obj= 'NT Authority\Localservice'
    ```

2. Start de service:

    ```console
    sc start DHCP
    ```

3. Probeer verbinding te maken met de virtuele machine met behulp van Extern bureaublad.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>De DHCP-client service loopt vast of loopt vast

1. Als de status van de service is vastgelopen in de status **starten** of **stoppen** , probeert u de service te stoppen:

    ```console
    sc stop DHCP
    ```

2. De service op een eigen ' Svchost '-container isoleren:

    ```console
    sc config DHCP type= own
    ```

3. Start de service:

    ```console
    sc start DHCP
    ```

4. Als de service nog steeds niet wordt gestart, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>De virtuele machine offline herstellen

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>De besturingssysteem schijf koppelen aan een herstel-VM

1. [Koppel de besturingssysteem schijf aan een herstel-VM](../windows/troubleshoot-recovery-disks-portal.md).
2. Start een Extern bureaublad verbinding met de virtuele machine voor herstel. Controleer of de gekoppelde schijf **online** is gemarkeerd in de schijf beheer-console. Noteer de stationsletter die is toegewezen aan de gekoppelde besturingssysteem schijf.
3.  Open een opdracht prompt exemplaar met verhoogde bevoegdheid (**als administrator uitvoeren**). Voer vervolgens het volgende script uit. In dit script wordt ervan uitgegaan dat de stationsletter die is toegewezen aan de gekoppelde besturingssysteem schijf **F**is. Vervang de letter naar wens door de waarde in uw VM.

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

4. [Ontkoppel de besturingssysteem schijf en maak de virtuele machine opnieuw](../windows/troubleshoot-recovery-disks-portal.md). Controleer vervolgens of het probleem is opgelost.

## <a name="next-steps"></a>Volgende stappen

Als u nog hulp nodig hebt, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om het probleem op te lossen.