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
ms.date: 08/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 44ee910aa1a5f3c1a703b9ed4202d395359970cb
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099951"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Azure gebruiken om SAP-werkbelasting scenario's te hosten en uit te voeren

Wanneer u Microsoft Azure gebruikt, kunt u op een betrouw bare manier uw bedrijfskritische SAP-workloads en-scenario's uitvoeren op een schaalbaar, compatibel en bedrijf bewezen platform. U krijgt de schaal baarheid, flexibiliteit en kosten besparingen van Azure. Met de uitgebreide samen werking tussen micro soft en SAP kunt u SAP-toepassingen uitvoeren in ontwikkelings-en test-en productie scenario's in Azure en volledig worden ondersteund. Van SAP NetWeaver tot SAP S/4HANA, SAP BI op Linux naar Windows en SAP HANA naar SQL, wij hebben u gedekt.

Naast het hosten van SAP NetWeaver-scenario's met het verschillende DBMS op Azure, kunt u andere SAP-werkbelasting scenario's hosten, zoals SAP BI op Azure. 

De uniekheid van Azure voor SAP HANA is een aanbieding waarin Azure wordt onderscheiden. Azure biedt het gebruik van door de klant toegewezen bare-metal hardware voor het hosten van meer geheugen-en CPU-resource-veeleisende SAP-scenario's met betrekking tot SAP HANA. Gebruik deze oplossing om SAP HANA-implementaties uit te voeren die Maxi maal 24 TB (120-TB scale-out) geheugen vereisen voor S/4HANA of een andere SAP HANA werk belasting. 

Bij het hosten van SAP-werkbelasting scenario's in azure kunnen ook vereisten voor identiteits integratie en eenmalige aanmelding worden gemaakt. Deze situatie kan zich voordoen wanneer u Azure Active Directory (Azure AD) gebruikt om verschillende SAP-onderdelen en SAP-software-as-a-Service (SaaS) of PaaS-aanbiedingen (platform-as-a-Service) te verbinden. Een lijst met dergelijke scenario's voor integratie en eenmalige aanmelding met Azure AD en SAP-entiteiten wordt beschreven en beschreven in de sectie ' AAD SAP identiteits integratie en eenmalige aanmelding '.

## <a name="changes-to-the-sap-workload-section"></a>Wijzigingen in de sectie SAP-workload
Wijzigingen in documenten in de sectie SAP on Azure workload worden aan het einde van dit artikel vermeld.


## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA op Azure (grote exemplaren)

Een reeks documenten leidt u door SAP HANA op Azure (grote instanties) of voor korte, HANA grote instanties. Zie voor informatie over de volgende gebieden van HANA grote instanties:

- [Overzicht van SAP HANA op Azure (grote exemplaren)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Architectuur van SAP HANA op Azure (grote exemplaren)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infra structuur en connectiviteit voor het SAP HANA van Azure (grote exemplaren)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [SAP HANA installeren op Azure (grote exemplaren)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Hoge Beschik baarheid en herstel na nood gevallen van SAP HANA op Azure (grote exemplaren)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Problemen oplossen en controleren SAP HANA op Azure (grote exemplaren)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Volgende stappen:

- Lees [overzicht en architectuur van SAP Hana op Azure (grote exemplaren)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA op virtuele machines van Azure
In deze sectie van de documentatie worden verschillende aspecten van SAP HANA besproken. Als vereiste moet u bekend zijn met de belangrijkste services van Azure die elementaire services van Azure IaaS bieden. U hebt dus kennis nodig van Azure compute, Storage en Networks. Veel van deze onderwerpen worden behandeld in de [Azure-plannings handleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)voor SAP NetWeaver. 

Voor informatie over HANA op Azure raadpleegt u de volgende artikelen en de bijbehorende subartikelen:

- [Snelstart: Hand matige installatie van SAP HANA met één exemplaar op virtuele machines van Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [SAP S/4HANA of BW/4HANA implementeren op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Configuraties en bewerkingen van SAP HANA-infrastructuur in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA hoge Beschik baarheid voor virtuele machines van Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Beschik baarheid van SAP HANA binnen een Azure-regio](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Beschik baarheid van SAP HANA in azure-regio's](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Hoge Beschik baarheid van SAP HANA op virtuele machines van Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Back-upgids voor SAP HANA op virtuele machines van Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Azure Backup op bestands niveau SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Back-up SAP HANA op basis van moment opnamen van opslag](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver geïmplementeerd op virtuele machines van Azure
In deze sectie vindt u de plannings-en implementatie documentatie voor SAP NetWeaver en Business One in Azure. De documentatie is gericht op de basis beginselen en het gebruik van niet-HANA-data bases met een SAP-werk belasting op Azure. De documenten en artikelen voor hoge Beschik baarheid zijn ook de basis voor HANA-hoge Beschik baarheid in azure, zoals:

- [SAP Business One op virtuele machines van Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [SAP IDE'S EHP7 SP3 implementeren voor SAP ERP 6,0 op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [SAP NetWeaver uitvoeren op Microsoft Azure SUSE Linux Vm's](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Azure Virtual Machines planning en implementatie voor SAP net-Weaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Azure Virtual Machines-implementatie voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Een toepassing met meerdere lagen SAP NetWeaver beveiligen met behulp van Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [SAP LaMa-connector voor Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Zie voor informatie over niet-HANA-data bases in een SAP-werk belasting op Azure:

- [Overwegingen voor de implementatie van Azure Virtual Machines DBMS voor SAP-workloads](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SQL Server Azure Virtual Machines DBMS-implementatie voor SAP net-Weaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor Oracle](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor IBM DB2](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor SAP ASE](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [SAP MaxDB, Live cache en implementatie van inhouds server op virtuele Azure-machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Zie de sectie ' SAP HANA op virtuele machines van Azure ' voor informatie over SAP HANA data bases in Azure.

Zie voor informatie over hoge Beschik baarheid van een SAP-werk belasting op Azure:

- [Azure Virtual Machines hoge Beschik baarheid voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Dit document verwijst naar verschillende documenten van de architectuur en het scenario. In latere scenario's worden documenten beschreven met koppelingen naar gedetailleerde technische documenten waarin de implementatie en configuratie van de verschillende methoden voor hoge Beschik baarheid wordt uitgelegd. De verschillende documenten die laten zien hoe u hoge Beschik baarheid kunt instellen en configureren voor een werk belasting van SAP NetWeaver Linux en Windows-besturings systemen.


Zie voor informatie over de integratie tussen Azure Active Directory (Azure AD) en SAP-services en eenmalige aanmelding:

- [Zelfstudie: Integratie met SAP-Cloud voor klanten Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelfstudie: Integratie met SAP-Cloud platform identiteits verificatie Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelfstudie: Integratie met SAP-Cloud platform Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelfstudie: Integratie met SAP NetWeaver Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelfstudie: Azure Active Directory integratie met SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelfstudie: Integratie met SAP HANA Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Uw S/4HANA-omgeving: Fiori launchpad SAML eenmalige aanmelding met Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Zie voor informatie over de integratie van Azure-Services in SAP-onderdelen:

- [SAP HANA gebruiken in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery en SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [De SAP BW-connector gebruiken in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory biedt integratie van SAP HANA- en Business Warehouse-gegevens](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Wijzigingenlogboek
- 08/22/2019: Wijzigingen in het [instellen van pacemaker op SuSE Linux Enterprise Server in azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) voor het bijwerken van de url's voor het maken van aangepaste rollen  
- 08/16/2019: Wijzigingen in het [instellen van pacemaker op Red Hat Enterprise Linux in azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) om klanten eraan te herinneren de acties in de aangepaste rol bij te werken, als een update naar de nieuwe versie van de Azure Fence-agent wordt uitgevoerd  
- 08/15/2019: Wijzigingen in [SAP Hana opslag configuraties van virtuele Azure-machines](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) om de algemene Beschik baarheid van ultra disk (voorheen Ultra-SSD) weer te geven
- 08/01/2019: Wijzigingen in het [instellen van pacemaker op SuSE Linux Enterprise Server in azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) om wijzigingen te integreren die specifiek zijn voor SLES 15 
- 07/23/2019: Wijzigingen in [cluster een SAP ASCS/SCS-exemplaar op een Windows-failovercluster met behulp van een bestands share in azure](sap-high-availability-guide-wsfc-file-share.md) om ondersteuning te geven voor opslag ruimte Direct door Azure site Recovery Services
- 07/14/2019: Release van [Azure proximity-plaatsings groepen voor optimale netwerk latentie met SAP-toepassingen](sap-proximity-placement-scenarios.md)
- 07/11/2019: Wijzigingen in diverse documenten met betrekking tot HANA grote instanties om revisie 4 van HANA grote instanties te behandelen
- 07/09/2019: Release van de nieuwe hand leiding voor [IBM DB2 HADR in Red Hat Enter prise server](high-availability-guide-rhel-ibm-db2-luw.md)
- 06/13/2019: Release van [hoge Beschik baarheid voor SAP NetWeaver op Red Hat Enterprise Linux met Azure NetApp files voor SAP-toepassingen](high-availability-guide-rhel-netapp-files.md)


