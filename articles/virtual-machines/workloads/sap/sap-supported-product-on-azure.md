---
title: 'SAP op Azure: welke SAP-software wordt ondersteund op Azure'
description: Legt uit welke SAP-software wordt ondersteund om te worden geïmplementeerd op Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0aaa13ff4d3331378cc17cd0cde29be43822397
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460786"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Welke SAP-software wordt ondersteund voor Azure-implementaties
In dit artikel wordt beschreven hoe u achterhalen welke SAP-software wordt ondersteund voor Azure-implementaties en wat de benodigde releases van het besturingssysteem of DBMS-releases zijn.

Evalueren, of uw huidige SAP-software wordt ondersteund en welke OS- en DBMS-releases worden ondersteund met uw SAP-software in Azure, u hebt toegang nodig tot:

- OPMERKINGEN voor SAP-ondersteuning
- SAP-productbeschikbaarheid Matrix



## <a name="general-restrictions-for-sap-workload"></a>Algemene beperkingen voor SAP-werkbelasting
Azure IaaS-services die kunnen worden gebruikt voor SAP-workload zijn beperkt tot x86-64- of x64-hardware. Er zijn geen Sparc- of Power CPU-aanbiedingen die van toepassing zijn op SAP-workload. Klanten die hun applicaties draaien op besturingssystemen die eigendom zijn van hardwarearchitecturen zoals IBM mainframe of AS400, of waar de besturingssystemen HP-UX, Solaris of AIX in gebruik zijn, moeten hun SAP-toepassingen, waaronder DBMS, wijzigen in een van de volgende besturingssystemen:

- Windows server 64bit voor het x86-64-platform
- SUSE linux 64bit voor het x86-64 platform
- Red hat Linux 64Bit voor het x86-64 platform
- Oracle Linux 64bit voor het x86-64 platform

In combinatie met SAP-software worden geen andere OS-releases of Linux-distributies ondersteund. Exacte details over specifieke versies en aanvragen worden later in het document gedocumenteerd.


## <a name="you-start-here"></a>Je begint hier
Het uitgangspunt voor u is [SAP support note #1928533.](https://launchpad.support.sap.com/#/notes/1928533) Als u deze SAP-notitie van boven naar beneden doorloopt, worden verschillende gebieden van ondersteunde software en VM's weergegeven

In het eerste gedeelte worden de minimumvereisten weergegeven voor het uitvoeren van releases die worden ondersteund met SAP-software in Azure VM's in het algemeen. Als u deze minimumvereisten niet bereikt en oudere versies van deze besturingssystemen uitvoert, moet u uw os-release upgraden naar een dergelijke minimale release of zelfs recentere releases. Het is juist dat Azure in het algemeen oudere versies van sommige van die besturingssystemen zou ondersteunen. Maar de beperkingen of minimale releases zoals vermeld zijn gebaseerd op tests en kwalificaties uitgevoerd en zijn niet van plan om verder terug worden uitgebreid. 


> [!NOTE]
>Er zijn een aantal specifieke VM-typen, HANA Large Instances of SAP-workloads waarvoor recentere OS-releases nodig zijn. Dergelijke gevallen worden in het hele document vermeld. Dergelijke gevallen zijn duidelijk gedocumenteerd in SAP notes of andere SAP publicaties.

In de volgende sectie worden algemene SAP-platforms weergegeven die worden ondersteund met de releases die worden ondersteund en belangrijker de SAP-kernels die worden ondersteund. Het bevat NetWeaver/ABAP of Java-stacks die worden ondersteund EN, die minimale kernelreleases nodig hebben. Recentere ABAP-stacks worden ondersteund op Azure, maar hebben geen minimale kernelreleases nodig omdat wijzigingen voor Azure zijn geïmplementeerd vanaf het begin van de ontwikkeling van de meer recente stapels

Je moet controleren:

- Of de SAP-toepassingen die u uitvoert, worden gedekt door de minimale releases vermeld. Als dit niet het zo is, moet u een nieuwe doelrelease definiëren, controleer dan in de SAP-productbeschikbaarheidsmatrix, welke builds van het besturingssysteem en DBMS-combinaties worden ondersteund met de nieuwe doelrelease. Dus, dat u kiezen voor de juiste versie van het besturingssysteem en DBMS release
- Of u uw SAP-kernels moet bijwerken in een overstap naar Azure
- Of u SAP Support Pakketten moet bijwerken. Met name Basis ondersteuningspakketten die nodig kunnen zijn voor gevallen waarin u moet overstappen naar een recentere DBMS-release


De volgende sectie gaat in meer details over andere SAP-producten en DBMS releases die worden ondersteund door SAP op Azure voor Windows en Linux. 

> [!NOTE]
> De minimale releases van de verschillende DBMS is zorgvuldig gekozen en mogelijk niet altijd weerspiegelen het hele spectrum van DBMS releases de verschillende DBMS leveranciers ondersteuning op Azure in het algemeen. Veel SAP-workloadgerelateerde overwegingen werden in aanmerking genomen om deze minimale releases te definiëren. Er is geen inspanning om oudere DBMS-releases te testen en in aanmerking te komen. 

> [!NOTE]
> De minimale releases die worden vermeld, vertegenwoordigen oudere versies van besturingssystemen en databasereleases. We raden ten zeerste aan om de meest recente releases van het besturingssysteem en databasereleases te gebruiken. In veel gevallen werd rekening gehouden met recentere besturingssysteem- en databasereleases in de public cloud en pasten ze code aan om te optimaliseren voor het uitvoeren in de openbare cloud of meer specifiek Azure

## <a name="oracle-dbms-support"></a>Ondersteuning voor Oracle DBMS
Besturingssysteem, Oracle DBMS-releases en Oracle-functionaliteit die op Azure worden ondersteund, worden specifiek vermeld in [SAP-ondersteuningsnotitie #2039619](https://launchpad.support.sap.com/#/notes/2039619). Essence uit die notitie kan worden samengevat als:

- Minimale Oracle-release die wordt ondersteund op Azure VM's die zijn gecertificeerd voor NetWeaver is Oracle 11g Release 2 Patchset 3 (11.2.0.4)
- Als gastbesturingssystemen komen alleen Windows en Oracle Linux in aanmerking. Exacte releases van het OS en de bijbehorende minimale DBMS releases worden vermeld in de notitie
- De ondersteuning van Oracle Linux strekt zich ook uit tot de Oracle DBMS-client. Dit betekent dat alle SAP-componenten, zoals dialogen van de ABAP of Java Stack, ook op Oracle Linux moeten worden uitgevoerd. Alleen SAP-componenten binnen zo'n SAP-systeem die geen verbinding zouden maken met het Oracle DBMS, zouden een ander Linux-besturingssysteem mogen draaien
- Oracle RAC wordt niet ondersteund 
- Oracle ASM wordt voor sommige gevallen ondersteund. Details staan vermeld in de notitie
- Niet-Unicode SAP-systemen worden alleen ondersteund met toepassingsservers die worden uitgevoerd met Windows Guest OS. Het gastbesturingssysteem van de DBMS kan Oracle Linux of Windows zijn. Reden voor deze beperking is duidelijk bij het controleren van de SAP Product Availability Matrix (PAM). Voor Oracle Linux heeft SAP nooit niet-Unicode SAP-kernels uitgebracht

Als u de DBMS-releases kennen die worden ondersteund met de gerichte Azure-infrastructuur, moet u de SAP-productbeschikbaarheidsmatrix controleren of de vereiste OS-releases en DBMS worden ondersteund met uw SAP-productreleases die u wilt uitvoeren. 


## <a name="sap-hana-support"></a>ONDERSTEUNING VOOR SAP HANA
In Azure zijn er twee services die kunnen worden gebruikt om hana-database uit te voeren:

- Azure Virtual Machines
- [HANA Grote Exemplaren](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

Voor het uitvoeren van SAP HANA heeft SAP meer en sterkere voorwaarden waaraan de infrastructuur moet voldoen dan voor het uitvoeren van NetWeaver of andere SAP-toepassingen en DBMS. Als gevolg hiervan komt een kleiner aantal Azure VM's in aanmerking voor het uitvoeren van de SAP HANA DBMS. De lijst met ondersteunde Azure-infrastructuur die wordt ondersteund voor SAP HANA is te vinden in de zogenaamde [SAP HANA-hardwaremap.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 

> [!NOTE]
> De eenheden die beginnen met de letter 'S' zijn [HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) units. 

> [!NOTE]
> SAP heeft geen specifieke certificering afhankelijk van de SAP HANA grote releases. In tegenstelling tot de gemeenschappelijke mening, de kolom **Certificering scenario** in de [HANA gecertificeerde IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure), de kolom maakt geen verklaring over de **HANA grote of kleine release gecertificeerd**. U moet ervan uitgaan dat alle genoemde eenheden die kunnen worden gebruikt voor HANA 1.0 en HANA 2.0, zolang de gecertificeerde besturingssysteem releases voor de specifieke eenheden worden ondersteund door HANA 1.0 releases ook. 

Voor het gebruik van SAP HANA kunnen verschillende minimale OS-releases van toepassing zijn dan voor de algemene NetWeaver-aanvragen. U moet de ondersteunde besturingssystemen voor elke eenheid afzonderlijk bekijken, omdat deze kunnen variëren. U doet dit door op elke eenheid te klikken. Meer details zullen verschijnen. Een van de genoemde details zijn de verschillende besturingssystemen die voor deze specifieke eenheid worden ondersteund.

> [!NOTE]
> Azure HANA Large Instance-eenheden zijn restrictiever met ondersteunde besturingssystemen in vergelijking met Azure VM's. Aan de andere kant kunnen Azure VM's meer recente bedrijfsreleases afdwingen als minimale releases. Dit geldt vooral voor sommige van de grotere VM-eenheden die wijzigingen in Linux-kernels vereist

Als u het ondersteunde besturingssysteem voor de Azure-infrastructuur kennen, moet u [de SAP-ondersteuningsnotitie](https://launchpad.support.sap.com/#/notes/2235581) controleren #2235581 voor de exacte SAP HANA-releases en patchniveaus die worden ondersteund met de Azure-eenheden die u target. 

> [!IMPORTANT]
> De stap van het controleren van de exacte SAP HANA releases en patch levels ondersteund is zeer belangrijk. In veel gevallen is de ondersteuning van een bepaalde OS-release afhankelijk van een specifiek patchniveau van de SAP HANA-uitvoerables.

Zoals u weet dat de specifieke HANA-releases die u uitvoeren op de gerichte Azure-infrastructuur, moet u in de SAP-productbeschikbaarheidsmatrix controleren of er beperkingen zijn met de SAP-productreleases die de HANA-releases ondersteunen die u hebt gefilterd


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>Gecertificeerde Azure VM's en HANA Large Instance-eenheden en zakelijke transactiedoorvoer
Naast het evalueren van ondersteunde releases van besturingssystemen, DBMS-releases en afhankelijke ondersteuning van SAP-softwarereleases voor Azure-infrastructuureenheden, hebt u de noodzaak om deze eenheden te kwalificeren voor de doorvoer van zakelijke transacties, die wordt uitgedrukt in de eenheid 'SAP' door Sap. Alle SAP-dimensionering is afhankelijk van SAPS-berekeningen. Bij het evalueren van bestaande SAP-systemen u meestal, met de hulp van uw infrastructuurprovider, de SAPS van de eenheden berekenen. Zowel voor de DBMS-laag als voor de toepassingslaag. In andere gevallen waarin nieuwe functionaliteit wordt gecreëerd, kan een maatoefening met SAP de vereiste SAPS-nummers voor de toepassingslaag en de DBMS-laag onthullen. Als infrastructuurprovider is Microsoft verplicht om de SAP-doorvoerkarakterisering van de verschillende eenheden die netweaver en/of HANA-gecertificeerd zijn, te leveren.

Voor Azure VM's worden deze SAPS-doorvoernummers gedocumenteerd in [SAP-ondersteuningsnotitie #1928533](https://launchpad.support.sap.com/#/notes/1928533). Voor Azure HANA Large Instance-eenheden worden de SAPS-doorvoernummers gedocumenteerd in [SAP-ondersteuningsnotitie #2316233](https://launchpad.support.sap.com/#/notes/2316233)

Als we kijken naar [SAP-ondersteuningsnota #1928533,](https://launchpad.support.sap.com/#/notes/1928533)zijn de volgende opmerkingen van toepassing:

- **Voor Azure VM's uit de M-serie en Azure VM's uit de M-serie zijn verschillende minimum-OS-versies van toepassing dan voor andere Azure VM-typen.** De vereiste voor recentere OS-releases is gebaseerd op wijzigingen die de verschillende leveranciers van het besturingssysteem moesten bieden in hun versies van het besturingssysteem om hun besturingssystemen die op de specifieke Azure VM-typen draaien mogelijk te maken of de prestaties te optimaliseren en doorvoer van SAP-workload op die VM-typen
- Er zijn twee tabellen die verschillende VM-typen opgeven. In de tweede tabel wordt de SAPS-doorvoer voor Azure VM-typen opgegeven die alleen azure-standaardopslag ondersteunen. DBMS-implementatie op de eenheden die in de tweede tabel van de notitie zijn opgegeven, wordt niet ondersteund


## <a name="other-sap-products-supported-on-azure"></a>Andere SAP-producten die op Azure worden ondersteund
In het algemeen is de veronderstelling dat met de status van hyperscale clouds zoals Azure, het grootste deel van de SAP-software moet worden uitgevoerd zonder functionele problemen in Azure. Niettemin en in tegenstelling tot private cloud visualisatie, SAP nog steeds ondersteuning voor de verschillende SAP-producten expliciet voor de verschillende hyerpscale cloud providers. Als gevolg hiervan zijn er verschillende SAP-ondersteuningsnotities die ondersteuning voor Azure voor verschillende SAP-producten aangeven. 

Voor het BI-platform Voor Bedrijfsobjecten geeft [SAP-ondersteuningsnotitie #2145537](https://launchpad.support.sap.com/#/notes/2145537) een lijst met SAP Business Objects-producten die op Azure worden ondersteund. Als er vragen zijn over componenten of combinaties van softwarereleases en OS-releases die niet lijken te worden vermeld of ondersteund en die recenter zijn dan de minimale releases die worden vermeld, moet u een SAP-ondersteuningsverzoek openen tegen het onderdeel dat u instelt ondersteuning voor.

Voor Business Objects Data Services, [SAP support note #22288344](https://launchpad.support.sap.com/#/notes/2288344) legt minimale ondersteuning van SAP Data Services op Azure uit. 

> [!NOTE]
> Zoals aangegeven in de SAP-ondersteuningsnotitie, moet u het SAP PAM inchecken om het juiste ondersteuningspakketniveau te identificeren dat op Azure moet worden ondersteund

SAP Datahub/Vora-ondersteuning in Azure Kubernetes Services (AKS) wordt beschreven in [SAP-ondersteuningsnotitie #2464722](https://launchpad.support.sap.com/#/notes/2464722)

Ondersteuning voor SAP BPC 10.1 SP08 wordt beschreven in [SAP support note #2451795](https://launchpad.support.sap.com/#/notes/2451795)

Ondersteuning voor SAP Hybris Commerce Platform 5.x en 6.x op Azure wordt beschreven in de [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud)


## <a name="next-steps"></a>Volgende stappen
Lees de volgende stappen in de [planning en implementatie van Azure Virtual Machines voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)

