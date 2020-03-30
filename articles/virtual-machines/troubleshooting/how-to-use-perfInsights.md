---
title: PerfInsights gebruiken in Microsoft Azure| Microsoft Documenten
description: Leert hoe u PerfInsights gebruiken om problemen met de prestaties van Windows VM op te lossen.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 783b479dd3e5f429516799d7d3ea82f363cac2ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250125"
---
# <a name="how-to-use-perfinsights"></a>PerfInsights gebruiken

[PerfInsights](https://aka.ms/perfinsightsdownload) is een hulpmiddel voor zelfhulpdiagnostiek dat de diagnostische gegevens verzamelt en analyseert en een rapport biedt om problemen met de prestaties van Windows-virtuele machines in Azure op te lossen. PerfInsights kan worden uitgevoerd op virtuele machines als een zelfstandig hulpmiddel, rechtstreeks vanuit de portal met behulp van [Performance Diagnostics voor Azure virtuele machines,](performance-diagnostics.md)of door het installeren van Azure Performance Diagnostics VM [Extension](performance-diagnostics-vm-extension.md).

Als u prestatieproblemen ondervindt met virtuele machines, voert u dit hulpprogramma uit voordat u contact opneemt met de ondersteuning.

## <a name="supported-troubleshooting-scenarios"></a>Ondersteunde scenario's voor het oplossen van problemen

PerfInsights kan verschillende soorten informatie verzamelen en analyseren. De volgende secties hebben betrekking op veelvoorkomende scenario's.

### <a name="quick-performance-analysis"></a>Snelle prestatieanalyse

Dit scenario verzamelt de schijfconfiguratie en andere belangrijke informatie, waaronder:

-   Gebeurtenislogboeken

-   Netwerkstatus voor alle inkomende en uitgaande verbindingen

-   Instellingen voor netwerk- en firewallconfiguratie

-   Takenlijst voor alle toepassingen die momenteel op het systeem worden uitgevoerd

-   Configuratie-instellingen van Microsoft SQL Server-database (als de VM wordt geïdentificeerd als een server waarop SQL Server wordt uitgevoerd)

-   Opslagbetrouwbaarheidstellers

-   Belangrijke Windows-hotfixes

-   Geïnstalleerde filterstuurprogramma's

Dit is een passieve verzameling van informatie die geen invloed mag hebben op het systeem. 

>[!Note]
>Dit scenario wordt automatisch opgenomen in elk van de volgende scenario's:

### <a name="benchmarking"></a>Benchmarking

In dit scenario wordt de [Diskspd-benchmarktest](https://github.com/Microsoft/diskspd) (IOPS en MBPS) uitgevoerd voor alle stations die aan de VM zijn gekoppeld. 

> [!Note]
> Dit scenario kan van invloed zijn op het systeem en mag niet worden uitgevoerd op een live productiesysteem. Voer dit scenario indien nodig uit in een speciaal onderhoudsvenster om problemen te voorkomen. Een verhoogde werkbelasting die wordt veroorzaakt door een traceer- of benchmarktest kan de prestaties van uw vm negatief beïnvloeden.
>

### <a name="performance-analysis"></a>Prestatieanalyse

In dit scenario wordt een [prestatiemetertracering](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) uitgevoerd met behulp van de tellers die zijn opgegeven in het bestand RuleEngineConfig.json. Als de VM wordt geïdentificeerd als een server waarop SQL Server wordt uitgevoerd, wordt een prestatiemetertracering uitgevoerd. Het doet dit met behulp van de tellers die zijn gevonden in de RuleEngineConfig.json bestand. Dit scenario bevat ook prestatiediagnostische gegevens.

### <a name="azure-files-analysis"></a>Azure-bestandenanalyse

Dit scenario voert een speciale prestatieteller vast te leggen samen met een netwerktracering. De opname omvat alle smb-clientblokkentellers (Server Message Block). De volgende zijn enkele belangrijke smb client share performance tellers die deel uitmaken van de capture:

| **Type**     | **Aandelen teller mkb-klant** |
|--------------|-------------------------------|
| IOPS         | Gegevensaanvragen/sec             |
|              | Lees verzoeken/sec             |
|              | Schrijfverzoeken/sec            |
| Latentie      | Avg. sec/Data Request         |
|              | Avg. sec/Lezen                 |
|              | Avg. sec/Write                |
| IO-grootte      | Avg. Bytes/gegevensaanvraag       |
|              | Avg. Bytes/Read               |
|              | Avg. Bytes/Write              |
| Doorvoer   | Bytes voor gegevens per seconde                |
|              | Gelezen bytes/sec                |
|              | Geschreven bytes/sec               |
| Wachtrijlengte | Avg. Lees wachtrijlengte        |
|              | Avg. Wachtrijlengte schrijven       |
|              | Avg. Lengte gegevenswachtrij        |

### <a name="advanced-performance-analysis"></a>Geavanceerde prestatieanalyse

Wanneer u een geavanceerde prestatieanalyse uitvoert, selecteert u sporen die parallel moeten worden uitgevoerd. Als u wilt, u ze allemaal uitvoeren (Prestatiemeter, Xperf, Netwerk en StorPort).  

> [!Note]
> Dit scenario kan van invloed zijn op het systeem en mag niet worden uitgevoerd op een live productiesysteem. Voer dit scenario indien nodig uit in een speciaal onderhoudsvenster om problemen te voorkomen. Een verhoogde werkbelasting die wordt veroorzaakt door een traceer- of benchmarktest kan de prestaties van uw vm negatief beïnvloeden.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Wat voor informatie wordt verzameld door PerfInsights?

Er wordt informatie verzameld over de configuratie van Windows VM, schijven of opslaggroepen, prestatiemeteritems, logboeken en verschillende traces. Het hangt af van het prestatiescenario dat u gebruikt. De volgende lijst bevat de details:

|Verzamelde gegevens                              |  |  | Prestatiescenario's |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Snelle prestatieanalyse | Benchmarking | Prestatieanalyse | Azure-bestandenanalyse | Geavanceerde prestatieanalyse |
| Informatie uit gebeurtenislogboeken       | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Systeeminformatie                | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Volumekaart                        | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Schijfkaart                          | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Taken uitvoeren                     | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Opslagbetrouwbaarheidstellers      | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Opslaggegevens               | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Fsutil-uitvoer                     | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Gegevens van filterstuurprogramma's                | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Netstat-uitvoer                    | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Netwerkconfiguratie             | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Firewallconfiguratie            | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| SQL Server-configuratie          | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Sporen van prestatiediagnostiek *  | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Prestatiemetertracering **      |                            |                                    | Ja                      |                      | Ja                  |
| SMB tellerspoor **              |                            |                                    |                          | Ja                  |                      |
| SQL Server-tellertracering **       |                            |                                    | Ja                      |                      | Ja                  |
| Xperf-spoor                       |                            |                                    |                          |                      | Ja                  |
| StorPort-spoor                    |                            |                                    |                          |                      | Ja                  |
| Netwerktracering                     |                            |                                    |                          | Ja                  | Ja                  |
| Diskspd benchmark trace ***       |                            | Ja                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Prestatiediagnosetraceringstracering (*)

Met een op regels gebaseerde engine op de achtergrond om gegevens te verzamelen en lopende prestatieproblemen te diagnosticeren. De volgende regels worden momenteel ondersteund:

- HighCpuUsage regel: Detecteert hoge CPU-gebruik perioden, en toont de top CPU-gebruik consumenten tijdens die periodes.
- HighDiskUsage-regel: detecteert perioden met een hoog schijfgebruik op fysieke schijven en toont de hoogste schijfgebruiksconsumenten tijdens die perioden.
- HighResolutionDiskMetric-regel: toont IOPS-, doorvoer- en I/O-latentiestatistieken per 50 milliseconden voor elke fysieke schijf. Het helpt om snel schijfbeperkingsperioden te identificeren.
- HighMemoryUsage-regel: detecteert perioden met een hoog geheugengebruik en toont de hoogste geheugenverbruiksconsumenten tijdens die perioden.

> [!NOTE] 
> Momenteel worden Windows-versies met de .NET Framework 4.5 of hoger versies ondersteund.

### <a name="performance-counter-trace-"></a>Prestatiemetertracering (**)

Verzamelt de volgende prestatiemeteritems:

- \Proces, \Processor, \Geheugen, \Thread, \PhysicalDisk en \LogicalDisk
- \Cache\Dirty Pages, \Cache\Lazy Write Flushes/sec, \Server\Pool Nonpaged, Failures, and \Server\Pool Paged Failures \Cache\Lazy Write Flushes/sec, \Server\Pool Nonpaged, Failures, and \Server\Pool Paged Failures \Cache\Lazy Write Flushes/sec, \Server\Pool Nonpaged, Failures, and \Server\Pool Paged Failures \Cache\Lazy Write Flushes/sec, \Server\Pool
- Geselecteerde tellers onder \Netwerkinterface, \IPv4\Datagrammen, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network Adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \ NetwerkQoS-beleid\Pakketten, \Per Processor Network Interface Card Activity en \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>Voor SQL Server-exemplaren
- \SQL Server:Buffer Manager, \SQLServer:Resource Pool Stats en \SQLServer:SQL Statistics\
- \SQLServer:Sloten, \SQLServer:Algemeen, Statistieken
- \SQLServer:Toegangsmethoden

#### <a name="for-azure-files"></a>Voor Azure-bestanden
\SMB-clientaandelen

### <a name="diskspd-benchmark-trace-"></a>Diskspd-benchmarktracering (***)
Diskspd I/O-workloadtests (OS Disk [write] and pool drives [read/write])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Voer het gereedschap PerfInsights uit op uw VM

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Wat moet ik weten voordat ik de tool uitvoer? 

#### <a name="tool-requirements"></a>Gereedschapsvereisten

-  Deze tool moet worden uitgevoerd op de VM die het prestatieprobleem heeft. 

-  De volgende besturingssystemen worden ondersteund: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 en Windows Server 2016; Windows 8.1 en Windows 10.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Mogelijke problemen wanneer u de tool uitvoert op productie-VM's

-  Voor het benchmarkingscenario of het scenario 'Geavanceerde prestatieanalyse' dat is geconfigureerd om Xperf of Diskspd te gebruiken, kan het hulpprogramma de prestaties van de VM negatief beïnvloeden. Deze scenario's mogen niet worden uitgevoerd in een live productieomgeving.

-  Voor het benchmarkingscenario of het scenario 'Geavanceerde prestatieanalyse' dat is geconfigureerd om Diskspd te gebruiken, moet u ervoor zorgen dat geen enkele andere achtergrondactiviteit de I/O-werkbelasting verstoort.

-  Standaard gebruikt het hulpprogramma het tijdelijke opslagstation om gegevens te verzamelen. Als tracering langer is ingeschakeld, kan de hoeveelheid gegevens die wordt verzameld relevant zijn. Dit kan de beschikbaarheid van ruimte op de tijdelijke schijf verminderen en kan daarom van invloed zijn op elke toepassing die op dit station is gebaseerd.

### <a name="how-do-i-run-perfinsights"></a>Hoe voer ik PerfInsights uit? 

U PerfInsights op een virtuele machine uitvoeren door [de VM-extensie Azure Performance Diagnostics te](performance-diagnostics-vm-extension.md)installeren. U het ook uitvoeren als een standalone tool. 

**PerfInsights installeren en uitvoeren vanuit de Azure-portal**

Zie [VM-extensie Azure Performance Diagnostics installeren](performance-diagnostics-vm-extension.md#install-the-extension)voor meer informatie over deze optie.  

**PerfInsights uitvoeren in zelfstandige modus**

Voer de volgende stappen uit om het gereedschap PerfInsights uit te voeren:


1. Download [PerfInsights.zip](https://aka.ms/perfinsightsdownload).

2. Deblokkering van het bestand PerfInsights.zip deblokkeren. Klik hiervoor met de rechtermuisknop op het bestand PerfInsights.zip en selecteer **Eigenschappen**. Selecteer op het tabblad **Algemeen** de optie **De blokkering opheffen**en selecteer **OK**. Dit zorgt ervoor dat de tool wordt uitgevoerd zonder extra beveiligingsaanwijzingen.  

    ![Schermafbeelding van perfInsights-eigenschappen, waarbij de blokkering is gemarkeerd](media/how-to-use-perfInsights/pi-unlock-file.png)

3.  Vouw het gecomprimeerde PerfInsights.zip-bestand uit naar uw tijdelijke schijf (standaard is dit meestal het D-station). 

4.  Open de opdrachtprompt van Windows als beheerder en voer PerfInsights.exe uit om de beschikbare commandlineparameters weer te geven.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Schermafbeelding van de opdrachtregeluitvoer van PerfInsights](media/how-to-use-perfInsights/pi-commandline.png)
    
    De basissyntaxis voor het uitvoeren van PerfInsights-scenario's is:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    U het onderstaande voorbeeld gebruiken om een scenario voor prestatieanalyse gedurende 5 minuten uit te voeren:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    U het volgende voorbeeld gebruiken om het geavanceerde scenario 5 minuten uit te voeren met Sporen van de Xperf- en Prestatieteller:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    U het onderstaande voorbeeld gebruiken om een scenario voor prestatieanalyse gedurende 5 minuten uit te voeren en het resultaatzipbestand naar het opslagaccount te uploaden:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    U alle beschikbare scenario's en opties opzoeken met de opdracht **/list:**
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Voordat een scenario wordt uitgevoerd, vraagt PerfInsights de gebruiker om in te stemmen met het delen van diagnostische informatie en in te stemmen met de EULA. Gebruik de optie **/AcceptDisclaimerAndShareDiagnostics** om deze prompts over te slaan. 
    >
    >Als u een actief ondersteuningsticket hebt bij Microsoft en PerfInsights uitvoert op verzoek van de ondersteuningstechnicus waarmee u werkt, moet u het ondersteuningsticketnummer opgeven met behulp van de optie **/sr.**
    >
    >PerfInsights probeert zichzelf standaard te updaten naar de nieuwste versie indien beschikbaar. Gebruik **/SkipAutoUpdate** of **/sau** parameter om automatische update over te slaan.  
    >
    >Als de **duurswitch/d** niet is opgegeven, vraagt PerfInsights u om het probleem opnieuw te bespelen tijdens het uitvoeren van vmslow, azurefiles en geavanceerde scenario's. 

Wanneer de traces of bewerkingen zijn voltooid, verschijnt er een nieuw bestand in dezelfde map als PerfInsights. De naam van het bestand is **PerformanceDiagnostics\_\_yyyy-MM-dd hh-mm-ss-fff.zip.** U dit bestand naar de ondersteuningsagent sturen voor analyse of het rapport openen in het zip-bestand om bevindingen en aanbevelingen te bekijken.

## <a name="review-the-diagnostics-report"></a>Het diagnoserapport bekijken

In het **PerformanceDiagnostics\_\_yyyy-MM-dd hh-mm-ss-fff.zip-bestand** vindt u een HTML-rapport met de bevindingen van PerfInsights. Als u het rapport wilt bekijken, vouwt u het bestand **\_PerformanceDiagnostics\_yyyy-MM-dd hh-mm-ss-fff.zip uit** en opent u het bestand **PerfInsights Report.html.**

Selecteer het tabblad **Bevindingen.**

![Schermafbeelding van PerfInsights Report](media/how-to-use-perfInsights/pi-finding-tab.png)
![Screenshot van PerfInsights Report](media/how-to-use-perfInsights/pi-findings.png)

> [!NOTE] 
> Bevindingen gecategoriseerd als hoog zijn bekende problemen die prestatieproblemen kunnen veroorzaken. Bevindingen die als medium zijn gecategoriseerd, vertegenwoordigen niet-optimale configuraties die niet noodzakelijkerwijs prestatieproblemen veroorzaken. Bevindingen gecategoriseerd als laag zijn informatieve verklaringen alleen.

Bekijk de aanbevelingen en links voor alle hoge en middelgrote bevindingen. Meer informatie over hoe ze de prestaties kunnen beïnvloeden, en ook over best practices voor prestatiegeoptimaliseerde configuraties.

### <a name="storage-tab"></a>Tabblad Opslag

De **sectie Bevindingen** toont verschillende bevindingen en aanbevelingen met betrekking tot opslag.

In de secties **Schijfkaart** en **Volumekaart** wordt beschreven hoe logische volumes en fysieke schijven aan elkaar gerelateerd zijn.

In het fysieke schijfperspectief (Schijfkaart) toont de tabel alle logische volumes die op de schijf worden uitgevoerd. In het volgende voorbeeld voert **PhysicalDrive2** twee logische volumes uit die zijn gemaakt op meerdere partities (J en H):

![Schermafbeelding van het tabblad Schijf](media/how-to-use-perfInsights/pi-disk-tab.png)

In het volumeperspectief (Volumemap) worden alle fysieke schijven onder elk logisch volume weergegeven. Voor RAID/Dynamic-schijven u een logisch volume uitvoeren op meerdere fysieke schijven. In het volgende voorbeeld, *C:\\mount* is een mount point geconfigureerd als *SpannedDisk* op fysieke schijven 2 en 3:

![Schermafbeelding van het tabblad Volume](media/how-to-use-perfInsights/pi-volume-tab.png)

### <a name="sql-tab"></a>SQL-tabblad

Als de doel-VM SQL Server-exemplaren host, ziet u een extra tabblad in het rapport met de naam **SQL:**

![Schermafbeelding van het tabblad SQL](media/how-to-use-perfInsights/pi-sql-tab.png)

Deze sectie bevat een tabblad **Bevindingen** en extra tabbladen voor elk van de SQL Server-exemplaren die op de VM worden gehost.

Het tabblad **Bevindingen** bevat een lijst met alle SQL-gerelateerde prestatieproblemen die zijn gevonden, samen met de aanbevelingen.

In het volgende voorbeeld wordt **PhysicalDrive0** (met het C-station) weergegeven. Dit komt omdat zowel de **modeldev-** als **modellogbestanden** zich op het C-station bevinden en van verschillende typen zijn (respectievelijk gegevensbestand en transactielogboek).

![Schermafbeelding van logboekgegevens](media/how-to-use-perfInsights/pi-log-info.png)

De tabbladen voor specifieke exemplaren van SQL Server bevatten een algemene sectie die basisinformatie over de geselecteerde instantie weergeeft. De tabbladen bevatten ook extra secties voor geavanceerde informatie, waaronder instellingen, configuraties en gebruikersopties.

### <a name="diagnostic-tab"></a>Tabblad Diagnostische gegevens
Het tabblad **Diagnostische** bevat informatie over de beste CPU-, schijf- en geheugenconsumenten op de computer gedurende de duur van het uitvoeren van PerfInsights. U ook informatie vinden over kritieke patches die mogelijk ontbreken in het systeem, de takenlijst en belangrijke systeemgebeurtenissen. 

## <a name="references-to-the-external-tools-used"></a>Verwijzingen naar de gebruikte externe gereedschappen

### <a name="diskspd"></a>Diskspd

Diskspd is een opslaglaadgenerator en prestatietesttool van Microsoft. Zie [Diskspd voor](https://github.com/Microsoft/diskspd)meer informatie.

### <a name="xperf"></a>Xperf Xperf Xperf

Xperf is een command-line tool om sporen vast te leggen uit de Windows Performance Toolkit. Zie [Windows Performance Toolkit – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

U diagnostische logboeken en rapporten uploaden naar Microsoft Support voor verdere controle. Ondersteuning kan u vragen om de uitvoer die wordt gegenereerd door PerfInsights te verzenden om te helpen bij het probleemoplossingsproces.

In de volgende schermafbeelding wordt een bericht weergegeven dat vergelijkbaar is met wat u mogelijk ontvangt:

![Schermafbeelding van voorbeeldbericht van Microsoft Support](media/how-to-use-perfInsights/pi-support-email.png)

Volg de instructies in het bericht om toegang te krijgen tot de werkruimte voor bestandsoverdracht. Voor extra beveiliging moet u uw wachtwoord bij het eerste gebruik wijzigen.

Nadat u zich hebt aangemeld, vindt u een dialoogvenster om het **\_PerformanceDiagnostics\_yyyy-MM-dd hh-mm-ss-fff.zip-bestand** te uploaden dat is verzameld door PerfInsights.

