---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/12/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 6668d9753d0b93ab907d37cdeff8315f488cff7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73935888"
---
**Laatste documentupdate**: 12 november 2019 10:00 AM PST.

De openbaarmaking van een [nieuwe klasse van CPU kwetsbaarheden](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) bekend als speculatieve uitvoering side-channel aanvallen heeft geresulteerd in vragen van klanten op zoek naar meer duidelijkheid.  

Microsoft heeft oplossingen geïmplementeerd voor al onze cloudservices. De infrastructuur die Azure uitvoert en klantworkloads van elkaar isoleert, is beveiligd. Dit betekent dat een potentiële aanvaller met dezelfde infrastructuur uw toepassing niet kan aanvallen met behulp van deze kwetsbaarheden.

Azure gebruikt waar mogelijk [geheugenbehoud,](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot) om de impact van de klant te minimaliseren en de noodzaak van opnieuw opstarten te elimineren. Azure zal deze methoden blijven gebruiken bij het maken van systeembrede updates voor de host en onze klanten beschermen.

Meer informatie over hoe beveiliging is geïntegreerd in elk aspect van Azure is beschikbaar op de [Azure Security Documentation-site.](https://docs.microsoft.com/azure/security/) 

> [!NOTE] 
> Sinds dit document voor het eerst werd gepubliceerd, zijn meerdere varianten van deze kwetsbaarheidsklasse bekendgemaakt. Microsoft blijft zwaar investeren in het beschermen van onze klanten en het geven van begeleiding. Deze pagina zal worden bijgewerkt als we doorgaan met verdere fixes vrij te geven. 
> 
> Op 12 november 2019 [publiceerde Intel](https://software.intel.com/security-software-guidance/insights/deep-dive-intel-transactional-synchronization-extensions-intel-tsx-asynchronous-abort) een technisch advies rond Intel® Transactional Synchronization Extensions (Intel® TSX) Transaction Asynchronous Abort (TAA) kwetsbaarheid die [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135)toegewezen krijgt. Dit beveiligingslek treft Intel® Core®-processors en Intel® Xeon®-processors.  Microsoft Azure heeft updates van het besturingssysteem uitgebracht en implementeert nieuwe microcode, zoals deze beschikbaar wordt gesteld door Intel, in onze hele vloot om onze klanten te beschermen tegen deze nieuwe kwetsbaarheden.   Azure werkt nauw samen met Intel om de nieuwe microcode te testen en te valideren voordat de officiële release op het platform wordt uitgebracht. 
>
> **Klanten die niet-vertrouwde code binnen hun VM uitvoeren,** moeten actie ondernemen om deze kwetsbaarheden te beschermen door hieronder aanvullende richtlijnen te lezen voor alle kwetsbaarheden in de speculatieve uitvoering (Microsoft Advisories ADV [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)en [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Andere klanten moeten deze kwetsbaarheden evalueren vanuit een Defense in Depth perspectief en rekening houden met de beveiligings- en prestatieimplicaties van hun gekozen configuratie.
> 



## <a name="keeping-your-operating-systems-up-to-date"></a>Uw besturingssystemen up-to-date houden

Hoewel een OS-update niet vereist is om uw toepassingen die op Azure worden uitgevoerd, te isoleren van andere Azure-klanten, is het altijd een goede gewoonte om uw software up-to-date te houden. De nieuwste beveiligingsrollups voor Windows bevatten oplossingen voor verschillende speculatieve uitvoeringsside channel kwetsbaarheden. Op dezelfde manier hebben Linux-distributies meerdere updates uitgebracht om deze kwetsbaarheden op te los te komen. Hier volgen onze aanbevolen acties om uw besturingssysteem bij te werken:

| Aanbod | Aanbevolen actie  |
|----------|---------------------|
| Azure Cloud Services  | Schakel [automatische update in](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) of zorg ervoor dat u het nieuwste gastbesturingssysteem uitvoert. |
| Azure Linux virtuele machines | Installeer updates van uw besturingssysteemprovider. Zie [Linux](#linux) later in dit document voor meer informatie. |
| Azure Windows Virtuele Machines  | Installeer de nieuwste beveiligingsrollup.
| Andere Azure PaaS-services | Er is geen actie nodig voor klanten die deze services gebruiken. Azure houdt uw OS-versies automatisch up-to-date. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Aanvullende richtlijnen als u niet-vertrouwde code uitvoert 

Klanten die niet-vertrouwde gebruikers toestaan willekeurige code uit te voeren, kunnen een aantal extra beveiligingsfuncties implementeren in hun Azure Virtual Machines of Cloud Services. Deze functies beschermen tegen de intra-proces openbaarmaking vectoren die verschillende speculatieve uitvoering kwetsbaarheden beschrijven.

Voorbeeldscenario's waarin extra beveiligingsfuncties worden aanbevolen:

- U staat toe dat code die u niet vertrouwt, in uw VM wordt uitgevoerd.  
    - *U staat bijvoorbeeld toe dat een van uw klanten een binaire of script uploadt dat u vervolgens uitvoert binnen uw toepassing.* 
- U staat gebruikers die u niet vertrouwt toe om zich aan te melden bij uw VM met accounts met een laag bevoorrecht account.   
    - *U bijvoorbeeld een gebruiker met een lage bevoorrechte toegang aanmelden bij een van uw VM's met behulp van extern bureaublad of SSH.*  
- U geeft niet-vertrouwde gebruikers toegang tot virtuele machines die zijn geïmplementeerd via geneste virtualisatie.  
    - *U beheert bijvoorbeeld de Hyper-V-host, maar wijst de VM's toe aan niet-vertrouwde gebruikers.* 

Klanten die geen scenario met niet-vertrouwde code implementeren, hoeven deze extra beveiligingsfuncties niet in te schakelen. 

## <a name="enabling-additional-security"></a>Extra beveiliging inschakelen 

U extra beveiligingsfuncties in uw VM of CloudService inschakelen als u niet-vertrouwde code uitvoert. Zorg er tegelijkertijd voor dat uw besturingssysteem up-to-date is om beveiligingsfuncties in uw VM- of cloudservice mogelijk te maken

### <a name="windows"></a>Windows 

Uw doelbesturingssysteem moet up-to-date zijn om deze extra beveiligingsfuncties in te schakelen. Hoewel tal van speculatieve uitvoeringszijkanaaloplossingen standaard zijn ingeschakeld, moeten de hier beschreven extra functies handmatig worden ingeschakeld en kunnen ze een impact hebben op de prestaties. 


**Stap 1: Schakel hyperthreading op de VM uit** - Klanten die niet-vertrouwde code uitvoeren op een hyperthreaded VM, moeten hyperthreading uitschakelen of naar een niet-hyperthread-vm-grootte gaan. Raadpleeg [dit document](https://docs.microsoft.com/azure/virtual-machines/windows/acu) voor een lijst met hyperthread-VM-formaten (waarbij de verhouding tussen vCPU en Core 2:1 is). Als u wilt controleren of uw VM hyperthreading heeft ingeschakeld, raadpleegt u het onderstaande script met de opdrachtregel Windows vanuit de VM.

Typ `wmic` om de interactieve interface in te voeren. Typ vervolgens de onderstaande om de hoeveelheid fysieke en logische processors op de VM te bekijken.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Als het aantal logische processors groter is dan fysieke processors (cores), is hyperthreading ingeschakeld.  Als u een hyperthreaded VM uitvoert, neemt u [contact op met Azure Support](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) om hyperthreading uitgeschakeld te krijgen.  Zodra hyperthreading is uitgeschakeld, **moet ondersteuning een volledige VM-reboot vereisen.** Raadpleeg [Core count](#core-count) om te begrijpen waarom het aantal VM-cores is gedaald.


**Stap 2**: Volg parallel aan stap 1 de instructies in [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) om te controleren of beveiligingen zijn ingeschakeld met behulp van de [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell-module.

> [!NOTE]
> Als u deze module eerder hebt gedownload, moet u de nieuwste versie installeren.
>


De uitvoer van het PowerShell-script moet de onderstaande waarden hebben om ingeschakelde beveiligingen tegen deze kwetsbaarheden te valideren:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
Windows OS support for TAA mitigation is enabled: True
```

Als de `MDS mitigation is enabled: False`uitvoer wordt weergegeven, neemt u [contact op met Azure Support](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) voor beschikbare mitigatieopties.



**Stap 3**: Volg de instructies in [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) om ondersteuning voor kernel virtual address shadowing (KVAS) `Session Manager` en Branch Target Injection (BTI) in te schakelen om beveiligingen met behulp van de registersleutels in te schakelen. Een reboot is vereist.


**Stap 4**: Voor implementaties die [geneste virtualisatie](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) gebruiken (alleen D3 en E3): deze instructies zijn van toepassing in de VM die u gebruikt als Hyper-V-host.

1.  Volg de instructies in [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) om `MinVmVersionForCpuBasedMitigations` beveiligingen met behulp van de registersleutels in te schakelen.
2.  Stel het type hypervisorplanner in door de instructies hier te `Core` [volgen.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types)


### <a name="linux"></a>Linux

<a name="linux"></a>Als u de set extra beveiligingsfuncties binnenin inschakelt, moet het beoogde besturingssysteem volledig up-to-date zijn. Sommige oplossingen zijn standaard ingeschakeld. In de volgende sectie worden de functies beschreven die standaard zijn uitgeschakeld en/of afhankelijk zijn van hardwareondersteuning (microcode). Het inschakelen van deze functies kan een impact hebben op de prestaties. Raadpleeg de documentatie van uw besturingssysteemprovider voor verdere instructies


**Stap 1: Schakel hyperthreading op de VM uit** - Klanten die niet-vertrouwde code uitvoeren op een hyperthreaded VM, moeten hyperthreading uitschakelen of naar een niet-hyperthread-vm gaan.  Raadpleeg [dit document](https://docs.microsoft.com/azure/virtual-machines/linux/acu) voor een lijst met hyperthread-VM-formaten (waarbij de verhouding tussen vCPU en Core 2:1 is). Als u wilt controleren of u een hyperthread-VM uitvoert, voert u de `lscpu` opdracht uit in de Linux-VM. 

Als `Thread(s) per core = 2`, dan is hyper-threading ingeschakeld. 

Als `Thread(s) per core = 1`, dan hyper-threading is uitgeschakeld. 

 
Voorbeelduitvoer voor een VM met hyperthreadingeschakeld: 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

Als u een hyperthreaded VM uitvoert, neemt u [contact op met Azure Support](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) om hyperthreading uitgeschakeld te krijgen.  Zodra hyperthreading is uitgeschakeld, **moet ondersteuning een volledige VM-reboot vereisen.** Raadpleeg [Core count](#core-count) om te begrijpen waarom het aantal VM-cores is gedaald.



**Stap 2**: Om een van de onderstaande speculatieve uitvoering side-channel kwetsbaarheden te beperken, raadpleegt u de documentatie van uw besturingssysteemprovider:   
 
- [Redhat en CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>Aantal kernen

Wanneer een hyperthread-VM wordt gemaakt, wijst Azure 2 threads per core toe- dit worden vCPU's genoemd. Wanneer hyperthreading is uitgeschakeld, verwijdert Azure een thread en surfaces single threaded cores (fysieke cores). De verhouding van vCPU naar CPU is 2:1, dus zodra hyper-threading is uitgeschakeld, zal het AANTAL CPU's in de VM met de helft zijn afgenomen. Een D8_v3 VM is bijvoorbeeld een hyperthreadVM die draait op 8 vCPU's (2 threads per core x 4 cores).  Wanneer hyperthreading is uitgeschakeld, zullen CPU's dalen tot 4 fysieke kernen met 1 thread per core. 

## <a name="next-steps"></a>Volgende stappen

Dit artikel biedt richtlijnen voor de onderstaande speculatieve uitvoering side-channel aanvallen die van invloed zijn op veel moderne processors:

[Spectre Meltdown:](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002)
- CVE-2017-5715 - Branch Target Injection (BTI)  
- CVE-2017-5754 - Kernel Page Table Isolation (KPTI)
- CVE-2018-3639 – Speculatieve winkelbypass (KPTI) 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) – Windows Kernel Information – variant van Spectre Variant 1
 
[L1 Terminal Fault (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615 - Intel Software Guard Extensions (Intel SGX)
- CVE-2018-3620 - Besturingssystemen (OS) en Systeembeheermodus (SMM)
- CVE-2018-3646 – effecten Virtual Machine Manager (VMM)

[Microarchitecturale gegevensbemonstering](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE-2019-11091 - Microarchitecturale gegevensbemonstering Uncacheable Memory (MDSUM)
- CVE-2018-12126 - Microarchitecturale opslagbuffergegevensbemonstering (MSBDS)
- CVE-2018-12127 - Microarchitectural Load Port Data Sampling (MLPDS)
- CVE-2018-12130 - Microarchitecturale vulbuffergegevensbemonstering (MFBDS)

Transactionele synchronisatie-extensies (Intel® TSX) Transactie Asynchrone aborteren:  
- [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135) – TSX Transaction Asynchronous Abort (TAA)








