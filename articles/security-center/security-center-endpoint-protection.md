---
title: Aanbevelingen voor endpointbeveiliging in Azure Security Centers
description: Hoe de oplossingen voor endpointbescherming worden ontdekt en geïdentificeerd als gezond.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: dcf7df501665ea3885d00b9f7668a95cbbf02428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208539"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Beoordeling van endpointbeveiliging en aanbevelingen in Azure Security Center

Azure Security Center biedt statusbeoordelingen van [ondersteunde](security-center-services.md#endpoint-supported) versies van Endpoint-beveiligingsoplossingen. In dit artikel worden de scenario's uitgelegd die security center ertoe leiden de volgende twee aanbevelingen te genereren:

* **Endpoint-beveiligingsoplossingen installeren op uw virtuele machine**
* **Gezondheidsproblemen met de beveiliging van eindpunten op uw machines oplossen**

## <a name="windows-defender"></a>Windows Defender

* Security Center raadt u aan **"Point protection solutions on virtual machine"** te installeren wanneer [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) wordt uitgevoerd en het resultaat **AMServiceEnabled: False** is

* Security Center raadt u **aan om problemen met de status van eindpuntbeveiliging op uw machines op** te lossen wanneer [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) wordt uitgevoerd en een van de volgende handelingen optreedt:

  * Een van de volgende eigenschappen is onwaar:

    **AMServiceEnabled**

    **AntispywareIngeschakeld**

    **RealTimeProtectionIngeschakeld**

    **BehaviorMonitorIngeschakeld**

    **IoavProtectionIngeschakeld**

    **OnAccessProtectionIngeschakeld**

  * Als een of beide van de volgende eigenschappen 7 of meer zijn.

    **AntispywareSignatureAge**

    **AntivirusSignatureAge AntivirusSignatureAge AntivirusSignatureAge AntivirusSignature**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center-eindpuntbeveiliging

* Security Center raadt u aan **"Point protection solutions on virtual machine"** te installeren bij het importeren van **SCEPMpModule ("$env:ProgramFiles\Microsoft Security Client\MpProvider\MpProvider.psd1")** en **get-MProtComputerStatus** resultaten met **AMServiceEnabled = false**

* Security Center raadt u **aan om problemen met de status van eindpuntbeveiliging op uw machines op** te lossen wanneer **Get-MprotComputerStatus** wordt uitgevoerd en een van de volgende handelingen optreedt:

    * Ten minste één van de volgende eigenschappen is onwaar:

            **AMServiceEnabled**

            **AntispywareEnabled**
    
            **RealTimeProtectionEnabled**
    
            **BehaviorMonitorEnabled**
    
            **IoavProtectionEnabled**
    
            **OnAccessProtectionEnabled**
          
    * Als een of beide van de volgende handtekeningupdates groter of gelijk is aan 7. 

            **AntispywareSignatureAge**
    
            **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* Security Center raadt u aan **"Point protection solutions on virtual machine installeren"** wanneer niet aan een van de volgende controles wordt voldaan:
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent** bestaat
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** bestaat
    * Het **bestand dsa_query.cmd** wordt gevonden in de installatiemap
    * **Dsa_query.cmd-resultaten** uitvoeren met **Component.AM.mode: on - Trend Micro Deep Security Agent gedetecteerd**

## <a name="symantec-endpoint-protection"></a>Symantec-eindpuntbeveiliging
Security Center raadt u aan **"Point protection solutions on virtual machine installeren"** wanneer niet aan een van de volgende controles wordt voldaan:

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

of

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Security Center raadt u **aan om problemen met de status van eindpuntbeveiliging op uw machines op** te lossen wanneer niet aan een van de volgende controles wordt voldaan:

* Symantec Version >= 12: Registerlocatie: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* Controleer de status van realtimebeveiliging: **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* Status handtekeningupdate controleren: **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 dagen**

* Controleer de status Van volledige scan: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 dagen**

* Handtekeningversienummer Path naar signature-versie zoeken voor Symantec 12: **Registerpaden+ 'CurrentVersion\SharedDefs' -Waarde "SRTSP"** 

* Pad naar handtekeningversie voor Symantec 14: **Registerpaden+ "CurrentVersion\SharedDefs\SDSDefs" -Waarde "SRTSP"**

Registerpaden:

* **"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee-eindpuntbeveiliging voor Windows

Security Center raadt u aan **"Point protection solutions on virtual machine installeren"** wanneer niet aan een van de volgende controles wordt voldaan:

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** bestaat

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Security Center raadt u **aan om problemen met de status van eindpuntbeveiliging op uw machines op** te lossen wanneer niet aan een van de volgende controles wordt voldaan:

* McAfee-versie: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* Signature Version zoeken: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwcontentmajorVersion"**

* Zoek handtekening datum: **HKLM:\Software\McAfee\AVSolution\DS\DS -Waarde "szContentCreationDate" >= 7 dagen**

* Zoekscandatum: **HKLM:\Software\McAfee\Endpoint\AV\ODS -Waarde "LastFullScanOdsRunTime" >= 7 dagen**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>McAfee Endpoint Security voor Linux Threat Prevention 

Security Center raadt u aan **"Point protection solutions on virtual machine installeren"** wanneer niet aan een van de volgende controles wordt voldaan:

- File **/opt/isec/ens/threatprevention/bin/isecav** exits 

- **"/opt/isec/ens/threatprevention/bin/isecav --version"** output is: **McAfee name = McAfee Endpoint Security for Linux Threat Prevention and McAfee version >= 10**

Security Center raadt u **aan om problemen met de status van eindpuntbeveiliging op uw machines op** te lossen wanneer niet aan een van de volgende controles wordt voldaan:

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** retourneert **Quick scan, Volledige scan** en beide scans <= 7 dagen

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** retourneert **DAT en engine Update tijd** en beiden <= 7 dagen

- **"/opt/isec/ens/threatprevention/bin/isecav --getoasconfig --summary"** retourneert **on access scanstatus**

## <a name="sophos-antivirus-for-linux"></a>Sophos Antivirus voor Linux 

Security Center raadt u aan **"Point protection solutions on virtual machine installeren"** wanneer niet aan een van de volgende controles wordt voldaan:

- Bestand **/opt/sophos-av/bin/savdstatus** exits of zoek naar aangepaste locatie **"readlink $(which savscan)"**

- **"/opt/sophos-av/bin/savdstatus --version"** retourneert sophosnaam = **Sophos Anti-Virus en versie >= 9**

Security Center raadt u **aan om problemen met de status van eindpuntbeveiliging op uw machines op** te lossen wanneer niet aan een van de volgende controles wordt voldaan:

- **"/opt/sophos-av/bin/savlog --maxage=7 | grep -i "Geplande scan . \* voltooid" | staart -1"**, geeft een waarde

- **"/opt/sophos-av/bin/savlog --maxage=7 | grep "scan voltooid"** | staart -1", geeft een waarde

- **"/opt/sophos-av/bin/savdstatus --lastupdate"** retourneert lastUpdate, die moet worden <= 7 dagen 

- **"/opt/sophos-av/bin/savdstatus -v"** is gelijk aan **"On-access scanning is running"** 

- **"/opt/sophos-av/bin/savconfig get LiveProtection"** retouren ingeschakeld

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Microsoft Antimalware-extensielogboeken zijn beschikbaar op: **%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(of PaaSAntimalware)\1.5.5.x(versie#)\CommandExecution.log**

### <a name="support"></a>Ondersteuning

Neem voor meer hulp contact op met de Azure-experts op de [FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/) Of dien een Azure-ondersteuningsincident in. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer Ondersteuning krijgen. Lees de veelgestelde vragen over [Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor informatie over het gebruik van Azure Support.