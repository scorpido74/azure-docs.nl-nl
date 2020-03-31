---
title: SAP Business One op Azure Virtual Machines | Microsoft Documenten
description: SAP Business One op Azure.
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
ms.date: 07/15/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 18409f93ab50f7d031ec78a55b9eaf8ad1b85a49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101418"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business One in virtuele Azure-machines
Dit document biedt richtlijnen voor het implementeren van SAP Business One op Azure Virtual Machines. De documentatie is geen vervanging voor installatiedocumentatie van Business one voor SAP. De documentatie moet betrekking hebben op basisrichtlijnen voor planning en implementatie voor de Azure-infrastructuur om Business One-toepassingen op uit te voeren.

Business One ondersteunt twee verschillende databases:
- SQL Server - zie [SAP Note #928839 - Releaseplanning voor Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA - voor exacte SAP Business One support matrix voor SAP HANA, checkout the [SAP Product Availability Matrix](https://support.sap.com/pam) SAP

Wat SQL Server betreft, zijn de basisimplementatieoverwegingen zoals gedocumenteerd in de [AZURE Virtual Machines DBMS-implementatie voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide) van toepassing. voor SAP HANA worden overwegingen in dit document vermeld.

## <a name="prerequisites"></a>Vereisten
Als u deze handleiding wilt gebruiken, hebt u basiskennis van de volgende Azure-componenten nodig:

- [Virtuele Azure-machines op Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- [Azure virtuele machines op Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure-netwerkbeheer en beheer van virtuele netwerken met PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-virtual-network)
- [Azure-netwerken en virtuele netwerken met CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure-schijven beheren met de Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Zelfs als u alleen geïnteresseerd bent in business One, kan het document [Azure Virtual Machines planning en implementatie voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) een goede bron van informatie zijn.

De aanname is dat u als instantie die SAP Business One implementeert:

- Vertrouwd met het installeren van SAP HANA op een bepaalde infrastructuur zoals een VM
- Vertrouwd installeren van de SAP Business One-toepassing op een infrastructuur zoals Azure VM's
- Vertrouwd met het operationele SAP Business One en het gekozen DBMS-systeem
- Vertrouwd met het implementeren van infrastructuur in Azure

Al deze gebieden komen niet aan bod in dit document.

Naast Azure-documentatie moet u op de hoogte zijn van de belangrijkste SAP Notes, die verwijzen naar Business One of die centrale notities van SAP voor business One zijn:

- [528296 - Algemene overzichtsnotitie voor SAP Business One-releases en gerelateerde producten](https://launchpad.support.sap.com/#/notes/528296)
- [2216195 - Release Updates Note voor SAP Business One 9.2, versie voor SAP HANA](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583 - Centrale noot voor SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615 - Release updates Opmerking voor SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595 - Collectieve nota voor SAP Business One 9.3 Algemene problemen](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458 - Collective Consulting Note for SAP HANA-Related Topics of SAP Business One, versie for SAP HANA](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>Business One-architectuur
Business One is een toepassing met twee lagen:

- Een klantenlaag met een 'dikke' klant
- Een databaselaag die het databaseschema voor een tenant bevat

Een beter overzicht van welke onderdelen in het clientgedeelte worden uitgevoerd en welke onderdelen in het servergedeelte worden uitgevoerd, is gedocumenteerd in [DE Beheergids van SAP Business One](https://help.sap.com/http.svc/rc/879bd9289df34a47af838e67d74ea302/9.3/en-US/AdministratorGuide_SQL.pdf) 

Aangezien er een zware latentiekritieke interactie is tussen de clientlaag en de DBMS-laag, moeten beide lagen zich in Azure bevinden wanneer ze worden geïmplementeerd in Azure. het is gebruikelijk dat de gebruikers vervolgens RDS in een of meerdere VM's met een RDS-service voor de Business One client componenten.

### <a name="sizing-vms-for-sap-business-one"></a>Dimensionering vm's voor SAP Business One

Met betrekking tot de grootte van de client VM(s) worden de resourcevereisten gedocumenteerd door SAP in het document [SAP Business One Hardware Requirements Guide](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Voor Azure moet u zich concentreren en berekenen met de vereisten in hoofdstuk 2.4 van het document.

Als Virtuele Azure-machines voor het hosten van de Business One-clientcomponenten en de DBMS-host zijn alleen VM's die worden ondersteund door SAP NetWeaver. Als u de lijst met door SAP NetWeaver ondersteunde Azure VM's wilt vinden, leest u [SAP Note-#1928533](https://launchpad.support.sap.com/#/notes/1928533).

Met SAP HANA als DBMS backend voor Business One worden alleen VM's, die voor Business op HANA zijn vermeld in de [HANA certificiid IaaS-platformlijst,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) ondersteund voor HANA. De Business One client componenten worden niet beïnvloed door deze sterkere beperking voor de SAP HANA als DBMS systeem.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Het besturingssysteem wordt gebruikt voor SAP Business One

In principe is het altijd het beste om de meest recente releases van het besturingssysteem te gebruiken. Vooral in de Linux-ruimte werd nieuwe Azure-functionaliteit geïntroduceerd met verschillende recentere kleine releases van Suse en Red Hat. Aan de Windows-kant is het gebruik van Windows Server 2016 een aanrader.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Infrastructuur implementeren in Azure voor SAP Business One
In de volgende hoofdstukken, de infrastructuur stukken die er toe doen voor de inzet van SAP.

### <a name="azure-network-infrastructure"></a>Azure-netwerkinfrastructuur
De netwerkinfrastructuur die u in Azure moet implementeren, is afhankelijk van de vraag of u één Business One-systeem zelf implementeert. Of of u een hoster bent die tientallen Business One-systemen voor klanten host. Er kunnen ook kleine wijzigingen in het ontwerp zijn op de vraag of u verbinding maakt met Azure. Ga door verschillende mogelijkheden, een ontwerp waarbij u een VPN-verbinding in Azure hebt en waar u uw Active Directory via [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) of [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) uitbreidt naar Azure.

![Eenvoudige netwerkconfiguratie met Business One](./media/business-one-azure/simple-network-with-VPN.PNG)

De vereenvoudigde configuratie gepresenteerd introduceert verschillende beveiligingsinstanties die het mogelijk maken om te controleren en te beperken routing. Het begint met 

- De router/firewall aan de on-premises kant van de klant.
- De volgende instantie is de [Azure Network Security Group](https://docs.microsoft.com/azure/virtual-network/security-overview) die u gebruiken om routerings- en beveiligingsregels in te voeren voor het Azure VNet waarin u uw SAP Business-configuratie uitvoert.
- Om te voorkomen dat gebruikers van Business One-client ook de server kunnen zien waarop de Business One-server wordt uitgevoerd, die de database uitvoert, moet u de VM die de Zakelijke één client host en de zakelijke één server scheiden in twee verschillende subnetten binnen het VNet.
- U zou Azure NSG opnieuw aan de twee verschillende subnetten hebben toegewezen om de toegang tot de Business one-server te beperken.

Een meer geavanceerde versie van een Azure-netwerkconfiguratie is gebaseerd op de door Azure [gedocumenteerde best practices van hub- en spoke-architectuur.](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) Het architectuurpatroon van hub en spaak zou de eerste vereenvoudigde configuratie veranderen in een als volgt:


![Hub en spaakconfiguratie met Business One](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

Voor gevallen waarin de gebruikers verbinding maken via het internet zonder privé-verbinding in Azure, moet het ontwerp van het netwerk in Azure worden afgestemd op de beginselen die zijn gedocumenteerd in de Azure-referentiearchitectuur voor [DMZ tussen Azure en internet.](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)

### <a name="business-one-database-server"></a>Business One-databaseserver
Voor het databasetype zijn SQL Server en SAP HANA beschikbaar. Onafhankelijk van de DBMS, moet u het document [Considerations for Azure Virtual Machines DBMS deployment for SAP workload](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) lezen om een algemeen inzicht te krijgen in DBMS-implementaties in azure VM's en de bijbehorende netwerk- en opslagonderwerpen.

Hoewel benadrukt in de specifieke en generieke database documenten al, moet je jezelf vertrouwd te maken met:

- [De beschikbaarheid van virtuele Windows-machines in Azure beheren](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) en [de beschikbaarheid van virtuele Linux-machines in Azure beheren](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)
- [SLA voor virtuele machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Deze documenten moeten u helpen om te beslissen over de selectie van opslagtypen en configuratie met hoge beschikbaarheid.

In principe moet u:

- Gebruik Premium SSD's via standaard HDD's. Zie ons artikel [Een schijftype selecteren voor](../../windows/disks-types.md) meer informatie over de beschikbare schijftypen
- Azure Managed-schijven gebruiken via onbeheerde schijven
- Zorg ervoor dat u voldoende IOPS- en I/O-doorvoer hebt die is geconfigureerd met uw schijfconfiguratie
- Combineer /hana/data en /hana/log volume om een kostenefficiënte opslagconfiguratie te hebben


#### <a name="sql-server-as-dbms"></a>SQL Server als DBMS
Ga mee met het document SQL Server Azure [Virtual Machines DBMS-implementatie voor SAP NetWeaver voor](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)het implementeren van SQL Server als DBMS voor Business One. 

Ruwe grootteschattingen voor de DBMS-kant voor SQL Server zijn:

| Aantal gebruikers | vCPUs | Geheugen | Voorbeeld vm-typen |
| --- | --- | --- | --- |
| maximaal 20 | 4 | 16 GB | D4s_v3, E4s_v3 |
| maximaal 40 | 8 | 32 GB | D8s_v3, E8s_v3 |
| maximaal 80 | 16 | 64 GB | D16s_v3, E16s_v3 |
| maximaal 150 | 32 | 128 GB | D32s_v3, E32s_v3 |

De hierboven genoemde grootte moet een idee geven waar te beginnen met. Het kan zijn dat u minder of meer resources nodig hebt, in welk geval een aanpassing op azure eenvoudig is. Een wijziging tussen VM-typen is mogelijk met slechts een herstart van de VM.

#### <a name="sap-hana-as-dbms"></a>SAP HANA als DBMS
Sap HANA als DBMS gebruiken in de volgende secties, u moet de overwegingen van het document [SAP HANA op Azure-bewerkingshandleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)volgen.

Voor configuraties met hoge beschikbaarheid en noodherstel rond SAP HANA als database voor Business One in Azure, moet u de documentatie [SAP HANA hoge beschikbaarheid voor Azure virtuele machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview) en de documentatie die uit dat document wordt vermeld, lezen.

Voor SAP HANA-back-up- en herstelstrategieën leest u de [documentback-uphandleiding voor SAP HANA op Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) en de documentatie waarnaar uit dat document wordt gewezen.

 
### <a name="business-one-client-server"></a>Zakelijke één clientserver
Voor deze componenten opslag overwegingen zijn niet de primaire zorg. toch wilt u een betrouwbaar platform hebben. Daarom moet u Azure Premium Storage gebruiken voor deze VM, zelfs voor de basis VHD. Het aanpassen van de VM, met de gegevens die worden gegeven in [SAP Business One Hardware Requirements Guide](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Voor Azure moet u zich concentreren en berekenen met de vereisten in hoofdstuk 2.4 van het document. Als u de vereisten berekent, moet u deze vergelijken met de volgende documenten om de ideale vm voor u te vinden:

- [Grootten voor Windows Virtual Machines in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
- [SAP-notitie #1928533](https://launchpad.support.sap.com/#/notes/1928533)

Vergelijk het aantal CPU's en geheugen dat nodig is met wat is gedocumenteerd door Microsoft. Houd ook rekening met de netwerkdoorvoer bij het kiezen van de VM's.








