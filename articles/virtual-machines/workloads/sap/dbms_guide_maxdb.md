---
title: SAP MaxDB-, liveCache- en ContentServer-implementatie op Azure VM's | Microsoft Documenten
description: SAP MaxDB-, liveCache- en Content Server-implementatie op Azure
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
ms.date: 07/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 90de49ae3137735683bae6a18b5f7c8951b021ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645868"
---
# <a name="sap-maxdb-livecache-and-content-server-deployment-on-azure-vms"></a>SAP MaxDB-, liveCache- en ContentServer-implementatie op Azure VM's

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
[dbms-guide-figure-900]:media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png

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




Dit document bestrijkt verschillende gebieden waar u rekening mee moet houden bij de implementatie van MaxDB, liveCache en Content Server in Azure IaaS. Als voorwaarde voor dit document moet u de [DBMS-implementatie van document Overwegingen voor Azure Virtual Machines DBMS en](dbms_guide_general.md) andere handleidingen in de [SAP-werkbelasting op Azure-documentatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)hebben gelezen. 

## <a name="specifics-for-the-sap-maxdb-deployments-on-windows"></a>Bijzonderheden voor de SAP MaxDB-implementaties op Windows
### <a name="sap-maxdb-version-support-on-azure"></a>SAP MaxDB-versieondersteuning op Azure
SAP ondersteunt momenteel SAP MaxDB-versie 7.9 of hoger voor gebruik met SAP NetWeaver-gebaseerde producten in Azure. Alle updates voor SAP MaxDB-server, of JDBC- en ODBC-stuurprogramma's die worden gebruikt <https://support.sap.com/swdc>met SAP NetWeaver-gebaseerde producten worden uitsluitend geleverd via de SAP Service Marketplace op .
Algemene informatie over het uitvoeren van SAP NetWeaver op SAP MaxDB is te vinden op <https://www.sap.com/community/topic/maxdb.html>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Ondersteunde Microsoft Windows-versies en Azure VM-typen voor SAP MaxDB DBMS
Zie het volledige bares om de ondersteunde Microsoft Windows-versie voor SAP MaxDB DBMS op Azure te vinden:

* [SAP-productbeschikbaarheidsmatrix (PAM)][sap-pam]
* SAP Note [1928533]

Het wordt ten zeerste aanbevolen om de nieuwste versie van het besturingssysteem Microsoft Windows te gebruiken, namelijk Microsoft Windows 2016.

### <a name="available-sap-maxdb-documentation-for-maxdb"></a>Beschikbare SAP MaxDB Documentatie voor MaxDB
De bijgewerkte lijst met SAP MaxDB-documentatie vindt u in de volgende SAP Note [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP MaxDB-configuratierichtlijnen voor SAP-installaties in Azure VM's
#### <a name="storage-configuration"></a><a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Opslagconfiguratie
Aanbevolen procedures voor Azure-opslag voor SAP MaxDB volgen de algemene aanbevelingen die in hoofdstuk [opslagstructuur van een VM voor RDBMS-implementaties worden](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)genoemd.

> [!IMPORTANT]
> Net als andere databases heeft SAP MaxDB ook gegevens en logbestanden. In SAP MaxDB-terminologie is de juiste term echter "volume" (niet "bestand"). Zo zijn er SAP MaxDB datavolumes en logvolumes. Verwar deze niet met de schijfvolumes van het besturingssysteem. 
> 
> 

Kortom, je moet:

* Als u Azure Storage-accounts gebruikt, stelt u het Azure-opslagaccount dat de SAP MaxDB-gegevens en logboekvolumes (gegevens en logboekbestanden) bevat, in op **Lokale redundante opslag (LRS)** zoals gespecificeerd in [Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-werkbelasting.](dbms_guide_general.md)
* Scheid het IO-pad voor SAP MaxDB-gegevensvolumes (gegevensbestanden) van het IO-pad voor logboekvolumes (logboekbestanden). Het betekent dat SAP MaxDB datavolumes (databestanden) moeten worden geïnstalleerd op een logische schijf en SAP MaxDB log volumes (log bestanden) moeten worden geïnstalleerd op een andere logische schijf.
* Stel het juiste cachingtype in voor elke schijf, afhankelijk van of u deze gebruikt voor SAP MaxDB-gegevens of logboekvolumes (gegevens en logboekbestanden) en of u Azure Standard- of Azure Premium-opslag gebruikt, zoals beschreven in [Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-workload.](dbms_guide_general.md)
* Zolang het huidige IOPS-quotum per schijf aan de vereisten voldoet, is het mogelijk om alle gegevensvolumes op één gemonteerde schijf op te slaan en ook alle databaselogboekvolumes op een andere gemonteerde schijf op te slaan.
* Als er meer IOPS en/of ruimte nodig is, is het raadzaam om Microsoft Window Storage Pools (alleen beschikbaar in Microsoft Windows Server 2012 en hoger) te gebruiken om één groot logisch apparaat te maken via meerdere gemonteerde schijven. Zie ook [Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-workload](dbms_guide_general.md). Deze aanpak vereenvoudigt de beheeroverhead om de schijfruimte te beheren en voorkomt de moeite om bestanden handmatig over meerdere gemonteerde schijven te distribueren.
* het wordt ten zeerste aanbevolen om Azure Premium Storage te gebruiken voor MaxDB-implementaties. 

![Referentieconfiguratie van Azure IaaS VM voor SAP MaxDB DBMS](./media/dbms_maxdb_deployment_guide/Simple_disk_structure_maxdb.PNG)


#### <a name="backup-and-restore"></a><a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Back-up en herstel
Wanneer u SAP MaxDB in Azure implementeert, moet u uw back-upmethodologie bekijken. Zelfs als het systeem geen productief systeem is, moet er periodiek een back-up worden back-ups van de SAP-database die wordt gehost door SAP MaxDB. Aangezien Azure Storage drie afbeeldingen bewaart, is een back-up nu minder belangrijk om uw systeem te beschermen tegen opslagfouten en belangrijkere operationele of administratieve fouten. De belangrijkste reden voor het onderhouden van een goed back-up- en herstelplan is, zodat u logische of handmatige fouten compenseren door point-in-time herstelmogelijkheden te bieden. Het doel is dus om back-ups te gebruiken om de database te herstellen naar een bepaald tijdstip of om de back-ups in Azure te gebruiken om een ander systeem te zaaien door de bestaande database te kopiëren. 

Het maken van back-ups en het herstellen van een database in Azure werkt op dezelfde manier als voor on-premises systemen, zodat u standaard SAP MaxDB-back-up-/hersteltools gebruiken, die worden beschreven in een van de SAP MaxDB-documentatiedocumenten die worden vermeld in SAP Note [767598.] 

#### <a name="performance-considerations-for-backup-and-restore"></a><a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Prestatieoverwegingen voor back-up en herstel
Net als bij kale-metaal implementaties, back-up en herstel prestaties zijn afhankelijk van hoeveel volumes kunnen worden gelezen in parallel en de doorvoer van deze volumes. Daarom kan men aannemen:

* Hoe minder schijven worden gebruikt om de databaseapparaten op te slaan, hoe lager de totale leesdoorvoer
* Hoe minder doelen (Stripe Directories, schijven) om de back-up naar te schrijven, hoe lager de doorvoer

Om het aantal doelen te verhogen om naar te schrijven, zijn er twee opties die u gebruiken, eventueel in combinatie, afhankelijk van uw behoeften:

* Afzonderlijke volumes wijden voor back-up
* Het back-updoelvolume over meerdere gemonteerde schijven strippen om de IOPS-doorvoer op dat gestreepte schijfvolume te verbeteren
* Het hebben van afzonderlijke specifieke logische schijfapparaten voor:
  * SAP MaxDB back-upvolumes (d.w.z. bestanden)
  * SAP MaxDB-gegevensvolumes (d.w.z. bestanden)
  * SAP MaxDB-logboekvolumes (d.w.z. bestanden)

Het strippen van een volume over meerdere gemonteerde schijven is eerder besproken in [Considerations for Azure Virtual Machines DBMS-implementatie voor SAP-workload.](dbms_guide_general.md) 

#### <a name="other-considerations"></a><a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Andere overwegingen
Alle andere algemene gebieden, zoals Azure Availability Sets of SAP-monitoring, zijn ook van toepassing zoals beschreven in [Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-workload.](dbms_guide_general.md)  voor implementaties van VM's met de SAP MaxDB-database.
Andere SAP MaxDB-specifieke instellingen zijn transparant voor Azure VM's en worden beschreven in verschillende documenten die worden vermeld in SAP Note [767598] en in deze SAP Notes:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-deployments-on-windows"></a>Bijzonderheden voor SAP liveCache-implementaties op Windows
### <a name="sap-livecache-version-support"></a>Ondersteuning voor SAP liveCache-versie
Minimale versie van SAP liveCache ondersteund in Azure Virtual Machines is **SAP LC/LCAPPS 10.0 SP 25** inclusief **liveCache 7.9.08.31** en **LCA-Build 25**, uitgebracht voor **EhP 2 voor SAP SCM 7.0** en latere releases.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Ondersteunde Microsoft Windows-versies en Azure VM-typen voor SAP liveCache DBMS
Zie het volledige zeggen over de ondersteunde Microsoft Windows-versie voor SAP liveCache op Azure.

* [SAP-productbeschikbaarheidsmatrix (PAM)][sap-pam]
* SAP Note [1928533]

Het wordt ten zeerste aanbevolen om de nieuwste versie van het besturingssysteem Microsoft Windows Server te gebruiken. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP liveCache-configuratierichtlijnen voor SAP-installaties in Azure VM's
#### <a name="recommended-azure-vm-types-for-livecache"></a>Aanbevolen Azure VM-typen voor liveCache
Aangezien SAP liveCache een toepassing is die enorme berekeningen uitvoert, heeft de hoeveelheid en de snelheid van RAM en CPU een grote invloed op sap livecache-prestaties. 

Voor de Azure VM-typen die worden ondersteund door SAP (SAP Note [1928533),]worden alle virtuele CPU-resources die aan de VM zijn toegewezen, ondersteund door speciale fysieke CPU-bronnen van de hypervisor. Er vindt geen overprovisioning plaats (en dus geen concurrentie voor CPU-bronnen).

Voor alle Azure VM-instantietypen die door SAP worden ondersteund, wordt het VM-geheugen 100% toegewezen aan het fysieke geheugen - overprovisioning (over-commitment) wordt bijvoorbeeld niet gebruikt.

Vanuit dit perspectief is het ten zeerste aan te raden om de meest recente VM's uit de Dv2-, Dv3-, Ev3- en M-serie te gebruiken. De keuze van de verschillende VM-typen is afhankelijk van het geheugen dat u nodig hebt voor liveCache en de CPU-resources die u nodig hebt. Net als bij alle andere DBMS-implementaties is het raadzaam om Azure Premium Storage te gebruiken voor prestatiekritieke volumes.

#### <a name="storage-configuration-for-livecache-in-azure"></a>Opslagconfiguratie voor liveCache in Azure
Aangezien SAP liveCache is gebaseerd op SAP MaxDB-technologie, zijn alle aanbevelingen voor best practices voor Azure-opslag die in dit document worden beschreven, ook geldig voor SAP liveCache. 

#### <a name="dedicated-azure-vm-for-livecache-scenario"></a>Dedicated Azure VM voor liveCache-scenario
Aangezien SAP liveCache intensief gebruik maakt van rekenkracht, is het voor productief gebruik ten zeerste aan te raden om te implementeren op een speciale Azure Virtual Machine. 

![Dedicated Azure VM voor liveCache voor productieve use case](./media/dbms_maxdb_deployment_guide/700-livecach-prod.PNG)


#### <a name="backup-and-restore-for-livecache-in-azure"></a>Back-upmaken en herstellen voor liveCache in Azure
back-up en herstel, inclusief prestatieoverwegingen, worden al beschreven in de relevante SAP MaxDB-hoofdstukken in dit document. 

#### <a name="other-considerations"></a>Andere overwegingen
Alle andere algemene gebieden zijn al beschreven in het desbetreffende SAP MaxDB-hoofdstuk. 

## <a name="specifics-for-the-sap-content-server-deployment-on-windows-in-azure"></a>Bijzonderheden voor de SAP Content Server-implementatie op Windows in Azure
De SAP Content Server is een afzonderlijke, op servers gebaseerde component om inhoud zoals elektronische documenten in verschillende indelingen op te slaan. De SAP Content Server wordt geleverd door de ontwikkeling van technologie en moet worden gebruikt cross-applicatie voor alle SAP-toepassingen. Het is geïnstalleerd op een apart systeem. Typische inhoud is trainingsmateriaal en documentatie uit Knowledge Warehouse of technische tekeningen afkomstig van het mySAP PLM Document Management System. 

### <a name="sap-content-server-version-support-for-azure-vms"></a>Ondersteuning voor SAP-inhoudsserverversie voor Azure VM's
SAP ondersteunt momenteel:

* **SAP Content Server** met versie **6.50 (en hoger)**
* **SAP MaxDB versie 7.9**
* **Microsoft IIS (Internet Information Server) versie 8.0 (en hoger)**

Het wordt ten zeerste aanbevolen om de nieuwste versie van SAP Content Server te gebruiken, en de nieuwste versie van **Microsoft IIS.** 

Controleer de nieuwste ondersteunde versies van SAP Content Server en Microsoft IIS in de [SAP Product Availability Matrix (PAM).][sap-pam]

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Ondersteunde Microsoft Windows- en Azure VM-typen voor SAP-inhoudsserver
Zie voor meer informatie over de ondersteunde Windows-versie voor SAP Content Server op Azure:

* [SAP-productbeschikbaarheidsmatrix (PAM)][sap-pam]
* SAP Note [1928533]

Het wordt ten zeerste aanbevolen om de nieuwste versie van Microsoft Windows Server te gebruiken.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Richtlijnen voor SAP-inhoudsserverconfiguratie voor SAP-installaties in Azure VM's
#### <a name="storage-configuration-for-content-server-in-azure"></a>Opslagconfiguratie voor inhoudsserver in Azure
Als u SAP Content Server configureert om bestanden op te slaan in de SAP MaxDB-database, zijn alle aanbevelingen voor aanbevolen procedures voor Azure-opslag die in dit document worden vermeld, ook geldig voor het SAP-inhoudsserverscenario. 

Als u SAP Content Server configureert om bestanden in het bestandssysteem op te slaan, wordt aanbevolen een specifiek logisch station te gebruiken. Met Windows-opslagruimten u ook de logische schijfgrootte en de IOPS-doorvoer vergroten, zoals beschreven in [Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-werkbelasting.](dbms_guide_general.md) 

#### <a name="sap-content-server-location"></a>LOCATIE VAN SAP-inhoudsserver
SAP Content Server moet worden geïmplementeerd in dezelfde Azure-regio en Azure VNET waar het SAP-systeem wordt geïmplementeerd. U zelf bepalen of u SAP Content Server-componenten wilt implementeren op een speciale Azure VM of op dezelfde VM waar het SAP-systeem wordt uitgevoerd. 

![Dedicated Azure VM voor SAP Content Server](./media/dbms_maxdb_deployment_guide/800-azure-vm-sap-content-server.png)


#### <a name="sap-cache-server-location"></a>LOCATIE VAN DE SAP-cacheserver
De SAP Cache Server is een extra servercomponent om lokaal toegang te bieden tot (cache)documenten. De SAP-cacheserver slaat de documenten van een SAP-inhoudsserver in de cache. Dit is om het netwerkverkeer te optimaliseren als documenten meer dan één keer van verschillende locaties moeten worden opgehaald. De algemene regel is dat de SAP-cacheserver fysiek dicht bij de client moet staan die toegang heeft tot de SAP-cacheserver. 

Hier heb je twee opties:

1. **Client is een backend SAP-systeem** Als een backend SAP-systeem is geconfigureerd om toegang te krijgen tot SAP Content Server, is dat SAP-systeem een client. Aangezien zowel het SAP-systeem als de SAP-inhoudsserver in dezelfde Azure-regio worden geïmplementeerd, zijn ze in hetzelfde Azure-datacenter fysiek dicht bij elkaar. Daarom is het niet nodig om een speciale SAP-cacheserver te hebben. SAP UI-clients (SAP GUI of webbrowser) hebben rechtstreeks toegang tot het SAP-systeem en het SAP-systeem haalt documenten op van de SAP-inhoudsserver.
2. **Client is een on-premises webbrowser** De SAP Content Server kan worden geconfigureerd om rechtstreeks toegankelijk te zijn via de webbrowser. In dit geval is een on-premises webbrowser een client van de SAP Content Server. On-premises datacenter en Azure datacenter worden geplaatst op verschillende fysieke locaties (idealiter dicht bij elkaar). Uw on-premises datacenter is verbonden met Azure via Azure Site-to-Site VPN of ExpressRoute. Hoewel beide opties een veilige VPN-netwerkverbinding met Azure bieden, biedt de netwerkverbinding van site-to-site geen netwerkbandbreedte en latentieSLA tussen het on-premises datacenter en het Azure-datacenter. Als u de toegang tot documenten wilt versnellen, u een van de volgende handelingen doen:
   1. SAP Cache Server on-premises installeren, in de buurt van de on-premises webbrowser (optie in figuur hieronder)
   2. Configureer Azure ExpressRoute, dat een speciale netwerkverbinding met hoge snelheid en lage latentie biedt tussen on-premises datacenter en Azure-datacenter.

![Optie om SAP Cache Server on-premises te installeren](./media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png)
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Back-up / herstel
Als u de SAP Content Server configureert om bestanden op te slaan in de SAP MaxDB-database, worden de back-up-/herstelprocedure en prestatieoverwegingen al beschreven in SAP MaxDB-hoofdstukken van dit document. 

Als u de SAP-inhoudsserver configureert om bestanden in het bestandssysteem op te slaan, is een optie het handmatig maken/herstellen van de hele bestandsstructuur waar de documenten zich bevinden. Net als bij SAP MaxDB back-up / herstel, is het raadzaam om een speciale schijf volume voor back-up doeleinden. 

#### <a name="other"></a>Overige
Andere SAP Content Server-specifieke instellingen zijn transparant voor Azure VM's en worden beschreven in verschillende documenten en SAP Notes:

* <https://service.sap.com/contentserver> 
* SAP Note [1619726]  
