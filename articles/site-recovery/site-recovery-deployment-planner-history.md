---
title: Versiegeschiedenis van Azure Site Recovery Deployment Planner
description: Bekende verschillende Site Recovery Deployment Planner Versions fixes en bekende beperkingen, samen met hun release data.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 10/16/2019
ms.author: dapatil
ms.openlocfilehash: bf32809f426f3bfcabd08ec3bd95e76202aa8f84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72433408"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Versiegeschiedenis van Azure Site Recovery Deployment Planner

In dit artikel vindt u de geschiedenis van alle versies van Azure Site Recovery Deployment Planner, samen met de fixes, bekende beperkingen in elk en hun releasedatums.

## <a name="version-251"></a>Versie 2.51

**Releasedatum: 22 augustus 2019**

**Fixes:**

- Het probleem met de kostenaanbeveling met Deployment Planner-versie 2.5 opgelost

## <a name="version-25"></a>Versie 2.5

**Releasedatum: 29 juli 2019**

**Fixes:**

- Voor virtuele VMware-machines en fysieke machines wordt de aanbeveling bijgewerkt om te worden gebaseerd op replicatie naar beheerde schijven.
- Ondersteuning toegevoegd voor Windows 10 (x64), Windows 8.1 (x64), Windows 8 (x64), Windows 7 (x64) SP1 of hoger

## <a name="version-24"></a>Versie 2.4

**Releasedatum: 17 april 2019**

**Fixes:**

- Verbeterde compatibiliteit van het besturingssysteem, met name bij het verwerken van op lokalisatie gebaseerde fouten.
- VM's met maximaal 20 Mbps gegevenswijzigingssnelheid (churn) toegevoegd aan de compatibiliteitschecklist.
- Verbeterde foutberichten
- Ondersteuning toegevoegd voor vCenter 6.7.
- Ondersteuning toegevoegd voor Windows Server 2019 en Het Red Hat Enterprise Linux-workstation (RHEL).



## <a name="version-23"></a>Versie 2.3

**Releasedatum: 3 december 2018**

**Fixes:**

- Fixed an issue that prevented the Deployment Planner from generating a report with the provided target location and subscription.

## <a name="version-22"></a>Versie 2.2 

**Releasedatum: 25 april 2018**

**Fixes:**

- GetVMList-bewerkingen:
  - Fixed an issue that caused GetVMList to fail if the specified folder doesn't exist. Hiermee wordt nu de standaardmap gemaakt of wordt de map gemaakt die is opgegeven in de parameter outputfile.
  - Meer gedetailleerde faalredenen toegevoegd voor GetVMList.
- Vm-typegegevens toegevoegd als kolom in het compatibele VM-blad van het rapport Deployment Planner.
- Hyper-V naar Azure disaster recovery:
  - Uitgesloten VM's met gedeelde VHD's en PassThrough-schijven van profilering. De bewerking Startprofiling toont de lijst met uitgesloten VM's in de console.
  - VM's met meer dan 64 schijven toegevoegd aan de lijst met incompatibele VM's.
  - Bijgewerkt de eerste replicatie (IR) en delta replicatie (DR) compressiefactor.
  - Beperkte ondersteuning voor MKB-opslag toegevoegd.

## <a name="version-21"></a>Versie 2.1

**Releasedatum: 3 januari 2018**

**Fixes:**

- Het Excel-rapport bijgewerkt.
- Fixed bugs in the GetThroughput operation.
- Added option to limit the number of VM's to profile or generate the report.Added option to limit the number of VM's to profile or generate the report.Added option to limit the number of VM's to profile or generate the report De standaardlimiet is 1.000 VM's.
- VMware naar Azure disaster recovery:
  - Fixed an issue of Windows Server 2016 VM going into the incompatible table. 
  - Bijgewerkte compatibiliteitsberichten voor EFI-vm's (Extensible Firmware Interface).
- De VMware hebben bijgewerkt naar Azure en Hyper-V naar Azure, VM-limiet voor gegevensverloop per VM. 
- Verbeterde betrouwbaarheid van VM-lijst-bestandsontwering.

## <a name="version-201"></a>Versie 2.0.1

**Releasedatum: 7 december 2017**

**Fixes:**

- Extra aanbeveling om de netwerkbandbreedte te optimaliseren.

## <a name="version-20"></a>Versie 2.0

**Releasedatum: 28 november 2017**

**Fixes:**

- Ondersteuning voor Hyper-V toegevoegd aan Azure disaster recovery.
- Toegevoegde kostencalculator.
- Added OS version check for VMware to Azure disaster recovery to determine if the VM is compatible or incompatible for the protection. Het hulpprogramma maakt gebruik van de tekenreeks VAN de OS-versie die door de vCenter-server voor die VM wordt geretourneerd. Het is de gast-besturingssysteem versie die de gebruiker heeft geselecteerd tijdens het maken van de VM in VMware.

**Bekende beperkingen:**

- Voor Hyper-V naar Azure disaster recovery, VM met `,` `"`naam `[` `]`met ``` ` ``` de tekens zoals: , , , en worden niet ondersteund. Als het wordt geprofileerd, mislukt het genereren van rapporten of heeft het een onjuist resultaat.
- Vm met naam die komma bevat, wordt niet ondersteund voor VMware tot Azure-herstel na noodgevallen. Als het wordt geprofileerd, mislukt de rapportgeneratie of heeft het een onjuist resultaat.

## <a name="version-131"></a>Versie 1.3.1

**Releasedatum: 19 juli 2017** 

**Fixes:**

- Ondersteuning toegevoegd voor grote schijven (> 1 TB) bij het genereren van rapporten. Nu u Deployment Planner gebruiken om replicatie te plannen voor virtuele machines met schijfformaten van meer dan 1 TB (tot 4095 GB).
Meer informatie over [ondersteuning voor grote schijven in Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>Versie 1.3

**Releasedatum: 9 mei 2017**

**Fixes:**

- Ondersteuning toegevoegd voor beheerde schijf bij het genereren van rapporten. Het aantal VM's dat op één opslagaccount kan worden geplaatst, wordt berekend op basis van of de beheerde schijf is geselecteerd voor Failover/Test Failover.

## <a name="version-12"></a>Versie 1.2

**Releasedatum: 7 april 2017**

**Fixes:**

- Added boot type (BIOS of EFI) controleert voor elke VM om te bepalen of de VM compatibel of onverenigbaar is voor de beveiliging.
- Added OS type information for each virtual machine in the compatible VM's and incompatible VM's.Added OS type information for each virtual machine in the compatible VM's and incompatible VM's.Added OS type information for each virtual machine in the compatible VM's and incompatible VM's.
- Ondersteuning toegevoegd voor De doorvoer van GetThroughput voor de Amerikaanse overheid en China Microsoft Azure-regio's.
- Er zijn meer controles op vereisten toegevoegd voor vCenter en ESXi Server.
- Fixed an issue of incorrect report getting generated when locale settings are set to non-English.

## <a name="version-11"></a>Versie 1.1

**Releasedatum: 9 maart 2017**

**Fixes:**

- Fixed an issue that prevent profiling VM's when there are two or more VM's with the same name or IP address across various vCenter ESXi hosts.
- Fixed an issue that caused copy and search to be disabled for the compatible VM's and incompatible VM's.Fixed an issue that caused copy and search to be disabled for the compatible VM's and incompatible VM's.Fixed an issue that caused copy and search to be disabled for the compatible VM's and incompatible VM's.Fixed an

## <a name="version-10"></a>Versie 1.0

**Releasedatum: 23 februari 2017**

**Bekende beperkingen:**

- Ondersteunt alleen voor Scenario's voor herstel van VMware tot Azure-noodherstel. Voor scenario's voor herstel van de hyperv naar Azure-noodherstel gebruikt u het [hulpprogramma voor hyper-v-capaciteitsplanner](./site-recovery-capacity-planning-for-hyper-v-replication.md).
- Ondersteunt de GetThroughput-bewerking voor de Microsoft Azure-regio's van de Amerikaanse overheid en China niet.
- De tool kan vm's niet profileren als de vCenter-server twee of meer VM's heeft met dezelfde naam of IP-adres voor verschillende ESXi-hosts.
In deze versie slaat het hulpprogramma profilering over bij namen of IP-adressen van virtuele machines die dubbel voorkomen in het bestand dat is opgegeven voor VMListFile. Een tijdelijke oplossing is de virtuele machines te profileren met behulp van een ESXi-host in plaats van de vCenter-server. Zorg ervoor dat je één exemplaar uitvoert voor elke ESXi-host.
