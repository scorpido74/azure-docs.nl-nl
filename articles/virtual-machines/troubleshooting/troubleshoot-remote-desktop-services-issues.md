---
title: Extern bureaublad-services wordt niet gestart op een virtuele Azure-machine | Microsoft Docs
description: Meer informatie over het oplossen van problemen met Extern bureaublad-services wanneer u verbinding maakt met een virtuele machine | Microsoft Docs
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
ms.openlocfilehash: 7949bedec2d304cd87fb512b44cd61d6f0894638
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168955"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Extern bureaublad-services wordt niet gestart op een virtuele Azure-machine

In dit artikel wordt beschreven hoe u problemen oplost wanneer u verbinding maakt met een virtuele machine (VM) van Azure en Extern bureaublad-services, of als de term wordt gestart, niet wordt gestart of niet start.

> [!NOTE]  
> Azure heeft twee verschillende implementatie modellen voor het maken van en werken met resources: [Azure Resource Manager en klassiek](../../azure-resource-manager/resource-manager-deployment-model.md). In dit artikel wordt het gebruik van het Resource Manager-implementatie model beschreven. U wordt aangeraden dit model te gebruiken voor nieuwe implementaties in plaats van het klassieke implementatie model.

## <a name="symptoms"></a>Symptomen

Wanneer u probeert verbinding te maken met een virtuele machine, treden de volgende scenario's op:

- In de VM-scherm afbeelding ziet u dat het besturings systeem volledig is geladen en wordt gewacht op referenties.

    ![Scherm afbeelding van de status van de virtuele machine](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- U kunt de gebeurtenis logboeken op afstand weer geven in de virtuele machine met behulp van Logboeken. U ziet dat Extern bureaublad-services, de term niet start of niet kan worden gestart. Het volgende logboek is een voor beeld:

    **Logboek naam**: systeem </br>
    **Bron**: Service besturings beheer </br>
    **Datum**: 12/16/2017 11:19:36 uur</br>
    **Gebeurtenis-id**: 7022</br>
    **Taak categorie**: geen</br>
    **Niveau**: fout</br>
    **Tref woorden**: klassiek</br>
    **Gebruiker**: n.v.t.</br>
    **Computer**: VM.contoso.com</br>
    **Beschrijving**: de Extern bureaublad-services-service is bij het starten vastgelopen. 

    U kunt ook de functie seriële toegangs console gebruiken om deze fouten op te sporen door de volgende query uit te voeren: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Oorzaak
 
Dit probleem treedt op omdat Extern bureaublad-services niet wordt uitgevoerd op de VM. De oorzaak kan afhankelijk zijn van de volgende scenario's: 

- De service term is ingesteld op **uitgeschakeld**. 
- De service term is vastgelopen of reageert niet. 
- De term wordt niet gestart vanwege een onjuiste configuratie.

## <a name="solution"></a>Oplossing

Gebruik de seriële console om dit probleem op te lossen. Of [herstel de virtuele machine offline](#repair-the-vm-offline) door de besturingssysteem schijf van de virtuele machine te koppelen aan een herstel-VM.

### <a name="use-serial-console"></a>Seriële console gebruiken

1. Open de [seriële console](serial-console-windows.md) door ondersteuning te selecteren & problemen  > **seriële console**op te **lossen**. Als de functie is ingeschakeld op de virtuele machine, kunt u verbinding maken met de virtuele machine.

2. Een nieuw kanaal maken voor een CMD-exemplaar. Voer **cmd** in om het kanaal te starten en de kanaal naam op te halen.

3. Schakel over naar het kanaal waarop het CMD-exemplaar wordt uitgevoerd. In dit geval moet het kanaal 1 zijn:

   ```
   ch -si 1
   ```

4. Selecteer opnieuw **invoeren** en voer een geldige gebruikers naam en wacht woord, lokale of domein-id in voor de virtuele machine.

5. Query uitvoeren op de status van de service term:

   ```
   sc query TermService
   ```

6. Als de status van de service **wordt weer gegeven**, probeert u de service te starten:

    ```
    sc start TermService
     ``` 

7. Voer een query uit op de service om ervoor te zorgen dat de service met succes wordt gestart:

   ```
   sc query TermService
   ```
8. Als de service niet kan worden gestart, volgt u de oplossing op basis van de fout die u hebt ontvangen:

    |  Fout |  Suggestie |
    |---|---|
    |5-TOEGANG GEWEIGERD |Zie [de service term wordt gestopt vanwege een fout bij de toegang geweigerd](#termservice-service-is-stopped-because-of-an-access-denied-problem). |
    |1053-ERROR_SERVICE_REQUEST_TIMEOUT  |Zie de [service term is uitgeschakeld](#termservice-service-is-disabled).  |  
    |1058-ERROR_SERVICE_DISABLED  |Zie de [service terminals is vastgelopen of vastloopt](#termservice-service-crashes-or-hangs).  |
    |1059-ERROR_CIRCULAR_DEPENDENCY |[Neem contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.|
    |1067-ERROR_PROCESS_ABORTED  |Zie de [service terminals is vastgelopen of vastloopt](#termservice-service-crashes-or-hangs).  |
    |1068-ERROR_SERVICE_DEPENDENCY_FAIL|[Neem contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.|
    |1069-ERROR_SERVICE_LOGON_FAILED  |Zie de [service term is mislukt vanwege een mislukte aanmelding](#termservice-service-fails-because-of-logon-failure) |
    |1070-ERROR_SERVICE_START_HANG   | Zie de [service terminals is vastgelopen of vastloopt](#termservice-service-crashes-or-hangs). |
    |1077-ERROR_SERVICE_NEVER_STARTED   | Zie de [service term is uitgeschakeld](#termservice-service-is-disabled).  |
    |1079-ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Neem contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen. |
    |1753   |[Neem contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>De service wordt gestopt vanwege een probleem met de toegang is geweigerd

1. Maak verbinding met de [seriële console](serial-console-windows.md) en open een Power shell-exemplaar.
2. Down load het hulp programma Process Monitor door het volgende script uit te voeren:

   ```
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

4. Reproduceer het probleem door de service te starten waarvoor **toegang wordt verleend**: 

   ```
   sc start TermService 
   ```

   Als dit mislukt, beëindigt u de proces monitor tracering:

   ```   
   procmon /Terminate 
   ```

5. Het bestand **c:\temp\ProcMonTrace.PML**verzamelen:

    1. [Een gegevens schijf koppelen aan de virtuele machine](../windows/attach-managed-disk-portal.md
).
    2. Seriële console gebruiken u kunt het bestand naar het nieuwe station kopiëren. Bijvoorbeeld `copy C:\temp\ProcMonTrace.PML F:\`. In deze opdracht is F de letter van het stuur programma van de gekoppelde gegevens schijf.
    3. Ontkoppel het gegevens station en koppel dit aan een werkende VM waarop Process Monitor-ubstakke is geïnstalleerd.

6. Open **ProcMonTrace. PML** met behulp van process monitoring the working VM. Vervolgens filteren op **resultaat is toegang geweigerd**, zoals wordt weer gegeven in de volgende scherm afbeelding:

    ![Filteren op resultaat in proces monitor](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Herstel de register sleutels,-mappen of-bestanden die zich in de uitvoer bevinden. Dit probleem treedt meestal op wanneer het aanmeldings account dat wordt gebruikt voor de service geen ACL-machtiging heeft voor toegang tot deze objecten. Als u de juiste ACL-machtiging voor het aanmeldings account wilt weten, kunt u controleren op een goede VM. 

#### <a name="termservice-service-is-disabled"></a>De service wordt uitgeschakeld

1. Herstel de standaard opstart waarde van de service:

   ```
   sc config TermService start= demand 
   ```

2. Start de service:

   ```
   sc start TermService
   ```

3. Vraag opnieuw de status aan om te controleren of de service wordt uitgevoerd:

   ```
   sc query TermService 
   ```

4. Probeer verbinding te maken met de virtuele machine met behulp van Extern bureaublad.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>De service Terminal Services is mislukt vanwege een mislukte aanmelding

1. Dit probleem treedt op als het opstart account van deze service is gewijzigd. De standaard instelling is gewijzigd in: 

        sc config TermService obj= 'NT Authority\NetworkService'
2. Start de service:

        sc start TermService
3. Probeer verbinding te maken met de virtuele machine met behulp van Extern bureaublad.

#### <a name="termservice-service-crashes-or-hangs"></a>Service wordt vastlopen of vastloopt
1. Als de status van de service vastzit aan het **starten** of **stoppen**, probeert u de service te stoppen: 

        sc stop TermService
2. De service op een eigen ' Svchost '-container isoleren:

        sc config TermService type= own
3. Start de service:

        sc start TermService
4. Als de service nog steeds niet kan worden gestart, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>De virtuele machine offline herstellen

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>De besturingssysteem schijf koppelen aan een herstel-VM

1. [Koppel de besturingssysteem schijf aan een herstel-VM](../windows/troubleshoot-recovery-disks-portal.md).
2. Start een Extern bureaublad verbinding met de virtuele machine voor herstel. Controleer of de gekoppelde schijf **online** is gemarkeerd in de schijf beheer-console. Noteer de stationsletter die is toegewezen aan de gekoppelde besturingssysteem schijf.
3. Open een opdracht prompt exemplaar met verhoogde bevoegdheid (**als administrator uitvoeren**). Voer vervolgens het volgende script uit. We gaan ervan uit dat de stationsletter die is toegewezen aan de gekoppelde besturingssysteem schijf **F**is. Vervang deze door de juiste waarde in uw VM. 

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

4. [Ontkoppel de besturingssysteem schijf en maak de virtuele machine opnieuw](../windows/troubleshoot-recovery-disks-portal.md). Controleer vervolgens of het probleem is opgelost.

## <a name="need-help-contact-support"></a>Hulp nodig? Contact opnemen met ondersteuning

Als u nog hulp nodig hebt, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om het probleem op te lossen.
