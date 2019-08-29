---
title: De Azure-infra structuur voorbereiden voor SAP HA met behulp van een Windows-failovercluster en een gedeelde schijf voor SAP ASCS/SCS | Microsoft Docs
description: Meer informatie over het voorbereiden van de Azure-infra structuur voor SAP HA met behulp van een Windows-failovercluster en gedeelde schijf voor een SAP ASCS/SCS-exemplaar.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 63a2a3a68adaa2e389cc2af173c8f75a18fbc36d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078694"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>De Azure-infra structuur voor SAP HA voorbereiden met behulp van een Windows-failovercluster en een gedeelde schijf voor SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Multi-SID-configuratie met hoge Beschik baarheid van SAP)

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows][Logo_Windows] Windows
>

In dit artikel worden de stappen beschreven die u moet uitvoeren om de Azure-infra structuur voor te bereiden voor het installeren en configureren van een SAP-systeem met hoge Beschik baarheid op een Windows-failovercluster met behulp van een *gedeelde cluster schijf* als optie voor het clusteren van een SAP ASCS-exemplaar.

## <a name="prerequisites"></a>Vereisten

Lees dit artikel voordat u met de installatie begint:

* [Architectuur handleiding: Een SAP ASCS/SCS-exemplaar op een Windows-failovercluster clusteren met behulp van een gedeelde cluster schijf][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>De infra structuur voorbereiden voor de architectuur sjabloon 1
Met Azure Resource Manager sjablonen voor SAP kunt u de implementatie van vereiste resources vereenvoudigen.

De sjablonen met drie lagen in Azure Resource Manager bieden ook ondersteuning voor scenario's met hoge Beschik baarheid. Architecturaal sjabloon 1 heeft bijvoorbeeld twee clusters. Elk cluster is een SAP-Single Point of Failure voor SAP ASCS/SCS en DBMS.

Hier kunt u Azure Resource Manager sjablonen ophalen voor het voorbeeld scenario dat in dit artikel wordt beschreven:

* [Azure Marketplace-installatie kopie](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Azure Marketplace-installatie kopie met behulp van Azure Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Aangepaste installatie kopie](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Aangepaste installatie kopie met behulp van Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

De infra structuur voorbereiden voor de architectuur sjabloon 1:

- Selecteer in de Azure Portal in het deel venster **para meters** in het vak **SYSTEMAVAILABILITY** de optie **ha**.

  ![Afbeelding 1: SAP-Azure Resource Manager-para meters met hoge Beschik baarheid instellen][sap-ha-guide-figure-3000]

_**Afbeelding 1:** SAP-Azure Resource Manager-para meters met hoge Beschik baarheid instellen_


  De sjablonen maken:

  * **Virtuele machines**:
    * SAP-toepassings server virtuele machines: \<SAPSystemSID\>-di-\<nummer\>
    * ASCS/SCS cluster virtual machines: \<SAPSystemSID\>-ascs-\<Number\>
    * DBMS-cluster: \<SAPSystemSID\>-DB-\<Number\>

  * **Netwerk kaarten voor alle virtuele machines, met de bijbehorende IP-adressen**:
    * \<SAPSystemSID\>-NIC-di-\<Number\>
    * \<SAPSystemSID\>-NIC-ascs-\<Number\>
    * \<SAPSystemSID\>-NIC-DB-\<Number\>

  * **Azure-opslag accounts (alleen onbeheerde schijven)** :

  * **Beschikbaarheids groepen** voor:
    * SAP-toepassings server virtuele machines: \<SAPSystemSID\>-avset-di
    * SAP ASCS/SCS cluster virtual machines: \<SAPSystemSID\>-avset-ascs
    * Virtuele machines van DBMS-cluster: \<SAPSystemSID\>-avset-db

  * **Interne Load Balancer van Azure**:
    * Met alle poorten voor het ASCS/SCS-exemplaar en IP \<-\>adres SAPSystemSID-lb-ASCS
    * Met alle poorten voor de SQL server DBMS en het IP \<-\>adres SAPSystemSID-lb-db

  * **Netwerk beveiligings groep**: \<SAPSystemSID\>-NSG-ascs-0  
    * Met een open externe Remote Desktop Protocol-poort (RDP) naar \<de\>virtuele machine SAPSystemSID-ascs-0

> [!NOTE]
> Alle IP-adressen van de netwerk kaarten en interne load balancers van Azure zijn standaard dynamisch. Wijzig deze in vaste IP-adressen. Verderop in dit artikel wordt beschreven hoe u dit doet.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Implementeer virtuele machines met de connectiviteit van een bedrijfs netwerk (cross-premises) voor gebruik in productie
Voor productie-SAP-systemen implementeert u virtuele Azure-machines met een [bedrijfs netwerk verbinding (cross-premises)][planning-guide-2.2] met behulp van Azure VPN gateway of Azure ExpressRoute.

> [!NOTE]
> U kunt uw Azure Virtual Network-exemplaar gebruiken. Het virtuele netwerk en het subnet zijn al gemaakt en voor bereid.
>
>

1. Selecteer in de Azure Portal in het deel venster **para meters** in het vak **NEWOREXISTINGSUBNET** de optie **bestaande**.
2. Voeg in het vak **SUBNETID** de volledige teken reeks toe van de voor bereide subnet-id van het Azure-netwerk waar u uw virtuele Azure-machines wilt implementeren.
3. Voer deze Power shell-opdracht uit om een lijst met alle subnetten van Azure Network op te halen:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   In het veld **id** wordt de waarde voor de subnet-id weer gegeven.
4. Voer de volgende Power shell-opdracht uit om een lijst met alle subnet-ID-waarden weer te geven:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   De subnet-ID ziet er als volgt uit:

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Alleen Cloud SAP-instanties implementeren voor test en demo
U kunt uw SAP-systeem met hoge Beschik baarheid implementeren in een alleen-Cloud implementatie model. Dit soort implementatie is vooral nuttig voor demonstratie-en test voorbeelden. Het is niet geschikt voor productie-use cases.

- Selecteer in de Azure Portal in het deel venster **para meters** in het vak **NEWOREXISTINGSUBNET** de optie **Nieuw**. Laat het veld **SUBNETID** leeg.

  De SAP-Azure Resource Manager sjabloon maakt automatisch het virtuele netwerk van Azure en het subnet.

> [!NOTE]
> U moet ook ten minste één toegewezen virtuele machine implementeren voor Active Directory-en DNS-service in hetzelfde exemplaar van Azure Virtual Network. Deze virtuele machines worden niet gemaakt met de sjabloon.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>De infra structuur voorbereiden voor de architectuur sjabloon 2

U kunt deze Azure Resource Manager sjabloon voor SAP gebruiken om de implementatie van vereiste infrastructuur resources voor SAP-architectuur sjabloon 2 te vereenvoudigen.

Hier kunt u Azure Resource Manager sjablonen voor dit implementatie scenario ophalen:

* [Azure Marketplace-installatie kopie](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Azure Marketplace-installatie kopie met behulp van Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Aangepaste installatie kopie](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Aangepaste installatie kopie met behulp van Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>De infra structuur voorbereiden voor de architectuur sjabloon 3

U kunt de infra structuur voorbereiden en SAP configureren voor multi-SID. U kunt bijvoorbeeld een extra SAP ASCS/SCS-exemplaar toevoegen aan een *bestaande* cluster configuratie. Zie [een extra SAP ASCS/SCS-instantie configureren voor een bestaande cluster configuratie voor het maken van een SAP-multi-sid-configuratie in azure Resource Manager][sap-ha-multi-sid-guide]voor meer informatie.

Als u een nieuw cluster met meerdere SID'S wilt maken, kunt u gebruikmaken van de Quick Start- [sjablonen voor](https://github.com/Azure/azure-quickstart-templates)multi-sid op github.

Als u een nieuw cluster met meerdere SID'S wilt maken, moet u de volgende drie sjablonen implementeren:

* [ASCS/SCS-sjabloon](#ASCS-SCS-template)
* [Database sjabloon](#database-template)
* [Sjabloon toepassings servers](#application-servers-template)

De volgende secties bevatten meer informatie over de sjablonen en para meters die u moet opgeven in de sjablonen.

### <a name="ASCS-SCS-template"></a>ASCS/SCS-sjabloon

De ASCS/SCS-sjabloon implementeert twee virtuele machines die u kunt gebruiken om een Windows Server-failovercluster te maken dat meerdere ASCS/SCS-instanties host.

Als u de sjabloon ASCS/SCS multi-sid wilt instellen, voert u in de sjabloon [ASCS/SCS multi][sap-templates-3-tier-multisid-xscs-marketplace-image] -sid sjabloon of [ASCS/SCS multi-sid in met behulp van Managed disks][sap-templates-3-tier-multisid-xscs-marketplace-image-md]waarden in voor de volgende para meters:

- **Resource voorvoegsel**:  Stel het resource voorvoegsel in, dat wordt gebruikt voor het voor voegsel van alle resources die tijdens de implementatie zijn gemaakt. Omdat de resources niet deel uitmaken van één SAP-systeem, is het voor voegsel van de resource niet de SID van een SAP-systeem.  Het voor voegsel moet tussen de drie en zes tekens lang zijn.
- **Stack type**: Selecteer het stack type van het SAP-systeem. Afhankelijk van het type stack heeft Azure Load Balancer één (alleen ABAP of Java) of twee (ABAP + Java) privé-IP-adressen per SAP-systeem.
- **Type besturings systeem**: Selecteer het besturings systeem van de virtuele machines.
- **Aantal SAP-systemen**: Selecteer het aantal SAP-systemen dat u wilt installeren in dit cluster.
- **Systeem beschikbaarheid**: Selecteer **ha**.
- **Gebruikers naam en wacht woord**beheerder: Maak een nieuwe gebruiker die kan worden gebruikt om u aan te melden bij de computer.
- **Nieuw of bestaand subnet**: Stel in of een nieuw virtueel netwerk en subnet moet worden gemaakt of dat een bestaand subnet moet worden gebruikt. Als u al een virtueel netwerk hebt dat is verbonden met uw on-premises netwerk, selecteert u **bestaande**.
- **Subnet-id**: Als u de virtuele machine wilt implementeren in een bestaand VNet waarvoor u een subnet hebt gedefinieerd, moet de virtuele machine worden toegewezen aan, de ID van het specifieke subnet benoemen. De ID ziet er meestal als volgt uit:

  /Subscriptions/\<-abonnements\>-\<id/resourceGroups/naam\>van\<de resource groep\>/providers/Microsoft.Network/virtualNetworks/virtuele netwerk naam/subnets/ \<subnetnaam\>

De sjabloon implementeert één Azure Load Balancer-exemplaar, dat ondersteuning biedt voor meerdere SAP-systemen:

- De ASCS-exemplaren worden geconfigureerd voor instantie nummer 00, 10, 20...
- De SCS-exemplaren zijn geconfigureerd voor instantie nummer 01, 11, 21...
- De exemplaren van de ASCS-replicatie server (ERS) (alleen voor Linux) worden geconfigureerd voor instantie nummer 02, 12, 22...
- De exemplaren van de SCS ERS (alleen Linux) worden geconfigureerd voor instantie nummer 03, 13, 23...

De load balancer bevat 1 VIP (s) (2 voor Linux), 1x VIP voor ASCS/SCS en 1x VIP voor ERS (alleen Linux).

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a>SAP-ASCS/SCS-poorten
De volgende lijst bevat alle regels voor taak verdeling (waarbij x het nummer van het SAP-systeem is, bijvoorbeeld 1, 2, 3...):
- Windows-specifieke poorten voor elk SAP-systeem: 445, 5985
- ASCS-poorten (exemplaar nummer x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS-poorten (exemplaar nummer x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- ASCS ERS-poorten op Linux (exemplaar nummer x2): 33x2, 5x213, 5x214, 5x216
- SCS ERS-poorten op Linux (exemplaar nummer x3): 33x3, 5x313, 5x314, 5x316

De load balancer is geconfigureerd voor het gebruik van de volgende test poorten (waarbij x het nummer van het SAP-systeem is, bijvoorbeeld 1, 2, 3...):
- ASCS/SCS interne load balancer test poort: 620x0
- ERS interne load balancer test poort (alleen Linux): 621x2

### <a name="database-template"></a>Database sjabloon

De database sjabloon implementeert een of twee virtuele machines die u kunt gebruiken om de relationele Database Management System (RDBMS) voor één SAP-systeem te installeren. Als u bijvoorbeeld een ASCS/SCS-sjabloon voor vijf SAP-systemen implementeert, moet u deze sjabloon vijf keer implementeren.

Als u de sjabloon multi-SID voor de Data Base wilt instellen, voert u in de sjabloon sjabloon [multi-sid][sap-templates-3-tier-multisid-db-marketplace-image] of [Data Base multi-sid met behulp van Managed disks][sap-templates-3-tier-multisid-db-marketplace-image-md]waarden in voor de volgende para meters:

- **SAP-systeem-id**: Voer de SAP-systeem-ID in van het SAP-systeem dat u wilt installeren. De ID wordt gebruikt als een voor voegsel voor de resources die worden geïmplementeerd.
- **Type besturings systeem**: Selecteer het besturings systeem van de virtuele machines.
- **DBTYPE**: Selecteer het type Data Base dat u op het cluster wilt installeren. Selecteer **SQL** als u Microsoft SQL Server wilt installeren. Selecteer **Hana** als u SAP Hana wilt installeren op de virtuele machines. Zorg ervoor dat u het juiste type besturings systeem selecteert. Selecteer **Windows** voor SQL en selecteer een Linux-distributie voor Hana. Azure Load Balancer die is verbonden met de virtuele machines, is geconfigureerd voor de ondersteuning van het geselecteerde database type:
  * **SQL**: De load balancer-balans poort 1433. Zorg ervoor dat u deze poort gebruikt voor de configuratie van de SQL Server AlwaysOn.
  * **HANA**: De load balancer taakverdelings poorten 35015 en 35017. Zorg ervoor dat u SAP HANA installeert met instantie nummer **50**.
  De load balancer gebruikt test poort 62550.
- **SAP-systeem grootte**: Stel het aantal SAP'S in dat door het nieuwe systeem wordt geleverd. Als u niet zeker weet hoeveel SAP'S het systeem nodig heeft, vraagt u uw SAP-technologie partner of systeem integrator.
- **Systeem beschikbaarheid**: Selecteer **ha**.
- **Gebruikers naam en wacht woord**beheerder: Maak een nieuwe gebruiker die kan worden gebruikt om u aan te melden bij de computer.
- **Subnet-id**: Voer de ID in van het subnet dat u hebt gebruikt tijdens de implementatie van de ASCS/SCS-sjabloon of de ID van het subnet dat is gemaakt als onderdeel van de implementatie van de ASCS/SCS-sjabloon.

### <a name="application-servers-template"></a>Sjabloon toepassings servers

Met de sjabloon toepassings servers worden twee of meer virtuele machines geïmplementeerd die kunnen worden gebruikt als SAP-toepassings server instanties voor één SAP-systeem. Als u bijvoorbeeld een ASCS/SCS-sjabloon voor vijf SAP-systemen implementeert, moet u deze sjabloon vijf keer implementeren.

Als u de sjabloon toepassings servers multi-SID wilt instellen, voert u in de sjabloon [toepassings servers multi-sid][sap-templates-3-tier-multisid-apps-marketplace-image] -sjabloon of [toepassings servers][sap-templates-3-tier-multisid-apps-marketplace-image-md]met behulp van Managed disks waarden in voor de volgende para meters:

  -  **SAP-systeem-id**: Voer de SAP-systeem-ID in van het SAP-systeem dat u wilt installeren. De ID wordt gebruikt als een voor voegsel voor de resources die worden geïmplementeerd.
  -  **Type besturings systeem**: Selecteer het besturings systeem van de virtuele machines.
  -  **SAP-systeem grootte**: Het aantal SAP'S dat het nieuwe systeem biedt. Als u niet zeker weet hoeveel SAP'S het systeem nodig heeft, vraagt u uw SAP-technologie partner of systeem integrator.
  -  **Systeem beschikbaarheid**: Selecteer **ha**.
  -  **Gebruikers naam en wacht woord**beheerder: Maak een nieuwe gebruiker die kan worden gebruikt om u aan te melden bij de computer.
  -  **Subnet-id**: Voer de ID in van het subnet dat u hebt gebruikt tijdens de implementatie van de ASCS/SCS-sjabloon of de ID van het subnet dat is gemaakt als onderdeel van de implementatie van de ASCS/SCS-sjabloon.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Azure Virtual Network
In ons voor beeld is de adres ruimte van het Azure Virtual Network-exemplaar 10.0.0.0/16. Er bevindt zich één subnet met de naam subnet, met het adres bereik 10.0.0.0/24. Alle virtuele machines en interne load balancers worden geïmplementeerd in dit virtuele netwerk.

> [!IMPORTANT]
> Breng geen wijzigingen aan in de netwerk instellingen binnen het gast besturingssysteem. Dit geldt ook voor IP-adressen, DNS-servers en subnet. Configureer al uw netwerk instellingen in Azure. De Dynamic Host Configuration Protocol (DHCP)-service geeft uw instellingen door.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>IP-adressen van DNS

Voer de volgende stappen uit om de vereiste DNS-IP-adressen in te stellen:

1. Controleer in de Azure Portal in het deel venster **DNS-servers** of de optie **DNS-servers** voor het virtuele netwerk is ingesteld op **aangepaste DNS**.
2. Selecteer uw instellingen op basis van het type netwerk dat u hebt. Zie de volgende bronnen voor meer informatie:
   * [Connectiviteit van het bedrijfs netwerk (cross-premises)][planning-guide-2.2]: Voeg de IP-adressen van de on-premises DNS-servers toe.  
   U kunt on-premises DNS-servers uitbreiden naar de virtuele machines die worden uitgevoerd in Azure. In dat scenario kunt u de IP-adressen toevoegen van de virtuele machines van Azure waarop u de DNS-service uitvoert.
   * Voor VM-implementaties die zijn geïsoleerd in Azure: Implementeer een extra virtuele machine in hetzelfde Virtual Network exemplaar dat fungeert als een DNS-server. Voeg de IP-adressen toe van de virtuele Azure-machines die u hebt ingesteld om de DNS-service uit te voeren.

   ![Afbeelding 2: DNS-servers configureren voor Azure Virtual Network][sap-ha-guide-figure-3001]

   _**Afbeelding 2:** DNS-servers configureren voor Azure Virtual Network_

   > [!NOTE]
   > Als u de IP-adressen van de DNS-servers wijzigt, moet u de virtuele machines van Azure opnieuw opstarten om de wijziging toe te passen en de nieuwe DNS-servers door te geven.
   >
   >

In ons voor beeld is de DNS-service geïnstalleerd en geconfigureerd op deze virtuele Windows-machines:

| Virtuele-machine functie | Hostnaam van virtuele machine | Naam van netwerk kaart | Vast IP-adres |
| --- | --- | --- | --- |
| Eerste DNS-server |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Tweede DNS-server |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Hostnamen en statische IP-adressen voor het geclusterde exemplaar van SAP ASCS/SCS geclusterde instantie en DBMS

Voor on-premises implementatie hebt u deze gereserveerde hostnamen en IP-adressen nodig:

| Rol van virtuele hostnaam | Naam van virtuele host | Virtueel statisch IP-adres |
| --- | --- | --- |
| SAP ASCS/SCS First virtual host name (voor cluster beheer) |PR1-ascs-vir |10.0.0.42 |
| Naam van de virtuele host van het SAP ASCS/SCS-exemplaar |PR1-ascs-SAP |10.0.0.43 |
| SAP DBMS tweede cluster naam virtuele host (Cluster beheer) |pr1-dbms-vir |10.0.0.32 |

Wanneer u het cluster maakt, maakt u de namen van de virtuele hosts PR1-ascs-vir en PR1-DBMS-vir en de bijbehorende IP-adressen die het cluster zelf beheren. Zie [cluster knooppunten verzamelen in een cluster configuratie][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]voor meer informatie over hoe u dit doet.

U kunt hand matig de andere twee namen van virtuele hosts maken, PR1-ascs-SAP en PR1-DBMS-SAP, en de bijbehorende IP-adressen op de DNS-server. Het geclusterde SAP ASCS/SCS-exemplaar en het geclusterde DBMS-exemplaar gebruiken deze bronnen. Zie [een virtuele-hostnaam maken voor een geclusterde SAP ASCS/SCS-instantie][sap-ha-guide-9.1.1]voor meer informatie over hoe u dit doet.

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Statische IP-adressen voor de virtuele machines van SAP instellen
Nadat u de virtuele machines hebt geïmplementeerd voor gebruik in uw cluster, moet u vaste IP-adressen instellen voor alle virtuele machines. Doe dit in de Azure Virtual Network-configuratie en niet in het gast besturingssysteem.

1. Selecteer in het Azure Portal**IP-adres**van de **resource groep** > **netwerk kaart** > **instellingen** > .
2. Selecteer in het deel venster **IP-adressen** onder **toewijzing**de optie **statisch**. Geef in het vak **IP-adres** het IP-adres op dat u wilt gebruiken.

   > [!NOTE]
   > Als u het IP-adres van de netwerk kaart wijzigt, moet u de virtuele machines van Azure opnieuw opstarten om de wijziging toe te passen.  
   >
   >

   ![Afbeelding 3: Statische IP-adressen voor de netwerk kaart van elke virtuele machine instellen][sap-ha-guide-figure-3002]

   _**Afbeelding 3:** Statische IP-adressen voor de netwerk kaart van elke virtuele machine instellen_

   Herhaal deze stap voor alle netwerk interfaces, dat wil zeggen, voor alle virtuele machines, met inbegrip van virtuele machines die u wilt gebruiken voor uw Active Directory of DNS-service.

In ons voor beeld hebben we deze virtuele machines en vaste IP-adressen:

| Virtuele-machine functie | Hostnaam van virtuele machine | Naam van netwerk kaart | Vast IP-adres |
| --- | --- | --- | --- |
| Eerste SAP-toepassings Server exemplaar |pr1-di-0 |PR1-NIC-di-0 |10.0.0.50 |
| Tweede SAP-toepassings Server exemplaar |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Laatste SAP-toepassings Server exemplaar |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Eerste cluster knooppunt voor ASCS/SCS-instantie |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Tweede cluster knooppunt voor ASCS/SCS-instantie |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Eerste cluster knooppunt voor het DBMS-exemplaar |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Tweede cluster knooppunt voor DBMS-exemplaar |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Stel een statisch IP-adres in voor de interne load balancer van Azure

Met de SAP-Azure Resource Manager sjabloon maakt u een interne Azure-load balancer die wordt gebruikt voor het SAP-ASCS/SCS-exemplaar cluster en het DBMS-cluster.

> [!IMPORTANT]
> Het IP-adres van de virtuele hostnaam van de SAP ASCS/SCS is hetzelfde als het IP-adres van de SAP ASCS/SCS Internal load balancer: PR1-lb-ASCS.
> Het IP-adres van de virtuele naam van het DBMS is hetzelfde als het IP-adres van de interne DBMS-load balancer: PR1-lb-DBMS.
>
>

Een statisch IP-adres voor de interne Azure-load balancer instellen:

1. De eerste implementatie stelt het interne load balancer IP-adres in op **dynamisch**. Selecteer in de Azure Portal in het deel venster **IP-adressen** onder **toewijzing**de optie **statisch**.
2. Stel het IP-adres van de interne load balancer **PR1-lb-ascs** in op het IP-adres van de naam van de virtuele host van het SAP ASCS/SCS-exemplaar.
3. Stel het IP-adres van de interne load balancer **PR1-lb-DBMS** in op het IP-adres van de naam van de virtuele host van het DBMS-exemplaar.

   ![Afbeelding 4: Statische IP-adressen voor de interne load balancer instellen voor het SAP ASCS/SCS-exemplaar][sap-ha-guide-figure-3003]

   _**Afbeelding 4:** Statische IP-adressen voor de interne load balancer instellen voor het SAP ASCS/SCS-exemplaar_

In ons voor beeld hebben we twee interne load balancers van Azure met deze statische IP-adressen:

| Rol van interne Azure-load balancer | Naam van de interne load balancer van Azure | Vast IP-adres |
| --- | --- | --- |
| Load balancer van SAP ASCS/SCS-instantie intern |pr1-lb-ascs |10.0.0.43 |
| SAP DBMS interne load balancer |pr1-lb-dbms |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Standaard regels voor ASCS/SCS-taak verdeling voor de interne load balancer van Azure

De SAP-Azure Resource Manager sjabloon maakt de poorten die u nodig hebt:
* Een ABAP ASCS-exemplaar met het standaard instantie nummer 00
* Een Java SCS-exemplaar met het standaard instantie nummer 01

Wanneer u uw SAP ASCS/SCS-exemplaar installeert, moet u het standaard instantie nummer 00 voor uw ABAP ASCS-exemplaar en het standaard instantie nummer 01 voor uw Java SCS-exemplaar gebruiken.

Maak vervolgens de vereiste interne taakverdelings eindpunten voor de SAP NetWeaver-poorten.

Als u vereiste interne taakverdelings eindpunten wilt maken, moet u eerst deze taakverdelings eindpunten maken voor de SAP NetWeaver ABAP ASCS-poorten:

| Service-en taakverdelings regel naam | Standaard poort nummers | Concrete poorten voor (ASCS-exemplaar met exemplaar nummer 00) (ERS met 10) |
| --- | --- | --- |
| Server- *lbrule3200* in wachtrij plaatsen |32\<InstanceNumber\> |3200 |
| ABAP-bericht server/ *lbrule3600* |36\<InstanceNumber\> |3600 |
| Intern ABAP-bericht/ *lbrule3900* |39\<InstanceNumber\> |3900 |
| HTTP- *Lbrule8100* van bericht server |81\<InstanceNumber\> |8100 |
| ASCS HTTP/ *Lbrule50013* voor SAP-start service |5\<InstanceNumber\>13 |50013 |
| ASCS HTTPS/ *Lbrule50014* voor SAP-start service |5\<InstanceNumber\>14 |50014 |
| Replicatie- *Lbrule50016* in wachtrij plaatsen |5\<InstanceNumber\>16 |50016 |
| ERS HTTP- *Lbrule51013* voor SAP-start service |5\<InstanceNumber\>13 |51013 |
| ERS HTTP- *Lbrule51014* voor SAP-start service |5\<InstanceNumber\>14 |51014 |
| *Lbrule5985* voor Windows Remote Management (WinRM) | |5985 |
| Bestands share *Lbrule445* | |445 |

**Tabel 1:** Poort nummers van de SAP NetWeaver ABAP ASCS-instanties

Vervolgens maakt u deze eind punten voor taak verdeling voor de SAP NetWeaver Java SCS-poorten:

| Service-en taakverdelings regel naam | Standaard poort nummers | Concrete poorten voor (SCS-exemplaar met exemplaar nummer 01) (ERS met 11) |
| --- | --- | --- |
| Server- *lbrule3201* in wachtrij plaatsen |32\<InstanceNumber\> |3201 |
| Gateway server- *lbrule3301* |33\<InstanceNumber\> |3301 |
| Java-bericht server- *lbrule3900* |39\<InstanceNumber\> |3901 |
| HTTP- *Lbrule8101* van bericht server |81\<InstanceNumber\> |8101 |
| SCS HTTP/ *Lbrule50113* voor SAP-start service |5\<InstanceNumber\>13 |50113 |
| SCS HTTPS/ *Lbrule50114* voor SAP-start service |5\<InstanceNumber\>14 |50114 |
| Replicatie- *Lbrule50116* in wachtrij plaatsen |5\<InstanceNumber\>16 |50116 |
| ERS HTTP- *Lbrule51113* voor SAP-start service |5\<InstanceNumber\>13 |51113 |
| ERS HTTP- *Lbrule51114* voor SAP-start service |5\<InstanceNumber\>14 |51114 |
| WinRM *Lbrule5985* | |5985 |
| Bestands share *Lbrule445* | |445 |

**Tabel 2:** Poort nummers van de SAP NetWeaver Java SCS-instanties

![Afbeelding 5: Standaard regels voor ASCS/SCS-taak verdeling voor de interne load balancer van Azure][sap-ha-guide-figure-3004]

_**Afbeelding 5:** Standaard regels voor ASCS/SCS-taak verdeling voor de interne load balancer van Azure_

Stel het IP-adres van de load balancer PR1-lb-DBMS in op het IP-adres van de naam van de virtuele host van het DBMS-exemplaar.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Wijzig de ASCS/SCS-standaard regels voor taak verdeling voor de interne Azure-load balancer

Als u andere nummers wilt gebruiken voor de SAP-instanties ASCS of SCS, moet u de namen en waarden van de poorten van de standaard waarden wijzigen.

1. Selecteer >   **\<in het Azure Portal sid\>-lb-ascs Load Balancer**taakverdelings regels.
2. Wijzig deze waarden voor alle taakverdelings regels die horen bij het SAP ASCS-of SCS-exemplaar:

   * Name
   * Port
   * Poort van back-end

   Als u bijvoorbeeld het standaard ASCS-instantie nummer wilt wijzigen van 00 in 31, moet u de wijzigingen aanbrengen voor alle poorten die worden vermeld in tabel 1.

   Hier volgt een voor beeld van een update voor poort *lbrule3200*.

   ![Afbeelding 6: Wijzig de ASCS/SCS-standaard regels voor taak verdeling voor de interne Azure-load balancer][sap-ha-guide-figure-3005]

   _**Afbeelding 6:** Wijzig de ASCS/SCS-standaard regels voor taak verdeling voor de interne Azure-load balancer_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Virtuele Windows-machines toevoegen aan het domein

Nadat u een statisch IP-adres aan de virtuele machines hebt toegewezen, voegt u de virtuele machines toe aan het domein.

![Afbeelding 7: Een virtuele machine toevoegen aan een domein][sap-ha-guide-figure-3006]

_**Afbeelding 7:** Een virtuele machine toevoegen aan een domein_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Voeg register vermeldingen toe aan cluster knooppunten van het SAP ASCS/SCS-exemplaar

Azure Load Balancer heeft een interne load balancer die verbindingen sluit wanneer de verbindingen gedurende een bepaalde periode inactief zijn (een time-out voor inactiviteit). SAP-werk processen in dialoogvenster exemplaren openen verbindingen met het SAP-bewerkings proces zodra de eerste aanvraag voor het plaatsen/verwijderen van de wachtrij moet worden verzonden. Deze verbindingen blijven doorgaans tot stand worden gebracht tot het werk proces of het proces voor het in de wachtrij plaatsen opnieuw wordt gestart. Als de verbinding echter gedurende een bepaalde periode inactief is, worden de verbindingen met de interne Azure-load balancer gesloten. Dit is geen probleem omdat het SAP werk proces de verbinding met het bewerkings proces moet herstellen als het niet meer bestaat. Deze activiteiten zijn gedocumenteerd in de ontwikkel aars van SAP-processen, maar ze maken een grote hoeveelheid extra inhoud in deze traceringen. Het is een goed idee om het TCP/IP- `KeepAliveTime` adres `KeepAliveInterval` en de cluster knooppunten te wijzigen. Combi neer deze wijzigingen in de TCP/IP-para meters met SAP-profiel parameters, verderop in het artikel beschreven.

Als u Register vermeldingen wilt toevoegen aan cluster knooppunten van het SAP ASCS/SCS-exemplaar, voegt u eerst deze Windows-register vermeldingen toe op beide Windows-cluster knooppunten voor SAP ASCS/SCS:

| Path | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Naam van de variabele |`KeepAliveTime` |
| Type variabele |REG_DWORD (decimaal) |
| Value |120000 |
| Koppeling naar documentatie |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabel 3:** De eerste TCP/IP-para meter wijzigen

Voeg deze Windows-register vermelding vervolgens toe aan de Windows-cluster knooppunten voor SAP ASCS/SCS:

| Path | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Naam van de variabele |`KeepAliveInterval` |
| Type variabele |REG_DWORD (decimaal) |
| Value |120000 |
| Koppeling naar documentatie |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**Tabel 4:** De tweede TCP/IP-para meter wijzigen

Start beide cluster knooppunten opnieuw op om de wijzigingen toe te passen.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Een Windows Server-failovercluster instellen voor een SAP ASCS/SCS-exemplaar

Voor het instellen van een Windows Server-failovercluster voor een SAP ASCS/SCS-exemplaar zijn de volgende taken vereist:

- Verzamel de cluster knooppunten in een cluster configuratie.
- Een cluster bestands share-Witness configureren.

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>De cluster knooppunten in een cluster configuratie verzamelen

1. Voeg in de wizard functies en onderdelen toevoegen Failover Clustering toe aan beide cluster knooppunten.
2. Stel het failovercluster in met behulp van Failoverclusterbeheer. In Failoverclusterbeheer selecteert u **cluster maken**en voegt u vervolgens alleen de naam van het eerste cluster toe (knoop punt a). Voeg het tweede knoop punt nog niet toe. u voegt het tweede knoop punt in een latere stap toe.

   ![Afbeelding 8: De naam van de server of de virtuele machine van het eerste cluster knooppunt toevoegen][sap-ha-guide-figure-3007]

   _**Afbeelding 8:** De naam van de server of de virtuele machine van het eerste cluster knooppunt toevoegen_

3. Voer de netwerk naam (naam van de virtuele host) van het cluster in.

   ![Afbeelding 9: Voer de naam van het cluster in][sap-ha-guide-figure-3008]

   _**Afbeelding 9:** Voer de naam van het cluster in_

4. Nadat u het cluster hebt gemaakt, voert u een cluster validatie test uit.

   ![Afbeelding 10: De cluster validatie controle uitvoeren][sap-ha-guide-figure-3009]

   _**Afbeelding 10:** De cluster validatie controle uitvoeren_

   U kunt op dit moment in het proces waarschuwingen over schijven negeren. U voegt later een bestands share-Witness en de SIOS gedeelde schijven toe. In deze fase hoeft u zich geen zorgen te maken over een quorum.

   ![Afbeelding 11: Er is geen quorum schijf gevonden][sap-ha-guide-figure-3010]

   _**Afbeelding 11:** Er is geen quorum schijf gevonden_

   ![Afbeelding 12: Een basis cluster bron moet een nieuw IP-adres hebben][sap-ha-guide-figure-3011]

   _**Afbeelding 12:** Een basis cluster bron moet een nieuw IP-adres hebben_

5. Wijzig het IP-adres van de kern Cluster service. Het cluster kan pas worden gestart als u het IP-adres van de kern Cluster service wijzigt, omdat het IP-adres van de server verwijst naar een van de knoop punten van de virtuele machine. Doe dit op de pagina **Eigenschappen** van de IP-bron van de kern Cluster service.

   We moeten bijvoorbeeld een IP-adres (in ons voor beeld 10.0.0.42) toewijzen voor de virtuele cluster naam PR1-ascs-vir.

   ![Afbeelding 13: Wijzig in het dialoog venster Eigenschappen het IP-adres][sap-ha-guide-figure-3012]

   _**Afbeelding 13:** Wijzig in het dialoog venster **Eigenschappen** het IP-adres_

   ![Afbeelding 14: Wijs het IP-adres toe dat is gereserveerd voor het cluster][sap-ha-guide-figure-3013]

   _**Afbeelding 14:** Wijs het IP-adres toe dat is gereserveerd voor het cluster_

6. Haal de naam van de virtuele cluster-host online.

   ![Afbeelding 15: De cluster core-service is actief, met het juiste IP-adres][sap-ha-guide-figure-3014]

   _**Afbeelding 15:** De cluster core-service is actief, met het juiste IP-adres_

7. Voeg het tweede cluster knooppunt toe.

   Nu de kern Cluster service actief is, kunt u het tweede cluster knooppunt toevoegen.

   ![Afbeelding 16 het tweede cluster knooppunt toevoegen][sap-ha-guide-figure-3015]

   _**Afbeelding 16:** Het tweede cluster knooppunt toevoegen_

8. Voer een naam in voor de tweede host van het cluster knooppunt.

   ![Afbeelding 17: Voer de tweede hostnaam van het cluster knooppunt in][sap-ha-guide-figure-3016]

   _**Afbeelding 17:** Voer de tweede hostnaam van het cluster knooppunt in_

   > [!IMPORTANT]
   > Zorg ervoor dat het selectie vakje **Voeg alle in aanmerking komende opslag aan het cluster toe** is *niet* is ingeschakeld.  
   >
   >

   ![Afbeelding 18: Schakel het selectie vakje niet in][sap-ha-guide-figure-3017]

   _**Afbeelding 18:** Schakel het selectie vakje *niet* in_

   U kunt waarschuwingen over quorum en schijven negeren. U moet het quorum instellen en de schijf later delen, zoals wordt beschreven in [de installatie van Sios data keeper cluster Edition voor een SAP ASCS/SCS-cluster share schijf][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

   ![Afbeelding 19: Waarschuwingen over het schijf quorum negeren][sap-ha-guide-figure-3018]

   _**Afbeelding 19:** Waarschuwingen over het schijf quorum negeren_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Een cluster bestands share-Witness configureren

Het configureren van een cluster bestands share-Witness omvat de volgende taken:

- Maak een bestands share.
- Stel het bestands share-Witness-quorum in Failoverclusterbeheer.

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Een bestands share maken

1. Selecteer een bestands share-Witness in plaats van een quorum schijf. SIOS data keeper ondersteunt deze optie.

   In de voor beelden in dit artikel bevindt de bestandssharewitness zich op de Active Directory of DNS-server die wordt uitgevoerd in Azure. De bestandssharewitness van de bestands share heet domcontr-0. Omdat u een VPN-verbinding met Azure hebt geconfigureerd (via VPN Gateway of Azure ExpressRoute), is uw Active Directory-of DNS-service on-premises en is niet geschikt voor het uitvoeren van een bestandssharewitness.

   > [!NOTE]
   > Als uw Active Directory-of DNS-service alleen on-premises wordt uitgevoerd, moet u de bestands share-Witness niet configureren op het Active Directory of het DNS-Windows-besturings systeem dat on-premises wordt uitgevoerd. Netwerk latentie tussen cluster knooppunten die worden uitgevoerd in Azure en on-premises Active Directory of DNS zijn mogelijk te groot en veroorzaken verbindings problemen. Zorg ervoor dat u de bestands share-Witness hebt geconfigureerd op een virtuele machine van Azure die dicht bij het cluster knooppunt wordt uitgevoerd.  
   >
   >

   Het quorum station heeft ten minste 1.024 MB beschik bare ruimte nodig. U kunt 2.048 MB beschik bare ruimte voor het quorum station aanraden.

2. Voeg het cluster naam object toe.

   ![Afbeelding 20: Wijs de machtigingen voor de share voor het cluster naam object toe][sap-ha-guide-figure-3019]

   _**Afbeelding 20:** Wijs de machtigingen voor de share voor het cluster naam object toe_

   Zorg ervoor dat de machtigingen de bevoegdheid voor het wijzigen van gegevens in de share voor het cluster naam object bevatten (in het voor beeld PR1-ascs-vir $).

3. Selecteer **toevoegen**om het cluster naam object toe te voegen aan de lijst. Wijzig het filter om op computer objecten te controleren, naast de afbeeldingen die in afbeelding 22 worden weer gegeven.

   ![Afbeelding 21: Object typen wijzigen om computers toe te voegen][sap-ha-guide-figure-3020]

   _**Afbeelding 21:** **Object typen** wijzigen om computers toe te voegen_

   ![Afbeelding 22: Selectie vakje computers selecteren][sap-ha-guide-figure-3021]

   _**Afbeelding 22:** Selectie vakje **computers** selecteren_

4. Voer het object cluster naam in zoals weer gegeven in afbeelding 21. Omdat de record al is gemaakt, kunt u de machtigingen wijzigen, zoals wordt weer gegeven in afbeelding 20.

5. Selecteer het tabblad **beveiliging** van de share en stel vervolgens gedetailleerde machtigingen in voor het cluster naam object.

   ![Afbeelding 23: De beveiligings kenmerken voor het cluster naam object op het bestands share quorum instellen][sap-ha-guide-figure-3022]

   _**Afbeelding 23:** De beveiligings kenmerken voor het cluster naam object op het bestands share quorum instellen_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Het bestands share-Witness-quorum instellen in Failoverclusterbeheer

1. Open de wizard quorum instelling configureren.

   ![Afbeelding 24: De wizard cluster quorum instelling configureren starten][sap-ha-guide-figure-3023]

   _**Afbeelding 24:** De wizard cluster quorum instelling configureren starten_

2. Op de pagina **optie voor quorum configuratie** selecteren selecteert u **de quorumwitness selecteren**.

   ![Afbeelding 25: Quorum configuraties waaruit u kunt kiezen][sap-ha-guide-figure-3024]

   _**Afbeelding 25:** Quorum configuraties waaruit u kunt kiezen_

3. Selecteer op de pagina **Quorumwitness selecteren** de optie **een bestandssharewitness configureren**.

   ![Afbeelding 26: De bestandssharewitness selecteren][sap-ha-guide-figure-3025]

   _**Afbeelding 26:** De bestandssharewitness selecteren_

4. Voer het UNC-pad naar de bestands share in (in het \\voor beeld domcontr-0\FSW). Selecteer **volgende**om een lijst weer te geven met de wijzigingen die u kunt aanbrengen.

   ![Afbeelding 27: Definieer de locatie van de bestands share voor de witness-share][sap-ha-guide-figure-3026]

   _**Afbeelding 27:** Definieer de locatie van de bestands share voor de witness-share_

5. Selecteer de gewenste wijzigingen en selecteer vervolgens **volgende**. U moet de cluster configuratie opnieuw configureren, zoals wordt weer gegeven in afbeelding 28:  

   ![Afbeelding 28: Bevestiging dat u het cluster opnieuw hebt geconfigureerd][sap-ha-guide-figure-3027]

   _**Afbeelding 28:** Bevestiging dat u het cluster opnieuw hebt geconfigureerd_

Nadat u het Windows-failovercluster hebt geïnstalleerd, moet u enkele drempel waarden wijzigen zodat failover-detectie wordt aangepast aan de voor waarden in Azure. De para meters die moeten worden gewijzigd, worden beschreven in de drempel waarden voor het afstemmen van [failover cluster netwerk][tuning-failover-cluster-network-thresholds]. Ervan uitgaande dat uw twee virtuele machines waaruit de configuratie van het Windows-cluster wordt opgebouwd voor ASCS/SCS zich in hetzelfde subnet bevinden, wijzigt u de volgende para meters in deze waarden:

- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

Deze instellingen zijn getest met klanten en bieden een goede inbreuk. Ze zijn robuust genoeg, maar ze bieden ook failover die snel genoeg is in echte fout situaties op een SAP-software of in een knoop punt-of VM-fout.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>De data keeper-cluster versie van SIOS installeren voor de SAP ASCS/SCS-cluster share schijf

U hebt nu een werkende Windows Server failover clustering-configuratie in Azure. Als u een SAP ASCS/SCS-exemplaar wilt installeren, hebt u een gedeelde schijf resource nodig. U kunt de gedeelde schijf bronnen die u nodig hebt, niet maken in Azure. SIOS data keeper cluster Edition is een oplossing van derden die u kunt gebruiken om gedeelde schijf bronnen te maken.

U moet de volgende taken uitvoeren om de data keeper-cluster versie van SIOS te installeren voor de SAP ASCS/SCS-cluster share disk:

- Voeg Microsoft .NET Framework 3,5 toe.
- Installeer SIOS data keeper.
- Stel SIOS data keeper in.

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>.NET Framework 3,5 toevoegen
.NET Framework 3,5 wordt niet automatisch geactiveerd of geïnstalleerd op Windows Server 2012 R2. Omdat voor SIOS data keeper .NET vereist is voor alle knoop punten waarop u data keeper installeert, moet u .NET Framework 3,5 installeren op het gast besturingssysteem van alle virtuele machines in het cluster.

Er zijn twee manieren om .NET Framework 3,5 toe te voegen:

- Gebruik de wizard functies en onderdelen toevoegen in Windows, zoals wordt weer gegeven in afbeelding 29:

  ![Afbeelding 29: .NET Framework 3,5 installeren met behulp van de wizard functies en onderdelen toevoegen][sap-ha-guide-figure-3028]

  _**Afbeelding 29:** .NET Framework 3,5 installeren met behulp van de wizard functies en onderdelen toevoegen_

  ![Afbeelding 30: Voortgangs balk voor installatie wanneer u .NET Framework 3,5 installeert met behulp van de wizard functies en onderdelen toevoegen][sap-ha-guide-figure-3029]

  _**Afbeelding 30:** Voortgangs balk voor installatie wanneer u .NET Framework 3,5 installeert met behulp van de wizard functies en onderdelen toevoegen_

- Gebruik het opdracht regel programma DISM. exe. Voor dit type installatie moet u toegang hebben tot de map SxS op het Windows-installatie medium. Voer de volgende opdracht uit vanaf een opdracht prompt met verhoogde bevoegdheid:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a>SIOS data keeper installeren

Installeer de data keeper-cluster versie van SIOS op elk knoop punt in het cluster. Als u virtuele gedeelde opslag met SIOS data keeper wilt maken, maakt u een gesynchroniseerde mirror en simuleert u vervolgens gedeelde cluster opslag.

Voordat u de SIOS-software installeert, maakt u de domein gebruiker DataKeeperSvc.

> [!NOTE]
> Voeg de domein gebruiker DataKeeperSvc toe aan de lokale groep Administrators op beide cluster knooppunten.
>
>

U kunt SIOS data keeper als volgt installeren:

1. Installeer de SIOS-software op beide cluster knooppunten.

   ![SIOS-installatie programma][sap-ha-guide-figure-3030]

   ![Afbeelding 31: Eerste pagina van de data keeper-installatie van SIOS][sap-ha-guide-figure-3031]

   _**Afbeelding 31:** Eerste pagina van de data keeper-installatie van SIOS_

2. Selecteer **Ja**in het dialoog venster.

   ![Afbeelding 32: Data keeper informeert u dat een service wordt uitgeschakeld][sap-ha-guide-figure-3032]

   _**Afbeelding 32:** Data keeper informeert u dat een service wordt uitgeschakeld_

3. In het dialoog venster wordt u aangeraden domein- **of Server account**te selecteren.

   ![Afbeelding 33: Gebruikers selectie voor SIOS data keeper][sap-ha-guide-figure-3033]

   _**Afbeelding 33:** Gebruikers selectie voor SIOS data keeper_

4. Voer de gebruikers naam en het wacht woord voor het domein account in dat u hebt gemaakt voor SIOS data keeper.

   ![Afbeelding 34: Voer de gebruikers naam en het wacht woord van het domein in voor de data keeper-installatie van SIOS][sap-ha-guide-figure-3034]

   _**Afbeelding 34:** Voer de gebruikers naam en het wacht woord van het domein in voor de data keeper-installatie van SIOS_

5. Installeer de licentie sleutel voor uw SIOS data keeper-exemplaar, zoals wordt weer gegeven in afbeelding 35.

   ![Afbeelding 35: Voer uw data keeper-licentie sleutel voor SIOS in][sap-ha-guide-figure-3035]

   _**Afbeelding 35:** Voer uw data keeper-licentie sleutel voor SIOS in_

6. Start de virtuele machine opnieuw op wanneer dit wordt gevraagd.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>SIOS data keeper instellen

Nadat u SIOS data keeper op beide knoop punten hebt geïnstalleerd, start u de configuratie. Het doel van de configuratie is het synchroon repliceren van gegevens tussen de extra schijven die aan de virtuele machines zijn gekoppeld.

1. Start het hulp programma data keeper beheer en configuratie en selecteer vervolgens **verbinding maken met server**.

   ![Afbeelding 36: Data keeper-beheer en configuratie hulpprogramma voor SIOS][sap-ha-guide-figure-3036]

   _**Afbeelding 36:** Data keeper-beheer en configuratie hulpprogramma voor SIOS_

2. Voer de naam of het TCP/IP-adres in van het eerste knoop punt waarvan het beheer-en configuratie hulpprogramma verbinding moet maken en, in een tweede stap, het tweede knoop punt.

   ![Afbeelding 37: Voeg de naam of het TCP/IP-adres in van het eerste knoop punt waarmee het beheer-en configuratie hulpprogramma verbinding moet maken, en in een tweede stap het tweede knoop punt][sap-ha-guide-figure-3037]

   _**Afbeelding 37:** Voeg de naam of het TCP/IP-adres in van het eerste knoop punt waarmee het beheer-en configuratie hulpprogramma verbinding moet maken, en in een tweede stap het tweede knoop punt_

3. Maak de replicatie taak tussen de twee knoop punten.

   ![Afbeelding 38: Een replicatie taak maken][sap-ha-guide-figure-3038]

   _**Afbeelding 38:** Een replicatie taak maken_

   Een wizard leidt u door het proces van het maken van een replicatie taak.

4. Definieer de naam van de replicatie taak.

   ![Afbeelding 39: De naam van de replicatie taak definiëren][sap-ha-guide-figure-3039]

   _**Afbeelding 39:** De naam van de replicatie taak definiëren_

   ![Afbeelding 40: Definieer de basis gegevens voor het knoop punt. dit moet het huidige bron knooppunt zijn][sap-ha-guide-figure-3040]

   _**Afbeelding 40:** Definieer de basis gegevens voor het knoop punt. dit moet het huidige bron knooppunt zijn_

5. Definieer de naam, het TCP/IP-adres en het schijf volume van het doel knooppunt.

   ![Afbeelding 41: Definieer de naam, het TCP/IP-adres en het schijf volume van het huidige doel knooppunt][sap-ha-guide-figure-3041]

   _**Afbeelding 41:** Definieer de naam, het TCP/IP-adres en het schijf volume van het huidige doel knooppunt_

6. Definieer de compressie algoritmen. In ons voor beeld raden we u aan de replicatie stroom te comprimeren. Met name bij hersynchronisaties situaties vermindert de compressie van de replicatie stroom aanzienlijk minder hersynchronisaties tijd. Compressie maakt gebruik van de CPU-en RAM-bronnen van een virtuele machine. Naarmate de compressie frequentie toeneemt, neemt het volume van de CPU-resources die worden gebruikt, dus toe. U kunt deze instelling later aanpassen.

7. Een andere instelling die u moet controleren, is of de replicatie asynchroon of synchroon plaatsvindt. Wanneer u SAP ASCS/SCS-configuraties beveiligt, moet u synchrone replicatie gebruiken.  

   ![Afbeelding 42: Replicatie Details definiëren][sap-ha-guide-figure-3042]

   _**Afbeelding 42:** Replicatie Details definiëren_

8. Definiëren of het volume dat door de replicatie taak wordt gerepliceerd, moet worden weer gegeven als een configuratie voor een Windows Server-failovercluster als een gedeelde schijf. Voor de SAP-ASCS/SCS-configuratie selecteert u **Ja** zodat het gerepliceerde volume door het Windows-cluster wordt gezien als een gedeelde schijf die als een cluster volume kan worden gebruikt.

   ![Afbeelding 43: Selecteer Ja om het gerepliceerde volume als cluster volume in te stellen][sap-ha-guide-figure-3043]

   _**Afbeelding 43:** Selecteer **Ja** om het gerepliceerde volume als cluster volume in te stellen_

   Nadat het volume is gemaakt, wordt in het data keeper-beheer en configuratie programma weer gegeven dat de replicatie taak actief is.

   ![Afbeelding 44: Data keeper synchrone mirroring voor de SAP ASCS/SCS-share schijf is actief][sap-ha-guide-figure-3044]

   _**Afbeelding 44:** Data keeper synchrone mirroring voor de SAP ASCS/SCS-share schijf is actief_

   Failoverclusterbeheer wordt nu de schijf weer gegeven als een Data keeper-schijf, zoals wordt weer gegeven in afbeelding 45:

   ![Afbeelding 45: Failoverclusterbeheer toont de schijf die data keeper gerepliceerd][sap-ha-guide-figure-3045]

   _**Afbeelding 45:** Failoverclusterbeheer toont de schijf die data keeper gerepliceerd_

## <a name="next-steps"></a>Volgende stappen

* [SAP NetWeaver HA installeren met behulp van een Windows-failovercluster en een gedeelde schijf voor een SAP ASCS/SCS-exemplaar][sap-high-availability-installation-wsfc-shared-disk]
