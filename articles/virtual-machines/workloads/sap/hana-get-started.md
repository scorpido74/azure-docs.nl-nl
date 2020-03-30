---
title: Installatie van SAP HANA op virtuele Azure-machines | Microsoft Docs'
description: Handleiding voor de installatie van SAP HANA op Azure VM's
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: e017e082472e7a4a2fab6a2845e52d3dc7acc460
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123353"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Installatie van SAP HANA op virtuele Azure-machines
## <a name="introduction"></a>Inleiding
Met deze handleiding u de juiste resources aanwijzen om HANA in virtuele Azure-machines te implementeren. Deze handleiding wijst u naar documentatiebronnen die u moet controleren voordat u SAP HANA installeert in een Azure VM. Zodat u in staat bent om de juiste stappen uit te voeren om te eindigen met een ondersteunde configuratie van SAP HANA in Azure VM's.  

> [!NOTE]
> Deze handleiding beschrijft implementaties van SAP HANA in Azure VM's. Zie [SAP HANA (Large Instances) installeren en configureren op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)voor informatie over het implementeren van SAP HANA in grote exemplaren.
 
## <a name="prerequisites"></a>Vereisten
Deze gids gaat er ook van uit dat u bekend bent met:
* SAP HANA en SAP NetWeaver en hoe ze on-premises te installeren.
* Sap HANA- en SAP-toepassingsexemplaren op Azure installeren en beheren.
* De begrippen en procedures gedocumenteerd in:
   * Planning voor SAP-implementatie op Azure, waaronder Azure Virtual Network-planning en Azure Storage-gebruik. Zie [SAP NetWeaver op Azure Virtual Machines - Plannings- en implementatiehandleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
   * Implementatieprincipes en manieren om VM's in Azure te implementeren. Bekijk [azure virtual machines-implementatie voor SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
   * Concepten met hoge beschikbaarheid voor SAP HANA zoals gedocumenteerd in [SAP HANA hoge beschikbaarheid voor Azure virtuele machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)

## <a name="step-by-step-before-deploying"></a>Stap voor stap voordat u de implementatie implementeert
In deze sectie worden de verschillende stappen weergegeven die u moet uitvoeren voordat u begint met de installatie van SAP HANA in een virtuele Azure-machine. De volgorde wordt opgesomd en als zodanig moet worden gevolgd door als opgesomd:

1. Niet alle mogelijke implementatiescenario's worden ondersteund op Azure. Controleer daarom de door Azure ondersteunde [document-SAP-werkbelasting op door Azure ondersteunde scenario's](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) voor het scenario dat u in gedachten hebt met uw SAP HANA-implementatie. Als het scenario niet wordt vermeld, moet u ervan uitgaan dat het niet is getest en als gevolg daarvan niet wordt ondersteund
2. Ervan uitgaande dat u een ruw idee hebt over uw geheugenvereisten voor uw SAP HANA-implementatie, moet u een geschikte Azure VM vinden. Niet alle VM's die zijn gecertificeerd voor SAP NetWeaver, zoals gedocumenteerd in [SAP support note #1928533,](https://launchpad.support.sap.com/#/notes/1928533)zijn SAP HANA-gecertificeerd. De bron van de waarheid voor SAP HANA gecertificeerde Azure VM's is de website [SAP HANA hardware directory](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). De eenheden die beginnen met **S** zijn [HANA Large Instances-eenheden](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) en geen Azure VM's.
3. Verschillende Azure VM-typen hebben verschillende minimale versies van besturingssystemen voor SUSE Linux of Red Hat Linux. Op de website [SAP HANA hardware directory](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure), moet u klikken op een vermelding in de lijst van SAP HANA gecertificeerde eenheden om gedetailleerde gegevens van dit apparaat te krijgen. Naast de ondersteunde HANA-workload worden de OS-releases die worden ondersteund met deze eenheden voor SAP HANA vermeld
4. Met betrekking tot het besturingssysteem releases, moet u rekening houden met bepaalde minimale kernel releases. Deze minimale releases worden gedocumenteerd in deze SAP-ondersteuningsnotities:
    - [SAP-ondersteuningsnotitie #2814271 SAP HANA Backup mislukt op Azure met Checksum-fout](https://launchpad.support.sap.com/#/notes/2814271)
    - [SAP-ondersteuningsnotitie #2753418 potentiële prestatiedegradatie als gevolg van timerterugval](https://launchpad.support.sap.com/#/notes/2753418)
    - [SAP-ondersteuningsnotitie #2791572 prestatiedegradatie vanwege ontbrekende VDSO-ondersteuning voor Hyper-V in Azure](https://launchpad.support.sap.com/#/notes/2791572)
4. Op basis van de OS-release die wordt ondersteund voor het type virtuele machine, moet u controleren of uw gewenste SAP HANA-release wordt ondersteund met de release van het besturingssysteem. Lees [SAP support note #2235581](https://launchpad.support.sap.com/#/notes/2235581) voor een support matrix van SAP HANA releases met de verschillende Operating System releases.
5. Aangezien u mogelijk een geldige combinatie van Azure VM-type, release van het besturingssysteem en SAP HANA-release hebt gevonden, moet u de SAP-productbeschikbaarheidsmatrix inchecken. In de SAP Availability Matrix u nagaan of het SAP-product dat u wilt uitvoeren tegen uw SAP HANA-database wordt ondersteund.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>Stapsgewijze VM-implementatie en overwegingen van gastbesturingssysteem
In deze fase moet u de stappen doorlopen die de VM(s) implementeren om HANA te installeren en uiteindelijk het gekozen besturingssysteem na de installatie te optimaliseren.

1. Kies de basisafbeelding uit de Azure-galerie. Als u uw eigen besturingssysteemafbeelding voor SAP HANA wilt bouwen, moet u alle verschillende pakketten kennen die nodig zijn voor een succesvolle SAP HANA-installatie. Anders wordt aanbevolen om de SUSE- en Red Hat-afbeeldingen voor SAP of SAP HANA uit de Azure-afbeeldingsgalerie te gebruiken. Deze afbeeldingen bevatten de pakketten die nodig zijn voor een succesvolle HANA-installatie. Op basis van uw ondersteuningscontract met de besturingssysteemprovider moet u een afbeelding kiezen waarbij u uw eigen licentie meeneemt. Of u kiest een OS-afbeelding met ondersteuning
2. Als u een gast-OS-afbeelding hebt gekozen waarvoor u uw eigen licentie moet meenemen, moet u de OS-afbeelding registreren met uw abonnement, zodat u de nieuwste patches downloaden en toepassen. Deze stap zal openbare internettoegang vereisen. Tenzij u uw privé-exemplaar van bijvoorbeeld een SMT-server in Azure instelt.
3. Bepaal de netwerkconfiguratie van de VM. U meer informatie lezen in de configuratie en bewerkingen van de [SAP HANA-infrastructuur op Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) Houd er rekening mee dat er geen netwerkdoorvoerquota zijn die u toewijzen aan virtuele netwerkkaarten in Azure. Als gevolg hiervan is het enige doel van het leiden van verkeer via verschillende vNICs gebaseerd op beveiligingsoverwegingen. We vertrouwen erop dat u een ondersteunend compromis vindt tussen de complexiteit van verkeersroutering door meerdere vNIC's en de vereisten die worden afgedwongen door beveiligingsaspecten.
3. Pas de nieuwste patches toe op het besturingssysteem zodra de VM is geïmplementeerd en geregistreerd. Geregistreerd met uw eigen abonnement. Of in het geval u een afbeelding die het besturingssysteem ondersteuning omvat de VM moet toegang hebben tot de patches al. 
4. Breng de deuntjes toe die nodig zijn voor SAP HANA. Deze nummers staan vermeld in deze SAP-ondersteuningsnotities:

    - [SAP support note #2694118 - Red Hat Enterprise Linux HA Add-On op Azure](https://launchpad.support.sap.com/#/notes/2694118)
    - [SAP support note #1984787 - SUSE LINUX Enterprise Server 12: Installatienotities](https://launchpad.support.sap.com/#/notes/1984787) 
    - [SAP-ondersteuningsnotitie #2578899 - SUSE Linux Enterprise Server 15: Installatienotitie](https://launchpad.support.sap.com/#/notes/2578899)
    - [SAP support note #2002167 - Red Hat Enterprise Linux 7.x: Installatie en upgrade](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP support note #2292690 - SAP HANA DB: Aanbevolen OS-instellingen voor RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690) 
    -  [SAP-ondersteuningsnotitie #2772999 - Red Hat Enterprise Linux 8.x: Installatie en configuratie](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [SAP support note #2777782 - SAP HANA DB: Aanbevolen OS-instellingen voor RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
    -  [SAP support note #2455582 - Linux: Running SAP applicaties compiled with GCC 6.x SAP support note - Linux: Running SAP applications compiled with GCC 6.x SAP support note - Linux: Running SAP applications compiled with GCC 6.x SAP support note -](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [SAP-ondersteuningsnotitie #2382421 - De netwerkconfiguratie optimaliseren op HANA- en OS-niveau](https://launchpad.support.sap.com/#/notes/2382421)

1. Selecteer het Azure-opslagtype voor SAP HANA. In deze stap moet u beslissen over de opslaglay-out voor SAP HANA-installatie. U gaat aangesloten Azure-schijven of native Azure NFS-shares gebruiken. De Azure-opslagtypen die of ondersteund worden en combinaties van verschillende Azure-opslagtypen die kunnen worden gebruikt, worden gedocumenteerd in [SAP HANA Azure-opslagconfiguraties](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)voor virtuele machines. Neem de configuraties gedocumenteerd als uitgangspunt. Voor niet-productiesystemen u mogelijk een lagere doorvoer of IOPS configureren. Voor productiedoeleinden moet u mogelijk een beetje meer doorvoer en IOPS configureren.
2. Zorg ervoor dat u [Azure Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) hebt geconfigureerd voor uw volumes die de DBMS-transactielogboeken bevatten of logboeken opnieuw uitvoeren wanneer u VM's uit de M- of Mv2-serie gebruikt. Wees je bewust van de beperkingen voor Write Accelerator zoals gedocumenteerd.
2. Controleer of [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) is ingeschakeld op de geïmplementeerde VM's.

> [!NOTE]
> Niet alle opdrachten in de verschillende sap-tuneprofielen of zoals beschreven in de notities kunnen succesvol worden uitgevoerd op Azure. Opdrachten die de energiemodus van VM's zouden manipuleren, worden meestal met een fout weergegeven, omdat de energiemodus van de onderliggende Azure-hosthardware niet kan worden gemanipuleerd.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Stapsgewijze voorbereidingen die specifiek zijn voor virtuele Azure-machines
Een van de Azure-specificaties is de installatie van een Azure VM-extensie die bewakingsgegevens levert voor de SAP Host Agent. De details over de installatie van deze monitoring extensie zijn gedocumenteerd in:

-  [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) bespreekt SAP-verbeterde monitoring met Linux VM's op Azure 
-  [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) bespreekt informatie over SAPOSCOL op Linux 
-  [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) bespreekt de belangrijkste monitoringstatistieken voor SAP op Microsoft Azure
-  [Azure Virtual Machines-implementatie voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>SAP HANA installatie
Met de Azure virtuele machines geïmplementeerd en de besturingssystemen geregistreerd en geconfigureerd, u SAP HANA installeren volgens de SAP-installatie. Als een goede start om naar deze documentatie, beginnen met deze SAP website [HANA middelen](https://www.sap.com/products/hana/implementation/resources.html)

Lees de details in de configuratie en bewerkingen van de [SAP HANA-infrastructuur op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#configuring-azure-infrastructure-for-sap-hana-scale-out) voor SAP HANA-scale-outconfiguraties met direct verbonden schijven van Azure Premium Storage of Ultra-schijf


## <a name="additional-resources-for-sap-hana-backup"></a>Extra bronnen voor SAP HANA-back-up
Zie voor informatie over het maken van back-ups van SAP HANA-databases in Azure VM's:
* [Back-uphandleiding voor SAP HANA op Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [SAP HANA Azure Backup op bestandsniveau](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)

## <a name="next-steps"></a>Volgende stappen
Lees de documentatie:

- [Configuraties en bewerkingen van SAP HANA-infrastructuur in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Configuraties van SAP HANA in virtuele Azure-machineopslag](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)





