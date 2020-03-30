---
title: 'SAP op Azure: plannings- en implementatiehandleiding'
description: Azure Virtual Machines planning en implementatie voor SAP NetWeaver
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7ddcc5165f5588ff9015d7fafbc2b822268ffea7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337168"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Azure Virtual Machines planning en implementatie voor SAP NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-Azvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-az-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-windows.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image-resource-manager]:../../windows/capture-image.md
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-create-upload-vhd-oracle]:../../linux/oracle-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics-windows]:../../windows/multiple-nics.md
[virtual-networks-multiple-nics-linux]:../../linux/multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-about-vpngateways.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image



Microsoft Azure stelt bedrijven in staat om reken- en opslagbronnen in minimale tijd te verwerven zonder lange inkoopcycli. Azure Virtual Machine-service stelt bedrijven in staat om klassieke toepassingen te implementeren, zoals op SAP NetWeaver gebaseerde toepassingen in Azure en hun betrouwbaarheid en beschikbaarheid uit te breiden zonder dat er meer on-premises resources beschikbaar zijn. Azure Virtual Machine Services ondersteunt ook cross-premises connectiviteit, waarmee bedrijven Azure Virtual Machines actief kunnen integreren in hun on-premises domeinen, hun Private Clouds en hun SAP-systeemlandschap.
Deze whitepaper beschrijft de fundamenten van Microsoft Azure Virtual Machine en biedt een doorloop van plannings- en implementatieoverwegingen voor SAP NetWeaver-installaties in Azure en moet als zodanig het document zijn dat moet worden gelezen voordat de werkelijke implementaties van SAP NetWeaver op Azure.
Het document vormt een aanvulling op de SAP-installatiedocumentatie en SAP Notes, die de belangrijkste bronnen vertegenwoordigen voor installaties en implementaties van SAP-software op bepaalde platforms.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>Samenvatting
Cloud Computing is een veelgebruikte term, die steeds belangrijker wordt binnen de IT-industrie, van kleine bedrijven tot grote en multinationale bedrijven.

Microsoft Azure is het Cloud Services Platform van Microsoft, dat een breed scala aan nieuwe mogelijkheden biedt. Nu kunnen klanten toepassingen snel inrichten en de-provisionen als een service in de cloud, zodat ze zich niet beperken tot technische of budgetteringbeperkingen. In plaats van tijd en budget te investeren in hardware-infrastructuur, kunnen bedrijven zich richten op de toepassing, bedrijfsprocessen en de voordelen ervan voor klanten en gebruikers.

Met Microsoft Azure-services voor virtuele machines biedt Microsoft een uitgebreid IaaS-platform (Infrastructure as a Service). SAP NetWeaver-toepassingen worden ondersteund op virtuele Azure-machines (IaaS). In deze whitepaper wordt beschreven hoe u op SAP NetWeaver gebaseerde toepassingen binnen Microsoft Azure plannen en implementeren als het platform van keuze.

Het document zelf richt zich op twee belangrijke aspecten:

* Het eerste deel beschrijft twee ondersteunde implementatiepatronen voor SAP NetWeaver-gebaseerde toepassingen op Azure. Het beschrijft ook de algemene afhandeling van Azure met SAP-implementaties in het achterhoofd.
* In het tweede deel wordt de implementatie van de verschillende scenario's beschreven in het eerste deel beschreven.

Zie hoofdstuk [Bronnen][planning-guide-1.2] in dit document voor aanvullende bronnen.

### <a name="definitions-upfront"></a>Definities vooraf
In het hele document gebruiken we de volgende termen:

* IaaS: Infrastructure as a Service
* PaaS: Platform as a Service
* SaaS: Software as a Service
* SAP Component: een individuele SAP-toepassing zoals ECC, BW, Solution Manager of S/4HANA.  SAP-componenten kunnen worden gebaseerd op traditionele ABAP- of Java-technologieën of een niet-NetWeaver-toepassing, zoals Bedrijfsobjecten.
* SAP-omgeving: een of meer SAP-componenten die logisch zijn gegroepeerd om een bedrijfsfunctie uit te voeren, zoals Ontwikkeling, QAS, Training, DR of Productie.
* SAP Landscape: Deze term verwijst naar de volledige SAP-assets in het IT-landschap van een klant. Het SAP-landschap omvat alle productie- en niet-productieomgevingen.
* SAP-systeem: De combinatie van DBMS-laag en toepassingslaag van bijvoorbeeld een SAP ERP-ontwikkelingssysteem, SAP BW-testsysteem, SAP CRM-productiesysteem, enz. In Azure-implementaties wordt deze twee lagen niet ondersteund om deze twee lagen te verdelen tussen on-premises en Azure. Dit betekent dat een SAP-systeem on-premises wordt geïmplementeerd of wordt geïmplementeerd in Azure. U de verschillende systemen van een SAP-landschap echter implementeren in Azure of on-premises. U bijvoorbeeld de SAP CRM-ontwikkelings- en testsystemen implementeren in Azure, maar het on-premises SAP CRM-productiesysteem.
* Cross-premises of hybride: beschrijft een scenario waarin VM's worden geïmplementeerd op een Azure-abonnement met site-to-site-, multi-site- of ExpressRoute-connectiviteit tussen het on-premises datacenter(s) en Azure. In veelvoorkomende Azure-documentatie worden dit soort implementaties ook beschreven als cross-premises of hybride scenario's. De reden voor de verbinding is om on-premises domeinen, on-premises Active Directory/OpenLDAP en on-premises DNS uit te breiden naar Azure. Het on-premises landschap wordt uitgebreid naar de Azure-elementen van het abonnement. Na deze extensie kunnen de VM's deel uitmaken van het on-premises domein. Domeingebruikers van het on-premises domein hebben toegang tot de servers en kunnen services uitvoeren op die VM's (zoals DBMS-services). Communicatie en naamoplossing tussen VM's die on-premises en Azure geïmplementeerde VM's zijn geïmplementeerd, is mogelijk. Dit is de meest voorkomende en bijna exclusieve case die SAP-assets implementeert in Azure. Voor meer informatie, zie [dit][vpn-gateway-cross-premises-options] artikel en [dit][vpn-gateway-site-to-site-create].
* Azure Monitoring Extension, Enhanced Monitoring en Azure Extension for SAP: Beschrijf een en hetzelfde item. Het beschrijft een VM-extensie die door u moet worden geïmplementeerd om een aantal basisgegevens over de Azure-infrastructuur aan de SAP Host Agent te verstrekken. SAP in SAP-notities kan verwijzen naar het als Monitoring Extension of Enhanced monitoring. In Azure verwijzen we ernaar als **Azure Extension voor SAP.**

> [!NOTE]
> Cross-premises of hybride implementaties van SAP-systemen waarbij Azure Virtual Machines met SAP-systemen lid zijn van een on-premises domein worden ondersteund voor productie-SAP-systemen. Cross-premises of hybride configuraties worden ondersteund voor het implementeren van onderdelen of complete SAP-landschappen in Azure. Zelfs het uitvoeren van het volledige SAP-landschap in Azure vereist dat deze VM's deel uitmaken van on-premises domein en ADS/OpenLDAP. 
>
>



### <a name="resources"></a><a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Middelen
Het ingangspunt voor SAP-workload op Azure-documentatie vindt u [hier.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) Beginnend met dit ingangspunt vindt u vele artikelen die de onderwerpen van behandelen:

- SAP NetWeaver en Business One op Azure
- SAP DBMS-handleidingen voor verschillende DBMS-systemen in Azure
- Hoge beschikbaarheid en disaster recovery voor SAP-workload op Azure
- Specifieke richtlijnen voor het uitvoeren van SAP HANA op Azure
- Richtlijnen voor Azure HANA Large Instances voor de SAP HANA DBMS 


> [!IMPORTANT]
> Waar mogelijk wordt een link naar de verwijzende SAP-installatiegidsen of andere SAP-documentatie gebruikt (Referentie InstGuide-01, zie <http://service.sap.com/instguides>). Als het gaat om de vereisten, het installatieproces of de details van specifieke SAP-functionaliteit, moeten de SAP-documentatie en -gidsen altijd zorgvuldig worden gelezen, aangezien de Microsoft-documenten alleen betrekking hebben op specifieke taken voor SAP-software die in een Microsoft Azure Virtual Machine.
>
>

De volgende SAP Notes zijn gerelateerd aan het onderwerp SAP op Azure:

| Notitienummer | Titel |
| --- | --- |
| [1928533] |SAP-toepassingen op Azure: ondersteunde producten en grootte |
| [2015553] |SAP op Microsoft Azure: vereisten voor ondersteuning |
| [1999351] |Probleemoplossing voor verbeterde Azure-bewaking voor SAP |
| [2178632] |Belangrijkste monitoringstatistieken voor SAP op Microsoft Azure |
| [1409604] |Virtualisatie op Windows: verbeterde controle |
| [2191498] |SAP op Linux met Azure: verbeterde monitoring |
| [2243692] |Linux op Microsoft Azure (IaaS) VM: SAP-licentieproblemen |
| [1984787] |SUSE LINUX Enterprise Server 12: Installatienotities |
| [2002167] |Red Hat Enterprise Linux 7.x: Installatie en upgrade |
| [2069760] |Oracle Linux 7.x SAP Installatie en upgrade |
| [1597355] |Swap-space aanbeveling voor Linux |

Lees ook de [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) die alle SAP Notes voor Linux bevat.

Algemene standaardbeperkingen en maximale beperkingen van Azure-abonnementen vindt u in [dit artikel.][azure-resource-manager/management/azure-subscription-service-limits-subscription]

## <a name="possible-scenarios"></a>Mogelijke scenario's
SAP wordt vaak gezien als een van de meest bedrijfskritische toepassingen binnen ondernemingen. De architectuur en de werking van deze toepassingen is meestal complex en ervoor te zorgen dat u voldoet aan de eisen op het gebied van beschikbaarheid en prestaties is belangrijk.

Zo moeten bedrijven goed nadenken over welke cloudprovider ze moeten kiezen voor het uitvoeren van dergelijke bedrijfskritische bedrijfsprocessen. Azure is het ideale publieke cloudplatform voor bedrijfskritische SAP-toepassingen en bedrijfsprocessen. Gezien de grote verscheidenheid aan Azure-infrastructuur kunnen bijna alle bestaande SAP NetWeaver- en S/4HANA-systemen vandaag in Azure worden gehost. Azure biedt VM's met veel Terabytes aan geheugen en meer dan 200 CPU's. Daarnaast biedt Azure [HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), waarmee opschad HANA-implementaties van maximaal 24 TB en SAP HANA scale-out implementaties van maximaal 120 TB kunnen worden uitgevoerd. Men kan vandaag stellen dat bijna alle on-premise SAP-scenario's kunnen worden uitgevoerd in Azure ook. 

Zie de door Azure [ondersteunde scenario's](./sap-planning-supported-configurations.md)voor een ruwe beschrijving van de scenario's en enkele niet-ondersteunde scenario's.

Controleer deze scenario's en enkele van de voorwaarden die zijn genoemd als niet ondersteund in de documentatie waarnaar wordt verwezen tijdens de planning en de ontwikkeling van uw architectuur die u in Azure wilt implementeren.

Over het algemeen is het meest voorkomende implementatiepatroon een cross-premises scenario zoals weergegeven

![VPN met site-to-site connectiviteit (cross-premises)][planning-guide-figure-300]

Reden voor veel klanten om een cross-premises implementatiepatroon toe te passen, is het feit dat het voor alle toepassingen het meest transparant is om on-premises uit te breiden naar Azure met Azure ExpressRoute en Azure als virtueel datacenter te behandelen. Naarmate meer en meer assets naar Azure worden verplaatst, zal de azure geïmplementeerde infrastructuur en netwerkinfrastructuur groeien en de on-premises assets dienovereenkomstig verminderen. Alles transparant voor gebruikers en applicaties.

Om SAP-systemen succesvol te implementeren in Azure IaaS of IaaS in het algemeen, is het belangrijk om de aanzienlijke verschillen tussen het aanbod van traditionele outsourcers of hosters en IaaS-aanbiedingen te begrijpen. Terwijl de traditionele hoster of outsourcer de infrastructuur (netwerk, opslag en servertype) aanpast aan de werkbelasting die een klant wil hosten, is het in plaats daarvan de verantwoordelijkheid van de klant of partner om de werkbelasting te karakteriseren en de juiste Azure te kiezen onderdelen van VM's, opslag en netwerk voor IaaS-implementaties.

Om gegevens te verzamelen voor de planning van uw implementatie in Azure, is het belangrijk om:

- Evalueren welke SAP-producten worden ondersteund in Azure VM's
- Evalueren welke specifieke releases van het besturingssysteem worden ondersteund met specifieke Azure VM's voor die SAP-producten
- Evalueren welke DBMS-releases worden ondersteund voor uw SAP-producten met specifieke Azure VM's
- Evalueren of sommige van de vereiste OS/DBMS-releases vereisen dat u SAP-release, upgrade van het ondersteuningspakket en kernelupgrades uitvoert om tot een ondersteunde configuratie te komen
- Evalueer of u naar verschillende besturingssystemen moet gaan om te kunnen implementeren op Azure.

Details over ondersteunde SAP-componenten op Azure, ondersteunde Azure-infrastructuureenheden en gerelateerde releases van besturingssystemen en DBMS-releases worden in het artikel uitgelegd [welke SAP-software wordt ondersteund voor Azure-implementaties.](./sap-supported-product-on-azure.md) Resultaten die zijn behaald uit de evaluatie van geldige SAP-releases, het besturingssysteem en DBMS-releases hebben een grote impact op de inspanningen om SAP-systemen naar Azure te verplaatsen. De resultaten van deze evaluatie zullen bepalen of er aanzienlijke voorbereidingsinspanningen kunnen zijn in gevallen waarin SAP-releaseupgrades of wijzigingen van besturingssystemen nodig zijn.


## <a name="azure-regions"></a><a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Azure-regio's
De Azure-services van Microsoft worden verzameld in Azure-regio's. Een Azure-regio is een één of een verzameling uit datacenters die de hardware en infrastructuur bevatten die de verschillende Azure-services uitvoert en host. Deze infrastructuur omvat een groot aantal knooppunten die fungeren als compute nodes of opslagknooppunten of netwerkfunctionaliteit uitvoeren. 

Raadpleeg het artikel [Azure-regio's](https://azure.microsoft.com/global-infrastructure/geographies/)voor een lijst met de verschillende Azure-regio's . Niet alle Azure-regio's bieden dezelfde services. Afhankelijk van het SAP-product dat u wilt uitvoeren en het besturingssysteem en dbms dat ermee verband houdt, u in een situatie terechtkomen dat een bepaalde regio niet de vm-typen biedt die u nodig hebt. Dit geldt vooral voor het uitvoeren van SAP HANA, waar je meestal VM's van de M/Mv2 VM-serie nodig hebt. Deze VM-families worden alleen geïmplementeerd in een subset van de regio's. U met behulp van de siteproducten die beschikbaar zijn [per regio,](https://azure.microsoft.com/global-infrastructure/services/)welke exacte VM-, -typen, Azure-opslagtypen of andere Azure-services beschikbaar zijn, welke van de regio's beschikbaar zijn. Wanneer u begint met uw planning en bepaalde regio's in gedachten hebt als primaire regio en uiteindelijk secundaire regio, moet u eerst onderzoeken of de benodigde diensten beschikbaar zijn in die regio's. 

### <a name="availability-zones"></a>Beschikbaarheidszones
Verschillende Azure-regio's hebben een concept geïmplementeerd dat beschikbaarheidszones wordt genoemd. Beschikbaarheidszones zijn fysiek gescheiden locaties binnen een Azure-gebied. Elke beschikbaarheidszone bestaat uit een of meer datacenters die zijn uitgerust met onafhankelijke voeding, koeling en netwerken. Als u bijvoorbeeld twee VM's implementeert in twee beschikbaarheidszones van Azure en een framework met hoge beschikbaarheid implementeert voor uw SAP DBMS-systeem of de SAP Central Services, krijgt u de beste SLA in Azure. Voor deze specifieke virtuele machine SLA in Azure, controleer de nieuwste versie van [Virtual Machine SLA.For](https://azure.microsoft.com/support/legal/sla/virtual-machines/)this particular virtual machine SLA in Azure, check the latest version of Virtual Machine SLAs . Aangezien Azure-regio's de afgelopen jaren snel zijn ontwikkeld en uitgebreid, kan de topologie van de Azure-regio's, het aantal fysieke datacenters, de afstand tussen die datacenters en de afstand tussen Azure Availability Zones verschillen. En daarmee de netwerklatentie.

Het principe van beschikbaarheidszones is niet van toepassing op de HANA-specifieke service van [HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Servicelevel-overeenkomsten voor HANA Large Instances zijn te vinden in het artikel [SLA voor SAP HANA op Azure Large Instances](https://azure.microsoft.com/support/legal/sla/sap-hana-large/) 


### <a name="fault-domains"></a><a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Foutdomeinen
Foutdomeinen vertegenwoordigen een fysieke storingseenheid, nauw verwant aan de fysieke infrastructuur in datacenters, en hoewel een fysiek blad of rek kan worden beschouwd als een foutdomein, is er geen directe één-op-één toewijzing tussen de twee.

Wanneer u meerdere virtuele machines implementeert als onderdeel van één SAP-systeem in Microsoft Azure Virtual Machine Services, u de Azure Fabric Controller beïnvloeden om uw toepassing in verschillende foutdomeinen te implementeren, waardoor aan hogere vereisten van beschikbaarheid sa's. De distributie van foutdomeinen over een Azure Scale Unit (verzameling van honderden Compute-knooppunten of opslagknooppunten en netwerken) of de toewijzing van VM's aan een specifiek foutdomein is echter iets waarover u geen directe controle hebt. Als u de Azure-fabriccontroller wilt sturen om een set VM's over verschillende foutdomeinen te implementeren, moet u een Azure-beschikbaarheidsset toewijzen aan de VM's tijdens de implementatietijd. Zie hoofdstuk [Azure-beschikbaarheidssets][planning-guide-3.2.3] in dit document voor meer informatie over azure-beschikbaarheidssets.


### <a name="upgrade-domains"></a><a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Domeinen bijwerken
Upgradedomeinen vertegenwoordigen een logische eenheid die helpt om te bepalen hoe een VM binnen een SAP-systeem, die bestaat uit SAP-exemplaren die in meerdere VM's worden uitgevoerd, wordt bijgewerkt. Wanneer een upgrade plaatsvindt, gaat Microsoft Azure door het proces om deze upgradedomeinen één voor één bij te werken. Door VM's tijdens implementatietijd over verschillende upgradedomeinen te verspreiden, u uw SAP-systeem gedeeltelijk beschermen tegen mogelijke downtime. Als u Azure wilt dwingen de VM's van een SAP-systeem te implementeren dat is verspreid over verschillende upgradedomeinen, moet u een specifiek kenmerk instellen op het moment van implementatie van elke vm. Net als bij foutdomeinen is een Azure Scale Unit onderverdeeld in meerdere upgradedomeinen. Als u de Azure-fabriccontroller wilt sturen om een set VM's over verschillende upgradedomeinen te implementeren, moet u een Azure Availability Set toewijzen aan de VM's tijdens de implementatietijd. Zie onderstaande [beschikbaarheidssets][planning-guide-3.2.3] voor Azure voor meer informatie over azure-beschikbaarheidssets.


### <a name="azure-availability-sets"></a><a name="18810088-f9be-4c97-958a-27996255c665"></a>Azure-beschikbaarheidssets
Azure Virtual Machines binnen één Azure-beschikbaarheidsset worden gedistribueerd door de Azure-structuurcontroller over verschillende fout- en upgradedomeinen. Het doel van de distributie over verschillende fout- en upgradedomeinen is om te voorkomen dat alle VM's van een SAP-systeem worden afgesloten in het geval van onderhoud van de infrastructuur of een storing binnen één foutdomein. Standaard maken VM's geen deel uit van een beschikbaarheidsset. De deelname van een VM aan een beschikbaarheidsset wordt gedefinieerd op implementatietijd of later door een herconfiguratie en herplaatsing van een VM.

Als u inzicht wilt krijgen in het concept van Azure-beschikbaarheidssets en de manier waarop beschikbaarheidssets betrekking hebben op fout- en upgradedomeinen, leest u [dit artikel][virtual-machines-manage-availability]. 

Als u beschikbaarheidssets definieert en verschillende VM's van verschillende VM-families binnen één beschikbaarheidsset probeert te mengen, u problemen tegenkomen waardoor u een bepaald VM-type niet in een dergelijke beschikbaarheidsset opnemen. De reden hiervoor is dat de beschikbaarheidsset is gebonden aan schaaleenheid die een bepaald type compute hosts bevat. En een bepaald type compute host kan alleen bepaalde typen VM-families uitvoeren. Als u bijvoorbeeld een beschikbaarheidsset maakt en de eerste VM implementeert in de beschikbaarheidsset en u een VM-type van de Esv3-familie kiest en vervolgens probeert u als tweede VM een VM van de M-familie te implementeren, wordt u geweigerd in de tweede toewijzing. Reden is dat de Esv3 familie VM's draaien niet op dezelfde host hardware als de virtuele machines van de M familie doen. Hetzelfde probleem kan zich voordoen wanneer u vm's probeert te wijzigen en probeert een VM uit de Esv3-familie te verplaatsen naar een VM-type van de M-familie. In het geval van het wijzigen van de grootte van een VM-familie die niet op dezelfde hosthardware kan worden gehost, moet u alle VM's in uw beschikbaarheidsset uitschakelen en het formaat ervan wijzigen om ze op het andere type hostmachine te kunnen uitvoeren. Voor SLA's van VM's die binnen de beschikbaarheidsset worden geïmplementeerd, raadpleegt u het artikel [Virtual Machine SLAs](https://azure.microsoft.com/support/legal/sla/virtual-machines/). 

Het principe van beschikbaarheidset en het bijbehorende update- en foutdomein is niet van toepassing op de HANA-specifieke service van [HANA Large Instances.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) Servicelevelovereenkomsten voor HANA Large Instances zijn te vinden in het artikel [SLA voor SAP HANA op Azure Large Instances](https://azure.microsoft.com/support/legal/sla/sap-hana-large/). 

> [!IMPORTANT]
> De concepten azure availability zones en Azure availability sets sluiten elkaar uit. Dat betekent dat u een paar of meerdere VM's implementeren in een specifieke beschikbaarheidszone of een Azure-beschikbaarheidsset. Maar niet beide.


## <a name="azure-virtual-machine-services"></a>Azure-services voor virtuele machines
Azure biedt een grote verscheidenheid aan virtuele machines die u selecteren om te implementeren. Er is geen behoefte aan up-front technologie en infrastructuur aankopen. De azure VM-service service biedt het onderhouden en bedienen van toepassingen eenvoudiger door on-demand compute en opslag te bieden voor het hosten, schalen en beheren van webtoepassingen en verbonden toepassingen. Infrastructuurbeheer wordt geautomatiseerd met een platform dat is ontworpen voor hoge beschikbaarheid en dynamische schaling om aan de gebruiksbehoeften te voldoen met de optie van verschillende prijsmodellen.

![Positionering van Microsoft Azure Virtual Machine Services][planning-guide-figure-400]

Met virtuele Azure-machines stelt Microsoft u in staat om aangepaste serverafbeeldingen te implementeren in Azure als IaaS-exemplaren. Of u kiezen uit een uitgebreide selectie van verbruiksbare besturingssysteemafbeeldingen uit de Azure-afbeeldingsgalerie.

Vanuit een operationeel perspectief biedt de Azure Virtual Machine Service vergelijkbare ervaringen als virtuele machines die op locatie worden geïmplementeerd. U bent verantwoordelijk voor het beheer, de bewerkingen en ook het patchen van het specifieke besturingssysteem, uitgevoerd in een Azure VM en de bijbehorende toepassingen in die VM. Microsoft levert geen services meer dan het hosten van die VM op zijn Azure-infrastructuur (Infrastructure as a Service - IaaS). Voor SAP-workload die u als klant implementeert, heeft Microsoft geen aanbiedingen die verder gaan dan het IaaS-aanbod. 

Het Microsoft Azure-platform is een multi-tenantplatform. Als gevolg hiervan worden opslag-, netwerk- en rekenresources die Azure VM's hosten, op enkele uitzonderingen na, gedeeld tussen tenants. Intelligente beperking en quotalogica wordt gebruikt om te voorkomen dat een tenant de prestaties van een andere tenant (luidruchtige buurman) drastisch beïnvloedt. Speciaal voor het certificeren van het Azure-platform voor SAP HANA moet Microsoft de bronisolatie bewijzen voor gevallen waarin meerdere VM's op dezelfde host op regelmatige basis naar SAP kunnen worden uitgevoerd. Hoewel logica in Azure probeert te houden varianties in bandbreedte ervaren kleine, sterk gedeelde platforms hebben de neiging om grotere varianties in de beschikbaarheid van middelen / bandbreedte dan klanten kunnen ervaren in hun on-premises implementaties te introduceren. Er moet rekening worden gehouden met de waarschijnlijkheid dat een SAP-systeem op Azure grotere varianties kan ervaren dan in een on-premises systeem.

### <a name="azure-virtual-machines-for-sap-workload"></a>Azure virtuele machines voor SAP-workload

Voor SAP-workload hebben we de selectie beperkt tot verschillende VM-families die specifieker geschikt zijn voor SAP-workload en SAP HANA-workload. De manier waarop u het juiste VM-type en de mogelijkheid om sap-werkbelasting te gebruiken vindt, wordt beschreven in het document [Welke SAP-software wordt ondersteund voor Azure-implementaties.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) 

> [!NOTE]
> De VM-typen die zijn gecertificeerd voor SAP-workload, er is geen over-provisioning van CPU- en geheugenbronnen.

Naast de selectie van puur ondersteunde VM's, moet u ook controleren of deze beschikbaar zijn in een specifieke regio op basis van de site [Producten beschikbaar per regio.](https://azure.microsoft.com/global-infrastructure/services/) Maar belangrijker, je moet evalueren of:

- CPU- en geheugenbronnen van verschillende VM-typen 
- IOPS-bandbreedte van verschillende VM-typen
- Netwerkmogelijkheden van verschillende VM-typen
- Aantal schijven dat kan worden aangesloten
- Mogelijkheid om bepaalde Azure-opslagtypen te gebruiken

past in uw behoefte. De meeste van die gegevens zijn hier te vinden [(Linux)][virtual-machines-sizes-linux] en [hier (Windows)][virtual-machines-sizes-windows] voor een bepaald VM-type.

Als prijsmodel heb je verschillende prijsopties die aanbieden, zoals:

- Betalen per gebruik
- Een jaar gereserveerd
- Drie jaar gereserveerd
- Spotprijzen

De prijzen van elk van de verschillende aanbiedingen met verschillende serviceaanbiedingen rond besturingssystemen en verschillende regio's is beschikbaar op de site [Linux Virtual Machines Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en Windows Virtual Machines [Pricing.](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) Voor meer informatie en flexibiliteit van een jaar en drie jaar gereserveerde exemplaren, controleer deze artikelen:

- [Wat zijn Azure-reserveringen?](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)
- [Flexibiliteit van de VM-grootte met gereserveerde VM-instanties](https://docs.microsoft.com/azure/virtual-machines/windows/reserved-vm-instance-size-flexibility)
- [Toepassing van de reserveringskorting op virtuele machines](https://docs.microsoft.com/azure/cost-management-billing/manage/understand-vm-reservation-charges) 

Lees het artikel Azure Spot [Virtual Machines voor](https://azure.microsoft.com/pricing/spot/)meer informatie over spotpricing. Prijzen van hetzelfde VM-type kunnen ook verschillen tussen verschillende Azure-regio's. Voor sommige klanten was het de moeite waard om te implementeren in een goedkopere Azure-regio.

Daarnaast biedt Azure de concepten van een dedicated host. Het speciale hostconcept geeft u meer controle over patchcycli die door Azure worden uitgevoerd. U de patching volgens uw eigen schema's timen. Deze aanbieding is specifiek gericht op klanten met werkbelasting die mogelijk niet de normale werkcyclus volgt. Als u de concepten van azure dedicated host-aanbiedingen wilt lezen, leest u het artikel [Azure Dedicated Host](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts). Het gebruik van deze aanbieding wordt ondersteund voor SAP-workload en wordt gebruikt door verschillende SAP-klanten die meer controle willen hebben over het patchen van infrastructuur en eventuele onderhoudsplannen van Microsoft. Lees het artikel [Onderhoud voor virtuele machines in Azure voor](https://docs.microsoft.com/azure/virtual-machines/maintenance-and-updates)meer informatie over hoe Microsoft de Azure-infrastructuur onderhoudt en patches die virtuele machines host.

#### <a name="generation-1-and-generation-2-virtual-machines"></a>Generatie 1 en Generatie 2 virtuele machines
Microsoft's hypervisor is in staat om twee verschillende generaties van virtuele machines te behandelen. Deze formaten heten **Generatie 1** en **Generatie 2**. **Generatie 2** werd geïntroduceerd in het jaar 2012 met Windows Server 2012 hypervisor. Azure is begonnen met virtuele generatie 1-machines. Terwijl u virtuele Azure-machines implementeert, is de standaardinstelling nog steeds het gebruik van de generatie 1-indeling. Ondertussen u ook Generation 2 VM-formaten implementeren. In het artikel [Ondersteuning voor generatie 2 VM's op Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) worden de Azure VM-families weergegeven die kunnen worden geïmplementeerd als Generatie 2 VM. In dit artikel worden ook de zeer belangrijke functionele verschillen van virtuele generatie-machines van Generatie 2 weergegeven, omdat ze kunnen worden uitgevoerd op Hyper-V private cloud en Azure. Belangrijker dit artikel bevat ook functionele verschillen tussen virtuele generatie 1-machines en Generatie 2 VM's, zoals die in Azure worden uitgevoerd. 

> [!NOTE]
> Er zijn functionele verschillen van Generatie 1 en Generatie 2 VM's die in Azure worden uitgevoerd. Lees het artikel [Ondersteuning voor generatie 2 VM's op Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) om een lijst met deze verschillen te bekijken.  
 
Het verplaatsen van een bestaande VM van de ene generatie naar de andere generatie is niet mogelijk. Om de generatie van de virtuele machine te veranderen, moet u een nieuwe VM van de gewenste generatie implementeren en de software die u uitvoert opnieuw installeren in de virtuele machine van de generatie. Dit heeft alleen invloed op de basis VHD-afbeelding van de VM en heeft geen invloed op de gegevensschijven of bijgevoegde NFS- of SMB-shares. Gegevensschijven, NFS- of SMB-shares die oorspronkelijk waren toegewezen aan bijvoorbeeld een Generatie 1 VM

Op dit moment zult u dit probleem vooral tegenkomen tussen de Dram's uit de Azure M-serie en vm's uit de Mv2-serie. Vanwege beperkingen in het Generation 1 VM-formaat konden de grote VM's van de Mv2-familie niet in Generatie 1-formaat worden aangeboden, maar moesten ze uitsluitend in Generatie 2 worden aangeboden. Aan de andere kant is de Vm-serie van de M-serie nog niet ingeschakeld om te worden geïmplementeerd in Generatie 2. Als gevolg hiervan, re-dimensionering tussen M-serie en Mv2-serie virtuele machines vereisen een herinstallatie van de software in een virtuele machine die u doel van de andere VM-familie. Microsoft werkt eraan om vm's uit de M-serie te implementeren voor implementaties van Generatie 2. Het implementeren van M-serie VM's als Generation 2 VM's in de toekomst, gaat een schijnbaar minder re-dimensionering tussen M-serie en Mv2-serie virtuele machines mogelijk te maken. In beide richtingen, hetzij up-sizing van M-serie tot grotere Mv2-serie virtuele machines of down-sizing van grotere Mv2-serie VM's tot kleinere M-serie VM's. De documentatie zal worden bijgewerkt zodra M-serie VM's kunnen worden ingezet als Generation 2 VM's.    

 

### <a name="storage-microsoft-azure-storage-and-data-disks"></a><a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Opslag: Microsoft Azure-opslag- en gegevensschijven
Microsoft Azure Virtual Machines maken gebruik van verschillende opslagtypen. Bij de implementatie van SAP op Azure Virtual Machine Services is het belangrijk om de verschillen tussen deze twee hoofdtypen opslag te begrijpen:

* Niet-persistente, vluchtige opslag.
* Permanente opslag.

Azure VM's bieden niet-permanente schijven nadat een VM is geïmplementeerd. In het geval van een VM reboot, zal alle inhoud op deze stations worden weggevaagd. Daarom is het een gegeven dat gegevensbestanden en log / redo bestanden van databases mag in geen geval worden gevestigd op die niet-aanhoudende stations. Er kunnen uitzonderingen zijn voor sommige databases, waar deze niet-aanhoudende schijven geschikt kunnen zijn voor tempdb- en tijdelijke tafelruimten. Vermijd echter het gebruik van deze schijven voor VM's uit de A-serie, omdat deze niet-aanhoudende schijven beperkt zijn in doorvoer met die VM-familie. Lees voor meer informatie het artikel [Inzicht in het tijdelijke station op Windows VM's in Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

---
> ![Windows][Logo_Windows] Windows
> 
> Station D:\ in een Azure VM is een niet-persistentstation, dat wordt ondersteund door een aantal lokale schijven op het Azure-compute-knooppunt. Omdat het niet-volgehouden is, betekent dit dat eventuele wijzigingen in de inhoud op de D:\ schijf gaat verloren wanneer de VM opnieuw wordt opgestart. Door "eventuele wijzigingen", zoals opgeslagen bestanden, mappen gemaakt, applicaties geïnstalleerd, enz.
> 
> ![Linux][Logo_Linux] Linux
> 
> Linux Azure VM's monteren automatisch een station op /mnt/resource dat een niet-aanhoudende schijf is die wordt ondersteund door lokale schijven op het Azure-compute-knooppunt. Omdat deze niet is aanhoudend, betekent dit dat wijzigingen in inhoud in /mnt/resource verloren gaan wanneer de VM opnieuw wordt opgestart. Door alle wijzigingen, zoals bestanden opgeslagen, mappen gemaakt, applicaties geïnstalleerd, enz.
> 
> 

---

Microsoft Azure Storage biedt blijvende opslag en de typische niveaus van bescherming en redundantie op SAN-opslag. Schijven op basis van Azure Storage zijn virtuele harde schijf (VHD's) in de Azure Storage Services. De lokale OS-Disk (Windows C:\, Linux /dev/sda1) wordt opgeslagen op de Azure Storage en extra volumes/schijven die aan de VM zijn gemonteerd, worden daar ook opgeslagen.

Het is mogelijk om een bestaande VHD van on-premises te uploaden of lege v.d.n. vanuit Azure te maken en deze VHD's aan geïmplementeerde VM's te koppelen.

Na het maken of uploaden van een VHD in Azure Storage, is het mogelijk om deze te monteren en te koppelen aan een bestaande virtuele machine en bestaande (unmounted) VHD te kopiëren.

Aangezien deze VHD's worden gehandhaafd, zijn gegevens en wijzigingen binnen die VHD's veilig bij het opnieuw opstarten en opnieuw maken van een instantie voor virtuele machines. Zelfs als een instantie wordt verwijderd, blijven deze VHD's veilig en kunnen ze opnieuw worden geïmplementeerd of in het geval van niet-OS-schijven kunnen worden gemonteerd op andere VM's.

Meer informatie over Azure Storage vindt u hier:

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Azure Standard Storage
Azure Standard-opslag was het type opslag dat beschikbaar was toen Azure IaaS werd uitgebracht. Er waren IOPS-quota afgedwongen per enkele schijf. Latentie ervaren was niet in dezelfde klasse als SAN / NAS-apparaten meestal ingezet voor high-end SAP-systemen gehost on-premises. Niettemin bleek de Azure Standard Storage voldoende voor vele honderden SAP-systemen die ondertussen in Azure zijn geïmplementeerd.

Schijven die zijn opgeslagen in Azure Standard Storage Accounts worden in rekening gebracht op basis van de werkelijke gegevens die worden opgeslagen, het volume van opslagtransacties, uitgaande gegevensoverdrachten en gekozen redundantieoptie. Veel schijven kunnen worden gemaakt op de maximale 1TB in grootte, maar zolang die leeg blijven is er geen kosten. Als u vervolgens een VHD met 100 GB per stuk vult, wordt u in rekening gebracht voor het opslaan van 100 GB en niet voor de nominale grootte waarmee de VHD is gemaakt.

#### <a name="azure-premium-storage"></a><a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Azure Premium-opslag
Azure Premium Storage is geïntroduceerd met als doel:

* Betere I/O latentie.
* Betere doorvoer.
* Minder variabiliteit in I/O-latentie.

Daartoe werden veel veranderingen doorgevoerd waarvan de twee belangrijkste zijn:

* Gebruik van SSD-schijven in de Azure Storage-knooppunten
* Een nieuwe leescache die wordt ondersteund door de lokale SSD van een Azure-compute-knooppunt

In tegenstelling tot standaardopslag, waar de mogelijkheden niet zijn gewijzigd, afhankelijk van de grootte van de schijf (of VHD), heeft Premium Storage momenteel drie verschillende schijfcategorieën, die in dit artikel worden weergegeven:<https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/>

U ziet dat IOPS/schijf en schijfdoorvoer/schijf afhankelijk zijn van de groottecategorie van de schijven

Kostenbasis in het geval van Premium Storage is niet het werkelijke gegevensvolume dat in dergelijke schijven is opgeslagen, maar de groottecategorie van een dergelijke schijf, onafhankelijk van de hoeveelheid gegevens die in de schijf is opgeslagen.

U ook schijven maken op Premium Storage die niet rechtstreeks worden in kaart gebracht in de weergegeven groottecategorieën. Dit kan het geval zijn, vooral bij het kopiëren van schijven van Standard Storage naar Premium Storage. In dergelijke gevallen wordt een toewijzing naar de volgende grootste Premium Storage-schijfoptie uitgevoerd.

De meeste Azure VM-families die zijn gecertificeerd met SAP, kunnen werken met Premium Storage en of een mix tussen Azure-standaard en Premium Storage.

Als u het deel van de VM's uit de DS-serie in [dit artikel (Linux)][virtual-machines-sizes-linux] en [dit artikel (Windows)][virtual-machines-sizes-windows]bekijkt, realiseert u zich dat er beperkingen zijn voor het gegevensvolume van Premium Storage-schijven op het granulariteitsniveau van het VM-niveau. Verschillende VM's uit de DS-serie of GS-serie hebben ook verschillende beperkingen in het aantal gegevensschijven dat kan worden gemonteerd. Deze limieten zijn gedocumenteerd in het hierboven genoemde artikel ook. Maar in wezen betekent dit dat als je bijvoorbeeld 32 x P30-schijven monteert op een enkele DS14 VM, je niet 32 x de maximale doorvoer van een P30-schijf krijgen. In plaats daarvan beperkt de maximale doorvoer op VM-niveau zoals gedocumenteerd in het artikel de gegevensdoorvoer.

Meer informatie over Premium Storage vindt u hier:<https://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="azure-storage-accounts"></a>Azure-opslagaccounts

Bij het implementeren van services of VM's in Azure kan de implementatie van VHD's en VM-afbeeldingen worden georganiseerd in eenheden die Azure Storage Accounts worden genoemd. Wanneer u een Azure-implementatie plant, moet u zorgvuldig rekening houden met de beperkingen van Azure. Aan de ene kant is er een beperkt aantal opslagaccounts per Azure-abonnement. Hoewel elk Azure Storage-account een groot aantal VHD-bestanden kan bevatten, is er een vaste limiet voor het totale IOPS per opslagaccount. Bij het implementeren van honderden SAP VM's met DBMS-systemen die aanzienlijke IO-oproepen maken, wordt aanbevolen om hoge IOPS DMS VM's te distribueren tussen meerdere Azure Storage Accounts. Er moet voor worden gezorgd dat de huidige limiet voor Azure-opslagaccounts per abonnement niet wordt overschreden. Omdat opslag een essentieel onderdeel is van de database-implementatie voor een SAP-systeem, wordt dit concept nader besproken in de reeds verwezen [DBMS Deployment Guide.][dbms-guide]

Meer informatie over Azure Storage Accounts vindt u in de [schaalbaarheidsdoelen voor standaardopslagaccounts](../../../storage/common/scalability-targets-standard-account.md) en [schaalbaarheidsdoelen voor blob-opslagaccounts met een hoge pagina.](../../../storage/blobs/scalability-targets-premium-page-blobs.md) Als u deze artikelen leest, realiseert u zich dat er verschillen zijn in de beperkingen tussen Azure Standard Storage Accounts en Premium Storage Accounts. Grote verschillen zijn de hoeveelheid gegevens die binnen een dergelijk opslagaccount kunnen worden opgeslagen. In Standaardopslag is het volume groter dan bij Premium Storage. Aan de andere kant is het standaardopslagaccount ernstig beperkt in IOPS (zie kolom **Total Request Rate),** terwijl het Azure Premium Storage Account geen dergelijke beperking heeft. We zullen details en resultaten van deze verschillen bespreken bij het bespreken van de implementaties van SAP-systemen, met name de DBMS-servers.

Binnen een opslagaccount heeft u de mogelijkheid om verschillende containers te maken met het oog op het organiseren en categoriseren van verschillende VHD's. Deze containers worden gebruikt om bijvoorbeeld VHD's van verschillende VM's te scheiden. Er zijn geen gevolgen voor de prestaties bij het gebruik van slechts één container of meerdere containers onder één Azure Storage-account.

Binnen Azure volgt een VHD-naam de volgende naamgevingsverbinding die een unieke naam moet opgeven voor de VHD binnen Azure:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

De bovenstaande tekenreeks moet op unieke wijze de VHD identificeren die is opgeslagen in Azure Storage.

#### <a name="managed-disks"></a><a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Beheerde schijven

Beheerde schijven zijn een nieuw brontype in Azure Resource Manager dat kan worden gebruikt in plaats van VHD's die zijn opgeslagen in Azure Storage Accounts. Beheerde schijven sluiten zich automatisch aan bij de beschikbaarheidsset van de virtuele machine waarop ze zijn aangesloten en verhogen daarom de beschikbaarheid van uw virtuele machine en de services die op de virtuele machine worden uitgevoerd. Voor meer informatie, lees het [overzichtsartikel](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

We raden u aan beheerde schijf te gebruiken, omdat deze de implementatie en het beheer van uw virtuele machines vereenvoudigen.
SAP ondersteunt momenteel alleen Premium Managed Disks. Voor meer informatie, lees SAP Note [1928533].

#### <a name="microsoft-azure-storage-resiliency"></a>Tolerantie voor Microsoft Azure-opslag

Microsoft Azure Storage slaat de basis VHD (met OS) en aangesloten schijven of BLOB's op ten minste drie afzonderlijke opslagknooppunten. Dit feit heet Local Redundant Storage (LRS). LRS is standaard voor alle typen opslag in Azure. 

Er zijn nog meer redundantiemethoden, die allemaal worden beschreven in het artikel [Azure Storage-replicatie](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Met betrekking tot Azure Premium Storage, het aanbevolen type opslag voor DBMS VM's en schijven die database- en log-/redo-bestanden opslaan, is de enige beschikbare methode LRS. Als gevolg hiervan moet u databasemethoden configureren, zoals SQL Server Always On, Oracle Data Guard of HANA System Replication om databasegegevensreplicatie in te schakelen in een andere Azure-regio of een andere Azure Availability Zone.


> [!NOTE]
> Voor DBMS-implementaties wordt het gebruik van Geo Redundant Storage zoals beschikbaar met Azure Standard Storage niet aanbevolen, omdat het ernstige prestatie-impact heeft en de schrijfvolgorde niet voor verschillende VHD's eert die zijn gekoppeld aan een VM. Het feit van het niet eren van de schrijfvolgorde over verschillende VHDs draagt een hoog potentieel om te eindigen in inconsistente databases aan de kant van de replicatie doel als database en log / redo bestanden zijn verspreid over meerdere VHDs (zoals meestal het geval) op de bron VM kant.


### <a name="microsoft-azure-networking"></a><a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Microsoft Azure-netwerken

Microsoft Azure biedt een netwerkinfrastructuur, waarmee alle scenario's kunnen worden in kaart gebracht, die we met SAP-software willen realiseren. De mogelijkheden zijn:

* Toegang van buitenaf, rechtstreeks naar de VM's via Windows Terminal Services of ssh/VNC
* Toegang tot diensten en specifieke poorten die worden gebruikt door toepassingen binnen de VM's
* Interne communicatie en naamomzetting tussen een groep VM's die zijn geïmplementeerd als Azure VM's
* Cross-premises connectiviteit tussen het on-premises netwerk van een klant en het Azure-netwerk
* Azure-regio of datacenterconnectiviteit tussen Azure-sites kruisen

Meer informatie vindt u hier: <https://azure.microsoft.com/documentation/services/virtual-network/>

Er zijn veel verschillende mogelijkheden om naam en IP-resolutie in Azure te configureren. Er is ook een Azure DNS-service, die kan worden gebruikt in plaats van het instellen van uw eigen DNS-server. Meer informatie is te vinden in [dit artikel][virtual-networks-manage-dns-in-vnet] en op [deze pagina](https://azure.microsoft.com/services/dns/).

Voor cross-premises of hybride scenario's vertrouwen we op het feit dat de on-premises AD/OpenLDAP/DNS is uitgebreid via VPN of privéverbinding met Azure. Voor bepaalde scenario's zoals hier gedocumenteerd, kan het nodig zijn om een AD/OpenLDAP-replica in Azure te laten installeren.

Omdat netwerk- en naamomzetting een essentieel onderdeel is van de database-implementatie voor een SAP-systeem, wordt dit concept nader besproken in de [DBMS Deployment Guide.][dbms-guide]

##### <a name="azure-virtual-networks"></a>Virtuele netwerken van Azure

Door een Azure Virtual Network op te bouwen, u het adresbereik definiëren van de privé-IP-adressen die zijn toegewezen door Azure DHCP-functionaliteit. In cross-premises scenario's wordt het gedefinieerde IP-adresbereik nog steeds toegewezen met DHCP by Azure. Het oplossen van domeinnamen gebeurt echter on-premises (ervan uitgaande dat de VM's deel uitmaken van een on-premises domein) en kan dus adressen oplossen die verder gaan dan verschillende Azure Cloud Services.

Elke virtuele machine in Azure moet worden aangesloten op een virtueel netwerk.

Meer details zijn te vinden in [dit artikel][resource-groups-networking] en op [deze pagina](https://azure.microsoft.com/documentation/services/virtual-network/).


> [!NOTE]
> Als een vm eenmaal is geïmplementeerd, u de configuratie van het virtuele netwerk niet wijzigen. De TCP/IP-instellingen moeten worden overgelaten aan de Azure DHCP-server. Standaardgedrag is Dynamische IP-toewijzing.
>
>

Het MAC-adres van de virtuele netwerkkaart kan veranderen, bijvoorbeeld na het wijzigen van de grootte en het Windows- of Linux-gastbesturingssysteem pakt de nieuwe netwerkkaart op en gebruikt in dit geval automatisch DHCP om de IP- en DNS-adressen toe te wijzen.

##### <a name="static-ip-assignment"></a>Statische IP-toewijzing
Het is mogelijk om vaste of gereserveerde IP-adressen toe te wijzen aan VM's binnen een Azure Virtual Network. Als u de VM's uitvoert in een Azure Virtual Network, wordt een grote mogelijkheid geboden om deze functionaliteit te gebruiken indien nodig of vereist voor sommige scenario's. De IP-toewijzing blijft geldig gedurende het hele bestaan van de VM, onafhankelijk van of de VM wordt uitgevoerd of afgesloten. Als gevolg hiervan moet u rekening houden met het totale aantal VM's (draaien en gestopte VM's) bij het definiëren van het bereik van IP-adressen voor het virtuele netwerk. Het IP-adres blijft toegewezen totdat de VM en de netwerkinterface zijn verwijderd of totdat het IP-adres opnieuw wordt toegewezen. Voor meer informatie, lees [dit artikel][virtual-networks-static-private-ip-arm-pportal].

> [!NOTE]
> U moet statische IP-adressen via Azure-middelen toewijzen aan afzonderlijke vNIC's. U mag geen statische IP-adressen binnen het gastbesturingssysteem toewijzen aan een vNIC. Sommige Azure-services zoals Azure Backup Service zijn afhankelijk van het feit dat ten minste de primaire vNIC is ingesteld op DHCP en niet op statische IP-adressen. Zie ook het document [Problemen met azure virtual machine backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking).
>
>

##### <a name="multiple-nics-per-vm"></a>Meerdere NIC's per VM

U meerdere virtuele netwerkinterfacekaarten (vNIC) definiëren voor een Azure Virtual Machine. Met de mogelijkheid om meerdere vNIC's te hebben, u beginnen met het instellen van netwerkverkeerscheiding, waarbij bijvoorbeeld clientverkeer wordt omgeleid via één vNIC en backendverkeer wordt omgeleid via een tweede vNIC. Afhankelijk van het type VM zijn er verschillende beperkingen voor het aantal vNIC's dat een VM kan hebben toegewezen. Exacte details, functionaliteit en beperkingen zijn te vinden in deze artikelen:

* [Een Windows-vm maken met meerdere NIC's][virtual-networks-multiple-nics-windows]
* [Een Linux-vm maken met meerdere NIC's][virtual-networks-multiple-nics-linux]
* [Multi NIC VM's implementeren met behulp van een sjabloon][virtual-network-deploy-multinic-arm-template]
* [Multi NIC VM's implementeren met PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Multi NIC VM's implementeren met de Azure CLI][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Site-to-Site-connectiviteit

Cross-premises is Azure VM's en On-Premises gekoppeld aan een transparante en permanente VPN-verbinding. Het wordt naar verwachting het meest voorkomende SAP-implementatiepatroon in Azure. De aanname is dat operationele procedures en processen met SAP-exemplaren in Azure transparant moeten werken. Dit betekent dat u deze systemen moet kunnen afdrukken en het SAP Transport Management System (TMS) moet kunnen gebruiken om wijzigingen van een ontwikkelingssysteem in Azure naar een testsysteem te transporteren, dat on-premises wordt geïmplementeerd. Meer documentatie rond site-to-site is te vinden in [dit artikel][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>VPN-tunnelapparaat

Als u een site-to-site-verbinding (on-premises datacenter met Azure-datacenter) wilt maken, moet u een VPN-apparaat verkrijgen en configureren, of routerings- en rastoegangsservice (RRAS) gebruiken die is geïntroduceerd als softwarecomponent met Windows Server 2012.

* [Een virtueel netwerk maken met een site-to-site VPN-verbinding met PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Informatie over VPN-apparaten voor verbinding met site-naar-site-VPN-gateways][vpn-gateway-about-vpn-devices]
* [Veelgestelde vragen VPN-gateways][vpn-gateway-vpn-faq]

![Site-to-site verbinding tussen on-premises en Azure][planning-guide-figure-600]

In de bovenstaande afbeelding ziet u twee Azure-abonnementen met SUBBereiken voor IP-adres die zijn gereserveerd voor gebruik in virtuele netwerken in Azure. De connectiviteit van het on-premises netwerk naar Azure wordt tot stand gebracht via VPN.

#### <a name="point-to-site-vpn"></a>Punt-naar-site-VPN

Point-to-site VPN vereist dat elke clientmachine verbinding maakt met zijn eigen VPN in Azure. Voor de SAP-scenario's kijken we naar, point-to-site connectiviteit is niet praktisch. Daarom worden er geen verdere verwijzingen gegeven naar point-to-site VPN-connectiviteit.

Meer informatie is hier te vinden
* [Een punt-naar-site-verbinding met een VNet configureren met Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Een punt-naar-site-verbinding met een VNet configureren met behulp van PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>Vpn met meerdere site's

Azure biedt tegenwoordig ook de mogelijkheid om Multi-Site VPN-connectiviteit te maken voor één Azure-abonnement. Voorheen was één abonnement beperkt tot één site-to-site VPN-verbinding. Deze beperking ging weg met Multi-Site VPN-verbindingen voor een enkel abonnement. Dit maakt het mogelijk om meer dan één Azure-regio te gebruiken voor een specifiek abonnement via cross-premises configuraties.

Zie [dit artikel][vpn-gateway-create-site-to-site-rm-powershell] voor meer documentatie

#### <a name="vnet-to-vnet-connection"></a>VNet naar VNet-verbinding

Met Multi-Site VPN moet u een afzonderlijk Azure Virtual Network configureren in elk van de regio's. Maar vaak heb je de eis dat de softwarecomponenten in de verschillende regio's met elkaar moeten communiceren. Idealiter mag deze communicatie niet worden gerouteerd van de ene Azure-regio naar on-premises en van daaruit naar de andere Azure-regio. Als u snelkoppelingwilt, biedt Azure de mogelijkheid om een verbinding te configureren van het ene Azure Virtual Network in de ene regio naar een andere Azure Virtual Network die in een andere regio wordt gehost. Deze functionaliteit wordt VNet-to-VNet-verbinding genoemd. Meer details over deze functionaliteit vindt <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>u hier: .

#### <a name="private-connection-to-azure-expressroute"></a>Privéverbinding met Azure ExpressRoute

Microsoft Azure ExpressRoute maakt het mogelijk om privéverbindingen te maken tussen Azure-datacenters en de on-premises infrastructuur van de klant of in een colocatieomgeving. ExpressRoute wordt aangeboden door verschillende MPLS (packet switched) VPN providers of andere Network Service Providers. ExpressRoute-verbindingen gaan niet via het openbare internet. ExpressRoute-verbindingen bieden een hogere beveiliging, meer betrouwbaarheid via meerdere parallelle circuits, hogere snelheden en lagere latencies dan typische verbindingen via internet.

Meer informatie over Azure ExpressRoute en aanbiedingen vindt u hier:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Express Route maakt meerdere Azure-abonnementen mogelijk via één ExpressRoute-circuit zoals hier gedocumenteerd

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Gedwongen tunneling in geval van cross-premises
Voor VM's die on-premises domeinen samenvoegen via site-to-site, point-to-site of ExpressRoute, moet u ervoor zorgen dat de internetproxy-instellingen ook worden geïmplementeerd voor alle gebruikers in die VM's. Standaard zou software die in die VM's of gebruikers die een browser gebruiken om toegang te krijgen tot het internet niet via de bedrijfsproxy gaan, maar rechtstreeks via Azure verbinding maken met het internet. Maar zelfs de proxy-instelling is niet een 100% oplossing om het verkeer direct via het bedrijf proxy, omdat het de verantwoordelijkheid van software en diensten om te controleren op de proxy. Als software die in de VM wordt uitgevoerd dat niet doet of als een beheerder de instellingen manipuleert, kan verkeer naar internet opnieuw rechtstreeks via Azure naar het internet worden omgeleid.

Om een dergelijke directe internetverbinding te voorkomen, u gedwongen tunneling configureren met site-to-site-connectiviteit tussen on-premises en Azure. De gedetailleerde beschrijving van de geforceerde tunnelfunctie wordt hier gepubliceerd<https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

Gedwongen tunneling met ExpressRoute wordt mogelijk gemaakt door klanten die een standaardroute adverteren via de ExpressRoute BGP-peeringsessies.

#### <a name="summary-of-azure-networking"></a>Overzicht van Azure Networking

Dit hoofdstuk bevatte veel belangrijke punten over Azure Networking. Hier is een samenvatting van de belangrijkste punten:

* Met Azure Virtual Networks u een netwerkstructuur in uw Azure-implementatie plaatsen. VNets kunnen tegen elkaar worden geïsoleerd of met behulp van Network Security Groups kan het verkeer tussen VNets worden geregeld.
* Azure Virtual Networks kan worden gebruikt om IP-adresbereiken toe te wijzen aan VM's of vaste IP-adressen toe te wijzen aan VM's
* Als u een site-naar-site- of point-to-site-verbinding wilt instellen, moet u eerst een Azure Virtual Network maken
* Zodra een virtuele machine is geïmplementeerd, is het niet meer mogelijk om het virtuele netwerk dat is toegewezen aan de VM

### <a name="quotas-in-azure-virtual-machine-services"></a>Quota in Azure Virtual Machine Services
We moeten duidelijk zijn over het feit dat de opslag- en netwerkinfrastructuur wordt gedeeld tussen VM's die verschillende services in de Azure-infrastructuur uitvoeren. En net als in de eigen datacenters van de klant vindt een deel van de infrastructuurbronnen tot op zekere hoogte plaats. Het Microsoft Azure Platform gebruikt schijf-, CPU-, netwerk- en andere quota om het resourceverbruik te beperken en consistente en deterministische prestaties te behouden.  De verschillende VM-typen (A5, A6, enz.) hebben verschillende quota voor het aantal schijven, CPU, RAM en Netwerk.

> [!NOTE]
> CPU- en geheugenbronnen van de VM-typen die door SAP worden ondersteund, worden vooraf toegewezen op de hostknooppunten. Dit betekent dat zodra de VM is geïmplementeerd, de resources op de host beschikbaar zijn zoals gedefinieerd door het VM-type.
>
>

Bij het plannen en dimensioneren van SAP op Azure-oplossingen moeten de quota voor elke grootte van de virtuele machine in aanmerking worden genomen. De VM quota worden hier beschreven [(Linux)][virtual-machines-sizes-linux] en [hier (Windows)][virtual-machines-sizes-windows].

De beschreven quota vertegenwoordigen de theoretische maximumwaarden.  De limiet van IOPS per schijf kan worden bereikt met kleine IOs (8kb), maar mogelijk niet worden bereikt met grote IOs (1Mb).  De IOPS-limiet wordt afgedwongen op de granulariteit van één schijf.

Als een ruwe beslissingsstructuur om te beslissen of een SAP-systeem past in Azure Virtual Machine Services en de mogelijkheden ervan of dat een bestaand systeem anders moet worden geconfigureerd om het systeem op Azure te implementeren, kan de onderstaande beslissingsstructuur worden gebruikt:

![Beslissingsstructuur om te beslissen of SAP kan worden geïmplementeerd op Azure][planning-guide-figure-700]

**Stap 1**: De belangrijkste informatie om mee te beginnen is de SAPS-vereiste voor een bepaald SAP-systeem. De SAPS-vereisten moeten worden opgesplitst in het DBMS-onderdeel en het SAP-toepassingsonderdeel, zelfs als het SAP-systeem al on-premises is geïmplementeerd in een 2-laagsconfiguratie. Voor bestaande systemen kan het SAPS met betrekking tot de gebruikte hardware vaak worden bepaald of geschat op basis van bestaande SAP-benchmarks. De resultaten zijn hier <https://sap.com/about/benchmark.html>te vinden: .
Voor nieuw geïmplementeerde SAP-systemen had u een maatoefening moeten doorlopen, die de SAPS-vereisten van het systeem moet bepalen.
Zie ook dit blog en bijgevoegd document voor SAP-dimensionering op Azure:<https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**Stap 2**: Voor bestaande systemen moeten de I/O-bewerkingen per seconde op de DBMS-server worden gemeten. Voor nieuw geplande systemen moet de maatvoering voor het nieuwe systeem ook ruwe ideeën geven over de I/O-vereisten aan de DBMS-kant. Als u het niet zeker weet, moet u uiteindelijk een Proof of Concept uitvoeren.

**Stap 3**: Vergelijk de SAPS-vereiste voor de DBMS-server met de SAPS die de verschillende VM-typen Azure kunnen bieden. De informatie over SAPS van de verschillende Azure VM-typen is gedocumenteerd in SAP Note [1928533]. De focus moet eerst op de DBMS VM liggen, omdat de databaselaag de laag is in een SAP NetWeaver-systeem dat in de meeste implementaties niet wordt uitschaald. De SAP-toepassingslaag kan daarentegen worden uitgeschaald. Als geen van de door SAP ondersteunde Azure VM-typen de vereiste SAPS kan leveren, kan de werkbelasting van het geplande SAP-systeem niet worden uitgevoerd op Azure. U moet het systeem on-premises implementeren of u moet het werkbelastingsvolume voor het systeem wijzigen.

**Stap 4**: Zoals hier gedocumenteerd [(Linux)][virtual-machines-sizes-linux] en [hier (Windows),][virtual-machines-sizes-windows]Azure dwingt een IOPS quotum per schijf onafhankelijk of u nu gebruik maakt van Standaard Opslag of Premium Storage. Afhankelijk van het VM-type varieert het aantal gegevensschijven, dat kan worden gemonteerd. Als gevolg hiervan u een maximaal IOPS-nummer berekenen dat kan worden bereikt met elk van de verschillende VM-typen. Afhankelijk van de lay-out van het databasebestand u schijven strepen om één volume in het gastbesturingssysteem te worden. Als het huidige IOPS-volume van een geïmplementeerd SAP-systeem echter de berekende limieten van het grootste VM-type Azure overschrijdt en als er geen kans is om te compenseren met meer geheugen, kan de werkbelasting van het SAP-systeem ernstig worden beïnvloed. In dergelijke gevallen u een punt raken waarop u het systeem niet op Azure moet implementeren.

**Stap 5**: Vooral in SAP-systemen, die on-premises worden geïmplementeerd in 2-tier-configuraties, is de kans groot dat het systeem mogelijk moet worden geconfigureerd op Azure in een 3-tier configuratie. In deze stap moet u controleren of er een component in de SAP-toepassingslaag is, die niet kan worden geschaald en die niet in de CPU- en geheugenbronnen zou passen die de verschillende Azure VM-typen bieden. Als er inderdaad een dergelijk onderdeel is, kunnen het SAP-systeem en de werkbelasting ervan niet worden geïmplementeerd in Azure. Maar als u de ONDERDELEN van de SAP-toepassing uitschalen naar meerdere Azure VM's, kan het systeem worden geïmplementeerd in Azure.

**Stap 6**: Als de dbms- en SAP-toepassingslaagcomponenten in Azure VM's kunnen worden uitgevoerd, moet de configuratie worden gedefinieerd met betrekking tot:

* Aantal Azure VM's
* VM-typen voor de afzonderlijke componenten
* Aantal VHD's in DBMS VM om voldoende IOPS te bieden

## <a name="managing-azure-assets"></a>Azure-elementen beheren

### <a name="azure-portal"></a>Azure Portal

De Azure-portal is een van de drie interfaces voor het beheren van Azure VM-implementaties. De basisbeheertaken, zoals het implementeren van VM's uit afbeeldingen, kunnen worden uitgevoerd via de Azure-portal. Daarnaast zijn het maken van opslagaccounts, virtuele netwerken en andere Azure-componenten ook taken die de Azure-portal goed kan verwerken. Functionaliteit zoals het uploaden van VHD's van on-premises naar Azure of het kopiëren van een VHD binnen Azure zijn echter taken, waarvoor hulpprogramma's van derden of beheer via PowerShell of CLI vereist zijn.

![Microsoft Azure-portal - Virtual Machine-overzicht][planning-guide-figure-800]


Beheer- en configuratietaken voor de instantie Virtuele machine zijn mogelijk vanuit de Azure-portal.

Naast het opnieuw opstarten en afsluiten van een virtuele machine u ook gegevensschijven voor de instantie Virtual Machine bevestigen, loskoppelen en maken, om de instantie voor het voorbereiden van afbeeldingen vast te leggen en de grootte van de instantie Virtuele machine te configureren.

De Azure-portal biedt basisfunctionaliteit voor het implementeren en configureren van VM's en vele andere Azure-services. Niet alle beschikbare functionaliteit valt echter onder de Azure-portal. In de Azure-portal is het niet mogelijk om taken uit te voeren zoals:

* VHD's uploaden naar Azure
* VM's kopiëren


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Beheer via Microsoft Azure PowerShell-cmdlets

Windows PowerShell is een krachtig en uitbreidbaar framework dat op grote schaal is overgenomen door klanten die grotere aantallen systemen in Azure implementeren. Na de installatie van PowerShell-cmdlets op een desktop, laptop of speciaal beheerstation kunnen de PowerShell-cmdlets op afstand worden uitgevoerd.

Het proces om een lokale desktop/laptop in te schakelen voor het gebruik van Azure PowerShell-cmdlets en hoe u deze configureren voor het gebruik met het Azure-abonnement(en) wordt in [dit artikel][powershell-install-configure]beschreven.

Meer gedetailleerde stappen over het installeren, bijwerken en configureren van de Azure PowerShell-cmdlets vindt u ook in [dit hoofdstuk van de implementatiehandleiding.][deployment-guide-4.1]

Customer experience tot nu toe is dat PowerShell (PS) is zeker de meer krachtige tool om VM's te implementeren en om aangepaste stappen in de implementatie van VM's te maken. Alle klanten met SAP-exemplaren in Azure gebruiken PS-cmdlets om beheertaken aan te vullen die ze uitvoeren in de Azure-portal of gebruiken zelfs PS-cmdlets uitsluitend om hun implementaties in Azure te beheren. Aangezien de Azure-specifieke cmdlets dezelfde naamgevingsconventie delen als de meer dan 2000 Windows-gerelateerde cmdlets, is het een gemakkelijke taak voor Windows-beheerders om gebruik te maken van deze cmdlets.

Zie voorbeeld hier:<https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>


Implementatie van de Azure Extension for SAP (zie hoofdstuk [Azure Extension for SAP][planning-guide-9.1] in dit document) is alleen mogelijk via PowerShell of CLI. Daarom is het verplicht om PowerShell of CLI in te stellen en te configureren bij het implementeren of beheren van een SAP NetWeaver-systeem in Azure.  

Aangezien Azure meer functionaliteit biedt, worden er nieuwe PS-cmdlets toegevoegd die een update van de cmdlets vereisen. Daarom is het zinvol om de Azure Download-site ten minste eenmaal per maand <https://azure.microsoft.com/downloads/> te controleren op een nieuwe versie van de cmdlets. De nieuwe versie is geïnstalleerd op de top van de oudere versie.

Voor een algemene lijst met Azure-gerelateerde PowerShell-opdrachten u hier controleren: <https://docs.microsoft.com/powershell/azure/overview>.

### <a name="management-via-microsoft-azure-cli-commands"></a>Beheer via Microsoft Azure CLI-opdrachten

Voor klanten die Linux gebruiken en Azure-bronnen willen beheren, is PowerShell mogelijk geen optie. Microsoft biedt Azure CLI als alternatief.
De Azure CLI biedt een reeks open source, cross-platform opdrachten voor het werken met het Azure Platform. De Azure CLI biedt veel van dezelfde functionaliteit in de Azure-portal.

Zie Informatie over installatie, configuratie en het gebruik van CLI-opdrachten om Azure-taken uit te voeren

* [De klassieke Azure CLI installeren][xplat-cli]
* [Virtuele machines implementeren en beheren met Azure Resource Manager-sjablonen en de Azure CLI] [.. /.. /linux/create-ssh-secured-vm-from-template.md]
* [De klassieke CLI voor Azure voor Mac, Linux en Windows gebruiken met Azure Resource Manager][xplat-cli-azure-resource-manager]

Lees ook hoofdstuk [Azure CLI voor Linux VM's][deployment-guide-4.5.2] in de [implementatiehandleiding][planning-guide] over het gebruik van Azure CLI om de Azure Extension voor SAP te implementeren.


## <a name="first-steps-planning-a-deployment"></a>Eerste stappen met het plannen van een implementatie
De eerste stap in implementatieplanning is NIET om te controleren of VM's beschikbaar zijn om SAP uit te voeren. De eerste stap kan tijdrovend zijn, maar het belangrijkste is om te werken met compliance- en beveiligingsteams in uw bedrijf aan de randvoorwaarden voor het implementeren van welk type SAP-workload of bedrijfsproces in de openbare cloud. Als uw bedrijf eerder andere software in Azure heeft geïmplementeerd, kan het proces eenvoudig zijn. Als uw bedrijf meer aan het begin van de reis staat, kunnen er grotere discussies nodig zijn om de randvoorwaarden, beveiligingsvoorwaarden, die het mogelijk maken bepaalde SAP-gegevens en SAP-bedrijfsprocessen te hosten in de openbare cloud te achterhalen.

Als nuttige hulp u [microsoft-nalevingsaanbiedingen](https://docs.microsoft.com/microsoft-365/compliance/offering-home) aanwijzen voor een lijst met nalevingsaanbiedingen die Microsoft kan bieden. 

Andere punten van zorg, zoals gegevensversleuteling voor gegevens in rust of andere versleuteling in Azure-service, zijn gedocumenteerd in [azure-versleutelingsoverzicht.](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview)

Onderschat deze fase van het project niet in uw planning. Alleen wanneer u afspraken en regels hebt over dit onderwerp, moet u naar de volgende stap gaan, namelijk de planning van de netwerkarchitectuur die u implementeert in Azure.


## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Verschillende manieren om VM's voor SAP te implementeren in Azure

In dit hoofdstuk leert u op verschillende manieren een VM implementeren in Azure. Aanvullende voorbereidingsprocedures en de afhandeling van VHD's en VM's in Azure komen in dit hoofdstuk aan bod.

### <a name="deployment-of-vms-for-sap"></a>Implementatie van VM's voor SAP

Microsoft Azure biedt meerdere manieren om VM's en gekoppelde schijven te implementeren. Het is dus belangrijk om de verschillen te begrijpen, aangezien de voorbereidingen van de VM's kunnen verschillen afhankelijk van de implementatiemethode. In het algemeen nemen we een kijkje bij de volgende scenario's:

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Een VM verplaatsen van on-premises naar Azure met een niet-gegeneraliseerde schijf

U bent van plan een specifiek SAP-systeem van on-premises naar Azure te verplaatsen. Dit kan worden gedaan door het uploaden van de VHD, die het OS, de SAP Binaries, en DBMS binaries, plus de VHD's met de gegevens en logbestanden van de DBMS bevat naar Azure. In tegenstelling tot [scenario-#2 hieronder,][planning-guide-5.1.2]behoudt u de hostname, SAP SID en SAP-gebruikersaccounts in de Azure VM zoals deze zijn geconfigureerd in de on-premises omgeving. Daarom is het generaliseren van het beeld niet nodig. Zie hoofdstukken [Voorbereiding voor het verplaatsen van een VM van on-premises naar Azure met een niet-gegeneraliseerde schijf][planning-guide-5.2.1] van dit document voor on-premises voorbereidingsstappen en uploaden van niet-gegeneraliseerde VM's of VHD's naar Azure. Lees hoofdstuk [Scenario 3: Een VM verplaatsen van on-premises met behulp van een niet-gegeneraliseerde Azure VHD met SAP][deployment-guide-3.4] in de [implementatiehandleiding][deployment-guide] voor gedetailleerde stappen voor het implementeren van een dergelijke afbeelding in Azure.

#### <a name="deploying-a-vm-with-a-customer-specific-image"></a><a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Een VM implementeren met een klantspecifieke afbeelding

Vanwege specifieke patchvereisten van uw OS- of DBMS-versie voldoen de geleverde afbeeldingen in de Azure Marketplace mogelijk niet aan uw behoeften. Daarom moet u mogelijk een VM maken met uw eigen privé-OS/DBMS VM-afbeelding, die daarna meerdere keren kan worden geïmplementeerd. Om een dergelijke privéafbeelding voor te bereiden op duplicatie, moeten de volgende punten in overweging worden genomen:

---
> ![Windows][Logo_Windows] Windows
>
> Bekijk hier meer <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> details: De Windows-instellingen (zoals Windows SID en hostname) moeten worden geabstraheerd /gegeneraliseerd op de on-premises VM via de sysprep-opdracht.
>
>
> ![Linux][Logo_Linux] Linux
>
> Volg de stappen die in deze artikelen worden beschreven voor [SUSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd]of [Oracle Linux,][virtual-machines-linux-create-upload-vhd-oracle]om een VHD voor te bereiden die naar Azure moet worden geüpload.
>
>

---
Als u al SAP-inhoud hebt geïnstalleerd in uw on-premises VM (met name voor 2-tier-systemen), u de SAP-systeeminstellingen aanpassen na de implementatie van de Azure VM via de instantiehernoemingsprocedure die wordt ondersteund door de SAP Software Provisioning Manager (SAP Note [1619720]). Zie hoofdstukken [Voorbereiding voor het implementeren van een VM met een klantspecifieke afbeelding voor SAP][planning-guide-5.2.2] en het [uploaden van een VHD van on-premises naar Azure][planning-guide-5.3.2] van dit document voor on-premises voorbereidingsstappen en uploaden van een gegeneraliseerde VM naar Azure. Lees hoofdstuk [Scenario 2: Een VM implementeren met een aangepaste afbeelding voor SAP][deployment-guide-3.3] in de [implementatiehandleiding][deployment-guide] voor gedetailleerde stappen voor het implementeren van een dergelijke afbeelding in Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Een VM implementeren uit de Azure Marketplace

U wilt een door Microsoft of door derden verstrekte VM-afbeelding van de Azure Marketplace gebruiken om uw VM te implementeren. Nadat u uw VM in Azure hebt geïmplementeerd, volgt u dezelfde richtlijnen en hulpprogramma's om de SAP-software en/of DBMS in uw VM te installeren als in een on-premises omgeving. Zie hoofdstuk [Scenario 1: Een VM implementeren uit de Azure Marketplace voor SAP][deployment-guide-3.2] in de implementatiehandleiding voor meer gedetailleerde [implementatiebeschrijving.][deployment-guide]

### <a name="preparing-vms-with-sap-for-azure"></a><a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>VM's voorbereiden met SAP voor Azure

Voordat u VM's uploadt naar Azure, moet u ervoor zorgen dat de VM's en VHD's aan bepaalde vereisten voldoen. Er zijn kleine verschillen afhankelijk van de implementatiemethode die wordt gebruikt.

#### <a name="preparation-for-moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Voorbereiding voor het verplaatsen van een vm van on-premises naar Azure met een niet-gegeneraliseerde schijf

Een veelgebruikte implementatiemethode is het verplaatsen van een bestaande VM, waarbij een SAP-systeem wordt uitgevoerd van on-premises naar Azure. Die VM en het SAP-systeem in de VM moeten gewoon draaien in Azure met dezelfde hostname en waarschijnlijk dezelfde SAP SID. In dit geval mag het gastbesturingssysteem van VM niet worden gegeneraliseerd voor meerdere implementaties. Als het on-premises netwerk is uitgebreid naar Azure, kunnen zelfs dezelfde domeinaccounts binnen de VM worden gebruikt als die eerder on-premises werden gebruikt.

Vereisten bij het voorbereiden van uw eigen Azure VM Disk zijn:

* Oorspronkelijk zou de VHD met het besturingssysteem een maximale grootte van 127 GB alleen kunnen hebben. Deze beperking werd eind maart 2015 opgeheven. Nu kan de VHD met het besturingssysteem tot 1 TB groot zijn, net als elke andere Azure Storage gehoste VHD.
* Het moet in de vaste VHD-formaat. Dynamische VHD's of VHD's in VHDx-indeling worden nog niet ondersteund op Azure. Dynamische VHD's worden geconverteerd naar statische VHD's wanneer u de VHD uploadt met PowerShell-commandlets of CLI
* VHD's, die zijn gemonteerd op de VM en moet opnieuw worden gemonteerd in Azure aan de VM moeten worden in een vaste VHD-formaat ook. Lees [dit artikel (Linux)](../../linux/managed-disks-overview.md) en [dit artikel (Windows)](../../windows/managed-disks-overview.md)) voor grootte limieten van gegevensschijven. Dynamische VHD's worden geconverteerd naar statische VHD's wanneer u de VHD uploadt met PowerShell-commandlets of CLI
* Voeg een ander lokaal account toe met beheerdersbevoegdheden, die kunnen worden gebruikt door Microsoft-ondersteuning of die kunnen worden toegewezen als context voor services en toepassingen om in uit te voeren totdat de VM is geïmplementeerd en meer geschikte gebruikers kunnen worden gebruikt.
* Voeg andere lokale accounts toe, omdat deze mogelijk nodig zijn voor het specifieke implementatiescenario.

---
> ![Windows][Logo_Windows] Windows
>
> In dit scenario is geen generalisatie (sysprep) van de VM vereist om de VM op Azure te uploaden en te implementeren.
> Zorg ervoor dat station D:\ wordt niet gebruikt.
> Stel automatisch schijfmonteren in voor gekoppelde schijven zoals beschreven in hoofdstuk [Automatisch monteren voor gekoppelde schijven][planning-guide-5.5.3] in dit document.
>
> ![Linux][Logo_Linux] Linux
>
> In dit scenario is geen generalisatie (waagent -deprovision) van de VM vereist om de VM op Azure te uploaden en te implementeren.
> Zorg ervoor dat / mnt / resource niet wordt gebruikt en dat alle schijven zijn gemonteerd via uuid. Voor de OS-schijf moet u ervoor zorgen dat de bootloader-vermelding ook de uuid-gebaseerde mount weerspiegelt.
>
>

---
#### <a name="preparation-for-deploying-a-vm-with-a-customer-specific-image-for-sap"></a><a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Voorbereiding voor het implementeren van een VM met een klantspecifieke afbeelding voor SAP

VHD-bestanden die een algemeen besturingssysteem bevatten, worden opgeslagen in containers op Azure Storage Accounts of als Beheerde schijfafbeeldingen. U een nieuwe virtuele machine uit een dergelijke afbeelding implementeren door de afbeelding VHD of Managed Disk te verwijzen als bron in uw implementatiesjabloonbestanden zoals beschreven in hoofdstuk [Scenario 2: Een VM implementeren met een aangepaste afbeelding voor SAP][deployment-guide-3.3] van de [implementatiehandleiding][deployment-guide].

Vereisten bij het voorbereiden van uw eigen Azure VM Image zijn:

* Oorspronkelijk zou de VHD met het besturingssysteem een maximale grootte van 127 GB alleen kunnen hebben. Deze beperking werd eind maart 2015 opgeheven. Nu kan de VHD met het besturingssysteem tot 1 TB groot zijn, net als elke andere Azure Storage gehoste VHD.
* Het moet in de vaste VHD-formaat. Dynamische VHD's of VHD's in VHDx-indeling worden nog niet ondersteund op Azure. Dynamische VHD's worden geconverteerd naar statische VHD's wanneer u de VHD uploadt met PowerShell-commandlets of CLI
* VHD's, die zijn gemonteerd op de VM en moet opnieuw worden gemonteerd in Azure aan de VM moeten worden in een vaste VHD-formaat ook. Lees [dit artikel (Linux)](../../windows/managed-disks-overview.md) en [dit artikel (Windows)](../../linux/managed-disks-overview.md) voor grootte grenzen van data schijven. Dynamische VHD's worden geconverteerd naar statische VHD's wanneer u de VHD uploadt met PowerShell-commandlets of CLI
* Voeg andere lokale accounts toe, omdat deze mogelijk nodig zijn voor het specifieke implementatiescenario.
* Als de afbeelding een installatie van SAP NetWeaver bevat en de naam van de hostnaam van de oorspronkelijke naam op het punt van de Azure-implementatie waarschijnlijk wijzigt, wordt aanbevolen om de nieuwste versies van de SAP Software Provisioning Manager-DVD naar de sjabloon te kopiëren. Hierdoor u eenvoudig de door SAP verstrekte naamswijzigingsfunctionaliteit gebruiken om de gewijzigde hostnaam aan te passen en/of de SID van het SAP-systeem binnen de geïmplementeerde VM-afbeelding te wijzigen zodra een nieuwe kopie wordt gestart.

---
> ![Windows][Logo_Windows] Windows
>
> Zorg ervoor dat station D:\ wordt niet gebruikt Schijf automatisch instellen voor gekoppelde schijven zoals beschreven in hoofdstuk [Automatisch monteren voor bijgevoegde schijven][planning-guide-5.5.3] in dit document.
>
> ![Linux][Logo_Linux] Linux
>
> Zorg ervoor dat / mnt / resource niet wordt gebruikt en dat alle schijven zijn gemonteerd via uuid. Voor de OS-schijf moet u ervoor zorgen dat de bootloader-vermelding ook de uuid-gebaseerde mount weerspiegelt.
>
>

---
* SAP GUI (voor administratieve en installatiedoeleinden) kan vooraf worden geïnstalleerd in een dergelijke sjabloon.
* Andere software die nodig is om de VM's met succes uit te voeren in cross-premises scenario's kunnen worden geïnstalleerd zolang deze software kan werken met de hernaam van de VM.

Als de VM voldoende is voorbereid om generiek te zijn en uiteindelijk onafhankelijk van accounts/gebruikers die niet beschikbaar zijn in het beoogde Azure-implementatiescenario, wordt de laatste voorbereidingsstap uitgevoerd om een dergelijke afbeelding te generaliseren.

##### <a name="generalizing-a-vm"></a>Een VM generaliseren
---
> ![Windows][Logo_Windows] Windows
>
> De laatste stap is om u aan te melden bij een VM met een Administrator-account. Open een Windows-opdrachtvenster als *beheerder*. Ga naar %windir%\windows\system32\sysprep en voer sysprep.exe uit.
> Er verschijnt een klein venster. Het is belangrijk om de optie **Generaliseren** te controleren (de standaardinstelling is niet ingeschakeld) en de afsluitoptie te wijzigen van de standaardinstelling 'Reboot' in 'afsluiten'. Deze procedure gaat ervan uit dat het sysprep-proces on-premises wordt uitgevoerd in het gastbesturingssysteem van een vm.
> Als u de procedure wilt uitvoeren met een vm die al in Azure wordt uitgevoerd, voert u de stappen uit die in [dit artikel](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)zijn beschreven.
>
> ![Linux][Logo_Linux] Linux
>
> [Een virtuele Linux-machine vastleggen om deze als Resource Manager-sjabloon te gebruiken][capture-image-linux-step-2-create-vm-image]
>
>

---
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>VM's en VHD's overbrengen tussen on-premises naar Azure
Aangezien het uploaden van VM-afbeeldingen en -schijven naar Azure niet mogelijk is via de Azure-portal, moet u Azure PowerShell-cmdlets of CLI gebruiken. Een andere mogelijkheid is het gebruik van de tool 'AzCopy'. Het hulpprogramma kan VHD's kopiëren tussen on-premises en Azure (in beide richtingen). Het kan ook VHD's kopiëren tussen Azure-regio's. Raadpleeg [deze documentatie][storage-use-azcopy] voor download en gebruik van AzCopy.

Een derde alternatief zou zijn om verschillende GUI-georiënteerde tools van derden te gebruiken. Zorg er echter voor dat deze hulpprogramma's Azure Page Blobs ondersteunen. Voor onze doeleinden moeten we azure page blob-winkel gebruiken <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>(de verschillen worden hier beschreven: ). Ook de tools van Azure zijn efficiënt in het comprimeren van de VM's en VHD's, die moeten worden geüpload. Dit is belangrijk omdat deze efficiëntie in compressie de uploadtijd vermindert (die toch varieert afhankelijk van de uploadlink naar het internet vanaf de on-premises faciliteit en de gerichte Azure-implementatieregio). Het is een eerlijke veronderstelling dat het uploaden van een VM of VHD van Europese locatie naar de Amerikaanse Azure-datacenters langer zal duren dan het uploaden van dezelfde VM's/ VHD's naar de Europese Azure-datacenters.

#### <a name="uploading-a-vhd-from-on-premises-to-azure"></a><a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Een VHD uploaden van on-premises naar Azure
Als u een bestaande VM of VHD wilt uploaden vanuit het on-premises netwerk, moet een dergelijke VM of VHD voldoen aan de vereisten zoals vermeld in hoofdstuk [Voorbereiding voor het verplaatsen van een VM van on-premises naar Azure met een niet-gegeneraliseerde schijf][planning-guide-5.2.1] van dit document.

Een dergelijke VM hoeft NIET te worden gegeneraliseerd en kan worden geüpload in de status en vorm die het heeft na het afsluiten aan de on-premises kant. Hetzelfde geldt voor extra VHD's, die geen besturingssysteem bevatten.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Een VHD uploaden en er een Azure-schijf van maken
In dit geval willen we een VHD uploaden, met of zonder een OS erin, en deze op een VM monteren als gegevensschijf of gebruiken als OS-schijf. Dit is een proces in meerdere stappen

**Powershell**

* Meld u aan bij uw abonnement met *Connect-AzAccount*
* Het abonnement van uw context instellen met *Set-AzContext* en parameter SubscriptionId of SubscriptionName - zie<https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Upload de VHD met *Add-AzVhd* naar een Azure Storage Account - zie<https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (Optioneel) Maak een beheerde schijf van de VHD met *New-AzDisk* - zie<https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk>
* Stel de OS-schijf van een nieuwe VM-config in op de VHD- of Beheerde schijf met *Set-AzVMOSDisk* - zie<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
* Maak een nieuwe VM van de VM config met *New-AzVM* - zie<https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>
* Een gegevensschijf toevoegen aan een nieuwe virtuele machine met *Add-AzVMDataDisk* - zie<https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk>

**Azure-CLI**

* Meld u aan bij uw abonnement met *az login*
* Selecteer uw abonnement met *az-accountset -abonnement `<subscription name or id` *
* De VHD uploaden met *het uploaden van AZ-opslagblob* - zie [Azure CLI gebruiken met Azure Storage][storage-azure-cli]
* (Optioneel) Maak een beheerde schijf van de VHD met *az-schijf maken* - ziehttps://docs.microsoft.com/cli/azure/disk
* Maak een nieuwe VM met de geüploade VHD of Managed Disk als OS-schijf met *az vm create* en parameter *--attach-os-disk*
* Een gegevensschijf toevoegen aan een nieuwe VM met *az vm-schijfvastmaken* en *parameter -nieuw*

**Sjabloon**

* De VHD uploaden met PowerShell of Azure CLI
* (Optioneel) Een beheerde schijf maken vanuit de VHD met PowerShell, Azure CLI of de Azure-portal
* Implementeer de VM met een JSON-sjabloon die verwijst naar de VHD zoals weergegeven in [dit voorbeeld JSON-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) of met beheerde schijven zoals weergegeven in [dit voorbeeld JSON-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Implementatie van een VM-afbeelding
Als u een bestaande VM of VHD wilt uploaden vanuit het on-premises netwerk, moet een dergelijke VM- of VHD-afbeelding voldoen aan de vereisten die zijn vermeld in hoofdstuk [Voorbereiding voor het implementeren van een VM met een klantspecifieke afbeelding voor SAP][planning-guide-5.2.2] van dit document.

* Gebruik *sysprep* op Windows of *waagent -deprovision* on Linux om uw VM te generaliseren - zie [Sysprep Technical Reference](https://technet.microsoft.com/library/cc766049.aspx) for Windows of How to capture a Linux virtual machine to use as a Resource Manager [template][capture-image-linux-step-2-create-vm-image] for Linux
* Meld u aan bij uw abonnement met *Connect-AzAccount*
* Het abonnement van uw context instellen met *Set-AzContext* en parameter SubscriptionId of SubscriptionName - zie<https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Upload de VHD met *Add-AzVhd* naar een Azure Storage Account - zie<https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (Optioneel) Een beheerde schijfafbeelding maken vanuit de VHD met *Nieuw-AzImage* - zie<https://docs.microsoft.com/powershell/module/az.compute/new-Azimage>
* Stel de OS-schijf van een nieuwe VM-config in op de
  * VHD met *Set-AzVMOSDisk -SourceImageUri -CreateOption fromImage* - zie<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
  * Managed Disk Image *Set-AzVMSourceImage* - zie<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage>
* Maak een nieuwe VM van de VM config met *New-AzVM* - zie<https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>

**Azure-CLI**

* Gebruik *sysprep* op Windows of *waagent -deprovision* on Linux om uw VM te generaliseren - zie [Sysprep Technical Reference](https://technet.microsoft.com/library/cc766049.aspx) for Windows of How to capture a Linux virtual machine to use as a Resource Manager [template][capture-image-linux-step-2-create-vm-image] for Linux
* Meld u aan bij uw abonnement met *az login*
* Selecteer uw abonnement met *az-accountset -abonnement `<subscription name or id` *
* De VHD uploaden met *het uploaden van AZ-opslagblob* - zie [Azure CLI gebruiken met Azure Storage][storage-azure-cli]
* (Optioneel) Een beheerde schijfafbeelding maken vanuit de VHD met *az-afbeelding maken* - ziehttps://docs.microsoft.com/cli/azure/image
* Een nieuwe VM maken waarin de geüploade VHD- of Managed Disk Image wordt opgegeven als OS-schijf met *az vm maken* en parameter *--image*

**Sjabloon**

* Gebruik *sysprep* op Windows of *waagent -deprovision* on Linux om uw VM te generaliseren - zie [Sysprep Technical Reference](https://technet.microsoft.com/library/cc766049.aspx) for Windows of How to capture a Linux virtual machine to use as a Resource Manager [template][capture-image-linux-step-2-create-vm-image] for Linux
* De VHD uploaden met PowerShell of Azure CLI
* (Optioneel) Een beheerde schijfafbeelding maken vanuit de VHD met PowerShell, Azure CLI of de Azure-portal
* Implementeer de VM met een JSON-sjabloon die verwijst naar de afbeelding VHD zoals weergegeven in [dit voorbeeld JSON-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) of gebruik de beheerde schijfafbeelding zoals weergegeven in [dit voorbeeld JSON-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>VHD's of beheerde schijven downloaden naar on-premises
Azure Infrastructure as a Service is geen eenrichtingsverkeer om alleen VHD's en SAP-systemen te kunnen uploaden. U SAP-systemen van Azure ook terugzetten naar de on-premises wereld.

Tijdens het downloaden kunnen de VHD's of Managed Disks niet actief zijn. Zelfs bij het downloaden van schijven, die zijn gemonteerd op VM's, de VM moet worden afgesloten en deallocated. Als u alleen de database-inhoud wilt downloaden, die vervolgens moet worden gebruikt om on-premises een nieuw systeem in te stellen en als het aanvaardbaar is dat tijdens de tijd van de download en de installatie van het nieuwe systeem het systeem in Azure nog steeds operationeel kan zijn , u een lange downtime voorkomen door een gecomprimeerde databaseback-up in een schijf uit te voeren en die schijf gewoon te downloaden in plaats van ook de VM van de osbasis te downloaden.

#### <a name="powershell"></a>PowerShell

* Een beheerde schijf downloaden  
  U moet eerst toegang krijgen tot de onderliggende blob van de beheerde schijf. Vervolgens u de onderliggende blob kopiëren naar een nieuw opslagaccount en de blob downloaden van dit opslagaccount.

  ```powershell
  $access = Grant-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

* Een VHD downloaden  
  Zodra het SAP-systeem is gestopt en de VM is uitgeschakeld, u de PowerShell-cmdlet Save-AzVhd op het on-premises doel gebruiken om de VHD-schijven terug te downloaden naar de on-premises wereld. Om dat te doen, hebt u de URL van de VHD nodig, die u vinden in de 'opslagsectie' van de Azure-portal (moet u naar het opslagaccount en de opslagcontainer waar de VHD is gemaakt) en moet u weten waar de VHD naartoe moet worden gekopieerd.

  Vervolgens u de opdracht gebruiken door de parameter SourceUri te definiëren als de URL van de VHD om te downloaden en de LocalFilePath als de fysieke locatie van de VHD (inclusief de naam). De opdracht kan er uitzien als:

  ```powerhell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Voor meer informatie over de Save-AzVhd <https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd>cmdlet, kijk hier .

#### <a name="azure-cli"></a>Azure-CLI
* Een beheerde schijf downloaden  
  U moet eerst toegang krijgen tot de onderliggende blob van de beheerde schijf. Vervolgens u de onderliggende blob kopiëren naar een nieuw opslagaccount en de blob downloaden van dit opslagaccount.

  ```azurecli
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* Een VHD downloaden   
  Zodra het SAP-systeem is gestopt en de VM is uitgeschakeld, u de Azure CLI-opdracht _azure storage blob downloaden_ op het on-premises doel gebruiken om de VHD-schijven terug te downloaden naar de on-premises wereld. Om dat te doen, moet u de naam en de container van de VHD, die u vinden in de 'Storage Section' van de Azure-portal (moet u navigeren naar het opslagaccount en de opslagcontainer waar de VHD is gemaakt) en u moet weten waar de VHD moet worden gekopieerd Aan.

  Vervolgens u de opdracht gebruiken door de parameters blob en container van de VHD te definiëren om te downloaden en de bestemming als de fysieke doellocatie van de VHD (inclusief de naam). De opdracht kan er uitzien als:

  ```azurecli
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>VM's en schijven overbrengen binnen Azure

#### <a name="copying-sap-systems-within-azure"></a>SAP-systemen binnen Azure kopiëren

Een SAP-systeem of zelfs een speciale DBMS-server die een SAP-toepassingslaag ondersteunt, zal waarschijnlijk bestaan uit meerdere schijven, die ofwel het besturingssysteem met de binaire bestanden of de gegevens en het logboekbestand(en) van de SAP-database bevatten. Noch de Azure-functionaliteit van het kopiëren van schijven, noch de Azure-functionaliteit voor het opslaan van schijven naar een lokale schijf heeft een synchronisatiemechanisme, dat meerdere schijven op een consistente manier momentopname. Daarom zou de status van de gekopieerde of opgeslagen schijven, zelfs als deze zijn gemonteerd tegen dezelfde VM anders zijn. Dit betekent dat in het concrete geval van het hebben van verschillende gegevens en logfile(s) in de verschillende schijven, de database uiteindelijk inconsistent zou zijn.

**Conclusie: Om schijven te kopiëren of op te slaan, die deel uitmaken van een SAP-systeemconfiguratie, moet u het SAP-systeem stoppen en moet u ook de geïmplementeerde VM uitschakelen. Alleen dan u de set schijven kopiëren of downloaden om een kopie van het SAP-systeem in Azure of on-premises te maken.**

Gegevensschijven kunnen worden opgeslagen als VHD-bestanden in een Azure Storage-account en kunnen rechtstreeks aan een virtuele machine worden gekoppeld of als afbeelding worden gebruikt. In dit geval wordt de VHD gekopieerd naar een andere locatie voordat deze aan de virtuele machine wordt bevestigd. De volledige naam van het VHD-bestand in Azure moet uniek zijn binnen Azure. Zoals eerder al vermeld, de naam is een soort van drie-delige naam die eruit ziet als:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Gegevensschijven kunnen ook beheerschijven zijn. In dit geval wordt de beheerschijf gebruikt om een nieuwe beheerde schijf te maken voordat deze aan de virtuele machine wordt gekoppeld. De naam van de beheerde schijf moet uniek zijn binnen een resourcegroep.

##### <a name="powershell"></a>PowerShell

U Azure PowerShell-cmdlets gebruiken om een VHD te kopiëren zoals in [dit artikel][storage-powershell-guide-full-copy-vhd]wordt weergegeven. Als u een nieuwe beheerde schijf wilt maken, gebruikt u Nieuw-AzDiskConfig en Nieuw-AzDisk zoals in het volgende voorbeeld wordt weergegeven.

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>Azure-CLI

U Azure CLI gebruiken om een VHD te kopiëren. Als u een nieuwe beheerde schijf wilt maken, gebruikt u *de az-schijf maken* zoals in het volgende voorbeeld wordt weergegeven.

```azurecli
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Azure-opslaghulpprogramma's

* <https://storageexplorer.com/>

Professionele edities van Azure Storage Explorers zijn hier te vinden:

* <https://www.cerebrata.com/>
* <https://clumsyleaf.com/products/cloudxplorer>

De kopie van een VHD zelf binnen een opslagaccount is een proces, dat slechts een paar seconden duurt (vergelijkbaar met SAN-hardware die snapshots maakt met luie kopie en kopie op schrijven). Nadat u een kopie van het VHD-bestand hebt, u het aan een virtuele machine koppelen of als afbeelding gebruiken om kopieën van de VHD aan virtuele machines te koppelen.

##### <a name="powershell"></a>PowerShell

```powershell
# attach a vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a copy of the vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzVM

# attach a copy of the managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzVM
```

##### <a name="azure-cli"></a>Azure-CLI

```azurecli

# attach a vhd to a vm
az vm unmanaged-disk attach --resource-group <resource group name> --vm-name <vm name> --vhd-uri <path to vhd>

# attach a managed disk to a vm
az vm disk attach --resource-group <resource group name> --vm-name <vm name> --disk <managed disk id>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.

# attach a copy of a managed disk to a vm
az disk create --name <new disk name> --resource-group <resource group name> --location <location of target virtual machine> --source <source managed disk id>
az vm disk attach --disk <new disk name or managed disk id> --resource-group <resource group name> --vm-name <vm name> --caching <caching option> --lun <lun, for example 0>
```

#### <a name="copying-disks-between-azure-storage-accounts"></a><a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Schijven kopiëren tussen Azure Storage-accounts
Deze taak kan niet worden uitgevoerd op de Azure-portal. U Azure PowerShell-cmdlets, Azure CLI of een opslagbrowser van derden gebruiken. De PowerShell-cmdlets- of CLI-opdrachten kunnen blobs maken en beheren, waaronder de mogelijkheid om blobs asynchroon te kopiëren in opslagaccounts en in verschillende regio's binnen het Azure-abonnement.

##### <a name="powershell"></a>PowerShell
U ook VHD's kopiëren tussen abonnementen. Voor meer informatie, lees [dit artikel][storage-powershell-guide-full-copy-vhd].

De basisstroom van de PS-cmdlet-logica ziet er als volgt uit:

* Een opslagaccountcontext maken voor het **bronopslagaccount** met *New-AzStorageContext* - zie<https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Een opslagaccountcontext maken voor het **doelopslagaccount** met *New-AzStorageContext* - zie<https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* De kopie starten met

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Controleer de status van de kopie in een lus met

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Bevestig de nieuwe VHD aan een virtuele machine zoals hierboven beschreven.

Zie bijvoorbeeld [dit artikel][storage-powershell-guide-full-copy-vhd].

##### <a name="azure-cli"></a>Azure-CLI
* De kopie starten met

```azurecli
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Controleer de status of de kopie zich nog in een lus bevindt met

```azurecli
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Bevestig de nieuwe VHD aan een virtuele machine zoals hierboven beschreven.

### <a name="disk-handling"></a>Schijfafhandeling

#### <a name="vmdisk-structure-for-sap-deployments"></a><a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>VM/diskstructuur voor SAP-implementaties

Idealiter moet de behandeling van de structuur van een VM en de bijbehorende schijven eenvoudig zijn. In on-premises installaties ontwikkelden klanten vele manieren om een serverinstallatie te structureren.

* Eén basisschijf, die het besturingssysteem en alle binaire bestanden van de DBMS en/of SAP bevat. Sinds maart 2015 kan deze schijf maximaal 1 TB groot zijn in plaats van eerdere beperkingen die hem beperkten tot 127 GB.
* Een of meer schijven, die het DBMS-logboekbestand van de SAP-database en het logboekbestand van het TIJDELIJKE OPSLAGgebied DBMS bevatten (als de DBMS dit ondersteunt). Als de IOPS-vereisten voor databaselogboek hoog zijn, moet u meerdere schijven strepen om het vereiste IOPS-volume te bereiken.
* Een aantal schijven met een of twee databasebestanden van de SAP-database en de DBMS temp-gegevensbestanden (als de DBMS dit ondersteunt).

![Referentieconfiguratie van Azure IaaS VM voor SAP][planning-guide-figure-1300]


---
> ![Windows][Logo_Windows] Windows
>
> Bij veel klanten zagen we configuraties waarbij bijvoorbeeld SAP- en DBMS-binaries niet op de c:\ schijf waar het besturingssysteem is geïnstalleerd. Er waren verschillende redenen voor dit, maar toen we terug naar de wortel gingen, was het meestal dat de schijven klein waren en OS upgrades extra ruimte nodig 10-15 jaar geleden. Beide voorwaarden gelden tegenwoordig niet meer. Vandaag de c:\ schijf kan worden toegewezen op schijven met een groot volume of VM's. Om implementaties eenvoudig te houden in hun structuur, wordt aanbevolen om het volgende implementatiepatroon voor SAP NetWeaver-systemen in Azure te volgen
>
> Het paginabestand van het Windows-besturingssysteem moet zich op het D:station (niet-persistente schijf)
>
> ![Linux][Logo_Linux] Linux
>
> Plaats de Linux swapfile onder /mnt /mnt/resource op Linux zoals beschreven in [dit artikel.][virtual-machines-linux-agent-user-guide] Het swapbestand kan worden geconfigureerd in het configuratiebestand van de Linux Agent /etc/waagent.conf. De volgende instellingen toevoegen of wijzigen:
>
>

```console
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Om de wijzigingen te activeren, moet u de Linux-agent opnieuw starten met

```console
sudo service waagent restart
```

Lees SAP Note [1597355] voor meer informatie over de aanbevolen swap bestandsgrootte

---
Het aantal schijven dat wordt gebruikt voor de DBMS-gegevensbestanden en het type Azure-opslag waarop deze schijven worden gehost, moet worden bepaald door de IOPS-vereisten en de vereiste latentie. Exacte quota worden beschreven in [dit artikel (Linux)][virtual-machines-sizes-linux] en [dit artikel (Windows)][virtual-machines-sizes-windows].

De ervaring met SAP-implementaties in de afgelopen twee jaar heeft ons een aantal lessen geleerd, die kunnen worden samengevat als:

* IOPS-verkeer naar verschillende gegevensbestanden is niet altijd hetzelfde, omdat bestaande klantsystemen mogelijk gegevensbestanden van verschillende grootte hebben die hun SAP-database(s) vertegenwoordigen. Als gevolg daarvan bleek het beter met behulp van een RAID-configuratie over meerdere schijven om de gegevensbestanden LUN's gesneden uit deze te plaatsen. Er waren situaties, vooral met Azure Standard Storage waarbij een IOPS-snelheid het quotum van een enkele schijf raakte ten opzichte van het DBMS-transactielogboek. In dergelijke scenario's wordt het gebruik van Premium Storage aanbevolen of wordt meerdere standaardopslagschijven met een softwarestreep aggregeren.

---
> ![Windows][Logo_Windows] Windows
>
> * [Aanbevolen procedures voor prestaties voor SQL Server op virtuele machines van Azure][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Software RAID configureren op Linux][virtual-machines-linux-configure-raid]
> * [LVM configureren op een Linux-vm in Azure][virtual-machines-linux-configure-lvm]
> * [Azure Storage geheimen en Linux I/O optimalisaties](https://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

---
* Premium Storage laat aanzienlijk betere prestaties zien, vooral voor het schrijven van kritieke transacties. Voor SAP-scenario's die naar verwachting productie-achtige prestaties opleveren, is het ten zeerste aan te raden om VM-series te gebruiken die gebruik kunnen maken van Azure Premium Storage.

Houd er rekening mee dat de schijf, die het besturingssysteem bevat, en zoals we aanbevelen, de binaire bestanden van SAP en de database (base VM) ook niet meer beperkt is tot 127GB. Het kan nu tot 1 TB groot zijn. Dit moet voldoende ruimte zijn om al het benodigde bestand te bewaren, inclusief bijvoorbeeld SAP batch job logs.

Raadpleeg de [DBMS Deployment Guide][dbms-guide] voor meer suggesties en meer details, specifiek voor DBMS VM's

#### <a name="disk-handling"></a>Schijfafhandeling

In de meeste scenario's moet u extra schijven maken om de SAP-database in de VM te implementeren. We spraken over de overwegingen over het aantal schijven in hoofdstuk [VM / schijfstructuur voor SAP-implementaties][planning-guide-5.5.1] van dit document. De Azure-portal maakt het mogelijk schijven te koppelen en los te maken zodra een basis-VM is geïmplementeerd. De schijven kunnen worden bevestigd / losgemaakt wanneer de VM is up and running en wanneer het wordt gestopt. Bij het koppelen van een schijf biedt de Azure-portal aan om een lege schijf of een bestaande schijf te koppelen, die op dit moment niet is gekoppeld aan een andere vm.

**Opmerking:** Schijven kunnen slechts op een bepaald moment aan één VM worden gekoppeld.

![Schijven koppelen / loskoppelen met Azure Standard Storage][planning-guide-figure-1400]

Tijdens de implementatie van een nieuwe virtuele machine u bepalen of u Beheerde schijven wilt gebruiken of uw schijven op Azure-opslagaccounts wilt plaatsen. Als u Premium Storage wilt gebruiken, raden we u aan Beheerde schijven te gebruiken.

Vervolgens moet u beslissen of u een nieuwe en lege schijf wilt maken of dat u een bestaande schijf wilt selecteren die eerder is geüpload en nu aan de vm moet worden gekoppeld.

**BELANGRIJK:** U wilt **hostcaching niet** gebruiken met Azure Standard Storage. U moet de voorkeur voor hostcache laten bij de standaardinstelling GEEN. Met Azure Premium Storage moet u Read Caching inschakelen als het I/O-kenmerk meestal wordt gelezen als typisch I/O-verkeer tegen databasegegevensbestanden. In het geval van database transactie log bestand, geen caching wordt aanbevolen.

---
> ![Windows][Logo_Windows] Windows
>
> [Een gegevensschijf koppelen aan de Azure-portal][virtual-machines-linux-attach-disk-portal]
>
> Als er schijven zijn aangesloten, moet u zich aanmelden bij de VM om Windows Disk Manager te openen. Als automount niet is ingeschakeld zoals aanbevolen in hoofdstuk [Instelling automatisch monteren voor aangesloten schijven,][planning-guide-5.5.3]moet het nieuw bijgevoegde volume online worden genomen en geinitialiseerd.
>
> ![Linux][Logo_Linux] Linux
>
> Als er schijven zijn gekoppeld, moet u zich aanmelden bij de VM en de schijven initialiseren zoals beschreven in [dit artikel][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

---
Als de nieuwe schijf een lege schijf is, moet u de schijf ook opmaken. Voor opmaak, met name voor DBMS-gegevens en logbestanden, gelden dezelfde aanbevelingen als voor kale-metaalimplementaties van de DBMS.

Een Azure Storage-account biedt geen oneindige resources in termen van I/O-volume, IOPS en gegevensvolume. Meestal DBMS VM's worden het meest beïnvloed door deze. Het is misschien het beste om voor elke VM een apart opslagaccount te gebruiken als u weinig VM's met een hoog I/O-volume hebt om te implementeren om binnen de limiet van het Azure Storage Account-volume te blijven. Anders moet u zien hoe u deze VM's balanceren tussen verschillende opslagaccounts zonder de limiet van elk opslagaccount te bereiken. Meer details worden besproken in de [DBMS Deployment Guide][dbms-guide]. Houd ook rekening met deze beperkingen voor pure SAP-toepassingsserverVM's of andere VM's, waarvoor uiteindelijk mogelijk extra VHD's nodig zijn. Deze beperkingen zijn niet van toepassing als u Managed Disk gebruikt. Als u Premium Storage wilt gebruiken, raden we u aan Managed Disk te gebruiken.

Een ander onderwerp, dat relevant is voor opslagaccounts, is of de VHD's in een opslagaccount geo-gerepliceerd worden. Georeplicatie is ingeschakeld of uitgeschakeld op het niveau van het opslagaccount en niet op VM-niveau. Als geo-replicatie is ingeschakeld, worden de VHD's in het opslagaccount gerepliceerd naar een ander Azure-datacenter binnen dezelfde regio. Voordat u hierover beslist, moet u nadenken over de volgende beperking:

Azure Geo-replicatie werkt lokaal op elke VHD in een VM en repliceert de IOs niet in chronologische volgorde over meerdere VHD's in een VM. Daarom worden de VHD die de basis-VM vertegenwoordigt en eventuele extra VHD's die aan de VM zijn gekoppeld, onafhankelijk van elkaar gerepliceerd. Dit betekent dat er geen synchronisatie is tussen de wijzigingen in de verschillende VHD's. Het feit dat de IOs onafhankelijk van de volgorde waarin ze zijn geschreven worden gerepliceerd, betekent dat geo-replicatie niet van waarde is voor databaseservers die hun databases over meerdere VHD's hebben verdeeld. Naast de DBMS kunnen er ook andere toepassingen zijn waarbij processen gegevens in verschillende VHD's schrijven of manipuleren en waar het belangrijk is om de volgorde van wijzigingen te behouden. Als dat een vereiste is, moet georeplicatie in Azure niet worden ingeschakeld. Afhankelijk van of u geo-replicatie nodig hebt of wilt voor een set VM's, maar niet voor een andere set, u vm's en bijbehorende VHD's al categoriseren in verschillende opslagaccounts die geo-replicatie hebben ingeschakeld of uitgeschakeld.

#### <a name="setting-automount-for-attached-disks"></a><a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Automatisch vaststellen instellen voor aangesloten schijven
---
> ![Windows][Logo_Windows] Windows
>
> Voor VM's, die zijn gemaakt van eigen afbeeldingen of schijven, is het noodzakelijk om de parameter automount te controleren en eventueel in te stellen. Als u deze parameter instelt, kan de VM na een herstart of herschikking in Azure de aangesloten/gemonteerde schijven automatisch weer monteren.
> De parameter is ingesteld voor de afbeeldingen die door Microsoft in de Azure Marketplace worden geleverd.
>
> Om de automount in te stellen, controleert u hier de documentatie van de uitvoerbare opdrachtregel.exe:
>
> * [Opdrachtregelopties voor DiskPart](https://technet.microsoft.com/library/bb490893.aspx)
> * [Automount](https://technet.microsoft.com/library/cc753703.aspx)
>
> Het Windows-opdrachtregelvenster moet worden geopend als beheerder.
>
> Als er schijven zijn aangesloten, moet u zich aanmelden bij de VM om Windows Disk Manager te openen. Als automount niet is ingeschakeld zoals aanbevolen in hoofdstuk [Instelling automatisch monteren voor aangesloten schijven,][planning-guide-5.5.3]moet het nieuw aangesloten volume >online worden genomen en geinitialiseerd.
>
> ![Linux][Logo_Linux] Linux
>
> U moet een nieuw bijgevoegde lege schijf initialiseren zoals beschreven in [dit artikel.][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
> Je moet ook nieuwe schijven toe te voegen aan de / etc / fstab.
>
>

---
### <a name="final-deployment"></a>Definitieve implementatie

Raadpleeg voor de uiteindelijke implementatie en exacte stappen, met name met betrekking tot de implementatie van de Azure Extension voor SAP, de [implementatiehandleiding][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Toegang tot SAP-systemen die worden uitgevoerd in Azure VM's

Voor scenario's waarin u verbinding wilt maken met die SAP-systemen via het openbare internet met SAP GUI, moeten de volgende procedures worden toegepast.

Later in het document bespreken we het andere belangrijke scenario, waarbij we verbinding maken met SAP-systemen in cross-premises implementaties, die een vpn-tunnel (site-to-site) of Azure ExpressRoute-verbinding hebben tussen de on-premises systemen en Azure-systemen.

### <a name="remote-access-to-sap-systems"></a>Externe toegang tot SAP-systemen

Met Azure Resource Manager zijn er geen standaardeindpunten meer zoals in het vorige klassieke model. Alle poorten van een Azure Resource Manager VM zijn geopend zolang:

1. Er is geen netwerkbeveiligingsgroep gedefinieerd voor het subnet of de netwerkinterface. Netwerkverkeer naar Azure VM's kan worden beveiligd via zogenaamde "Network Security Groups". Zie voor meer informatie [Wat is een netwerkbeveiligingsgroep (NSG)?][virtual-networks-nsg]
2. Er is geen Azure Load Balancer gedefinieerd voor de netwerkinterface   

Zie het architectuurverschil tussen klassiek model en ARM zoals beschreven in [dit artikel.][virtual-machines-azure-resource-manager-architecture]

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>Configuratie van de SAP-systeem- en SAP GUI-connectiviteit via internet

Zie dit artikel, waarin details van dit onderwerp worden beschreven:<https://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>Firewall-instellingen binnen VM wijzigen

Het kan nodig zijn om de firewall op uw virtuele machines te configureren om binnenkomend verkeer naar uw SAP-systeem toe te staan.

---
> ![Windows][Logo_Windows] Windows
>
> Standaard is de Windows Firewall binnen een door Azure geïmplementeerde VM ingeschakeld. U moet nu toestaan dat de SAP-poort wordt geopend, anders kan de SAP GUI geen verbinding maken.
> Om dit te doen:
>
> * Open Configuratiescherm\Systeem en beveiliging\Windows Firewall naar **geavanceerde instellingen**.
> * Klik nu met een rechtermuisknop op Binnenkomende regels en kies **Nieuwe regel**.
> * In de volgende wizard is gekozen om een nieuwe **poortregel** te maken.
> * Laat in de volgende stap van de wizard de instelling bij TCP staan en typ het poortnummer dat u wilt openen. Aangezien onze SAP instance ID 00 is, hebben we er 3200 genomen. Als uw instantie een ander instantienummer heeft, moet de poort die u eerder hebt gedefinieerd op basis van het instantienummer worden geopend.
> * In het volgende deel van de wizard moet u de **item Verbinding toestaan** laten controleren.
> * In de volgende stap van de wizard moet u bepalen of de regel van toepassing is op domein- en privénetwerk. Gelieve aan te passen indien nodig aan uw behoeften. Als u echter van buitenaf verbinding maakt met SAP GUI via het openbare netwerk, moet u de regel op het openbare netwerk hebben toegepast.
> * Geef in de laatste stap van de wizard een naam aan de regel en sla op door op **Voltooien**te drukken.
>
> De regel wordt onmiddellijk van kracht.
>
> ![Definitie van poortregel][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> De Linux-afbeeldingen in de Azure Marketplace schakelen de iptables-firewall niet standaard in en de verbinding met uw SAP-systeem zou moeten werken. Als u iptables of een andere firewall hebt ingeschakeld, raadpleegt u de documentatie van iptables of de gebruikte firewall om binnenkomend tcp-verkeer toe te staan aan poort 32xx (waarbij xx het systeemnummer van uw SAP-systeem is).
>
>

---
#### <a name="security-recommendations"></a>Aanbevelingen voor beveiliging

De SAP GUI maakt niet onmiddellijk verbinding met een van de SAP-exemplaren (poort 32xx) die worden uitgevoerd, maar maakt eerst verbinding via de poort die is geopend voor het SAP-berichtenserverproces (poort 36xx). In het verleden werd dezelfde poort gebruikt door de berichtenserver voor de interne communicatie naar de toepassingsinstanties. Om te voorkomen dat on-premises toepassingsservers per ongeluk communiceren met een berichtenserver in Azure, kunnen de interne communicatiepoorten worden gewijzigd. Het wordt ten zeerste aanbevolen om de interne communicatie tussen de SAP-berichtenserver en de toepassingsinstanties ervan te wijzigen in een ander poortnummer op systemen die zijn gekloond uit on-premises systemen, zoals een kloon van ontwikkeling voor projecttests enz. Dit kan met de standaardprofielparameter:

> rdisp/msserv_internal
>
>

zoals gedocumenteerd in [beveiligingsinstellingen voor de SAP Message Server](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)


### <a name="single-vm-with-sap-netweaver-demotraining-scenario"></a><a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Eén VM met SAP NetWeaver-demo/trainingsscenario

![Uitvoeren van enkele VM SAP-demosystemen met dezelfde VM-namen, geïsoleerd in Azure Cloud Services][planning-guide-figure-1700]

In dit scenario implementeren we een typisch trainings-/demosysteemscenario waarin het volledige trainings-/demoscenario in één VM is opgenomen. We gaan ervan uit dat de implementatie wordt uitgevoerd via VM-afbeeldingssjablonen. We gaan er ook van uit dat meerdere van deze demo/trainingen VM's moeten worden ingezet met de VM's met dezelfde naam. De hele trainingssystemen hebben geen verbinding met uw on-premises assets en zijn het tegenovergestelde van een hybride implementatie.

De aanname is dat u een VM-afbeelding hebt gemaakt zoals beschreven in sommige secties van hoofdstuk [Vm's voorbereiden met SAP voor Azure][planning-guide-5.2] in dit document.

De volgorde van gebeurtenissen om het scenario te implementeren ziet er als volgt uit:

##### <a name="powershell"></a>PowerShell

* Maak een nieuwe resourcegroep voor elk trainings-/demolandschap

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```

* Een nieuw opslagaccount maken als u managed disks niet wilt gebruiken

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Maak een nieuw virtueel netwerk voor elk trainings-/demolandschap om het gebruik van dezelfde hostname en IP-adressen mogelijk te maken. Het virtuele netwerk wordt beschermd door een netwerkbeveiligingsgroep waarmee alleen verkeer naar poort 3389 kan worden poort om toegang tot extern bureaublad en poort 22 voor SSH in te schakelen.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Maak een nieuw openbaar IP-adres dat kan worden gebruikt om toegang te krijgen tot de virtuele machine vanaf het internet

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Een nieuwe netwerkinterface voor de virtuele machine maken

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Hiermee maakt u een virtuele machine. Voor dit scenario heeft elke virtuele machine dezelfde naam. De SAP SID van de SAP NetWeaver-exemplaren in die VM's zal ook hetzelfde zijn. Binnen de Azure Resource Group moet de naam van de VM uniek zijn, maar in verschillende Azure Resource Groups u VM's met dezelfde naam uitvoeren. Het standaard 'Administrator'-account van Windows of 'root' voor Linux is niet geldig. Daarom moet een nieuwe beheerdersgebruikersnaam samen met een wachtwoord worden gedefinieerd. De grootte van de VM moet ook worden gedefinieerd.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Voeg optioneel extra schijven toe en herstel de benodigde inhoud. Alle blobnamen (URL's voor de blobs) moeten uniek zijn binnen Azure.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzVM

# Optional: Attach additional Managed Disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzVM
```

##### <a name="cli"></a>CLI

De volgende voorbeeldcode kan worden gebruikt op Linux. Gebruik voor Windows PowerShell zoals hierboven beschreven of pas het voorbeeld aan om %rgName% *set*te gebruiken in plaats van $rgName en stel de omgevingsvariabele in met de Windows-opdrachtset .

* Maak een nieuwe resourcegroep voor elk trainings-/demolandschap

```azurecli
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Een nieuw opslagaccount maken

```azurecli
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Maak een nieuw virtueel netwerk voor elk trainings-/demolandschap om het gebruik van dezelfde hostname en IP-adressen mogelijk te maken. Het virtuele netwerk wordt beschermd door een netwerkbeveiligingsgroep waarmee alleen verkeer naar poort 3389 kan worden poort om toegang tot extern bureaublad en poort 22 voor SSH in te schakelen.

```azurecli
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Maak een nieuw openbaar IP-adres dat kan worden gebruikt om toegang te krijgen tot de virtuele machine vanaf het internet

```azurecli
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Een nieuwe netwerkinterface voor de virtuele machine maken

```azurecli
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Hiermee maakt u een virtuele machine. Voor dit scenario heeft elke virtuele machine dezelfde naam. De SAP SID van de SAP NetWeaver-exemplaren in die VM's zal ook hetzelfde zijn. Binnen de Azure Resource Group moet de naam van de VM uniek zijn, maar in verschillende Azure Resource Groups u VM's met dezelfde naam uitvoeren. Het standaard 'Administrator'-account van Windows of 'root' voor Linux is niet geldig. Daarom moet een nieuwe beheerdersgebruikersnaam samen met een wachtwoord worden gedefinieerd. De grootte van de VM moet ook worden gedefinieerd.

```azurecli
#####
# Create virtual machines using storage accounts
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password

#####
# Create virtual machines using Managed Disks
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
```

```azurecli
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd> --authentication-type password

#####
# Create a new virtual machine with a Managed Disk Image
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id> --authentication-type password
```

* Voeg optioneel extra schijven toe en herstel de benodigde inhoud. Alle blobnamen (URL's voor de blobs) moeten uniek zijn binnen Azure.

```azurecli
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Template

U de voorbeeldsjablonen gebruiken in de azure-quickstart-templates-repository op GitHub.

* [Eenvoudige Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Eenvoudige Windows-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [VM van afbeelding](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Een set VM's implementeren die communiceren binnen Azure

Dit niet-hybride scenario is een typisch scenario voor trainings- en demodoeleinden waarbij de software die het demo/trainingsscenario vertegenwoordigt, is verspreid over meerdere VM's. De verschillende componenten die in de verschillende VM's zijn geïnstalleerd, moeten met elkaar communiceren. Nogmaals, in dit scenario is geen on-premises netwerkcommunicatie of cross-premises scenario nodig.

Dit scenario is een uitbreiding van de installatie beschreven in hoofdstuk [Single VM met SAP NetWeaver demo/trainingsscenario][planning-guide-7.1] van dit document. In dit geval worden meer virtuele machines toegevoegd aan een bestaande resourcegroep. In het volgende voorbeeld bestaat het trainingslandschap uit een SAP ASCS/SCS VM, een VM met een DBMS en een SAP Application Server instance VM.

Voordat u dit scenario bouwt, moet u nadenken over basisinstellingen zoals die al eerder in het scenario zijn uitgeoefend.

#### <a name="resource-group-and-virtual-machine-naming"></a>Resourcegroep en naamgeving van virtuele machines

Alle namen van resourcegroepen moeten uniek zijn. Ontwikkel uw eigen naamgevingsschema van `<rg-name` uw resources, zoals>-achtervoegsel.

De naam van de virtuele machine moet uniek zijn binnen de resourcegroep.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Netwerk instellen voor communicatie tussen de verschillende VM's

![Set VM's binnen een Azure Virtual Network][planning-guide-figure-1900]

Om naamgevingsbotsingen met klonen van dezelfde trainings-/demolandschappen te voorkomen, moet u voor elk liggend landschap een Azure Virtual Network maken. DNS-naamomzetting wordt geleverd door Azure of u uw eigen DNS-server buiten Azure configureren (om hier niet verder te worden besproken). In dit scenario configureren we onze eigen DNS niet. Voor alle virtuele machines in één Azure Virtual Network wordt communicatie via hostnamen ingeschakeld.

De redenen om trainings- of demolandschappen te scheiden door virtuele netwerken en niet alleen brongroepen kunnen zijn:

* Het SAP-landschap zoals ingesteld heeft een eigen AD/OpenLDAP nodig en een domeinserver moet deel uitmaken van elk van de landschappen.  
* Het SAP-landschap zoals ingesteld, heeft componenten die moeten werken met vaste IP-adressen.

Meer informatie over Azure Virtual Networks en hoe ze te definiëren zijn te vinden in [dit artikel.][virtual-networks-create-vnet-arm-pportal]

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>SAP VM's implementeren met Corporate Network Connectivity (Cross-Premises)

U voert een SAP-landschap uit en wilt de implementatie verdelen tussen bare-metal voor high-end DBMS-servers, on-premises gevirtualiseerde omgevingen voor toepassingslagen en kleinere 2-tier geconfigureerde SAP-systemen en Azure IaaS. De basisveronderstelling is dat SAP-systemen binnen één SAP-landschap met elkaar en met vele andere softwarecomponenten die in het bedrijf worden geïmplementeerd, onafhankelijk van hun implementatievorm, met elkaar moeten communiceren. Er mogen ook geen verschillen worden geïntroduceerd door het implementatieformulier voor de eindgebruiker die verbinding maakt met SAP GUI of andere interfaces. Aan deze voorwaarden kan alleen worden voldaan als de on-premises Active Directory/OpenLDAP- en DNS-services zijn uitgebreid naar de Azure-systemen via site-to-site/multi-site connectiviteit of privéverbindingen zoals Azure ExpressRoute.



### <a name="scenario-of-an-sap-landscape"></a>Scenario van een SAP-landschap

De cross-premises of hybride scenario kan ruwweg worden beschreven zoals in de onderstaande afbeeldingen:

![Site-to-Site-connectiviteit tussen on-premises en Azure-assets][planning-guide-figure-2100]

De minimale vereiste is het gebruik van beveiligde communicatieprotocollen zoals SSL/TLS voor browsertoegang of VPN-gebaseerde verbindingen voor systeemtoegang tot de Azure-services. De aanname is dat bedrijven de VPN-verbinding tussen hun bedrijfsnetwerk en Azure anders behandelen. Sommige bedrijven kunnen alle poorten leeg openen. Sommige andere bedrijven willen misschien precies zijn in welke havens ze moeten openen, enz.

In de onderstaande tabel worden typische SAP-communicatiepoorten vermeld. In principe is het voldoende om de SAP-gatewaypoort te openen.

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| Service | Poortnaam | Voorbeeld `<nn`> = 01 | Standaardbereik (min-max) | Opmerking |
| --- | --- | --- | --- | --- |
| Dispatcher |sapdp`<nn>` zie * |3201 |3200 - 3299 |SAP Dispatcher, gebruikt door SAP GUI voor Windows en Java |
| Berichtenserver |sapms`<sid`> zien ** |3600 |gratis sapmen`<anySID`> |sid = SAP-System-ID |
| Gateway |sapgw`<nn`> zien * |3301 |Gratis |SAP-gateway, gebruikt voor CPIC- en RFC-communicatie |
| SAP-router |sapdp99 |3299 |Gratis |Alleen CI (centrale instantie) Servicenamen kunnen na installatie in /etc/services worden toegewezen aan een willekeurige waarde. |

*) nn = SAP-instantienummer

**) sid = SAP-System-ID

Meer gedetailleerde informatie over poorten die nodig zijn voor verschillende <https://scn.sap.com/docs/DOC-17124>SAP-producten of -diensten van SAP-producten vindt u hier.
Met dit document moet u speciale poorten in het VPN-apparaat kunnen openen die nodig zijn voor specifieke SAP-producten en -scenario's.

Andere beveiligingsmaatregelen bij het implementeren van VM's in een dergelijk scenario kunnen zijn om een [netwerkbeveiligingsgroep][virtual-networks-nsg] te maken om toegangsregels te definiëren.



#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Afdrukken op een lokale netwerkprinter vanuit SAP-instantie in Azure

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Afdrukken via TCP/IP in cross-premises scenario

Het instellen van uw on-premises TCP/IP-gebaseerde netwerkprinters in een Azure VM is over het algemeen hetzelfde als in uw bedrijfsnetwerk, ervan uitgaande dat u een VPN Site-To-Site-tunnel of ExpressRoute-verbinding tot stand hebt gebracht.

---
> ![Windows][Logo_Windows] Windows
>
> Om dit te doen:
>
> * Sommige netwerkprinters worden geleverd met een configuratiewizard waarmee u uw printer eenvoudig instellen in een Azure VM. Als er geen wizardsoftware met de printer is gedistribueerd, u de printer handmatig instellen door een nieuwe TCP/IP-printerpoort te maken.
> * Configuratiescherm openen > apparaten en printers -> een printer toevoegen
> * Een printer toevoegen met een TCP/IP-adres of hostnaam kiezen
> * Typ het IP-adres van de printer
> * Printerpoortstandaard 9100
> * Installeer indien nodig de juiste printerdriver handmatig.
>
> ![Linux][Logo_Linux] Linux
>
> * net als voor Windows gewoon de standaard procedure volgen om een netwerkprinter te installeren
> * volg gewoon de openbare Linux-gidsen voor [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) of [Red Hat en Oracle Linux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) over hoe je een printer toe te voegen.
>
>

---
![Netwerkafdrukken][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Host-based printer over SMB (shared printer) in Cross-Premises scenario

Hostprinters zijn niet door het ontwerp compatibel met het netwerk. Maar een hostprinter kan worden gedeeld tussen computers in een netwerk, zolang de printer is aangesloten op een computer met stroom. Verbind uw bedrijfsnetwerk site-to-site of ExpressRoute en deel uw lokale printer. Het SMB-protocol gebruikt NetBIOS in plaats van DNS als naamservice. De NetBIOS-hostnaam kan afwijken van de DNS-hostnaam. Het standaardgeval is dat de NetBIOS-hostnaam en de DNS-hostnaam identiek zijn. Het DNS-domein heeft geen zin in de NetBIOS-naamruimte. De volledig gekwalificeerde DNS-hostnaam, bestaande uit de DNS-hostnaam en het DNS-domein, mag daarom niet worden gebruikt in de NetBIOS-naamruimte.

Het printeraandeel wordt geïdentificeerd door een unieke naam in het netwerk:

* Hostnaam van de SMB-host (altijd nodig).
* Naam van het aandeel (altijd nodig).
* Naam van het domein als printershare zich niet in hetzelfde domein bevindt als het SAP-systeem.
* Daarnaast zijn een gebruikersnaam en een wachtwoord vereist om toegang te krijgen tot het printeraandeel.

Procedure:

---
> ![Windows][Logo_Windows] Windows
>
> Deel uw lokale printer.
> Open de Windows Verkenner in de Azure-vm en typ de naam van de printer.
> Een wizard printerinstallatie begeleidt u bij het installatieproces.
>
> ![Linux][Logo_Linux] Linux
>
> Hier volgen enkele voorbeelden van documentatie over het configureren van netwerkprinters in Linux of het opnemen van een hoofdstuk over afdrukken in Linux. Het zal op dezelfde manier werken in een Azure Linux VM zolang de VM deel uitmaakt van een VPN:
>
> * Sles<https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL of Oracle Linux<https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

---
##### <a name="usb-printer-printer-forwarding"></a>USB-printer (printer doorschakelen)

In Azure is de mogelijkheid van de Extern bureaublad-services om gebruikers toegang te geven tot hun lokale printerapparaten in een externe sessie, niet beschikbaar.

---
> ![Windows][Logo_Windows] Windows
>
> Meer details over afdrukken met Windows <https://technet.microsoft.com/library/jj590748.aspx>vindt u hier: .
>
>

---
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integratie van SAP Azure-systemen in correctie- en transportsysteem (TMS) in cross-premises

Het SAP Change and Transport System (TMS) moet worden geconfigureerd voor het exporteren en importeren van transportaanvragen voor verschillende systemen in het landschap. We gaan ervan uit dat de ontwikkelexemplaren van een SAP-systeem (DEV) zich in Azure bevinden, terwijl de kwaliteitsborging (QA) en productieve systemen (PRD) on-premises zijn. Verder gaan we ervan uit dat er een centrale transportgids is.

##### <a name="configuring-the-transport-domain"></a>Het transportdomein configureren

Configureer uw transportdomein op het systeem dat u hebt aangewezen als de Transport Domain Controller zoals beschreven in [Het configureren van de Transport Domain Controller.](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm) Er wordt een Systeemgebruiker TMSADM gemaakt en de vereiste RFC-bestemming wordt gegenereerd. U deze RFC-verbindingen controleren met behulp van de transactie SM59. Hostname-resolutie moet zijn ingeschakeld in uw transportdomein.

Procedure:

* In ons scenario hebben we besloten dat het on-premises QAS-systeem de CTS-domeincontroller zal zijn. Bel transactie STMS. Het dialoogvenster TMS wordt weergegeven. Er wordt een dialoogvenster Transportdomein configureren weergegeven. (Dit dialoogvenster wordt alleen weergegeven als u nog geen transportdomein hebt geconfigureerd.)
* Zorg ervoor dat de automatisch gemaakte gebruiker TMSADM is geautoriseerd (SM59 -> ABAP Connection -> TMSADM@E61.DOMAIN_E61 -> Details -> Utilities(M) -> Autorisatietest). Het eerste scherm van transactie STMS moet aantonen dat dit SAP-systeem nu functioneert als de controller van het transportdomein zoals hier wordt weergegeven:

![Beginscherm van transactie STMS op de domeincontroller][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>SAP-systemen opnemen in het transportdomein

De volgorde van het opnemen van een SAP-systeem in een transportdomein ziet er als volgt uit:

* Ga op het DEV-systeem in Azure naar het transportsysteem (Client 000) en bel transactieSTMS. Kies Andere configuratie in het dialoogvenster en ga verder met Systeem opnemen in domein. Geef de domeincontroller op als doelhost[(inclusief SAP-systemen in het transportdomein).](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm) Het systeem wacht nu om in het transportdomein te worden opgenomen.
* Om veiligheidsredenen moet u vervolgens teruggaan naar de domeincontroller om uw verzoek te bevestigen. Kies Systeemoverzicht en Keur het wachtsysteem goed. Bevestig vervolgens de prompt en de configuratie wordt gedistribueerd.

Dit SAP-systeem bevat nu de nodige informatie over alle andere SAP-systemen in het transportdomein. Tegelijkertijd worden de adresgegevens van het nieuwe SAP-systeem naar alle andere SAP-systemen verzonden en wordt het SAP-systeem ingevoerd in het transportprofiel van het transportcontroleprogramma. Controleer of RFC's en toegang tot de transportmap van het domein werken.

Ga verder met de configuratie van uw transportsysteem zoals gebruikelijk zoals beschreven in de documentatie [Change and Transport System.](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)

Procedure:

* Zorg ervoor dat uw STMS on-premises correct is geconfigureerd.
* Zorg ervoor dat de hostnaam van de Transport Domain Controller kan worden opgelost door uw virtuele machine op Azure en vice-visa.
* Oproeptransactie STMS -> Andere configuratie -> systeem in domein opnemen.
* Bevestig de verbinding in het on-premises TMS-systeem.
* Configureer transportroutes, groepen en lagen zoals gewoonlijk.

In met site-to-site verbonden cross-premises scenario's kan de latentie tussen on-premises en Azure nog steeds aanzienlijk zijn. Als we de volgorde volgen van het transporteren van objecten door middel van ontwikkelings- en testsystemen naar productie of nadenken over het toepassen van transporten of ondersteuningspakketten op de verschillende systemen, realiseer je je dat, afhankelijk van de locatie van de centrale transportgids, sommige van de systemen zullen te maken krijgen met gegevens over het lezen of schrijven van hoge latentie in de centrale transportmap. De situatie is vergelijkbaar met SAP-landschapsconfiguraties waarbij de verschillende systemen worden verspreid door verschillende datacenters met een aanzienlijke afstand tussen de datacenters.

Om zo'n latentie te omzeilen en de systemen snel te laten bij het lezen of schrijven van of naar de transportmap, u twee STMS-transportdomeinen instellen (één voor on-premises en één met de systemen in Azure en de transportdomeinen koppelen. Bekijk deze documentatie, waarin de principes achter dit concept <https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>worden uitgelegd in de SAP TMS: .

Procedure:

* Een transportdomein instellen op elke locatie (on-premises en Azure) met behulp van transaction STMS<https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Koppel de domeinen aan een domeinkoppeling en bevestig de koppeling tussen de twee domeinen.
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Distribueer de configuratie naar het gekoppelde systeem.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>RFC-verkeer tussen SAP-exemplaren in Azure en on-premises (Cross-Premises)

RFC-verkeer tussen systemen, die on-premises en in Azure zijn, moet werken. Als u een verbindingsgesprekstransactie SM59 wilt instellen in een bronsysteem waarbij u een RFC-verbinding naar het doelsysteem moet definiëren. De configuratie is vergelijkbaar met de standaardinstelling van een RFC-verbinding.

We gaan ervan uit dat in het cross-premises scenario de VM's, die SAP-systemen draaien die met elkaar moeten communiceren, zich in hetzelfde domein bevinden. Daarom verschilt de installatie van een RFC-verbinding tussen SAP-systemen niet van de installatiestappen en -ingangen in on-premises scenario's.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Toegang tot lokale bestandsshares vanuit SAP-exemplaren in Azure of vice versa

SAP-exemplaren in Azure moeten toegang krijgen tot bestandsshares, die zich binnen het bedrijfsgebouw bevinden. Bovendien moeten on-premises SAP-exemplaren toegang krijgen tot bestandsshares, die zich in Azure bevinden. Als u de bestandsshares wilt inschakelen, moet u de machtigingen en opties voor delen op het lokale systeem configureren. Zorg ervoor dat u de poorten opent op de VPN- of ExpressRoute-verbinding tussen Azure en uw datacenter.

## <a name="supportability"></a>Ondersteuning

### <a name="azure-extension-for-sap"></a><a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Azure-extensie voor SAP

Om een deel van de Azure-infrastructuurinformatie van bedrijfskritieke SAP-systemen te kunnen verwerken aan de SAP Host Agent-exemplaren, geïnstalleerd in VM's, moet een Azure-extensie (VM) voor SAP worden geïnstalleerd voor de geïmplementeerde VM's. Aangezien de eisen van SAP specifiek waren voor SAP-toepassingen, besloot Microsoft de vereiste functionaliteit niet generiek te implementeren in Azure, maar het aan klanten over te laten om de benodigde VM-extensie en -configuraties te implementeren op hun virtuele machines met draaien in Azure. Implementatie- en levenscyclusbeheer van de Azure VM-extensie voor SAP worden echter meestal geautomatiseerd door Azure.

#### <a name="solution-design"></a>Ontwerp van de oplossing

De oplossing die is ontwikkeld om SAP Host Agent in staat te stellen de vereiste informatie te verkrijgen, is gebaseerd op de architectuur van Azure VM Agent en Extension framework. Het idee van het Azure VM Agent and Extension framework is om de installatie van softwareapplicaties(s) beschikbaar te maken in de Azure VM Extension gallery binnen een VM. Het belangrijkste idee achter dit concept is om (in gevallen zoals de Azure Extension voor SAP), de implementatie van speciale functionaliteit in een VM en de configuratie van dergelijke software tijdens de implementatie tijd mogelijk te maken.

De 'Azure VM-agent' waarmee specifieke Azure VM-extensies binnen de VM kunnen worden verwerkt, wordt standaard geïnjecteerd in Windows VM's op het maken van vm's in de Azure-portal. In het geval van SUSE, Red Hat of Oracle Linux maakt de VM-agent al deel uit van de Azure Marketplace-afbeelding. In het geval dat men een Linux VM zou uploaden van on-premises naar Azure, moet de VM-agent handmatig worden geïnstalleerd.

De basisbouwstenen van de oplossing om Azure-infrastructuurinformatie te verstrekken aan SAP Host-agent in Azure zien er als volgt uit:

![Microsoft Azure-extensiecomponenten][planning-guide-figure-2400]

Zoals in het bovenstaande blokdiagram wordt een deel van de oplossing gehost in de Azure VM Image en Azure Extension Gallery, een wereldwijd gerepliceerde opslagplaats die wordt beheerd door Azure Operations. Het is de verantwoordelijkheid van het gezamenlijke SAP/MS-team dat werkt aan de Azure-implementatie van SAP om met Azure Operations te werken aan het publiceren van nieuwe versies van de Azure Extension voor SAP.

Wanneer u een nieuwe Windows-VM implementeert, wordt de Azure VM-agent automatisch toegevoegd aan de VM. De functie van deze agent is het coördineren van het laden en configureren van de Azure-extensies van de VM's. Voor Linux VM's maakt de Azure VM Agent al deel uit van de Azure Marketplace OS-afbeelding.

Er is echter een stap die nog moet worden uitgevoerd door de klant. Dit is de enablement en configuratie van de prestatieverzameling. Het proces met betrekking tot de configuratie wordt geautomatiseerd door een PowerShell-script of de opdracht CLI. Het PowerShell-script kan worden gedownload in het Microsoft Azure Script Center zoals beschreven in de [implementatiehandleiding.][deployment-guide]

De algehele architectuur van de Azure-extensie voor SAP ziet eruit als volgt:

![Azure-extensie voor SAP ][planning-guide-figure-2500]

**Volg de instructies in de [implementatiehandleiding][deployment-guide]voor de exacte how-to en voor gedetailleerde stappen van het gebruik van deze PowerShell-cmdlets of CLI-opdracht tijdens implementaties.**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integratie van Azure-locatie SAP-instantie in SAProuter

SAP-exemplaren die in Azure worden uitgevoerd, moeten ook toegankelijk zijn vanaf SAProuter.

![SAP-Router-netwerkverbinding][planning-guide-figure-2600]

Een SAProuter maakt de TCP/IP-communicatie tussen deelnemende systemen mogelijk als er geen directe IP-verbinding is. Dit biedt het voordeel dat er op netwerkniveau geen end-to-end verbinding tussen de communicatiepartners nodig is. De SAProuter luistert standaard op poort 3299.
Als u SAP-exemplaren via een SAProuter wilt verbinden, moet u de saprouter-tekenreeks en hostnaam opgeven bij elke poging om verbinding te maken.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver ALS Java

Tot nu toe lag de focus van het document op SAP NetWeaver in het algemeen of de SAP NetWeaver ABAP-stack. In deze kleine sectie worden specifieke overwegingen voor de SAP Java-stack vermeld. Een van de belangrijkste SAP NetWeaver Java uitsluitend gebaseerde applicaties is de SAP Enterprise Portal. Andere SAP NetWeaver-gebaseerde toepassingen zoals SAP PI en SAP Solution Manager maken gebruik van zowel de SAP NetWeaver ABAP- als Java-stacks. Daarom is er zeker een noodzaak om specifieke aspecten met betrekking tot de SAP NetWeaver Java stack ook te overwegen.

### <a name="sap-enterprise-portal"></a>SAP Enterprise-portal

De installatie van een SAP-portal in een Azure Virtual Machine verschilt niet van een on-premises installatie als u implementeert in cross-premises scenario's. Aangezien de DNS on-premises wordt uitgevoerd, kunnen de poortinstellingen van de afzonderlijke instanties worden uitgevoerd als on-premises geconfigureerd. De aanbevelingen en beperkingen die in dit document tot nu toe worden beschreven, zijn van toepassing voor een toepassing zoals SAP Enterprise Portal of de SAP NetWeaver Java-stack in het algemeen.

![Blootgestelde SAP-portal][planning-guide-figure-2700]

Een speciaal implementatiescenario door sommige klanten is de directe blootstelling van de SAP Enterprise Portal aan het internet, terwijl de virtuele machinehost is verbonden met het bedrijfsnetwerk via site-to-site VPN-tunnel of ExpressRoute. Voor een dergelijk scenario moet u ervoor zorgen dat specifieke poorten zijn geopend en niet worden geblokkeerd door firewall of netwerkbeveiligingsgroep. 

De eerste portal URI is`<Portalserver` http(s):>:5XX00/irj waar de <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>poort wordt gevormd zoals gedocumenteerd door SAP in .

![Eindpuntconfiguratie][planning-guide-figure-2800]

Als u de URL en/of poorten van uw SAP Enterprise Portal wilt aanpassen, raadpleegt u de gewenste documentatie:

* [URL van portal wijzigen](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Standaardpoortnummers wijzigen, portalpoortnummers](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>HA (High Availability) en Disaster Recovery (DR) voor SAP NetWeaver die wordt uitgevoerd op Azure Virtual Machines

### <a name="definition-of-terminologies"></a>Definitie van terminologieën

De term **high availability (HA)** is over het algemeen gerelateerd aan een reeks technologieën die IT-storingen minimaliseert door de bedrijfscontinuïteit van IT-services te bieden door redundante, fouttolerante of failoverbeschermde componenten binnen **hetzelfde** datacenter. In ons geval binnen één Azure-regio.

**Disaster recovery (DR)** is ook gericht op het minimaliseren van IT-services verstoring, en hun herstel, maar in **verschillende** datacenters, die meestal bevinden zich honderden kilometers afstand. In ons geval meestal tussen verschillende Azure-regio's binnen dezelfde geopolitieke regio of zoals vastgesteld door u als klant.

### <a name="overview-of-high-availability"></a>Overzicht van hoge beschikbaarheid

We kunnen de discussie over sap hoge beschikbaarheid in Azure in twee delen scheiden:

* **Azure-infrastructuur hoge beschikbaarheid,** bijvoorbeeld HA van compute (VM's), netwerk, opslag etc. en de voordelen ervan voor het verhogen van de beschikbaarheid van SAP-toepassingen.
* **SAP applicatie hoge beschikbaarheid,** bijvoorbeeld HA van SAP software componenten:
  * SAP-toepassingsservers
  * SAP ASCS/SCS-exemplaar
  * DB-server

en hoe het kan worden gecombineerd met Azure-infrastructuur HA.

SAP High Availability in Azure heeft enkele verschillen ten opzichte van SAP High Availability in een on-premises fysieke of virtuele omgeving. De volgende paper van SAP beschrijft standaard SAP High Availability-configuraties in gevirtualiseerde omgevingen op Windows: <https://scn.sap.com/docs/DOC-44415>. Er is geen sapinst-geïntegreerde SAP-HA configuratie voor Linux zoals het bestaat voor Windows. Met betrekking tot SAP HA on-premises <https://scn.sap.com/docs/DOC-8541>voor Linux vindt u meer informatie hier: .

### <a name="azure-infrastructure-high-availability"></a>Hoge beschikbaarheid azure-infrastructuur

Er is momenteel een single-VM SLA van 99,9%. Als u een idee wilt krijgen hoe de beschikbaarheid van één VIRTUELE VM eruit zou <https://azure.microsoft.com/support/legal/sla/>kunnen zien, u het product van de verschillende beschikbare Azure-SLA's bouwen:.

De basis voor de berekening is 30 dagen per maand, of 43200 minuten. Daarom komt 0,05% downtime overeen met 21,6 minuten. Zoals gebruikelijk zal de beschikbaarheid van de verschillende diensten zich op de volgende manier vermenigvuldigen:

(Availability Service #1/100) * (Availability Service #2/100) * (Availability Service #3/100) 

Als:

(99.95/100) * (99.9/100) * (99.9/100) = 0.9975 of een totale beschikbaarheid van 99.75%.

#### <a name="virtual-machine-vm-high-availability"></a>Virtuele machine (VM) Hoge beschikbaarheid

Er zijn twee soorten Azure-platformgebeurtenissen die van invloed kunnen zijn op de beschikbaarheid van uw virtuele machines: gepland onderhoud en ongepland onderhoud.

* Gepland onderhoud bestaat uit periodieke updates van het onderliggende Azure-platform die door Microsoft worden doorgevoerd ter verbetering van de algemene betrouwbaarheid, prestaties en beveiliging van de platform-infrastructuur waarop uw virtuele machines worden uitgevoerd.
* Niet-gepland onderhoud vindt plaats wanneer de hardware of de fysieke infrastructuur die uw virtuele machine ondersteunt defect raakt. Voorbeelden hiervan zijn lokale netwerkproblemen, lokale schijfdefecten of andere defecten op rack-niveau. Wanneer een dergelijke fout wordt gedetecteerd, migreert het Azure-platform uw virtuele machine automatisch van de ongezonde fysieke server die uw virtuele machine host naar een gezonde fysieke server. Dergelijke gebeurtenissen zijn zeldzaam, maar kunnen er ook toe leiden dat uw virtuele machine opnieuw moet opstarten.

Meer details zijn te vinden in deze documentatie:<https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Redundantie azure-opslag

De gegevens in uw Microsoft Azure Storage Account worden altijd gerepliceerd om duurzaamheid en hoge beschikbaarheid te garanderen, zodat de Azure Storage SLA wordt bereikt, zelfs in het licht van tijdelijke hardwarefouten.

Aangezien Azure Storage standaard drie afbeeldingen van de gegevens bewaart, is RAID5 of RAID1 niet nodig voor meerdere Azure-schijven.

Meer details zijn te vinden in dit artikel:<https://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Azure Infrastructure VM Opnieuw opstarten gebruiken om een hogere beschikbaarheid van SAP-toepassingen te bereiken

Als u besluit geen functionaliteiten zoals Windows Server Failover Clustering (WSFC) of Pacemaker op Linux te gebruiken (momenteel alleen ondersteund voor SLES 12 en hoger), wordt Azure VM Restart gebruikt om een SAP-systeem te beschermen tegen geplande en ongeplande downtime van de Azure fysieke serverinfrastructuur en het algemene onderliggende Azure-platform.

> [!NOTE]
> Het is belangrijk om te vermelden dat Azure VM Restart voornamelijk VM's en NIET-toepassingen beschermt. VM Restart biedt geen hoge beschikbaarheid voor SAP-toepassingen, maar biedt wel een bepaald niveau van beschikbaarheid van de infrastructuur en dus indirect een hogere beschikbaarheid van SAP-systemen. Er is ook geen SLA voor de tijd die nodig is om een VM opnieuw op te starten na een geplande of ongeplande hoststoring. Daarom is deze methode van hoge beschikbaarheid niet geschikt voor kritieke componenten van een SAP-systeem zoals (A)SCS of DBMS.
>
>

Een ander belangrijk infrastructuurelement voor hoge beschikbaarheid is opslag. Azure Storage SLA is bijvoorbeeld 99,9 % beschikbaarheid. Als men alle VM's met zijn schijven implementeert in één Azure Storage-account, leidt potentiële azure-opslagonbeschikbaarheid tot onbeschikbaarheid van alle VM's die in dat Azure-opslagaccount zijn geplaatst, en ook alle SAP-componenten die in die VM's worden uitgevoerd.  

In plaats van alle VM's in één Azure-opslagaccount te plaatsen, u ook speciale opslagaccounts voor elke VM gebruiken en op deze manier de algehele beschikbaarheid van VM-en SAP-toepassingen verhogen door meerdere onafhankelijke Azure-opslagaccounts te gebruiken.

Azure Managed Disks worden automatisch geplaatst in het foutdomein van de virtuele machine waaraan ze zijn gekoppeld. Als u twee virtuele machines in een beschikbaarheidsset plaatst en Beheerde schijven gebruikt, zorgt het platform ervoor dat de beheerde schijven ook in verschillende foutdomeinen worden gedistribueerd. Als u premiumopslag wilt gebruiken, raden we u ten zeerste aan om ook schijven beheren te gebruiken.

Een voorbeeldarchitectuur van een SAP NetWeaver-systeem dat gebruikmaakt van Azure-infrastructuur HA en opslagaccounts, kan er als volgt uitzien:

![Gebruik makend van Azure-infrastructuur HA om de beschikbaarheid van SAP-toepassingen te verhogen][planning-guide-figure-2900]

Een voorbeeldarchitectuur van een SAP NetWeaver-systeem dat gebruikmaakt van Azure-infrastructuur HA en Beheerde schijven, kan er als volgt uitzien:

![Gebruik makend van Azure-infrastructuur HA om de beschikbaarheid van SAP-toepassingen te verhogen][planning-guide-figure-2901]

Voor kritieke SAP-componenten bereikten we tot nu toe het volgende:

* Hoge beschikbaarheid van SAP-toepassingsservers (AS)

  SAP-toepassingsserver-exemplaren zijn redundante componenten. Elke SAP AS-instantie wordt geïmplementeerd op zijn eigen VM, die wordt uitgevoerd in een ander Azure-fout- en upgradedomein (zie hoofdstukken [Foutdomeinen][planning-guide-3.2.1] en [Upgradedomeinen).][planning-guide-3.2.2] Dit wordt gewaarborgd door azure-beschikbaarheidssets te gebruiken (zie hoofdstuk [Azure Availability Sets).][planning-guide-3.2.3] Potentiële geplande of ongeplande onbeschikbaarheid van een Azure Fault- of Upgrade-domein zal leiden tot onbeschikbaarheid van een beperkt aantal VM's met hun SAP AS-exemplaren.

  Elke SAP AS-instantie wordt in zijn eigen Azure Storage-account geplaatst - mogelijk onbeschikbaarheid van één Azure Storage-account zal leiden tot onbeschikbaarheid van slechts één VM met zijn SAP AS-exemplaar. Houd er echter rekening mee dat er een limiet is voor Azure-opslagaccounts binnen één Azure-abonnement. Als u wilt zorgen voor een automatische start van (A)SCS-instantie na het opnieuw opstarten van de VM, moet u de parameter Autostart instellen in (A)SCS-instantiestartprofiel beschreven in hoofdstuk [Autostart gebruiken voor SAP-exemplaren][planning-guide-11.5].
  Lees ook hoofdstuk [Hoge beschikbaarheid voor SAP-toepassingsservers][planning-guide-11.4.1] voor meer informatie.

  Zelfs als u Beheerde schijven gebruikt, worden deze schijven ook opgeslagen in een Azure-opslagaccount en kunnen ze niet beschikbaar zijn in geval van een opslagstoring.

* *Hoger* Beschikbaarheid van SAP (A)SCS-exemplaar

  Hier gebruiken we Azure VM Restart om de VM te beschermen met een geïnstalleerd SAP (A)SCS-exemplaar. In het geval van geplande of ongeplande downtime van Azure-severs worden VM's opnieuw gestart op een andere beschikbare server. Zoals eerder vermeld, beschermt Azure VM Restart voornamelijk VM's en NIET-toepassingen, in dit geval het (A)SCS-exemplaar. Via de VM Restart bereiken we indirect een hogere beschikbaarheid van SAP (A)SCS-exemplaar. Als u de automatische start van (A)SCS-instantie na het opnieuw opstarten van de VM wilt verzekeren, moet u de parameter Autostart instellen in (A)SCS-instantiestartprofiel beschreven in hoofdstuk [Autostart gebruiken voor SAP-exemplaren][planning-guide-11.5]. Dit betekent dat de (A)SCS-instantie als single point of failure (SPOF) die in één VM wordt uitgevoerd, de bepalende factor zal zijn voor de beschikbaarheid van het hele SAP-landschap.

* *Hoger* Beschikbaarheid van DBMS Server

  Hier gebruiken we, net als bij de SAP(A)SCS instance use case, Azure VM Restart om de VM te beschermen met geïnstalleerde DBMS-software, en bereiken we een hogere beschikbaarheid van DBMS-software via VM Restart.
  DBMS draait in een enkele VM is ook een SPOF, en het is de bepalende factor voor de beschikbaarheid van het hele SAP-landschap.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Hoge beschikbaarheid van SAP-toepassingen op Azure IaaS

Om een volledige beschikbaarheid van het SAP-systeem te bereiken, moeten we alle kritieke SAP-systeemcomponenten beschermen, bijvoorbeeld redundante SAP-toepassingsservers, en unieke componenten (bijvoorbeeld Single Point of Failure) zoals SAP (A)SCS-instantie en DBMS.

#### <a name="high-availability-for-sap-application-servers"></a><a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Hoge beschikbaarheid voor SAP-toepassingsservers

Voor de SAP-toepassingsservers/-dialoogvensters is het niet nodig om na te denken over een specifieke oplossing met hoge beschikbaarheid. Hoge beschikbaarheid wordt bereikt door redundantie en daardoor met genoeg van hen in verschillende virtuele machines. Ze moeten allemaal in dezelfde Azure-beschikbaarheidsset worden geplaatst om te voorkomen dat de VM's tegelijkertijd kunnen worden bijgewerkt tijdens geplande onderhoudsdowntime. De basisfunctionaliteit, die voortbouwt op verschillende upgrade- en foutdomeinen binnen een Azure Scale Unit, is al geïntroduceerd in [hoofdstukupgradedomeinen.][planning-guide-3.2.2] Azure-beschikbaarheidssets zijn gepresenteerd in de [azure-beschikbaarheidssets][planning-guide-3.2.3] van dit document in hoofdstuk.

Er is geen oneindig aantal fout- en upgradedomeinen dat kan worden gebruikt door een Azure-beschikbaarheidsset binnen een Azure Scale Unit. Dit betekent dat het plaatsen van een aantal VM's in een beschikbaarheid set, vroeg of laat meer dan een VM belandt in dezelfde fout of upgrade domein.

Het implementeren van een paar SAP-applicatieserver-exemplaren in hun speciale VM's en ervan uitgaande dat we vijf upgradedomeinen hebben, verschijnt aan het einde het volgende beeld. Het werkelijke maximumaantal fout- en updatedomeinen binnen een beschikbaarheidsset kan in de toekomst veranderen:

![HA van SAP-toepassingsservers in Azure][planning-guide-figure-3000]

Meer details zijn te vinden in deze documentatie:<https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-sap-central-services-on-azure"></a>Hoge beschikbaarheid voor SAP Central Services op Azure

Raadpleeg het artikel [Architectuur en scenario's voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) als invoerinformatie voor de architectuur met hoge beschikbaarheid van SAP Central Services op Azure. Het artikel wijst op meer gedetailleerde beschrijvingen voor de specifieke besturingssystemen.

#### <a name="high-availability-for-the-sap-database-instance"></a>Hoge beschikbaarheid voor de SAP-database-instantie

De typische SAP DBMS HA-setup is gebaseerd op twee DBMS VM's waarbij DBMS-functionaliteit met hoge beschikbaarheid wordt gebruikt om gegevens van de actieve DBMS-instantie naar de tweede VM te repliceren in een passieve DBMS-instantie.

Functionaliteit voor hoge beschikbaarheid en noodherstel voor DBMS in het algemeen en specifieke DBMS worden beschreven in de [DBMS Deployment Guide][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>End-to-End hoge beschikbaarheid voor het complete SAP-systeem

Hier volgen twee voorbeelden van een complete SAP NetWeaver HA-architectuur in Azure- een voor Windows en een voor Linux.

Alleen niet-beheerde schijven: de concepten zoals hieronder uitgelegd, moeten mogelijk een beetje worden gecompromitteerd wanneer u veel SAP-systemen implementeert en het aantal geïmplementeerde VM's de maximale limiet van opslagaccounts per abonnement overschrijdt. In dergelijke gevallen moeten VHD's van VM's worden gecombineerd binnen één opslagaccount. Meestal zou je dit doen door het combineren van VHD's van SAP applicatie laag VM's van verschillende SAP-systemen.  We hebben ook verschillende VHD's van verschillende DMS VM's van verschillende SAP-systemen gecombineerd in één Azure Storage-account. Houd daarbij de IOPS-limieten van<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>Azure Storage Accounts in gedachten ( )


##### <a name="windowslogo_windows-ha-on-windows"></a>![Windows][Logo_Windows] HA op Windows

![SAP NetWeaver-toepassingsHA-architectuur met SQL Server in Azure IaaS][planning-guide-figure-3200]

De volgende Azure-constructies worden gebruikt voor het SAP NetWeaver-systeem om de impact van infrastructuurproblemen en hostpatching te minimaliseren:

* Het volledige systeem wordt geïmplementeerd op Azure (vereist - DBMS-laag, (A)SCS-instantie en volledige toepassingslaag moeten op dezelfde locatie worden uitgevoerd).
* Het volledige systeem wordt uitgevoerd binnen één Azure-abonnement (vereist).
* Het volledige systeem wordt uitgevoerd binnen één Azure Virtual Network (vereist).
* De scheiding van de VM's van één SAP-systeem in drie beschikbaarheidssets is mogelijk, zelfs met alle VM's die tot hetzelfde virtuele netwerk behoren.
* Elke laag (bijvoorbeeld DBMS, ASCS, Application Servers) moet een speciale beschikbaarheidsset gebruiken.
* Alle VM's met DBMS-exemplaren van één SAP-systeem bevinden zich in één beschikbaarheidsset. We gaan ervan uit dat er meer dan één VM is met DBMS-exemplaren per systeem, omdat native DBMS-functies met hoge beschikbaarheid worden gebruikt, zoals SQL Server AlwaysOn of Oracle Data Guard.
* Alle VM's met DMS-exemplaren gebruiken hun eigen opslagaccount. DBMS-gegevens en logboekbestanden worden gerepliceerd van het ene opslagaccount naar een ander opslagaccount met dbms-functies met hoge beschikbaarheid die de gegevens synchroniseren. Als u één opslagaccount niet beschikbaar hebt, is er geen beschikbaarheid van één SQL Windows-clusterknooppunt, maar niet de hele SQL Server-service.
* Alle VM's met (A)SCS-instantie van één SAP-systeem bevinden zich in één beschikbaarheidsset. Een Windows Server Failovercluster (WSFC) is geconfigureerd in deze VM's om het (A)SCS-exemplaar te beschermen.
* Alle VM's met (A)SCS-exemplaren gebruiken hun eigen opslagaccount. A) SCS-instantiebestanden en SAP-globale map worden gerepliceerd van het ene opslagaccount naar een ander opslagaccount met behulp van SIOS DataKeeper-replicatie. Als u één opslagaccount niet beschikbaar maakt, wordt één (A)SCS Windows-clusterknooppunt niet beschikbaar, maar niet de hele (A)SCS-service.
* Alle VM's die de SAP-toepassingsserverlaag vertegenwoordigen, bevinden zich in een derde beschikbaarheidsset.
* Alle VM's met SAP-toepassingsservers gebruiken hun eigen opslagaccount. Als de beschikbaarheid van één opslagaccount niet beschikbaar is, is er geen beschikbaarheid van één SAP-toepassingsserver, waar andere SAP-toepassingsservers blijven worden uitgevoerd.

De volgende afbeelding illustreerde hetzelfde landschap met beheerde schijven.

![SAP NetWeaver-toepassingsHA-architectuur met SQL Server in Azure IaaS][planning-guide-figure-3201]

##### <a name="linuxlogo_linux-ha-on-linux"></a>![Linux][Logo_Linux] HA op Linux

De architectuur voor SAP HA op Linux op Azure is in principe hetzelfde als voor Windows zoals hierboven beschreven. Raadpleeg SAP Note [1928533] voor een lijst met ondersteunde oplossingen voor hoge beschikbaarheid.

### <a name="using-autostart-for-sap-instances"></a><a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Autostart gebruiken voor SAP-exemplaren

SAP bood de functionaliteit aan om SAP-exemplaren direct na de start van het besturingssysteem binnen de VM te starten. De exacte stappen werden gedocumenteerd in SAP Knowledge Base Artikel [1909114]. SAP raadt echter niet aan om de instelling niet meer te gebruiken omdat er geen controle is in de volgorde van het opnieuw opstarten van instanties, ervan uitgaande dat er meer dan één VM is getroffen of meerdere exemplaren per VM worden uitgevoerd. Ervan uitgaande dat een typisch Azure-scenario van één SAP-toepassingsserverinstantie in een VM en het geval van één virtuele vm uiteindelijk opnieuw wordt opgestart, is de Autostart niet kritiek en kan deze worden ingeschakeld door deze parameter toe te voegen:

    Autostart = 1

In het startprofiel van het SAP ABAP en/of Java exemplaar.

> [!NOTE]
> De parameter Autostart kan ook een aantal downfalls hebben. In meer detail activeert de parameter het begin van een SAP ABAP- of Java-exemplaar wanneer de gerelateerde Windows/Linux-service van de instantie wordt gestart. Dat is zeker het geval wanneer het besturingssysteem opstart. Het opnieuw opstarten van SAP-services is echter ook een gangbare zaak voor SAP Software Lifecycle Management-functionaliteit zoals SOM of andere updates of upgrades. Deze functionaliteiten verwachten niet dat een instantie automatisch opnieuw wordt opgestart. Daarom moet de parameter Autostart worden uitgeschakeld voordat dergelijke taken worden uitgevoerd. De parameter Autostart mag ook niet worden gebruikt voor SAP-exemplaren die zijn geclusterd, zoals ASCS/SCS/CI.
>
>

Zie hier aanvullende informatie over automatisch starten voor SAP-exemplaren:

* [Start/Stop SAP samen met uw Unix Server Start/Stop](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [SAP NetWeaver-beheeragents starten en stoppen](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Automatisch starten van HANA-database inschakelen](http://sapbasisinfo.com/blog/2016/08/15/enabling-autostart-of-sap-hana-database-on-server-boot-situation/)

### <a name="larger-3-tier-sap-systems"></a>Grotere 3-tier SAP-systemen
High-Availability aspecten van 3-Tier SAP configuraties werd al besproken in eerdere secties. Maar hoe zit het met systemen waar de DBMS-serververeisten te groot zijn om deze in Azure te hebben, maar de SAP-toepassingslaag kan worden geïmplementeerd in Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Locatie van 3-tier SAP-configuraties
Het wordt niet ondersteund om de toepassingslaag zelf of de toepassings- en DBMS-laag te splitsen tussen on-premises en Azure. Een SAP-systeem wordt volledig on-premises geïmplementeerd OF in Azure. Het wordt ook niet ondersteund om sommige van de toepassingsservers on-premises en sommige anderen in Azure te laten draaien. Dat is het uitgangspunt van de discussie. We ondersteunen ook niet om de DBMS-componenten van een SAP-systeem en de SAP-toepassingsserverlaag in twee verschillende Azure-regio's te laten implementeren. Dbms in West US en SAP-toepassingslaag in Centraal VS. Reden om dergelijke configuraties niet te ondersteunen is de latentiegevoeligheid van de SAP NetWeaver-architectuur.

In de loop van vorig jaar ontwikkelden datacenterpartners echter co-locaties voor Azure Regions. Deze colocaties bevinden zich vaak in de nabijheid van de fysieke Azure-datacenters binnen een Azure-regio. De korte afstand en de verbinding van assets in de colocatie via ExpressRoute in Azure kan resulteren in een latentie van minder dan 2 ms. In dergelijke gevallen is het mogelijk om de DBMS-laag (inclusief opslag SAN/NAS) in een dergelijke colocatie en de SAP-toepassingslaag in Azure te vinden. [HANA Grote Instanties](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). 

### <a name="offline-backup-of-sap-systems"></a>Offline back-up van SAP-systemen
Afhankelijk van de gekozen SAP-configuratie (2-Tier of 3-Tier) kan er een back-up moeten worden gemaakt. De inhoud van de VM zelf plus om een back-up van de database te hebben. De DBMS-gerelateerde back-ups zullen naar verwachting worden gedaan met databasemethoden. Een gedetailleerde beschrijving voor de verschillende databases, is te vinden in [DBMS Guide][dbms-guide]. Aan de andere kant kunnen de SAP-gegevens offline worden geback-upt (inclusief de database-inhoud) zoals beschreven in deze sectie of online zoals beschreven in de volgende sectie.

De offline back-up zou in principe een uitschakeling van de VM via de Azure-portal en een kopie van de basis-VM-schijf plus alle aangesloten schijven aan de VM vereisen. Hiermee blijft een tijdsbeeld van de VM en de bijbehorende schijf behouden. Het wordt aanbevolen om de back-ups te kopiëren naar een ander Azure Storage-account. Vandaar dat de procedure beschreven in hoofdstuk [Het kopiëren van schijven tussen Azure Storage Accounts][planning-guide-5.4.2] van dit document van toepassing zou zijn.
Naast de shutdown met behulp van de Azure portal kan men het ook doen via Powershell of CLI zoals hier beschreven:<https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Een herstel van die status zou bestaan uit het verwijderen van de basis-VM evenals de originele schijven van de basis-VM en gemonteerde schijven, het kopiëren van de opgeslagen schijven naar de oorspronkelijke opslagaccount of resourcegroep voor beheerde schijven en vervolgens het systeem opnieuw implementeren.
In dit artikel ziet u een voorbeeld hoe u dit proces scripten in PowerShell:<http://www.westerndevs.com/azure-snapshots/>

Zorg ervoor dat u een nieuwe SAP-licentie installeert, omdat het herstellen van een VM-back-up zoals hierboven beschreven, een nieuwe hardwaresleutel maakt.

### <a name="online-backup-of-an-sap-system"></a>Online back-up van een SAP-systeem

Back-up van de DBMS wordt uitgevoerd met DBMS-specifieke methoden zoals beschreven in de [DBMS-gids][dbms-guide].

Andere VM's binnen het SAP-systeem kunnen worden geback-upt met azure virtual machine backup-functionaliteit. Azure Virtual Machine Backup is een standaardmethode om een back-up te maken van een volledige vm in Azure. Azure Backup slaat de back-ups op in Azure en maakt het mogelijk om een vm opnieuw te herstellen.

> [!NOTE]
> Vanaf december 2015 met behulp van VM Backup niet de unieke VM-ID die wordt gebruikt voor SAP-licenties te houden. Dit betekent dat een herstel van een VM-back-up vereist installatie van een nieuwe SAP-licentiesleutel als de herstelde VM wordt beschouwd als een nieuwe VM en niet een vervanging van de voormalige die werd opgeslagen.
>
> ![Windows][Logo_Windows] Windows
>
> In theorie kunnen VM's die databases uitvoeren op een consistente manier worden geback-upt als <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>het DBMS-systeem de Windows VSS (Volume Shadow Copy Service) ondersteunt, zoals sql Server bijvoorbeeld doet.
> Houd er echter rekening mee dat op basis van Azure VM-back-ups point-in-time-herstel van databases niet mogelijk zijn. Daarom is de aanbeveling om back-ups van databases met DBMS-functionaliteit uit te voeren in plaats van te vertrouwen op Azure VM Backup.
>
> Om vertrouwd te raken met Azure <https://docs.microsoft.com/azure/backup/backup-azure-vms>Virtual Machine Backup start hier: .
>
> Andere mogelijkheden zijn het gebruik van een combinatie van Microsoft Data Protection Manager die is geïnstalleerd in een Azure VM en Azure Backup om databases te back-ups/herstellen. Meer informatie is hier <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>te vinden: .  
>
> ![Linux][Logo_Linux] Linux
>
> Er is geen equivalent aan Windows VSS in Linux. Daarom zijn alleen bestandsconsistente back-ups mogelijk, maar geen toepassingsconsistente back-ups. De SAP DBMS back-up moet worden gedaan met behulp van DBMS functionaliteit. Het bestandssysteem dat de SAP-gerelateerde gegevens bevat, kan bijvoorbeeld worden opgeslagen met teer zoals hier beschreven:<https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure als DR-site voor productie SAP-landschappen

Sinds medio 2014 maken extensies voor verschillende componenten rond Hyper-V, System Center en Azure het gebruik van Azure als DR-site mogelijk voor VM's die on-premises worden uitgevoerd op basis van Hyper-V.

Een blog waarin wordt beschreven hoe <https://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>deze oplossing te implementeren is hier gedocumenteerd: .

## <a name="summary"></a>Samenvatting

De belangrijkste punten van hoge beschikbaarheid voor SAP-systemen in Azure zijn:

* Op dit moment kan het SAP single point of failure niet op dezelfde manier worden beveiligd als in on-premises implementaties. De reden hiervoor is dat Shared Disk-clusters nog niet in Azure kunnen worden gebouwd zonder het gebruik van software van derden.
* Voor de DBMS-laag moet u DBMS-functionaliteit gebruiken die niet afhankelijk is van gedeelde schijfclustertechnologie. Details zijn gedocumenteerd in de [DBMS Guide][dbms-guide].
* Als u de impact van problemen binnen foutdomeinen in de Azure-infrastructuur of het beheer van de host wilt minimaliseren, moet u Azure-beschikbaarheidssets gebruiken:
  * Het wordt aanbevolen om één beschikbaarheidsset te hebben voor de SAP-toepassingslaag.
  * Het wordt aanbevolen om een aparte beschikbaarheidsset te hebben voor de SAP DBMS-laag.
  * Het wordt NIET aanbevolen om dezelfde beschikbaarheidsset toe te passen voor VM's van verschillende SAP-systemen.
  * Het wordt aanbevolen om Premium Managed Disks te gebruiken.
* Voor back-updoeleinden van de SAP DBMS-laag raadpleegt u de [DBMS-gids][dbms-guide].
* Een back-up maken van SAP Dialog-exemplaren heeft weinig zin, omdat het meestal sneller is om eenvoudige dialoogvensters opnieuw te implementeren.
* Een back-up van de VM, die de globale directory van het SAP-systeem bevat en daarmee alle profielen van de verschillende instanties, heeft zin en moet worden uitgevoerd met Windows Backup of, bijvoorbeeld, teer op Linux. Aangezien er verschillen zijn tussen Windows Server 2008 (R2) en Windows Server 2012 (R2), waardoor het eenvoudiger wordt om een back-up te maken met de meer recente Windows Server-releases, raden we u aan Windows Server 2012 (R2) als Windows-gastbesturingssysteem uit te voeren.

## <a name="next-steps"></a>Volgende stappen
Lees de artikelen:

- [Azure Virtual Machines-implementatie voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-workload](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP HANA-infrastructuurconfiguraties en -bewerkingen op Azure] (https://docs.microsoft.com/
- azure/virtual-machines/workloads/sap/hana-vm-operations)
