---
title: Detectie van Endpoint Protection-oplossingen en status beoordeling in Azure Security Center | Microsoft Docs
description: Hoe de Endpoint Protection-oplossingen worden gedetecteerd en in orde worden geïdentificeerd.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2019
ms.author: v-mohabe
ms.openlocfilehash: a5cd0f88173abb65a120aa305206505af51d9f9e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861367"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Endpoint Protection-evaluatie en aanbevelingen in Azure Security Center

Endpoint Protection-evaluatie en aanbevelingen in Azure Security Center detecteert en geeft de status beoordeling van [ondersteunde](https://docs.microsoft.com/azure/security-center/security-center-os-coverage) versies van Endpoint Protection-oplossingen. In dit onderwerp worden de scenario's beschreven voor het genereren van de volgende twee aanbevelingen voor Endpoint Protection-oplossingen door Azure Security Center.

* **Endpoint Protection-oplossingen op uw virtuele machine installeren**
* **Problemen met de status van Endpoint Protection op uw computers oplossen**

## <a name="windows-defender"></a>Windows Defender

* De aanbeveling **Endpoint Protection-oplossingen op de virtuele machine installeren** wordt gegenereerd wanneer [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) wordt uitgevoerd en het resultaat **AMServiceEnabled is: Terecht**

* De aanbeveling **Endpoint Protection-status problemen op uw computers oplossen** wordt gegenereerd wanneer [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) wordt uitgevoerd en een of beide van de volgende situaties zich voordoen:

  * Ten minste één van de volgende eigenschappen is onwaar:

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * Als een of beide van de volgende eigenschappen groter zijn dan of gelijk zijn aan 7.

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Micro soft System Center Endpoint Protection

* De aanbeveling **Endpoint Protection-oplossingen op virtuele machine installeren** wordt gegenereerd bij het importeren van **SCEPMpModule (' $env:P rogramfiles\microsoft Security Client\MpProvider\MpProvider.psd1 ')** en wordt uitgevoerd  **Get-MProtComputerStatus** resultaten met **AMServiceEnabled = False**

* De aanbeveling **Endpoint Protection-status problemen op uw computers oplossen** wordt gegenereerd wanneer **Get-MprotComputerStatus** wordt uitgevoerd en een of beide van de volgende situaties zich voordoen:

    * Ten minste één van de volgende eigenschappen is onwaar:

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * Als een of beide van de volgende handtekening updates groter of gelijk zijn aan 7. 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* De aanbeveling **Endpoint Protection-oplossingen op de virtuele machine installeren** wordt gegenereerd als er niet aan een of meer van de volgende controles is voldaan:
    * **HKLM: \ SOFTWARE\TrendMicro\Deep Security-Agent** bestaat
    * **HKLM: \ SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** bestaat
    * Het bestand **dsq_query. cmd** bevindt zich in de installatiemap
    * Resultaten van **dsa_query. cmd** uitvoeren met **component. am. mode: on-trend micro diepe beveiligings agent gedetecteerd**

## <a name="symantec-endpoint-protection"></a>Symantec Endpoint Protection
De aanbeveling **Endpoint Protection-oplossingen installeren op virtuele machine** wordt gegenereerd als aan een van de volgende controles niet wordt voldaan:

* **HKLM: \ Software\Symantec\Symantec endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

of

* **HKLM: \ Software\Wow6432Node\Symantec\Symantec endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

De aanbeveling **Endpoint Protection-status problemen op uw computers oplossen** wordt gegenereerd als aan een van de volgende controles niet wordt voldaan:  

* Raadpleeg Symantec Version > = 12:  Register locatie: **HKLM: \ Software\Symantec\Symantec endpoint Protection\CurrentVersion "-waarde" PRODUCTVERSION "**

* Controleer de status van de realtime-beveiliging: **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* Status van handtekening update controleren: **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 days**

* Volledige scan status controleren: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 days**

* Pad naar handtekening versie nummer zoeken naar versie van hand tekening voor Symantec 12: **Register paden + "CurrentVersion\SharedDefs"-waarde "SRTSP"** 

* Pad naar versie van de hand tekening voor Symantec 14: **Register paden + "CurrentVersion\SharedDefs\SDSDefs"-waarde "SRTSP"**

Register paden:

* **"HKLM: \ Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee Endpoint Protection voor Windows

De aanbeveling **Endpoint Protection-oplossingen op virtuele machine installeren** wordt gegenereerd als aan de volgende controles niet wordt voldaan:

* **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion** bestaat

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

De aanbeveling **Endpoint Protection-status problemen op uw computers oplossen** wordt gegenereerd als er niet aan de volgende controles wordt voldaan:

* McAfee-versie: **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion > = 10**

* Handtekening versie zoeken: **HKLM: \ Software\McAfee\AVSolution\DS\DS-waarde "dwContentMajorVersion"**

* Handtekening datum zoeken: **HKLM: \ Software\McAfee\AVSolution\DS\DS-waarde "szContentCreationDate" > = 7 dagen**

* Zoek scan datum: **HKLM: \ Software\McAfee\Endpoint\AV\ODS-waarde "LastFullScanOdsRunTime" > = 7 dagen**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>McAfee Endpoint Security voor Linux Threat Prevention 

De aanbevelingen **voor het installeren van Endpoint Protection op** de aanbeveling van de virtuele machine worden gegenereerd als er niet aan een van de volgende controles wordt voldaan:  

- Bestands **/opt/ISEC/ens/threatprevention/bin/isecav** afsluiten 

- de uitvoer van **/opt/ISEC/ens/threatprevention/bin/isecav--version** is: **McAfee name = McAfee Endpoint Security voor Linux Threat Prevention en McAfee-versie > = 10**

De aanbevelingen **voor het oplossen van Endpoint Protection-status problemen op uw computers** worden gegenereerd als er niet aan een of meer van de volgende controles wordt voldaan:

- **'/opt/ISEC/ens/threatprevention/bin/isecav--listtask '** retourneert **snelle scan, volledige scan** en beide scans < = 7 dagen

- **"/opt/ISEC/ens/threatprevention/bin/isecav--listtask"** retourneert **dat-en Engine-update tijdstip** en beide < = 7 dagen

- **"/opt/ISEC/ens/threatprevention/bin/isecav--getoasconfig--Summary"** retourneert de status **van de toegangs controle**

## <a name="sophos-antivirus-for-linux"></a>Sophos Anti Virus voor Linux 

De aanbevelingen **voor het installeren van Endpoint Protection op** de aanbeveling van de virtuele machine worden gegenereerd als er niet aan een van de volgende controles wordt voldaan:

- Bestand **/opt/Sophos-AV/bin/savdstatus** wordt afgesloten of zoekt naar een aangepaste locatie **' readlink $ (wat savscan) '**

- **"/opt/Sophos-AV/bin/savdstatus--version"** retourneert Sophos name = **Sophos Anti-virus-en sophos-versie > = 9**

De aanbevelingen **voor het oplossen van Endpoint Protection-status problemen op uw computers** worden gegenereerd als er niet aan een of meer van de volgende controles wordt voldaan:

- **"/opt/Sophos-AV/bin/savlog--maxAge = 7 | grep-i "geplande scan. "\* | staart 1" is voltooid**, retourneert een waarde   

- **"/opt/Sophos-AV/bin/savlog--maxAge = 7 | grep "scannen voltooid"** | ' staart 1 ', retourneert een waarde   

- **"/opt/Sophos-AV/bin/savdstatus--Last update"** retourneert Last update die moet < = 7 dagen 

- **'/opt/Sophos-AV/bin/savdstatus-v '** is gelijk aan **' on-Access scanning ' wordt uitgevoerd '** 

- **'/opt/Sophos-AV/bin/savconfig Get LiveProtection '** retourneert ingeschakeld  

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Micro soft antimalware-extensie logboeken zijn beschikbaar op:  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>Ondersteuning

Als u hulp nodig hebt op elk gewenst moment in dit artikel, u kunt contact opnemen met de Azure-experts op het [forums voor Azure MSDN en Stack Overflow](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteuning van Azure site](https://azure.microsoft.com/support/options/) en selecteer Get-ondersteuning. Voor meer informatie over het gebruik van ondersteuning voor Azure, de [Veelgestelde vragen over Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/).
