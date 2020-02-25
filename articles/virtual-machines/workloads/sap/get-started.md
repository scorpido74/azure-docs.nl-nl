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
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: abbacf57f6e00411c6041422cfc47e68fd46bb15
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566026"
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

- [Snelstartgids: hand matige installatie van SAP HANA met één exemplaar op virtuele machines van Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
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

- [Zelf studie: integratie Azure Active Directory met SAP-Cloud voor klant](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelf studie: integratie met SAP Cloud platform identiteits verificatie Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelf studie: integratie met SAP-Cloud platform Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelf studie: integratie met SAP net-Weaver Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelf studie: integratie Azure Active Directory met SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Zelf studie: integratie Azure Active Directory met SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Uw S/4HANA-omgeving: Fiori launchpad SAML eenmalige aanmelding met Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Zie voor informatie over de integratie van Azure-Services in SAP-onderdelen:

- [SAP HANA gebruiken in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery en SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [De SAP BW-connector gebruiken in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory biedt integratie van SAP HANA- en Business Warehouse-gegevens](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Wijzigingslogboek
- 02/21/2020: wijziging in [Azure-vm's hoge Beschik baarheid voor SAP NetWeaver op RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) en [Azure-vm's hoge Beschik baarheid voor SAP NetWeaver op RHEL met Azure NetApp files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) om de cluster beperkingen aan te passen voor Server replicatie 2 in de wachtrij plaatsen (ENSA2)
- 02/20/2020: wijziging in [hoge Beschik baarheid voor SAP NetWeaver op Azure vm's in de SLES-hand leiding voor multi-sid](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) om een koppeling toe te voegen aan de SuSE multi-sid-cluster gids
- 02/13/2020: wijziging in [Azure-vm's hoge Beschik baarheid voor SAP NetWeaver op RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) en [Azure-vm's hoge Beschik baarheid voor SAP NetWeaver op RHEL met Azure NetApp files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) om de time-outs van de cluster resources af te stemmen op de aanbevelingen voor Red Hat time-outs
- 02/11/2020: release van [SAP Hana voor de migratie van grote exemplaren van Azure naar azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)
- 02/07/2020: wijziging in de [connectiviteit van open bare eind punten voor virtuele machines met behulp van Azure Standard ILB in SAP ha-scenario's](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) voor het bijwerken van de voorbeeld scherm afbeelding
- 02/03/2020: wijziging in [hoge Beschik baarheid voor SAP NW op Azure vm's op SLES voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) en [hoge beschik BAARHEID voor SAP NW op Azure vm's op SLES met ANF for SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) om de waarschuwing over het gebruik van een streepje in de hostnamen van cluster knooppunten op SLES te verwijderen
- 01/28/2020: wijziging in [hoge Beschik baarheid van SAP Hana op virtuele machines van Azure op RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) om de time-outs van de SAP Hana cluster resources af te stemmen op de aanbevelingen voor Red Hat time-out
- 01/17/2020: wijziging in [Azure proximity-plaatsings groepen voor optimale netwerk latentie met SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) om de sectie van het verplaatsen van bestaande vm's in een proximity-plaatsings groep te wijzigen
- 01/17/2020: wijziging in [SAP-werkbelasting configuraties met Azure-beschikbaarheidszones](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) om naar een procedure te verwijzen waarmee de latentie tussen Beschikbaarheidszones wordt geautomatiseerd
- 01/16/2020: wijzigen in [het installeren en configureren van SAP Hana (grote exemplaren) op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) om besturingssysteem releases aan te passen aan Hana IaaS-hardware Directory
- 01/16/2020: wijzigingen in [hoge Beschik baarheid voor SAP NetWeaver op Azure vm's in de SLES-hand leiding voor multi-sid](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) om instructies toe te voegen voor SAP-systemen met behulp van Server 2-architectuur (ENSA2)
- 01/10/2020: wijzigingen in [SAP Hana uitschalen met het stand-by-knoop punt op virtuele machines van Azure met Azure NetApp files op SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) en in [SAP Hana uitschalen met stand-by-knoop punt op virtuele machines van Azure met Azure NetApp files op RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) om instructies toe te voegen voor het permanent maken van `nfs4_disable_idmapping` wijzigingen.
- 01/10/2020: wijzigingen in [hoge Beschik baarheid voor SAP NetWeaver op Azure vm's op SLES met Azure NetApp files voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) en in [Azure virtual machines hoge Beschik baarheid voor SAP NetWeaver op RHEL met Azure NetApp files voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) om instructies toe te voegen voor het koppelen van Azure NetApp files NFSv4-volumes.
- 12/23/2019: release van [hoge Beschik baarheid voor SAP NetWeaver op Azure vm's in de SLES-hand leiding voor multi-sid](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 12/18/2019: release van [SAP Hana uitschalen met stand-by-knoop punt op virtuele machines van Azure met Azure NetApp files op RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 11/21/2019: wijzigingen in [SAP Hana uitschalen met behulp van het knoop punt stand-by op virtuele machines van Azure met Azure NetApp files op SuSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) om de configuratie voor NFS-id-toewijzing te vereenvoudigen en de aanbevolen primaire netwerk interface te wijzigen om de route ring te vereenvoudigen.
- 11/15/2019: kleine wijzigingen in [hoge Beschik baarheid voor SAP NetWeaver op SuSE Linux Enterprise Server met Azure NetApp files voor SAP-toepassingen](high-availability-guide-suse-netapp-files.md) en [hoge Beschik baarheid voor SAP NetWeaver op Red Hat Enterprise Linux met Azure NetApp files voor SAP-toepassingen](high-availability-guide-rhel-netapp-files.md) om te verduidelijken de grootte beperkingen van de capaciteits groep en de instructie Remove die alleen NFSv3-versie wordt ondersteund.
- 11/12/2019: release van [hoge Beschik baarheid voor SAP NetWeaver op Windows met Azure NetApp files (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 11/08/2019: wijzigingen in de [maximale Beschik baarheid van SAP Hana op virtuele machines van Azure op SuSE Linux Enterprise Server](sap-hana-high-availability.md), [SAP Hana systeem replicatie op Azure virtual machine (vm's) instellen](sap-hana-high-availability-rhel.md), [Azure virtual machines hoge Beschik baarheid voor sap netweaver op SuSE Linux Enterprise Server voor sap-toepassingen](high-availability-guide-suse.md), Azure virtual machines hoge Beschik baarheid voor SAP netweaver op SuSE Linux Enterprise Server [met Azure NetApp files](high-availability-guide-suse-netapp-files.md), Azure virtual machines hoge Beschik baarheid voor SAP [NetWeaver op Red Hat Enterprise Linux ](high-availability-guide-rhel.md) [Azure virtual machines hoge beschik BAARHEID voor SAP NetWeaver op Red Hat Enterprise Linux met Azure NetApp files](high-availability-guide-rhel-netapp-files.md), [hoge Beschik baarheid voor NFS op virtuele machines in azure op SuSE Linux Enterprise Server](high-availability-guide-suse-nfs.md), [GlusterFS op Azure-Vm's in Red Hat Enterprise Linux voor SAP netweaver](high-availability-guide-rhel-glusterfs.md) om Azure Standard Load Balancer aan te bevelen  
- 11/08/2019: wijzigingen in de [SAP-werk belasting planning en implementatie controlelijst](sap-deployment-checklist.md) voor het verduidelijken van de aanbeveling voor versleuteling  
- 11/04/2019: wijzigingen in het [instellen van pacemaker op SuSE Linux Enterprise Server in azure](high-availability-guide-suse-pacemaker.md) om het cluster rechtstreeks te maken met unicast-configuratie  
- 10/29/2019: release van de [connectiviteit van open bare eind punten voor virtual machines met behulp van Azure Standard Load Balancer in scenario's met hoge Beschik baarheid van SAP](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 10/25/2019: wijzigingen in [SAP Hana opslag configuraties voor virtuele machines van Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) en [SAP Hana uitschalen met behulp van het knoop punt stand-by op virtuele machines van Azure met Azure NetApp files op SuSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) om het NFS-protocol voor/Hana/Shared-volume te verduidelijken
- 10/22/2019: wijziging in [hoge Beschik baarheid voor SAP NetWeaver op Azure vm's op SuSE Linux Enterprise Server voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [hoge Beschik baarheid voor SAP NetWeaver op azure vm's op SuSE Linux Enterprise Server met Azure NetApp files voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [hoge Beschik baarheid voor NFS op Azure-vm's in Suse Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), het [instellen van pacemaker op SuSE Linux Enterprise Server in azure, een](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) [hoge Beschik baarheid van IBM Db2 LUW op Azure-vm's](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)in en [hoge Beschik baarheid van SAP Hana op virtuele machines van Azure op SuSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) voor de detectie beveiliging van Azure Load Balancer
- De sectie en koptekst van de sectie ANF wijzigen in [SAP Hana opslag configuraties voor virtuele Azure-machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/21/2019: release van [SAP Hana uitschalen met stand-by-knoop punt op virtuele machines van Azure met Azure NetApp files op SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 10/16/2019: verbroken koppelingen herstellen in [back-up en herstellen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)
- 10/16/2019: Wijzig het minimale aanbevolen besturings systeem van SLES 12 SP3 in SLES 12 SP4 in [hoge Beschik baarheid van IBM DB2 LUW op Azure-vm's op SuSE Linux Enterprise Server met pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
- 10/11/2019: wijzigingen in de configuratie van ultra Disk Storage en de introductie van ANF in [SAP Hana opslag configuraties van virtuele Azure-machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/01/2019: wijziging in grafische afbeeldingen van [Azure proximity-plaatsings groepen voor een optimale netwerk latentie met SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) om meer duidelijkheid te krijgen
- 10/01/2019: wijziging in [configuratie van SAP Hana infrastructuur en-bewerkingen op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) om de instructies te corrigeren rond Maxi maal beschik bare NFS-share voor/Hana/Shared. 
- 09/28/2019: wijziging in het [instellen van pacemaker op Red Hat Enterprise Linux in azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) om te verduidelijken dat SBD als een omheinings mechanisme niet wordt ondersteund op RHEL-clusters  
- 09/17/2019: wijziging in de hand leiding voor planning en implementatie van NetWeaver om de voor waarden van de VM-extensie voor SAP samen te voegen  
- 08/22/2019: wijzigingen in het [instellen van pacemaker op SuSE Linux Enterprise Server in azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) voor het bijwerken van de url's voor het maken van aangepaste rollen  
- 08/16/2019: wijzigingen in het [instellen van pacemaker op Red Hat Enterprise Linux in azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) om klanten eraan te herinneren de acties in de aangepaste rol bij te werken, als een update naar de nieuwe versie van de Azure Fence-agent wordt uitgevoerd  
- 08/15/2019: wijzigingen in [SAP Hana opslag configuraties van virtuele Azure-machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) om de algemene Beschik baarheid van ultra disk (voorheen Ultra-SSD) weer te geven
- 08/01/2019: wijzigingen in het [instellen van pacemaker op SuSE Linux Enterprise Server in azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) om wijzigingen te integreren die specifiek zijn voor SLES 15 
- 07/23/2019: wijzigingen in [cluster een SAP ASCS/SCS-exemplaar op een Windows-failovercluster met behulp van een bestands share in azure](sap-high-availability-guide-wsfc-file-share.md) om ondersteuning te geven voor opslag ruimte Direct door Azure site Recovery Services
- 07/14/2019: release van [Azure proximity-plaatsings groepen voor optimale netwerk latentie met SAP-toepassingen](sap-proximity-placement-scenarios.md)
- 07/11/2019: wijzigingen in diverse documenten met betrekking tot HANA grote instanties om revisie 4 van HANA grote instanties te behandelen
- 07/09/2019: release van de nieuwe hand leiding voor [IBM DB2 HADR in Red Hat Enter prise server](high-availability-guide-rhel-ibm-db2-luw.md)
- 06/13/2019: release van [hoge Beschik baarheid voor SAP NetWeaver op Red Hat Enterprise Linux met Azure NetApp files voor SAP-toepassingen](high-availability-guide-rhel-netapp-files.md)


