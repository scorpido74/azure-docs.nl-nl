---
title: Oracle Azure Virtual Machines DBMS-implementatie voor SAP-workload | Microsoft Documenten
description: DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor Oracle
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
ms.date: 12/14/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a23fb981e24f6152d99b76bd72115f8159f5d60f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645841"
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Azure Virtual Machines DBMS-implementatie voor SAP-workload

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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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


Dit document bestrijkt verschillende gebieden waar u rekening mee moet houden wanneer u Oracle Database implementeert voor SAP-workload in Azure IaaS. Voordat u dit document leest, raden we u aan [overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-werkbelasting te](dbms_guide_general.md)lezen. We raden u ook aan andere handleidingen te lezen in de [SAP-werkbelasting op Azure-documentatie.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) 

Informatie over Oracle-versies en bijbehorende OS-versies die worden ondersteund voor het uitvoeren van SAP op Oracle op Azure in SAP Note [2039619.]

Algemene informatie over het uitvoeren van SAP Business Suite op Oracle is te vinden bij [SAP op Oracle.](https://www.sap.com/community/topic/oracle.html)
Oracle-software wordt ondersteund door Oracle om te draaien op Microsoft Azure. Raadpleeg de [veelgestelde vragen](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)van Oracle en Microsoft Azure voor meer informatie over algemene ondersteuning voor Windows Hyper-V en Azure. 

## <a name="sap-notes-relevant-for-oracle-sap-and-azure"></a>SAP Notes relevant voor Oracle, SAP en Azure 

De volgende SAP Notes zijn gerelateerd aan SAP op Azure.

| Notitienummer | Titel |
| --- | --- |
| [1928533] |SAP-toepassingen op Azure: ondersteunde producten en Azure VM-typen |
| [2015553] |SAP op Microsoft Azure: vereisten voor ondersteuning |
| [1999351] |Probleemoplossing voor verbeterde Azure-bewaking voor SAP |
| [2178632] |Belangrijkste monitoringstatistieken voor SAP op Microsoft Azure |
| [2191498] |SAP op Linux met Azure: verbeterde monitoring |
| [2039619] |SAP-toepassingen op Microsoft Azure met behulp van de Oracle-database: ondersteunde producten en versies |
| [2243692] |Linux op Microsoft Azure (IaaS) VM: SAP-licentieproblemen |
| [2069760] |Oracle Linux 7.x SAP installatie en upgrade |
| [1597355] |Swap-space aanbeveling voor Linux |
| [2171857] |Oracle Database 12c - ondersteuning voor bestandssystemen op Linux |
| [1114181] |Oracle Database 11g - ondersteuning voor bestandssystemen op Linux |

De exacte configuraties en functionaliteit die worden ondersteund door Oracle en SAP op Azure zijn gedocumenteerd in SAP Note [#2039619.](https://launchpad.support.sap.com/#/notes/2039619)

Windows en Oracle Linux zijn de enige besturingssystemen die worden ondersteund door Oracle en SAP op Azure. De veelgebruikte SLES- en RHEL Linux-distributies worden niet ondersteund voor het implementeren van Oracle-componenten in Azure. Oracle-componenten omvatten de Oracle Database-client, die door SAP-toepassingen wordt gebruikt om verbinding te maken met het Oracle DBMS. 

Uitzonderingen zijn volgens SAP Note [#2039619](https://launchpad.support.sap.com/#/notes/2039619)SAP-componenten die geen gebruik maken van de Oracle Database-client. Dergelijke SAP-componenten zijn de zelfstandige wachtrij van SAP, de berichtenserver, de replicatieservices van Enqueue, WebDispatcher en SAP Gateway.  

Zelfs als u uw Oracle DBMS- en SAP-toepassingsexemplaren op Oracle Linux uitvoert, u uw SAP Central Services op SLES of RHEL uitvoeren en beveiligen met een cluster op basis van pacemakers. Pacemaker als een framework met hoge beschikbaarheid wordt niet ondersteund op Oracle Linux.

## <a name="specifics-for-oracle-database-on-windows"></a>Bijzonderheden voor Oracle Database op Windows

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-windows"></a>Oracle-configuratierichtlijnen voor SAP-installaties in Azure VM's op Windows

In overeenstemming met de SAP-installatiehandleiding mogen Oracle-gerelateerde bestanden niet worden geïnstalleerd of in het systeemstuurprogramma voor de OS-schijf van een vm (station c:). Virtuele machines van verschillende grootte kunnen een wisselend aantal aangesloten schijven ondersteunen. Kleinere virtuele machinetypen kunnen een kleiner aantal aangesloten schijven ondersteunen. 

Als u kleinere VM's hebt, raden we u aan Oracle thuis, in het stadium, "saptrace", "saparch", "sapbackup", "sapcheck" of "sapreorg" in de OS-schijf te installeren/lokaliseren. Deze onderdelen van Oracle DBMS-componenten zijn niet intens op de I/O- en I/O-doorvoer. Dit betekent dat de OS-schijf de I/O-vereisten aankan. De standaardgrootte van de OS-schijf is 127 GB. 

Als er niet genoeg vrije ruimte beschikbaar is, kan de schijf worden [aangepast](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk) tot 2048 GB. Oracle Database- en redo-logboekbestanden moeten worden opgeslagen op afzonderlijke gegevensschijven. Er is een uitzondering voor de tijdelijke tabelruimte van Oracle. Tempfiles kunnen worden gemaakt op D:/ (niet-persistente schijf). De niet-permanente D:\ drive biedt ook een betere I/O latentie en doorvoer (met uitzondering van A-serie VM's). 

Als u de juiste hoeveelheid ruimte voor de tijdelijke bestanden wilt bepalen, u de grootte van de tijdelijke bestanden op bestaande systemen controleren.

### <a name="storage-configuration"></a>Opslagconfiguratie
Alleen Oracle met één exemplaar met NTFS-geformatteerde schijven wordt ondersteund. Alle databasebestanden moeten worden opgeslagen in het NTFS-bestandssysteem op Beheerde schijven (aanbevolen) of op VHD's. Deze schijven zijn gemonteerd op de Azure VM en zijn gebaseerd op [Azure-paginablobopslag](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) of [Azure Managed Disks.](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview) 

We raden ten zeerste aan [azure managed disks te](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview)gebruiken. We raden ook ten zeerste aan [om premium SSD's te](../../windows/disks-types.md) gebruiken voor uw Oracle Database-implementaties.

Netwerkstations of externe shares zoals Azure-bestandsservices worden niet ondersteund voor Oracle Database-bestanden. Zie voor meer informatie:

- [Introducing Microsoft Azure File Service (Introductie van Microsoft Azure File-service)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)

- [Persisting connections to Microsoft Azure Files (Permanente verbindingen met Microsoft Azure-bestanden)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)


Als u schijven gebruikt die zijn gebaseerd op Azure-paginablobopslag of Beheerde schijven, zijn de instructies in [Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-werkbelasting](dbms_guide_general.md) ook van toepassing op implementaties met Oracle Database.

Er bestaan quota voor IOPS-doorvoer voor Azure-schijven. Dit concept wordt uitgelegd in [Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-workload.](dbms_guide_general.md) De exacte quota zijn afhankelijk van het VM-type dat u gebruikt. Een lijst met VM-typen met hun quota is te vinden bij [Grootte voor virtuele Windows-machines in Azure.][virtual-machines-sizes-windows]

Zie SAP Note [1928533]om de ondersteunde Azure VM-typen te identificeren.

De minimale configuratie is als volgt: 

| Onderdeel | Schijf | Caching | Opslaggroep |
| --- | ---| --- | --- |
| \oracle\<SID>\origlogaA & mirrlogB | Premium | Geen | Niet vereist |
| \oracle\<SID>\origlogaB & mirrlogA | Premium | Geen | Niet vereist |
| \oracle\<SID>\sapdata1... N | Premium | Alleen-lezen | Kan worden gebruikt |
| \orakel\<SID>\oraarch | Standard | Geen | Niet vereist |
| Oracle Home, saptrace, ... | Besturingssysteemschijf | | Niet vereist |


Schijven selectie voor het hosten van online redo logs moet worden aangestuurd door IMP-vereisten. Het is mogelijk om alle sapdata1 op te slaan... n (tabelruimten) op één gemonteerde schijf, zolang de grootte, IOPS en doorvoer aan de vereisten voldoen. 

De prestatieconfiguratie is als volgt:

| Onderdeel | Schijf | Caching | Opslaggroep |
| --- | ---| --- | --- |
| \oracle\<SID>\origlogaA | Premium | Geen | Kan worden gebruikt  |
| \oracle\<SID>\origlogaB | Premium | Geen | Kan worden gebruikt |
| \oracle\<SID>\mirrlogAB | Premium | Geen | Kan worden gebruikt |
| \oracle\<SID>\mirrlogBA | Premium | Geen | Kan worden gebruikt |
| \oracle\<SID>\sapdata1... N | Premium | Alleen-lezen | Aanbevolen  |
| \oracle\SID\sapdata(n+1)* | Premium | Geen | Kan worden gebruikt |
| \oracle\<SID>\oraarch* | Premium | Geen | Niet vereist |
| Oracle Home, saptrace, ... | Besturingssysteemschijf | Niet vereist |

*(n+1): het hosten van SYSTEEM-, TEMP- en UNDO-tafelruimten. Het I/O-patroon van tabelruimten systeem en ongedaan maken zijn anders dan andere tabelruimten die toepassingsgegevens hosten. Geen caching is de beste optie voor de prestaties van het systeem en ongedaan maken tafelruimten.

*oraarch: opslagpool is niet nodig vanuit het oogpunt van prestaties. Het kan worden gebruikt om meer ruimte te krijgen.

Als er meer IOPS nodig is, raden we u aan Windows Storage Pools (alleen beschikbaar in Windows Server 2012 en hoger) te gebruiken om één groot logisch apparaat te maken via meerdere gemonteerde schijven. Deze aanpak vereenvoudigt de beheeroverhead voor het beheren van de schijfruimte en helpt u de moeite te vermijden om bestanden handmatig over meerdere gemonteerde schijven te distribueren.


#### <a name="write-accelerator"></a>Write Accelerator
Voor VM's uit de Azure M-serie kan de latentie die in de online redo-logboeken wordt geschreven, worden verminderd door factoren in vergelijking met Azure Premium Storage. Azure Write Accelerator inschakelen voor de schijven (VHD's) op basis van Azure Premium Storage die worden gebruikt voor online redo-logboekbestanden. Zie [Accelerator schrijven voor](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)meer informatie.


### <a name="backuprestore"></a>Back-up/herstel
Voor back-up-/herstelfunctionaliteit worden de SAP BR*-hulpprogramma's voor Oracle op dezelfde manier ondersteund als op standaard Windows Server-besturingssystemen. Oracle Recovery Manager (RMAN) wordt ook ondersteund voor back-ups op schijf en herstelt vanaf schijf.

U Azure Backup ook gebruiken om een vm-back-up met consistente toepassing uit te voeren. In het artikel [Plan uw VM-back-upinfrastructuur in Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction) hoe Azure Backup de Windows VSS-functionaliteit gebruikt voor het uitvoeren van toepassingsconsistente back-ups. De Oracle DBMS-releases die door SAP op Azure worden ondersteund, kunnen gebruikmaken van de VSS-functionaliteit voor back-ups. Zie voor meer informatie de [Basisconcepten van de Oracle-documentatie voor databaseback-up en herstel met VSS](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ntqrf/basic-concepts-of-database-backup-and-recovery-with-vss.html#GUID-C085101B-237F-4773-A2BF-1C8FD040C701).


### <a name="high-availability"></a>Hoge beschikbaarheid
Oracle Data Guard wordt ondersteund voor doeleinden met hoge beschikbaarheid en noodherstel. Om een automatische failover in Data Guard te bereiken, moet u Fast-Start Failover (FSFA) gebruiken. De Observer (FSFA) activeert de failover. Als u geen FSFA gebruikt, u alleen een handmatige failoverconfiguratie gebruiken.

Zie [Disaster recovery voor een Oracle Database 12c-database in een Azure-omgeving voor](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-disaster-recovery)meer informatie over disaster recovery voor Oracle-databases in Azure.

### <a name="accelerated-networking"></a>Versneld netwerken
Voor Oracle-implementaties op Windows raden we u ten zeerste aan om versneld te netwerken zoals beschreven in [Azure-versnelde netwerken.](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) Houd ook rekening met de aanbevelingen die worden gedaan in [Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-workload.](dbms_guide_general.md) 
### <a name="other"></a>Overige
[Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-workload](dbms_guide_general.md) beschrijft andere belangrijke concepten met betrekking tot implementaties van VM's met Oracle Database, waaronder Azure-beschikbaarheidssets en SAP-monitoring.

## <a name="specifics-for-oracle-database-on-oracle-linux"></a>Bijzonderheden voor Oracle Database op Oracle Linux
Oracle-software wordt ondersteund door Oracle om te draaien op Microsoft Azure met Oracle Linux als gastbesturingssysteem. Zie de [veelgestelde vragen](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)over Azure en Oracle voor meer informatie over algemene ondersteuning voor Windows Hyper-V en Azure. 

Het specifieke scenario van SAP-toepassingen die gebruikmaken van Oracle Databases wordt ook ondersteund. Details worden besproken in het volgende deel van het document.

### <a name="oracle-version-support"></a>Ondersteuning voor Oracle-versies
Zie SAP Note [2039619]voor informatie over welke Oracle-versies en bijbehorende OS-versies worden ondersteund voor het uitvoeren van SAP op Oracle op Oracle op Azure Virtual Machines.

Algemene informatie over het uitvoeren van SAP Business Suite op Oracle is te vinden in de [SAP-communitypagina](https://www.sap.com/community/topic/oracle.html)van Oracle.

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux"></a>Oracle-configuratierichtlijnen voor SAP-installaties in Azure VM's op Linux

In overeenstemming met SAP-installatiehandleidingen mogen Oracle-gerelateerde bestanden niet worden geïnstalleerd of in systeemstuurprogramma's voor de opstartschijf van een VM worden geïnstalleerd. Verschillende groottes van virtuele machines ondersteunen een wisselend aantal aangesloten schijven. Kleinere virtuele machinetypen kunnen een kleiner aantal aangesloten schijven ondersteunen. 

In dit geval raden we aan Oracle thuis, stage, saptrace, saparch, sapbackup, sapcheck of sapreorg te installeren/lokaliseren naar de schijf. Deze onderdelen van Oracle DBMS-componenten zijn niet intens op de I/O- en I/O-doorvoer. Dit betekent dat de OS-schijf de I/O-vereisten aankan. De standaardgrootte van de OS-schijf is 30 GB. U de opstartschijf uitbreiden met de Azure-portal, PowerShell of CLI. Nadat de opstartschijf is uitgebreid, u een extra partitie toevoegen voor Oracle-binaire bestanden.


### <a name="storage-configuration"></a>Opslagconfiguratie

De bestandssystemen van ext4, xfs of Oracle ASM worden ondersteund voor Oracle Database-bestanden op Azure. Alle databasebestanden moeten op deze bestandssystemen worden opgeslagen op basis van VHD's of Beheerde schijven. Deze schijven zijn gemonteerd op de Azure VM en zijn gebaseerd op [Azure-paginablobopslag](<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) of [Azure Managed Disks.](../../windows/managed-disks-overview.md)

Voor Oracle Linux UEK-kernels is een minimum aan UEK-versie 4 vereist om [Azure premium SSD's](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-caching)te ondersteunen.

Het wordt ten zeerste aanbevolen om [door Azure beheerde schijven](../../windows/managed-disks-overview.md)te gebruiken. Het wordt ook ten zeerste aanbevolen om [Azure premium-SSD's](../../windows/disks-types.md) te gebruiken voor uw Oracle Database-implementaties.

Netwerkstations of externe shares zoals Azure-bestandsservices worden niet ondersteund voor Oracle Database-bestanden. Raadpleeg de volgende artikelen voor meer informatie: 

- [Introducing Microsoft Azure File Service (Introductie van Microsoft Azure File-service)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)

- [Persisting connections to Microsoft Azure Files (Permanente verbindingen met Microsoft Azure-bestanden)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

Als u schijven gebruikt op basis van Azure-paginablobopslag of Beheerde schijven, zijn de instructies in [overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-werkbelasting](dbms_guide_general.md) ook van toepassing op implementaties met Oracle Database.

 Er bestaan quota voor IOPS-doorvoer voor Azure-schijven. Dit concept wordt uitgelegd in [Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-workload.](dbms_guide_general.md) De exacte quota zijn afhankelijk van het vm-type dat wordt gebruikt. Zie [Grootte voor virtuele Linux-machines in Azure voor][virtual-machines-sizes-linux]een lijst met VM-typen met hun quota.

Zie SAP Note [1928533]om de ondersteunde Azure VM-typen te identificeren.

Minimale configuratie:

| Onderdeel | Schijf | Caching | Strippen* |
| --- | ---| --- | --- |
| /oracle/\<SID>/origlogaA & mirrlogB | Premium | Geen | Niet vereist |
| /oracle/\<SID>/origlogaB & mirrlogA | Premium | Geen | Niet vereist |
| /oracle/\<SID>/sapdata1... N | Premium | Alleen-lezen | Kan worden gebruikt |
| /orakel/\<SID>/oraarch | Standard | Geen | Niet vereist |
| Oracle Home, saptrace, ... | Besturingssysteemschijf | | Niet vereist |

*Strippen: LVM streep of MDADM met RAID0

De schijfselectie voor het hosten van oracle's online redo-logboeken moet worden aangestuurd door IOPS-vereisten. Het is mogelijk om alle sapdata1 op te slaan... n (tabelruimten) op één gemonteerde schijf zolang het volume, de IOPS en de doorvoer aan de vereisten voldoen. 

Prestatieconfiguratie:

| Onderdeel | Schijf | Caching | Strippen* |
| --- | ---| --- | --- |
| /oracle/\<SID>/origlogaA | Premium | Geen | Kan worden gebruikt  |
| /oracle/\<SID>/origlogaB | Premium | Geen | Kan worden gebruikt |
| /oracle/\<SID>/mirrlogAB | Premium | Geen | Kan worden gebruikt |
| /oracle/\<SID>/mirrlogBA | Premium | Geen | Kan worden gebruikt |
| /oracle/\<SID>/sapdata1... N | Premium | Alleen-lezen | Aanbevolen  |
| /oracle/\<SID>/sapdata(n+1)* | Premium | Geen | Kan worden gebruikt |
| /orakel/\<SID>/oraarch* | Premium | Geen | Niet vereist |
| Oracle Home, saptrace, ... | Besturingssysteemschijf | Niet vereist |

*Strippen: LVM streep of MDADM met RAID0

*(n+1):hosting SYSTEM, TEMP en UNDO-tabelruimten: het I/O-patroon van systeem- en ongedaan makende tabelruimten verschilt van andere tabelruimten die toepassingsgegevens hosten. Geen caching is de beste optie voor de prestaties van het systeem en ongedaan maken tafelruimten.

*oraarch: opslagpool is niet nodig vanuit het oogpunt van prestaties.


Als er meer IOPS nodig is, raden we aan om LVM (Logical Volume Manager) of MDADM te gebruiken om één groot logisch volume te maken over meerdere gemonteerde schijven. Zie [Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-workload](dbms_guide_general.md) met betrekking tot richtlijnen en aanwijzingen over het gebruik van LVM of MDADM voor meer informatie. Deze aanpak vereenvoudigt de beheeroverhead van het beheer van de schijfruimte en helpt u de moeite te vermijden om bestanden handmatig over meerdere gemonteerde schijven te distribueren.


#### <a name="write-accelerator"></a>Write Accelerator
Voor Azure M-series VM's kan bij het gebruik van Azure Write Accelerator de latentie die in de online redo-logboeken wordt geschreven, worden verminderd door factoren in vergelijking met de prestaties van Azure Premium Storage. Azure Write Accelerator inschakelen voor de schijven (VHD's) op basis van Azure Premium Storage die worden gebruikt voor online redo-logboekbestanden. Zie [Accelerator schrijven voor](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)meer informatie.


### <a name="backuprestore"></a>Back-up/herstel
Voor back-up-/herstelfunctionaliteit worden de SAP BR*Tools voor Oracle op dezelfde manier ondersteund als op bare metal en Hyper-V. Oracle Recovery Manager (RMAN) wordt ook ondersteund voor back-ups op schijf en herstelt vanaf schijf.

Zie Back-ups maken en herstellen van [een Oracle Database 12c-database op een virtuele Azure Linux-machine](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-backup-recovery)voor meer informatie over hoe u Azure Backup- en Recovery-services gebruiken voor het maken van back-ups en het herstellen van Oracle-databases.

### <a name="high-availability"></a>Hoge beschikbaarheid
Oracle Data Guard wordt ondersteund voor doeleinden met hoge beschikbaarheid en noodherstel. Om een automatische failover in Data Guard te bereiken, moet u Fast-Start Failover (FSFA) gebruiken. De Observer-functionaliteit (FSFA) activeert de failover. Als u geen FSFA gebruikt, u alleen een handmatige failoverconfiguratie gebruiken. Zie [Oracle Data Guard implementeren op een virtuele Azure Linux-machine](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)voor meer informatie.


Disaster Recovery-aspecten voor Oracle-databases in Azure worden weergegeven in het artikel [Disaster recovery for an Oracle Database 12c-database in een Azure-omgeving.](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-disaster-recovery)

### <a name="accelerated-networking"></a>Versneld netwerken
Ondersteuning voor Azure Accelerated Networking in Oracle Linux wordt geleverd met Oracle Linux 7 Update 5 (Oracle Linux 7.5). Als u niet upgraden naar de nieuwste Oracle Linux 7.5-versie, is er mogelijk een tijdelijke oplossing door de RedHat Compatible Kernel (RHCK) te gebruiken in plaats van de Oracle UEK-kernel. 

Het gebruik van de RHEL kernel binnen Oracle Linux wordt ondersteund volgens SAP Note [#1565179](https://launchpad.support.sap.com/#/notes/1565179). Voor Azure Accelerated Networking moet de minimale RHCKL-kernelrelease 3.10.0-862.13.1.el7 zijn. Als u de UEK-kernel in Oracle Linux gebruikt in combinatie met [Azure Accelerated Networking,](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)moet u Oracle UEK-kernelversie 5 gebruiken.

Als u VM's implementeert vanuit een afbeelding die niet is gebaseerd op Azure Marketplace, moet u extra configuratiebestanden naar de VM kopiëren door de volgende code uit te voeren: 
<pre><code># Copy settings from GitHub to the correct place in the VM
sudo curl -so /etc/udev/rules.d/68-azure-sriov-nm-unmanaged.rules https://raw.githubusercontent.com/LIS/lis-next/master/hv-rhel7.x/hv/tools/68-azure-sriov-nm-unmanaged.rules 
</code></pre>


### <a name="other"></a>Overige
[Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-workload](dbms_guide_general.md) beschrijft andere belangrijke concepten met betrekking tot implementaties van VM's met Oracle Database, waaronder Azure-beschikbaarheidssets en SAP-monitoring.
