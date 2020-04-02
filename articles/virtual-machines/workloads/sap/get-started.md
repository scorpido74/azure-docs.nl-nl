---
title: Aan de slag met SAP op Azure VM's | Microsoft Documenten
description: Meer informatie over SAP-oplossingen die worden uitgevoerd op virtuele machines (VM's) in Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 64469641173cfe31c3e4fbac5e2f21c75a94c716
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521868"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Azure gebruiken om SAP-werkbelastingscenario's te hosten en uit te voeren

Wanneer u Microsoft Azure gebruikt, u uw bedrijfskritieke SAP-workloads en -scenario's op betrouwbare wijze uitvoeren op een schaalbaar, compatibel en door ondernemingen bewezen platform. U krijgt de schaalbaarheid, flexibiliteit en kostenbesparingen van Azure. Met de uitgebreide samenwerking tussen Microsoft en SAP u SAP-toepassingen uitvoeren in ontwikkelings- en test- en productiescenario's in Azure en volledig worden ondersteund. Van SAP NetWeaver tot SAP S/4HANA, SAP BI op Linux tot Windows en SAP HANA tot SQL, wij staan voor u klaar.

Naast het hosten van SAP NetWeaver-scenario's met de verschillende DBMS op Azure, u ook andere SAP-werklastscenario's hosten, zoals SAP BI op Azure. 

Het unieke van Azure voor SAP HANA is een aanbieding die Azure onderscheidt. Om het hosten van meer geheugen- en CPU-resourceveeleisende SAP-scenario's mogelijk te maken waarbij SAP HANA betrokken is, biedt Azure het gebruik van klantspecifieke bare-metal-hardware. Gebruik deze oplossing om SAP HANA-implementaties uit te voeren waarvoor maximaal 24 TB (120 TB scale-out) geheugen nodig is voor S/4HANA of andere SAP HANA-workload. 

Het hosten van SAP-werklastscenario's in Azure kan ook vereisten voor identiteitsintegratie en eenmalige aanmelding maken. Deze situatie kan optreden wanneer u Azure Active Directory (Azure AD) gebruikt om verschillende SAP-componenten en SAP-software-as-a-service (SaaS) of platform-as-a-service -aanbiedingen (PaaS) met elkaar te verbinden. Een lijst met dergelijke integratie- en aanmeldingsscenario's met Azure AD- en SAP-entiteiten wordt beschreven en gedocumenteerd in de sectie 'AAD SAP-identiteitsintegratie en enkele aanmelding'.

## <a name="changes-to-the-sap-workload-section"></a>Wijzigingen in de sectie SAP-werkbelasting
Wijzigingen in documenten in de sectie SAP op Azure-werkbelasting worden aan het einde van dit artikel weergegeven. De vermeldingen in het wijzigingslogboek worden ongeveer 180 dagen bewaard.

## <a name="you-want-to-know"></a>Je wilt het weten.
Als u specifieke vragen hebt, wijzen we u naar specifieke documenten of stromen in dit gedeelte van de startpagina. Je wilt weten:

- Welke Azure VM's en HANA Large Instance-eenheden worden ondersteund voor welke SAP-software wordt uitgebracht en welke versies van het besturingssysteem. Lees het document [Welke SAP-software wordt ondersteund voor Azure-implementatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) voor antwoorden en het proces om de informatie te vinden
- Welke SAP-implementatiescenario's worden ondersteund met Azure VM's en HANA Large Instances. Informatie over de ondersteunde scenario's vindt u in de documenten:
    - [SAP-werkbelasting op door Azure ondersteunde scenario's voor virtuele machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [Ondersteunde scenario's voor HANA Large Instance](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- Welke Azure Services, Azure VM-typen en Azure-opslagservices beschikbaar zijn in de verschillende Azure-regio's, controleer de [siteproducten die beschikbaar](https://azure.microsoft.com/global-infrastructure/services/) zijn per regio 

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA op Azure (grote exemplaren)

Een reeks documenten leidt u via SAP HANA op Azure (Large Instances), of kortom, HANA Large Instances. Voor informatie over HANA Large Instances begint u met het documentoverzicht en de [architectuur van SAP HANA op Azure (Large Instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) en doornemen van de gerelateerde documentatie in de sectie GROTE Instantie HANA



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA op azure virtuele machines
Dit deel van de documentatie behandelt verschillende aspecten van SAP HANA. Als voorwaarde moet u bekend zijn met de belangrijkste services van Azure die elementaire services van Azure IaaS bieden. U hebt dus kennis nodig van Azure compute, storage en netwerken. Veel van deze onderwerpen worden behandeld in de SAP NetWeaver-gerelateerde [Azure-planningshandleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 

Zie de volgende artikelen en hun subartikelen voor informatie over HANA op Azure:

- [Configuraties en bewerkingen van SAP HANA-infrastructuur in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA hoge beschikbaarheid voor Virtuele Azure-machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Hoge beschikbaarheid van SAP HANA op Azure virtuele machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Back-uphandleiding voor SAP HANA op virtuele Azure-machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver geïmplementeerd op virtuele Azure-machines
In deze sectie vindt u plannings- en implementatiedocumentatie voor SAP NetWeaver en Business One op Azure. De documentatie richt zich op de basis en het gebruik van niet-HANA-databases met een SAP-workload op Azure. De documenten en artikelen voor hoge beschikbaarheid vormen ook de basis voor hana-hoge beschikbaarheid in Azure, zoals:

- [Azure-planningshandleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 
- [SAP Business One op virtuele Azure-machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Een meervoudige SAP NetWeaver-toepassing beveiligen met siteherstel](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [SAP LaMa-connector voor Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Zie voor informatie over niet-HANA-databases onder een SAP-werkbelasting op Azure:

- [Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-workload](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SQL Server Azure Virtual Machines DBMS-implementatie voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor Oracle](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [IBM DB2 Azure Virtual Machines DBMS-implementatie voor SAP-workload](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor SAP ASE](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [SAP MaxDB-, Live-cache- en contentserverimplementatie op Azure VM's](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Zie de sectie 'SAP HANA op virtuele Azure-machines' voor informatie over SAP HANA-databases op Azure.

Zie voor informatie over de hoge beschikbaarheid van een SAP-workload op Azure:

- [Azure Virtual Machines hoge beschikbaarheid voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Dit document verwijst naar verschillende andere architectuur- en scenariodocumenten. In latere scenariodocumenten worden koppelingen naar gedetailleerde technische documenten verstrekt waarin de implementatie en configuratie van de verschillende methoden voor hoge beschikbaarheid worden uitgelegd. De verschillende documenten die laten zien hoe u hoge beschikbaarheid voor een SAP NetWeaver-workload instellen en configureren, hebben betrekking op Linux- en Windows-besturingssystemen.


Zie voor informatie over de integratie tussen Azure Active Directory (Azure AD) en SAP-services en eenmalige aanmelding:

- [Zelfstudie: Azure Active Directory-integratie met SAP Cloud voor de klant](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelfstudie: Azure Active Directory-integratie met SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelfstudie: Azure Active Directory-integratie met SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelfstudie: Azure Active Directory-integratie met SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelfstudie: Azure Active Directory-integratie met SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelfstudie: Azure Active Directory-integratie met SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Uw S/4HANA-omgeving: Fiori Launchpad SAML-singlesign-aan met Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Zie voor informatie over de integratie van Azure-services in SAP-componenten:

- [SAP HANA gebruiken in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery en SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [De SAP BW-connector gebruiken in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory biedt integratie van SAP HANA- en Business Warehouse-gegevens](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Logboek wijzigen
- 03/31/2020: Wijziging in [hoge beschikbaarheid van SAP HANA op Azure VM's op SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) en hoge beschikbaarheid van SAP [HANA op Azure VM's op RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) om instructies toe te voegen hoe u de streepgrootte opgeeft bij het maken van gestreepte volumes
- 03/27/2020: Wijziging in [hoge beschikbaarheid voor SAP NW op Azure VM's op SLES met ANF voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) om de opties voor het monteren van bestandssystemen uit te lijnen op NetApp TR-4746 (verwijder de optie synchronisatiebevestiging)
- 03/26/2020: Wijziging in [hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SLES multi-SID-handleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) om verwijzing naar NetApp TR-4746 toe te voegen
- 03/26/2020: Wijziging in [hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SLES voor SAP-toepassingen,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)hoge beschikbaarheid voor SAP [NetWeaver op Azure VM's op SLES met Azure NetApp-bestanden voor SAP-toepassingen,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) [Hoge beschikbaarheid voor NFS op Azure VM's op SLES,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) [Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SLES multi-SID-handleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid), [Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op RHEL voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) en [Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op RHEL met Azure NetApp-bestanden voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) om diagrammen bij te werken en instructies voor het maken van backend-backendpools van Azure Load Balancer te verduidelijken
- 03/19/2020: Grote revisie van document [Quickstart: Handmatige installatie van SAP HANA met één exemplaar op Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started) voor installatie van SAP [HANA op Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- 03/17/2020: Wijziging in [het instellen van pacemaker op SUSE Linux Enterprise Server in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) om SBD-configuratie-instelling te verwijderen die niet langer nodig is
- 03/16/2020: Verduidelijking van het scenario voor kolomcertificering in SAP HANA IaaS-gecertificeerd platform in [Welke SAP-software wordt ondersteund voor Azure-implementaties](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 03/11/2020: Wijziging in [SAP-werkbelasting op door Azure ondersteunde scenario's](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) voor virtuele machines om meerdere databases per DBMS-instantieondersteuning te verduidelijken
- 03/11/2020: Wijziging in [de planning en implementatie van Azure Virtual Machines voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) met uitleg over generatie 1 en Generatie 2 VM's
- 03/10/2020: Wijziging in [SAP HANA Azure-configuraties voor virtuele machineopslag](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) om echte bestaande doorvoerlimieten van ANF te verduidelijken
- 03/09/2020: Wijziging in [hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SUSE Linux Enterprise Server voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), Hoge beschikbaarheid voor SAP [NetWeaver op Azure VM's op SUSE Linux Enterprise Server met Azure NetApp-bestanden voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [Hoge beschikbaarheid voor NFS op Azure VM's op SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), Instellen Pacemaker op [SUSE Linux Enterprise Server in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), Hoge beschikbaarheid van IBM [Db2 LUW op Azure VM's op SUSE Linux Enterprise Server met Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm), Hoge beschikbaarheid van SAP [HANA op Azure VM's op SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) en Hoge beschikbaarheid voor SAP [NetWeaver op Azure VMs op SLES multi-SID-handleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) voor het bijwerken van clusterbronnen met resourceagent azure-lb 
- 03/05/2020: Wijzigingen en inhoudswijzigingen voor Azure-regio's en Azure Virtual-machines in [azure virtual machines planning en implementatie voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- 03/03/2020: Wijziging in [hoge beschikbaarheid voor SAP NW op Azure VM's op SLES met ANF voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) om over te schakelen op een efficiëntere ANF-volumeindeling
- 03/01/2020: Vernieuwde [back-uphandleiding voor SAP HANA op Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) om Azure Backup-service op te nemen. Verminderde en gecondenseerde inhoud in [SAP HANA Azure Backup op bestandsniveau](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level) en een derde document verwijderd dat betrekking heeft op back-up via schijfmomentopname. Inhoud wordt verwerkt in back-uphandleiding voor SAP HANA op Azure Virtual Machines 
- 02/27/2020: Wijziging in [hoge beschikbaarheid voor SAP NW op Azure VM's op SLES voor SAP-toepassingen,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)hoge beschikbaarheid voor SAP NW op Azure [VM's op SLES met ANF voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) en [Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SLES multi-SID-handleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) om clusterparameter "on fail" aan te passen
- 02/26/2020: Wijziging in [SAP HANA Azure-configuratie van virtuele machineopslag](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) om de keuze van het bestandssysteem voor HANA op Azure te verduidelijken
- 02/26/2020: Wijziging in [architectuur en scenario's met hoge beschikbaarheid voor SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) om de koppeling naar de HA voor SAP NetWeaver op Azure VM's op RHEL multi-SID-handleiding op te nemen
- 02/26/2020: Wijziging in [hoge beschikbaarheid voor SAP NW op Azure VM's op SLES voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), Hoge beschikbaarheid voor SAP NW op Azure [VM's op SLES met ANF voor SAP-toepassingen,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) [Azure VM's hoge beschikbaarheid voor SAP NetWeaver op RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) en [Azure VM's Hoge beschikbaarheid voor SAP NetWeaver op RHEL met Azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) om de verklaring te verwijderen dat multi-SIDCS AS/ERS-cluster niet wordt ondersteund
- 02/26/2020: Release van [hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op RHEL multi-SID-handleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid) om een koppeling toe te voegen aan de SUSE multi-SID-clusterhandleiding
- 02/25/2020: Wijziging in [architectuur met hoge beschikbaarheid en scenario's voor SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) om koppelingen toe te voegen aan nieuwere HA-artikelen
- 02/25/2020: Wijziging in [hoge beschikbaarheid van IBM Db2 LUW op Azure VM's op SUSE Linux Enterprise Server met Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) om te wijzen op document dat toegang tot openbaar eindpunt beschrijft met Standaard Azure Load balancer
- 02/21/2020: Volledige revisie van het artikel [SAP ASE Azure Virtual Machines DBMS-implementatie voor SAP-workload](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- 02/21/2020: Wijziging in [SAP HANA Azure-configuratie voor virtuele machineopslag](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) om nieuwe aanbeveling in streepgrootte voor /hana/gegevens weer te geven en instelling van I/O-planner toe te voegen
- 02/21/2020: Wijzigingen in HANA Large Instance documenten om nieuw gecertificeerde SKU's van S224 en S224m te vertegenwoordigen
- 02/21/2020: Wijziging in [Azure VM's hoge beschikbaarheid voor SAP NetWeaver op RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) en [Azure VM's hoge beschikbaarheid voor SAP NetWeaver op RHEL met Azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) om de clusterbeperkingen voor de replicatie 2-architectuur van de wachtrijserver (ENSA2) aan te passen
- 02/20/2020: Wijziging in [hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SLES multi-SID-handleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) om een koppeling toe te voegen aan de SUSE multi-SID-clusterhandleiding
- 02/13/2020: Wijzigingen in [de planning en implementatie van Azure Virtual Machines voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) om koppelingen naar nieuwe documenten te implementeren
- 02/13/2020: Nieuwe document [SAP-werkbelasting](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) toegevoegd op door Azure ondersteunde scenario voor virtuele machines
- 02/13/2020: Nieuw document toegevoegd [Welke SAP-software wordt ondersteund voor Azure-implementatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 02/13/2020: Wijziging in [hoge beschikbaarheid van IBM Db2 LUW op Azure VM's op Red Hat Enterprise Linux Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw) om te wijzen op document dat toegang tot openbaar eindpunt beschrijft met Standaard Azure Load balancer
- 02/13/2020: Voeg de nieuwe VM-typen toe aan [SAP-certificeringen en -configuraties die worden uitgevoerd op Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 02/13/2020: Nieuwe SAP-ondersteuningsnotities [toevoegen SAP-workloads op Azure: checklist planning en implementatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 02/13/2020: Wijziging in [Azure VM's hoge beschikbaarheid voor SAP NetWeaver op RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) en [Azure VM's hoge beschikbaarheid voor SAP NetWeaver op RHEL met Azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) om de time-outs van de clusterresources af te stemmen op de time-outs van Red Hat
- 02/11/2020: Release van [SAP HANA op Azure Large Instance-migratie naar Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)
- 02/07/2020: Wijziging in [openbare eindpuntconnectiviteit voor VM's met Azure Standard ILB in SAP HA-scenario's](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) om voorbeeld-NSG-schermafbeelding bij te werken
- 02/03/2020: Wijziging in [hoge beschikbaarheid voor SAP NW op Azure VM's op SLES voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) en hoge beschikbaarheid voor SAP NW op Azure [VM's op SLES met ANF voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) om de waarschuwing over het gebruik van streepje in de hostnamen van clusterknooppunten op SLES te verwijderen
- 01/28/2020: Wijziging in [hoge beschikbaarheid van SAP HANA op Azure VM's op RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) om de time-outs van de SAP HANA-clusterresources af te stemmen op de time-outaanbevelingen van Red Hat
- 01/17/2020: Wijzigen in [Azure-plaatsingsgroepen voor een optimale netwerklatentie met SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) om het gedeelte van het verplaatsen van bestaande VM's in een plaatsingsgroep voor nabijheid te wijzigen
- 01/17/2020: Wijziging in [SAP-workloadconfiguraties met Azure Availability Zones](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) om te wijzen op een procedure die metingen van latentie tussen beschikbaarheidszones automatiseert
- 01/16/2020: Wijzigen in [het installeren en configureren van SAP HANA (Large Instances) op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) om OS-releases aan te passen aan HANA IaaS-hardwaredirectory
- 01/16/2020: Wijzigingen in [hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SLES multi-SID-handleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) voor het toevoegen van instructies voor SAP-systemen, met behulp van enqueue server 2-architectuur (ENSA2)
- 01/10/2020: Wijzigingen in [SAP HANA-scale-out met stand-by node op Azure VM's met Azure NetApp Files op SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) en in [SAP HANA scale-out met stand-by node op Azure VM's met Azure NetApp Files op RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) om instructies toe te voegen over het permanent aanbrengen van `nfs4_disable_idmapping` wijzigingen.
- 01/10/2020: Wijzigingen in [hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SLES met Azure NetApp-bestanden voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) en in Azure Virtual Machines hoge beschikbaarheid voor SAP [NetWeaver op RHEL met Azure NetApp-bestanden voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) om instructies toe te voegen voor het monteren van NFSv4-volumes voor Azure NetApp Files.
- 12/23/2019: Release van [hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SLES multi-SID-handleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 12/18/2019: Release van [SAP HANA scale-out met stand-by node op Azure VM's met Azure NetApp Files op RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 11/21/2019: Wijzigingen in [SAP HANA-scale-out met stand-by node op Azure VM's met Azure NetApp-bestanden op SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) om de configuratie voor NFS ID-toewijzing te vereenvoudigen en de aanbevolen primaire netwerkinterface te wijzigen om routering te vereenvoudigen.
- 11/15/2019: Kleine wijzigingen in [hoge beschikbaarheid voor SAP NetWeaver op SUSE Linux Enterprise Server met Azure NetApp-bestanden voor SAP-toepassingen](high-availability-guide-suse-netapp-files.md) en hoge beschikbaarheid voor SAP [NetWeaver op Red Hat Enterprise Linux met Azure NetApp-bestanden voor SAP-toepassingen](high-availability-guide-rhel-netapp-files.md) om beperkingen van capaciteitspoolgrootte te verduidelijken en de verklaring te verwijderen dat alleen de NFSv3-versie wordt ondersteund.
- 11/12/2019: Release van [hoge beschikbaarheid voor SAP NetWeaver op Windows met Azure NetApp Files (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 11/08/2019: Wijzigingen in [hoge beschikbaarheid van SAP HANA op Azure VM's op SUSE Linux Enterprise Server](sap-hana-high-availability.md), SAP [HANA-systeemreplicatie instellen op Virtuele Azure-machines (VM's),](sap-hana-high-availability-rhel.md) [Azure Virtual Machines hoge beschikbaarheid voor SAP NetWeaver op SUSE Linux Enterprise Server voor SAP-toepassingen,](high-availability-guide-suse.md) [Azure Virtual Machines hoge beschikbaarheid voor SAP NetWeaver op SUSE Linux Enterprise Server met Azure NetApp Files](high-availability-guide-suse-netapp-files.md), Azure Virtual Machines hoge beschikbaarheid voor SAP [NetWeaver op Red Hat Enterprise Linux](high-availability-guide-rhel.md), Azure Virtual Machines hoge beschikbaarheid voor SAP [NetWeaver op Red Hat Enterprise Linux met Azure NetApp Files](high-availability-guide-rhel-netapp-files.md), Hoge beschikbaarheid voor [NFS op Azure VM's op SUSE Linux Enterprise Server](high-availability-guide-suse-nfs.md), [GlusterFS op Azure VM's op Red Hat Enterprise Linux voor SAP NetWeaver](high-availability-guide-rhel-glusterfs.md) om Azure standaard load balancer aan te bevelen  
- 11/08/2019: Wijzigingen in [checklist voor sap-workloadplanning en -implementatie](sap-deployment-checklist.md) om de aanbeveling voor versleuteling te verduidelijken  
- 11/04/2019: Wijzigingen in [het instellen van pacemaker op SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) om het cluster rechtstreeks te maken met unicast-configuratie  
- 10/29/2019: Release van [openbare eindpuntconnectiviteit voor virtuele machines met Azure Standard Load Balancer in SAP-scenario's met hoge beschikbaarheid](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 10/25/2019: Wijzigingen in [SAP HANA Azure virtual machine storage configuraties](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) en SAP [HANA scale-out met stand-by node op Azure VM's met Azure NetApp Files op SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) om NFS-protocol voor /hana/gedeeld volume te verduidelijken
- 10/22/2019: Wijziging in [hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SUSE Linux Enterprise Server voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), Hoge beschikbaarheid voor SAP [NetWeaver op Azure VM's op SUSE Linux Enterprise Server met Azure NetApp-bestanden voor SAP-toepassingen,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) [hoge beschikbaarheid voor NFS op Azure VM's op SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), Pacemaker instellen op [SUSE Linux Enterprise Server in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), Hoge beschikbaarheid van IBM [Db2 LUW op Azure VM's op SUSE Linux Enterprise Server met Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm), en Hoge beschikbaarheid van SAP [HANA op Azure VM's op SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) voor Azure Load-Balancer Detection Hardening
- Wijzigt de sectie ANF en de sectie koptekst in [SAP HANA Azure-opslagconfiguraties voor virtuele machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/21/2019: Release van [SAP HANA scale-out met stand-by node op Azure VM's met Azure NetApp Files op SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 10/16/2019: Verbroken koppelingen herstellen in [Back-up en herstellen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)
- 10/16/2019: Wijzig het minimaal aanbevolen besturingssysteem van SLES 12 SP3 naar SLES 12 SP4 bij [hoge beschikbaarheid van IBM Db2 LUW op Azure VM's op SUSE Linux Enterprise Server met Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
- 10/11/2019: Wijzigingen in ultraschijfopslagconfiguraties en introductie van ANF in [SAP HANA Azure-opslagconfiguraties voor virtuele machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/01/2019: Wijziging in afbeeldingen van [Azure-nabijheidsplaatsingsgroepen voor optimale netwerklatentie met SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) om meer duidelijkheid te krijgen
- 10/01/2019: Wijziging in [SAP HANA-infrastructuurconfiguraties en -bewerkingen op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) om verklaringen rond zeer beschikbaar NFS-aandeel te corrigeren voor /hana/shared. 
- 09/28/2019: Wijziging in [het instellen van pacemaker op Red Hat Enterprise Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) om SBD te verduidelijken als een hekwerkmechanisme wordt niet ondersteund op RHEL-clusters  
- 09/17/2019: Wijziging in netweaver-plannings- en implementatiehandleiding om termen rond VM-extensie voor SAP te verenigen  
- 08/22/2019: Wijzigingen in [het instellen van pacemaker op SUSE Linux Enterprise Server in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) om de URL's bij te werken voor het maken van aangepaste rollen  
- 08/16/2019: Wijzigingen in het instellen van [pacemaker op Red Hat Enterprise Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) om klanten eraan te herinneren de acties in de aangepaste rol bij te werken, als deze worden bijgewerkt naar de nieuwe versie van de Azure-afrasteringsagent  
- 08/15/2019: Wijzigingen in [SAP HANA Azure-configuratie van virtuele machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) om de algemene beschikbaarheid van Ultra-schijf (voorheen Ultra SSD) weer te geven
- 08/01/2019: Wijzigingen in [het instellen van pacemaker op SUSE Linux Enterprise Server in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) om wijzigingen specifiek voor SLES 15 te integreren 


