---
title: SQL Server Azure Virtual Machines DBMS-implementatie voor SAP-workload | Microsoft Documenten
description: DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor SQL Server
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a0fbed1f4dd62b2d75d39f475d2fe124c55a2b97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645800"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>SQL Server Azure Virtual Machines DBMS-implementatie voor SAP NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
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
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
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

[dbms-guide]:dbms-guide_general.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms_guide_sqlserver.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

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

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/eresources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md




Dit document bestrijkt verschillende gebieden waar u rekening mee moet houden bij het implementeren van SQL Server voor SAP-workload in Azure IaaS. Als voorwaarde voor dit document moet u de [DBMS-implementatie van document Overwegingen voor Azure Virtual Machines DBMS en](dbms_guide_general.md) andere handleidingen in de [SAP-werkbelasting op Azure-documentatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)hebben gelezen. 



> [!IMPORTANT]
> Het bereik van dit document is de Windows-versie op SQL Server. SAP ondersteunt de Linux-versie van SQL Server niet met een van de SAP-software. Het document gaat niet over Microsoft Azure SQL Database, een Platform as a Service-aanbieding van het Microsoft Azure Platform. De discussie in dit document gaat over het uitvoeren van het SQL Server-product zoals het bekend staat voor on-premises implementaties in Azure Virtual Machines, waarbij gebruik wordt gemaakt van de infrastructuur als servicemogelijkheid van Azure. Database mogelijkheden en functionaliteiten tussen deze twee aanbiedingen zijn verschillend en mag niet worden gemengd met elkaar. Zie ook:<https://azure.microsoft.com/services/sql-database/>
> 
>

Over het algemeen moet u overwegen de meest recente SQL Server-releases te gebruiken om SAP-workload in Azure IaaS uit te voeren. De nieuwste SQL Server-releases bieden een betere integratie in sommige Azure-services en -functionaliteit. Of heb wijzigingen die bewerkingen optimaliseren in een Azure IaaS-infrastructuur.

Het wordt aanbevolen om [deze][virtual-machines-sql-server-infrastructure-services] documentatie te bekijken voordat u verdergaat.

In de volgende secties worden delen van de documentatie onder de bovenstaande link samengevoegd en vermeld. Ook details rond SAP worden genoemd en sommige concepten worden nader beschreven. Het is echter sterk aanbevolen om eerst de bovenstaande documentatie te doorlopen voordat u de SQL Server-specifieke documentatie leest.

Er is een aantal SQL Server in IaaS specifieke informatie die u moet weten voordat u verder gaat:

* **SQL Version Support**: Voor SAP-klanten wordt SQL Server 2008 R2 en hoger ondersteund op Microsoft Azure Virtual Machine. Eerdere edities worden niet ondersteund. Bekijk deze algemene [ondersteuningsverklaring](https://support.microsoft.com/kb/956893) voor meer details. Over het algemeen wordt SQL Server 2008 ook ondersteund door Microsoft. Vanwege de aanzienlijke functionaliteit voor SAP, dat werd geïntroduceerd met SQL Server 2008 R2, is SQL Server 2008 R2 echter de minimale release voor SAP. Over het algemeen moet u overwegen de meest recente SQL Server-releases te gebruiken om SAP-workload in Azure IaaS uit te voeren. De nieuwste SQL Server-releases bieden een betere integratie in sommige Azure-services en -functionaliteit. Of heb wijzigingen die bewerkingen optimaliseren in een Azure IaaS-infrastructuur. Daarom is het papier beperkt tot SQL Server 2016 en SQL Server 2017.
* **SQL-prestaties**: Door Microsoft Azure gehoste virtuele machines presteren goed in vergelijking met andere openbare cloudvirtualisatieaanbiedingen, maar individuele resultaten kunnen variëren. Bekijk het artikel [Aanbevolen procedures voor prestaties voor SQL Server in Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).
* **Afbeeldingen gebruiken van Azure Marketplace:** de snelste manier om een nieuwe Microsoft Azure VM te implementeren, is het gebruik van een afbeelding van de Azure Marketplace. Er zijn afbeeldingen in de Azure Marketplace, die de meest recente SQL Server-releases bevatten. De afbeeldingen waarop SQL Server al is geïnstalleerd, kunnen niet onmiddellijk worden gebruikt voor SAP NetWeaver-toepassingen. De reden hiervoor is dat de standaard SQL Server-collatie is geïnstalleerd in die afbeeldingen en niet de collatie die vereist is door SAP NetWeaver-systemen. Als u dergelijke afbeeldingen wilt gebruiken, controleert u de stappen die zijn gedocumenteerd in hoofdstuk [Met behulp van een SQL Server-afbeelding uit de Microsoft Azure Marketplace][dbms-guide-5.6]. 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Aanbevelingen voor VM/VHD-structuur voor SAP-gerelateerde SQL Server-implementaties
In overeenstemming met de algemene beschrijving moeten SQL Server-uitvoerbare bestanden worden gevestigd of geïnstalleerd in\)het systeemstation van de OS-schijf van de VM (station C: .  Doorgaans worden de meeste SQL Server-systeemdatabases niet op hoog niveau gebruikt door SAP NetWeaver-workload. Hierdoor kunnen de systeemdatabases van SQL Server (master, msdb en model) op de C:\ rijden ook. Een uitzondering moet tempdb zijn, waarvoor in het geval van SAP-workloads een hoger gegevensvolume of een I/O-bedrijfsvolume nodig is. I/O-workload, die niet mag worden toegepast op de OS VHD. Voor dergelijke systemen moeten de volgende stappen worden uitgevoerd:


* Bij alle SAP-gecertificeerde VM-typen (zie SAP Note [1928533),]behalve VM's uit de A-serie, kunnen tempdb-gegevens en logbestanden op de niet-aanhoudende D:\ Station. 
* Toch is het raadzaam om meerdere tempdb-gegevensbestanden te gebruiken. Let op D:\ schijfvolumes zijn verschillend op basis van het VM-type. Voor de exacte grootte van de D:\ station van de verschillende VM's, controleer het artikel [Grootte voor Windows virtuele machines in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

Met deze configuraties kan tempdb meer ruimte innemen dan het systeemstation kan bieden. De niet-permanente D:\ drive biedt ook een betere I/O latentie en doorvoer (met uitzondering van A-serie VM's). Om de juiste tempdb grootte te bepalen, u de tempdb maten op bestaande systemen. 

>[!NOTE]
> in het geval u tempdb-gegevensbestanden plaatst en in een map op D:\ schijf die u hebt gemaakt, moet u ervoor zorgen dat de map bestaat na een vm-reboot. Sinds de D:\ schijf is vers geïnitialiseerd na een VM reboot alle bestand en directory structuren worden weggevaagd. Een mogelijkheid om eventuele directorystructuren opnieuw te maken op D:\ station voordat de start van de SQL Server-service is gedocumenteerd in [dit artikel](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).

Een VM-configuratie, die SQL Server uitvoert met een SAP-database en waarbij tempdb-gegevens en tempdb-logbestand op het D:\ drive zou eruit zien als:

![Diagram met eenvoudige VM-schijfconfiguratie voor SQL Server](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

Het diagram hierboven toont een eenvoudige behuizing. Zoals ontweken in het artikel [Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-werkbelasting,](dbms_guide_general.md)is het aantal en de grootte van Premium Storage-schijven afhankelijk van verschillende factoren. Maar in het algemeen raden we aan:

- Met behulp van opslagruimten om een of een klein aantal volumes te vormen, die de SQL Server-gegevensbestanden bevatten. Reden achter deze configuratie is dat er in het echte leven tal van SAP-databases zijn met databasebestanden van verschillende grootte met verschillende I/O-workload.
- Met behulp van opslagruimten om voldoende IOPS en voor het SQL Server-transactielogboekbestand te leveren. Potentiële IOPS-workload is vaak de leidende lijn voor de grootte van het transactielogboekvolume en niet voor het potentiële volume van het SQL Server-transactievolume
- Gebruik de D:\drive voor tempdb zolang de prestaties goed genoeg zijn. Als de totale werklast beperkt is in prestaties door dat Tmepdb zich op de D:\ schijf die u zou moeten overwegen om tempdb te verplaatsen naar Premium Storage schijven te scheiden zoals aanbevolen in [dit artikel](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).


### <a name="special-for-m-series-vms"></a>Speciaal voor Vm's uit de M-serie
Voor Azure M-Series VM kan de latentie die in het transactielogboek wordt geschreven, worden verminderd met factoren in vergelijking met de prestaties van Azure Premium Storage bij het gebruik van Azure Write Accelerator. Daarom moet u Azure Write Accelerator implementeren voor de VHD(s) die het volume vormen voor het SQL Server-transactielogboek. Details kunnen worden gelezen in het document [Schrijfaccelerator](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).
  

### <a name="formatting-the-disks"></a>De schijven opmaken
Voor SQL Server moet de NTFS-blokgrootte voor schijven met SQL Server-gegevens en logboekbestanden 64 KB zijn. Het is niet nodig om de D:\ Station. Dit station wordt geleverd pre-geformatteerd.

Als u ervoor wilt zorgen dat het herstellen of maken van databases de gegevensbestanden niet initialiseert door de inhoud van de bestanden te nullen, moet u ervoor zorgen dat de gebruikerscontext waarin de SQL Server-service wordt uitgevoerd een bepaalde toestemming heeft. Meestal hebben gebruikers in de Windows Administrator-groep deze machtigingen. Als de SQL Server-service wordt uitgevoerd in de gebruikerscontext van de gebruiker van niet-Windows-beheerders, moet u die gebruiker de **volumeonderhoudstaken**van gebruiker zelf uitvoeren.  Zie de details in dit Microsoft Knowledge Base-artikel:<https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Impact van databasecompressie
In configuraties waar I/O-bandbreedte een beperkende factor kan worden, kan elke maatregel, die IOPS vermindert, helpen om de werkbelasting uit te rekken die men kan uitvoeren in een IaaS-scenario zoals Azure. Daarom, als dit nog niet is gebeurd, wordt het toepassen van SQL Server PAGE-compressie aanbevolen door zowel SAP als Microsoft voordat een bestaande SAP-database naar Azure wordt geüpload.

De aanbeveling om databasecompressie uit te voeren voordat u naar Azure wordt geüpload, wordt gegeven op basis van twee redenen:

* De hoeveelheid gegevens die moet worden geüpload, is lager.
* De duur van de compressieuitvoering is korter in de veronderstelling dat men sterkere hardware kan gebruiken met meer CPU's of een hogere I/O-bandbreedte of minder I/O-latentie on-premises.
* Kleinere databaseformaten kunnen leiden tot minder kosten voor schijftoewijzing

Databasecompressie werkt net zo goed in een Azure Virtual Machines als on-premises. Voor meer informatie over het comprimeren van bestaande SAP NetWeaver SQL Server-databases raadpleegt u het artikel [Improved SAP-compressietool MSSCOMPRESS](https://blogs.msdn.microsoft.com/saponsqlserver/2016/11/25/improved-sap-compression-tool-msscompress/). 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 en recenter - Databasebestanden rechtstreeks opslaan in Azure Blob Storage
SQL Server 2014 en latere releases openen de mogelijkheid om databasebestanden rechtstreeks op Azure Blob Store op te slaan zonder de 'wrapper' van een VHD om hen heen. Vooral met het gebruik van Standaard Azure Storage of kleinere VM-typen maakt dit type implementatie scenario's mogelijk waarin u de limieten van IOPS overwinnen die zouden worden afgedwongen door een beperkt aantal schijven die kunnen worden gemonteerd op een aantal kleinere VM-typen. Deze manier van implementatie werkt voor gebruikersdatabases echter niet voor systeemdatabases van SQL Server. Het werkt ook voor gegevens en logboekbestanden van SQL Server. Als u een SAP SQL Server-database op deze manier wilt implementeren in plaats van deze in VHD's te 'verpakken', moet u rekening houden met:

* Het gebruikte opslagaccount moet zich in dezelfde Azure-regio bevinden als het account dat wordt gebruikt om de VM SQL Server te implementeren.
* Eerder genoemde overwegingen met betrekking tot de distributie van VHD's over verschillende Azure Storage Accounts zijn ook van toepassing op deze methode van implementaties. Betekent dat de I/O-bewerkingen meetellen voor de limieten van het Azure Storage-account.
* In plaats van rekening te houden met het I/O-quotum voor opslag van de VM, wordt het verkeer tegen opslagblobs die de SQL Server-gegevens en logboekbestanden vertegenwoordigen, verwerkt in de netwerkbandbreedte van de VM van het specifieke VM-type. Raadpleeg het artikel [Grootte voor virtuele Windows-machines in Azure voor](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)netwerk- en opslagbandbreedte van een bepaald VM-type.
* Als gevolg van het indrukken van bestand I/O door het netwerkquotum, strandt u het opslagquotum meestal en gebruikt u daarmee de totale bandbreedte van de VM slechts gedeeltelijk.
* De IOPS- en I/O-doorvoerPrestatiedoelen die Azure Premium Storage heeft voor de verschillende schijfformaten zijn niet meer van toepassing. Zelfs als de blobs die u hebt gemaakt zich bevinden in Azure Premium Storage. De doelstellingen zijn gedocumenteerd het artikel [High-performance Premium Storage en beheerde schijven voor VM's](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage). Als gevolg van het plaatsen van SQL Server-gegevensbestanden en logboekbestanden rechtstreeks op blobs die zijn opgeslagen in Azure Premium Storage, kunnen de prestatiekenmerken verschillen in vergelijking met VHD's in Azure Premium Storage.
* Hostgebaseerde caching zoals beschikbaar voor Azure Premium Storage-schijven is niet beschikbaar wanneer SQL Server-gegevensbestanden rechtstreeks op Azure-blobs worden geplaatst.
* Op VM's uit de M-serie kan Azure Write Accelerator niet worden gebruikt ter ondersteuning van afschrijvingen van submilliseconde ten opzichte van het SQL Server-transactielogboekbestand. 

Details van deze functionaliteit vindt u in het artikel [SQL Server-gegevensbestanden in Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-2017)

Aanbeveling voor productiesystemen is om deze configuratie te vermijden en liever de plaatsingen van SQL Server-gegevens en logbestanden in Azure Premium Storage VHDs te kiezen in plaats van rechtstreeks op Azure blobs.


## <a name="sql-server-2014-buffer-pool-extension"></a>SQL Server 2014-buffergroepextensie
SQL Server 2014 heeft een nieuwe functie geïntroduceerd, die [Buffer Pool Extension](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension?view=sql-server-2017)wordt genoemd. Deze functionaliteit breidt de bufferpool van SQL Server uit, die in het geheugen wordt bewaard met een cache op het tweede niveau die wordt ondersteund door lokale SSD's van een server of vm. De bufferpoolextensie maakt het mogelijk om een grotere werkset gegevens 'in het geheugen' te houden. In vergelijking met toegang tot Azure Standard Storage is de toegang tot de extensie van de buffergroep, die is opgeslagen op lokale SSD's van een Azure VM, veel factoren sneller. Als u buffergroepextensie vergelijkt met Azure Premium Storage Read Cache, zoals aanbevolen voor SQL Server-gegevensbestanden, worden geen significante voordelen verwacht voor Buffer Pool-extensies. Reden is dat beide caches (SQL Server Buffer Pool Extension en Premium Storage Read Cache) de lokale schijven van het Azure compute node gebruiken.

Ervaringen die in de tussentijd zijn opgedaan met SQL Server Buffer Pool Extension met SAP-workload zijn gemengd en staan nog steeds geen duidelijke aanbevelingen toe over het al dan niet gebruiken ervan in alle gevallen. Het ideale geval is dat de werkset die de SAP-toepassing nodig heeft, in het hoofdgeheugen past. Met Azure biedt ondertussen VM's die worden geleverd met maximaal 4 TB geheugen, moet het haalbaar zijn om de werkset in het geheugen te houden. Vandaar dat het gebruik van Buffer Pool Extension is beperkt tot een aantal zeldzame gevallen en mag niet een mainstream geval.  

## <a name="backuprecovery-considerations-for-sql-server"></a>Overwegingen voor back-up/herstel voor SQL Server
Bij het implementeren van SQL Server in Azure moet uw back-upmethodologie worden gecontroleerd. Zelfs als het systeem geen productiesysteem is, moet er periodiek een back-up worden back-ups van de SAP-database die wordt gehost door SQL Server. Aangezien Azure Storage drie afbeeldingen bewaart, is een back-up nu minder belangrijk voor het compenseren van een opslagcrash. De prioriteit reden voor het handhaven van een goede back-up en herstelplan is meer dat u compenseren voor logische / handmatige fouten door het verstrekken van point in time recovery mogelijkheden. Het doel is dus om back-ups te gebruiken om de database terug te herstellen naar een bepaald tijdstip of om de back-ups in Azure te gebruiken om een ander systeem te zaaien door de bestaande database te kopiëren. 

Om te kijken naar verschillende SQL Server back-up mogelijkheden in Azure lees het artikel [Back-up en herstel voor SQL Server in Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery). Het artikel behandelt verschillende mogelijkheden.

### <a name="manual-backups"></a>Handmatige back-ups
Je hebt verschillende mogelijkheden om 'handmatige' back-ups uit te voeren door:

1. Het uitvoeren van conventionele SQL Server-back-ups op direct aangesloten Azure-schijven. Deze methode heeft als voordeel dat u de back-ups snel beschikbaar hebt voor systeemvernieuwingen en het opbouwen van nieuwe systemen als kopieën van bestaande SAP-systemen
2.  SQL Server 2012 CU4 en hoger kunnen back-ups maken van databases naar een AZURE-opslag-URL.
3.  Back-ups voor bestandsmomentopnamen voor databasebestanden in Azure Blob Storage. Deze methode werkt alleen wanneer uw SQL Server-gegevens en logboekbestanden zich bevinden in Azure blob-opslag

De eerste methode is ook in veel gevallen in de on-premises wereld bekend en toegepast. Toch, het laat je met de taak om de langere termijn back-up locatie op te lossen. Aangezien u uw back-ups niet 30 of meer dagen wilt bewaren in de lokaal bevestigde Azure Storage, hebt u de behoefte om Azure Backup Services te gebruiken of een ander back-up-/herstelprogramma van derden dat toegangs- en bewaarbeheer voor uw back-ups bevat. Of u bouwt een grote bestandsserver in Azure uit met Windows-opslagruimten.

De tweede methode wordt dichter bij het artikel [SQL Server Backup naar URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017)beschreven. Verschillende versies van SQL Server hebben een aantal variaties in deze functionaliteit. Bekijk daarom de documentatie voor uw specifieke SQL Server-releasecontrole. Belangrijk om op te merken dat dit artikel een heleboel beperkingen bevat. Je hebt ofwel de mogelijkheid om de back-up uit te voeren tegen:

- Eén Azure-paginablob, die vervolgens de back-upgrootte beperkt tot 1000 GB. Dit beperkt ook de doorvoer die u bereiken.
- Meerdere (tot 64) Azure-blokblobs, waarmee een theoretische back-upgrootte van 12 TB kan worden ingeschakeld. Uit tests met klantendatabases bleek echter dat de maximale back-upgrootte kleiner kan zijn dan de theoretische limiet. In dit geval bent u verantwoordelijk voor het beheer van het behoud van back-ups en toegang tot de back-ups.


### <a name="automated-backup-for-sql-server"></a>Automatische back-up voor SQL Server
Automated Backup biedt een automatische back-upservice voor SQL Server Standard- en Enterprise-edities die worden uitgevoerd in een Windows VM in Azure. Deze service wordt geleverd door de [SQL Server IaaS Agent Extension](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension), die automatisch wordt geïnstalleerd op SQL Server Windows virtuele machine afbeeldingen in de Azure-portal. Als u uw eigen BE-afbeeldingen implementeert met SQL Server geïnstalleerd, moet u de VM-extensies afzonderlijk installeren. De stappen die nodig zijn gedocumenteerd in dit [artikel](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

Meer details over de mogelijkheden van deze methode vindt u in deze artikelen:

- SQL Server 2014: [automatische back-up voor SQL Server 2014 Virtual Machines (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)
- SQL Server 2016/2017: [Geautomatiseerde back-up v2 voor Azure Virtual Machines (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)

Als u de documentatie bekijkt, u zien dat de functionaliteit met de recentere SQL Server-releases is verbeterd. Meer details over sql server geautomatiseerde back-ups worden vrijgegeven in het artikel [SQL Server Managed Backup naar Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017). De theoretische limiet voor de grootte van de back-up is 12 TB.  De geautomatiseerde back-ups kunnen een goede methode zijn voor back-upformaten tot 12 TB. Aangezien meerdere blobs parallel zijn geschreven, u een doorvoer van meer dan 100 MB/sec verwachten. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Azure Backup voor SQL Server VM's
Deze nieuwe methode van SQL Server-back-ups wordt vanaf juni 2018 aangeboden als openbare preview door Azure Backup-services. De methode om SQL Server te back-upten is hetzelfde als andere hulpprogramma's van derden, namelijk de SQL Server VSS/VDI-interface om back-ups naar een doellocatie te streamen. In dit geval is de doellocatie Azure Recovery Service vault.

Een meer dan gedetailleerde beschrijving van deze back-up methode, die tal van voordelen van centrale back-up configuraties, monitoring en beheer voegt is [hier](https://docs.microsoft.com/azure/backup/backup-azure-sql-database)beschikbaar. 


### <a name="third-party-backup-solutions"></a>Back-upoplossingen van derden
Voor een groot aantal SAP-klanten was er geen mogelijkheid om opnieuw te beginnen en complete nieuwe back-upoplossingen te introduceren voor het deel van hun SAP-landschap dat op Azure werd uitgevoerd. Als gevolg hiervan moesten de bestaande back-upoplossingen worden gebruikt en uitgebreid naar Azure. Het uitbreiden van bestaande back-upoplossingen naar Azure werkte meestal goed met de meeste van de belangrijkste leveranciers in deze ruimte. 


## <a name="using-a-sql-server-image-out-of-the-microsoft-azure-marketplace"></a><a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Een SQL Server-afbeelding gebruiken vanuit de Microsoft Azure Marketplace
Microsoft biedt VM's aan in de Azure Marketplace, die al versies van SQL Server bevatten. Voor SAP-klanten die licenties nodig hebben voor SQL Server en Windows, kan het gebruik van deze afbeeldingen een kans zijn om de behoefte aan licenties te dekken door VM's op te draaien met SQL Server die al is geïnstalleerd. Om dergelijke afbeeldingen voor SAP te gebruiken, moeten de volgende overwegingen worden gemaakt:

* De SQL Server-niet-evaluatieversies krijgen hogere kosten dan een 'Windows-only' VM die is geïmplementeerd vanuit Azure Marketplace. Zie deze artikelen om <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> prijzen <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>te vergelijken: en . 
* U alleen SQL Server-releases gebruiken, die worden ondersteund door SAP.
* De collatie van de SQL Server-instantie, die is geïnstalleerd in de VM's die in de Azure Marketplace worden aangeboden, is niet de collatie die SAP NetWeaver vereist. U de collatie al met de aanwijzingen in de volgende sectie.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>De SQL Server-collatie van een Microsoft Windows/SQL Server VM wijzigen
Aangezien de SQL Server-afbeeldingen in de Azure Marketplace niet zijn ingesteld om de collatie te gebruiken, wat vereist is voor SAP NetWeaver-toepassingen, moet deze onmiddellijk na de implementatie worden gewijzigd. Voor SQL Server kan deze wijziging van collatie worden uitgevoerd met de volgende stappen zodra de VM is geïmplementeerd en een beheerder zich kan aanmelden bij de geïmplementeerde VM:

* Open een Windows-opdrachtvenster als beheerder.
* Wijzig de map in C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Voer de opdracht uit: Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name`> /SQLCOLLATION=SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> is het account, dat is gedefinieerd als het beheerdersaccount bij het voor het eerst implementeren van de VM via de galerie.

Het proces duurt slechts een paar minuten. Voer de volgende stappen uit om ervoor te zorgen dat de stap met het juiste resultaat is geëindigd:

* Open SQL Server Management Studio.
* Open een queryvenster.
* Voer de opdracht sp_helpsort uit in de SQL Server-hoofddatabase.

Het gewenste resultaat moet eruit zien als:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Als het resultaat anders is, STOP met het implementeren van SAP en onderzoek je waarom de installatieopdracht niet werkte zoals verwacht. Implementatie van SAP NetWeaver-toepassingen op SQL Server-instantie met verschillende SQL Server-codepagina's dan de hierboven genoemde is **NIET** ondersteund.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server Hoge beschikbaarheid voor SAP in Azure
Met SQL Server in Azure IaaS-implementaties voor SAP hebt u verschillende mogelijkheden om de DBMS-laag te implementeren die zeer beschikbaar is. Zoals besproken in [Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-werkbelasting](dbms_guide_general.md) al, Azure bieden verschillende up-time SLA's voor een enkele VM en een paar VM's geïmplementeerd in een Azure Availability Set. Aangenomen wordt dat u naar de up-time SLA rijdt voor uw productie-implementaties waarvoor de implementatie in Azure Availability Sets vereist is. In een dergelijk geval moet u minimaal twee VM's implementeren in een dergelijke beschikbaarheidsset. Met één VM wordt het actieve SQL Server-exemplaar uitgevoerd. De andere VM voert de passieve instantie uit

### <a name="sql-server-clustering-using-windows-scale-out-file-server"></a>SQL Server-clustering met Windows Scale-out Bestandsserver
Met Windows Server 2016 heeft Microsoft [Storage Spaces Direct](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)geïntroduceerd. Sql Server FCI-clustering wordt ondersteund op basis van direct implementatie van Storage Spaces Direct. Details zijn te vinden in het artikel [SQL Server Failover clusterinstance configureren op Azure Virtual Machines.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster) De oplossing vereist ook een Azure load balancer om te gaan met het virtuele IP-adres van de clusterbronnen. De SQL Server-databasebestanden worden opgeslagen in opslagruimten. Daarom is het een gegeven dat u de Windows-opslagruimten moet opbouwen op basis van Azure Premium Storage. Aangezien deze oplossing nog niet al te lang wordt ondersteund, zijn er geen bekende SAP-klanten die deze oplossing gebruiken in SAP-productiescenario's.  

### <a name="sql-server-log-shipping"></a>Verzending van SQL Server-logboeken
Een van de methoden van hoge beschikbaarheid (HA) is SQL Server Log Shipping. Als de VM's die deelnemen aan de HA-configuratie een werknaamoplossing hebben, is er geen probleem en verschilt de installatie in Azure niet van elke installatie die on-premises wordt uitgevoerd. Met betrekking tot het opzetten van Log Shipping en de principes rond Log Shipping. Details van SQL Server Log Shipping vindt u in het artikel [Over Logboekverzending (SQL Server).](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server?view=sql-server-2017)

De SQL Server-logverzendfunctionaliteit werd in Azure nauwelijks gebruikt om een hoge beschikbaarheid binnen één Azure-regio te bereiken. In de volgende scenario's gebruikten SAP-klanten echter logboekverzending die succesvol waren in combinatie met Azure:

- Scenario's voor noodherstel van de ene Azure-regio naar een andere Azure-regio
- Disaster Recovery-configuratie van on-premises naar een Azure-regio
- Cut-over scenario's van on-premises naar Azure. In die gevallen wordt logboekverzending gebruikt om de nieuwe DBMS-implementatie in Azure te synchroniseren met het on-premises productiesysteem. Op het moment van het overmaken wordt de productie stilgelegd en wordt ervoor gezorgd dat de laatste en laatste transactielogboekback-ups zijn overgedragen naar de Azure DBMS-implementatie. Vervolgens wordt de Azure DBMS-implementatie geopend voor productie.  



### <a name="database-mirroring"></a>Database Mirroring
Database Mirroring zoals ondersteund door SAP (zie SAP Note [965908)]is gebaseerd op het definiëren van een failoverpartner in de SAP-verbindingstekenreeks. Voor de cross-premises-aanvragen gaan we ervan uit dat de twee VM's zich in hetzelfde domein bevinden en dat de gebruikerscontext van de twee SQL Server-exemplaren ook onder een domeingebruiker worden uitgevoerd en voldoende bevoegdheden hebben in de twee betrokken SQL Server-instanties. Daarom verschilt de installatie van Database Mirroring in Azure niet tussen een typische on-premises installatie/configuratie.

Met betrekking tot Cloud-Only implementaties, de makkelijkste methode is om een ander domein setup in Azure om die DBMS VM's (en idealiter dedicated SAP VM's) binnen een domein.

Als een domein niet mogelijk is, kan men ook certificaten gebruiken voor de database spiegelende eindpunten zoals hier beschreven:<https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Een zelfstudie voor het instellen van Database Mirroring in Azure vindt u hier:<https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>SQL Server Always On
Aangezien Always On wordt ondersteund voor SAP on-premises (zie SAP Note [1772688),]wordt het ondersteund in combinatie met SAP in Azure. Er zijn een aantal speciale overwegingen met betrekking tot het implementeren van de SQL Server Availability Group Listener (niet te verwarren met de Azure Availability Set), omdat Azure op dit moment geen AD/DNS-object kan maken omdat het on-premises mogelijk is. Daarom zijn er verschillende installatiestappen nodig om het specifieke gedrag van Azure te overwinnen.

Enkele overwegingen met behulp van een Listener beschikbaarheidsgroep zijn:

* Het gebruik van een typelistener voor beschikbaarheidsgroepen is alleen mogelijk met Windows Server 2012 of hoger als gastbesturingssysteem van de VM. Voor Windows Server 2012 moet u ervoor zorgen dat deze patch wordt toegepast:<https://support.microsoft.com/kb/2854082> 
* Voor Windows Server 2008 R2 bestaat deze patch niet en always on moet op dezelfde manier worden gebruikt als Database Mirroring door een failoverpartner op te geven in de verbindingstekenreeks (gedaan via de SAP default.pfl parameter dbs/mss/server - zie SAP Note [965908]).
* Wanneer u een listener voor beschikbaarheidsgroep gebruikt, moeten de database-VM's worden gekoppeld aan een speciale load balancer. Om te voorkomen dat Azure nieuwe IP-adressen toewijst in gevallen waarin beide VM's overigens worden afgesloten, moet men statische IP-adressen toewijzen aan de netwerkinterfaces van die VM's in de Always On-configuratie (het definiëren van een statisch IP-adres wordt in [dit][virtual-networks-reserved-private-ip] artikel beschreven)
* Er zijn speciale stappen vereist bij het bouwen van de WSFC-clusterconfiguratie waarbij een speciaal IP-adres moet worden toegewezen, omdat Azure met de huidige functionaliteit de clusternaam hetzelfde IP-adres toewijst als het knooppunt waarop het cluster wordt gemaakt. Dit betekent dat er een handmatige stap moet worden uitgevoerd om een ander IP-adres aan het cluster toe te wijzen.
* De listener voor beschikbaarheidsgroepen wordt gemaakt in Azure met TCP/IP-eindpunten, die zijn toegewezen aan de VM's waarop de primaire en secundaire replica's van de groep Beschikbaarheid worden uitgevoerd.
* Mogelijk is het nodig om deze eindpunten te beveiligen met ACL's.

Gedetailleerde documentatie over het implementeren van Always On met SQL Server in Azure VM's lijsten zoals:

- [Maak kennis met SQL Server Always On-beschikbaarheidsgroepen op virtuele Azure-machines.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
- [Configureer een groep Always On beschikbaarheid op virtuele Azure-machines in verschillende regio's](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr).
- [Een load balancer configureren voor een groep Always On beschikbaarheid in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).

>[!NOTE]
> Als u de Azure load balancer configureert voor het virtuele IP-adres van de listener Beschikbaarheidsgroep, controleert u of de DirectServerReturn is geconfigureerd. als u deze optie configureert, wordt de latentie van de netwerkronde reis tussen de SAP-toepassingslaag en de DBMS-laag verminderd. 

SQL Server Always On is de meest gebruikte functionaliteit voor hoge beschikbaarheid en disaster recovery die in Azure wordt gebruikt voor SAP-workloadimplementaties. De meeste klanten gebruiken Always On voor hoge beschikbaarheid binnen één Azure-regio. Als de implementatie beperkt is tot slechts twee knooppunten, hebt u twee opties voor connectiviteit:

- De listener voor beschikbaarheidsgroep gebruiken. Met de listener voor beschikbaarheidsgroep moet u een Azure-load balancer implementeren. Dit is meestal de standaardmethode voor implementatie. SAP-toepassingen worden geconfigureerd om verbinding te maken met de listener beschikbaarheidsgroep en niet tegen één knooppunt
- Met behulp van de verbindingsparameters van SQL Server Database Mirroring. In dit geval moet u de connectiviteit van de SAP-toepassingen zo configureren dat beide knooppuntnamen worden genoemd. Exacte details van een dergelijke SAP-zijconfiguratie worden gedocumenteerd in SAP Note [#965908.](https://launchpad.support.sap.com/#/notes/965908) Als u deze optie gebruikt, hoeft u geen listener beschikbaarheidsgroep te configureren. En met dat geen Azure load balancer voor de SQL Server hoge beschikbaarheid. Als gevolg hiervan is de netwerklatentie tussen de SAP-toepassingslaag en de DBMS-laag lager omdat het binnenkomende verkeer naar de SQL Server-instantie niet wordt doorgestuurd via de Azure-load balancer. Maar vergeet niet, deze optie werkt alleen als u uw beschikbaarheidsgroep beperkt tot twee instanties. 

Heel wat klanten maken gebruik van de SQL Server Always On-functionaliteit voor extra disaster recovery-functionaliteit tussen Azure-regio's. Verschillende klanten maken ook gebruik van de mogelijkheid om back-ups uit te voeren van een secundaire replica. 

## <a name="sql-server-transparent-data-encryption"></a>Transparante gegevensversleuteling van SQL Server
Er is een aantal klanten die SQL Server [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) gebruiken bij het implementeren van hun SAP SQL Server-databases in Azure. De SQL Server TDE-functionaliteit wordt volledig ondersteund door SAP (zie SAP Note [#1380493).](https://launchpad.support.sap.com/#/notes/1380493) 

### <a name="applying-sql-server-tde"></a>SQL Server TDE toepassen
In gevallen waarin u een heterogene migratie uitvoert van een andere DBMS, die on-premises wordt uitgevoerd, naar Windows/SQL Server die in Azure wordt uitgevoerd, moet u van tevoren uw lege doeldatabase in SQL Server maken. Als volgende stap zou u SQL Server TDE-functionaliteit toepassen. Terwijl u nog steeds uw productiesysteem on-premises uitvoert. Reden die u wilt uitvoeren in deze volgorde is dat het proces van het versleutelen van de lege database kan een tijdje duren. De SAP-importprocessen importeren de gegevens vervolgens in de versleutelde database tijdens de downtimefase. De overhead van het importeren in een versleutelde database heeft een veel lagere tijdimpact dan het versleutelen van de database na de exportfase in de down time fase. Negatieve ervaringen waar gemaakt wanneer het proberen om TDE toe te passen met SAP werkbelasting draait op de top van de database. Daarom is aanbeveling het behandelen van de implementatie van TDE als een activiteit die moet worden gedaan zonder SAP-werkbelasting in de specifieke database.

In gevallen waarin u SAP SQL Server-databases van on-premises naar Azure verplaatst, raden we u aan te testen op welke infrastructuur u de versleuteling het snelst laten toepassen. Houd hiervoor rekening met deze feiten:

- U niet definiëren hoeveel threads worden gebruikt om gegevensversleuteling toe te passen op de database. Het aantal threads is sterk afhankelijk van het aantal schijfvolumes waarover SQL Server-gegevens en logbestanden worden verdeeld. Betekent dat de meer verschillende volumes (drive letters), hoe meer threads zullen worden betrokken in parallel aan de encryptie uit te voeren. Een dergelijke configuratie is in tegenspraak met eerdere suggestie voor schijfconfiguratie bij het bouwen van één of een kleiner aantal opslagruimten voor de SQL Server-databasebestanden in Azure VM's. Een configuratie met een klein aantal volumes zou leiden tot een klein aantal threads het uitvoeren van de encryptie. Een enkele thread versleutelen is het lezen van 64KB omvang, versleutelt het en schrijf vervolgens een record in de transactie log bestand, vertellen dat de omvang werd versleuteld. Als gevolg hiervan is de belasting op het transactielogboek gematigd.
- In oudere SQL Server-releases kreeg back-upcompressie geen efficiëntie meer toen u uw SQL Server-database versleutelde. Dit gedrag kan zich ontwikkelen tot een probleem wanneer uw plan was om uw SQL Server-database on-premises te versleutelen en vervolgens een back-up naar Azure te kopiëren om de database in Azure te herstellen. SQL Server-back-upcompressie bereikt meestal een compressieverhouding van factor 4.
- Met SQL Server 2016 introduceerde SQL Server nieuwe functionaliteit waarmee versleutelde databases ook op een efficiënte manier kunnen worden gecomprimeerd. Zie [deze blogs](https://blogs.msdn.microsoft.com/sqlcat/2016/06/20/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases/) voor enkele details.
 
Als u de toepassing van TDE-versleuteling alleen zonder tot weinig SAP-workload behandelt, moet u in uw specifieke configuratie testen of het beter is om TDE on-premises toe te passen op uw SAP-database of dit in Azure te doen. In Azure hebt u zeker meer flexibiliteit op het gebied van overprovisioningsinfrastructuur en krimpt u de infrastructuur nadat TDE is toegepast.

### <a name="using-azure-key-vault"></a>Azure Key Vault gebruiken
Azure biedt de service van een [Key Vault](https://azure.microsoft.com/services/key-vault/) om versleutelingssleutels op te slaan. SQL Server aan de andere kant biedt een connector om Azure Key Vault te gebruiken als winkel voor de TDE-certificaten.

Meer details voor het gebruik van Azure Key Vault voor SQL Server TDE-lijsten, zoals:

- [Uitbreidbaar sleutelbeheer met Azure Key Vault (SQL Server).](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server?view=sql-server-2017)
- [SQL Server TDE Extensible Key Management Using Azure Key Vault - Setup Steps](https://docs.microsoft.com/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault?view=sql-server-2017).
- [SQL Server Connector Onderhoud & probleemoplossing](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting?view=sql-server-2017).
- [Meer vragen van klanten over SQL Server Transparent Data Encryption – TDE + Azure Key Vault](https://blogs.msdn.microsoft.com/saponsqlserver/2017/04/04/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault/).


>[!IMPORTANT]
>Met SQL Server TDE, met name met Azure key Vault, wordt het aanbevolen om de nieuwste patches van SQL Server 2014, SQL Server 2016 en SQL Server 2017 te gebruiken. Reden is dat op basis van feedback van klanten, optimalisaties en fixes werd toegepast op de code. Controleer bijvoorbeeld [KBA-#4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm).
>  

## <a name="general-sql-server-for-sap-on-azure-summary"></a><a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Algemene SQL Server voor SAP op Azure-overzicht
Er zijn veel aanbevelingen in deze handleiding en we raden u aan deze meer dan één keer te lezen voordat u uw Azure-implementatie plant. In het algemeen, hoewel, zorg ervoor dat de top algemene DBMS op Azure-specifieke aanbevelingen te volgen:

1. Gebruik de nieuwste DBMS-versie, zoals SQL Server 2017, die de meeste voordelen in Azure heeft. 
2. Plan uw SAP-systeemliggend saneren in Azure zorgvuldig om de lay-out van gegevensbestanden en Azure-beperkingen in evenwicht te brengen:
   * Heb niet te veel schijven, maar hebben genoeg om ervoor te zorgen dat u uw vereiste IOPS te bereiken.
   * Als u beheerde schijven niet gebruikt, moet u er rekening mee houden dat IOPS ook beperkt is per Azure Storage-account en dat opslagaccounts beperkt zijn binnen elk Azure-abonnement[(meer details).][azure-resource-manager/management/azure-subscription-service-limits] 
   * Streep alleen over schijven als u een hogere doorvoer moet bereiken.
3. Installeer nooit software of plaats bestanden die persistentie vereisen op de D:\ drive als het is niet-permanent en alles op dit station is verloren bij een Windows reboot.
4. Gebruik schijfcache niet voor Azure Standard Storage.
5. Gebruik geen azure geo-gerepliceerde Azure Standard Storage Accounts.  Gebruik Lokaal redundant voor DBMS-workloads.
6. Gebruik de HA/DR-oplossing van uw DBMS-leverancier om databasegegevens te repliceren.
7. Gebruik altijd Naamomzetting, vertrouw niet op IP-adressen.
8. Pas SQL Server TDE toe met SQL Server-patches.
9. Gebruik de hoogst mogelijke databasecompressie. Dat is paginacompressie voor SQL Server.
10. Wees voorzichtig met SQL Server-afbeeldingen van de Azure Marketplace. Als u de SQL Server-een gebruikt, moet u de instantiecollatie wijzigen voordat u een SAP NetWeaver-systeem installeert.
11. Installeer en configureer de SAP Host Monitoring voor Azure zoals beschreven in [Implementatiehandleiding.][deployment-guide]
