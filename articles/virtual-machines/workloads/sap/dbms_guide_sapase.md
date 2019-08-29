---
title: Implementatie van SAP ASE Azure Virtual Machines DBMS voor SAP-workload | Microsoft Docs
description: DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor SAP ASE
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
ms.date: 07/1/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 51e97089b1de88ccf9f45b1a0f429abc0cfac9f3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101329"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor SAP ASE

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]: https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]: https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]: https://launchpad.support.sap.com/#/notes/1558958
[1585981]: https://launchpad.support.sap.com/#/notes/1585981
[1588316]: https://launchpad.support.sap.com/#/notes/1588316
[1590719]: https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]: https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]: https://launchpad.support.sap.com/#/notes/1619967
[1750510]: https://launchpad.support.sap.com/#/notes/1750510
[1752266]: https://launchpad.support.sap.com/#/notes/1752266
[1757924]: https://launchpad.support.sap.com/#/notes/1757924
[1757928]: https://launchpad.support.sap.com/#/notes/1757928
[1758182]: https://launchpad.support.sap.com/#/notes/1758182
[1758496]: https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]: https://launchpad.support.sap.com/#/notes/1814258
[1882376]: https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]: https://launchpad.support.sap.com/#/notes/1922555
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]: https://launchpad.support.sap.com/#/notes/1941500
[1956005]: https://launchpad.support.sap.com/#/notes/1956005
[1973241]: https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]: https://launchpad.support.sap.com/#/notes/2121797
[2134316]: https://launchpad.support.sap.com/#/notes/2134316
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
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

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
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
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
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
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



In dit document worden verschillende gebieden besproken waarmee u rekening moet houden wanneer u SAP ASE implementeert in azure IaaS. Als een voor waarde voor dit document, moet u de document [overwegingen voor azure virtual machines DBMS-implementatie van de SAP-werk belasting](dbms_guide_general.md) en andere hand leidingen in de [SAP-werk belasting op de Azure-documentatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)lezen. 

## <a name="specifics-to-sap-ase-on-windows"></a>Details van SAP-ASE in Windows
Vanaf Microsoft Azure kunt u uw bestaande SAP ASE-toepassingen migreren naar Azure Virtual Machines. Met SAP ASE in een virtuele machine van Azure kunt u de total cost of ownership van de implementatie, het beheer en het onderhoud van toepassingen op bedrijfs niveau verminderen door deze toepassingen eenvoudig te migreren naar Microsoft Azure. Met SAP ASE in een virtuele machine van Azure kunnen beheerders en ontwikkel aars nog steeds gebruikmaken van dezelfde hulpprogram ma's voor ontwikkeling en beheer die on-premises beschikbaar zijn.

De Sla's voor Azure Virtual Machines vindt u hier:<https://azure.microsoft.com/support/legal/sla/virtual-machines>

Microsoft Azure biedt talrijke verschillende typen virtuele machines waarmee u kleinste SAP-systemen en-landschappen kunt uitvoeren tot grote SAP-systemen en landschappen met duizenden gebruikers. SAP-beschik bare SAP'S-nummers van de verschillende SAP gecertificeerde VM Sku's vindt u in SAP-notitie [1928533].

Instructies en aanbevelingen met betrekking tot het gebruik van Azure Storage, implementatie van SAP-Vm's of SAP-bewaking die zijn aangebracht in [overwegingen voor Azure virtual machines DBMS-implementatie voor SAP-werk belasting](dbms_guide_general.md) is ook van toepassing op implementaties van SAP ASE.

### <a name="sap-ase-version-support"></a>Ondersteuning voor SAP ASE-versie
SAP ondersteunt momenteel SAP ASE versie 16,0 voor gebruik met SAP Business Suite-producten. Alle updates voor SAP ASE server-of JDBC-en ODBC-stuur Programma's die worden gebruikt met SAP Business Suite-producten, worden uitsluitend via de SAP <https://support.sap.com/swdc>-Service Marketplace op: beschreven.

Down load geen updates voor de SAP ASE-server of voor de JDBC-en ODBC-stuur Programma's rechtstreeks van Sybase websites. Voor gedetailleerde informatie over patches die worden ondersteund voor gebruik met SAP-producten on-premises en in azure Virtual Machines raadpleegt u de volgende SAP-opmerkingen:

* [1590719]
* [1973241]

Algemene informatie over het uitvoeren van SAP Business Suite op SAP ASE vindt u in het [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>SAP ASE-configuratie richtlijnen voor SAP ASE-installaties op het gebied van Azure-Vm's
#### <a name="structure-of-the-sap-ase-deployment"></a>Structuur van de SAP ASE-implementatie
SAP ASE uitvoer bare bestanden moeten worden gevonden of geïnstalleerd in het systeem station van de besturingssysteem schijf van de VM (\)station c:. Normaal gesp roken hebben de meeste data bases van het SAP ASE-systeem en hulpprogram ma's geen hoge werk belasting. Daarom kunnen de systeem-en hulpprogram ma's (Master, model, saptools, sybmgmtdb, sybsystemdb) op de C:\ blijven stationsletter. 

Een uitzonde ring kan de tijdelijke data base zijn. in het geval van sommige SAP ERP en alle BW-workloads is mogelijk een hoger gegevens volume of een I/O-bewerkingen vereist. Volumes of IOPS die niet kunnen worden weer gegeven door de besturingssysteem schijf van de VM (\)station C:.

Afhankelijk van de versie van SAPInst/SWPM die wordt gebruikt om te installeren, kan de configuratie van het SAP ASE-exemplaar er als volgt uitzien:

* Eén SAP ASE TempDB, die wordt gemaakt bij het installeren van SAP ASE
* Een SAP ASE TempDB gemaakt door SAP ASE te installeren en een extra saptempdb gemaakt door de SAP-installatie routine
* Een SAP ASE TempDB gemaakt door het installeren van SAP ASE en een extra TempDB die hand matig is gemaakt (bijvoorbeeld SAP Note [1752266]) om te voldoen aan de vereisten van de ERP/BW-specifieke tempdb

Vanwege de prestaties van specifieke ERP of alle BW-workloads, kan het zinvol zijn om de tempdb-apparaten van de verder gemaakte TempDB op te slaan op een andere schijf dan C:\. Als er geen extra TempDB bestaat, is het raadzaam om er een te maken (SAP-notitie [1752266]).

Voor dergelijke systemen moeten de volgende stappen worden uitgevoerd voor de verder gemaakte TempDB:

* Het eerste TempDB-apparaat verplaatsen naar het eerste apparaat van de SAP-data base
* TempDB-apparaten toevoegen aan elk van de Vhd's die een apparaat van de SAP-data base bevatten

Met deze configuratie kan TempDB meer ruimte gebruiken dan het systeem station kan bieden. Als referentie één kan de omvang van het TempDB-apparaat controleren op bestaande systemen, die on-premises worden uitgevoerd. Of een dergelijke configuratie maakt IOPS-nummers mogelijk voor TempDB, wat niet kan worden meegeleverd met het systeem station. Systemen die on-premises worden uitgevoerd, kunnen worden gebruikt om de I/O-werk belasting te controleren op Tempdb.

Geen SAP ASE-apparaten op de D:\ plaatsen station van de virtuele machine. Voor SAP-ASE geldt dit advies ook voor TempDB, zelfs als de objecten die in de tempdb worden bewaard, alleen tijdelijk zijn.

Voor implementaties van gegevens en transactie logboek bestanden zijn de instructies en suggesties die zijn aangebracht in [overwegingen voor Azure virtual machines DBMS-implementatie voor SAP-workloads](dbms_guide_general.md). In het geval van op Windows gebaseerde implementaties wordt het gebruik van Windows-opslag ruimten aanbevolen om stripesets te maken met voldoende IOPS, door Voer en volume.  

#### <a name="impact-of-database-compression"></a>Impact van database compressie
Bij configuraties waarbij de I/O-band breedte een beperkende factor kan worden, kan elke meting, die het aantal IOPS reduceert, de werk belasting die in een IaaS-scenario zoals Azure kan worden uitgevoerd, worden uitgerekt. Daarom is het raadzaam om ervoor te zorgen dat SAP ASE-compressie wordt gebruikt voordat u een bestaande SAP-data base uploadt naar Azure.

De aanbeveling om compressie toe te passen voordat uploadt naar Azure, heeft verschillende oorzaken:

* De hoeveelheid gegevens die naar Azure moet worden geüpload, is lager
* De duur van het uitvoeren van de compressie is korter, ervan uitgaande dat een grotere hardware kan gebruiken met meer Cpu's of een hogere I/O-band breedte of minder I/o-latentie on-premises.
* Kleinere database grootten kunnen leiden tot minder kosten voor schijf toewijzing

Gegevens-en LOB-compressie werken in een virtuele machine die wordt gehost in azure Virtual Machines als deze on-premises wordt uitgevoerd. Voor meer informatie over het controleren of compressie al in gebruik is in een bestaande SAP ASE-data base, raadpleegt u SAP-opmerking [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Data base-exemplaren bewaken met behulp van DBACockpit
Voor SAP-systemen, die gebruikmaken van SAP ASE als database platform, is de DBACockpit toegankelijk als Inge sloten browser vensters in trans actie DBACockpit of als Webdynpro. De volledige functionaliteit voor het bewaken en beheren van de data base is echter alleen beschikbaar in de Webdynpro-implementatie van de DBACockpit.

Net als bij on-premises systemen zijn er verschillende stappen vereist om alle SAP NetWeaver-functionaliteit in te scha kelen die wordt gebruikt door de Webdynpro-implementatie van de DBACockpit. Volg SAP-opmerking [1245200] om het gebruik van webdynpros in te scha kelen en de vereiste items te genereren. Wanneer u de instructies in de bovenstaande opmerkingen volgt, moet u ook de Internet Communication Manager (ICM) configureren, samen met de poorten die moeten worden gebruikt voor http-en HTTPS-verbindingen. De standaard instelling voor http ziet er als volgt uit:

> ICM/server_port_0 = PROCTIMEOUT = HTTP, poort = 8000, = 600, TIMEOUT = 600
> 
> ICM/server_port_1 = door Voer = HTTPS, poort = 443 $ $, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> 

en de koppelingen die zijn gegenereerd in trans actie DBACockpit zien er ongeveer als volgt uit:

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Afhankelijk van de manier waarop de virtuele machine van Azure die als host fungeert voor het SAP-systeem is verbonden met uw AD en DNS, moet u ervoor zorgen dat ICM gebruikmaakt van een volledig gekwalificeerde hostnaam die kan worden opgelost op de computer waar u de DBACockpit van probeert te openen. Zie SAP Note [773830] om te begrijpen hoe ICM de volledige gekwalificeerde hostnaam bepaalt op basis van de profiel parameters en stel para meter ICM/host_name_full expliciet in als dat nodig is.

Als u de virtuele machine hebt geïmplementeerd in een alleen-Cloud scenario zonder cross-premises-connectiviteit tussen on-premises en Azure, moet u een openbaar IP-adres en een domainlabel definiëren. De indeling van de open bare DNS-naam van de virtuele machine ziet er als volgt uit:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Meer informatie over de DNS-naam kunt u [hier][virtual-machines-azurerm-versus-azuresm]vinden.

De SAP-profiel parameter ICM/host_name_full instellen op de DNS-naam van de virtuele machine van Azure de koppeling kan er ongeveer als volgt uitzien:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

In dat geval moet u het volgende doen:

* Regels voor binnenkomend verkeer toevoegen aan de netwerk beveiligings groep in de Azure Portal voor de TCP/IP-poorten die worden gebruikt voor communicatie met ICM
* Voeg regels voor binnenkomend verkeer toe aan de Windows Firewall configuratie voor de TCP/IP-poorten die worden gebruikt voor communicatie met de ICM

Voor een geautomatiseerde import van alle beschik bare correcties is het raadzaam om regel matig de correctie verzameling SAP-opmerking toe te passen die van toepassing is op uw SAP-versie:

* [1558958]
* [1619967]
* [1882376]

Meer informatie over DBA cockpit voor SAP ASE vindt u in de volgende SAP-opmerkingen:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Overwegingen voor back-up/herstel voor SAP ASE
Wanneer u SAP ASE implementeert in azure, moet uw back-upmethodologie worden gecontroleerd. Zelfs voor niet-productie systemen moet u regel matig een back-up van de SAP-data base (s) maken. Omdat Azure Storage drie installatie kopieën houdt, is een back-up mogelijk minder belang rijk voor het compenseren van een storing in de opslag. De belangrijkste reden voor het onderhouden van een juiste back-up-en herstel planning is meer dat u kunt compenseren voor logische/hand matige fouten door herstel mogelijkheden voor het punt op te geven. Het doel is dus back-ups te gebruiken om de data base terug te zetten naar een bepaald tijdstip of om de back-ups in azure te gebruiken om een ander systeem te seeden door de bestaande Data Base te kopiëren. 

Het maken van een back-up en het herstellen van een data base in azure werkt op dezelfde manier als op locatie. Zie SAP-opmerkingen:

* [1588316]
* [1585981]

voor meer informatie over het maken van dump configuraties en het plannen van back-ups. Afhankelijk van de strategie en de behoeften, kunt u de data base-en logboek dumps configureren op schijf op een van de bestaande schijven of een extra schijf toevoegen voor de back-up. Om het risico van gegevens verlies in het geval van een fout te verminderen, is het raadzaam om een schijf te gebruiken waarop geen database bestanden zijn opgeslagen.

Naast gegevens-en LOB-compressie biedt SAP ASE ook back-upcompressie. Als u minder ruimte wilt gebruiken voor de data base en de logboek dump, wordt u aangeraden back-upcompressie. Zie SAP Note [1588316]voor meer informatie. Het comprimeren van de back-up is ook cruciaal om de hoeveelheid gegevens die moet worden overgedragen te verminderen als u van plan bent om back-ups of Vhd's met back-updumps van de virtuele machine van Azure naar on-premises te downloaden.

Gebruik geen station D:\ Als de data base-of logboek dump bestemming.

#### <a name="performance-considerations-for-backupsrestores"></a>Prestatie overwegingen voor back-ups/herstel bewerkingen
Net als bij bare-metal implementaties is de prestaties van back-up/herstel afhankelijk van het aantal volumes dat parallel kan worden gelezen en de door Voer van die volumes. Houd er wel van uit dat de compressie van back-ups CPU-Resources verbruikt. Dit CPU-verbruik van back-upcompressie kan een belang rijke rol spelen op Vm's met een klein aantal CPU-threads. Daarom kunt u aannemen:

* Hoe minder schijven er worden gebruikt voor het opslaan van de database apparaten, hoe kleiner de totale door Voer bij het lezen
* Het kleinere aantal CPU-threads in de virtuele machine, des te groter is de impact van back-upcompressie
* Hoe minder doelen (Stripe-Directory's, schijven) om de back-up naar te schrijven, hoe lager de door Voer

Als u het aantal doelen wilt verg Roten om naar te schrijven, zijn er twee opties, die kunnen worden gebruikt/gecombineerd, afhankelijk van uw behoeften:

* Het back-updoel volume over meerdere gekoppelde schijven verwijderen om de IOPS-door Voer voor dat striped volume te verbeteren
* Er wordt een dump configuratie op SAP ASE-niveau gemaakt, die gebruikmaakt van meer dan één doel directory om de dump te schrijven naar

Het verwijderen van een schijf volume over meerdere gekoppelde schijven is besproken in [overwegingen bij de implementatie van Azure virtual machines DBMS voor SAP-workloads](dbms_guide_general.md). Raadpleeg de documentatie over de opgeslagen procedure sp_config_dump, die wordt gebruikt om de dump configuratie te maken op de [Sybase InfoCenter](http://infocenter.sybase.com/help/index.jsp)voor meer informatie over het gebruik van meerdere mappen in de SAP ASE dump configuratie.

### <a name="disaster-recovery-with-azure-vms"></a>Herstel na nood geval met Azure-Vm's
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Gegevens replicatie met SAP Sybase-replicatie server
Met de SAP Sybase Replication server (SRS) biedt SAP ASE een warme stand-by-oplossing om database transacties asynchroon over te dragen naar een locatie op afstand. 

De installatie en werking van SRS werkt goed in een VM die wordt gehost in azure virtual machine-Services, zoals on-premises.

Voor SAP ASE HADR is geen interne Load Balancer van Azure vereist en er zijn geen afhankelijkheden op besturingssysteem niveau clustering. Het werkt op Azure Windows-en Linux-Vm's. Lees de [hand leiding voor SAP ASE HADR-gebruikers](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html)voor meer informatie over SAP ASE HADR.

## <a name="specifics-to-sap-ase-on-linux"></a>Details van SAP ASE in Linux
Vanaf Microsoft Azure kunt u eenvoudig uw bestaande SAP ASE-toepassingen migreren naar Azure Virtual Machines. Met SAP ASE in een virtuele machine kunt u de total cost of ownership van de implementatie, het beheer en het onderhoud van toepassingen op bedrijfs niveau verminderen door deze toepassingen eenvoudig te migreren naar Microsoft Azure. Met SAP ASE in een virtuele machine van Azure kunnen beheerders en ontwikkel aars nog steeds gebruikmaken van dezelfde hulpprogram ma's voor ontwikkeling en beheer die on-premises beschikbaar zijn.

Voor het implementeren van Azure-Vm's is het belang rijk dat u de officiële Sla's kent, die u hier kunt vinden:<https://azure.microsoft.com/support/legal/sla>

SAP-grootte gegevens en een lijst met SAP-gecertificeerde VM-Sku's vindt u in SAP Note [1928533]. Meer sap-formaat documenten voor virtuele machines van Azure vindt u <https://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> hier en hier<https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Instructies en aanbevelingen met betrekking tot het gebruik van Azure Storage, de implementatie van SAP-Vm's of SAP-bewaking zijn van toepassing op implementaties van SAP ASE in combi natie met SAP-toepassingen, zoals vermeld in de eerste vier hoofd stukken van dit document.

De volgende twee SAP-opmerkingen bevatten algemene informatie over ASE in Linux en ASE in de Cloud:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>Ondersteuning voor SAP ASE-versie
SAP ondersteunt momenteel SAP ASE versie 16,0 voor gebruik met SAP Business Suite-producten. Alle updates voor SAP ASE server-of JDBC-en ODBC-stuur Programma's die worden gebruikt met SAP Business Suite-producten, worden uitsluitend via de SAP <https://support.sap.com/swdc>-Service Marketplace op: beschreven.

Als voor on-premises installaties, moet u geen updates voor de SAP ASE-server of voor de JDBC-en ODBC-stuur Programma's rechtstreeks van Sybase websites downloaden. Voor gedetailleerde informatie over patches die worden ondersteund voor gebruik met SAP Business Suite-producten on-premises en in azure Virtual Machines raadpleegt u de volgende SAP-opmerkingen:

* [1590719]
* [1973241]

Algemene informatie over het uitvoeren van SAP Business Suite op SAP ASE vindt u in het [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>SAP ASE-configuratie richtlijnen voor SAP ASE-installaties op het gebied van Azure-Vm's
#### <a name="structure-of-the-sap-ase-deployment"></a>Structuur van de SAP ASE-implementatie
SAP ASE uitvoer bare bestanden moeten worden gevonden of geïnstalleerd in het hoofd bestandssysteem van de virtuele machine (/Sybase). Normaal gesp roken hebben de meeste data bases van het SAP ASE-systeem en hulpprogram ma's geen hoge werk belasting. Daarom kunnen de systeem-en hulpprogram ma's (Master, model, saptools, sybmgmtdb, sybsystemdb) worden opgeslagen op het hoofd bestands systeem. 

Een uitzonde ring kan de tijdelijke data base zijn. in het geval van sommige SAP ERP en alle BW-workloads is mogelijk een hoger gegevens volume of een I/O-bewerkingen vereist. Volumes of IOPS die niet kunnen worden weer gegeven door de besturingssysteem schijf van de VM 

Afhankelijk van de versie van SAPInst/SWPM die is gebruikt om het systeem te installeren, kan de data base het volgende bevatten:

* Er is één SAP ASE TempDB gemaakt tijdens de installatie van SAP ASE
* Een SAP ASE TempDB gemaakt door SAP ASE te installeren en een extra saptempdb gemaakt door de SAP-installatie routine
* Een SAP ASE TempDB gemaakt door het installeren van SAP ASE en een extra TempDB die hand matig is gemaakt (bijvoorbeeld SAP Note [1752266]) om te voldoen aan de vereisten van de ERP/BW-specifieke tempdb

Vanwege de prestaties van specifieke ERP-of alle BW-workloads, kan het zinvol zijn om de tempdb-apparaten van de verder gemaakte TempDB (door SWPM of hand matig) op te slaan op een afzonderlijk bestands systeem, dat kan worden vertegenwoordigd door één Azure-gegevens schijf of een Linux RAID-spanning m ultiple Azure-gegevens schijven. Als er geen extra TempDB bestaat, is het raadzaam om er een te maken (SAP-notitie [1752266]).

Voor dergelijke systemen moeten de volgende stappen worden uitgevoerd voor de verder gemaakte TempDB:

* De eerste TempDB-directory verplaatsen naar het eerste bestands systeem van de SAP-data base
* TempDB-mappen toevoegen aan alle schijven met een bestands systeem van de SAP-data base

Met deze configuratie kan TempDB meer ruimte gebruiken dan het systeem station kan bieden. Als referentie één kan de omvang van het TempDB-apparaat controleren op bestaande systemen, die on-premises worden uitgevoerd. Of een dergelijke configuratie maakt IOPS-nummers mogelijk voor TempDB, wat niet kan worden meegeleverd met het systeem station. Systemen die on-premises worden uitgevoerd, kunnen worden gebruikt om de I/O-werk belasting te controleren op Tempdb.

Plaats nooit SAP ASE-directory's op/mnt of/mnt/resource van de virtuele machine. Voor SAP-ASE geldt dit advies ook voor TempDB, zelfs als de objecten die in de tempdb worden bewaard, alleen tijdelijk zijn. Omdat/mnt of/mnt/resource een standaard tijdelijke Azure-VM-ruimte is, die niet permanent is. Meer informatie over de tijdelijke Azure VM-ruimte vindt u in [dit artikel][virtual-machines-linux-how-to-attach-disk]

Voor implementaties van gegevens en transactie logboek bestanden zijn de instructies en suggesties die zijn aangebracht in [overwegingen voor Azure virtual machines DBMS-implementatie voor SAP-workloads](dbms_guide_general.md). In het geval van op Linux gebaseerde implementaties wordt het gebruik van LVM of MDADM aanbevolen voor het maken van stripesets met voldoende IOPS, door Voer en volume. 

#### <a name="impact-of-database-compression"></a>Impact van database compressie
Bij configuraties waarbij de I/O-band breedte een beperkende factor kan worden, kan elke meting, die het aantal IOPS reduceert, de werk belasting die in een IaaS-scenario zoals Azure kan worden uitgevoerd, worden uitgerekt. Daarom is het raadzaam om ervoor te zorgen dat SAP ASE-compressie wordt gebruikt voordat u een bestaande SAP-data base uploadt naar Azure.

De aanbeveling om compressie toe te passen voordat uploadt naar Azure, heeft verschillende oorzaken:

* De hoeveelheid gegevens die naar Azure moet worden geüpload, is lager
* De duur van het uitvoeren van de compressie is korter, ervan uitgaande dat een grotere hardware kan gebruiken met meer Cpu's of een hogere I/O-band breedte of minder I/o-latentie on-premises.
* Kleinere database grootten kunnen leiden tot minder kosten voor schijf toewijzing

Gegevens-en LOB-compressie werken in een virtuele machine die wordt gehost in azure Virtual Machines als deze on-premises wordt uitgevoerd. Voor meer informatie over het controleren of compressie al in gebruik is in een bestaande SAP ASE-data base, raadpleegt u SAP-opmerking [1750510]. Zie SAP Note [2121797]voor meer informatie over database compressie.

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Data base-exemplaren bewaken met behulp van DBACockpit
Voor SAP-systemen, die gebruikmaken van SAP ASE als database platform, is de DBACockpit toegankelijk als Inge sloten browser vensters in trans actie DBACockpit of als Webdynpro. De volledige functionaliteit voor het bewaken en beheren van de data base is echter alleen beschikbaar in de Webdynpro-implementatie van de DBACockpit.

Net als bij on-premises systemen zijn er verschillende stappen vereist om alle SAP NetWeaver-functionaliteit in te scha kelen die wordt gebruikt door de Webdynpro-implementatie van de DBACockpit. Volg SAP-opmerking [1245200] om het gebruik van webdynpros in te scha kelen en de vereiste items te genereren. Wanneer u de instructies in de bovenstaande opmerkingen volgt, moet u ook de Internet Communication Manager (ICM) configureren, samen met de poorten die moeten worden gebruikt voor http-en HTTPS-verbindingen. De standaard instelling voor http ziet er als volgt uit:

> ICM/server_port_0 = PROCTIMEOUT = HTTP, poort = 8000, = 600, TIMEOUT = 600
> 
> ICM/server_port_1 = door Voer = HTTPS, poort = 443 $ $, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> 

en de koppelingen die zijn gegenereerd in trans actie DBACockpit zien er ongeveer als volgt uit:

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Afhankelijk van de manier waarop de virtuele machine van Azure die als host fungeert voor het SAP-systeem is verbonden met uw AD en DNS, moet u ervoor zorgen dat ICM gebruikmaakt van een volledig gekwalificeerde hostnaam die kan worden opgelost op de computer waar u de DBACockpit van probeert te openen. Zie SAP Note [773830] om te begrijpen hoe ICM de volledig gekwalificeerde hostnaam bepaalt, afhankelijk van de profiel parameters en stel para meter ICM/host_name_full expliciet in als dat nodig is.

Als u de virtuele machine hebt geïmplementeerd in een alleen-Cloud scenario zonder cross-premises-connectiviteit tussen on-premises en Azure, moet u een openbaar IP-adres en een domainlabel definiëren. De indeling van de open bare DNS-naam van de virtuele machine ziet er als volgt uit:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Meer informatie over de DNS-naam kunt u [hier][virtual-machines-azurerm-versus-azuresm]vinden.

De SAP-profiel parameter ICM/host_name_full instellen op de DNS-naam van de virtuele machine van Azure de koppeling kan er ongeveer als volgt uitzien:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

In dat geval moet u het volgende doen:

* Regels voor binnenkomend verkeer toevoegen aan de netwerk beveiligings groep in de Azure Portal voor de TCP/IP-poorten die worden gebruikt voor communicatie met ICM
* Voeg regels voor binnenkomend verkeer toe aan de Windows Firewall configuratie voor de TCP/IP-poorten die worden gebruikt voor communicatie met de ICM

Voor een geautomatiseerde import van alle beschik bare correcties is het raadzaam om regel matig de correctie verzameling SAP-opmerking toe te passen die van toepassing is op uw SAP-versie:

* [1558958]
* [1619967]
* [1882376]

Meer informatie over DBA cockpit voor SAP ASE vindt u in de volgende SAP-opmerkingen:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Overwegingen voor back-up/herstel voor SAP ASE
Wanneer u SAP ASE implementeert in azure, moet uw back-upmethodologie worden gecontroleerd. Zelfs voor niet-productie systemen moet u regel matig een back-up van de SAP-data base (s) maken. Omdat Azure Storage drie installatie kopieën houdt, is een back-up mogelijk minder belang rijk voor het compenseren van een storing in de opslag. De belangrijkste reden voor het onderhouden van een juiste back-up-en herstel planning is meer dat u kunt compenseren voor logische/hand matige fouten door herstel mogelijkheden voor het punt op te geven. Het doel is dus back-ups te gebruiken om de data base terug te zetten naar een bepaald tijdstip of om de back-ups in azure te gebruiken om een ander systeem te seeden door de bestaande Data Base te kopiëren. 

Het maken van een back-up en het herstellen van een data base in azure werkt op dezelfde manier als op locatie. Zie SAP-opmerkingen:

* [1588316]
* [1585981]

voor meer informatie over het maken van dump configuraties en het plannen van back-ups. Afhankelijk van uw strategie en behoeften kunt u data base-en logboek dumps configureren op schijf op een van de bestaande schijven of een extra schijf toevoegen voor de back-up. Om het risico van gegevens verlies in het geval van een fout te verminderen, is het raadzaam om een schijf te gebruiken waarop geen data base-map of-bestand is opgeslagen.

Naast gegevens-en LOB-compressie biedt SAP ASE ook back-upcompressie. Als u minder ruimte wilt gebruiken voor de data base en de logboek dump, wordt u aangeraden back-upcompressie. Zie SAP Note [1588316]voor meer informatie. Het comprimeren van de back-up is ook cruciaal om de hoeveelheid gegevens die moet worden overgedragen te verminderen als u van plan bent om back-ups of Vhd's met back-updumps van de virtuele machine van Azure naar on-premises te downloaden.

Gebruik niet de tijdelijke ruimte/mnt of/mnt/resource van de Azure VM als de data base-of logboek dump bestemming.

#### <a name="performance-considerations-for-backupsrestores"></a>Prestatie overwegingen voor back-ups/herstel bewerkingen
Net als bij bare-metal implementaties is de prestaties van back-up/herstel afhankelijk van het aantal volumes dat parallel kan worden gelezen en de door Voer van die volumes. Houd er wel van uit dat de compressie van back-ups CPU-Resources verbruikt. Dit CPU-verbruik van back-upcompressie kan een belang rijke rol spelen op Vm's met een klein aantal CPU-threads.  Daarom kunt u aannemen:

* Hoe minder schijven er worden gebruikt voor het opslaan van de database apparaten, hoe kleiner de totale door Voer bij het lezen
* Het kleinere aantal CPU-threads in de virtuele machine, des te groter is de impact van back-upcompressie
* De minder doelen (Linux software RAID, schijven) voor het schrijven van de back-up naar, de lagere door Voer

Als u het aantal doelen wilt verg Roten om naar te schrijven, zijn er twee opties, die kunnen worden gebruikt/gecombineerd, afhankelijk van uw behoeften:

* Het back-updoel volume over meerdere gekoppelde schijven verwijderen om de IOPS-door Voer voor dat striped volume te verbeteren
* Er wordt een dump configuratie op SAP ASE-niveau gemaakt, die gebruikmaakt van meer dan één doel directory om de dump te schrijven naar

Het verwijderen van een schijf volume over meerdere gekoppelde schijven is besproken in [overwegingen bij de implementatie van Azure virtual machines DBMS voor SAP-workloads](dbms_guide_general.md). Raadpleeg de documentatie over de opgeslagen procedure sp_config_dump, die wordt gebruikt om de dump configuratie te maken op de [Sybase InfoCenter](http://infocenter.sybase.com/help/index.jsp)voor meer informatie over het gebruik van meerdere mappen in de SAP ASE dump configuratie.

### <a name="disaster-recovery-with-azure-vms"></a>Herstel na nood geval met Azure-Vm's
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Gegevens replicatie met SAP Sybase-replicatie server
Met de SAP Sybase Replication server (SRS) biedt SAP ASE een warme stand-by-oplossing om database transacties asynchroon over te dragen naar een locatie op afstand. 

De installatie en werking van SRS werkt goed in een VM die wordt gehost in azure virtual machine-Services, zoals on-premises.

ASE HADR via SAP-replicatie server wordt ondersteund. Het is raadzaam om SAP ASE 16,03 te gebruiken om een dergelijke configuratie te proberen. Meer gedetailleerde instructies voor het installeren van dergelijke configuraties vindt u in dit [blog](https://blogs.msdn.microsoft.com/saponsqlserver/2018/06/18/installation-procedure-for-sybase-16-3-patch-level-3-always-on-dr-on-suse-12-3-recent-customer-proof-of-concept/).
