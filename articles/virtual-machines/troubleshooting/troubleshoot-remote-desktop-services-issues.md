---
title: Extern bureaublad-services start niet op een Azure VM | Microsoft Documenten
description: Meer informatie over het oplossen van problemen met Extern bureaublad-services wanneer u verbinding maakt met een virtuele machine | Microsoft Documenten
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 4b314fbdb9cbc0c0b797cbee8e92ee4702bbea81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919461"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Extern bureaublad-services start niet op een Azure VM

In dit artikel wordt beschreven hoe u problemen oplossen wanneer u verbinding maakt met een virtuele Azure-machine (VM) en Remote Desktop Services, of TermService, niet wordt gestart of niet wordt gestart.


## <a name="symptoms"></a>Symptomen

Wanneer u verbinding probeert te maken met een virtuele machine, ervaart u de volgende scenario's:

- De VM screenshot toont het besturingssysteem is volledig geladen en wachten op referenties.

    ![Schermafbeelding van de VM-status](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- U bekijkt op afstand de gebeurtenislogboeken in de VM met Logboeken. U ziet dat Remote Desktop Services, TermService, niet wordt gestart of niet wordt gestart. Het volgende logboek is een voorbeeld:

    **Lognaam**: Systeem </br>
    **Bron**: Service Control Manager </br>
    **Datum**: 12/16/2017 11:19:36</br>
    **Gebeurtenis-id**: 7022</br>
    **Taakcategorie**: Geen</br>
    **Niveau**: Fout</br>
    **Trefwoorden**: Klassiek</br>
    **Gebruiker**: N/A</br>
    **Computer**: vm.contoso.com</br>
    **Beschrijving**: De service Extern bureaublad-services is bij het starten opgehangen. 

    U ook de functie Seriële toegangsconsole gebruiken om naar deze fouten te zoeken door de volgende query uit te voeren: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Oorzaak
 
Dit probleem treedt op omdat Extern bureaublad-services niet op de vm worden uitgevoerd. De oorzaak kan afhangen van de volgende scenario's: 

- De TermService-service is ingesteld op **Uitgeschakeld**. 
- De TermService-service loopt vast of reageert niet. 
- De TermService wordt niet gestart vanwege een onjuiste configuratie.

## <a name="solution"></a>Oplossing

Als u dit probleem wilt oplossen, gebruikt u de seriële console. Of [repareer de VM offline](#repair-the-vm-offline) door de OS-schijf van de VM aan een herstel-vm te koppelen.

### <a name="use-serial-console"></a>Seriële console gebruiken

1. Toegang tot de [seriële console](serial-console-windows.md) door **ondersteuning & seriële** > **console oplossen**. Als de functie is ingeschakeld op de VM, u de vm met succes verbinden.

2. Maak een nieuw kanaal voor een CMD-instantie. Voer **CMD** in om het kanaal te starten en de kanaalnaam op te halen.

3. Schakel over naar het kanaal waarop de CMD-instantie wordt uitgevoerd. In dit geval moet het kanaal 1 zijn:

   ```
   ch -si 1
   ```

4. Selecteer **Opnieuw invoeren** en voer een geldige gebruikersnaam en wachtwoord, lokale of domein-id, in voor de VM.

5. Vraag de status van de TermService-service op:

   ```
   sc query TermService
   ```

6. Als de servicestatus **Gestopt**wordt weergegeven, probeert u de service te starten:

    ```
    sc start TermService
     ``` 

7. Vraag de service opnieuw op om ervoor te zorgen dat de service is gestart:

   ```
   sc query TermService
   ```
8. Als de service niet wordt gestart, volgt u de oplossing op basis van de fout die u hebt ontvangen:

    |  Fout |  Suggestie |
    |---|---|
    |5- TOEGANG GEWEIGERD |Zie [TermService-service is gestopt vanwege een fout toegang geweigerd](#termservice-service-is-stopped-because-of-an-access-denied-problem). |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT  |Zie [TermService-service is uitgeschakeld](#termservice-service-is-disabled).  |  
    |1058 - ERROR_SERVICE_DISABLED  |Zie [TermService-service loopt vast of loopt vast](#termservice-service-crashes-or-hangs).  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |[Neem contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om het probleem snel op te lossen.|
    |1067 - ERROR_PROCESS_ABORTED  |Zie [TermService-service loopt vast of loopt vast](#termservice-service-crashes-or-hangs).  |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|[Neem contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om het probleem snel op te lossen.|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |See [TermService-service mislukt vanwege aanmeldingsfout](#termservice-service-fails-because-of-logon-failure) |
    |1070 - ERROR_SERVICE_START_HANG   | Zie [TermService-service loopt vast of loopt vast](#termservice-service-crashes-or-hangs). |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | Zie [TermService-service is uitgeschakeld](#termservice-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Neem contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om het probleem snel op te lossen. |
    |1753   |[Neem contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om het probleem snel op te lossen.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>TermService-service wordt gestopt vanwege een probleem met geweigerde toegang

1. Maak verbinding [met Serial Console](serial-console-windows.md) en open een PowerShell-exemplaar.
2. Download het gereedschap Procesmonitor door het volgende script uit te voeren:

   ```
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

4. Reproduceren het probleem door het starten van de service die toegang **geweigerd:** 

   ```
   sc start TermService 
   ```

   Als dit niet lukt, beëindigt u de tracering van de procesmonitor:

   ```   
   procmon /Terminate 
   ```

5. Het bestand **c:\temp\ProcMonTrace.PML verzamelen:**

    1. [Een gegevensschijf aan de vm koppelen](../windows/attach-managed-disk-portal.md
).
    2. Gebruik Serial Console u het bestand kopiëren naar het nieuwe station. Bijvoorbeeld `copy C:\temp\ProcMonTrace.PML F:\`. In deze opdracht is F de stuurprogrammaletter van de bijgevoegde gegevensschijf.
    3. Maak de datadrive los en bevestig deze op een werkende VM waarop procesmonitor ubstakke is geïnstalleerd.

6. Open **ProcMonTrace.PML** met behulp van Process Monitor de werkende VM. Vervolgens filter op **resultaat is toegang geweigerd,** zoals weergegeven in de volgende screenshot:

    ![Filteren op resultaat in procesmonitor](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Repareer de registersleutels, mappen of bestanden die zich in de uitvoer bevinden. Meestal wordt dit probleem veroorzaakt wanneer het aanmeldingsaccount dat op de service wordt gebruikt, geen ACL-toestemming heeft om toegang te krijgen tot deze objecten. Als u de juiste ACL-toestemming voor het aanmeldingsaccount wilt weten, u een gezonde VM controleren. 

#### <a name="termservice-service-is-disabled"></a>TermService-service is uitgeschakeld

1. De service herstellen naar de standaardopstartwaarde:

   ```
   sc config TermService start= demand 
   ```

2. Start de service:

   ```
   sc start TermService
   ```

3. Vraag de status opnieuw op om te controleren of de service wordt uitgevoerd:

   ```
   sc query TermService 
   ```

4. Probeer verbinding te maken met VM met Extern bureaublad.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>TermService-service mislukt vanwege aanmeldingsfout

1. Dit probleem treedt op als het opstartaccount van deze service is gewijzigd. Wijzigde dit terug naar de standaardinstelling: 

        sc config TermService obj= 'NT Authority\NetworkService'
2. Start de service:

        sc start TermService
3. Probeer verbinding te maken met VM met Extern bureaublad.

#### <a name="termservice-service-crashes-or-hangs"></a>TermService-service loopt vast of loopt vast
1. Als de servicestatus vastzit in **Starten** of **Stoppen,** probeert u de service te stoppen: 

        sc stop TermService
2. Isoleer de service op zijn eigen 'svchost'-container:

        sc config TermService type= own
3. Start de service:

        sc start TermService
4. Als de service nog steeds niet wordt gestart, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>De VM offline herstellen

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>De OS-schijf koppelen aan een herstel-vm

1. [Koppel de OS-schijf aan een herstelvm](../windows/troubleshoot-recovery-disks-portal.md).
2. Start een verbinding met Extern bureaublad met de herstel-vm. Controleer of de aangesloten schijf is gemarkeerd als **Online** in de schijfbeheerconsole. Let op de stationsletter die is toegewezen aan de gekoppelde osschijf.
3. Een instantie met een opdracht met verhoogde bevoegdheid openen **(Uitvoeren als beheerder).** Voer vervolgens het volgende script uit. We gaan ervan uit dat de drive letter die is toegewezen aan de bijgevoegde OS schijf is **F**. Vervang deze door de juiste waarde in uw VM. 

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
   REM Set default values back on the broken service 
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
   ```

4. [Maak de OS-schijf los en maak de VM opnieuw](../windows/troubleshoot-recovery-disks-portal.md). Controleer vervolgens of het probleem is opgelost.

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Contact opnemen met ondersteuning

Als u nog steeds hulp nodig hebt, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om het probleem op te lossen.
