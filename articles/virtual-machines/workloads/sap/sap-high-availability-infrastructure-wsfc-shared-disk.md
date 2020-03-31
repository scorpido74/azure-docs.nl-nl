---
title: Azure-infrastructuur voor SAP ASCS/SCS met WSFC&gedeelde schijf | Microsoft Documenten
description: Meer informatie over het voorbereiden van de Azure-infrastructuur voor SAP HA met behulp van een Windows-failovercluster en gedeelde schijf voor een SAP ASCS/SCS-exemplaar.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8a49bc979923bf52d099e30615910c5bdb0601b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279856"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>De Azure-infrastructuur voor SAP HA voorbereiden met behulp van een Windows-failovercluster en gedeelde schijf voor SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID configuratie met hoge beschikbaarheid)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows][Logo_Windows] Windows
>

In dit artikel worden de stappen beschreven die u neemt om de Azure-infrastructuur voor te bereiden voor het installeren en configureren van een SAP-systeem met hoge beschikbaarheid op een Windows-failovercluster met behulp van een *gedeelde clusterschijf* als optie voor het clusteren van een SAP ASCS-exemplaar.

## <a name="prerequisites"></a>Vereisten

Voordat u met de installatie begint, bekijkt u dit artikel:

* [Architectuurhandleiding: een SAP ASCS/SCS-exemplaar clusteren op een Windows-failovercluster met behulp van een gedeelde clusterschijf][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>De infrastructuur voorbereiden op architectuursjabloon 1
Azure Resource Manager-sjablonen voor SAP helpen de implementatie van vereiste resources te vereenvoudigen.

De drielaagse sjablonen in Azure Resource Manager ondersteunen ook scenario's met hoge beschikbaarheid. Architectural Template 1 heeft bijvoorbeeld twee clusters. Elk cluster is een SAP single point of failure voor SAP ASCS/SCS en DBMS.

Hier u Azure Resource Manager-sjablonen krijgen voor het voorbeeldscenario dat we in dit artikel beschrijven:

* [Azure Marketplace-afbeelding](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Azure Marketplace-afbeelding met Azure Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Aangepaste afbeelding](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Aangepaste afbeelding met beheerde schijven](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

De infrastructuur voorbereiden op architecturale sjabloon 1:

- Selecteer **HA**in de Azure-portal in het deelvenster **Parameters** in het vak **SYSTEEMBESCHIKBAARHEID.**

  ![Figuur 1: Azure Resource Manager-parameters met hoge beschikbaarheid instellen][sap-ha-guide-figure-3000]

_**Figuur 1:** Azure Resource Manager-parameters met hoge beschikbaarheid instellen_


  De sjablonen maken:

  * **Virtuele machines**:
    * SAP Application Server \<virtuele machines: SAPSystemSID\>-di-\<Nummer\>
    * VIRTUELE MACHINES van ASCS/SCS-cluster: \<SAPSystemSID\>-ascs-\<Nummer\>
    * DBMS cluster: \<SAPSystemSID\>\<-db- Aantal\>

  * **Netwerkkaarten voor alle virtuele machines, met bijbehorende IP-adressen:**
    * \<SAPSystemSID\>-nic-di-\<Aantal\>
    * \<SAPSystemSID\>-nic-ascs-\<Nummer\>
    * \<SAPSystemSID\>-nic-db-\<Aantal\>

  * **Azure-opslagaccounts (alleen niet-beheerde schijven):**

  * **Beschikbaarheidsgroepen** voor:
    * SAP Application Server \<virtuele machines: SAPSystemSID\>-avset-di
    * VIRTUELE VIRTUELE MACHINES VAN SAP \<ASCS/SCS-cluster: SAPSystemSID\>-avset-ascs
    * DBMS cluster virtuele \<machines:\>SAPSystemSID -avset-db

  * **Azure interne load balancer:**
    * Met alle poorten voor het ASCS/SCS-exemplaar en IP-adres \<SAPSystemSID\>-lb-ascs
    * Met alle poorten voor het SQL \<Server DBMS en IP-adres SAPSystemSID\>-lb-db

  * **Netwerkbeveiligingsgroep** \<: SAPSystemSID\>-nsg-ascs-0  
    * Met een open externe RDP-poort (Remote Desktop Protocol) naar de \<SAPSystemSID-ascs-0\>virtuele machine

> [!NOTE]
> Alle IP-adressen van de netwerkkaarten en Azure internal load balancers zijn standaard dynamisch. Wijzig ze in statische IP-adressen. We beschrijven hoe dit later in het artikel te doen.
>
>

## <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Implementeer virtuele machines met bedrijfsnetwerkconnectiviteit (cross-premises) om te gebruiken in de productie
Implementeer azure virtuele machines met zakelijke netwerkconnectiviteit voor de productie van SAP-systemen met Azure VPN Gateway of Azure ExpressRoute.

> [!NOTE]
> U uw Azure Virtual Network-exemplaar gebruiken. Het virtuele netwerk en subnet zijn al gemaakt en voorbereid.
>
>

1. Selecteer in de Azure-portal in het deelvenster **Parameters** in het vak **NEWOREXISTINGSUBNET** **bestaande**.
2. Voeg in het vak **SUBNETID** de volledige tekenreeks van uw voorbereide Azure-netwerksubnet-id toe waar u van plan bent uw virtuele Azure-machines te implementeren.
3. Voer de opdracht PowerShell uit om een lijst met alle Azure-netwerksubnetten te krijgen:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   In het veld **ID** wordt de waarde voor de subnet-id weergegeven.
4. Voer de opdracht PowerShell uit om een lijst met alle subnet-ID-waarden te krijgen:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   De subnet-id ziet er als volgt uit:

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Cloud-only SAP-exemplaren implementeren voor test en demo
U uw SAP-systeem met hoge beschikbaarheid implementeren in een implementatiemodel voor alleen de cloud. Dit soort implementatie is vooral handig voor demo- en testusecases. Het is niet geschikt voor productie use cases.

- Selecteer in de Azure-portal in het deelvenster **Parameters** in het vak **NEWOREXISTINGSUBNET** de optie **nieuw**. Laat het **SUBNETID-veld** leeg.

  Met de sjabloon SAP Azure Resource Manager wordt automatisch het virtuele Azure-netwerk en subnet gemaakt.

> [!NOTE]
> U moet ook ten minste één speciale virtuele machine implementeren voor Active Directory- en DNS-service in hetzelfde Azure Virtual Network-exemplaar. De sjabloon maakt deze virtuele machines niet.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>De infrastructuur voorbereiden op architecturale sjabloon 2

U deze Azure Resource Manager-sjabloon voor SAP gebruiken om de implementatie van vereiste infrastructuurbronnen voor SAP Architectural Template 2 te vereenvoudigen.

Hier u Azure Resource Manager-sjablonen voor dit implementatiescenario krijgen:

* [Azure Marketplace-afbeelding](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Azure Marketplace-afbeelding met beheerde schijven](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Aangepaste afbeelding](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Aangepaste afbeelding met beheerde schijven](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>De infrastructuur voorbereiden op architectuursjabloon 3

U de infrastructuur voorbereiden en SAP configureren voor multi-SID. U bijvoorbeeld een extra SAP ASCS/SCS-exemplaar toevoegen aan een *bestaande* clusterconfiguratie. Zie [Een extra SAP ASCS/SCS-exemplaar configureren voor een bestaande clusterconfiguratie om een SAP multi-SID-configuratie te maken in Azure Resource Manager voor][sap-ha-multi-sid-guide]meer informatie.

Als u een nieuw multi-SID-cluster wilt maken, u de multi-SID [quickstart-sjablonen op GitHub](https://github.com/Azure/azure-quickstart-templates)gebruiken.

Als u een nieuw multi-SID-cluster wilt maken, moet u de volgende drie sjablonen implementeren:

* [ASCS/SCS-sjabloon](#ASCS-SCS-template)
* [Databasesjabloon](#database-template)
* [Sjabloon toepassingsservers](#application-servers-template)

In de volgende secties vindt u meer informatie over de sjablonen en parameters die u in de sjablonen moet opgeven.

### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a>ASCS/SCS-sjabloon

De ASCS/SCS-sjabloon implementeert twee virtuele machines die u gebruiken om een failovercluster van Windows Server te maken dat meerdere ASCS/SCS-exemplaren host.

Als u de sjabloon ASCS/SCS multi-SID wilt instellen, voert u in de [ASCS/SCS multi-SID-sjabloon][sap-templates-3-tier-multisid-xscs-marketplace-image] of [ASCS/SCS multi-SID-sjabloon met managed disks][sap-templates-3-tier-multisid-xscs-marketplace-image-md]waarden in voor de volgende parameters:

- **Resourcevoorvoegsel:** stel het bronvoorvoegsel in, dat wordt gebruikt om alle resources die tijdens de implementatie zijn gemaakt, vooraf aan te maken. Omdat de resources niet tot slechts één SAP-systeem behoren, is het voorvoegsel van de bron niet de sid van één SAP-systeem.  Het voorvoegsel moet tussen de drie en zes tekens bevatten.
- **Stacktype:** selecteer het stacktype van het SAP-systeem. Afhankelijk van het stacktype heeft Azure Load Balancer één (alleen ABAP- of Java- of Java-) of twee (ABAP+Java) privé-IP-adressen per SAP-systeem.
- **OS-type:** Selecteer het besturingssysteem van de virtuele machines.
- **SAP System Count**: Selecteer het aantal SAP-systemen dat u in dit cluster wilt installeren.
- **Beschikbaarheid van het systeem**: Selecteer **HA**.
- **Gebruikersnaam en beheerderswachtwoord:** maak een nieuwe gebruiker die kan worden gebruikt om zich aan te melden bij de machine.
- **Nieuw of bestaand subnet:** Stel in of u een nieuw virtueel netwerk en subnet wilt maken of een bestaand subnet wilt gebruiken. Als u al een virtueel netwerk hebt dat is verbonden met uw on-premises netwerk, selecteert u **bestaande**.
- **Subnet-id:** Als u de VM wilt implementeren in een bestaand VNet waarbij een subnet is gedefinieerd waaraan de VM moet worden toegewezen, geeft u de id van dat specifieke subnet een naam. De ID ziet er meestal als volgt uit:

  /subscriptions/\<subscription\>id\</resourceGroups/\>resource group name /providers/Microsoft.Network/virtualNetworks/\<virtual network name\>/subnets/\<subnet name /\>

De sjabloon implementeert één Azure Load Balancer-instantie, die meerdere SAP-systemen ondersteunt:

- De ASCS-exemplaren zijn geconfigureerd voor bijvoorbeeld nummer 00, 10, 20...
- De SCS-exemplaren zijn geconfigureerd voor bijvoorbeeld nummer 01, 11, 21...
- De exemplaren VAN DE ASCS Enqueue Replication Server (ERS) (alleen Linux) zijn geconfigureerd voor bijvoorbeeld nummer 02, 12, 22...
- De SCS ERS (alleen Linux) exemplaren zijn geconfigureerd voor bijvoorbeeld nummer 03, 13, 23...

De load balancer bevat 1 VIP(s) (2 voor Linux), 1x VIP voor ASCS/SCS en 1x VIP voor ERS (alleen Linux).

#### <a name="sap-ascsscs-ports"></a><a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a>SAP ASCS/SCS-poorten
De volgende lijst bevat alle regels voor het balanceren van de lasten (waarbij x het nummer van het SAP-systeem is, bijvoorbeeld 1, 2, 3...):
- Windows-specifieke poorten voor elk SAP-systeem: 445, 5985
- ASCS-poorten (instantienummer x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS-poorten (instantienummer x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- ASCS ERS poorten op Linux (instantie nummer x2): 33x2, 5x213, 5x214, 5x216
- SCS ERS poorten op Linux (instantie nummer x3): 33x3, 5x313, 5x314, 5x316

De load balancer is geconfigureerd om de volgende sondepoortenpoorten te gebruiken (waarbij x het nummer is van het SAP-systeem, bijvoorbeeld 1, 2, 3...):
- ASCS/SCS interne load balancer sonde poort: 620x0
- ERS interne load balancer probe port (alleen Linux): 621x2

### <a name="database-template"></a><a name="database-template"></a>Databasesjabloon

De databasesjabloon implementeert een of twee virtuele machines die u gebruiken om het relationele databasebeheersysteem (RDBMS) voor één SAP-systeem te installeren. Als u bijvoorbeeld een ASCS/SCS-sjabloon implementeert voor vijf SAP-systemen, moet u deze sjabloon vijf keer implementeren.

Voer waarden in voor het instellen van de multi-SID-sjabloon voor de database in de [database multi-SID-sjabloon][sap-templates-3-tier-multisid-db-marketplace-image] of [database multi-SID-sjabloon met beheerde schijven:][sap-templates-3-tier-multisid-db-marketplace-image-md]

- **Sap-systeemid:** voer de SAP-systeem-id in van het SAP-systeem dat u wilt installeren. De id wordt gebruikt als voorvoegsel voor de resources die worden geïmplementeerd.
- **Type besturingssysteem**: Selecteer het besturingssysteem van de virtuele machines.
- **Dbtype:** selecteer het type database dat u op het cluster wilt installeren. Selecteer **SQL** als u Microsoft SQL Server wilt installeren. Selecteer **HANA** als u SAP HANA op de virtuele machines wilt installeren. Zorg ervoor dat u het juiste type besturingssysteem selecteert. Selecteer **Windows** voor SQL en selecteer een Linux-distributie voor HANA. Azure Load Balancer dat is verbonden met de virtuele machines is geconfigureerd om het geselecteerde databasetype te ondersteunen:
  * **SQL**: De load balancer load-balance poort 1433. Zorg ervoor dat u deze poort gebruikt voor uw SQL Server AlwaysOn-installatie.
  * **HANA**: De load balancer load-balance ports 35015 en 35017. Zorg ervoor dat sap HANA met instantienummer **50**wordt geïnstalleerd.
  De load balancer maakt gebruik van sondepoort 62550.
- **Sap-systeemgrootte**: Stel het aantal SAPS in dat het nieuwe systeem biedt. Als u niet zeker weet hoeveel SAPS het systeem nodig heeft, vraag het dan aan uw SAP Technology Partner of System Integrator.
- **Beschikbaarheid van het systeem**: Selecteer **HA**.
- **Gebruikersnaam en beheerderswachtwoord:** maak een nieuwe gebruiker die kan worden gebruikt om zich aan te melden bij de machine.
- **Subnet-id:** voer de id in van het subnet dat u hebt gebruikt tijdens de implementatie van de ASCS/SCS-sjabloon of de id van het subnet dat is gemaakt als onderdeel van de ascs/SCS-sjabloonimplementatie.

### <a name="application-servers-template"></a><a name="application-servers-template"></a>Sjabloon toepassingsservers

De sjabloon toepassingsservers implementeert twee of meer virtuele machines die kunnen worden gebruikt als SAP Application Server-exemplaren voor één SAP-systeem. Als u bijvoorbeeld een ASCS/SCS-sjabloon implementeert voor vijf SAP-systemen, moet u deze sjabloon vijf keer implementeren.

Als u de multi-SID-sjabloon voor toepassingsservers wilt instellen, voert u in de [multi-SID-sjabloon voor toepassingsservers][sap-templates-3-tier-multisid-apps-marketplace-image] of [multi-SID-sjabloon voor toepassingsservers met beheerde schijven][sap-templates-3-tier-multisid-apps-marketplace-image-md]waarden in voor de volgende parameters:

  -  **Sap-systeemid:** voer de SAP-systeem-id in van het SAP-systeem dat u wilt installeren. De id wordt gebruikt als voorvoegsel voor de resources die worden geïmplementeerd.
  -  **Type besturingssysteem**: Selecteer het besturingssysteem van de virtuele machines.
  -  **Sap-systeemgrootte**: Het aantal SAPS dat het nieuwe systeem biedt. Als u niet zeker weet hoeveel SAPS het systeem nodig heeft, vraag het dan aan uw SAP Technology Partner of System Integrator.
  -  **Beschikbaarheid van het systeem**: Selecteer **HA**.
  -  **Gebruikersnaam en beheerderswachtwoord:** maak een nieuwe gebruiker die kan worden gebruikt om zich aan te melden bij de machine.
  -  **Subnet-id:** voer de id in van het subnet dat u hebt gebruikt tijdens de implementatie van de ASCS/SCS-sjabloon of de id van het subnet dat is gemaakt als onderdeel van de ascs/SCS-sjabloonimplementatie.


## <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Virtueel Azure-netwerk
In ons voorbeeld is de adresruimte van het exemplaar Azure Virtual Network 10.0.0.0/16. Er is een subnet genaamd Subnet, met een adresbereik van 10.0.0.0/24. Alle virtuele machines en interne load balancers worden ingezet in dit virtuele netwerk.

> [!IMPORTANT]
> Breng geen wijzigingen aan in de netwerkinstellingen in het gastbesturingssysteem. Dit omvat IP-adressen, DNS-servers en subnet. Configureer al uw netwerkinstellingen in Azure. De DHCP-service (Dynamic Host Configuration Protocol) geeft uw instellingen door.
>
>

## <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>DNS-IP-adressen

Voer de volgende stappen uit om de vereiste DNS-IP-adressen in te stellen:

1. Controleer in de Azure-portal in het deelvenster **DNS-servers** of de optie voor **dns-servers** van uw virtuele netwerk is ingesteld op **Aangepaste DNS.**
2. Selecteer uw instellingen op basis van het type netwerk dat u hebt. Zie de volgende bronnen voor meer informatie:
   * Voeg de IP-adressen van de on-premises DNS-servers toe.  
   U on-premises DNS-servers uitbreiden naar de virtuele machines die in Azure worden uitgevoerd. In dat scenario u de IP-adressen toevoegen van de virtuele Azure-machines waarop u de DNS-service uitvoert.
   * Voor VM-implementaties die zijn geïsoleerd in Azure: implementeer een extra virtuele machine in dezelfde instantie van het virtuele netwerk die als DNS-server fungeert. Voeg de IP-adressen toe van de virtuele Azure-machines die u hebt ingesteld om de DNS-service uit te voeren.

   ![Figuur 2: DNS-servers configureren voor Azure Virtual Network][sap-ha-guide-figure-3001]

   _**Figuur 2:** DNS-servers configureren voor Azure Virtual Network_

   > [!NOTE]
   > Als u de IP-adressen van de DNS-servers wijzigt, moet u de virtuele Azure-machines opnieuw starten om de wijziging toe te passen en de nieuwe DNS-servers te verspreiden.
   >
   >

In ons voorbeeld wordt de DNS-service geïnstalleerd en geconfigureerd op deze virtuele Windows-machines:

| Rol virtuele machine | Naam virtuele machinehost | Naam van netwerkkaart | Statisch IP-adres |
| --- | --- | --- | --- |
| Eerste DNS-server |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Tweede DNS-server |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Hostnamen en statische IP-adressen voor de geclusterde SAP ASCS/SCS-instantie en de geclusterde DBMS-instantie

Voor on-premises implementatie hebt u deze gereserveerde hostnamen en IP-adressen nodig:

| Rol virtuele hostnaam | Virtuele hostnaam | Virtueel statisch IP-adres |
| --- | --- | --- |
| SAP ASCS/SCS eerste cluster virtuele hostnaam (voor clusterbeheer) |pr1-ascs-vir |10.0.0.42 |
| Virtuele hostnaam SAP ASCS/SCS-instantie |pr1-ascs-sap |10.0.0.43 |
| SAP DBMS tweede cluster virtuele hostnaam (clusterbeheer) |pr1-dbms-vir |10.0.0.32 |

Wanneer u het cluster maakt, maakt u de virtuele hostnamen pr1-ascs-vir en pr1-dbms-vir en de bijbehorende IP-adressen die het cluster zelf beheren. Zie [Clusterknooppunten verzamelen in een clusterconfiguratie][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]voor informatie over hoe u dit doen.

U handmatig de andere twee virtuele hostnamen, pr1-ascs-sap en pr1-dbms-sap en de bijbehorende IP-adressen, op de DNS-server maken. De geclusterde SAP ASCS/SCS-instantie en de geclusterde DBMS-instantie gebruiken deze bronnen. Zie [Een virtuele hostnaam maken voor een geclusterd SAP ASCS/SCS-exemplaar voor][sap-ha-guide-9.1.1]informatie over hoe u dit doen.

## <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Statische IP-adressen instellen voor de virtuele SAP-machines
Nadat u de virtuele machines hebt geïmplementeerd om in uw cluster te gebruiken, moet u statische IP-adressen instellen voor alle virtuele machines. Doe dit in de Azure Virtual Network-configuratie en niet in het gastbesturingssysteem.

1. Selecteer in de Azure-portal > **IP-adres** **brongroepnetwerkkaartinstellingen** > **Network Card** > **Settings**.
2. Selecteer Statisch in het deelvenster **IP-adressen** onder **Toewijzing** **.** Voer in het vak **IP-adres** het IP-adres in dat u wilt gebruiken.

   > [!NOTE]
   > Als u het IP-adres van de netwerkkaart wijzigt, moet u de virtuele Azure-machines opnieuw starten om de wijziging toe te passen.  
   >
   >

   ![Figuur 3: Statische IP-adressen instellen voor de netwerkkaart van elke virtuele machine][sap-ha-guide-figure-3002]

   _**Figuur 3:** Statische IP-adressen instellen voor de netwerkkaart van elke virtuele machine_

   Herhaal deze stap voor alle netwerkinterfaces, dat wil zeggen voor alle virtuele machines, inclusief virtuele machines die u wilt gebruiken voor uw Active Directory- of DNS-service.

In ons voorbeeld hebben we deze virtuele machines en statische IP-adressen:

| Rol virtuele machine | Naam virtuele machinehost | Naam van netwerkkaart | Statisch IP-adres |
| --- | --- | --- | --- |
| Eerste exemplaar VAN de SAP-toepassingsserver |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Tweede EXEMPLAAR SAP Application Server |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Laatste exemplaar van de SAP-toepassingsserver |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Eerste clusterknooppunt voor ASCS/SCS-exemplaar |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Tweede clusterknooppunt voor ASCS/SCS-exemplaar |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Eerste clusterknooppunt voor DBMS-exemplaar |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Tweede clusterknooppunt voor DBMS-exemplaar |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Een statisch IP-adres instellen voor de interne loadbalancer van Azure

Met de sjabloon SAP Azure Resource Manager wordt een azure internal load balancer gemaakt die wordt gebruikt voor het SAP ASCS/SCS-instantiecluster en het DBMS-cluster.

> [!IMPORTANT]
> Het IP-adres van de virtuele hostnaam van de SAP ASCS/SCS is hetzelfde als het IP-adres van de interne load balancer van SAP ASCS/SCS: pr1-lb-ascs.
> Het IP-adres van de virtuele naam van de DBMS is hetzelfde als het IP-adres van de interne load balancer van DBMS: pr1-lb-dbms.
>
>

Ga als lid van het nieuwe IP-adres van de interne lastenbalans van Azure:

1. Bij de eerste implementatie wordt het IP-adres van de interne load balancer ingesteld op **Dynamic**. Selecteer **Statisch**in de Azure-portal in het deelvenster **IP-adressen** onder **Toewijzing**.
2. Stel het IP-adres van de interne load balancer **pr1-lb-ascs** in op het IP-adres van de virtuele hostnaam van het exemplaar SAP ASCS/SCS.
3. Stel het IP-adres van de interne load balancer **pr1-lb-dbms** in op het IP-adres van de virtuele hostnaam van de DBMS-instantie.

   ![Figuur 4: Statische IP-adressen instellen voor de interne load balancer voor de instantie SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Figuur 4:** Statische IP-adressen instellen voor de interne load balancer voor de instantie SAP ASCS/SCS_

In ons voorbeeld hebben we twee azure interne load balancers die deze statische IP-adressen hebben:

| Azure interne load balancer-rol | Naam van Azure-interne lastenbalans | Statisch IP-adres |
| --- | --- | --- |
| Interne load balancer van SAP ASCS/SCS-instantie |pr1-lb-ascs |10.0.0.43 |
| SAP DBMS interne load balancer |pr1-lb-dbms |10.0.0.33 |


## <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Standaardregels voor ASCS/SCS-taakverdeling voor de interne loadbalancer van Azure

Met de sjabloon SAP Azure Resource Manager worden de poorten gemaakt die u nodig hebt:
* Een ABAP ASCS-instantie, met het standaardinstantienummer 00
* Een Java SCS-exemplaar, met het standaardinstantienummer 01

Wanneer u uw SAP ASCS/SCS-exemplaar installeert, moet u het standaardinstantienummer 00 gebruiken voor uw ABAP ASCS-exemplaar en het standaardexemplaarnummer 01 voor uw Java SCS-exemplaar.

Maak vervolgens de vereiste interne taakverdelingseindpunten voor de SAP NetWeaver-poorten.

Als u vereiste interne taakverdelingseindpunten wilt maken, maakt u eerst deze eindpunten voor het balanceren van de lasten voor de SAP NetWeaver ABAP ASCS-poorten:

| Regelnaam service/taakverdeling | Standaardpoortnummers | Betonpoorten voor (ASCS-instantie met instantienummer 00) (ERS met 10) |
| --- | --- | --- |
| Enqueue server / *lbrule3200* |32\<InstanceNumber\> |3200 |
| ABAP-berichtenserver / *lbrule3600* |36\<InstanceNumber\> |3600 |
| Intern ABAP-bericht / *lbrule3900* |39\<InstanceNumber\> |3900 |
| Berichtenserver HTTP / *Lbrule8100* |81\<Aantal instances\> |8100 |
| SAP start service ASCS HTTP / *Lbrule50013* |5\<InstanceNummer\>13 |50013 |
| SAP start service ASCS HTTPS / *Lbrule50014* |5\<InstanceNummer\>14 |50014 |
| Replicatie indewachtrij / *Lbrule50016* |5\<InstanceNummer\>16 |50016 |
| SAP start service ERS HTTP *Lbrule51013* |5\<InstanceNummer\>13 |51013 |
| SAP start service ERS HTTP *Lbrule51014* |5\<InstanceNummer\>14 |51014 |
| Windows Remote Management (WinRM) *Lbrule5985* | |5985 |
| Bestandsshare *Lbrule445* | |445 |

**Tabel 1:** Poortnummers van de SAP NetWeaver ABAP ASCS-exemplaren

Maak vervolgens deze eindpunten voor het balanceren van de lastenvoor de SAP NetWeaver Java SCS-poorten:

| Regelnaam service/taakverdeling | Standaardpoortnummers | Betonpoorten voor (SCS-exemplaar met instantienummer 01) (ERS met 11) |
| --- | --- | --- |
| Enqueue server / *lbrule3201* |32\<InstanceNumber\> |3201 |
| Gatewayserver / *lbrule3301* |33\<Aantal instances\> |3301 |
| Java-berichtenserver / *lbrule3900* |39\<InstanceNumber\> |3901 |
| Berichtenserver HTTP / *Lbrule8101* |81\<Aantal instances\> |8101 |
| SAP start service SCS HTTP / *Lbrule50113* |5\<InstanceNummer\>13 |50113 |
| SAP start service SCS HTTPS / *Lbrule50114* |5\<InstanceNummer\>14 |50114 |
| Replicatie indewachtrij / *Lbrule50116* |5\<InstanceNummer\>16 |50116 |
| SAP start service ERS HTTP *Lbrule51113* |5\<InstanceNummer\>13 |51113 |
| SAP start service ERS HTTP *Lbrule51114* |5\<InstanceNummer\>14 |51114 |
| WinRM *Lbrule5985* | |5985 |
| Bestandsshare *Lbrule445* | |445 |

**Tabel 2:** Poortnummers van de SAP NetWeaver Java SCS-exemplaren

![Figuur 5: Standaardregels voor ascs/SCS-taakverdeling voor de interne loadbalancer van Azure][sap-ha-guide-figure-3004]

_**Figuur 5:** Standaardregels voor ASCS/SCS-taakverdeling voor de interne loadbalancer van Azure_

Stel het IP-adres van de load balancer pr1-lb-dbms in op het IP-adres van de virtuele hostnaam van het DBMS-exemplaar.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>De standaardregels voor taakverdeling van ASCS/SCS wijzigen voor de interne loadbalancer van Azure

Als u verschillende nummers wilt gebruiken voor de SAP ASCS- of SCS-exemplaren, moet u de namen en waarden van hun poorten wijzigen van standaardwaarden.

1. Selecteer sid ** \<\>-lb-ascs load balancer** > **load balancing rules**in de Azure-portal.
2. Wijzig de volgende waarden voor alle regels voor het balanceren van de taak die deel uitmaken van de SAP ASCS- of SCS-instantie:

   * Name
   * Poort
   * Back-endpoort

   Als u bijvoorbeeld het standaard ASCS-instantienummer wilt wijzigen van 00 naar 31, moet u de wijzigingen aanbrengen voor alle poorten die in tabel 1 worden vermeld.

   Hier is een voorbeeld van een update voor *poortlbrule3200*.

   ![Figuur 6: De standaardregels voor taakverdeling van ASCS/SCS wijzigen voor de interne loadbalancer van Azure][sap-ha-guide-figure-3005]

   _**Figuur 6:** De standaardregels voor taakverdeling van ASCS/SCS wijzigen voor de interne loadbalancer van Azure_

## <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Virtuele Windows-machines toevoegen aan het domein

Nadat u een statisch IP-adres aan de virtuele machines hebt toegewezen, voegt u de virtuele machines toe aan het domein.

![Figuur 7: Een virtuele machine toevoegen aan een domein][sap-ha-guide-figure-3006]

_**Figuur 7:** Een virtuele machine toevoegen aan een domein_

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Registervermeldingen toevoegen op beide clusterknooppunten van de instantie SAP ASCS/SCS

Azure Load Balancer heeft een interne load balancer die verbindingen sluit wanneer de verbindingen niet actief zijn voor een bepaalde periode (een niet-actieve time-out). SAP-werkprocessen in dialoogvensters openen verbindingen met het SAP-wachtrijproces zodra de eerste aanvraag voor wachtrij/wachtrij moet worden verzonden. Deze verbindingen blijven meestal tot stand totdat het werkproces of het wachtrijproces opnieuw wordt opgestart. Als de verbinding echter gedurende een bepaalde periode niet actief is, worden de verbindingen met de interne lastenbalancer van Azure gesloten. Dit is geen probleem omdat het SAP-werkproces de verbinding met het wachtrijproces opnieuw vaststelt als deze niet meer bestaat. Deze activiteiten worden gedocumenteerd in de ontwikkelaarssporen van SAP-processen, maar ze creëren een grote hoeveelheid extra inhoud in die traces. Het is een goed idee om `KeepAliveTime` de `KeepAliveInterval` TCP/IP en op beide clusterknooppunten te wijzigen. Combineer deze wijzigingen in de TCP/IP-parameters met SAP-profielparameters, die later in het artikel worden beschreven.

Als u registervermeldingen wilt toevoegen op beide clusterknooppunten van de instantie SAP ASCS/SCS, voegt u eerst deze Windows-registervermeldingen toe op beide Windows-clusterknooppunten voor SAP ASCS/SCS:

| Pad | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Naam van de variabele |`KeepAliveTime` |
| Variabel type |REG_DWORD (Decimaal) |
| Waarde |120000 |
| Koppeling naar documentatie |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabel 3:** De eerste PARAMETER TCP/IP wijzigen

Voeg vervolgens deze Windows-registervermelding toe aan beide Windows-clusterknooppunten voor SAP ASCS/SCS:

| Pad | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Naam van de variabele |`KeepAliveInterval` |
| Variabel type |REG_DWORD (Decimaal) |
| Waarde |120000 |
| Koppeling naar documentatie |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**Tabel 4:** De tweede PARAMETER TCP/IP wijzigen

Als u de wijzigingen wilt toepassen, start u beide clusterknooppunten opnieuw.

## <a name="set-up-a-windows-server-failover-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Een failovercluster van Windows Server instellen voor een SAP ASCS/SCS-exemplaar

Het instellen van een Failovercluster van Windows Server voor een SAP ASCS/SCS-exemplaar omvat de volgende taken:

- Verzamel de clusterknooppunten in een clusterconfiguratie.
- Een clusterbestandsshare-getuige configureren.

### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>De clusterknooppunten verzamelen in een clusterconfiguratie

1. Voeg in de wizard Rol en onderdelen toevoegen failoverclustering toe aan beide clusterknooppunten.
2. Stel het failovercluster in met Failoverclusterbeheer. Selecteer in Failoverclusterbeheer **clusterbeheer**en voeg vervolgens alleen de naam van het eerste cluster (knooppunt A) toe. Voeg het tweede knooppunt nog niet toe; u het tweede knooppunt in een latere stap toevoegt.

   ![Figuur 8: De naam van de server of virtuele machine van het eerste clusterknooppunt toevoegen][sap-ha-guide-figure-3007]

   _**Figuur 8:** De naam van de server of virtuele machine van het eerste clusterknooppunt toevoegen_

3. Voer de netwerknaam (virtuele hostnaam) van het cluster in.

   ![Figuur 9: Voer de clusternaam in][sap-ha-guide-figure-3008]

   _**Figuur 9:** Voer de clusternaam in_

4. Nadat u het cluster hebt gemaakt, voert u een clustervalidatietest uit.

   ![Figuur 10: De clustervalidatiecontrole uitvoeren][sap-ha-guide-figure-3009]

   _**Figuur 10:** De clustervalidatiecontrole uitvoeren_

   U waarschuwingen over schijven op dit punt in het proces negeren. U voegt later een getuige voor bestandsshare toe en de gedeelde SIOS-schijven. In dit stadium hoeft u zich geen zorgen te maken over het hebben van een quorum.

   ![Figuur 11: Er wordt geen quorumschijf gevonden][sap-ha-guide-figure-3010]

   _**Figuur 11:** Er wordt geen quorumschijf gevonden_

   ![Figuur 12: Een kernclusterbron heeft een nieuw IP-adres nodig][sap-ha-guide-figure-3011]

   _**Figuur 12:** Een kernclusterbron heeft een nieuw IP-adres nodig_

5. Wijzig het IP-adres van de kernclusterservice. Het cluster kan pas worden gestart als u het IP-adres van de kernclusterservice wijzigt, omdat het IP-adres van de server naar een van de knooppunten voor virtuele machines verwijst. Doe dit op de pagina **Eigenschappen** van de IP-bron van de kernclusterservice.

   We moeten bijvoorbeeld een IP-adres (in ons voorbeeld 10.0.0.42) toewijzen voor de virtuele hostnaam van het cluster pr1-ascs-vir.

   ![Figuur 13: Wijzig in het dialoogvenster Eigenschappen het IP-adres][sap-ha-guide-figure-3012]

   _**Figuur 13:** Wijzig het IP-adres in het dialoogvenster **Eigenschappen**_

   ![Figuur 14: Het IP-adres toewijzen dat is gereserveerd voor het cluster][sap-ha-guide-figure-3013]

   _**Figuur 14:** Het IP-adres toewijzen dat is gereserveerd voor het cluster_

6. Breng de naam van de virtuele host van het cluster online.

   ![Figuur 15: De clusterkernservice is operationeel, met het juiste IP-adres][sap-ha-guide-figure-3014]

   _**Figuur 15:** De clusterkernservice is operationeel, met het juiste IP-adres_

7. Voeg het tweede clusterknooppunt toe.

   Nu de kernclusterservice operationeel is, u het tweede clusterknooppunt toevoegen.

   ![Figuur 16 Het tweede clusterknooppunt toevoegen][sap-ha-guide-figure-3015]

   _**Figuur 16:** Het tweede clusterknooppunt toevoegen_

8. Voer een naam in voor de status van het tweede clusterknooppunt.

   ![Figuur 17: Voer de hostnaam van het tweede clusterknooppunt in][sap-ha-guide-figure-3016]

   _**Figuur 17:** De naam van de hostnaam van het tweede clusterknooppunt invoeren_

   > [!IMPORTANT]
   > Zorg ervoor dat het selectievakje **Alle in aanmerking komende opslag toevoegen aan het cluster** *niet* is ingeschakeld.  
   >
   >

   ![Figuur 18: Schakel het selectievakje niet in][sap-ha-guide-figure-3017]

   _**Figuur 18:** Schakel het selectievakje *niet* in_

   U waarschuwingen over quorum en schijven negeren. U stelt het quorum in en deelt de schijf later, zoals beschreven in [SIOS DataKeeper Cluster Edition installeren voor een SAP ASCS/SCS-clustershareschijf.][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]

   ![Figuur 19: Waarschuwingen over het schijfquorum negeren][sap-ha-guide-figure-3018]

   _**Figuur 19:** Waarschuwingen over het schijfquorum negeren_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Een clusterbestandsshare-getuige configureren

Het configureren van een clusterbestandssharegetuige omvat de volgende taken:

- Maak een bestandsshare.
- Stel het quorum voor het aantal getuigen van het bestand in failoverclusterbeheer in.

#### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Een bestandsshare maken

1. Selecteer een getuige voor bestandsshare in plaats van een quorumschijf. SIOS DataKeeper ondersteunt deze optie.

   In de voorbeelden in dit artikel bevindt de getuige voor bestandsshare zich op de Active Directory- of DNS-server die in Azure wordt uitgevoerd. De getuige van het dossieraandeel wordt domcontr-0 genoemd. Omdat u een VPN-verbinding met Azure zou hebben geconfigureerd (via VPN Gateway of Azure ExpressRoute), is uw Active Directory- of DNS-service on-premises en niet geschikt om een getuige voor bestandsshare uit te voeren.

   > [!NOTE]
   > Als uw Active Directory- of DNS-service alleen on-premises wordt uitgevoerd, configureert u de getuige voor bestandsshare niet op het Active Directory- of DNS Windows-besturingssysteem dat on-premises wordt uitgevoerd. De netwerklatentie tussen clusterknooppunten die worden uitgevoerd in Azure en Active Directory of DNS on-premises zijn mogelijk te groot en veroorzaken verbindingsproblemen. Zorg ervoor dat u de getuige voor bestandsshare configureert op een virtuele Azure-machine die dicht bij het clusterknooppunt wordt uitgevoerd.  
   >
   >

   Het quorumstation heeft minstens 1.024 MB vrije ruimte nodig. We raden 2.048 MB vrije ruimte aan voor het quorumstation.

2. Voeg het object clusternaam toe.

   ![Figuur 20: De machtigingen voor het aandeel toewijzen voor het clusternaamobject][sap-ha-guide-figure-3019]

   _**Figuur 20:** De machtigingen voor de share toewijzen voor het clusternaamobject_

   Zorg ervoor dat de machtigingen de bevoegdheid bevatten om gegevens in het aandeel voor het clusternaamobject te wijzigen (in ons voorbeeld pr1-ascs-vir$).

3. Als u het object clusternaam aan de lijst wilt toevoegen, selecteert u **Toevoegen**. Wijzig het filter om te controleren op computerobjecten, naast de objecten in figuur 22.

   ![Figuur 21: Objecttypen wijzigen om computers op te nemen][sap-ha-guide-figure-3020]

   _**Figuur 21:** **Objecttypen wijzigen** om computers op te nemen_

   ![Figuur 22: Schakel het selectievakje Computers in][sap-ha-guide-figure-3021]

   _**Figuur 22:** Schakel het selectievakje **Computers** in_

4. Voer het clusternaamobject in zoals weergegeven in figuur 21. Omdat de record al is gemaakt, u de machtigingen wijzigen, zoals weergegeven in figuur 20.

5. Selecteer het tabblad **Beveiliging** van het aandeel en stel vervolgens meer gedetailleerde machtigingen in voor het clusternaamobject.

   ![Figuur 23: De beveiligingskenmerken instellen voor het clusternaamobject in het quorum voor bestandsdelen][sap-ha-guide-figure-3022]

   _**Figuur 23:** De beveiligingskenmerken instellen voor het clusternaamobject in het quorum voor bestandsshare_

#### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Het quorum van de bestandsshare-getuige instellen in Failoverclusterbeheer

1. Open de wizard Quoruminstelling configureren.

   ![Figuur 24: De wizard Clusterquoruminstelling configureren starten][sap-ha-guide-figure-3023]

   _**Figuur 24:** De wizard Clusterquoruminstelling configureren starten_

2. Selecteer op de pagina **Quorumconfiguratieoptie selecteren** de optie **Quorum .**

   ![Figuur 25: Quorumconfiguraties waaruit u kiezen][sap-ha-guide-figure-3024]

   _**Figuur 25:** Quorumconfiguraties waaruit u kiezen_

3. Selecteer op de pagina **Quorumgetuige selecteren** de optie **Een getuige voor bestandsshare configureren**.

   ![Figuur 26: Selecteer de getuige van het bestandsdelen][sap-ha-guide-figure-3025]

   _**Figuur 26:** De getuige voor bestandsshare selecteren_

4. Voer het UNC-pad naar de bestandsshare in (in ons voorbeeld \\domcontr-0\FSW). Als u een lijst wilt zien met de wijzigingen die u aanbrengen, selecteert u **Volgende**.

   ![Figuur 27: De locatie van de bestandsshare voor het getuigenaandeel definiëren][sap-ha-guide-figure-3026]

   _**Figuur 27:** De locatie voor bestandsshare voor het getuigenaandeel definiëren_

5. Selecteer de gewenste wijzigingen en selecteer **Volgende**. U moet de clusterconfiguratie opnieuw configureren zoals weergegeven in figuur 28:  

   ![Figuur 28: Bevestiging dat u het cluster opnieuw hebt geconfigureerd][sap-ha-guide-figure-3027]

   _**Figuur 28:** Bevestiging dat u het cluster opnieuw hebt geconfigureerd_

Nadat u het Windows-failovercluster hebt geïnstalleerd, moet u een aantal drempelwaarden wijzigen, zodat ze failoverdetectie aanpassen aan de omstandigheden in Azure. De te wijzigen parameters worden gedocumenteerd in [Drempelwaarden voor failoverclusternetwerk.][tuning-failover-cluster-network-thresholds] Ervan uitgaande dat uw twee VM's die deel uitmaken van de Windows-clusterconfiguratie voor ASCS/SCS in hetzelfde subnet staan, wijzigt u de volgende parameters in deze waarden:

- SamesubnetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

Deze instellingen werden getest met klanten, en bieden een goed compromis. Ze zijn veerkrachtig genoeg, maar ze bieden ook failover dat is snel genoeg in echte foutomstandigheden op een SAP-software of in een knooppunt of VM-storing.

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>SIOS DataKeeper Cluster Edition installeren voor de SCHIJF VOOR het delen van SAP ASCS/SCS-clusterdelen

U hebt nu een werkende Windows Server-failoverclusterconfiguratie in Azure. Als u een SAP ASCS/SCS-exemplaar wilt installeren, hebt u een gedeelde schijfbron nodig. U de gedeelde schijfbronnen die u nodig hebt in Azure niet maken. SIOS DataKeeper Cluster Edition is een oplossing van derden die u gebruiken om bronnen voor gedeelde schijven te maken.

Het installeren van SIOS DataKeeper Cluster Edition voor de SAP ASCS/SCS-clustershareschijf omvat de volgende taken:

- Microsoft .NET Framework 3.5 toevoegen.
- Installeer SIOS DataKeeper.
- SIOS DataKeeper instellen.

### <a name="add-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>.NET Framework 3.5 toevoegen
.NET Framework 3.5 wordt niet automatisch geactiveerd of geïnstalleerd op Windows Server 2012 R2. Omdat SIOS DataKeeper vereist dat .NET zich op alle knooppunten bevindt waar u DataKeeper installeert, moet u .NET Framework 3.5 installeren op het gastbesturingssysteem van alle virtuele machines in het cluster.

Er zijn twee manieren om .NET Framework 3.5 toe te voegen:

- Gebruik de wizard Rollen en onderdelen toevoegen in Windows, zoals weergegeven in figuur 29:

  ![Figuur 29: .NET Framework 3.5 installeren met de wizard Rollen en functies toevoegen][sap-ha-guide-figure-3028]

  _**Figuur 29:** .NET Framework 3.5 installeren met de wizard Rollen en functies toevoegen_

  ![Figuur 30: Voortgangsbalk installeren wanneer u .NET Framework 3.5 installeert met de wizard Rollen en functies toevoegen][sap-ha-guide-figure-3029]

  _**Figuur 30:** Voortgangsbalk voor installatie wanneer u .NET Framework 3.5 installeert met de wizard Rollen en functies toevoegen_

- Gebruik het opdrachtregelgereedschap dism.exe. Voor dit type installatie moet u toegang krijgen tot de SxS-map op de Windows-installatiemedia. Voer bij een opdrachtprompt met verhoogde bevoegdheid de opdracht in:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a>SIOS DataKeeper installeren

Installeer SIOS DataKeeper Cluster Edition op elk knooppunt in het cluster. Als u virtuele gedeelde opslag wilt maken met SIOS DataKeeper, maakt u een gesynchroniseerde spiegel en simuleert u gedeelde clusteropslag.

Voordat u de SIOS-software installeert, maakt u de gegevenskeepersvc-domeingebruiker.

> [!NOTE]
> Voeg de datakeepersvc-domeingebruiker toe aan de groep Lokale beheerder op beide clusterknooppunten.
>
>

SIOS DataKeeper installeren:

1. Installeer de SIOS-software op beide clusterknooppunten.

   ![SIOS-installateur][sap-ha-guide-figure-3030]

   ![Figuur 31: Eerste pagina van de Installatie van SIOS DataKeeper][sap-ha-guide-figure-3031]

   _**Figuur 31:** Eerste pagina van de Installatie van SIOS DataKeeper_

2. Selecteer **Ja**.

   ![Figuur 32: DataKeeper informeert u dat een service wordt uitgeschakeld][sap-ha-guide-figure-3032]

   _**Figuur 32:** DataKeeper informeert u dat een service wordt uitgeschakeld_

3. In het dialoogvenster raden we u aan **domein- of serveraccount**te selecteren.

   ![Figuur 33: Gebruikersselectie voor SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**Figuur 33:** Gebruikersselectie voor SIOS DataKeeper_

4. Voer de gebruikersnaam en het wachtwoord van het domeinaccount in die u hebt gemaakt voor SIOS DataKeeper.

   ![Figuur 34: Voer de domeinnaam en het wachtwoord voor de Installatie van SIOS DataKeeper in][sap-ha-guide-figure-3034]

   _**Figuur 34:** Voer de domeinnaam en het wachtwoord van het domein in voor de installatie Van SIOS DataKeeper_

5. Installeer de licentiesleutel voor uw SIOS DataKeeper-exemplaar, zoals weergegeven in figuur 35.

   ![Figuur 35: Voer uw SIOS DataKeeper-licentiesleutel in][sap-ha-guide-figure-3035]

   _**Figuur 35:** Voer uw SIOS DataKeeper-licentiesleutel in_

6. Start de virtuele machine opnieuw op wanneer u daarom wordt gevraagd.

### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>SIOS DataKeeper instellen

Nadat u SIOS DataKeeper op beide knooppunten hebt geïnstalleerd, start u de configuratie. Het doel van de configuratie is om synchrone gegevensreplicatie te hebben tussen de extra schijven die aan elk van de virtuele machines zijn gekoppeld.

1. Start het gereedschap Beheer en configuratie van DataKeeper en selecteer **Vervolgens Server verbinden**.

   ![Figuur 36: SIOS DataKeeper-beheer- en configuratietool][sap-ha-guide-figure-3036]

   _**Figuur 36:** SIOS DataKeeper-beheer- en configuratietool_

2. Voer de naam of het TCP/IP-adres van het eerste knooppunt in waarmee het gereedschap Beheer en configuratie verbinding moet maken en in een tweede stap het tweede knooppunt.

   ![Figuur 37: De naam of het TCP/IP-adres van het eerste knooppunt invoegen waarmee het gereedschap Beheer en configuratie verbinding moet maken en in een tweede stap het tweede knooppunt][sap-ha-guide-figure-3037]

   _**Figuur 37:** De naam of het TCP/IP-adres van het eerste knooppunt invoegen, het gereedschap Beheer en configuratie moet verbinding maken met en in een tweede stap het tweede knooppunt_

3. Maak de replicatietaak tussen de twee knooppunten.

   ![Figuur 38: Een replicatietaak maken][sap-ha-guide-figure-3038]

   _**Figuur 38:** Een replicatietaak maken_

   Een wizard begeleidt u bij het maken van een replicatietaak.

4. Definieer de naam van de replicatietaak.

   ![Figuur 39: De naam van de replicatietaak definiëren][sap-ha-guide-figure-3039]

   _**Figuur 39:** De naam van de replicatietaak definiëren_

   ![Figuur 40: Definieer de basisgegevens voor het knooppunt, dat het huidige bronknooppunt moet zijn][sap-ha-guide-figure-3040]

   _**Figuur 40:** Definieer de basisgegevens voor het knooppunt, dat het huidige bronknooppunt moet zijn_

5. Definieer de naam, het TCP/IP-adres en het schijfvolume van het doelknooppunt.

   ![Figuur 41: De naam, het TCP/IP-adres en het schijfvolume van het huidige doelknooppunt definiëren][sap-ha-guide-figure-3041]

   _**Figuur 41:** De naam, het TCP/IP-adres en het schijfvolume van het huidige doelknooppunt definiëren_

6. Definieer de compressiealgoritmen. In ons voorbeeld raden we u aan de replicatiestream te comprimeren. Vooral in resynchronisatiesituaties vermindert de compressie van de replicatiestroom de synchronisatietijd aanzienlijk. Compressie maakt gebruik van de CPU- en RAM-bronnen van een virtuele machine. Naarmate de compressiesnelheid toeneemt, neemt ook het volume van cpu-bronnen toe dat wordt gebruikt. U deze instelling later aanpassen.

7. Een andere instelling die u moet controleren, is of de replicatie asynchroon of synchroon plaatsvindt. Wanneer u SAP ASCS/SCS-configuraties beschermt, moet u synchrone replicatie gebruiken.  

   ![Figuur 42: replicatiedetails definiëren][sap-ha-guide-figure-3042]

   _**Figuur 42:** Replicatiedetails definiëren_

8. Definieer of het volume dat door de replicatietaak wordt gerepliceerd, moet worden weergegeven in een failoverclusterconfiguratie van Windows Server als een gedeelde schijf. Selecteer Voor de SAP ASCS/SCS-configuratie **Ja,** zodat het Windows-cluster het gerepliceerde volume ziet als een gedeelde schijf die het als clustervolume kan gebruiken.

   ![Figuur 43: Selecteer Ja om het gerepliceerde volume in te stellen als clustervolume][sap-ha-guide-figure-3043]

   _**Figuur 43:** Ja **Yes** selecteren om het gerepliceerde volume in te stellen als clustervolume_

   Nadat het volume is gemaakt, geeft het hulpprogramma Gegevenskeeperbeheer en -configuratie aan dat de replicatietaak actief is.

   ![Figuur 44: DataKeeper synchrone spiegeling voor de SAP ASCS/SCS-sharedisk is actief][sap-ha-guide-figure-3044]

   _**Figuur 44:** DataKeeper synchrone mirroring voor de SAP ASCS/SCS share disk is actief_

   Failoverclusterbeheer toont de schijf nu als een DataKeeper-schijf, zoals weergegeven in figuur 45:

   ![Figuur 45: Failoverclusterbeheer toont de schijf die DataKeeper heeft gerepliceerd][sap-ha-guide-figure-3045]

   _**Figuur 45:** Failoverclusterbeheer toont de schijf die DataKeeper heeft gerepliceerd_

## <a name="next-steps"></a>Volgende stappen

* [SAP NetWeaver HA installeren met behulp van een Windows-failovercluster en gedeelde schijf voor een SAP ASCS/SCS-exemplaar][sap-high-availability-installation-wsfc-shared-disk]
