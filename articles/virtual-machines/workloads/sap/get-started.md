---
title: Aan de slag met SAP on Azure-Vm's | Microsoft Docs
description: Meer informatie over SAP-oplossingen die worden uitgevoerd op virtuele machines (Vm's) in Microsoft Azure
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
ms.date: 07/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7319adfc88eedb007677a78693ab0b2e514e646f
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171653"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Azure gebruiken om SAP-werkbelasting scenario's te hosten en uit te voeren

Wanneer u Microsoft Azure gebruikt, kunt u op een betrouw bare manier uw bedrijfskritische SAP-workloads en-scenario's uitvoeren op een schaalbaar, compatibel en bedrijf bewezen platform. U krijgt de schaal baarheid, flexibiliteit en kosten besparingen van Azure. Met de uitgebreide samen werking tussen micro soft en SAP kunt u SAP-toepassingen uitvoeren in ontwikkelings-en test-en productie scenario's in Azure en volledig worden ondersteund. Van SAP NetWeaver tot SAP S/4HANA, SAP BI op Linux naar Windows en SAP HANA naar SQL, wij hebben u gedekt.

Naast het hosten van SAP NetWeaver-scenario's met het verschillende DBMS op Azure, kunt u andere SAP-werkbelasting scenario's hosten, zoals SAP BI op Azure. 

De uniekheid van Azure voor SAP HANA is een aanbieding waarin Azure wordt onderscheiden. Azure biedt het gebruik van door de klant toegewezen bare-metal hardware voor het hosten van meer geheugen-en CPU-resource-veeleisende SAP-scenario's met betrekking tot SAP HANA. Gebruik deze oplossing om SAP HANA-implementaties uit te voeren die Maxi maal 24 TB (120 TB scale-out) geheugen vereisen voor S/4HANA of een andere SAP HANA-werk belasting. 

Bij het hosten van SAP-werkbelasting scenario's in azure kunnen ook vereisten voor identiteits integratie en eenmalige aanmelding worden gemaakt. Deze situatie kan zich voordoen wanneer u Azure Active Directory (Azure AD) gebruikt om verschillende SAP-onderdelen en SAP-software-as-a-Service (SaaS) of PaaS-aanbiedingen (platform-as-a-Service) te verbinden. Een lijst met dergelijke scenario's voor integratie en eenmalige aanmelding met Azure AD en SAP-entiteiten wordt beschreven en beschreven in de sectie ' AAD SAP identiteits integratie en eenmalige aanmelding '.

## <a name="changes-to-the-sap-workload-section"></a>Wijzigingen in de sectie SAP-workload
Wijzigingen in documenten in de sectie SAP on Azure workload worden aan het einde van dit artikel vermeld. De vermeldingen in het wijzigingslog bestand worden ongeveer 180 dagen bewaard.

## <a name="you-want-to-know"></a>U wilt weten
Als u specifieke vragen hebt, gaat u naar specifieke documenten of stromen in deze sectie van de start pagina. U wilt het volgende weten:

- Welke virtuele machines van Azure en HANA-eenheden voor het grote exemplaar worden ondersteund voor de SAP-software en de versies van het besturings systeem. Het document lezen [welke SAP-software wordt ondersteund voor Azure-implementatie](./sap-supported-product-on-azure.md) voor antwoorden en het proces voor het vinden van de informatie
- Welke SAP-implementatie scenario's worden ondersteund met Azure Vm's en HANA grote instanties. Informatie over de ondersteunde scenario's vindt u in de documenten:
    - [SAP-workload in scenario's met virtuele Azure-machines](./sap-planning-supported-configurations.md)
    - [Ondersteunde scenario's voor HANA grote instanties](./hana-supported-scenario.md)
- Wat Azure-Services, Azure VM-typen en Azure Storage-services zijn beschikbaar in de verschillende Azure-regio's. Controleer de [beschik bare site producten per regio](https://azure.microsoft.com/global-infrastructure/services/) 
- Zijn HA-frames van derden, naast Windows en pacemaker, ook wel ondersteund? Controleer het onderste deel van de [SAP-ondersteunings opmerking #1928533](https://launchpad.support.sap.com/#/notes/1928533)

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA op Azure (grote exemplaren)

Een reeks documenten leidt u door SAP HANA op Azure (grote instanties) of voor korte, HANA grote instanties. Voor informatie over HANA grote instanties begint u met het [overzicht van documenten en de architectuur van SAP Hana op Azure (grote exemplaren)](./hana-overview-architecture.md) en gaat u verder met de gerelateerde documentatie in de sectie van het grote exemplaar van Hana



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA op virtuele machines van Azure
In deze sectie van de documentatie worden verschillende aspecten van SAP HANA besproken. Als vereiste moet u bekend zijn met de belangrijkste services van Azure die elementaire services van Azure IaaS bieden. U hebt dus kennis nodig van Azure compute, Storage en Networks. Veel van deze onderwerpen worden behandeld in de [Azure-plannings handleiding](./planning-guide.md)voor SAP NetWeaver. 

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver geïmplementeerd op virtuele machines van Azure
In deze sectie vindt u de plannings-en implementatie documentatie voor SAP NetWeaver en Business One in Azure. De documentatie is gericht op de basis beginselen en het gebruik van niet-HANA-data bases met een SAP-werk belasting op Azure. De documenten en artikelen voor hoge Beschik baarheid zijn ook de basis voor HANA-hoge Beschik baarheid in azure, zoals:

- [Azure-plannings handleiding](./planning-guide.md). 
- [SAP Business One op virtuele machines van Azure](./business-one-azure.md)
- [Een toepassing met meerdere lagen SAP NetWeaver beveiligen met behulp van Site Recovery](../../../site-recovery/site-recovery-sap.md)
- [SAP LaMa-connector voor Azure](./lama-installation.md)

Zie voor informatie over niet-HANA-data bases in een SAP-werk belasting op Azure:

- [Overwegingen voor de implementatie van Azure Virtual Machines DBMS voor SAP-workloads](./dbms_guide_general.md)
- [SQL Server Azure Virtual Machines DBMS-implementatie voor SAP net-Weaver](./dbms_guide_sqlserver.md)
- [DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor Oracle](./dbms_guide_oracle.md)
- [Implementatie van IBM DB2 Azure Virtual Machines DBMS voor SAP-workload](./dbms_guide_ibm.md)
- [DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor SAP ASE](./dbms_guide_sapase.md)
- [SAP MaxDB, Live cache en implementatie van inhouds server op virtuele Azure-machines](./dbms_guide_maxdb.md)

Zie de sectie ' SAP HANA op virtuele machines van Azure ' voor informatie over SAP HANA data bases in Azure.

Zie voor informatie over hoge Beschik baarheid van een SAP-werk belasting op Azure:

- [Azure Virtual Machines hoge Beschik baarheid voor SAP NetWeaver](./sap-high-availability-guide-start.md)

Dit document verwijst naar verschillende documenten van de architectuur en het scenario. In latere scenario's worden documenten beschreven met koppelingen naar gedetailleerde technische documenten waarin de implementatie en configuratie van de verschillende methoden voor hoge Beschik baarheid wordt uitgelegd. De verschillende documenten die laten zien hoe u hoge Beschik baarheid kunt instellen en configureren voor een werk belasting van SAP NetWeaver Linux en Windows-besturings systemen.


Zie voor informatie over de integratie tussen Azure Active Directory (Azure AD) en SAP-services en eenmalige aanmelding:

- [Zelf studie: integratie Azure Active Directory met SAP-Cloud voor klant](../../../active-directory/saas-apps/sap-customer-cloud-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelf studie: integratie met SAP Cloud platform identiteits verificatie Azure Active Directory](../../../active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelfstudie: Azure Active Directory-integratie met SAP Cloud Platform](../../../active-directory/saas-apps/sap-hana-cloud-platform-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelfstudie: Azure Active Directory-integratie met SAP NetWeaver](../../../active-directory/saas-apps/sap-netweaver-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelfstudie: Azure Active Directory-integratie met SAP Business ByDesign](../../../active-directory/saas-apps/sapbusinessbydesign-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelf studie: integratie Azure Active Directory met SAP HANA](../../../active-directory/saas-apps/saphana-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Uw S/4HANA-omgeving: Fiori launchpad SAML eenmalige aanmelding met Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Zie voor informatie over de integratie van Azure-Services in SAP-onderdelen:

- [SAP HANA gebruiken in Power BI Desktop](/power-bi/desktop-sap-hana)
- [DirectQuery en SAP HANA](/power-bi/desktop-directquery-sap-hana)
- [De SAP BW-connector gebruiken in Power BI Desktop](/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory biedt integratie van SAP HANA- en Business Warehouse-gegevens](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Wijzigingen logboek

- 07/23/2020: de functie [opslaan op SAP Hana large instances is toegevoegd aan een Azure reservation](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md) -artikel waarin wordt uitgelegd wat u moet weten voordat u een SAP Hana large instances reservering koopt en hoe u de aankoop kunt doen
- 07/16/2020: beschrijving van het gebruik van Azure PowerShell voor het installeren van een nieuwe VM-extensie voor SAP in de [implementatie handleiding](deployment-guide.md)
- 7/04/2020: release van [Azure monitor voor SAP-oplossingen (preview-versie)](./azure-monitor-overview.md)
- 07/01/2020: minder dure opslag configuratie voorgesteld op basis van Azure Premium Storage burst-functionaliteit in document [SAP Hana opslag configuraties voor virtuele Azure-machines](./hana-vm-operations-storage.md) 
- 06/24/2020: wijziging in instellen van [pacemaker op sles in azure](./high-availability-guide-suse-pacemaker.md) voor het vrijgeven van nieuwe verbeterde Azure Fence-agent en meer robuuste STONITH-configuratie voor apparaten, gebaseerd op de Azure Fence-agent 
- 06/24/2020: wijziging in instellen van [pacemaker op RHEL in azure](./high-availability-guide-rhel-pacemaker.md) voor het vrijgeven van meer ROBUUSTe STONITH-configuratie
- 06/23/2020: wijzigingen in [Azure virtual machines planning en implementatie van SAP NetWeaver](./planning-guide.md) Guide en inleiding van [Azure Storage types voor SAP-werkbelasting](./planning-guide-storage.md) gids
- 06/22/2020: Voeg installatie stappen voor nieuwe VM-extensie voor SAP toe aan de [implementatie handleiding](deployment-guide.md)
- 06/16/2020: wijziging in [open bare-eindpunt connectiviteit voor vm's met behulp van Azure Standard ILB in SAP ha-scenario's](./high-availability-guide-standard-load-balancer-outbound-connections.md) om een koppeling toe te voegen aan SuSE open bare Cloud Infrastructure 101-documentatie 
- 06/10/2020: nieuwe HLI-Sku's toevoegen aan [beschik bare sku's voor HLI](./hana-available-skus.md) en [SAP Hana (grote exemplaren) opslag architectuur](./hana-storage-architecture.md)
- 05/21/2020: wijziging in het [instellen van pacemaker op sles in azure](./high-availability-guide-suse-pacemaker.md) en het [instellen van pacemaker op RHEL in azure](./high-availability-guide-rhel-pacemaker.md) om een koppeling toe te voegen aan [open bare-eindpunt connectiviteit voor Vm's met behulp van Azure Standard ILB in SAP ha-scenario's](./high-availability-guide-standard-load-balancer-outbound-connections.md)  
- 05/19/2020: belang rijk bericht toevoegen dat geen hoofd volume groep mag gebruiken bij gebruik van LVM voor HANA-gerelateerde volumes in [SAP Hana opslag configuraties van virtuele Azure-machines](./hana-vm-operations-storage.md)
- 05/19/2020: een nieuw ondersteund besturings systeem voor HANA grote instantie type II toevoegen in [compatibele besturings systemen voor Hana grote instanties](/- azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance)
- 05/12/2020: wijziging in [open bare-eindpunt connectiviteit voor vm's met behulp van Azure Standard ILB in SAP ha-scenario's](./high-availability-guide-standard-load-balancer-outbound-connections.md) om koppelingen bij te werken en informatie toe te voegen voor de firewall configuratie van een derde partij
- 05/11/2020: wijziging in [hoge Beschik baarheid van SAP Hana op virtuele machines van Azure op SLES](./sap-hana-high-availability.md) om resource persistentie in te stellen op 0 voor de netcat-resource, omdat dit leidt tot meer gestroomlijnde failover 
- 05/05/2020: wijzigingen in [Azure virtual machines planning en implementatie van SAP NetWeaver](./planning-guide.md) om aan te duiden dat Gen2-implementaties beschikbaar zijn voor Mv1 VM-familie
- 04/24/2020: wijzigingen in [SAP Hana uitschalen met stand-by-knoop punt op virtuele machines van Azure met ANF op SLES](./sap-hana-scale-out-standby-netapp-files-suse.md), in [SAP Hana uitschalen met het stand-by-knoop punt op Azure vm's met ANF op RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md), [hoge Beschik baarheid voor SAP net-Weaver](./high-availability-guide-suse-netapp-files.md) op Azure Vm's op SLES met ANF en [hoge Beschik baarheid voor SAP](./high-availability-guide-rhel-netapp-files.md) NetWeaver op Azure vm's op RHEL met ANF
- 04/22/2020: wijziging in [hoge Beschik baarheid van SAP Hana op virtuele machines van Azure op SLES](./sap-hana-high-availability.md) om meta kenmerk `is-managed` uit de instructies te verwijderen, omdat het een conflict veroorzaakt met het plaatsen van het cluster in of uit de onderhouds modus
- 04/21/2020: SQL Azure DB toegevoegd als ondersteund DBMS voor SAP (Hybris) commerce platform 1811 en hoger in artikelen [wat SAP-software wordt ondersteund voor Azure-implementaties](./sap-supported-product-on-azure.md) en [SAP-certificeringen en-configuraties die worden uitgevoerd op Microsoft Azure](./sap-certifications.md)
- 04/16/2020: SAP HANA als ondersteund DBMS voor SAP (Hybris) commerce platform in artikelen toegevoegd [wat SAP-software wordt ondersteund voor Azure-implementaties](./sap-supported-product-on-azure.md) en [SAP-certificeringen en-configuraties die worden uitgevoerd op Microsoft Azure](./sap-certifications.md)
- 04/13/2020: correct naar exacte SAP ASE-release nummers in [SAP ASE Azure virtual machines DBMS-implementatie voor SAP-workload](./dbms_guide_sapase.md)
- 04/07/2020: wijziging in instellen van [pacemaker op sles in azure](./high-availability-guide-suse-pacemaker.md) om de Cloud-netconfig-Azure-instructies te verduidelijken
- 04/06/2020: wijzigingen in [SAP Hana uitschalen met het stand-by-knoop punt op virtuele machines van Azure met Azure NetApp files op SLES](./sap-hana-scale-out-standby-netapp-files-suse.md) en in [SAP Hana uitschalen met stand-by-knoop punt op virtuele machines van Azure met Azure NetApp files op RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md) om verwijzingen naar NetApp [tr-4435](https://www.netapp.com/us/media/tr-4746.pdf) te verwijderen (vervangen door [tr-4746](https://www.netapp.com/us/media/tr-4746.pdf))
- 03/31/2020: wijziging in [hoge Beschik baarheid van SAP Hana op virtuele machines van Azure op SLES](./sap-hana-high-availability.md) en [hoge Beschik baarheid van SAP Hana op Azure vm's op RHEL](./sap-hana-high-availability-rhel.md) om instructies toe te voegen hoe Stripe-grootte moet worden opgegeven bij het maken van striped volumes
- 03/27/2020: wijziging in [hoge Beschik baarheid voor SAP NW op Azure vm's op SLES met ANF voor SAP-toepassingen](./high-availability-guide-suse-netapp-files.md) om de opties voor het bestands systeem koppelen af te stemmen op NetApp TR-4746 (de optie voor synchronisatie koppeling verwijderen)
- 03/26/2020: wijziging in [hoge Beschik baarheid voor SAP NetWeaver op Azure vm's in de SLES-hand leiding voor multi-sid](./high-availability-guide-suse-multi-sid.md) om referentie toe te voegen aan NetApp TR-4746
- 03/26/2020: wijziging in [hoge Beschik baarheid voor SAP NetWeaver op Azure vm's op SLES voor SAP-toepassingen](./high-availability-guide-suse.md), [hoge Beschik baarheid voor SAP NetWeaver op Azure vm's op SLES met Azure NetApp files voor SAP-toepassingen](./high-availability-guide-suse-netapp-files.md), [hoge Beschik baarheid voor NFS op Azure vm's op SLES](./high-availability-guide-suse-nfs.md), [hoge Beschik baarheid voor SAP NetWeaver op Azure vm's in de SLES-hand leiding voor meerdere sid's](./high-availability-guide-suse-multi-sid.md), hoge [Beschik baarheid voor SAP NetWeaver op Azure Vm's op RHEL voor SAP-toepassingen en](./high-availability-guide-rhel.md) hoge [beschik BAARHEID voor sap netweaver op Azure vm's op RHEL met Azure NetApp files voor SAP-toepassingen voor](./high-availability-guide-rhel-netapp-files.md) het bijwerken van diagrammen Azure Load Balancer
- 03/19/2020: belang rijke revisie van document [Quick Start: hand matige installatie van SAP Hana met één exemplaar op azure virtual machines](./hana-get-started.md) voor de [installatie van SAP HANA op Azure virtual machines](./hana-get-started.md)
- 03/17/2020: wijziging in instellen van [pacemaker op SuSE Linux Enterprise Server in azure](./high-availability-guide-suse-pacemaker.md) voor het verwijderen van de SBD-configuratie-instelling die niet meer nodig is
- 03/16/2020: uitleg van het scenario voor kolom certificering in SAP HANA IaaS gecertificeerd platform in [welke SAP-software wordt ondersteund voor Azure-implementaties](./sap-supported-product-on-azure.md)
- 03/11/2020: wijziging in [SAP-werk belasting op door Azure virtual machine ondersteunde scenario's](./sap-planning-supported-configurations.md) om meerdere data bases per DBMS-exemplaar te verduidelijken
- 03/11/2020: wijziging in [Azure virtual machines planning en implementatie voor SAP NetWeaver uitleg van](./planning-guide.md) de virtuele machines van de eerste en tweede generatie
- 03/10/2020: wijziging in [SAP Hana opslag configuraties van virtuele Azure-machines](./hana-vm-operations-storage.md) om echte bestaande doorvoer limieten van ANF te verduidelijken
- 03/09/2020: wijziging in [hoge Beschik baarheid voor SAP NetWeaver op Azure vm's op SuSE Linux Enterprise Server voor SAP-toepassingen](./high-availability-guide-suse.md), [hoge Beschik baarheid voor SAP NetWeaver op azure vm's op SuSE Linux Enterprise Server met Azure NetApp files voor SAP-toepassingen](./high-availability-guide-suse-netapp-files.md), [hoge Beschik baarheid voor NFS op Azure-vm's op SuSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md), het [instellen van pacemaker op SuSE Linux Enterprise Server in azure, een](./high-availability-guide-suse-pacemaker.md) [hoge Beschik baarheid van IBM Db2 LUW op Azure-vm's in SUSE Linux Enterprise Server met pacemaker](./dbms-guide-ha-ibm.md), hoge beschik [baarheid van SAP Hana op Azure-Vm's op SuSE Linux Enterprise Server](./sap-hana-high-availability.md) en [hoge Beschik baarheid voor SAP NetWeaver op Azure vm's in de SLES-hand leiding voor multi-sid](./high-availability-guide-suse-multi-sid.md) 
- 03/05/2020: structuur wijzigingen en wijzigingen in de inhoud voor Azure-regio's en Azure virtual machines in [azure virtual machines planning en implementatie voor SAP net-Weaver](./planning-guide.md)
- 03/03/2020: wijziging in [hoge Beschik baarheid voor SAP NW op Azure vm's op SLES met ANF voor SAP-toepassingen](./high-availability-guide-suse-netapp-files.md) om te wijzigen in een EFFICIËNTere ANF-volume-indeling
- 03/01/2020: de herwerkte [back-upgids voor SAP Hana op Azure virtual machines](./sap-hana-backup-guide.md) om Azure backup service te kunnen gebruiken. Gereduceerde en versmalde inhoud in [SAP HANA Azure Backup op bestands niveau](./sap-hana-backup-file-level.md) en heeft een derde document verwijderd met een back-up via een schijf momentopname. Inhoud wordt verwerkt in de back-upgids voor SAP HANA op Azure Virtual Machines 
- 27 februari 2020: wijziging in [hoge Beschik baarheid voor SAP NW op Azure vm's op SLES voor SAP-toepassingen](./high-availability-guide-suse.md), [hoge beschik BAARHEID voor SAP NW op Azure vm's op SLES met ANF voor SAP-toepassingen](./high-availability-guide-suse-netapp-files.md) en [hoge Beschik baarheid voor SAP NetWeaver op Azure VM'S in SLES multi-sid-hand leiding](./high-availability-guide-suse-multi-sid.md) voor het aanpassen van de cluster parameter "on Fail"
- 26 februari 2020: Wijzig in [SAP Hana opslag configuraties van virtuele Azure-machines](./hana-vm-operations-storage.md) om de bestandssysteem keuze voor Hana op Azure te verduidelijken
- 26 februari 2020: wijziging in [architectuur met hoge Beschik baarheid en scenario's voor SAP](./sap-high-availability-architecture-scenarios.md) om de koppeling toe te voegen aan de ha voor SAP NetWeaver op Azure vm's in de RHEL-hand leiding voor multi-sid
- 26 februari 2020: wijziging in [hoge Beschik baarheid voor SAP NW op Azure vm's op SLES voor SAP-toepassingen](./high-availability-guide-suse.md), [hoge beschik BAARHEID voor SAP NW op Azure vm's op SLES met ANF for SAP-toepassingen](./high-availability-guide-suse-netapp-files.md), Azure- [vm's hoge Beschik baarheid voor SAP NetWeaver op RHEL](./high-availability-guide-rhel.md) en [Azure vm's hoge beschik](./high-availability-guide-rhel-netapp-files.md) baarheid voor SAP NetWeaver op RHEL met Azure NetApp files om de instructie te verwijderen die multi
- 26 februari 2020: release van [hoge Beschik baarheid voor SAP NetWeaver op Azure vm's in de RHEL-hand leiding voor multi-sid](./high-availability-guide-rhel-multi-sid.md) om een koppeling toe te voegen aan de SuSE multi-sid-cluster gids
- 02/25/2020: wijziging in [architectuur met hoge Beschik baarheid en scenario's voor SAP](./sap-high-availability-architecture-scenarios.md) om koppelingen toe te voegen aan nieuwere ha-artikelen
- 25 februari 2020: wijziging in [hoge Beschik baarheid van IBM Db2-LUW op Azure-vm's op SuSE Linux Enterprise Server met pacemaker](./dbms-guide-ha-ibm.md) om te verwijzen naar document met een beschrijving van de toegang tot het open bare eind punt met Standard Azure Load Balancer
- 21 februari 2020: volledige revisie van het artikel [SAP ASE Azure virtual machines DBMS-implementatie voor SAP-workload](./dbms_guide_sapase.md)
- 21 februari 2020: Wijzig in [SAP Hana opslag configuratie van de virtuele machine van Azure](./hana-vm-operations-storage.md) om nieuwe aanbeveling te vertegenwoordigen in Stripe-grootte voor/Hana/data en het toevoegen van een I/O-planner.
- 21 februari 2020: wijzigingen in HANA-documenten voor grote instanties om nieuwe gecertificeerde Sku's van S224 en S224m weer te geven
- 21 februari 2020: wijziging in [Azure-vm's hoge Beschik baarheid voor SAP NetWeaver op RHEL](./high-availability-guide-rhel.md) en [Azure-vm's hoge Beschik baarheid voor SAP NetWeaver op RHEL met Azure NetApp files](./high-availability-guide-rhel-netapp-files.md) om de cluster beperkingen aan te passen voor Server replicatie 2 in de wachtrij plaatsen (ENSA2)
- 20 februari 2020: wijziging in [hoge Beschik baarheid voor SAP NetWeaver op Azure vm's in de SLES-hand leiding voor multi-sid](./high-availability-guide-suse-multi-sid.md) om een koppeling toe te voegen aan de SuSE multi-sid-cluster gids
- 13 februari 2020: wijzigingen in [Azure virtual machines planning en implementatie van SAP NetWeaver](./planning-guide.md) om koppelingen naar nieuwe documenten te implementeren
- 13 februari 2020: er is een nieuwe [SAP-workload voor het document toegevoegd op het Ondersteunde scenario van de virtuele machine van Azure](./sap-planning-supported-configurations.md)
- 13 februari 2020: nieuw document toegevoegd [welke SAP-software wordt ondersteund voor Azure-implementatie](./sap-supported-product-on-azure.md)
- 13 februari 2020: wijziging in [hoge Beschik baarheid van IBM Db2-LUW op Azure-vm's op Red Hat Enterprise Linux server](./high-availability-guide-rhel-ibm-db2-luw.md) om te verwijzen naar document met een beschrijving van de toegang tot het open bare eind punt met Standard Azure Load Balancer
- 13 februari 2020: Voeg de nieuwe VM-typen toe aan [SAP-certificeringen en-configuraties die worden uitgevoerd op Microsoft Azure](./sap-certifications.md)
- 13 februari 2020: nieuwe SAP-ondersteunings notities toevoegen [SAP-workloads op Azure: controle lijst voor planning en implementatie](./sap-deployment-checklist.md)
- 13 februari 2020: wijziging in [Azure-vm's hoge Beschik baarheid voor SAP NetWeaver op RHEL](./high-availability-guide-rhel.md) en [Azure-vm's hoge Beschik baarheid voor SAP NetWeaver op RHEL met Azure NetApp files](./high-availability-guide-rhel-netapp-files.md) voor het uitlijnen van de time-outs van de cluster resources op de aanbevelingen van Red Hat time-out
- 11 februari 2020: release van [SAP Hana voor de migratie van grote Azure-instanties naar azure virtual machines](./hana-large-instance-virtual-machine-migration.md)
- Februari 07, 2020: wijziging in de [connectiviteit van open bare eind punten voor vm's met behulp van Azure Standard ILB in SAP ha-scenario's](./high-availability-guide-standard-load-balancer-outbound-connections.md) voor het bijwerken van de voorbeeld afbeelding van NSG
- 03 februari 2020: wijziging in [hoge Beschik baarheid voor SAP NW op Azure vm's op SLES voor SAP-toepassingen](./high-availability-guide-suse.md) en [hoge beschik BAARHEID voor SAP NW op Azure-vm's op SLES met ANF for SAP-toepassingen](./high-availability-guide-suse-netapp-files.md) om de waarschuwing over het gebruik van een streepje in de hostnamen van cluster knooppunten op SLES te verwijderen
- 28 januari 2020: wijziging in [hoge Beschik baarheid van SAP Hana op virtuele machines van Azure op RHEL](./sap-hana-high-availability-rhel.md) om de time-outs van de SAP Hana cluster resources af te stemmen op de aanbevelingen voor Red Hat-time-outs
- 17 januari 2020: wijziging in [Azure proximity-plaatsings groepen voor optimale netwerk latentie met SAP-toepassingen](./sap-proximity-placement-scenarios.md) om de sectie van het verplaatsen van bestaande vm's in een proximity-plaatsings groep te wijzigen
- 17 januari 2020: wijziging in [SAP-werkbelasting configuraties met Azure-beschikbaarheidszones](./sap-ha-availability-zones.md) om te verwijzen naar een procedure die de metingen van de latentie tussen Beschikbaarheidszones automatiseert
- 16 januari 2020: wijzigen in [het installeren en configureren van SAP Hana (grote exemplaren) op Azure](./hana-installation.md) om de versies van besturings systemen aan Hana IaaS hardware-map aan te passen
- 16 januari 2020: wijzigingen in [hoge Beschik baarheid voor SAP NetWeaver op Azure vm's in de SLES-hand leiding voor multi-sid](./high-availability-guide-suse-multi-sid.md) om instructies toe te voegen voor SAP-systemen met behulp van Server 2-architectuur (in wachtrij plaatsen) (ENSA2)
- 10 januari 2020: wijzigingen in [SAP Hana uitschalen met een stand-by-knoop punt op virtuele machines van Azure met Azure NetApp files op SLES](./sap-hana-scale-out-standby-netapp-files-suse.md) en in [SAP Hana uitschalen met het stand-by-knoop punt op virtuele machines van Azure met Azure NetApp files op RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md) om instructies toe te voegen voor het permanent maken van `nfs4_disable_idmapping` wijzigingen.
- 10 januari 2020: wijzigingen in [hoge Beschik baarheid voor SAP NetWeaver op Azure vm's op SLES met Azure NetApp files voor SAP-toepassingen](./high-availability-guide-suse-netapp-files.md) en in [Azure virtual machines hoge Beschik baarheid voor SAP NetWeaver op RHEL met Azure NetApp files voor SAP-toepassingen](./high-availability-guide-rhel-netapp-files.md) om instructies toe te voegen voor het koppelen van Azure NetApp files NFSv4-volumes.
- 23 december 2019: release van [hoge Beschik baarheid voor SAP NetWeaver op Azure vm's in de SLES-hand leiding voor multi-sid](./high-availability-guide-suse-multi-sid.md)
- 18 december 2019: release van [SAP Hana uitschalen met stand-by-knoop punt op virtuele machines van Azure met Azure NetApp files op RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- 21 november 2019: wijzigingen in [SAP Hana uitschalen met het knoop punt stand-by op virtuele machines van Azure met Azure NetApp files op SuSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md) om de configuratie voor NFS-id-toewijzing te vereenvoudigen en de aanbevolen primaire netwerk interface te wijzigen om de route ring te vereenvoudigen.
- 15 november 2019: kleine wijzigingen in [hoge Beschik baarheid voor SAP NetWeaver op SuSE Linux Enterprise Server met Azure NetApp files voor SAP-toepassingen](high-availability-guide-suse-netapp-files.md) en [hoge Beschik baarheid voor SAP NetWeaver op Red Hat Enterprise Linux met Azure NetApp files voor SAP-toepassingen](high-availability-guide-rhel-netapp-files.md) om te verduidelijken de grootte beperkingen van de capaciteits groep en de instructie Remove die alleen NFSv3-versie wordt ondersteund.
- 12 november 2019: release van [hoge Beschik baarheid voor SAP NetWeaver op Windows met Azure NetApp files (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 8 november 2019: wijzigingen in de [hoge Beschik baarheid van SAP Hana op virtuele machines van Azure op SuSE Linux Enterprise Server](sap-hana-high-availability.md), [SAP Hana systeem replicatie instellen op Azure virtual machine (vm's)](sap-hana-high-availability-rhel.md), [Azure virtual machines hoge Beschik baarheid voor sap netweaver op SuSE Linux Enterprise Server voor sap-toepassingen](high-availability-guide-suse.md), [Azure virtual machines hoge Beschik baarheid voor sap netweaver op SuSE Linux Enterprise Server met Azure NetApp files](high-availability-guide-suse-netapp-files.md), [Azure virtual machines hoge](high-availability-guide-rhel.md)Beschik baarheid voor SAP NetWeaver op Red Hat Enterprise Linux, Azure virtual machines hoge Beschik baarheid voor [SAP NetWeaver op Red Hat Enterprise Linux met Azure NetApp files](high-availability-guide-rhel-netapp-files.md), [hoge Beschik baarheid voor NFS op Azure vm's](high-availability-guide-suse-nfs.md)op SuSE Linux Enterprise Server, [GlusterFS op Azure-vm's op Red Hat Enterprise Linux voor SAP NetWeaver](high-availability-guide-rhel-glusterfs.md) om Azure Standard Load Balancer aan te bevelen  
- 8 november 2019: wijzigingen in de [SAP-werk belasting planning en implementatie controle lijst](sap-deployment-checklist.md) om de aanbeveling voor de versleuteling te verduidelijken  
- 4 november 2019: wijzigingen in het [instellen van pacemaker op SuSE Linux Enterprise Server in azure](high-availability-guide-suse-pacemaker.md) om het cluster rechtstreeks te maken met unicast-configuratie  
