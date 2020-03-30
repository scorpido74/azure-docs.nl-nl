---
title: Diagnostics Tool om problemen op te lossen StorSimple 8000 apparaat | Microsoft Documenten
description: Beschrijft de storSimple-apparaatmodi en legt uit hoe u Windows PowerShell voor StorSimple gebruiken om de apparaatmodus te wijzigen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 48bd909eefbaea15cf6ca2427e106ad9bc0ffbb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298748"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Gebruik het StorSimple Diagnostics Tool om problemen met apparaten uit de 8000-serie op te lossen

## <a name="overview"></a>Overzicht

De StorSimple Diagnostics tool diagnosticeert problemen met betrekking tot de status van systeem-, prestatie-, netwerk- en hardwarecomponent voor een StorSimple-apparaat. De diagnosetool kan in verschillende scenario's worden gebruikt. Deze scenario's omvatten werkbelastingplanning, het implementeren van een StorSimple-apparaat, het beoordelen van de netwerkomgeving en het bepalen van de prestaties van een operationeel apparaat. Dit artikel geeft een overzicht van de diagnostische tool en beschrijft hoe de tool kan worden gebruikt met een StorSimple-apparaat.

De diagnosetool is voornamelijk bedoeld voor on-premises apparaten uit de StorSimple 8000-serie (8100 en 8600).

## <a name="run-diagnostics-tool"></a>Diagnosegereedschap uitvoeren

Deze tool kan worden uitgevoerd via de Windows PowerShell-interface van uw StorSimple-apparaat. Er zijn twee manieren om toegang te krijgen tot de lokale interface van uw apparaat:

* [Gebruik PuTTY om verbinding te maken met de seriële console van het apparaat.](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)
* [Op afstand toegang tot de tool via de Windows PowerShell voor StorSimple](storsimple-8000-remote-connect.md).

In dit artikel gaan we ervan uit dat u via PuTTY verbinding hebt gemaakt met de seriële console van het apparaat.

#### <a name="to-run-the-diagnostics-tool"></a>Het diagnosegereedschap uitvoeren

Zodra u verbinding hebt gemaakt met de Windows PowerShell-interface van het apparaat, voert u de volgende stappen uit om de cmdlet uit te voeren.
1. Meld u aan bij de seriële console van het apparaat door de stappen te volgen in [PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Typ de volgende opdracht:

    `Invoke-HcsDiagnostics`

    Als de scopeparameter niet is opgegeven, voert de cmdlet alle diagnostische tests uit. Deze tests omvatten systeem- en hardwarecomponentstatus, netwerk en prestaties. 
    
    Als u alleen een specifieke test wilt uitvoeren, geeft u de bereikparameter op. Als u bijvoorbeeld alleen de netwerktest wilt uitvoeren, typt u

    `Invoke-HcsDiagnostics -Scope Network`

3. Selecteer en kopieer de uitvoer vanuit het PuTTY-venster naar een tekstbestand voor verdere analyse.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Scenario's voor het gebruik van het diagnosegereedschap

Gebruik het diagnoseprogramma om problemen op te lossen met de netwerk-, prestatie-, systeem- en hardwarestatus van het systeem. Hier zijn enkele mogelijke scenario's:

* **Apparaat offline** - Uw StorSimple 8000-serie apparaat is offline. Echter, van de Windows PowerShell-interface, lijkt het erop dat zowel de controllers zijn up and running.
    * U deze tool gebruiken om vervolgens de netwerkstatus te bepalen.
         
         > [!NOTE]
         > Gebruik deze tool niet om de prestaties en netwerkinstellingen op een apparaat te beoordelen vóór de registratie (of configureren via de wizard Setup). Er wordt een geldig IP-adres aan het apparaat toegewezen tijdens de wizard setup en registratie. U deze cmdlet, op een apparaat dat niet is geregistreerd, voor de gezondheid van de hardware en het systeem. Gebruik bijvoorbeeld de bereikparameter:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Permanente apparaatproblemen** - U ondervindt apparaatproblemen die lijken te blijven bestaan. Registratie faalt bijvoorbeeld. U ook apparaatproblemen ondervinden nadat het apparaat een tijdje is geregistreerd en operationeel is.
    * Gebruik deze tool in dit geval voor voorbereidende probleemoplossing voordat u een serviceaanvraag registreert bij Microsoft Support. We raden u aan deze tool uit te voeren en de uitvoer van deze tool vast te leggen. U deze uitvoer vervolgens aan Ondersteuning verstrekken om het oplossen van problemen te versnellen.
    * Als er hardwareonderdelen of clusterfouten zijn, moet u zich aanmelden bij een ondersteuningsaanvraag.

* **Lage apparaatprestaties** - Uw StorSimple-apparaat is traag.
    * Voer in dit geval deze cmdlet uit met de bereikparameter ingesteld op prestaties. Analyseer de output. U krijgt de cloud read-write latencies. Gebruik de gerapporteerde latencies als maximaal haalbare doelstelling, factor in sommige overhead voor de interne gegevensverwerking en implementeer vervolgens de workloads op het systeem. Ga voor meer informatie naar [De netwerktest gebruiken om de prestaties van het apparaat op te lossen.](#network-test)


## <a name="diagnostics-test-and-sample-outputs"></a>Diagnostische test en monsteruitgangen

### <a name="hardware-test"></a>Hardwaretest

Deze test bepaalt de status van de hardwarecomponenten, de USM-firmware en de schijffirmware die op uw systeem wordt uitgevoerd.

* De gerapporteerde hardwarecomponenten zijn onderdelen die de test hebben mislukt of die niet in het systeem aanwezig zijn.
* De FIRMWARE- en schijffirmwareversies van de USM worden gerapporteerd voor de controller 0, Controller 1 en gedeelde componenten in uw systeem. Ga voor een volledige lijst met hardwarecomponenten naar:

    * [Componenten in de primaire behuizing](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Componenten in EBOD-behuizing](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Als de hardwaretestonderdelen rapporteert, [meldt u zich aan bij een serviceaanvraag met Microsoft Support](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Voorbeelduitvoer van hardwaretests op een 8100-apparaat

Hier is een voorbeeld uitgang van een StorSimple 8100 apparaat. In het 8100-modelapparaat is de EBOD-behuizing niet aanwezig. Daarom worden de ebod-controllercomponenten niet gerapporteerd.

```
Controller0>Invoke-HcsDiagnostics -Scope Hardware
Running hardware diagnostics ...
--------------------------------------------------
Hardware components failed or not present
----------------------

           Type           State      Controller           Index     EnclosureId
           ----           -----      ----------           -----     -----------
...rVipResource      NotPresent            None               1            None
...rVipResource      NotPresent            None               2            None
...rVipResource      NotPresent            None               3            None
...rVipResource      NotPresent            None               4            None
...rVipResource      NotPresent            None               5            None
...rVipResource      NotPresent            None               6            None
...rVipResource      NotPresent            None               7            None
...rVipResource      NotPresent            None               8            None
...rVipResource      NotPresent            None               9            None
...rVipResource      NotPresent            None              10            None
...rVipResource      NotPresent            None              11            None

Firmware information
----------------------
TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

--------------------------------------------------
```

### <a name="system-test"></a>Systeemtest

Deze test rapporteert de systeeminformatie, de beschikbare updates, de clusterinformatie en de servicegegevens voor uw apparaat.

* De systeeminformatie omvat het model, het serienummer van het apparaat, de tijdzone, de status van de controller en de gedetailleerde softwareversie die op het systeem wordt uitgevoerd. Als u de verschillende systeemparameters wilt begrijpen die als uitvoer worden gerapporteerd, gaat u naar [Systeeminformatie interpreteren](#appendix-interpreting-system-information).

* De beschikbaarheid van de update rapporteert of de reguliere en onderhoudsmodi beschikbaar zijn en de bijbehorende pakketnamen. Als `RegularUpdates` `MaintenanceModeUpdates` en `false`zijn, geeft dit aan dat de updates niet beschikbaar zijn. Uw apparaat is up-to-date.
* De clusterinformatie bevat de informatie over verschillende logische componenten van alle HCS-clustergroepen en hun respectieve statussen. Als u een offline clustergroep ziet in dit gedeelte van het rapport, neemt u [contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* De service-informatie bevat de namen en statussen van alle HCS- en CiS-services die op uw apparaat worden uitgevoerd. Deze informatie is handig voor de Microsoft Support bij het oplossen van het apparaatprobleem.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Voorbeelduitvoer van systeemtests op een 8100-apparaat

Hier is een voorbeeld uitvoer van het systeem test uitgevoerd op een 8100 apparaat.

```
Controller0>Invoke-HcsDiagnostics -Scope System
Running system diagnostics ...
--------------------------------------------------

System information
----------------------
Controller0:

InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                : (UTC-08:00) Pacific Time (US & Canada)
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : Disabled
FipsMode                : Enabled

Controller1:
InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                :
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : HttpsAndHttpEnabled
FipsMode                : Enabled

Update availability
----------------------
RegularUpdates              : False
MaintenanceModeUpdates      : False
RegularUpdatesTitle         : {}
MaintenanceModeUpdatesTitle : {}

Cluster information
----------------------
Name                          State OwnerGroup
----                          ----- ----------
ApplicationHostRLUA           Online HCS Cluster Group
Data0v4                       Online HCS Cluster Group
HCS Vnic Resource             Online HCS Cluster Group
hcs_cloud_connectivity_...    Online HCS Cluster Group
hcs_controller_replacement    Online HCS Cluster Group
hcs_datapath_service          Online HCS Cluster Group
hcs_management_service        Online HCS Cluster Group
hcs_nvram_service             Online HCS Cluster Group
hcs_passive_datapath          Online HCS Passive Cluster Group
hcs_platform_service          Online HCS Cluster Group
hcs_saas_agent_service        Online HCS Cluster Group
HddDataClusterDisk            Online HCS Cluster Group
HddMgmtClusterDisk            Online HCS Cluster Group
HddReplClusterDisk            Online HCS Cluster Group
iSCSI Target Server           Online HCS Cluster Group
NvramClusterDisk              Online HCS Cluster Group
SSAdminRLUA                   Online HCS Cluster Group
SsdDataClusterDisk            Online HCS Cluster Group
SsdNvramClusterDisk           Online HCS Cluster Group

Service information
----------------------
Name                                          Status DisplayName
----                                          ------ -----------
CiSAgentSvc                                   Stopped CiS Service Agent
hcs_cloud_connectivity_...                    Running hcs_cloud_connectivity...
hcs_controller_replacement                    Running HCS Controller Replace...
hcs_datapath_service                          Running HCS Datapath Service
hcs_management_service                        Running HCS Management Service
hcs_minishell                                 Running hcs_minishell
HCS_NVRAM_Service                             Running HCS NVRAM Service
hcs_passive_datapath                          Stopped HCS Passive Datapath S...
hcs_platform_service                          Running HCS Platform Monitor S...
hcs_saas_agent_service                        Running hcs_saas_agent_service
hcs_startup                                   Stopped hcs_startup
--------------------------------------------------
```

### <a name="network-test"></a>Netwerktest

Deze test valideert de status van de netwerkinterfaces, poorten, DNS- en NTP-serverconnectiviteit, TLS/SSL-certificaat, opslagaccountreferenties, connectiviteit met de updateservers en webproxyconnectiviteit op uw StorSimple-apparaat.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Voorbeelduitvoer van netwerktest wanneer alleen DATA0 is ingeschakeld

Hier is een voorbeeld van de 8100 apparaat. U in de uitvoer zien dat:
* Alleen DATA 0- en DATA 1-netwerkinterfaces zijn ingeschakeld en geconfigureerd.
* GEGEVENS 2 - 5 zijn niet ingeschakeld in de portal.
* De DNS-serverconfiguratie is geldig en het apparaat kan verbinding maken via de DNS-server.
* De NTP-serverconnectiviteit is ook prima.
* Havens 80 en 443 zijn open. Poort 9354 is echter geblokkeerd. Op basis van de [systeemnetwerkvereisten](storsimple-system-requirements.md)moet u deze poort openen voor de communicatie van de servicebus.
* De TLS/SSL-certificering is geldig.
* Het apparaat kan verbinding maken met het opslagaccount: _myss8000storageacct_.
* De verbinding met Update-servers is geldig.
* De webproxy is niet geconfigureerd op dit apparaat.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Voorbeelduitvoer van netwerktest wanneer DATA0 en DATA1 zijn ingeschakeld

```
Controller0>Invoke-HcsDiagnostics -Scope Network
Running network diagnostics ....
--------------------------------------------------
Validating networks .....
Name                Entity              Result              Details
----                ------              ------              -------
Network interface   Data0               Valid
Network interface   Data1               Valid
Network interface   Data2               Not enabled
Network interface   Data3               Not enabled
Network interface   Data4               Not enabled
Network interface   Data5               Not enabled
DNS                 10.222.118.154      Valid
NTP                 time.windows.com    Valid
Port                80                  Open
Port                443                 Open
Port                9354                Blocked
SSL certificate     https://myss8000... Valid
Storage account ... myss8000storageacct Valid
URL                 http://download.... Valid
URL                 http://download.... Valid
Web proxy                               Not enabled         Web proxy is not...
--------------------------------------------------
```

### <a name="performance-test"></a>Prestatietest

Deze test rapporteert de cloudprestaties via de latencies voor het schrijven van cloud-schrijfresultaten voor uw apparaat. Deze tool kan worden gebruikt om een basislijn vast te stellen van de cloudprestaties die u bereiken met StorSimple. De tool rapporteert de maximale prestaties (best case scenario voor lees-schrijf latencies) die u krijgen voor uw verbinding.

Aangezien de tool de maximaal haalbare prestaties rapporteert, kunnen we de gerapporteerde lees-schrijflatencies gebruiken als doelen bij het implementeren van de workloads.

De test simuleert de blobformaten die zijn gekoppeld aan de verschillende volumetypen op het apparaat. Regelmatige gelaagde en back-ups van lokaal vastgemaakte volumes gebruiken een blobgrootte van 64 KB. Gelaagde volumes met aangevinkte archiefoptie gebruiken de grootte van 512 KB blobgegevens. Als uw apparaat gelaagde en lokaal vastgemaakte volumes heeft geconfigureerd, wordt alleen de test uitgevoerd die overeenkomt met de grootte van de 64 KB blobgegevens.

Voer de volgende stappen uit om dit gereedschap te gebruiken:

1.  Maak eerst een mix van gelaagde volumes en gelaagde volumes waarbij de gearchiveerde optie is ingeschakeld. Deze actie zorgt ervoor dat de tool de tests uitvoert voor zowel 64 KB- als 512 KB-blobformaten.

2. Voer de cmdlet uit nadat u de volumes hebt gemaakt en geconfigureerd. Type:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Noteer de lees-schrijflatencies die door het hulpprogramma worden gerapporteerd. Deze test kan enkele minuten duren voordat de resultaten worden gerapporteerd.

4. Als de verbindingslatencies allemaal onder het verwachte bereik vallen, kunnen de latencies die door het hulpprogramma worden gerapporteerd, worden gebruikt als maximaal haalbaar doel bij het implementeren van de workloads. Factor in sommige overhead voor interne gegevensverwerking.

    Als de lees-schrijflatencies die door het diagnoseprogramma worden gerapporteerd hoog zijn:

    1. Configureer Storage Analytics voor blob-services en analyseer de uitvoer om inzicht te krijgen in de latencies voor het Azure-opslagaccount. Ga voor gedetailleerde instructies naar [Het inschakelen en configureren van Storage Analytics](../storage/common/storage-enable-and-view-metrics.md). Als deze latencies ook hoog en vergelijkbaar zijn met de nummers die u hebt ontvangen van het hulpprogramma StorSimple Diagnostics, moet u een serviceaanvraag registreren met Azure-opslag.

    2. Als de latenomvan het opslagaccount laag is, neemt u contact op met uw netwerkbeheerder om eventuele latentieproblemen in uw netwerk te onderzoeken.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Voorbeelduitvoer van prestatietests op een 8100-apparaat

```
Controller0>Invoke-HcsDiagnostics -Scope Performance
Running performance diagnostics...
--------------------------------------------------
Cloud performance: writing blobs
Cloud write latency: 194 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: reading blobs..
Cloud read latency: 544 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: writing blobs.
Cloud write latency: 369 ms using credential 'myss8000storageacct', blob size '512KB'
Cloud performance: reading blobs...
Cloud read latency: 4924 ms using credential 'myss8000storageacct', blob size '512KB'
--------------------------------------------------
Controller0>
```

## <a name="appendix-interpreting-system-information"></a>Aanhangsel: interpretatie van systeeminformatie

Hier is een tabel waarin wordt beschreven wat de verschillende Windows PowerShell-parameters in de systeeminformatiekaart aan. 

| PowerShell-parameter    | Beschrijving  |
|-------------------------|------------------|
| Exemplaar-id             | Elke controller heeft een unieke id of een GUID die eraan is gekoppeld.|
| Name                    | De vriendelijke naam van het apparaat zoals geconfigureerd via de Azure-portal tijdens de implementatie van het apparaat. De standaardvriendelijke naam is het serienummer van het apparaat. |
| Model                   | Het model van uw StorSimple 8000-serie apparaat. Het model kan 8100 of 8600 zijn.|
| SerialNumber            | Het serienummer van het apparaat wordt toegewezen in de fabriek en is 15 tekens lang. 8600-SHX0991003G44HT geeft bijvoorbeeld aan:<br> 8600 – Is het apparaatmodel.<br>SHX – Is de productielocatie.<br> 0991003 - Is een specifiek product. <br> G44HT- de laatste 5 cijfers worden verhoogd om unieke serienummers te maken. Dit is mogelijk geen sequentiële set.|
| Tijdzone                | De apparaattijdzone zoals geconfigureerd in de Azure-portal tijdens de implementatie van het apparaat.|
| CurrentController (CurrentController)       | De controller waarmee u bent verbonden via de Windows PowerShell-interface van uw StorSimple-apparaat.|
| ActiveController (ActiveController)        | De controller die actief is op uw apparaat en alle netwerk- en schijfbewerkingen regelt. Dit kan Controller 0 of Controller 1 zijn.  |
| Controller0Status       | De status van Controller 0 op uw apparaat. De status van de controller kan normaal zijn, in herstelmodus of onbereikbaar.|
| Controller1Status       | De status van Controller 1 op uw apparaat.  De status van de controller kan normaal zijn, in herstelmodus of onbereikbaar.|
| SystemMode SystemMode              | De algehele status van uw StorSimple-apparaat. De status van het apparaat kan normaal zijn, onderhoud of buiten gebruik worden gesteld (komt overeen met gedeactiveerd in de Azure-portal).|
| FriendlySoftwareVersie | De vriendelijke string die overeenkomt met de apparaatsoftwareversie. Voor een systeem met Update 4 zou de vriendelijke softwareversie StorSimple 8000 Series Update 4.0 zijn.|
| HcsSoftwareVersie      | De HCS-softwareversie die op uw apparaat wordt uitgevoerd. Zo is de HCS-softwareversie die overeenkomt met StorSimple 8000 Series Update 4.0 6.3.9600.17820. |
| ApiVersie              | De softwareversie van de Windows PowerShell API van het HCS-apparaat.|
| VhdVersie              | De softwareversie van het fabrieksbeeld waarmee het apparaat is verzonden. Als u uw apparaat opnieuw instelt op standaardinstellingen in de fabriek, wordt deze softwareversie uitgevoerd.|
| OSVersion               | De softwareversie van het Windows Server-besturingssysteem die op het apparaat wordt uitgevoerd. Het StorSimple-apparaat is gebaseerd op de Windows Server 2012 R2 die overeenkomt met 6.3.9600.|
| CisAgentVersie         | De versie voor uw Cis-agent die op uw StorSimple-apparaat draait. Deze agent helpt te communiceren met de StorSimple Manager-service die in Azure wordt uitgevoerd.|
| MdsAgentVersie         | De versie die overeenkomt met de Mds-agent die op uw StorSimple-apparaat wordt uitgevoerd. Deze agent verplaatst gegevens naar de Monitoring and Diagnostics Service (MDS).|
| Lsisas2Versie          | De versie die overeenkomt met de LSI-stuurprogramma's op uw StorSimple-apparaat.|
| Capaciteit                | De totale capaciteit van het apparaat in bytes.|
| RemoteManagementMode    | Geeft aan of het apparaat op afstand kan worden beheerd via de Windows PowerShell-interface. |
| FipsMode                | Geeft aan of de FIPS-modus (Federal Information Processing Standard) van de Verenigde Staten is ingeschakeld op uw apparaat. De FIPS 140-standaard definieert cryptografische algoritmen die zijn goedgekeurd voor gebruik door computersystemen van de Amerikaanse federale overheid voor de bescherming van gevoelige gegevens. Voor apparaten met Update 4 of hoger is de FIPS-modus standaard ingeschakeld. |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over de syntaxis van de cmdlet Invoke-HcsDiagnostics](https://technet.microsoft.com/library/mt795371.aspx).

* Meer informatie over het [oplossen van implementatieproblemen](storsimple-troubleshoot-deployment.md) op uw StorSimple-apparaat.
