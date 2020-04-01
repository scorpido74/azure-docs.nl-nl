---
title: Azure Virtual Machines-implementatie voor SAP NetWeaver | Microsoft Documenten
description: Meer informatie over het implementeren van SAP-software op virtuele Linux-machines in Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: sedusch
ms.openlocfilehash: 7fb87380047d046a580d1ad62b1d7107a94bb297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239890"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Azure Virtual Machines-implementatie voor SAP NetWeaver

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
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../../../cli-install-nodejs.md
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Azure Virtual Machines DBMS-implementatie voor SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Caching voor VM's en VHD's)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure-opslag)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Structuur van een RDBMS-implementatie)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Hoge beschikbaarheid en herstel na noodgevallen met Azure VM's)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 en hoger)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 en eerdere releases)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Een SQL Server-afbeelding gebruiken vanuit azure marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Algemene SQL Server voor SAP op Azure-overzicht)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Specifieke kenmerken van SQL Server RDBMS)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Opslagconfiguratie)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Back-up maken en herstellen)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Prestatieoverwegingen voor back-up en herstel)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Andere)
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

[deployment-guide]:deployment-guide.md (Azure Virtual Machines-implementatie voor SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP-resources)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Een VM implementeren met behulp van een aangepaste afbeelding)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scenario 1: Een VM implementeren vanuit de Azure Marketplace voor SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scenario 2: Een VM implementeren met een aangepaste afbeelding voor SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scenario 3: Een VM verplaatsen van on-premises met behulp van een niet-gegeneraliseerde Azure VHD met SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Implementatiescenario's van VM's voor SAP op Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Azure PowerShell-cmdlets implementeren)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (SAP-relevante PowerShell-cmdlets downloaden en importeren)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Een vm deelnemen aan een on-premises domein - alleen Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (De Azure VM-agent downloaden, installeren en inschakelen)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (De Azure-extensie voor SAP configureren)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Gereedheidscontrole voor Azure Extension voor SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Statuscontrole voor de Azure-extensie voor SAP-configuratie)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Azure-extensie voor SAP oplossen)

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (VM-extensie configureren)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (De proxy configureren)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Controles en probleemoplossing voor end-to-end gegevensverzameling voor SAP Host Agent)

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

[msdn-set-Azvmaemextension]:https://docs.microsoft.com/powershell/module/az.compute/set-azvmaemextension

[planning-guide]:planning-guide.md (Azure Virtual Machines planning en implementatie voor SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Middelen)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Hoge beschikbaarheid en noodherstel voor SAP NetWeaver dat wordt uitgevoerd op Azure Virtual Machines)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Hoge beschikbaarheid voor SAP Application Servers)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Autostart gebruiken voor SAP-exemplaren)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Alleen in de cloud - Implementaties van virtuele machines in Azure zonder afhankelijkte van het on-premises klantennetwerk)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Cross-premises - Implementatie van enkele of meerdere SAP VM's in Azure volledig geïntegreerd met het on-premises netwerk)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure-regio's)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Foutdomeinen)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Domeinen upgraden)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure-beschikbaarheidssets)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Microsoft Azure virtuele machines concept)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium-opslag)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Een VM verplaatsen van on-premises naar Azure met een niet-gegeneraliseerde schijf)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Een VM implementeren met een klantspecifieke afbeelding)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Voorbereiding voor het verplaatsen van een vm van on-premises naar Azure met een niet-gegeneraliseerde schijf)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Voorbereiding voor het implementeren van een VM met een klantspecifieke afbeelding voor SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (VM's voorbereiden met SAP voor Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Verschil tussen een Azure-schijf en een Azure-afbeelding)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Een VHD uploaden van on-premises naar Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Schijven kopiëren tussen Azure Storage-accounts)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (VM/VHD-structuur voor SAP-implementaties)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Automatisch vaststellen instellen voor aangesloten schijven)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Eén VM met SAP NetWeaver-demo/trainingsscenario)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Concepten van cloud-only implementatie van SAP-exemplaren)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure-bewakingsoplossing voor SAP)
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium-opslag)
[planning-guide-managed-disks]:planning-guide.md#c55b2c6e-3ca1-4476-be16-16c81927550f (Beheerde schijven)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure-netwerken)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Opslag: Microsoft Azure-opslag- en gegevensschijven)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (SAP-productbeschikbaarheidsmatrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-os-disk-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk-md%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image-md%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image-md%2Fazuredeploy.json
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
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Virtuele machines implementeren en beheren met Azure Resource Manager-sjablonen en de Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Virtuele machines beheren met Azure Resource Manager en PowerShell)
[virtual-machines-windows-agent-user-guide]:../../extensions/agent-windows.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-linux.md#command-line-options
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
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
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

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Azure Virtual Machines is de oplossing voor organisaties die reken- en opslagbronnen nodig hebben, in minimale tijd en zonder lange inkoopcycli. U Azure Virtual Machines gebruiken om klassieke toepassingen, zoals op SAP NetWeaver gebaseerde toepassingen, in Azure te implementeren. Verleng de betrouwbaarheid en beschikbaarheid van een toepassing zonder extra on-premises resources. Azure Virtual Machines ondersteunt cross-premises connectiviteit, zodat u Azure Virtual Machines integreren in de on-premises domeinen, private clouds en SAP-systeemlandschap van uw organisatie.

In dit artikel behandelen we de stappen om SAP-toepassingen op virtuele machines (VM's) in Azure te implementeren, inclusief alternatieve implementatieopties en probleemoplossing. Dit artikel bouwt voort op de informatie in de [planning en implementatie van Azure Virtual Machines voor SAP NetWeaver.][planning-guide] Het vormt ook een aanvulling op SAP-installatiedocumentatie en SAP Notes, de belangrijkste bronnen voor het installeren en implementeren van SAP-software.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Het instellen van een Azure virtuele machine voor SAP-software implementatie omvat meerdere stappen en resources. Controleer voordat u begint of u voldoet aan de vereisten voor het installeren van SAP-software op virtuele machines in Azure.

### <a name="local-computer"></a>Lokale computer

Als u Windows- of Linux-VM's wilt beheren, u een PowerShell-script en de Azure-portal gebruiken. Voor beide hulpprogramma's hebt u een lokale computer met Windows 7 of een latere versie van Windows nodig. Als u alleen Linux-VM's wilt beheren en u een Linux-computer voor deze taak wilt gebruiken, u Azure CLI gebruiken.

### <a name="internet-connection"></a>Internetverbinding

Als u de hulpprogramma's en scripts wilt downloaden en uitvoeren die nodig zijn voor de implementatie van SAP-software, moet u verbonden zijn met internet. De Azure VM waarop de Azure Extension voor SAP wordt uitgevoerd, heeft ook toegang tot internet nodig. Als de Azure VM deel uitmaakt van een Virtueel Azure-netwerk of on-premises domein, controleert u of de relevante proxy-instellingen zijn ingesteld, zoals beschreven in [De proxy configureren.][deployment-guide-configure-proxy]

### <a name="microsoft-azure-subscription"></a>Microsoft Azure-abonnement

U hebt een actief Azure-account nodig.

### <a name="topology-and-networking"></a>Topologie en netwerken

U moet de topologie en architectuur van de SAP-implementatie in Azure definiëren:

* Te gebruiken Azure-opslagaccounts
* Virtueel netwerk waar u het SAP-systeem wilt implementeren
* Resourcegroep waaraan u het SAP-systeem wilt implementeren
* Azure-regio waar u het SAP-systeem wilt implementeren
* SAP-configuratie (tweelaags of drielagen)
* VM-formaten en het aantal extra gegevensschijven dat aan de VM's moet worden gemonteerd
* SAP Correction and Transport System (CTS) configuratie

Azure-opslagaccounts maken en configureren (indien nodig) of virtuele Azure-netwerken voordat u met het implementatieproces van DE SAP-software begint. Zie Azure Virtual Machines planning en [implementatie voor SAP NetWeaver voor][planning-guide]informatie over het maken en configureren van deze resources.

### <a name="sap-sizing"></a>SAP-dimensionering

Ken de volgende informatie, voor SAP-formaat:

* Geprojecteerde SAP-workload, bijvoorbeeld met behulp van de SAP Quick Sizer-tool en het SAP-toepassingsprestatienummer (SAPS) van SAP Application Performance
* Vereiste CPU-bron- en geheugenverbruik van het SAP-systeem
* Vereiste input/output (I/O) bewerkingen per seconde
* Vereiste netwerkbandbreedte van uiteindelijke communicatie tussen VM's in Azure
* Vereiste netwerkbandbreedte tussen on-premises assets en het door Azure geïmplementeerde SAP-systeem

### <a name="resource-groups"></a>Resourcegroepen

In Azure Resource Manager u resourcegroepen gebruiken om alle toepassingsbronnen in uw Azure-abonnement te beheren. Zie voor meer informatie [Overzicht van Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Resources

### <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP-resources

Wanneer u uw SAP-softwareimplementatie instelt, hebt u de volgende SAP-bronnen nodig:

* SAP Note [1928533], die heeft:
  * Lijst met Azure VM-formaten die worden ondersteund voor de implementatie van SAP-software
  * Belangrijke capaciteitsinformatie voor Azure VM-formaten
  * Ondersteunde SAP-software en besturingssysteem- en databasecombinaties
  * Vereiste SAP-kernelversie voor Windows en Linux op Microsoft Azure

* SAP Note [2015553] bevat vereisten voor SAP-ondersteunde SAP-softwareimplementaties in Azure.
* SAP Note [2178632] heeft gedetailleerde informatie over alle monitoringstatistieken die voor SAP in Azure worden gerapporteerd.
* SAP Note [1409604] heeft de vereiste SAP Host Agent-versie voor Windows in Azure.
* SAP Note [2191498] heeft de vereiste SAP Host Agent-versie voor Linux in Azure.
* SAP Note [2243692] heeft informatie over SAP-licenties op Linux in Azure.
* SAP Note [1984787] heeft algemene informatie over SUSE Linux Enterprise Server 12.
* SAP Note [2002167] heeft algemene informatie over Red Hat Enterprise Linux 7.x.
* SAP Note [2069760] heeft algemene informatie over Oracle Linux 7.x.
* SAP Note [1999351] heeft aanvullende informatie over probleemoplossing voor de Azure Enhanced Monitoring Extension voor SAP.
* SAP Note [1597355] heeft algemene informatie over swap-ruimte voor Linux.
* [SAP op azure SCN-pagina](https://wiki.scn.sap.com/wiki/x/Pia7Gg) heeft nieuws en een verzameling nuttige bronnen.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) heeft alle vereiste SAP Notes voor Linux.
* SAP-specifieke PowerShell-cmdlets die deel uitmaken van [Azure PowerShell.][azure-ps]
* SAP-specifieke Azure CLI-opdrachten die deel uitmaken van [Azure CLI][azure-cli].

### <a name="windows-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Windows-bronnen

Deze Microsoft-artikelen hebben betrekking op SAP-implementaties in Azure:

* [Azure Virtual Machines planning en implementatie voor SAP NetWeaver][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP NetWeaver (dit artikel)][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP NetWeaver][dbms-guide]

## <a name="deployment-scenarios-for-sap-software-on-azure-vms"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Implementatiescenario's voor SAP-software op Azure VM's

U hebt meerdere opties voor het implementeren van VM's en gekoppelde schijven in Azure. Het is belangrijk om de verschillen tussen implementatieopties te begrijpen, omdat u verschillende stappen nemen om uw VM's voor te bereiden op implementatie op basis van het implementatietype dat u kiest.

### <a name="scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Scenario 1: Een VM implementeren vanuit de Azure Marketplace voor SAP

U een afbeelding van Microsoft of een derde partij in de Azure Marketplace gebruiken om uw vm te implementeren. De Marketplace biedt een aantal standaard OS-afbeeldingen van Windows Server en verschillende Linux-distributies. U ook een afbeelding implementeren die dissku's (databasebeheersystemen) bevat, bijvoorbeeld Microsoft SQL Server. Zie [Azure Virtual Machines DBMS-implementatie voor SAP NetWeaver voor][dbms-guide]meer informatie over het gebruik van afbeeldingen met DBMS-sKU's.

In het volgende stroomdiagram ziet u de SAP-specifieke reeks stappen voor het implementeren van een VM vanuit de Azure Marketplace:

![Stroomdiagram van VM-implementatie voor SAP-systemen met behulp van een VM-afbeelding van de Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Een virtuele machine maken met behulp van de Azure-portal

De eenvoudigste manier om een nieuwe virtuele machine te maken met een afbeelding van de Azure Marketplace is door de Azure-portal te gebruiken.

1.  Ga naar <https://portal.azure.com/#create/hub>.  Of selecteer **+ Nieuw**in het menu Azure portal .
1.  Selecteer **Berekenen**en selecteer vervolgens het type besturingssysteem dat u wilt implementeren. Bijvoorbeeld Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) of Oracle Linux 7.2. In de standaardlijstweergave worden niet alle ondersteunde besturingssystemen weergegeven. Selecteer **Alles bekijken** voor een volledige lijst. Zie SAP Note [1928533]voor meer informatie over ondersteunde besturingssystemen voor de implementatie van SAP-software.
1.  Bekijk op de volgende pagina de algemene voorwaarden.
1.  Selecteer **Resourcebeheer**in het vak **Een implementatiemodel** selecteren .
1.  Selecteer **Maken**.

De wizard begeleidt u bij het instellen van de vereiste parameters om de virtuele machine te maken, naast alle benodigde resources, zoals netwerkinterfaces en opslagaccounts. Enkele van deze parameters zijn:

1. **Basics**:
   * **Naam**: De naam van de resource (de naam van de virtuele machine).
   * **VM-schijftype:** selecteer het schijftype van de osschijf. Als u Premium Storage voor uw gegevensschijven wilt gebruiken, raden we u aan ook Premium Storage voor de OS-schijf te gebruiken.
   * **Gebruikersnaam en wachtwoord** of **SSH-openbare sleutel**: Voer de gebruikersnaam en het wachtwoord in van de gebruiker die tijdens de inrichting is gemaakt. Voor een Virtuele Linux-machine u de openbare Secure Shell -toets (SSH) invoeren die u gebruikt om u aan te melden bij de machine.
   * **Abonnement**: Selecteer het abonnement dat u wilt gebruiken om de nieuwe virtuele machine in te richten.
   * **Resourcegroep:** de naam van de resourcegroep voor de VM. U de naam van een nieuwe resourcegroep of de naam van een resourcegroep invoeren die al bestaat.
   * **Locatie**: Waar de nieuwe virtuele machine te implementeren. Als u de virtuele machine wilt verbinden met uw on-premises netwerk, moet u de locatie selecteren van het virtuele netwerk dat Azure verbindt met uw on-premises netwerk. Zie [Microsoft Azure-netwerken][planning-guide-microsoft-azure-networking] in [Azure Virtual Machines planning en implementatie voor SAP NetWeaver voor][planning-guide]meer informatie.
1. **Omvang**:

     Zie SAP Note [1928533]voor een lijst met ondersteunde VM-typen. Zorg ervoor dat u het juiste VM-type selecteert als u Azure Premium Storage wilt gebruiken. Niet alle VM-typen ondersteunen Premium Storage. Zie [Storage: Microsoft Azure Storage and data disks][planning-guide-storage-microsoft-azure-storage-and-data-disks] en Azure Premium [Storage][planning-guide-azure-premium-storage] in Azure Virtual Machines planning and implementation for [SAP NetWeaver for][planning-guide]more information.

1. **Instellingen**:
   * **Opslag**
     * **Schijftype:** selecteer het schijftype van de osschijf. Als u Premium Storage voor uw gegevensschijven wilt gebruiken, raden we u aan ook Premium Storage voor de OS-schijf te gebruiken.
     * **Beheerde schijven gebruiken:** Als u Beheerde schijven wilt gebruiken, selecteert u Ja. Zie hoofdstuk [Beheerde schijven][planning-guide-managed-disks] in de planningshandleiding voor meer informatie over beheerde schijven.
     * **Opslagaccount:** selecteer een bestaand opslagaccount of maak een nieuw account. Niet alle opslagtypen werken voor het uitvoeren van SAP-toepassingen. Zie [Opslagstructuur van een VM voor RDBMS-implementaties voor](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)meer informatie over opslagtypen.
   * **Netwerk**
     * **Virtueel netwerk** en **subnet:** Als u de virtuele machine wilt integreren met uw intranet, selecteert u het virtuele netwerk dat is verbonden met uw on-premises netwerk.
     * **Openbaar IP-adres:** selecteer het openbare IP-adres dat u wilt gebruiken of voer parameters in om een nieuw openbaar IP-adres te maken. U een openbaar IP-adres gebruiken om toegang te krijgen tot uw virtuele machine via internet. Zorg ervoor dat u ook een netwerkbeveiligingsgroep maakt om de toegang tot uw virtuele machine te beveiligen.
     * **Netwerkbeveiligingsgroep:** zie [Netwerkverkeerstroom beheren met netwerkbeveiligingsgroepen][virtual-networks-nsg]voor meer informatie.
   * **Extensies:** U virtuele machine-extensies installeren door ze toe te voegen aan de implementatie. U hoeft in deze stap geen extensies toe te voegen. De extensies die nodig zijn voor SAP-ondersteuning worden later geïnstalleerd. Zie hoofdstuk [Configureer de Azure Extension voor SAP][deployment-guide-4.5] in deze handleiding.
   * **Hoge beschikbaarheid**: Selecteer een beschikbaarheidsset of voer de parameters in om een nieuwe beschikbaarheidsset te maken. Zie [Azure-beschikbaarheidssets][planning-guide-3.2.3]voor meer informatie .
   * **Bewaking**
     * **Opstartdiagnostiek**: U **Uitschakelen** selecteren voor opstartdiagnose.
     * **GastOS-diagnose:** U **Uitschakelen** selecteren voor het controleren van diagnoses.

1. **Summary**:

   Bekijk uw selecties en selecteer **OK**.

Uw virtuele machine wordt geïmplementeerd in de resourcegroep die u hebt geselecteerd.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Een virtuele machine maken met behulp van een sjabloon

U een virtuele machine maken met behulp van een van de SAP-sjablonen die zijn gepubliceerd in de [GitHub-opslagplaats voor Azure-quickstart.][azure-quickstart-templates-github] U ook handmatig een virtuele machine maken met behulp van de [Azure-portal,][virtual-machines-windows-tutorial] [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]of [Azure CLI][virtual-machines-linux-tutorial].

* [**Sjabloon voor configuratie met twee lagen (slechts één virtuele machine)** (sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image]

  Als u een systeem met twee lagen wilt maken met slechts één virtuele machine, gebruikt u deze sjabloon.
* [**Sjabloon voor configuratie met twee lagen (slechts één virtuele machine) - Beheerde schijven** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  Als u een systeem met twee lagen wilt maken met slechts één virtuele machine en beheerde schijven, gebruikt u deze sjabloon.
* [**Sjabloon voor configuratie met drie lagen (meerdere virtuele machines)** (sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image]

  Als u een systeem met drie lagen wilt maken met meerdere virtuele machines, gebruikt u deze sjabloon.
* [**Sjabloon voor configuratie met drie lagen (meerdere virtuele machines) - Beheerde schijven** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  Als u een systeem met drie lagen wilt maken met meerdere virtuele machines en beheerde schijven, gebruikt u deze sjabloon.

Voer in de Azure-portal de volgende parameters voor de sjabloon in:

1. **Basics**:
   * **Abonnement:** het abonnement dat moet worden gebruikt om de sjabloon te implementeren.
   * **Resourcegroep:** de resourcegroep die moet worden gebruikt om de sjabloon te implementeren. U een nieuwe resourcegroep maken of u een bestaande resourcegroep selecteren in het abonnement.
   * **Locatie:** waar moet de sjabloon worden geïmplementeerd. Als u een bestaande resourcegroep hebt geselecteerd, wordt de locatie van die resourcegroep gebruikt.

1. **Instellingen**:
   * **SAP System ID**: De SAP System ID (SID).
   * **OS-type**: Het besturingssysteem dat u bijvoorbeeld Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) of Oracle Linux 7.2 wilt implementeren.

     In de lijstweergave worden niet alle ondersteunde besturingssystemen weergegeven. Zie SAP Note [1928533]voor meer informatie over ondersteunde besturingssystemen voor de implementatie van SAP-software.
   * **SAP-systeemgrootte**: De grootte van het SAP-systeem.

     Het aantal SAPS dat het nieuwe systeem biedt. Als u niet zeker weet hoeveel SAPS het systeem nodig heeft, vraag het dan aan uw SAP Technology Partner of System Integrator.
   * **Beschikbaarheid van het systeem** (alleen sjabloon met drie lagen): de beschikbaarheid van het systeem.

     Selecteer **HA** voor een configuratie die geschikt is voor een installatie met hoge beschikbaarheid. Er worden twee databaseservers en twee servers voor ABAP SAP Central Services (ASCS) gemaakt.
   * **Opslagtype** (alleen sjabloon met twee lagen): het type opslag dat moet worden gebruikt.

     Voor grotere systemen raden we ten zeerste aan azure premium opslag te gebruiken. Zie de volgende bronnen voor meer informatie over opslagtypen:
      * [Gebruik van Azure Premium SSD-opslag voor SAP DBMS-instantie][2367194]
      * [Opslagstructuur van een VM voor RDBMS-implementaties](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium-opslag: krachtige opslag voor Azure Virtual Machine-workloads][storage-premium-storage-preview-portal]
      * [Inleiding tot Microsoft Azure Storage][storage-introduction]
   * **Gebruikersnaam** en **beheerderswachtwoord**: Een gebruikersnaam en wachtwoord.
     Er wordt een nieuwe gebruiker gemaakt om zich aan te melden bij de virtuele machine.
   * **Nieuw of bestaand subnet**: hiermee bepaalt u of een nieuw virtueel netwerk en subnet worden gemaakt of dat een bestaand subnet wordt gebruikt. Als u al een virtueel netwerk hebt dat is verbonden met uw on-premises netwerk, selecteert u **Bestaand**.
   * **Subnet-id**: Als u de VM wilt implementeren in een bestaand VNet waarbij een subnet is gedefinieerd waaraan de VM moet worden toegewezen, geeft u de id van dat specifieke subnet een naam. De ID ziet er meestal&lt;als volgt uit:&lt;/subscriptions/ subscription id>/resourceGroups/&lt;resource group name>/providers/Microsoft.Network/virtualNetworks/ virtual network name>/subnets/&lt;subnet name>

1. **Algemene voorwaarden**:  
    Bekijk en accepteer de wettelijke voorwaarden.

1. Selecteer **Aankoop**.

De Azure VM-agent wordt standaard geïmplementeerd wanneer u een afbeelding uit de Azure Marketplace gebruikt.

#### <a name="configure-proxy-settings"></a>Proxyinstellingen configureren

Afhankelijk van hoe uw on-premises netwerk is geconfigureerd, moet u mogelijk de proxy op uw vm instellen. Als uw VM via VPN of ExpressRoute is verbonden met uw on-premises netwerk, heeft de VM mogelijk geen toegang tot internet en kan hij de vereiste VM-extensies niet downloaden of Azure-infrastructuurinformatie verzamelen voor de SAP Host-agent via de SAP-extensie voor Azure. Zie [De proxy configureren][deployment-guide-configure-proxy]voor meer informatie .

#### <a name="join-a-domain-windows-only"></a>Lid worden van een domein (alleen Windows)

Als uw Azure-implementatie is verbonden met een on-premises Active Directory- of DNS-instantie via een Azure-site-to-site VPN-verbinding of ExpressRoute (dit wordt *cross-premises* genoemd in [de planning en implementatie van Azure Virtual Machines voor SAP NetWeaver),][planning-guide]wordt verwacht dat de VM deelneemt aan een on-premises domein. Zie Een virtuele vm deelnemen [aan een on-premises domein (alleen Windows)][deployment-guide-4.3]voor meer informatie over overwegingen voor deze taak.

#### <a name="configure-vm-extension"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>VM-extensie configureren

Als u wilt weten of SAP uw omgeving ondersteunt, stelt u de Azure Extension for SAP in zoals beschreven in [Azure Extension for SAP configureren.][deployment-guide-4.5] Controleer de vereisten voor SAP en vereiste minimumversies van SAP Kernel en SAP Host Agent in de bronnen die in [SAP-bronnen worden][deployment-guide-2.2]vermeld.

#### <a name="vm-extension-for-sap-check"></a>VM-extensie voor SAP-controle

Controleer of de VM-extensie voor SAP werkt, zoals beschreven in [Controles en probleemoplossing voor end-to-end gegevensverzameling voor SAP Host Agent.][deployment-guide-troubleshooting-chapter]

#### <a name="post-deployment-steps"></a>Stappen na implementatie

Nadat u de VM hebt gemaakt en de VM is geïmplementeerd, moet u de vereiste softwarecomponenten in de VM installeren. Vanwege de implementatie/software-installatiereeks in dit type VM-implementatie moet de te installeren software al beschikbaar zijn, hetzij in Azure, op een andere VM, hetzij als een schijf die kan worden aangesloten. Of overweeg een cross-premises scenario te gebruiken, waarin connectiviteit met de on-premises assets (installatieaandelen) wordt gegeven.

Nadat u uw VM in Azure hebt geïmplementeerd, volgt u dezelfde richtlijnen en hulpprogramma's om de SAP-software op uw VM te installeren als in een on-premises omgeving. Als u SAP-software op een Azure-vm wilt installeren, raden zowel SAP als Microsoft u aan de SAP-installatiemedia te uploaden en op te slaan op Azure VHDs of Beheerde schijven, of dat u een Azure VM maakt die werkt als een bestandsserver met alle vereiste SAP-installatiemedia.

### <a name="scenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Scenario 2: Een VM implementeren met een aangepaste afbeelding voor SAP

Omdat verschillende versies van een besturingssysteem of DBMS verschillende patchvereisten hebben, voldoen de afbeeldingen die u in de Azure Marketplace vindt mogelijk niet aan uw behoeften. U in plaats daarvan een VM maken met behulp van uw eigen OS/DBMS VM-afbeelding, die u later opnieuw implementeren.
U gebruikt verschillende stappen om een privéafbeelding voor Linux te maken dan om er een voor Windows te maken.

---
> ![Windows][Logo_Windows] Windows
>
> Als u een Windows-afbeelding wilt voorbereiden waarmee u meerdere virtuele machines wilt implementeren, moeten de Windows-instellingen (zoals Windows SID en hostname) worden geabstraheerd of gegeneraliseerd op de on-premises VM. U [sysprep](https://msdn.microsoft.com/library/hh825084.aspx) gebruiken om dit te doen.
>
> ![Linux][Logo_Linux] Linux
>
> Om een Linux-afbeelding voor te bereiden die u gebruiken om meerdere virtuele machines te implementeren, moeten sommige Linux-instellingen worden geabstraheerd of gegeneraliseerd op de on-premises VM. U kunt `waagent -deprovision` gebruiken om dit te doen. Zie [Een virtuele Linux-machine vastleggen die op Azure en][virtual-machines-linux-capture-image] de gebruikershandleiding van de Azure [Linux-agent][virtual-machines-linux-agent-user-guide-command-line-options]wordt uitgevoerd voor meer informatie.
>
>

---
U een aangepaste afbeelding voorbereiden en maken en deze vervolgens gebruiken om meerdere nieuwe VM's te maken. Dit wordt beschreven in [de planning en implementatie van Azure Virtual Machines voor SAP NetWeaver.][planning-guide] Stel uw database-inhoud in met SAP Software Provisioning Manager om een nieuw SAP-systeem te installeren (herstelt een databaseback-up van een schijf die is gekoppeld aan de virtuele machine) of door een databaseback-up rechtstreeks te herstellen vanuit Azure-opslag, als uw DBMS dit ondersteunt. Zie [Azure Virtual Machines DBMS-implementatie voor SAP NetWeaver voor][dbms-guide]meer informatie. Als u al een SAP-systeem op uw on-premises VM hebt geïnstalleerd (met name voor tweelaagse systemen), u de SAP-systeeminstellingen aanpassen na de implementatie van de Azure VM met behulp van de systeemnaamprocedure die wordt ondersteund door SAP Software Provisioning Manager (SAP Note [1619720]). Anders u de SAP-software installeren nadat u de Azure VM hebt geïmplementeerd.

In het volgende stroomdiagram ziet u de SAP-specifieke reeks stappen voor het implementeren van een VM vanuit een aangepaste afbeelding:

![Stroomdiagram van VM-implementatie voor SAP-systemen met behulp van een VM-afbeelding in privémarktplaats][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Een virtuele machine maken met behulp van de Azure-portal

De eenvoudigste manier om een nieuwe virtuele machine te maken op basis van een beheerde schijfafbeelding, is door de Azure-portal te gebruiken. Lees [Een beheerde afbeelding van een gealgemene VM in Azure voor](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) meer informatie over het maken van een schijfafbeelding beheren.

1.  Ga naar <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. Of selecteer **Afbeeldingen**in het menu van de Azure-portal .
1.  Selecteer de afbeelding Van beheerde schijf die u wilt implementeren en klik op **VM maken**

De wizard begeleidt u bij het instellen van de vereiste parameters om de virtuele machine te maken, naast alle benodigde resources, zoals netwerkinterfaces en opslagaccounts. Enkele van deze parameters zijn:

1. **Basics**:
   * **Naam**: De naam van de resource (de naam van de virtuele machine).
   * **VM-schijftype:** selecteer het schijftype van de osschijf. Als u Premium Storage voor uw gegevensschijven wilt gebruiken, raden we u aan ook Premium Storage voor de OS-schijf te gebruiken.
   * **Gebruikersnaam en wachtwoord** of **SSH-openbare sleutel**: Voer de gebruikersnaam en het wachtwoord in van de gebruiker die tijdens de inrichting is gemaakt. Voor een Virtuele Linux-machine u de openbare Secure Shell -toets (SSH) invoeren die u gebruikt om u aan te melden bij de machine.
   * **Abonnement**: Selecteer het abonnement dat u wilt gebruiken om de nieuwe virtuele machine in te richten.
   * **Resourcegroep:** de naam van de resourcegroep voor de VM. U de naam van een nieuwe resourcegroep of de naam van een resourcegroep invoeren die al bestaat.
   * **Locatie**: Waar de nieuwe virtuele machine te implementeren. Als u de virtuele machine wilt verbinden met uw on-premises netwerk, moet u de locatie selecteren van het virtuele netwerk dat Azure verbindt met uw on-premises netwerk. Zie [Microsoft Azure-netwerken][planning-guide-microsoft-azure-networking] in [Azure Virtual Machines planning en implementatie voor SAP NetWeaver voor][planning-guide]meer informatie.
1. **Omvang**:

     Zie SAP Note [1928533]voor een lijst met ondersteunde VM-typen. Zorg ervoor dat u het juiste VM-type selecteert als u Azure Premium Storage wilt gebruiken. Niet alle VM-typen ondersteunen Premium Storage. Zie [Storage: Microsoft Azure Storage and data disks][planning-guide-storage-microsoft-azure-storage-and-data-disks] en Azure Premium [Storage][planning-guide-azure-premium-storage] in Azure Virtual Machines planning and implementation for [SAP NetWeaver for][planning-guide]more information.

1. **Instellingen**:
   * **Opslag**
     * **Schijftype:** selecteer het schijftype van de osschijf. Als u Premium Storage voor uw gegevensschijven wilt gebruiken, raden we u aan ook Premium Storage voor de OS-schijf te gebruiken.
     * **Beheerde schijven gebruiken:** Als u Beheerde schijven wilt gebruiken, selecteert u Ja. Zie hoofdstuk [Beheerde schijven][planning-guide-managed-disks] in de planningshandleiding voor meer informatie over beheerde schijven.
   * **Netwerk**
     * **Virtueel netwerk** en **subnet:** Als u de virtuele machine wilt integreren met uw intranet, selecteert u het virtuele netwerk dat is verbonden met uw on-premises netwerk.
     * **Openbaar IP-adres:** selecteer het openbare IP-adres dat u wilt gebruiken of voer parameters in om een nieuw openbaar IP-adres te maken. U een openbaar IP-adres gebruiken om toegang te krijgen tot uw virtuele machine via internet. Zorg ervoor dat u ook een netwerkbeveiligingsgroep maakt om de toegang tot uw virtuele machine te beveiligen.
     * **Netwerkbeveiligingsgroep:** zie [Netwerkverkeerstroom beheren met netwerkbeveiligingsgroepen][virtual-networks-nsg]voor meer informatie.
   * **Extensies:** U virtuele machine-extensies installeren door ze toe te voegen aan de implementatie. U hoeft in deze stap geen extensie toe te voegen. De extensies die nodig zijn voor SAP-ondersteuning worden later geïnstalleerd. Zie hoofdstuk [Configureer de Azure Extension voor SAP][deployment-guide-4.5] in deze handleiding.
   * **Hoge beschikbaarheid**: Selecteer een beschikbaarheidsset of voer de parameters in om een nieuwe beschikbaarheidsset te maken. Zie [Azure-beschikbaarheidssets][planning-guide-3.2.3]voor meer informatie .
   * **Bewaking**
     * **Opstartdiagnostiek**: U **Uitschakelen** selecteren voor opstartdiagnose.
     * **GastOS-diagnose:** U **Uitschakelen** selecteren voor het controleren van diagnoses.

1. **Summary**:

   Bekijk uw selecties en selecteer **OK**.

Uw virtuele machine wordt geïmplementeerd in de resourcegroep die u hebt geselecteerd.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Een virtuele machine maken met behulp van een sjabloon

Als u een implementatie wilt maken met behulp van een privé-besturingssysteemafbeelding van de Azure-portal, gebruikt u een van de volgende SAP-sjablonen. Deze sjablonen worden gepubliceerd in de [GitHub-repository met Azure-quickstart-sjablonen.][azure-quickstart-templates-github] U ook handmatig een virtuele machine maken met [PowerShell.][virtual-machines-upload-image-windows-resource-manager]

* [**Sjabloon voor configuratie met twee lagen (slechts één virtuele machine)** (sap-2-tier-user-image)][sap-templates-2-tier-user-image]

  Als u een systeem met twee lagen wilt maken met slechts één virtuele machine, gebruikt u deze sjabloon.
* [**Sjabloon voor configuratie met twee lagen (slechts één virtuele machine) - Beheerde schijfafbeelding** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  Als u een systeem met twee lagen wilt maken met slechts één virtuele machine en een beheerde schijfafbeelding, gebruikt u deze sjabloon.
* [**Sjabloon voor configuratie met drie lagen (meerdere virtuele machines)** (sap-3-tier-user-image)][sap-templates-3-tier-user-image]

  Als u een systeem met drie lagen wilt maken met meerdere virtuele machines of uw eigen OS-afbeelding, gebruikt u deze sjabloon.
* [**Sjabloon voor configuratie met drie lagen (meerdere virtuele machines) - Beheerde schijfafbeelding** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  Als u een systeem met drie lagen wilt maken met meerdere virtuele machines of uw eigen osafbeelding en een beheerde schijfafbeelding, gebruikt u deze sjabloon.

Voer in de Azure-portal de volgende parameters voor de sjabloon in:

1. **Basics**:
   * **Abonnement:** het abonnement dat moet worden gebruikt om de sjabloon te implementeren.
   * **Resourcegroep:** de resourcegroep die moet worden gebruikt om de sjabloon te implementeren. U een nieuwe resourcegroep maken of een bestaande resourcegroep selecteren in het abonnement.
   * **Locatie:** waar moet de sjabloon worden geïmplementeerd. Als u een bestaande resourcegroep hebt geselecteerd, wordt de locatie van die resourcegroep gebruikt.
1. **Instellingen**:
   * **SAP System ID**: De SAP System ID.
   * **BE-type:** het type besturingssysteem dat u wilt implementeren (Windows of Linux).
   * **SAP-systeemgrootte**: De grootte van het SAP-systeem.

     Het aantal SAPS dat het nieuwe systeem biedt. Als u niet zeker weet hoeveel SAPS het systeem nodig heeft, vraag het dan aan uw SAP Technology Partner of System Integrator.
   * **Beschikbaarheid van het systeem** (alleen sjabloon met drie lagen): de beschikbaarheid van het systeem.

     Selecteer **HA** voor een configuratie die geschikt is voor een installatie met hoge beschikbaarheid. Er worden twee databaseservers en twee servers voor ASCS gemaakt.
   * **Opslagtype** (alleen sjabloon met twee lagen): het type opslag dat moet worden gebruikt.

     Voor grotere systemen raden we ten zeerste aan azure premium opslag te gebruiken. Zie de volgende bronnen voor meer informatie over opslagtypen:
      * [Gebruik van Azure Premium SSD-opslag voor SAP DBMS-instantie][2367194]
      * [Opslagstructuur van een VM voor RDBMS-implementaties](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: krachtige opslag voor Azure virtual machine workloads][storage-premium-storage-preview-portal]
      * [Inleiding tot Microsoft Azure Storage][storage-introduction]
   * **Gebruikersafbeelding VHD URI** (alleen onbeheerde schijfafbeeldingssjabloon): de URI van&lt;de privé-osafbeelding VHD, bijvoorbeeld https:// accountnaam>.blob.core.windows.net/vhds/userimage.vhd.
   * **Account voor opslag van gebruikersafbeeldingen** (alleen onbeheerde schijfafbeeldingssjabloon): de naam van &lt;het opslagaccount&lt;waar de privé-besturingssysteemafbeelding wordt opgeslagen, bijvoorbeeld accountnaam> in https:// accountnaam>.blob.core.windows.net/vhds/userimage.vhd.
   * **userImageId** (alleen beheerde schijfafbeeldingssjabloon): id van de beheerde schijfafbeelding die u wilt gebruiken
   * **Gebruikersnaam** en **beheerderswachtwoord**: De gebruikersnaam en het wachtwoord.

     Er wordt een nieuwe gebruiker gemaakt om zich aan te melden bij de virtuele machine.
   * **Nieuw of bestaand subnet**: hiermee bepaalt u of een nieuw virtueel netwerk en subnet wordt gemaakt of dat een bestaand subnet wordt gebruikt. Als u al een virtueel netwerk hebt dat is verbonden met uw on-premises netwerk, selecteert u **Bestaand**.
   * **Subnet-id**: Als u de VM wilt implementeren in een bestaand VNet waarbij een subnet is gedefinieerd waaraan de VM moet worden toegewezen, geeft u de id van dat specifieke subnet een naam. De ID ziet er meestal&lt;als volgt uit:&lt;/subscriptions/ subscription id>/resourceGroups/&lt;resource group name>/providers/Microsoft.Network/virtualNetworks/ virtual network name>/subnets/&lt;subnet name>

1. **Algemene voorwaarden**:  
    Bekijk en accepteer de wettelijke voorwaarden.

1. Selecteer **Aankoop**.

#### <a name="install-the-vm-agent-linux-only"></a>De VM-agent installeren (alleen Linux)

Als u de sjablonen wilt gebruiken die in de vorige sectie zijn beschreven, moet de Linux-agent al in de gebruikersafbeelding zijn geïnstalleerd of mislukt de implementatie. Download en installeer de VM-agent in de gebruikersafbeelding zoals beschreven in [Downloaden, installeren en inschakelen van de Azure VM Agent][deployment-guide-4.4]. Als u de sjablonen niet gebruikt, u de VM-agent ook later installeren.

#### <a name="join-a-domain-windows-only"></a>Lid worden van een domein (alleen Windows)

Als uw Azure-implementatie is verbonden met een on-premises Active Directory- of DNS-instantie via een Azure-site-to-site VPN-verbinding of Azure ExpressRoute (dit wordt *cross-premises* genoemd in [de planning en implementatie van Azure Virtual Machines voor SAP NetWeaver),][planning-guide]wordt verwacht dat de VM een on-premises domein aansluit. Zie Een virtuele vm deelnemen [aan een on-premises domein (alleen Windows)][deployment-guide-4.3]voor meer informatie over overwegingen voor deze stap.

#### <a name="configure-proxy-settings"></a>Proxyinstellingen configureren

Afhankelijk van hoe uw on-premises netwerk is geconfigureerd, moet u mogelijk de proxy op uw vm instellen. Als uw vm via VPN of ExpressRoute is verbonden met uw on-premises netwerk, heeft de VM mogelijk geen toegang tot internet en kan hij de vereiste VM-extensies niet downloaden of Azure-infrastructuurinformatie verzamelen voor de SAP Host-agent via de SAP-extensie voor Azure, zie [De proxy configureren.][deployment-guide-configure-proxy]

#### <a name="configure-azure-vm-extension-for-sap"></a>Azure VM-extensie configureren voor SAP

Als u wilt weten of SAP uw omgeving ondersteunt, stelt u de Azure Extension for SAP in zoals beschreven in [Azure Extension for SAP configureren.][deployment-guide-4.5] Controleer de vereisten voor SAP en vereiste minimumversies van SAP Kernel en SAP Host Agent in de bronnen die in [SAP-bronnen worden][deployment-guide-2.2]vermeld.

#### <a name="sap-vm-extension-check"></a>SAP VM-extensiecontrole

Controleer of de VM-extensie voor SAP werkt, zoals beschreven in [Controles en probleemoplossing voor end-to-end gegevensverzameling voor SAP Host Agent.][deployment-guide-troubleshooting-chapter]


### <a name="scenario-3-moving-an-on-premises-vm-by-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Scenario 3: Een on-premises VM verplaatsen met een niet-gegeneraliseerde Azure VHD met SAP

In dit scenario bent u van plan een specifiek SAP-systeem te verplaatsen van een on-premises omgeving naar Azure. U dit doen door de VHD met het OS, de SAP-binaire bestanden en uiteindelijk de DBMS-binaries, plus de VHD's met de gegevens en logboekbestanden van de DBMS, naar Azure te uploaden. In tegenstelling tot het scenario dat wordt beschreven in [scenario 2: Het implementeren van een VM met een aangepaste afbeelding voor SAP][deployment-guide-3.3], in dit geval, behoudt u de hostname, SAP SID en SAP-gebruikersaccounts in de Azure VM, omdat deze zijn geconfigureerd in de on-premises omgeving. U hoeft het besturingssysteem niet te generaliseren. Dit scenario is meestal van toepassing op cross-premises scenario's waarbij een deel van het SAP-landschap on-premises wordt uitgevoerd en een deel ervan op Azure wordt uitgevoerd.

In dit scenario wordt de VM-agent **niet** automatisch geïnstalleerd tijdens de implementatie. Omdat de VM-agent en de Azure-extensie voor SAP nodig zijn om SAP NetWeaver op Azure uit te voeren, moet u beide componenten handmatig downloaden, installeren en inschakelen nadat u de virtuele machine hebt gemaakt.

Zie de volgende bronnen voor meer informatie over de Azure VM-agent.

---
> ![Windows][Logo_Windows] Windows
>
> [Overzicht van Azure Virtual Machine Agent][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Gebruikershandleiding voor Azure Linux Agent][virtual-machines-linux-agent-user-guide]
>
>

---

In het volgende stroomdiagram ziet u de reeks stappen voor het verplaatsen van een on-premises VM met behulp van een niet-gegeneraliseerde Azure VHD:

![Stroomdiagram van VM-implementatie voor SAP-systemen met behulp van een VM-schijf][deployment-guide-figure-400]

Als de schijf al is geüpload en gedefinieerd in Azure (zie [Azure Virtual Machines planning en implementatie voor SAP NetWeaver),][planning-guide]voert u de taken uit die in de volgende secties worden beschreven.

#### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Als u een implementatie wilt maken met behulp van een privé-schijf voor het besturingssysteem via de Azure-portal, gebruikt u de SAP-sjabloon die is gepubliceerd in de [GitHub-opslagplaats voor Azure-quickstart-sjablonen.][azure-quickstart-templates-github] U ook handmatig een virtuele machine maken met PowerShell.

* [**Sjabloon voor configuratie met twee lagen (slechts één virtuele machine)** (sap-2-tier-user-disk)][sap-templates-2-tier-os-disk]

  Als u een systeem met twee lagen wilt maken met slechts één virtuele machine, gebruikt u deze sjabloon.
* [**Sjabloon voor configuratie met twee lagen (slechts één virtuele machine) - Beheerde schijf** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  Als u een systeem met twee lagen wilt maken met slechts één virtuele machine en een beheerde schijf, gebruikt u deze sjabloon.

Voer in de Azure-portal de volgende parameters voor de sjabloon in:

1. **Basics**:
   * **Abonnement:** het abonnement dat moet worden gebruikt om de sjabloon te implementeren.
   * **Resourcegroep:** de resourcegroep die moet worden gebruikt om de sjabloon te implementeren. U een nieuwe resourcegroep maken of een bestaande resourcegroep selecteren in het abonnement.
   * **Locatie:** waar moet de sjabloon worden geïmplementeerd. Als u een bestaande resourcegroep hebt geselecteerd, wordt de locatie van die resourcegroep gebruikt.
1. **Instellingen**:
   * **SAP System ID**: De SAP System ID.
   * **BE-type:** het type besturingssysteem dat u wilt implementeren (Windows of Linux).
   * **SAP-systeemgrootte**: De grootte van het SAP-systeem.

     Het aantal SAPS dat het nieuwe systeem biedt. Als u niet zeker weet hoeveel SAPS het systeem nodig heeft, vraag het dan aan uw SAP Technology Partner of System Integrator.
   * **Opslagtype** (alleen sjabloon met twee lagen): het type opslag dat moet worden gebruikt.

     Voor grotere systemen raden we ten zeerste aan azure premium opslag te gebruiken. Zie de volgende bronnen voor meer informatie over opslagtypen:
      * [Gebruik van Azure Premium SSD-opslag voor SAP DBMS-instantie][2367194]
      * [Opslagstructuur van een VM voor RDBMS-implementaties](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium-opslag: krachtige opslag voor Azure Virtual Machine-workloads][storage-premium-storage-preview-portal]
      * [Inleiding tot Microsoft Azure Storage][storage-introduction]
   * **OS-schijf VHD URI** (alleen onbeheerde schijfsjabloon): de URI&lt;van de privé-osschijf, bijvoorbeeld https:// accountnaam>.blob.core.windows.net/vhds/osdisk.vhd.
   * **Os disk Managed Disk Id** (alleen beheerde schijfsjabloon): de id van de beheerschijf OS, /abonnementen/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroepen/groep/providers/Microsoft.Compute/disks/WIN
   * **Nieuw of bestaand subnet**: hiermee bepaalt u of een nieuw virtueel netwerk en subnet worden gemaakt of dat een bestaand subnet wordt gebruikt. Als u al een virtueel netwerk hebt dat is verbonden met uw on-premises netwerk, selecteert u **Bestaand**.
   * **Subnet-id**: Als u de VM wilt implementeren in een bestaand VNet waarbij een subnet is gedefinieerd waaraan de VM moet worden toegewezen, geeft u de id van dat specifieke subnet een naam. De ID ziet er meestal&lt;als volgt uit:&lt;/subscriptions/ subscription id>/resourceGroups/&lt;resource group name>/providers/Microsoft.Network/virtualNetworks/ virtual network name>/subnets/&lt;subnet name>

1. **Algemene voorwaarden**:  
    Bekijk en accepteer de wettelijke voorwaarden.

1. Selecteer **Aankoop**.

#### <a name="install-the-vm-agent"></a>De VM-agent installeren

Als u de sjablonen wilt gebruiken die in de vorige sectie zijn beschreven, moet de VM-agent op de osschijf zijn geïnstalleerd of mislukt de implementatie. Download en installeer de VM-agent in de VM, zoals beschreven in [Downloaden, installeren en inschakelen van de Azure VM Agent][deployment-guide-4.4].

Als u de sjablonen die in de vorige sectie zijn beschreven, niet gebruikt, u de VM-agent ook achteraf installeren.

#### <a name="join-a-domain-windows-only"></a>Lid worden van een domein (alleen Windows)

Als uw Azure-implementatie is verbonden met een on-premises Active Directory- of DNS-instantie via een Azure-site-to-site VPN-verbinding of ExpressRoute (dit wordt *cross-premises* genoemd in [de planning en implementatie van Azure Virtual Machines voor SAP NetWeaver),][planning-guide]wordt verwacht dat de VM deelneemt aan een on-premises domein. Zie Een virtuele vm deelnemen [aan een on-premises domein (alleen Windows)][deployment-guide-4.3]voor meer informatie over overwegingen voor deze taak.

#### <a name="configure-proxy-settings"></a>Proxyinstellingen configureren

Afhankelijk van hoe uw on-premises netwerk is geconfigureerd, moet u mogelijk de proxy op uw vm instellen. Als uw vm via VPN of ExpressRoute is verbonden met uw on-premises netwerk, heeft de VM mogelijk geen toegang tot internet en kan hij de vereiste VM-extensies niet downloaden of Azure-infrastructuurinformatie verzamelen voor de SAP Host-agent via de SAP-extensie voor Azure, zie [De proxy configureren.][deployment-guide-configure-proxy]

#### <a name="configure-azure-vm-extension-for-sap"></a>Azure VM-extensie configureren voor SAP

Als u wilt weten of SAP uw omgeving ondersteunt, stelt u de Azure Extension for SAP in zoals beschreven in [Azure Extension for SAP configureren.][deployment-guide-4.5] Controleer de vereisten voor SAP en vereiste minimumversies van SAP Kernel en SAP Host Agent in de bronnen die in [SAP-bronnen worden][deployment-guide-2.2]vermeld.

#### <a name="sap-vm-check"></a>SAP VM-controle

Controleer of de VM-extensie voor SAP werkt, zoals beschreven in [Controles en probleemoplossing voor end-to-end gegevensverzameling voor SAP Host Agent.][deployment-guide-troubleshooting-chapter]

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>De configuratie van Azure Extension voor SAP bijwerken

Werk de configuratie van Azure Extension voor SAP bij in een van de volgende scenario's:
* Het gezamenlijke Microsoft/SAP-team breidt de mogelijkheden van de VM-extensie uit en vraagt meer of minder tellers.
* Microsoft introduceert een nieuwe versie van de onderliggende Azure-infrastructuur die de gegevens levert, en de Azure-extensie voor SAP moet worden aangepast aan deze wijzigingen.
* U monteert extra gegevensschijven op uw Azure VM of u verwijdert een gegevensschijf. Werk in dit scenario het verzamelen van opslaggerelateerde gegevens bij. Het wijzigen van uw configuratie door het toevoegen of verwijderen van eindpunten of door IP-adressen toe te wijzen aan een VM heeft geen invloed op de configuratie van de extensie.
* U wijzigt bijvoorbeeld de grootte van uw Azure VM van grootte A5 naar een andere VM-grootte.
* U voegt nieuwe netwerkinterfaces toe aan uw Azure VM.

Als u instellingen wilt bijwerken, werkt u de configuratie van Azure Extension voor SAP bij door de stappen te volgen in [Azure Extension voor SAP configureren.][deployment-guide-4.5]

## <a name="detailed-tasks-for-sap-software-deployment"></a>Gedetailleerde taken voor de implementatie van SAP-software

In deze sectie worden gedetailleerde stappen uitgevoerd voor het uitvoeren van specifieke taken in het configuratie- en implementatieproces.

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Azure PowerShell-cmdlets implementeren

1. Ga naar [Microsoft Azure Downloads](https://azure.microsoft.com/downloads/).
1. Selecteer **Onder Command-line-hulpprogramma's**onder **PowerShell**de optie **Windows installeren**.
1. Selecteer in het dialoogvenster Microsoft Downloadbeheer voor het gedownloade bestand (bijvoorbeeld WindowsAzurePowershellGet.3f.3f.3fnew.exe) de optie **Uitvoeren**.
1. Als u Microsoft Web Platform Installer (Microsoft Web PI) wilt uitvoeren, selecteert u **Ja**.
1. Er wordt een pagina weergegeven die er zo uitziet:

   ![Installatiepagina voor Azure PowerShell-cmdlets][deployment-guide-figure-500]<a name="figure-5"></a>

1. Selecteer **Installeren**en accepteer vervolgens de licentievoorwaarden voor Microsoft-software.
1. PowerShell is geïnstalleerd. Selecteer **Voltooien** om de wizard installatie te sluiten.

Controleer regelmatig op updates van de PowerShell-cmdlets, die meestal maandelijks worden bijgewerkt. De eenvoudigste manier om te controleren op updates is om de voorgaande installatiestappen uit te voeren, tot aan de installatiepagina in stap 5. De releasedatum en het releasenummer van de cmdlets zijn opgenomen op de pagina in stap 5. Tenzij anders vermeld in SAP Note [1928533] of SAP Note [2015553,]raden we u aan om te werken met de nieuwste versie van Azure PowerShell-cmdlets.

Voer de opdracht PowerShell uit om de versie van de Azure PowerShell-cmdlets te controleren die op uw computer zijn geïnstalleerd:

```powershell
(Get-Module Az.Compute).Version
```

Het resultaat ziet er als volgt uit:

![Resultaat van Azure PowerShell-cmdlet-versiecontrole][deployment-guide-figure-600]
<a name="figure-6"></a>

Als de Azure-cmdlet-versie die op uw computer is geïnstalleerd de huidige versie is, geeft de eerste pagina van de installatiewizard dit aan door **(Geïnstalleerd)** toe te voegen aan de producttitel (zie de volgende schermafbeelding). Uw PowerShell Azure-cmdlets zijn up-to-date. Als u de wizard Installatie wilt sluiten, selecteert u **Afsluiten**.

![Installatiepagina voor Azure PowerShell-cmdlets die aangeven dat de meest recente versie van Azure PowerShell-cmdlets is geïnstalleerd][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Azure CLI implementeren

1. Ga naar [Microsoft Azure Downloads](https://azure.microsoft.com/downloads/).
1. Selecteer onder **Opdrachtregelgereedschappen**onder **Azure-opdrachtregelinterface**de koppeling **Installeren** voor uw besturingssysteem.
1. Selecteer in het dialoogvenster Microsoft Downloadbeheer voor het gedownloade bestand (bijvoorbeeld WindowsAzureXPlatCLI.3f.3f.3fnew.exe) de optie **Uitvoeren**.
1. Als u Microsoft Web Platform Installer (Microsoft Web PI) wilt uitvoeren, selecteert u **Ja**.
1. Er wordt een pagina weergegeven die er zo uitziet:

   ![Installatiepagina voor Azure PowerShell-cmdlets][deployment-guide-figure-500]<a name="figure-5"></a>

1. Selecteer **Installeren**en accepteer vervolgens de licentievoorwaarden voor Microsoft-software.
1. Azure CLI is geïnstalleerd. Selecteer **Voltooien** om de wizard installatie te sluiten.

Controleer regelmatig op updates van Azure CLI, die meestal maandelijks worden bijgewerkt. De eenvoudigste manier om te controleren op updates is om de voorgaande installatiestappen uit te voeren, tot aan de installatiepagina in stap 5.

Voer de opdracht uit om de versie van Azure CLI te controleren die op uw computer is geïnstalleerd:

```console
azure --version
```

Het resultaat ziet er als volgt uit:

![Resultaat van Azure CLI-versiecontrole][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="join-a-vm-to-an-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Een vm deelnemen aan een on-premises domein (alleen Windows)

Als u SAP VM's implementeert in een cross-premises scenario, waarbij on-premises Active Directory en DNS worden uitgebreid in Azure, wordt verwacht dat de VM's een on-premises domein samenvoegen. De gedetailleerde stappen die u neemt om lid te worden van een VM in een on-premises domein en de extra software die nodig is om lid te zijn van een on-premises domein, variëren per klant. Meestal moet u extra software installeren, zoals antimalwaresoftware en back-up- of bewakingssoftware om lid te worden van een VM naar een on-premises domein.

In dit scenario moet u er ook voor zorgen dat als de instellingen van internetproxy worden geforceerd wanneer een VM een domein in uw omgeving aansluit, het Windows Local System Account (S-1-5-18) in de gast-VM dezelfde proxy-instellingen heeft. De eenvoudigste optie is om de proxy te forceren met behulp van een domeingroepsbeleid, dat van toepassing is op systemen in het domein.

### <a name="download-install-and-enable-the-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>De Azure VM-agent downloaden, installeren en inschakelen

Voor virtuele machines die worden geïmplementeerd vanuit een os-afbeelding die niet is gegeneraliseerd (bijvoorbeeld een afbeelding die niet afkomstig is van het Windows-systeemvoorbereiding of het sysprep-hulpprogramma), moet u de Azure VM-agent handmatig downloaden, installeren en inschakelen.

Als u een VM implementeert vanuit de Azure Marketplace, is deze stap niet vereist. Afbeeldingen van de Azure Marketplace hebben al de Azure VM-agent.

#### <a name="windows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Download de Azure VM-agent:
   1.  Download het [installatiepakket azure VM Agent.](https://go.microsoft.com/fwlink/?LinkId=394789)
   1.  Sla het VM Agent MSI-pakket lokaal op een personal computer of server op.
1. Installeer de Azure VM-agent:
   1.  Maak verbinding met de geïmplementeerde Azure VM met Extern bureaublad-protocol (RDP).
   1.  Open een Windows Explorer-venster op de vm en selecteer de doelmap voor het MSI-bestand van de VM-agent.
   1.  Sleep het MSI-bestand Azure VM Agent Installer van uw lokale computer/server naar de doelmap van de VM-agent op de VM.
   1.  Dubbelklik op het MSI-bestand op de VM.
1. Voor VM's die zijn verbonden met on-premises domeinen, moet u ervoor zorgen dat eventuele internetproxy-instellingen ook van toepassing zijn op het Windows Local System-account (S-1-5-18) in de VM, zoals beschreven in [De proxy configureren.][deployment-guide-configure-proxy] De VM-agent wordt in deze context uitgevoerd en moet verbinding kunnen maken met Azure.

Er is geen gebruikersinteractie vereist om de Azure VM-agent bij te werken. De VM-agent wordt automatisch bijgewerkt en vereist geen herstart van de virtuele machine.

#### <a name="linux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

Gebruik de volgende opdrachten om de VM-agent voor Linux te installeren:

* **SUSE Linux Enterprise Server (SLES)**

  ```console
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) of Oracle Linux**

  ```console
  sudo yum install WALinuxAgent
  ```

Als de agent al is geïnstalleerd, voert u de stappen uit die zijn beschreven in [Update de Azure Linux Agent op een VM naar de nieuwste versie van GitHub.][virtual-machines-linux-update-agent]

### <a name="configure-the-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>De proxy configureren

De stappen die u neemt om de proxy in Windows te configureren, verschillen van de manier waarop u de proxy in Linux configureert.

#### <a name="windows"></a>Windows

Proxy-instellingen moeten correct zijn ingesteld voor het lokale systeemaccount om toegang te krijgen tot internet. Als uw proxy-instellingen niet zijn ingesteld op groepsbeleid, u de instellingen voor het lokale systeemaccount configureren.

1. Ga naar **Start,** voer **gpedit.msc in**en selecteer **Enter**.
1. Selecteer > **Systeembeheersjablonen voor** >  **computerconfiguratie****Windows-onderdelen** > **Internet Explorer**. Zorg ervoor dat de instelling **Maak proxy-instellingen per machine (in plaats van per gebruiker)** is uitgeschakeld of niet geconfigureerd.
1. Ga in **het Configuratiescherm**naar**Internetopties** **netwerk- en deelcentrum** > .
1. Selecteer op het tabblad **Verbindingen** de knop **LAN-instellingen.**
1. Schakel het selectievakje **Instellingen automatisch detecteren** uit.
1. Schakel het selectievakje **Een proxyserver voor uw LAN gebruiken** in en voer het proxyadres en de poort in.
1. Selecteer de knop **Geavanceerd.**
1. Voer **in** het vak Uitzonderingen het IP-adres **168.63.129.16**in . Selecteer **OK**.

#### <a name="linux"></a>Linux

Configureer de juiste proxy in het configuratiebestand van de \\\\Microsoft Azure Guest Agent, die zich op etc waagent.conf bevindt.

Stel de volgende parameters in:

1. **HTTP-proxyhost**. Stel het bijvoorbeeld in op **proxy.corp.local**.

   ```console
   HttpProxy.Host=<proxy host>

   ```

1. **HTTP-proxypoort**. Stel deze bijvoorbeeld in op **80**.

   ```console
   HttpProxy.Port=<port of the proxy host>

   ```

1. Start de agent opnieuw.

   ```console
   sudo service waagent restart
   ```

De proxy-instellingen in \\etc\\waagent.conf zijn ook van toepassing op de vereiste VM-extensies. Als u de Azure-opslagplaatsen wilt gebruiken, moet u ervoor zorgen dat het verkeer naar deze opslagplaatsen niet via uw on-premises intranet verloopt. Als u door de gebruiker gedefinieerde routes hebt gemaakt om gedwongen tunneling mogelijk te maken, moet u ervoor zorgen dat u een route toevoegt die verkeer rechtstreeks naar de opslagplaatsen naar het internet leidt, en niet via uw vpn-verbinding van site naar site.

* **SLES**

  U moet ook routes toevoegen voor de \\IP-adressen die worden vermeld in etc\\regionserverclnt.cfg. Het volgende cijfer geeft een voorbeeld:

  ![Geforceerde tunneling][deployment-guide-figure-50]


* **RHEL**

  U moet ook routes toevoegen voor de IP-adressen \\\\van de hosts\\die worden vermeld in etc yum.repos.d rhui-load-balancers. Zie bijvoorbeeld het voorgaande cijfer.

* **Oracle Linux**

  Er zijn geen repositories voor Oracle Linux op Azure. U moet uw eigen repositories configureren voor Oracle Linux of de openbare repositories gebruiken.

Zie [Door de gebruiker gedefinieerde routes en IP-forwarding][virtual-networks-udr-overview]voor meer informatie over door de gebruiker gedefinieerde routes.

### <a name="configure-the-azure-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>De Azure-extensie voor SAP configureren

Wanneer u de VM hebt voorbereid zoals beschreven in [implementatiescenario's van VM's voor SAP op Azure,][deployment-guide-3]wordt de Azure VM-agent op de virtuele machine geïnstalleerd. De volgende stap is het implementeren van de Azure Extension voor SAP, die beschikbaar is in de Azure Extension Repository in de wereldwijde Azure-datacenters. Zie [Azure Virtual Machines planning en implementatie voor SAP NetWeaver voor][planning-guide-9.1]meer informatie.

U PowerShell of Azure CLI gebruiken om de Azure Extension voor SAP te installeren en te configureren. Zie [Azure PowerShell][deployment-guide-4.5.1]als u de extensie op een Windows- of Linux-vm wilt installeren met behulp van een Windows-machine. Zie [Azure CLI][deployment-guide-4.5.2]als u de extensie op een Linux-vm wilt installeren met behulp van een Linux-bureaublad.

#### <a name="azure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell voor Linux- en Windows VM's

Ga als het gaat om de Azure Extension voor SAP te installeren met PowerShell:

1. Zorg ervoor dat u de nieuwste versie van de Azure PowerShell-cmdlet hebt geïnstalleerd. Zie [Azure PowerShell-cmdlets implementeren voor][deployment-guide-4.1]meer informatie.  
1. Voer de volgende PowerShell-cmdlet uit.
    Voer voor een lijst `commandlet Get-AzEnvironment`met beschikbare omgevingen uit. Als u wereldwijd Azure wilt gebruiken, is uw omgeving **AzureCloud.** Selecteer **AzureChinaCloud**voor Azure in China .

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Nadat u uw accountgegevens hebt ingevoerd en de virtuele Azure-machine hebt geïdentificeerd, implementeert het script de vereiste extensies en worden de vereiste functies geïmplementeerd. Dit kan enkele minuten duren.
Zie Set-AzVMAEMExtension voor meer informatie over `Set-AzVMAEMExtension`, zie [Set-AzVMAEMExtension][msdn-set-Azvmaemextension].

![Succesvolle uitvoering van SAP-specifieke Azure cmdlet Set-AzVMAEMExtension][deployment-guide-figure-900]

De `Set-AzVMAEMExtension` configuratie doet alle stappen om het verzamelen van hostgegevens voor SAP te configureren.

De scriptuitvoer bevat de volgende informatie:

* Bevestiging dat het verzamelen van gegevens voor de osschijf en alle extra gegevensschijven is geconfigureerd.
* De volgende twee berichten bevestigen de configuratie van opslagstatistieken voor een specifiek opslagaccount.
* Eén uitvoerregel geeft de status van de werkelijke update van de VM-extensie voor SAP-configuratie.
* Een andere uitvoerlijn bevestigt dat de configuratie is geïmplementeerd of bijgewerkt.
* De laatste regel is informatief. Het toont uw opties voor het testen van de VM-extensie voor SAP-configuratie.
* Ga te werk met de gereedheidscontrole voor de Azure Extension voor SAP, zoals beschreven in [Gereedheidscontrole voor Azure Extension voor SAP.][deployment-guide-5.1]
* Wacht 15-30 minuten tot Azure Diagnostics de relevante gegevens verzamelt.

#### <a name="azure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Azure CLI voor Linux VM's

Ga als het gaat om het installeren van de Azure Extension voor SAP met Azure CLI:

   1. Installeer Azure classic CLI, zoals beschreven in [Installeer de Azure classic CLI][azure-cli].
   1. Meld u aan met uw Azure-account:

      ```console
      azure login
      ```

   1. Overschakelen naar de Azure Resource Manager-modus:

      ```console
      azure config mode arm
      ```

   1. Azure Extension voor SAP inschakelen:

      ```console
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Installeren met Azure CLI 2.0

   1. Installeer Azure CLI 2.0, zoals beschreven in [Installatie Azure CLI 2.0][azure-cli-2].
   1. Meld u aan met uw Azure-account:

      ```azurecli
      az login
      ```

   1. Azure CLI AEM-extensie installeren
  
      ```azurecli
      az extension add --name aem
      ```
  
   1. Installeer de extensie met
  
      ```azurecli
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Controleer of de Azure Extension for SAP actief is op de Azure Linux VM. Controleer of \\de\\\\bestandsvar\\lib AzureEnhancedMonitor PerfCounters bestaat. Als deze bestaat, voert u bij een opdrachtprompt deze opdracht uit om informatie weer te geven die is verzameld door de Azure Extension voor SAP:

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   De uitvoer ziet er als volgt uit:

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

## <a name="checks-and-troubleshooting-for-end-to-end-data-collection-for-sap-host-agent"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Controles en probleemoplossing voor end-to-end gegevensverzameling voor SAP Host Agent

Nadat u uw Azure VM hebt geïmplementeerd en de relevante Azure-extensie voor SAP hebt ingesteld, controleert u of alle onderdelen van de extensie werken zoals verwacht.

Voer de gereedheidscontrole uit voor de Azure Extension for SAP zoals beschreven in [Gereedheidscontrole voor de Azure Extension for SAP.][deployment-guide-5.1] Als alle resultaten van de gereedheidscontrole positief zijn en alle relevante prestatiemeteritems ok worden weergegeven, is Azure Extension voor SAP met succes ingesteld. U doorgaan met de installatie van SAP Host Agent zoals beschreven in de SAP Notes in [SAP-bronnen.][deployment-guide-2.2] Als uit de gereedheidscontrole blijkt dat er tellers ontbreken, voert u de statuscontrole uit voor de Azure Extension for SAP, zoals beschreven in [Health check for Azure Extension for SAP-configuratie.][deployment-guide-5.2] Zie Azure Extension [voor SAP oplossen voor][deployment-guide-5.3]meer probleemoplossing.

### <a name="readiness-check-for-the-azure-extension-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Gereedheidscontrole voor de Azure-extensie voor SAP

Deze controle zorgt ervoor dat alle prestatiestatistieken die in uw SAP-toepassing worden weergegeven, worden geleverd door de onderliggende Azure Extension voor SAP.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>De gereedheidscontrole uitvoeren op een Windows-vm

1. Meld u aan bij de virtuele Azure-machine (het is niet nodig om een beheerdersaccount te gebruiken).
1. Open een opdrachtpromptvenster.
1. Wijzig bij de opdrachtprompt de map in de installatiemap van\\\\de\\Azure Extension for SAP: C: Packages\\&lt;Plugins Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler-versie>\\drop

   De *versie* in het pad naar de extensie kan variëren. Als u mappen voor meerdere versies van de extensie in de installatiemap ziet, controleert u de configuratie van de Windows-service AzureEnhancedMonitoring en schakelt u over naar de map die is aangegeven als *Pad naar uitvoerbaar*.

   ![Eigenschappen van service waarop de Azure-extensie voor SAP wordt uitgevoerd][deployment-guide-figure-1000]

1. Voer bij de opdrachtprompt **azperflib.exe** uit zonder parameters.

   > [!NOTE]
   > Azperflib.exe loopt in een lus en werkt de verzamelde tellers elke 60 seconden bij. Als u de lus wilt beëindigen, sluit u het venster Opdrachtprompt.
   >
   >

Als de Azure Extension voor SAP niet is geïnstalleerd of als de AzureEnhancedMonitoring-service niet wordt uitgevoerd, is de extensie niet correct geconfigureerd. Zie [Probleemoplossing voor de Azure-extensie voor SAP voor][deployment-guide-5.3]meer informatie over het implementeren van de extensie.

> [!NOTE]
> De Azperflib.exe is een component die niet voor eigen doeleinden kan worden gebruikt. Het is een onderdeel dat Azure-infrastructuurgegevens met betrekking tot de VM voor de SAP Host Agent uitsluitend levert.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Controleer de output van azperflib.exe

Azperflib.exe-uitvoer toont alle ingevulde Azure-prestatiemeteritems voor SAP. Onder aan de lijst met verzamelde tellers geven een overzicht en statusindicator de status van Azure Extension voor SAP weer.

![Output van de gezondheidscontrole door het uitvoeren van azperflib.exe, wat aangeeft dat er geen problemen bestaan][deployment-guide-figure-1100]
<a name="figure-11"></a>

Controleer het geretourneerde resultaat voor de totale uitvoer **van Tellers,** die als leeg wordt gerapporteerd en voor **de status Status**, weergegeven in de vorige afbeelding.

Interpreteer de resulterende waarden als volgt:

| Resultatenwaarden azperflib.exe | Azure-extensie voor de status van SAP |
| --- | --- |
| **API-aanroepen - niet beschikbaar** | Tellers die niet beschikbaar zijn, zijn mogelijk niet van toepassing op de configuratie van de virtuele machine of zijn fouten. Zie **Status van status**. |
| **Tellers totaal - leeg** |De volgende twee Azure-opslagtellers kunnen leeg zijn: <ul><li>Storage Read Op Latency Server msec</li><li>Storage Read Op Latency E2E msec</li></ul>Alle andere tellers moeten waarden hebben. |
| **Status van gezondheid** |Alleen OK als de retourstatus **OK**wordt weergegeven. |
| **Diagnostiek** |Gedetailleerde informatie over de gezondheidstoestand. |

Als de statuswaarde **Status** niet **ok**is, volgt u de instructies in [De controle op status voor Azure Extension voor SAP-configuratie][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Voer de gereedheidscontrole uit op een Linux-VM

1. Maak verbinding met de Azure Virtual Machine met Behulp van SSH.

1. Controleer de uitvoer van de Azure-extensie voor SAP.

   a.  Voer `more /var/lib/AzureEnhancedMonitor/PerfCounters` uit.

   **Verwacht resultaat**: Geeft als resultaat een lijst met prestatiemeteritems. Het bestand mag niet leeg zijn.

   b. Voer `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error` uit.

   **Verwacht resultaat**: geeft als resultaat één regel waarin de fout **geen**is, bijvoorbeeld **3;config; Fout;;0;0; none;0;1456416792;tst-servercs;**

   c. Voer `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord` uit.

   **Verwacht resultaat**: Retourzendingen als leeg of bestaan niet.

Als de vorige controle niet is geslaagd, voert u de volgende aanvullende controles uit:

1. Zorg ervoor dat de waagent is geïnstalleerd en ingeschakeld.

   a.  Voer `sudo ls -al /var/lib/waagent/` uit.

     **Verwacht resultaat**: geeft de inhoud van de waagent-map weer.

   b.  Voer `ps -ax | grep waagent` uit.

   **Verwacht resultaat**: Geeft één vermelding weer die vergelijkbaar is met:`python /usr/sbin/waagent -daemon`

1. Zorg ervoor dat de Azure Extension for SAP is geïnstalleerd en uitgevoerd.

   a.  Voer `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'` uit.

   **Verwacht resultaat:** geeft de inhoud van de Azure Extension for SAP-map weer.

   b. Voer `ps -ax | grep AzureEnhanced` uit.

   **Verwacht resultaat**: Geeft één vermelding weer die vergelijkbaar is met:`python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Installeer SAP Host Agent zoals beschreven in SAP Note [1031096]en controleer de uitvoer van `saposcol`.

   a.  Voer `/usr/sap/hostctrl/exe/saposcol -d` uit.

   b.  Voer `dump ccm` uit.

   c.  Controleer of de **statistiek Virtualization_Configuration\Uitgebreide bewakingstoegang** **waar is.**

Als u al een SAP NetWeaver ABAP-toepassingsserver hebt geïnstalleerd, opent u de transactie ST06 en controleert u of uitgebreide bewaking is ingeschakeld.

Zie Problemen met [de Azure-extensie voor SAP oplossen][deployment-guide-5.3]als een van deze controles mislukt en voor gedetailleerde informatie over het opnieuw implementeren van de extensie.

### <a name="health-check-for-the-azure-extension-for-sap-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Statuscontrole voor de Azure-extensie voor SAP-configuratie

Als sommige infrastructuurgegevens niet correct worden geleverd zoals aangegeven in de test die is `Test-AzVMAEMExtension` beschreven in [de gereedheidscontrole voor Azure Extension voor SAP,][deployment-guide-5.1]voert u de cmdlet uit om te controleren of de Azure-infrastructuur en de Azure-extensie voor SAP correct zijn geconfigureerd.

1. Zorg ervoor dat u de nieuwste versie van de Azure PowerShell-cmdlet hebt geïnstalleerd, zoals beschreven in [Azure PowerShell-cmdlets implementeren.][deployment-guide-4.1]
1. Voer de volgende PowerShell-cmdlet uit. Voer de cmdlet `Get-AzEnvironment`uit voor een lijst met beschikbare omgevingen. Als u globale Azure wilt gebruiken, selecteert u de **AzureCloud-omgeving.** Selecteer **AzureChinaCloud**voor Azure in China .

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Voer uw accountgegevens in en identificeer de virtuele Azure-machine.

   ![Invoerpagina van SAP-specifieke Azure-cmdlet Test-VMConfigForSAP_GUI][deployment-guide-figure-1200]

1. Het script test de configuratie van de virtuele machine die u selecteert.

   ![Uitvoer van succesvolle test van de Azure-extensie voor SAP][deployment-guide-figure-1300]

Zorg ervoor dat elke health check resultaat **in orde**is. Als sommige controles niet **OK**worden weergegeven, voert u de updatecmdlet uit zoals beschreven in [De Azure-extensie configureren voor SAP][deployment-guide-4.5]. Wacht 15 minuten en herhaal de controles die zijn beschreven in [de gereedheidscontrole voor Azure Extension voor SAP-][deployment-guide-5.1] en [statuscontrole voor Azure Extension voor SAP-configuratie.][deployment-guide-5.2] Zie Problemen met de Azure Extension voor [SAP][deployment-guide-5.3]oplossen als de controles nog steeds wijzen op een probleem met sommige of alle tellers.

> [!Note]
> U bepaalde waarschuwingen ervaren in gevallen waarin u Managed Standard Azure Disks gebruikt. Waarschuwingen worden weergegeven in plaats van de tests die "OK" retourneren. Dit is normaal en bedoeld in het geval van dat schijftype. Zie ook [Het oplossen van problemen met de Azure-extensie voor SAP][deployment-guide-5.3]
> 

### <a name="troubleshooting-azure-extension-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Azure-extensie voor SAP oplossen

#### <a name="windowslogo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Azure-prestatiemeteritems worden helemaal niet weergegeven

De AzureEnhancedMonitoring Windows-service verzamelt prestatiestatistieken in Azure. Als de service niet correct is geïnstalleerd of als deze niet in uw vm wordt uitgevoerd, kunnen geen prestatiestatistieken worden verzameld.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>De installatiemap van de Azure Extension voor SAP is leeg

###### <a name="issue"></a>Probleem

De installatiemap\\C: Pakketten\\Plug-ins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler-versie\\&lt;>-drop \\leeg is.

###### <a name="solution"></a>Oplossing

De extensie is niet geïnstalleerd. Bepaal of dit een proxyprobleem is (zoals eerder beschreven). Mogelijk moet u de machine opnieuw `Set-AzVMAEMExtension` opstarten of het configuratiescript opnieuw uitvoeren.

##### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>Service voor Azure Extension voor SAP bestaat niet

###### <a name="issue"></a>Probleem

De Windows-service AzureEnhancedMonitoring bestaat niet.

Azperflib.exe output gooit een fout:

![Uitvoering van azperflib.exe geeft aan dat de service van de Azure Extension voor SAP niet wordt uitgevoerd][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Oplossing

Als de service niet bestaat, is de Azure-extensie voor SAP niet correct geïnstalleerd. Implementeer de extensie opnieuw met behulp van de stappen die zijn beschreven voor uw implementatiescenario in [implementatiescenario's van VM's voor SAP in Azure.][deployment-guide-3]

Controleer na het implementeren van de extensie na een uur opnieuw of de Azure-prestatiemeteritems zijn geleverd in de Azure VM.

##### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>Service voor Azure Extension voor SAP bestaat, maar kan niet worden gestart

###### <a name="issue"></a>Probleem

De AzureEnhancedMonitoring Windows-service bestaat en is ingeschakeld, maar kan niet worden gestart. Ga voor meer informatie naar het logboek van de aanvraaggebeurtenis.

###### <a name="solution"></a>Oplossing

De configuratie is onjuist. Start de Azure-extensie voor SAP opnieuw in de VM, zoals beschreven in [De Azure-extensie configureren voor SAP][deployment-guide-4.5].

#### <a name="windowslogo_windows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Sommige Azure-prestatiemeteritems ontbreken

De AzureEnhancedMonitoring Windows-service verzamelt prestatiestatistieken in Azure. De service krijgt gegevens uit verschillende bronnen. Sommige configuratiegegevens worden lokaal verzameld en sommige prestatiestatistieken worden gelezen vanuit Azure Diagnostics. Opslagtellers worden gebruikt vanaf uw logboekregistratie op het opslagabonnementsniveau.

Als het probleem probleem niet wordt opgelost door het probleem met SAP `Set-AzVMAEMExtension` Note [1999351] te gebruiken, voert u het configuratiescript opnieuw uit. Mogelijk moet u een uur wachten omdat opslaganalyses of diagnostische tellers mogelijk niet onmiddellijk worden gemaakt nadat ze zijn ingeschakeld. Als het probleem zich blijft voordoen, opent u een SAP-klantondersteuningsbericht op het onderdeel BC-OP-NT-AZR voor Windows of BC-OP-LNX-AZR voor een virtuele Linux-machine.

#### <a name="linuxlogo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Azure-prestatiemeteritems worden helemaal niet weergegeven

Prestatiestatistieken in Azure worden verzameld door een daemon. Als de daemon niet wordt uitgevoerd, kunnen er geen prestatiestatistieken worden verzameld.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>De installatiemap van de Azure Extension voor SAP is leeg

###### <a name="issue"></a>Probleem

De \\directory\\\\var\\ lib waagent heeft geen submap voor de Azure Extension voor SAP.

###### <a name="solution"></a>Oplossing

De extensie is niet geïnstalleerd. Bepaal of dit een proxyprobleem is (zoals eerder beschreven). Mogelijk moet u de machine opnieuw opstarten `Set-AzVMAEMExtension` en/of het configuratiescript opnieuw uitvoeren.

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>De uitvoering van Set-AzVMAEMExtension en Test-AzVMAEMExtension tonen waarschuwingsberichten waarin staat dat standaard beheerde schijven niet worden ondersteund

###### <a name="issue"></a>Probleem

Bij het uitvoeren van Set-AzVMAEMExtension of Test-AzVMAEMExtension worden berichten als deze weergegeven:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Het uitvoeren van azperfli.exe zoals eerder beschreven u een resultaat dat wijst op een niet-gezonde toestand te krijgen. 

###### <a name="solution"></a>Oplossing

De berichten worden veroorzaakt door het feit dat standaard beheerde schijven niet de API's leveren die door de SAP-extensie voor SAP worden gebruikt om de statistieken van de standaard Azure-opslagaccounts te controleren. Dit is geen punt van zorg. Reden voor de invoering van de gegevens voor Standard Disk Storage-accounts was beperking van I /O's die vaak voorkwamen. Beheerde schijven voorkomen een dergelijke beperking door het aantal schijven in een opslagaccount te beperken. Daarom is het niet hebben van dat type van die gegevens niet van cruciaal belang.


#### <a name="linuxlogo_linux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Sommige Azure-prestatiemeteritems ontbreken

Prestatiestatistieken in Azure worden verzameld door een daemon, die gegevens uit verschillende bronnen haalt. Sommige configuratiegegevens worden lokaal verzameld en sommige prestatiestatistieken worden gelezen vanuit Azure Diagnostics. Opslagtellers zijn afkomstig van de logboeken in uw opslagabonnement.

Zie SAP Note [1999351,]met aanvullende informatie over probleemoplossing voor Azure Extension voor SAP, voor een volledige en up-to-date lijst met bekende problemen.

Als het probleem probleem niet wordt opgelost door het probleem op `Set-AzVMAEMExtension` te lossen door probleem op te lossen door sap noot [1999351] te gebruiken, voert u het configuratiescript zoals beschreven in [Configureer de Azure Extension for SAP][deployment-guide-4.5]opnieuw uit. Mogelijk moet u een uur wachten omdat opslaganalyses of diagnostische tellers mogelijk niet onmiddellijk worden gemaakt nadat ze zijn ingeschakeld. Als het probleem zich blijft voordoen, opent u een SAP-klantondersteuningsbericht op het onderdeel BC-OP-NT-AZR voor Windows of BC-OP-LNX-AZR voor een virtuele Linux-machine.
