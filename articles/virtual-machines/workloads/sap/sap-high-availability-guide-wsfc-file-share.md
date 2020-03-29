---
title: Cluster SAP ASCS/SCS op WSFC met bestandsshare in Azure | Microsoft Documenten
description: Meer informatie over het clusteren van een SAP ASCS/SCS-exemplaar op een Windows-failovercluster met behulp van een bestandsshare in Azure.
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
ms.date: 07/24/2019
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 545bcd1fa521b945d822b7eb69945cf381bf480a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918662"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:../../dv2-dsv2-series.md
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general

[sap-installation-guides]:http://service.sap.com/instguides

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
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

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


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

[1869038]:https://launchpad.support.sap.com/#/notes/1869038 

# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-file-share-in-azure"></a>Een SAP ASCS/SCS-exemplaar clusteren op een Windows-failovercluster met behulp van een bestandsshare in Azure

> ![Windows][Logo_Windows] Windows
>

Windows Server failover clustering is de basis van een hoge beschikbaarheid SAP ASCS / SCS installatie en DBMS in Windows.

Een failovercluster is een groep van 1+n onafhankelijke servers (knooppunten) die samenwerken om de beschikbaarheid van toepassingen en services te vergroten. Als er een node-fout optreedt, berekent windows Server-failoverclustering het aantal fouten dat kan optreden en behoudt het nog steeds een gezond cluster om toepassingen en services te leveren. U kiezen uit verschillende quorummodi om failoverclustering te bereiken.

## <a name="prerequisites"></a>Vereisten
Voordat u begint met de taken die in dit artikel worden beschreven, controleert u dit artikel:

* [Azure Virtual Machines met hoge beschikbaarheid en scenario's voor SAP NetWeaver][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
> Het clusteren van SAP ASCS/SCS-exemplaren met behulp van een bestandsshare wordt ondersteund voor SAP NetWeaver 7.40 (en hoger), met SAP Kernel 7.49 (en hoger).
>


## <a name="windows-server-failover-clustering-in-azure"></a>Windows Server failover clustering in Azure

In vergelijking met bare-metal- of private cloudimplementaties vereist Azure Virtual Machines extra stappen om windows server-failoverclustering te configureren. Wanneer u een cluster bouwt, moet u verschillende IP-adressen en virtuele hostnamen instellen voor het exemplaar SAP ASCS/SCS.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Naamresolutie in Azure en de naam van de virtuele clusterhost

Het Azure-cloudplatform biedt niet de optie om virtuele IP-adressen te configureren, zoals zwevende IP-adressen. U hebt een alternatieve oplossing nodig om een virtueel IP-adres in te stellen om de clusterbron in de cloud te bereiken. 

De Azure Load Balancer-service biedt een *interne load balancer* voor Azure. Met de interne load balancer bereiken clients het cluster via het virtuele IP-adres van het cluster. 

Implementeer de interne load balancer in de resourcegroep die de clusterknooppunten bevat. Configureer vervolgens alle benodigde poortdoorstuurregels met behulp van de sondepoortenpoorten van de interne load balancer. De clients kunnen verbinding maken via de naam van de virtuele host. De DNS-server lost het IP-adres van het cluster op. De interne load balancer verwerkt poortdoorsturen naar het actieve knooppunt van het cluster.

![Figuur 1: Configuratie van failoverclustering van Windows Server in Azure zonder gedeelde schijf][sap-ha-guide-figure-1001]

_**Figuur 1:** Configuratie van windows Server-failoverclustering in Azure zonder gedeelde schijf_

## <a name="sap-ascsscs-ha-with-file-share"></a>SAP ASCS/SCS HA met bestandsshare

SAP ontwikkelde een nieuwe aanpak en een alternatief voor gedeelde clusterschijven voor het clusteren van een SAP ASCS/SCS-exemplaar op een Windows-failovercluster. In plaats van gedeelde clusterschijven te gebruiken, u een SMB-bestandsshare gebruiken om algemene gehostbestanden van SAP te implementeren.

> [!NOTE]
> Een SMB-bestandsshare is een alternatief voor het gebruik van gedeelde clusterschijven voor het clusteren van SAP ASCS/SCS-exemplaren.  
>

Deze architectuur is specifiek op de volgende manieren:

* SAP centrale diensten (met een eigen bestandsstructuur en berichten- en wachtrijprocessen) staan los van de SAP global host-bestanden.
* SAP centrale diensten worden uitgevoerd onder een SAP ASCS/SCS-exemplaar.
* Sap ASCS/SCS-exemplaar is geclusterd \<en is toegankelijk met\> de virtuele hostnaam ASCS/SCS.
* SAP global files worden geplaatst op het mkb-bestand \<delen en\> worden \\ \\ &lt;benaderd met&gt;behulp van\\&lt;&gt;de\.SAP global host host naam: SAP global host \sapmnt SID \SYS ..
* De instantie SAP ASCS/SCS is geïnstalleerd op een lokale schijf op beide clusterknooppunten.
* De \<naam van het virtuele\> hostnetwerk van &lt;ASCS/SCS verschilt van de wereldwijde host&gt;van SAP.

![Figuur 2: SAP ASCS/SCS HA-architectuur met mkb-bestandsshare][sap-ha-guide-figure-8004]

_**Figuur 2:** Nieuwe SAP ASCS/SCS HA-architectuur met een smb-bestandsaandeel_

Vereisten voor een aandeel van een MKB-bestand:

* SMB 3.0 (of hoger) protocol.
* Mogelijkheid om Active Directory access control lists (ACL's) in te stellen voor Active Directory-gebruikersgroepen en het `computer$` computerobject.
* De bestandsshare moet HA-ingeschakeld zijn:
    * Schijven die worden gebruikt om bestanden op te slaan, mogen geen enkel storingspunt zijn.
    * Server- of VM-downtime leidt niet tot downtime in het bestandsshare.

De \<SAP\> SID-clusterrol bevat geen gedeelde clusterschijven of een algemene clusterbron voor bestandsshare.


![Figuur 3: \<\> SAP SID-clusterrolbronnen voor het gebruik van een bestandsshare][sap-ha-guide-figure-8005]

_**Figuur 3:** SAP &lt;&gt; SID-clusterrolbronnen voor het gebruik van een bestandsshare_


## <a name="scale-out-file-shares-with-storage-spaces-direct-in-azure-as-an-sapmnt-file-share"></a>Schaal-out bestandsshares met Storage Spaces Direct in Azure als SAPMNT-bestandsshare

U een scale-out bestandsshare gebruiken om sap-globale hostbestanden te hosten en te beveiligen. Een scale-out bestandsshare biedt ook een zeer beschikbare SAPMNT file share service.

![Figuur 4: Scale-out bestandsshare gebruikt om SAP global host files te beschermen][sap-ha-guide-figure-8006]

_**Figuur 4:** Een scale-out bestandsshare dat wordt gebruikt om sap-globale hostbestanden te beschermen_

> [!IMPORTANT]
> Scale-out bestandsshares worden volledig ondersteund in de Microsoft Azure-cloud en in on-premises omgevingen.
>

Een scale-out bestandsshare biedt een zeer beschikbare en horizontaal schaalbare SAPMNT-bestandsshare.

Storage Spaces Direct wordt gebruikt als gedeelde schijf voor een scale-out bestandsshare. U Storage Spaces Direct gebruiken om zeer beschikbare en schaalbare opslag te bouwen met behulp van servers met lokale opslag. Gedeelde opslag die wordt gebruikt voor een scale-out bestandsshare, zoals voor SAP global host-bestanden, is geen enkel storingspunt.

Houd bij het kiezen van Direct aan opslagruimten rekening met deze use cases:

- De virtuele machines die worden gebruikt om het cluster Direct voor opslagruimten te bouwen, moeten worden geïmplementeerd in een Azure-beschikbaarheidsset.
- Voor noodherstel van een direct cluster voor opslagruimten u [Azure Site Recovery Services](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#replicated-machines---storage)gebruiken.
- Het wordt niet ondersteund om het storage space direct-cluster uit te rekken over verschillende Azure Availability Zones.

### <a name="sap-prerequisites-for-scale-out-file-shares-in-azure"></a>SAP-vereisten voor scale-out bestandsshares in Azure

Als u een scale-out bestandsshare wilt gebruiken, moet uw systeem aan de volgende vereisten voldoen:

* Ten minste twee clusterknooppunten voor een scale-out bestandsshare.
* Elk knooppunt moet ten minste twee lokale schijven hebben.
* Om prestatieredenen moet u *spiegelende tolerantie*gebruiken:
    * Tweerichtingsspiegeling voor een scale-out bestandsshare met twee clusterknooppunten.
    * Driewegspiegeling voor een scale-out bestandsshare met drie (of meer) clusterknooppunten.
* We raden drie (of meer) clusterknooppunten aan voor een scale-out bestandsshare, met drie-weg spiegeling.
    Deze instelling biedt meer schaalbaarheid en meer opslagtolerantie dan de scale-out bestandsshare-installatie met twee clusterknooppunten en tweerichtingsmirroring.
* U moet Azure Premium-schijven gebruiken.
* We raden u aan Azure Managed Disks te gebruiken.
* We raden u aan volumes op te maken met behulp van Resilient File System (ReFS).
    * Zie [SAP Note 1869038 - SAP-ondersteuning voor ReFs-bestandssysteem][1869038] en het [hoofdstuk Van het bestandssysteem][planning-volumes-s2d-choosing-filesystem] kiezen van het artikel Planning volumes in Storage Spaces Direct voor meer informatie.
    * Zorg ervoor dat u [de cumulatieve update van Microsoft KB4025334 installeert.][kb4025334]
* U Azure VM-formaten uit de DS-serie of DSv2-serie gebruiken.
* Voor goede netwerkprestaties tussen VM's, die nodig zijn voor direct schijfsynchronisatie met opslagruimten, gebruikt u een VM-type dat ten minste een "hoge" netwerkbandbreedte heeft.
    Zie voor meer informatie de specificaties van de [DSv2-serie][dv2-series] en [DS-serie.][ds-series]
* We raden u aan een aantal niet-toegewezen capaciteit in de opslaggroep te reserveren. Het verlaten van een aantal niet-toegewezen capaciteit in de opslag pool geeft volumes ruimte om te repareren "op zijn plaats" als een station uitvalt. Dit verbetert de veiligheid en prestaties van gegevens.  Zie [Volumegrootte kiezen][choosing-the-size-of-volumes-s2d]voor meer informatie .
* U hoeft de interne load balancer van Azure niet te configureren voor de \<naam\>van het scale-out netwerk voor bestandsshare, zoals voor SAP global host. Dit wordt gedaan \<voor de virtuele hostnaam\> ASCS/SCS van het SAP ASCS/SCS-exemplaar of voor het DBMS. Een scale-out bestandsshare schaalt de belasting op alle clusterknooppunten. \<SAP global\> host gebruikt het lokale IP-adres voor alle clusterknooppunten.


> [!IMPORTANT]
> U de naam van de SAPMNT-bestandsshare, die verwijst naar \<SAP global host,\>niet wijzigen. SAP ondersteunt alleen de naam 'sapmnt'.
>
> Zie [SAP Note 2492395 - Kan de naam sapmnt van het aandeel worden gewijzigd voor][2492395] meer informatie?

### <a name="configure-sap-ascsscs-instances-and-a-scale-out-file-share-in-two-clusters"></a>SAP ASCS/SCS-exemplaren configureren en een scale-out bestandsshare in twee clusters

U SAP ASCS/SCS-exemplaren implementeren in \<één\> cluster, met hun eigen SAP SID-clusterrol. In dit geval configureert u de scale-out bestandsshare op een ander cluster, met een andere clusterrol.

> [!IMPORTANT]
>In dit scenario is het exemplaar SAP ASCS/SCS geconfigureerd om toegang \\ \\ &lt;te&gt;krijgen tot\\&lt;de&gt;globale SAP-host met behulp van UNC-pad SAP global host \sapmnt SID \SYS\.
>

![Figuur 5: SAP ASCS/SCS-exemplaar en een scale-out bestandsshare geïmplementeerd in twee clusters][sap-ha-guide-figure-8007]

_**Figuur 5:** Een SAP ASCS/SCS-exemplaar en een scale-out bestandsshare geïmplementeerd in twee clusters_

> [!IMPORTANT]
> In de Azure-cloud moet elk cluster dat wordt gebruikt voor SAP- en scale-outbestandsshares worden geïmplementeerd in de eigen Azure-beschikbaarheidsset of in Azure Availability Zones. Dit zorgt voor gedistribueerde plaatsing van de clusterVM's over de onderliggende Azure-infrastructuur. Beschikbaarheid Zone-implementaties worden ondersteund met deze technologie.
>

## <a name="generic-file-share-with-sios-datakeeper-as-cluster-shared-disks"></a>Algemene bestandsshare met SIOS DataKeeper als gedeelde clusterschijven


Een generieke bestandsshare is een andere optie voor het bereiken van een zeer beschikbare bestandsshare.

In dit geval u een SIOS-oplossing van derden gebruiken als gedeelde clusterschijf.

## <a name="next-steps"></a>Volgende stappen

* [De Azure-infrastructuur voor SAP HA voorbereiden met behulp van een Windows-failovercluster en bestandsshare voor een SAP ASCS/SCS-exemplaar][sap-high-availability-infrastructure-wsfc-file-share]
* [SAP NetWeaver HA installeren op een Windows-failovercluster en bestandsshare voor een SAP ASCS/SCS-exemplaar][sap-high-availability-installation-wsfc-shared-disk]
* [Een direct uitschalende bestandsserver met twee nodes openen voor UPD-opslag in Azure][deploy-sofs-s2d-in-azure]
* [Opslagruimten Direct in Windows Server 2016][s2d-in-win-2016]
* [Diepe duik: Volumes in Storage Spaces Direct][deep-dive-volumes-in-s2d]
