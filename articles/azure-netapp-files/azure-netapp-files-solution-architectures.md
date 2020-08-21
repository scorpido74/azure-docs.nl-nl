---
title: Oplossings architecturen met behulp van Azure NetApp Files | Microsoft Docs
description: Bevat verwijzingen naar aanbevolen procedures voor oplossings architecturen met behulp van Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: b-juche
ms.openlocfilehash: e7bdf6ce35e2649a068de234b4b52e13efe72c44
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690541"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Oplossingsarchitecturen op basis van Azure NetApp Files
Dit artikel bevat verwijzingen naar aanbevolen procedures waarmee u inzicht krijgt in de oplossings architecturen voor het gebruik van Azure NetApp Files.  

In het volgende diagram ziet u een overzicht van de categorieën oplossings architecturen die Azure NetApp Files biedt:

![Categorieën voor oplossings architectuur](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Linux OSS-apps en-database oplossingen

Deze sectie bevat verwijzingen naar oplossingen voor Linux OSS-toepassingen en-data bases. 

### <a name="oracle"></a>Oracle

* [best practice hand leiding Oracle op Azure-implementatie met behulp van Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf)
* [Oracle-VM-installatie kopieën en hun implementatie op Microsoft Azure: configuratie opties voor gedeelde opslag](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-vm-solutions#shared-storage-configuration-options)
* [Voordelen van het gebruik van Azure NetApp Files met Oracle Database](solutions-benefits-azure-netapp-files-oracle-database.md)

## <a name="windows-apps-and-sql-server-solutions"></a>Windows-apps en oplossingen voor SQL Server

Deze sectie bevat verwijzingen naar Windows-toepassingen en SQL Server oplossingen.

### <a name="file-sharing-and-global-file-caching"></a>Bestanden delen en globale bestands cache

* [Uw eigen Azure NFS bouwen? Wrestling Linux-bestands shares in de Cloud](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares)
* [Implementatie van globale bestands cache/Azure NetApp Files](https://youtu.be/91LKb1qsLIM)

### <a name="sql-server"></a>SQL Server

* [SQL Server implementeren via SMB met Azure NetApp Files](https://www.youtube.com/watch?v=x7udfcYbibs)
* [SQL Server failover cluster met behulp van SMB implementeren met Azure NetApp Files](https://www.youtube.com/watch?v=zuNJ5E07e8Q)
* [Altijd-on-beschikbaarheids groepen implementeren met Azure NetApp Files](https://www.youtube.com/watch?v=y3VQmzzeyvc)

## <a name="sap-on-azure-solutions"></a>SAP on Azure oplossingen

Deze sectie bevat Naslag informatie over SAP on Azure oplossingen. 

### <a name="generic-sap-and-sap-netweaver"></a>Algemene SAP-en SAP net-Weaver 

* [SAP-toepassingen op Microsoft Azure met behulp van Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)
* [Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's op SUSE Linux Enterprise Server met Azure NetApp Files voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
* [Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's op Red Hat Enterprise Linux met Azure NetApp Files voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* [Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's in Windows met Azure NetApp Files (SMB) voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
* [Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's op Red Hat Enterprise Linux voor de multi-SID-hand leiding voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)

### <a name="sap-hana"></a>SAP HANA 

* [Configuraties van SAP HANA in virtuele Azure-machineopslag](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
* [Hoge Beschik baarheid van SAP HANA omhoog schalen met Azure NetApp Files op Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-netapp-files-red-hat)
* [SAP HANA uitschalen met het stand-by-knoop punt op virtuele machines van Azure met Azure NetApp Files op SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
* [SAP HANA uitschalen met het stand-by-knoop punt op virtuele machines van Azure met Azure NetApp Files op Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)

### <a name="sap-tech-community-and-blog-posts"></a>SAP-technische community-en blog berichten 

* [Azure NetApp Files-SAP HANA back-up in enkele seconden](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files: de HANA-data base herstellen vanuit een back-up van een moment opname](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files – SAP HANA offloading van een back-up met Cloud Sync](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Uw SAP HANA-systeem kopieën versnellen met Azure NetApp Files](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Cloud volumes ONTAP en Azure NetApp Files: SAP HANA systeem migratie eenvoudig](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)

## <a name="virtual-desktop-infrastructure-solutions"></a>Infrastructuur oplossingen voor Virtual Desktop

Deze sectie bevat verwijzingen naar oplossingen voor virtuele-bureaublad infrastructuur.

### <a name="windows-virtual-desktop"></a>Windows Virtual Desktop

* [Voordelen van het gebruik van Azure NetApp Files met Windows Virtual Desktop](solutions-windows-virtual-desktop.md)
* [Opslag opties voor FSLogix-profiel containers in virtueel bureau blad van Windows](https://docs.microsoft.com/azure/virtual-desktop/store-fslogix-profile#azure-platform-details)
* [Een FSLogix-profiel container maken voor een hostgroep met Azure NetApp Files](https://docs.microsoft.com/azure/virtual-desktop/create-fslogix-profile-container)
* [Virtueel bureau blad van Windows op schaal van onderneming](https://docs.microsoft.com/azure/architecture/example-scenario/wvd/windows-virtual-desktop)

## <a name="hpc-solutions"></a>HPC-oplossingen

Deze sectie bevat verwijzingen naar High Performance Computing (HPC)-oplossingen. 

### <a name="generic-hpc"></a>Algemeen HPC

* [Azure NetApp Files: optimaal profiteren van uw Cloud opslag](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [Voer MPI-workloads uit met Azure Batch en Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure Cycle Cloud: CycleCloud HPC-omgevingen op Azure NetApp Files](https://docs.microsoft.com/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>Olie en gas

* [High Performance Computing (HPC): olie en gas in azure](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [Reservoir simulatie software uitvoeren op Azure](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>Elektronische ontwerp automatisering (EDA)

* [Voordelen van het gebruik van Azure NetApp Files voor electronic design automation](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud: EDA HPC Lab met Azure NetApp Files](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)

### <a name="analytics"></a>Analyse

* [Azure NetApp Files: een nieuw gedeeld bestands systeem dat kan worden gebruikt met een SAS-raster op Microsoft Azure](https://communities.sas.com/t5/Architecture/Azure-NetApp-Files-A-new-shared-file-system-to-use-with-SAS-Grid/m-p/606978)

## <a name="azure-platform-services-solutions"></a>Oplossingen voor Azure platform Services

Deze sectie bevat oplossingen voor Azure platform Services. 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Azure Kubernetes Services en Kubernetes

* [Azure NetApp Files integreren met de Azure Kubernetes-service](https://docs.microsoft.com/azure/aks/azure-netapp-files)
* [Kubernetes-prestaties op Azure met Azure NetApp Files van buiten deze wereld](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Trident-opslag Orchestrator voor containers](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)

### <a name="azure-batch"></a>Azure Batch

* [Voer MPI-workloads uit met Azure Batch en Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
 
