---
title: Azure VM's HA-architectuur en -scenario's voor SAP NetWeaver | Microsoft Documenten
description: Architectuur en scenario's met hoge beschikbaarheid voor SAP NetWeaver op Azure Virtual Machines
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08f770ced6cb1ec1102159788e1583d481436b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279908"
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Architectuur en scenario's met hoge beschikbaarheid voor SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


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
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-suse-ascs-ha-anf]:high-availability-guide-suse-netapp-files.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:https://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:https://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability
[azure-storage-redundancy]:https://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

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


## <a name="terminology-definitions"></a>Terminologiedefinities

**Hoge beschikbaarheid**: verwijst naar een reeks technologieën die IT-storingen minimaliseren door bedrijfscontinuïteit van IT-services te bieden door redundante, fouttolerante of failoverbeveiligde componenten in *hetzelfde* datacenter. In ons geval bevindt het datacenter zich binnen één Azure-regio.

**Disaster recovery**: Verwijst ook naar het minimaliseren van IT-diensten verstoring en hun herstel, maar over *verschillende* datacenters die honderden mijlen afstand van elkaar zou kunnen zijn. In ons geval bevinden de datacenters zich mogelijk in verschillende Azure-regio's binnen dezelfde geopolitieke regio of op locaties die door u als klant zijn vastgesteld.


## <a name="overview-of-high-availability"></a>Overzicht van hoge beschikbaarheid
Sap hoge beschikbaarheid in Azure kan worden onderverdeeld in drie typen:

* **Azure-infrastructuur hoge beschikbaarheid:** 

    Hoge beschikbaarheid kan bijvoorbeeld compute (VM's), netwerk of opslag omvatten en de voordelen ervan voor het vergroten van de beschikbaarheid van SAP-toepassingen.

* **Gebruik makend van Azure-infrastructuur VM opnieuw opstarten om *een hogere beschikbaarheid* van SAP-toepassingen te bereiken:** 

    Als u besluit geen functionaliteiten zoals Windows Server Failover Clustering (WSFC) of Pacemaker op Linux te gebruiken, wordt Azure VM opnieuw opstarten gebruikt. Het beschermt SAP-systemen tegen geplande en ongeplande downtime van de fysieke serverinfrastructuur van Azure en het totale onderliggende Azure-platform.

* **SAP applicatie hoge beschikbaarheid:** 

    Om een volledige beschikbaarheid van het SAP-systeem te bereiken, moet u alle kritieke SAP-systeemcomponenten beschermen. Bijvoorbeeld:
    * Redundante SAP-toepassingsservers.
    * Unieke componenten. Een voorbeeld hiervan is een SPOF-component (single point of failure), zoals een SAP ASCS/SCS-instantie of een databasebeheersysteem (DBMS).

Sap hoge beschikbaarheid in Azure verschilt van SAP hoge beschikbaarheid in een on-premises fysieke of virtuele omgeving. De volgende papieren [SAP NetWeaver hoge beschikbaarheid en bedrijfscontinuïteit in virtuele omgevingen met VMware en Hyper-V op Microsoft Windows][sap-ha-bc-virtual-env-hyperv-vmware-white-paper] beschrijft standaard SAP-configuraties met hoge beschikbaarheid in gevirtualiseerde omgevingen op Windows.

Er is geen sapinst-geïntegreerde SAP high-availability configuratie voor Linux als er is voor Windows. Zie Partnerinformatie met [hoge beschikbaarheid][sap-ha-partner-information]voor informatie over de beschikbaarheid van SAP-beschikbaarheid on-premises voor Linux.

## <a name="azure-infrastructure-high-availability"></a>Azure-infrastructuur hoge beschikbaarheid

### <a name="sla-for-single-instance-virtual-machines"></a>SLA voor virtuele machines met één instantie

Er is momenteel een single-VM SLA van 99,9% met premium opslag. Als u een idee wilt krijgen over wat de beschikbaarheid van één virtuele machine zou kunnen zijn, u het product van de verschillende beschikbare [Azure Service Level Agreements][azure-sla]bouwen.

De basis voor de berekening is 30 dagen per maand, of 43.200 minuten. Een downtime van 0,05% komt bijvoorbeeld overeen met 21,6 minuten. Zoals gebruikelijk wordt de beschikbaarheid van de verschillende diensten als volgt berekend:

(Availability Service #1/100) * (Availability Service #2/100) * (Availability Service #3/100) \*...

Bijvoorbeeld:

(99.95/100) * (99.9/100) * (99.9/100) = 0.9975 of een totale beschikbaarheid van 99.75%.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Meerdere exemplaren van virtuele machines in dezelfde beschikbaarheidsset
Voor alle virtuele machines die twee of meer exemplaren hebben geïmplementeerd in dezelfde *beschikbaarheidsset,* garanderen we dat u ten minste 99,95% van de tijd een virtuele machine-connectiviteit hebt.

Wanneer twee of meer VM's deel uitmaken van dezelfde beschikbaarheidsset, krijgt elke virtuele machine in de beschikbaarheidsset een *updatedomein* en een *foutdomein* toegewezen door het onderliggende Azure-platform.

* **Updatedomeinen** garanderen dat meerdere VM's niet tegelijkertijd opnieuw worden opgestart tijdens het geplande onderhoud van een Azure-infrastructuur. Er wordt slechts één VM tegelijk opnieuw opgestart.

* **Foutdomeinen** garanderen dat VM's worden geïmplementeerd op hardwarecomponenten die geen gemeenschappelijke stroombron en netwerkswitch delen. Wanneer servers, een netwerkswitch of een stroombron een ongeplande uitvaltijd ondergaan, wordt slechts één VM beïnvloed.

Zie [De beschikbaarheid van virtuele Windows-machines beheren in Azure][azure-virtual-machines-manage-availability]voor meer informatie.

Een beschikbaarheidsset wordt gebruikt voor het bereiken van een hoge beschikbaarheid van:

* Redundante SAP-toepassingsservers.  
* Clusters met twee of meer knooppunten (VM's, bijvoorbeeld) die SPOF's beschermen, zoals een SAP ASCS/SCS-exemplaar of een DBMS.


### <a name="azure-availability-zones"></a>Azure-beschikbaarheidszones
Azure is bezig met het uitrollen van een concept van [Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview) in verschillende [Azure-regio's.](https://azure.microsoft.com/global-infrastructure/regions/) In Azure-regio's waar beschikbaarheidszones worden aangeboden, hebben de Azure-regio's meerdere datacenters, die onafhankelijk zijn in de levering van stroombronnen, koeling en netwerk. Reden voor het aanbieden van verschillende zones binnen één Azure-regio is om u in staat te stellen toepassingen te implementeren in twee of drie aangeboden beschikbaarheidszones. Ervan uitgaande dat problemen in energiebronnen en/of netwerk alleen van invloed zijn op één availability zone-infrastructuur, is de implementatie van uw toepassing binnen een Azure-regio nog steeds volledig functioneel. Uiteindelijk met een aantal verminderde capaciteit, omdat sommige VM's in een zone zou kunnen worden verloren. Maar VM's in de andere twee zones zijn nog steeds operationeel. De Azure-regio's die zones aanbieden, worden weergegeven in [Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview).

Met behulp van beschikbaarheidszones zijn er een aantal dingen om rekening mee te houden. De overwegingen lijst als:

- U Azure Availability Sets niet implementeren binnen een beschikbaarheidszone. U moet een beschikbaarheidszone of een beschikbaarheidsset kiezen als implementatieframe voor een vm.
- U de [Basic Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) niet gebruiken om failoverclusteroplossingen te maken op basis van Windows Failover Cluster Services of Linux Pacemaker. In plaats daarvan moet u de [SKU van Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) gebruiken
- Azure Availability Zones geven geen garanties voor bepaalde afstand tussen de verschillende zones binnen één regio
- De netwerklatentie tussen verschillende Azure Availability Zones binnen de verschillende Azure-regio's kan verschillen van Azure-regio tot regio. Er zullen gevallen zijn waarin u als klant de SAP-toepassingslaag die in verschillende zones wordt geïmplementeerd redelijkerwijs uitvoeren, aangezien de netwerklatentie van de ene zone naar de actieve DBMS VM nog steeds acceptabel is vanuit een effect op een bedrijfsproces. Terwijl er klantscenario's zijn waarbij de latentie tussen de actieve DBMS VM in de ene zone en een SAP-toepassingsinstantie in een VM in een andere zone te opdringerig en niet acceptabel kan zijn voor de SAP-bedrijfsprocessen. Als gevolg hiervan moeten de implementatiearchitecturen verschillend zijn met een actieve/actieve architectuur voor de toepassing of actieve/passieve architectuur als de latentie te hoog is.
- Het gebruik van [Azure managed disks](https://azure.microsoft.com/services/managed-disks/) is verplicht voor implementatie in Azure Availability Zones 


### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>Gepland en ongepland onderhoud van virtuele machines

Twee typen Azure-platformgebeurtenissen kunnen van invloed zijn op de beschikbaarheid van uw virtuele machines:

* **Geplande onderhoudsgebeurtenissen** zijn periodieke updates die door Microsoft zijn uitgevoerd voor het onderliggende Azure-platform. De updates verbeteren de algehele betrouwbaarheid, prestaties en beveiliging van de platforminfrastructuur waarop uw virtuele machines draaien.

* **Ongeplande onderhoudsgebeurtenissen** treden op wanneer de hardware of fysieke infrastructuur die ten grondslag ligt aan uw virtuele machine op de een of andere manier is mislukt. Het kan lokale netwerkstoringen, lokale schijfstoringen of andere fouten op rackniveau omvatten. Wanneer een dergelijke fout wordt gedetecteerd, migreert het Azure-platform uw virtuele machine automatisch van de ongezonde fysieke server die uw virtuele machine host naar een gezonde fysieke server. Dergelijke gebeurtenissen zijn zeldzaam, maar ze kunnen er ook voor zorgen dat uw virtuele machine opnieuw wordt opgestart.

Zie [De beschikbaarheid van virtuele Windows-machines beheren in Azure][azure-virtual-machines-manage-availability]voor meer informatie.

### <a name="azure-storage-redundancy"></a>Redundantie azure-opslag
De gegevens in uw opslagaccount worden altijd gerepliceerd om duurzaamheid en hoge beschikbaarheid te garanderen, zodat u voldoet aan de SLA van Azure Storage, zelfs in het licht van tijdelijke hardwarefouten.

Omdat Azure Storage standaard drie afbeeldingen van de gegevens bewaart, is het gebruik van RAID 5 of RAID 1 over meerdere Azure-schijven niet nodig.

Zie [Azure Storage-replicatie][azure-storage-redundancy]voor meer informatie .

### <a name="azure-managed-disks"></a>Azure Managed Disks
Beheerde schijven is een brontype in Azure Resource Manager dat wordt aanbevolen om te worden gebruikt in plaats van virtuele harde schijven (VHD's) die zijn opgeslagen in Azure-opslagaccounts. Beheerde schijven sluiten zich automatisch aan op een Azure-beschikbaarheidsset van de virtuele machine waaraan ze zijn gekoppeld. Ze verhogen de beschikbaarheid van uw virtuele machine en de services die erop worden uitgevoerd.

Zie [overzicht van Azure Managed Disks][azure-storage-managed-disks-overview]voor meer informatie.

We raden u aan beheerde schijven te gebruiken omdat deze de implementatie en het beheer van uw virtuele machines vereenvoudigen.



## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Gebruik maken van Azure-infrastructuur met hoge beschikbaarheid om *een hogere beschikbaarheid* van SAP-toepassingen te bereiken

Als u besluit functionaliteiten zoals WSFC of Pacemaker op Linux niet te gebruiken (momenteel alleen ondersteund voor SUSE Linux Enterprise Server [SLES] 12 en hoger), wordt Azure VM opnieuw opgestart. Het beschermt SAP-systemen tegen geplande en ongeplande downtime van de fysieke serverinfrastructuur van Azure en het totale onderliggende Azure-platform.

Zie [Azure-infrastructuur opnieuw opstarten om een hogere beschikbaarheid van het SAP-systeem te bereiken voor][sap-higher-availability]meer informatie over deze aanpak.

## <a name="high-availability-of-sap-applications-on-azure-iaas"></a><a name="baed0eb3-c662-4405-b114-24c10a62954e"></a>Hoge beschikbaarheid van SAP-toepassingen op Azure IaaS

Om een volledige beschikbaarheid van het SAP-systeem te bereiken, moet u alle kritieke SAP-systeemcomponenten beschermen. Bijvoorbeeld:
  * Redundante SAP-toepassingsservers.
  * Unieke componenten. Een voorbeeld hiervan is een SPOF-component (single point of failure), zoals een SAP ASCS/SCS-instantie of een databasebeheersysteem (DBMS).

In de volgende secties wordt besproken hoe u een hoge beschikbaarheid bereiken voor alle drie de kritieke SAP-systeemcomponenten.

### <a name="high-availability-architecture-for-sap-application-servers"></a>Architectuur met hoge beschikbaarheid voor SAP-toepassingsservers

> Deze sectie is van toepassing op:
>
> ![Windows][Logo_Windows] Windows en ![Linux][Logo_Linux] Linux
>

U hebt meestal geen specifieke oplossing met hoge beschikbaarheid nodig voor de SAP-toepassingsserver en dialoogvensters. U bereikt een hoge beschikbaarheid door redundantie en u configureert meerdere dialoogvensters in verschillende exemplaren van virtuele Azure-machines. U moet ten minste twee SAP-toepassingsexemplaren hebben geïnstalleerd in twee exemplaren van virtuele Azure-machines.

![Figuur 1: SAP-toepassingsserver met hoge beschikbaarheid][sap-ha-guide-figure-2000]

_**Figuur 1:** Sap-toepassingsserver met hoge beschikbaarheid_

U moet alle virtuele machines die SAP-toepassingsserverexemplaren hosten, in dezelfde Azure-beschikbaarheidsset plaatsen. Een Azure-beschikbaarheidsset zorgt ervoor dat:

* Alle virtuele machines maken deel uit van hetzelfde updatedomein.  
    Een updatedomein zorgt ervoor dat de virtuele machines niet tegelijkertijd worden bijgewerkt tijdens geplande onderhoudsdowntime.

    De basisfunctionaliteit, die voortbouwt op verschillende update- en foutdomeinen binnen een Azure-schaaleenheid, werd al geïntroduceerd in de sectie [updatedomeinen.][planning-guide-3.2.2]

* Alle virtuele machines maken deel uit van hetzelfde storingsdomein.  
    Een foutdomein zorgt ervoor dat virtuele machines worden geïmplementeerd, zodat er geen enkel storingspunt van invloed is op de beschikbaarheid van alle virtuele machines.

Het aantal update- en foutdomeinen dat kan worden gebruikt door een Azure-beschikbaarheidsset binnen een Azure-schaaleenheid is eindig. Als u VM's blijft toevoegen aan één beschikbaarheidsset, komen twee of meer VM's uiteindelijk in hetzelfde fout- of updatedomein terecht.

Als u een paar SAP-toepassingsserver-exemplaren implementeert in hun speciale VM's, ervan uitgaande dat we vijf updatedomeinen hebben, verschijnt het volgende beeld. Het werkelijke maximumaantal update- en foutdomeinen binnen een beschikbaarheidsset kan in de toekomst veranderen:

![Figuur 2: Hoge beschikbaarheid van SAP-toepassingsservers][planning-guide-figure-3000]
in een Azure-beschikbaarheidsset_**Figuur 2:** Hoge beschikbaarheid van SAP-toepassingsservers in een Azure-beschikbaarheidsset_

Zie [De beschikbaarheid van virtuele Windows-machines beheren in Azure][azure-virtual-machines-manage-availability]voor meer informatie.

Zie het gedeelte [Azure-beschikbaarheidssets][planning-guide-3.2.3] van de azure virtual machines planning en implementatie voor SAP NetWeaver-document voor meer informatie.

**Alleen niet-beheerde schijven:** Omdat het Azure-opslagaccount een mogelijk single point of failure is, is het belangrijk om ten minste twee Azure-opslagaccounts te hebben, waarin ten minste twee virtuele machines worden gedistribueerd. In een ideale installatie worden de schijven van elke virtuele machine waarop een SAP-dialoogvenster wordt uitgevoerd, geïmplementeerd in een ander opslagaccount.

> [!IMPORTANT]
> We raden u ten zeerste aan azure managed disks te gebruiken voor uw INSTALLATIES met hoge beschikbaarheid van SAP. Omdat beheerde schijven automatisch overeenkomen met de beschikbaarheidsset van de virtuele machine waaraan ze zijn gekoppeld, verhogen ze de beschikbaarheid van uw virtuele machine en de services die erop worden uitgevoerd.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Architectuur met hoge beschikbaarheid voor een SAP ASCS/SCS-exemplaar op Windows

> ![Windows][Logo_Windows] Windows
>

U een WSFC-oplossing gebruiken om het SAP ASCS/SCS-exemplaar te beschermen. De oplossing heeft twee varianten:

* **Cluster de INSTANTIE SAP ASCS/SCS met geclusterde gedeelde schijven:** Zie [Een SAP ASCS/SCS-exemplaar clusteren op een Windows-failovercluster voor meer][sap-high-availability-guide-wsfc-shared-disk]informatie over deze architectuur met behulp van een gedeelde clusterschijf.   

* **Cluster de INSTANTIE SAP ASCS/SCS met bestandsshare:** Zie [Een SAP ASCS/SCS-exemplaar clusteren op een Windows-failovercluster met behulp van bestandsshare][sap-high-availability-guide-wsfc-file-share]voor meer informatie over deze architectuur.

* **Cluster de INSTANTIE SAP ASCS/SCS met ANF SMB-share:** Zie [Clustercluster een SAP ASCS/SCS-exemplaar op een Windows-failovercluster met ANF SMB-bestandsshare](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)voor meer informatie over deze architectuur.

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Architectuur met hoge beschikbaarheid voor een SAP ASCS/SCS-exemplaar op Linux

> ![Linux][Logo_Linux] Linux
> 
> Zie [Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SUSE Linux Enterprise Server voor SAP-toepassingen voor][sap-suse-ascs-ha]meer informatie over het clusteren van het SAP ASCS/SCS-exemplaar. Voor alternatieve HA-architectuur op SLES, waarvoor geen zeer beschikbare NFS vereist, wordt [de handleiding voor hoge beschikbaarheid voor SAP NetWeaver op SUSE Linux Enterprise Server met Azure NetApp-bestanden voor SAP-toepassingen weergegeven.][sap-suse-ascs-ha-anf]

Zie [Azure Virtual Machines hoge beschikbaarheid voor SAP NetWeaver op Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) voor meer informatie over het clusteren van het SAP ASCS/SCS met behulp van het Red Hat-clusterframework.


### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>SAP NetWeaver multi-SID-configuratie voor een geclusterd SAP ASCS/SCS-exemplaar

> ![Windows][Logo_Windows] Windows
> 
> Multi-SID wordt ondersteund met WSFC, met behulp van bestandsshare en gedeelde schijf.
> 
> Zie voor meer informatie over architectuur met hoge beschikbaarheid met hoge SID op Windows:

* [SAP ASCS/SCS-instantie multi-SID hoge beschikbaarheid voor Windows Server Failover Clustering en bestandsshare][sap-ascs-ha-multi-sid-wsfc-file-share]

* [SAP ASCS/SCS-instantie multi-SID hoge beschikbaarheid voor Windows Server Failover Clustering en gedeelde schijf][sap-ascs-ha-multi-sid-wsfc-shared-disk]

> ![Linux][Logo_Linux] Linux
> 
> Multi-SID clustering wordt ondersteund op Linux Pacemaker clusters voor SAP ASCS/ERS, beperkt tot **vijf** SAP SID's op hetzelfde cluster.
> Voor meer informatie over multi-SID high-availability architectuur op Linux, zie:

* [HA voor SAP NW op Azure VM's op SLES voor SAP-toepassingen multi-SID-handleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
* [HA voor SAP NW op Azure VM's op RHEL voor SAP-toepassingen multi-SID-handleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)

### <a name="high-availability-dbms-instance"></a>DbMS-exemplaar met hoge beschikbaarheid

De DBMS is ook een enkel aanspreekpunt in een SAP-systeem. U moet het beschermen door een oplossing met hoge beschikbaarheid te gebruiken. De volgende afbeelding toont een SQL Server AlwaysOn oplossing met hoge beschikbaarheid in Azure, met Windows Server Failover Clustering en de interne load balancer van Azure. SQL Server AlwaysOn repliceert DBMS-gegevens en logbestanden met behulp van een eigen DBMS-replicatie. In dit geval hebt u geen gedeelde clusterschijf nodig, wat de hele installatie vereenvoudigt.

![Figuur 3: Voorbeeld van een SAP DBMS met hoge beschikbaarheid, met SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**Figuur 3:** Voorbeeld van een SAP DBMS met hoge beschikbaarheid, met SQL Server AlwaysOn_

Zie de volgende artikelen voor meer informatie over het clusteren van SQL Server DBMS in Azure met behulp van het Azure Resource Manager-implementatiemodel:

* [Een beschikbaarheidsgroep AlwaysOn in Azure virtuele machines handmatig configureren met Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Een Azure internal load balancer configureren voor een AlwaysOn-beschikbaarheidsgroep in Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

Zie [Hoge beschikbaarheid van SAP HANA op Azure virtual machines (VM's)][sap-hana-ha]voor meer informatie over het clusteren van SAP HANA DBMS in Azure met behulp van het Azure Resource Manager-implementatiemodel.
