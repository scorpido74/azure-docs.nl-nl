---
title: Azure VM's HA voor SAP NW op Windows met Azure NetApp Files (SMB)| Microsoft Documenten
description: Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op Windows met Azure NetApp Files (SMB) voor SAP-toepassingen
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: radeltch
ms.openlocfilehash: b41db629c5308348f632b3dc51c75822ba361c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591350"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op Windows met Azure NetApp Files(SMB) voor SAP-toepassingen

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

In dit artikel wordt beschreven hoe u de virtuele machines implementeert, configureert, het clusterframework installeert en een zeer beschikbaar SAP NetWeaver 7.50-systeem installeert op Windows VM's, met behulp van [SMB](https://docs.microsoft.com/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) op [Azure NetApp-bestanden.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)  

De databaselaag wordt in dit artikel niet in detail behandeld. We gaan ervan uit dat het [virtuele Azure-netwerk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) al is gemaakt.  

Lees eerst de volgende SAP Notes en papers:

* [Azure NetApp-bestandendocumentatie][anf-azure-doc] 
* SAP Note [1928533][1928533], die bevat:  
  * Een lijst met Azure VM-formaten die worden ondersteund voor de implementatie van SAP-software
  * Belangrijke capaciteitsinformatie voor Azure VM-formaten
  * Ondersteunde SAP-software en besturingssysteem- en databasecombinaties
  * Vereiste SAP-kernelversie voor Windows op Microsoft Azure
* SAP Note [2015553][2015553] bevat vereisten voor SAP-ondersteunde SAP-softwareimplementaties in Azure.
* SAP Note [2178632][2178632] heeft gedetailleerde informatie over alle monitoringstatistieken die voor SAP in Azure worden gerapporteerd.
* SAP Note [1999351][1999351] heeft aanvullende informatie over probleemoplossing voor de Azure Enhanced Monitoring Extension voor SAP.
* SAP Note [2287140](https://launchpad.support.sap.com/#/notes/2287140) bevat vereisten voor de DOOR SAP ondersteunde CA-functie van het SMB 3.x-protocol.
* SAP Note [2802770](https://launchpad.support.sap.com/#/notes/2802770) heeft informatie over probleemoplossing voor de langzaam lopende SAP-transactie AL11 op Windows 2012 en 2016.
* SAP Note [1911507](https://launchpad.support.sap.com/#/notes/1911507) bevat informatie over de transparante failoverfunctie voor een bestandsshare op Windows Server met het SMB 3.0-protocol.
* SAP Note [662452](https://launchpad.support.sap.com/#/notes/662452) heeft aanbeveling (deactiveren 8.3 naam generatie) om slechte bestandssysteem prestaties / fouten aan te pakken tijdens toegang tot gegevens.
* [SAP NetWeaver-hoge beschikbaarheid installeren op een Windows-failovercluster en bestandsshare voor SAP ASCS/SCS-exemplaren op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-file-share) 
* [Azure Virtual Machines met hoge beschikbaarheid en scenario's voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
* [Sondepoort toevoegen in ASCS-clusterconfiguratie](sap-high-availability-installation-wsfc-file-share.md)
* [Installatie van een (A)SCS-exemplaar op een failovercluster](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Een SMB-volume maken voor Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections)
* [NetApp SAP-toepassingen op Microsoft Azure met Azure NetApp-bestanden][anf-sap-applications-azure]

## <a name="overview"></a>Overzicht

SAP ontwikkelde een nieuwe aanpak en een alternatief voor gedeelde clusterschijven voor het clusteren van een SAP ASCS/SCS-exemplaar op een Windows-failovercluster. In plaats van gedeelde clusterschijven te gebruiken, kan men een SMB-bestandsshare gebruiken om algemene gehostbestanden van SAP te implementeren. Azure NetApp Files ondersteunt SMBv3 (samen met NFS) met NTFS ACL met Active Directory. Azure NetApp Files is automatisch zeer beschikbaar (omdat het een PaaS-service is). Deze functies maken Azure NetApp Files geweldige optie voor het hosten van het mkb-bestandsaandeel voor SAP global.  
Zowel [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) als Active Directory Domain Services [(AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) worden ondersteund. U bestaande Active Directory-domeincontrollers gebruiken met Azure NetApp-bestanden. Domeincontrollers kunnen zich in Azure bevinden als virtuele machines of on-premises via ExpressRoute of S2S VPN. In dit artikel gebruiken we domeincontroller in een Azure VM.  
Voor hoge beschikbaarheid (HA) voor centrale SAP Netweaver-services is gedeelde opslag vereist. Om dat te bereiken op Windows, tot nu toe was het noodzakelijk om ofwel SOFS cluster te bouwen of gebruik cluster gedeelde schijf s / w zoals SIOS. Nu is het mogelijk om SAP Netweaver HA te bereiken met behulp van gedeelde opslag, geïmplementeerd op Azure NetApp Files. Als u Azure NetApp-bestanden gebruikt voor de gedeelde opslag, is het niet meer nodig om SOFS of SIOS te gebruiken.  

> [!NOTE]
> Het clusteren van SAP ASCS/SCS-exemplaren met behulp van een bestandsshare wordt ondersteund voor SAP NetWeaver 7.40 (en hoger), met SAP Kernel 7.49 (en hoger).  

![SAP ASCS/SCS HA Architecture met MKB-aandeel](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

De voorwaarden voor een smb-bestandsaandeel zijn:
* SMB 3.0 (of hoger) protocol.
* Mogelijkheid om Active Directory access control lists (ACL's) in te stellen voor Active Directory-gebruikersgroepen en het computerobject$
* Het bestandsaandeel moet HA-ingeschakeld zijn.

Het aandeel voor de SAP Central-services in deze referentiearchitectuur wordt aangeboden door Azure NetApp Files:

![SAP ASCS/SCS HA Architecture met MKB-aandeel](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>SMB-volume maken en monteren voor Azure NetApp-bestanden

Voer de volgende stappen uit als voorbereiding op het gebruik van Azure NetApp-bestanden.  

1. De stappen uitvoeren om [te registreren voor Azure NetApp-bestanden](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)  
2. Azure NetApp-account maken volgens de stappen die zijn beschreven in [Een NetApp-account maken](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)  
3. Capaciteitspool instellen volgens de instructies in [Een capaciteitspool instellen](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)
4. Azure NetApp Files-bronnen moeten zich in gedelegeerd subnet bevinden. Volg de instructies in [Een subnet delegeren aan Azure NetApp-bestanden](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) om gedelegeerd subnet te maken.  

> [!IMPORTANT]
> U moet Active Directory-verbindingen maken voordat u een SMB-volume maakt. Bekijk de [vereisten voor Active Directory-verbindingen](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections).  

5. Active Directory-verbinding maken, zoals beschreven in [Een Active Directory-verbinding maken](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#create-an-active-directory-connection)  
6. SMB Azure NetApp Files SMB-volume maken volgens de instructies in [Een SMB-volume toevoegen](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#add-an-smb-volume)  
7. Monteer het SMB-volume op uw Windows Virtual Machine.

> [!TIP]
> Als u in [Azure Portal](https://portal.azure.com/#home) naar het azure NetApp-bestanden-object navigeert, klikt u op het blade **Volumes** en geeft **u instructies.**  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>De infrastructuur voorbereiden op SAP HA met behulp van een Windows-failovercluster 

1. [De vereiste DNS-IP-adressen instellen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#b22d7b3b-4343-40ff-a319-097e13f62f9e)  
2. [Stel statische IP-adressen in voor de virtuele SAP-machines.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#84c019fe-8c58-4dac-9e54-173efd4b2c30)
3. [Stel een statisch IP-adres in voor de interne loadbalancer van Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#7a8f3e9b-0624-4051-9e41-b73fff816a9e).
4. [Stel standaard ASCS/SCS-taakverdelingsregels in voor de interne load balancer van Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#f19bd997-154d-4583-a46e-7f5a69d0153c).
5. [Wijzig de standaardregels voor taakverdeling van ASCS/SCS voor de interne load balancer van Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#fe0bd8b5-2b43-45e3-8295-80bee5415716).
6. [Windows-virtuele machines toevoegen aan het domein](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#e69e9a34-4601-47a3-a41c-d2e11c626c0c).
7. [Registervermeldingen toevoegen op beide clusterknooppunten van de instantie SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#661035b2-4d0f-4d31-86f8-dc0a50d78158)
8. [Een failovercluster van Windows Server instellen voor een SAP ASCS/SCS-exemplaar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#0d67f090-7928-43e0-8772-5ccbf8f59aab)
9. Als u Windows Server 2016 gebruikt, raden we u aan [Azure Cloud Witness te](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)configureren.


## <a name="install-sap-ascs-instance-on-both-nodes"></a>SAP ASCS-instantie op beide knooppunten installeren

Je hebt de volgende software van SAP nodig:
   * SAP Software Provisioning Manager (SWPM) installatietool versie SPS25 of hoger.
   * SAP Kernel 7.49 of hoger
   * Maak een virtuele hostnaam (clusternetwerknaam) voor de geclusterde SAP ASCS/SCS-instantie, zoals beschreven in [Een virtuele hostnaam maken voor het geclusterde SAP ASCS/SCS-exemplaar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#a97ad604-9094-44fe-a364-f89cb39bf097).

> [!NOTE]
> Het clusteren van SAP ASCS/SCS-exemplaren met behulp van een bestandsshare wordt ondersteund voor SAP NetWeaver 7.40 (en hoger), met SAP Kernel 7.49 (en hoger).  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>Een ASCS/SCS-exemplaar installeren op het eerste ASCS/SCS-clusterknooppunt

1. Installeer een SAP ASCS/SCS-exemplaar op het eerste clusterknooppunt. Start het SAP SWPM-installatiegereedschap en navigeer vervolgens naar: **Product** > **DBMS** > Installation > Application Server ABAP (of Java) > systeem met hoge beschikbaarheid > ASCS/SCS-exemplaar > Eerste clusterknooppunt.  

2. Selecteer **Cluster voor bestandsshare** als de clustershareconfiguratie in SWPM.  
3. Wanneer u wordt gevraagd bij stap **SAP-systeemclusterparameters,** voert u de hostnaam in voor het Azure NetApp Files SMB-aandeel dat u al hebt gemaakt als **Hostname voor bestandsshare**.  In dit voorbeeld is de naam van de SMB-share host **anfsmb-9562**. 

> [!IMPORTANT]
> Als de vereiste controleresultaten in SWPM de voorwaarde voor de functie voor continue beschikbaarheid niet hebben voldaan, kan deze worden verholpen door de instructies in het foutbericht Vertraagd te volgen [wanneer u probeert toegang te krijgen tot een gedeelde map die niet meer bestaat in Windows.](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l)  

> [!TIP]
> Als uit de vereiste controleresultaten in SWPM blijkt dat niet is voldaan aan de voorwaarde Swapsize, u de SWAP-grootte aanpassen door naar Mijn computer>Systeemeigenschappen te navigeren>prestatie-instellingen> geavanceerd> virtueel geheugen> wijzigen.  

4. Configureer een SAP-clusterbron, de `SAP-SID-IP` sondepoort, met PowerShell. Voer deze configuratie uit op een van de SAP ASCS/SCS-clusterknooppunten, zoals beschreven in [Probe-poort configureren.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#10822f4f-32e7-4871-b63a-9b86c76ce761)

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>Een ASCS/SCS-exemplaar installeren op het tweede ASCS/SCS-clusterknooppunt

1. Installeer een SAP ASCS/SCS-exemplaar op het tweede clusterknooppunt. Start het SAP SWPM-installatiegereedschap en navigeer vervolgens naar **Product** > **DBMS** > Installatie > Application Server ABAP (of Java) > ascs/SCS-instantie > > Extra clusterknooppunt.  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>Een DBMS-instantie en SAP-toepassingsservers installeren

Voltooi uw SAP-installatie door het installeren van:

   * Een DBMS-exemplaar  
   * Een primaire SAP-toepassingsserver  
   * Een extra SAP-toepassingsserver  

## <a name="test-the-sap-ascsscs-instance-failover"></a>De failover van de SAP ASCS/SCS-instantie testen 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>Fail over van clusterknooppunt A naar clusterknooppunt B en terug
In dit testscenario verwijzen we naar clusternode sapascs1 als knooppunt A en naar clusternode sapascs2 als knooppunt B.

1. Controleren of de clusterresources worden uitgevoerd ![op knooppunt A. Figuur 1: Windows Server failoverclusterresources die worden uitgevoerd op knooppunt A vóór de failovertest](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. Clusterknooppunt A opnieuw starten. De SAP-clusterresources worden verplaatst naar ![clusterknooppunt B. Figuur 2: Windows Server failoverclusterresources die na de failovertest op knooppunt B worden uitgevoerd](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>Proefmeting vergrendelen

1.Controleren of de SAP Enqueue Replication Server (ERS) actief is  
2. Meld u aan bij het SAP-systeem, voer transactie SU01 uit en open een gebruikers-id in de wijzigingsmodus. Dat zal sap lock entry genereren.  
3. Terwijl u bent ingelogd in het SAP-systeem, geeft u de vergrendelingsvermelding weer door naar transactie ST12 te navigeren.  
4. Fail over ASCS-resources van clusterknooppunt A naar clusterknooppunt B.  
5. Controleer of de vergrendelingsvermelding die is gegenereerd voordat de failover van de SAP ASCS/SCS-clusterbronnen wordt bewaard.  

![Figuur 3: Vergrendeling blijft behouden na failovertest](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

Zie [Probleemoplossing voor failover voor enqueue in ASCS met ERS voor](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS) meer informatie
## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Leren hoe u hoge beschikbaarheid vaststellen en plannen voor noodherstel van SAP 
* HANA op Azure (grote exemplaren), zie [SAP HANA (grote exemplaren) hoge beschikbaarheid en disaster recovery op Azure](hana-overview-high-availability-disaster-recovery.md).
* Zie [Hoge beschikbaarheid van SAP HANA op Azure Virtual Machines (VM's)][sap-hana-ha] voor meer informatie over het instellen van hoge beschikbaarheid en het plannen van noodherstel van SAP HANA op Azure VM's.
