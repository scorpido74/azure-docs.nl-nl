---
title: SAP ASE Azure Virtual Machines DBMS-implementatie voor SAP-workload | Microsoft Documenten
description: DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor SAP ASE
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 007e8d87c670376ad334c1c4e58fd93995930b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616240"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor SAP ASE

In dit document bestrijkt u verschillende gebieden waar u rekening mee moet houden bij de implementatie van SAP ASE in Azure IaaS. Als voorwaarde voor dit document moet u de [DBMS-implementatie van document Overwegingen voor Azure Virtual Machines DBMS voor SAP-workload](dbms_guide_general.md) en andere hulplijnen in de [SAP-werkbelasting op Azure-documentatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)hebben gelezen. Dit document heeft betrekking op SAP ASE dat draait op Linux en op Windows-besturingssystemen. De minimaal ondersteunde release op Azure is SAP ASE 16.0 Patch Level 2.  Het wordt aanbevolen om de nieuwste versie van SAP en het nieuwste patchniveau te implementeren.  Als minimum wordt SAP ASE 16.3 Patch Level 7 aanbevolen.  De meest recente versie van SAP is te vinden in [Targeted ASE 16.0 Release Schedule en CR lijst Informatie](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information).

Aanvullende informatie over releaseondersteuning met SAP-toepassingen of locatie van installatiemedia vindt u, naast in de SAP Product Availability Matrix op deze locaties:

- [SAP-ondersteuningsnotitie #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [SAP-ondersteuningsnotitie #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [SAP-ondersteuningsnotitie #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [SAP-ondersteuningsnotitie #1973241](https://launchpad.support.sap.com/#/notes/1973241)

Opmerking: In de documentatie binnen en buiten de SAP-wereld wordt de naam van het product aangeduid als Sybase ASE of SAP ASE of in sommige gevallen beide. Om consistent te blijven, gebruiken we de naam **SAP ASE** in deze documentatie.

## <a name="operating-system-support"></a>Ondersteuning voor het besturingssysteem
De SAP Product Availability Matrix bevat de ondersteunde Operating System en SAP Kernel combinaties voor elke SAP applicatie.  Linux distributies SUSE 12.x, SUSE 15.x, Red Hat 7.x worden volledig ondersteund.  Oracle Linux als besturingssysteem voor SAP ASE wordt niet ondersteund.  Het wordt aanbevolen om de meest recente Linux releases beschikbaar te gebruiken. Windows-klanten moeten Windows Server 2016- of Windows Server 2019-releases gebruiken.  Oudere versies van Windows, zoals Windows 2012 worden technisch ondersteund, maar de nieuwste Windows-versie wordt altijd aanbevolen.


## <a name="specifics-to-sap-ase-on-windows"></a>Specifieke kenmerken van SAP ASE op Windows
Vanaf Microsoft Azure u uw bestaande SAP ASE-toepassingen migreren naar Azure Virtual Machines. MET SAP ASE in een Azure Virtual Machine u de totale eigendomskosten van implementatie, beheer en onderhoud van bedrijfsbreedtetoepassingen verlagen door deze toepassingen eenvoudig te migreren naar Microsoft Azure. Met SAP ASE in een Azure Virtual Machine kunnen beheerders en ontwikkelaars nog steeds dezelfde ontwikkel- en beheertools gebruiken die on-premises beschikbaar zijn.

Microsoft Azure biedt tal van verschillende virtuele machinetypen waarmee u de kleinste SAP-systemen en landschappen uitvoeren tot grote SAP-systemen en landschappen met duizenden gebruikers. SAP-dimensionering SAPS-nummers van de verschillende SAP-gecertificeerde VM SKU's worden geleverd in [SAP-ondersteuningsnota #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Documentatie voor het installeren van SAP ASE op Windows is te vinden in de [SAP ASE Installation Guide voor Windows](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)

Pagina's in het geheugen vergrendelen is een instelling die voorkomt dat de SAP ASE-databasebuffer wordt uitgepiept.  Deze instelling is handig voor grote drukke systemen met veel geheugen. Neem contact op met BC-DB-SYB voor meer informatie. 


## <a name="linux-operating-system-specific-settings"></a>Specifieke instellingen voor Linux-besturingssystemen
Op Linux VM's, draaien `saptune` met profiel SAP-ASE Linux Enorme pagina's moeten standaard worden ingeschakeld en kan worden geverifieerd met opdracht  

`cat /proc/meminfo` 

Het paginaformaat is meestal 2048 KB. Voor meer informatie zie het artikel [Huge Pages on Linux](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>Aanbevelingen voor VM- en schijfstructuur voor SAP ASE-implementaties

SAP ASE voor SAP NetWeaver-toepassingen wordt ondersteund op elk VM-type dat wordt vermeld in [SAP-ondersteuningsnotitie #1928533](https://launchpad.support.sap.com/#/notes/1928533) Typische VM-typen die worden gebruikt voor middelgrote SAP ASE-databaseservers, omvatten Esv3.  Grote multi-terabyte databases kunnen gebruik maken van Vm-typen uit de M-serie. De schrijfprestaties van de SAP ASE-transactielogboekschijf kunnen worden verbeterd door de Schrijfaccelerator uit de M-serie in te schakelen. Write Accelerator moet zorgvuldig worden getest met SAP ASE vanwege de manier waarop SAP ASE logboekschrijft uitvoert.  Bekijk [de SAP-ondersteuningsnotitie #2816580](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator) en overweeg een prestatietest uit te voeren.  
Write Accelerator is alleen ontworpen voor de schijf van transactielogboeken. De cache op schijfniveau moet op GEEN worden ingesteld. Wees niet verbaasd als Azure Write Accelerator geen vergelijkbare verbeteringen laat zien als bij andere DBMS. Op basis van de manier waarop SAP ASE in het transactielogboek schrijft, kan het zijn dat er weinig tot geen versnelling is door Azure Write Accelerator.
Afzonderlijke schijven worden aanbevolen voor gegevensapparaten en logboekapparaten.  De systeemdatabases sybsecurity en `saptools` vereisen geen speciale schijven en kunnen worden geplaatst op de schijven met de SAP-database gegevens en log apparaten 

![Opslagconfiguratie voor SAP ASE](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>Bestandssystemen, streepgrootte & IO-balancering 
SAP ASE schrijft gegevens achtereenvolgens in schijfopslagapparaten, tenzij anders geconfigureerd. Dit betekent dat een lege SAP ASE-database met vier apparaten alleen gegevens naar het eerste apparaat zal schrijven.  De andere schijfapparaten worden alleen geschreven wanneer het eerste apparaat vol is.  De hoeveelheid LEES- en SCHRIJFIO voor elk SAP ASE-apparaat zal waarschijnlijk anders zijn. Om schijf IO in evenwicht te brengen tussen alle beschikbare Azure-schijven moet Windows Storage Spaces of Linux LVM2 worden gebruikt. Op Linux is het raadzaam om xfs-bestandssysteem te gebruiken om de schijven op te maken. De LVM streep grootte moet worden getest met een prestatietest. 128 KB streepgrootte is een goed uitgangspunt. In Windows moet de NTFS Allocation Unit Size (AUS) worden getest. 64 KB kan worden gebruikt als startwaarde. 

Het wordt aanbevolen om automatische databaseuitbreiding te configureren zoals beschreven in het artikel [Automatische databaseruimteuitbreiding configureren in SAP Adaptive Server Enterprise](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/) en [SAP-ondersteuningsnotitie #1815695](https://launchpad.support.sap.com/#/notes/1815695). 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Voorbeeld van SAP ASE op configuratie van de virtuele machine, schijf en bestandssysteem van Azure 
De onderstaande sjablonen tonen voorbeeldconfiguraties voor zowel Linux als Windows. Voordat u de virtuele machine- en schijfconfiguratie bevestigt, moet u ervoor zorgen dat de netwerk- en opslagbandbreedtequota van de afzonderlijke vm voldoende zijn om aan de zakelijke vereisten te voldoen. Houd er ook rekening mee dat verschillende Azure VM-typen verschillende maximale aantallen schijven hebben die aan de VM kunnen worden gekoppeld. Een E4s_v3 VM heeft bijvoorbeeld een limiet van 48 MB/sec opslag IO-doorvoer. Als de opslagdoorvoer die vereist is voor databaseback-upactiviteit meer dan 48 MB/sec vereist, is een groter VM-type met meer doorvoer van opslagbandbreedte onvermijdelijk. Bij het configureren van Azure-opslag moet u er ook rekening mee houden dat vooral met [Azure Premium-opslag](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance) de doorvoer en IOPS per GB capaciteit veranderen. Meer over dit onderwerp in het artikel [Welke schijftypen zijn beschikbaar in Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types). De quota voor specifieke Azure VM-typen zijn gedocumenteerd in het artikel [Geheugen geoptimaliseerde virtuele machine maten](https://docs.microsoft.com/azure/virtual-machines/sizes-memory) en artikelen die zijn gekoppeld aan het. 

> [!NOTE]
>  Als een DBMS-systeem wordt verplaatst van on-premises naar Azure, wordt aanbevolen om de controle op de VM uit te voeren en de CPU, het geheugen, iOPS en de opslagdoorvoer te beoordelen. Vergelijk de waargenomen piekwaarden met de VM-quotalimieten die zijn gedocumenteerd in de hierboven genoemde artikelen

De onderstaande voorbeelden zijn voor illustratieve doeleinden en kunnen worden gewijzigd op basis van individuele behoeften. Door het ontwerp van SAP ASE is het aantal gegevensapparaten niet zo kritisch als bij andere databases. Het aantal gegevensapparaten dat in dit document wordt beschreven, is slechts een handleiding. 

Een voorbeeld van een configuratie voor een kleine SAP ASE DB Server met een databasegrootte tussen 50 GB – 250 GB, zoals SAP solution Manager, kan eruit zien

| Configuratie | Windows | Linux | Opmerkingen |
| --- | --- | --- | --- |
| VM-type | E4s_v3 (4 vCPU/32 GB RAM) | E4s_v3 (4 vCPU/32 GB RAM) | --- |
| Versneld netwerken | Inschakelen | Inschakelen | ---|
| SAP ASE-versie | 16,3 PL 7 of hoger | 16,3 PL 7 of hoger | --- |
| Aantal gegevensapparaten | 4 | 4 | ---|
| # van logapparaten | 1 | 1 | --- |
| # van temp apparaten | 1 | 1 | meer voor SAP BW-workload |
| Besturingssysteem | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 of RHEL 7.6 | --- |
| Schijfaggregatie | Opslagruimten | LVM2 | --- |
| Bestandssysteem | NTFS | Xfs |
| Blokgrootte opmaken | moet workload testen | moet workload testen | --- |
| # en type gegevensschijven | Premium opslag: 2 x P10 (RAID0) | Premium opslag: 2 x P10 (RAID0)| Cache = Alleen lezen |
| # en type logboekschijven | Premium opslag: 1 x P20  | Premium opslag: 1 x P20 | Cache = GEEN |
| ASE MaxMemory, parameter | 90% van het fysieke RAM-geheugen | 90% van het fysieke RAM-geheugen | uitgaande van een enkele instantie |
| # van back-upapparaten | 4 | 4| --- |
| # en type back-upschijven | 1 | 1 | --- |


Een voorbeeld van een configuratie voor een middelgrote SAP ASE DB Server met een databasegrootte tussen 250 GB – 750 GB, zoals een kleiner SAP Business Suite-systeem, kan eruit zien

| Configuratie | Windows | Linux | Opmerkingen |
| --- | --- | --- | --- |
| VM-type | E16s_v3 (16 vCPU/128 GB RAM) | E16s_v3 (16 vCPU/128 GB RAM) | --- |
| Versneld netwerken | Inschakelen | Inschakelen | ---|
| SAP ASE-versie | 16,3 PL 7 of hoger | 16,3 PL 7 of hoger | --- |
| Aantal gegevensapparaten | 8 | 8 | ---|
| # van logapparaten | 1 | 1 | --- |
| # van temp apparaten | 1 | 1 | meer voor SAP BW-workload |
| Besturingssysteem | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 of RHEL 7.6 | --- |
| Schijfaggregatie | Opslagruimten | LVM2 | --- |
| Bestandssysteem | NTFS | Xfs |
| Blokgrootte opmaken | moet workload testen | moet workload testen | --- |
| # en type gegevensschijven | Premium opslag: 4 x P20 (RAID0) | Premium opslag: 4 x P20 (RAID0)| Cache = Alleen lezen |
| # en type logboekschijven | Premium opslag: 1 x P20  | Premium opslag: 1 x P20 | Cache = GEEN |
| ASE MaxMemory, parameter | 90% van het fysieke RAM-geheugen | 90% van het fysieke RAM-geheugen | uitgaande van een enkele instantie |
| # van back-upapparaten | 4 | 4| --- |
| # en type back-upschijven | 1 | 1 | --- |

Een voorbeeld van een configuratie voor een kleine SAP ASE DB Server met een databasegrootte tussen 750 GB – 2000 GB, zoals een groter SAP Business Suite-systeem, kan eruit zien

| Configuratie | Windows | Linux | Opmerkingen |
| --- | --- | --- | --- |
| VM-type | E64s_v3 (64 vCPU/432 GB RAM) | E64s_v3 (64 vCPU/432 GB RAM) | --- |
| Versneld netwerken | Inschakelen | Inschakelen | ---|
| SAP ASE-versie | 16,3 PL 7 of hoger | 16,3 PL 7 of hoger | --- |
| Aantal gegevensapparaten | 16 | 16 | ---|
| # van logapparaten | 1 | 1 | --- |
| # van temp apparaten | 1 | 1 | meer voor SAP BW-workload |
| Besturingssysteem | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 of RHEL 7.6 | --- |
| Schijfaggregatie | Opslagruimten | LVM2 | --- |
| Bestandssysteem | NTFS | Xfs |
| Blokgrootte opmaken | moet workload testen | moet workload testen | --- |
| # en type gegevensschijven | Premium opslag: 4 x P30 (RAID0) | Premium opslag: 4 x P30 (RAID0)| Cache = Alleen lezen |
| # en type logboekschijven | Premium opslag: 1 x P20  | Premium opslag: 1 x P20 | Cache = GEEN |
| ASE MaxMemory, parameter | 90% van het fysieke RAM-geheugen | 90% van het fysieke RAM-geheugen | uitgaande van een enkele instantie |
| # van back-upapparaten | 4 | 4| --- |
| # en type back-upschijven | 1 | 1 | --- |


Een voorbeeld van een configuratie voor een kleine SAP ASE DB Server met een databasegrootte van 2 TB+, zoals een groter wereldwijd gebruikt SAP Business Suite-systeem, kan eruit zien

| Configuratie | Windows | Linux | Opmerkingen |
| --- | --- | --- | --- |
| VM-type | M-serie (1,0 tot 4,0 TB RAM)  | M-serie (1,0 tot 4,0 TB RAM) | --- |
| Versneld netwerken | Inschakelen | Inschakelen | ---|
| SAP ASE-versie | 16,3 PL 7 of hoger | 16,3 PL 7 of hoger | --- |
| Aantal gegevensapparaten | 32 | 32 | ---|
| # van logapparaten | 1 | 1 | --- |
| # van temp apparaten | 1 | 1 | meer voor SAP BW-workload |
| Besturingssysteem | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 of RHEL 7.6 | --- |
| Schijfaggregatie | Opslagruimten | LVM2 | --- |
| Bestandssysteem | NTFS | Xfs |
| Blokgrootte opmaken | moet workload testen | moet workload testen | --- |
| # en type gegevensschijven | Premium opslag: 4+ x P30 (RAID0) | Premium opslag: 4+ x P30 (RAID0)| Cache = Alleen lezen, Overweeg Azure Ultra-schijf |
| # en type logboekschijven | Premium opslag: 1 x P20  | Premium opslag: 1 x P20 | Cache = GEEN, Overweeg Azure Ultra-schijf |
| ASE MaxMemory, parameter | 90% van het fysieke RAM-geheugen | 90% van het fysieke RAM-geheugen | uitgaande van een enkele instantie |
| # van back-upapparaten | 16 | 16 | --- |
| # en type back-upschijven | 4 | 4 | LVM2/Opslagruimten gebruiken |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Back-up& hersteloverwegingen voor SAP ASE op Azure
Het verhogen van het aantal gegevens en back-upapparaten verhoogt de back-up en herstelt de prestaties. Het wordt aanbevolen om de Azure-schijven te strepen die het SAP ASE-back-upapparaat hosten zoals weergegeven in de eerder weergegeven tabellen. Er moet voor worden gezorgd dat het aantal back-upapparaten en -schijven in evenwicht wordt gebracht en ervoor moet zorgen dat de back-updoorvoer niet hoger mag zijn dan 40%-50% van het totale VM-doorvoerquotum. Het wordt aanbevolen om SAP Backup Compression standaard te gebruiken. Meer details zijn te vinden in de artikelen:

- [SAP-ondersteuningsnotitie #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [SAP-ondersteuningsnotitie #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [SAP-ondersteuningsnotitie #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

Gebruik station D:\ of /temp ruimte als database of log dump bestemming.

### <a name="impact-of-database-compression"></a>Impact van databasecompressie
In configuraties waar I/O-bandbreedte een beperkende factor kan worden, kunnen maatregelen, die IOPS verminderen, helpen om de werkbelasting uit te rekken die men kan uitvoeren in een IaaS-scenario zoals Azure. Daarom wordt aanbevolen om ervoor te zorgen dat SAP ASE-compressie wordt gebruikt voordat u een bestaande SAP-database uploadt naar Azure.

De aanbeveling om compressie toe te passen voordat u naar Azure wordt geüpload, wordt gegeven op verschillende redenen:

* De hoeveelheid gegevens die naar Azure moet worden geüpload, is lager
* De duur van de compressieuitvoering is korter in de veronderstelling dat men sterkere hardware kan gebruiken met meer CPU's of een hogere I/O-bandbreedte of minder I/O-latentie on-premises
* Kleinere databaseformaten kunnen leiden tot minder kosten voor schijftoewijzing

Gegevens- en LOB-compressiewerk in een VM die wordt gehost in Azure Virtual Machines, zoals on-premises. Voor meer informatie over hoe u controleren of compressie al in een bestaande SAP ASE-database wordt gebruikt, raadpleegt u [SAP-ondersteuningsnotitie 1750510](https://launchpad.support.sap.com/#/notes/1750510). Voor meer informatie over SAP ASE database compressie check [SAP support note #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Hoge beschikbaarheid van SAP ASE op Azure 
De HADR Users Guide beschrijft de installatie en configuratie van een 2-node SAP ASE "Always-on" oplossing.  Daarnaast wordt ook een derde noodherstelknooppunt ondersteund. SAP ASE ondersteunt veel high available configuraties, waaronder gedeelde schijf en native OS-clustering (zwevend IP). De enige ondersteunde configuratie op Azure is het gebruik van Fault Manager zonder zwevend IP.  De methode Zwevend IP-adres werkt niet op Azure.  De SAP Kernel is een "HA Aware" applicatie en weet over de primaire en secundaire SAP ASE servers. Er zijn geen nauwe integraties tussen de SAP ASE en Azure, de Azure Internal load balancer wordt niet gebruikt. Daarom moet de standaard SAP ASE-documentatie worden gevolgd, te beginnen met [de SAP ASE HADR-gebruikersgids](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) 

> [!NOTE]
> De enige ondersteunde configuratie op Azure is het gebruik van Fault Manager zonder zwevend IP.  De methode Zwevend IP-adres werkt niet op Azure. 

### <a name="third-node-for-disaster-recovery"></a>Derde knooppunt voor herstel na noodgevallen
Naast het gebruik van SAP ASE Always-On voor lokale hoge beschikbaarheid, wilt u de configuratie mogelijk uitbreiden naar een asynchrone gerepliceerd knooppunt in een andere Azure-regio. Documentatie voor een dergelijk scenario is [hier](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)te vinden.

## <a name="sap-ase-database-encryption--ssl"></a>SAP ASE-databaseversleuteling & SSL 
SAP Software provisioning Manager (SWPM) geeft een optie om de database te versleutelen tijdens de installatie.  Als u versleuteling wilt gebruiken, wordt het aanbevolen om SAP Full Database Encryption te gebruiken.  Zie details gedocumenteerd in:

- [SAP-ondersteuningsnotitie #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [SAP-ondersteuningsnotitie #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [SAP-ondersteuningsnotitie #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [SAP-ondersteuningsnotitie #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> Als een SAP ASE-database is versleuteld, werkt back-updumpcompressie niet. Zie ook [SAP support note #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>CHECKLIST SAP ASE voor Azure-implementatie
 
- SAP ASE 16.3 PL7 of hoger implementeren
- Update naar de nieuwste versie en patches van FaultManager en SAPHostAgent
- Implementeren op de nieuwste gecertificeerde besturingssysteem beschikbaar, zoals Windows 2019, Suse 15.1 of Redhat 7.6 of hoger
- Sap-gecertificeerde VM's gebruiken – Azure VM SKU's met hoog geheugen, zoals Es_v3 of voor x-grote vm-skuisten van de M-serie m-serie, worden aanbevolen
- Overeenkomen met het schijf-IOPS- en totale VM-doorvoerquotum van de VM met het schijfontwerp.  Voldoende aantal schijven implementeren
- Schijven samenvoegen met Windows-opslagruimten of Linux LVM2 met de juiste streepgrootte en bestandssysteem
- Voldoende aantal apparaten maken voor gegevens- en logboek-, tijdelijke en back-updoeleinden
- Overweeg UltraDisk te gebruiken voor x-large systemen 
- Run `saptune` SAP-ASE op Linux OS 
- Beveilig de database met DB-versleuteling – sla sleutels handmatig op in Azure Key Vault 
- Het [SAP op Azure-checklist voltooien](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist) 
- Logboekbackup en volledige back-up configureren 
- Ha/DR testen, back-upmaken en herstellen en stress uitvoeren & volumetest 
- Automatische databaseextensie bevestigen werkt 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>DBACockpit gebruiken om database-exemplaren te controleren
Voor SAP-systemen, die SAP ASE gebruiken als databaseplatform, is de DBACockpit toegankelijk als ingesloten browservensters in transactie DBACockpit of als Webdynpro. De volledige functionaliteit voor het bewaken en beheren van de database is echter alleen beschikbaar in de Webdynpro-implementatie van de DBACockpit.

Net als bij on-premises systemen zijn verschillende stappen nodig om alle SAP NetWeaver-functionaliteit in te schakelen die wordt gebruikt door de Webdynpro-implementatie van de DBACockpit. Volg [SAP support note #1245200](https://launchpad.support.sap.com/#/notes/1245200) om het gebruik van webdynpros mogelijk te maken en de vereiste te genereren. Wanneer u de instructies in de bovenstaande notities volgt, configureert u ook de Internet Communication Manager (`ICM`) samen met de poorten die moeten worden gebruikt voor http- en https-verbindingen. De standaardinstelling voor http ziet eruit als volgt:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

en de links gegenereerd in transactie DBACockpit lijkt op:

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Afhankelijk van hoe de Azure Virtual Machine die het SAP-systeem host is aangesloten op uw AD en DNS, moet u ervoor zorgen dat ICM een volledig gekwalificeerde hostname gebruikt die kan worden opgelost op de machine waar u de DBACockpit opent. Zie [SAP-ondersteuningsnotitie #773830](https://launchpad.support.sap.com/#/notes/773830) om te begrijpen hoe ICM de volledig gekwalificeerde hostnaam bepaalt op basis van profielparameters en indien nodig expliciet parameter icm/host_name_full instelt.

Als u de VM hebt geïmplementeerd in een Cloud-Only-scenario zonder cross-premises connectiviteit tussen on-premises `domainlabel`en Azure, moet u een openbaar IP-adres en een . Het formaat van de openbare DNS-naam van de VM ziet eruit als volgt:

> `<custom domainlabel`>. `<azure region`>.cloudapp.azure.com
> 
> 

Meer details met betrekking tot de DNS-naam zijn te vinden [hier][virtual-machines-azurerm-versus-azuresm].

Als u de SAP-profielparameter icm/host_name_full instelt op de DNS-naam van de Azure VM, lijkt de koppeling mogelijk op:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

In dit geval moet u ervoor zorgen dat:

* Binnenkomende regels toevoegen aan de netwerkbeveiligingsgroep in de Azure-portal voor de TCP/IP-poorten die worden gebruikt om met ICM te communiceren
* Binnenkomende regels toevoegen aan de Windows Firewall-configuratie voor de TCP/IP-poorten die worden gebruikt om met de ICM te communiceren

Voor een geautomatiseerd geïmporteerd van alle beschikbare correcties is het raadzaam om de correctieverzameling SAP Note die van toepassing is op uw SAP-versie periodiek toe te passen:

* [SAP-ondersteuningsnotitie #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [SAP-ondersteuningsnotitie #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [SAP-ondersteuningsnotitie #1882376](https://launchpad.support.sap.com/#/notes/1882376)

Meer informatie over DBA Cockpit voor SAP ASE is te vinden in de volgende SAP Notes:

* [SAP-ondersteuningsnotitie #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [SAP-ondersteuningsnotitie #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [SAP-ondersteuningsnotitie #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [SAP-ondersteuningsnotitie #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [SAP-ondersteuningsnotitie #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [SAP-ondersteuningsnotitie #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [SAP-ondersteuningsnotitie #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [SAP-ondersteuningsnotitie #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>Nuttige links, notities & whitepapers voor SAP ASE
De startpagina voor [Sybase ASE 16.3 PL7 Documentatie](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) geeft links naar verschillende documenten waarvan de documenten van:

- SAP ASE Learning Journey - Beheer & Monitoring
- SAP ASE Learning Journey - Upgrade & installatie

zijn nuttig. Een ander nuttig document is [SAP-toepassingen op SAP Adaptive Server Enterprise Best Practices voor migratie en runtime.](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf)

Andere handige SAP-ondersteuningsnotities zijn:

- [SAP-ondersteuningsnotitie #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [SAP-ondersteuningsnotitie #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [SAP-ondersteuningsnotitie #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [SAP-ondersteuningsnotitie #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [SAP-ondersteuningsnotitie #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [SAP-ondersteuningsnotitie #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [SAP-ondersteuningsnotitie #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP-ondersteuningsnotitie #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [SAP-ondersteuningsnotitie #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [SAP-ondersteuningsnotitie #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [SAP-ondersteuningsnotitie #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [SAP-ondersteuningsnotitie #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP-ondersteuningsnotitie #1588316](https://launchpad.support.sap.com/#/notes/158831) 

Andere informatie wordt gepubliceerd op 

- [SAP-toepassingen op SAP Adaptive Server Enterprise](https://community.sap.com/topics/applications-on-ase)
- [Sybase infocenter](http://infocenter.sybase.com/help/index.jsp) 

Een maandelijkse nieuwsbrief wordt gepubliceerd via [SAP support note #2381575](https://launchpad.support.sap.com/#/notes/2381575) 

[Sybase ASE Always-on met 3e DR Node Setup](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199) 

## <a name="next-steps"></a>Volgende stappen
Het artikel [SAP-workloads op Azure: checklist voor planning en implementatie controleren](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)

