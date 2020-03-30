---
title: SAP NetWeaver HA installeren op een Windows-failovercluster en gedeelde schijf voor een SAP ASCS/SCS-exemplaar in Azure | Microsoft Documenten
description: Meer informatie over het installeren van SAP NetWeaver HA op een Windows-failovercluster en gedeelde schijf voor een SAP ASCS/SCS-exemplaar.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e50733c843dfd21e35572f00fc6690e1e84aba97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279830"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>SAP NetWeaver HA installeren op een Windows-failovercluster en gedeelde schijf voor een SAP ASCS/SCS-exemplaar in Azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

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
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md



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

In dit artikel wordt beschreven hoe u een SAP-systeem met hoge beschikbaarheid in Azure installeert en configureert met behulp van een Windows Server-failovercluster en gedeelde clusterschijf voor het clusteren van een SAP ASCS/SCS-exemplaar.

## <a name="prerequisites"></a>Vereisten

Voordat u met de installatie begint, controleert u de volgende documenten:

* [Architectuurhandleiding: een SAP ASCS/SCS-exemplaar clusteren op een Windows-failovercluster met behulp van een gedeelde clusterschijf][sap-high-availability-guide-wsfc-shared-disk]

* [De Azure-infrastructuur voor SAP HA voorbereiden met behulp van een Windows-failovercluster en gedeelde schijf voor een SAP ASCS/SCS-exemplaar][sap-high-availability-infrastructure-wsfc-shared-disk]

We beschrijven de DBMS-setup in dit artikel niet omdat de instellingen variëren afhankelijk van het DBMS-systeem dat u gebruikt. We gaan ervan uit dat problemen met hoge beschikbaarheid bij de DBMS worden aangepakt met de functionaliteiten die verschillende DBMS-leveranciers voor Azure ondersteunen. Voorbeelden zijn AlwaysOn of database mirroring voor SQL Server en Oracle Data Guard voor Oracle-databases. In het scenario dat we in dit artikel gebruiken, voegen we niet meer bescherming toe aan de DBMS.

Er zijn geen speciale overwegingen wanneer verschillende DBMS-services werken met een geclusterde SAP ASCS- of SCS-configuratie in Azure.

> [!NOTE]
> De installatieprocedures van SAP NetWeaver ABAP-systemen, Java-systemen en ABAP+Java-systemen zijn vrijwel identiek. Het belangrijkste verschil is dat een SAP ABAP-systeem één ASCS-instantie heeft. Het SAP Java-systeem heeft één SCS-exemplaar. Het SAP ABAP+Java-systeem heeft één ASCS-instantie en één SCS-exemplaar dat wordt uitgevoerd in dezelfde Microsoft-failoverclustergroep. Eventuele installatieverschillen voor elke SAP NetWeaver-installatiestack worden expliciet vermeld. U ervan uitgaan dat alle andere onderdelen hetzelfde zijn.  
>
>

## <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>SAP installeren met een ASCS/SCS-exemplaar met hoge beschikbaarheid

> [!IMPORTANT]
> Zorg ervoor dat u uw paginabestand niet op gespiegelde volumes van SIOS DataKeeper plaatst. DataKeeper ondersteunt geen gespiegelde volumes. U uw paginabestand achterlaten op het tijdelijke station D van een virtuele Azure-machine, wat de standaardoptie is. Als het er nog niet is, verplaatst u het Windows-paginabestand naar station D van uw virtuele Azure-machine.
>
>

Het installeren van SAP met een ASCS/SCS-exemplaar met hoge beschikbaarheid omvat de volgende taken:

* Maak een virtuele hostnaam voor het geclusterde SAP ASCS/SCS-exemplaar.
* Installeer het eerste clusterknooppunt van SAP.
* Wijzig het SAP-profiel van de instantie ASCS/SCS.
* Voeg een sondepoort toe.
* Open de sondepoort van de Windows-firewall.

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Een virtuele hostnaam maken voor het geclusterde SAP ASCS/SCS-exemplaar

1. Maak in Windows DNS-beheer een DNS-vermelding voor de virtuele hostnaam van het ascs/SCS-exemplaar.

   > [!IMPORTANT]
   > Het IP-adres dat u aan de virtuele hostnaam van het ASCS/SCS-exemplaar toewijst, moet\<\>hetzelfde zijn als het IP-adres dat u hebt toegewezen aan Azure Load Balancer (SID -lb-ascs).  
   >
   >

   Het IP-adres van de virtuele SAP ASCS/SCS-hostnaam (pr1-ascs-sap) is hetzelfde als het IP-adres van Azure Load Balancer (pr1-lb-ascs).

   ![Figuur 1: De DNS-vermelding definiëren voor de virtuele naam en TCP/IP-adres van het SAP ASCS/SCS-cluster][sap-ha-guide-figure-3046]

   _**Figuur 1:** De DNS-vermelding definiëren voor de virtuele naam en TCP/IP-adres van het SAP ASCS/SCS-cluster_

2. Als u het IP-adres wilt definiëren dat is toegewezen aan de naam van de virtuele host, selecteert u **DNS-beheerdomein** > **Domain**.

   ![Figuur 2: Nieuwe virtuele naam en TCP/IP-adres voor SAP ASCS/SCS-clusterconfiguratie][sap-ha-guide-figure-3047]

   _**Figuur 2:** Nieuwe virtuele naam en TCP/IP-adres voor SAP ASCS/SCS-clusterconfiguratie_

### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Het eerste clusterknooppunt van SAP installeren

1. Voer de eerste clusterknooppuntoptie uit op clusterknooppunt A. Bijvoorbeeld op de pr1-ascs-0*host.
2. Als u de standaardpoorten voor de interne lastenbalancer van Azure wilt behouden, selecteert u:

   * **ABAP-systeem**: **ASCS-instantienummer** **00**
   * **Java-systeem**: **SCS-instantienummer** **01**
   * **ABAP+Java-systeem**: **ASCS-instantienummer** **00** en **SCS-instantienummer** **01**

   Als u andere instantienummers dan 00 wilt gebruiken voor de ABAP ASCS-instantie en 01 voor de Java SCS-instantie, wijzigt u eerst de standaardregels voor de standaardtaakvan de interne lastenafweging van Azure. Zie [De standaardregels voor taakverdeling van ASCS/SCS voor de interne load balancer van Azure wijzigen][sap-ha-guide-8.9]voor meer informatie.

De volgende taken worden niet beschreven in de standaard SAP-installatiedocumentatie.

> [!NOTE]
> De SAP-installatiedocumentatie beschrijft hoe u het eerste ASCS/SCS-clusterknooppunt installeert.
>
>

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Het SAP-profiel van het instantieASCS/SCS wijzigen

Voeg eerst een nieuwe profielparameter toe. De profielparameter voorkomt dat verbindingen tussen SAP-werkprocessen en de wachtrijserver worden gesloten wanneer deze te lang inactief zijn. We vermelden het probleemscenario in [Registervermeldingen toevoegen op beide clusterknooppunten van de instantie SAP ASCS/SCS.][sap-ha-guide-8.11] In die sectie introduceren we ook twee wijzigingen in een aantal basisparameters voor TCP/IP-verbindingen. In een tweede stap moet u de wachtrijserver `keep_alive` zo instellen dat een signaal wordt verzonden, zodat de verbindingen de niet-actieve drempeldrempel van de interne lastenbalancer van Azure niet raken.

Ga als lid van het SAP-profiel van het exemplaar ASCS/SCS:

1. Voeg deze profielparameter toe aan het SAP ASCS/SCS-instantieprofiel:

   ```
   enque/encni/set_so_keepalive = true
   ```
   In ons voorbeeld is het pad:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Bijvoorbeeld naar het SAP SCS-instantieprofiel en het bijbehorende pad:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Als u de wijzigingen wilt toepassen, start u het exemplaar SAP ASCS/SCS opnieuw.

### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Een sondepoort toevoegen

Gebruik de sondefunctionaliteit van de interne load balancer om de volledige clusterconfiguratie te laten werken met Azure Load Balancer. De interne load balancer van Azure verdeelt de binnenkomende werkbelasting meestal gelijk tussen deelnemende virtuele machines.

 Dit werkt echter niet in sommige clusterconfiguraties omdat slechts één exemplaar actief is. De andere instantie is passief en kan geen van de werkbelasting accepteren. Een sondefunctionaliteit helpt wanneer de interne load balancer van Azure alleen werk toewijst aan een actieve instantie. Met de sondefunctionaliteit kan de interne load balancer detecteren welke exemplaren actief zijn en vervolgens alleen de instantie met de werkbelasting targeten.

Ga als lid van het werk om een sondepoort toe te voegen:

1. Controleer de huidige **ProbePort-waarde** door de volgende PowerShell-opdracht uit te voeren:

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

   Voer de opdracht uit vanuit een van de virtuele machines in de clusterconfiguratie.

2. Definieer een sondepoort. Het standaardsondepoortnummer is 0. In ons voorbeeld gebruiken we sondepoort 62000.

   ![Figuur 3: De sondepoort van de clusterconfiguratie is standaard 0][sap-ha-guide-figure-3048]

   _**Figuur 3:** De standaardpoort voor clusterconfiguratiessonde is 0_

   Het poortnummer wordt gedefinieerd in SAP Azure Resource Manager-sjablonen. U het poortnummer toewijzen in PowerShell.

   Als u een nieuwe ProbePort-waarde wilt instellen voor de SAP \<SID\> IP-clusterbron, voert u het volgende PowerShell-script uit om de PowerShell-variabelen voor uw omgeving bij te werken:

   ```powershell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure internal load balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   Nadat u de \<\> SAP SID-clusterrol online hebt geplaatst, controleert u of **ProbePort** is ingesteld op de nieuwe waarde.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```
   Nadat het script is uitgevoerd, wordt u gevraagd de SAP-clustergroep opnieuw te starten om de wijzigingen te activeren.

   ![Figuur 4: De clusterpoort sonde nadat u de nieuwe waarde hebt ingesteld][sap-ha-guide-figure-3049]

   _**Figuur 4:** Sonde de clusterpoort nadat u de nieuwe waarde hebt ingesteld_

### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>De sondepoort van de Windows-firewall openen

Open een Windows firewall sondepoort op beide clusterknooppunten. Gebruik het volgende script om een Windows-firewallsondepoort te openen. Werk de PowerShell-variabelen voor uw omgeving bij.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** is ingesteld op **62000**. Nu hebt u toegang \\tot de bestandsshare \ascsha-clsap\sapmnt van andere hosts, zoals van ascsha-dbas.

## <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>De database-instantie installeren

Als u de database-instantie wilt installeren, volgt u het proces dat wordt beschreven in de SAP-installatiedocumentatie.

## <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>Het tweede clusterknooppunt installeren

Als u het tweede cluster wilt installeren, voert u de stappen uit die worden beschreven in de SAP-installatiehandleiding.

## <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Het starttype van het Windows-serviceexemplaar SAP ERS wijzigen

Wijzig het starttype van de SAP ERS Windows-service in **Automatisch (Vertraagde start)** op beide clusterknooppunten.

![Figuur 5: Het servicetype voor de SAP ERS-instantie wijzigen in vertraagde automatische][sap-ha-guide-figure-3050]

_**Figuur 5:** Het servicetype voor de SAP ERS-instantie wijzigen in vertraagde automatische_

## <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>De SAP Primary Application Server installeren

Installeer de SID-di-0 \<\>(Primary Application Server) instantie SID -di-0 op de virtuele machine die u hebt aangewezen om het PAS te hosten. Er zijn geen afhankelijkheden van Azure. Er zijn geen DataKeeper-specifieke instellingen.

## <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>De SAP Extra Application Server installeren

Installeer een SAP Additional Application Server (AAS) op alle virtuele machines die u hebt aangewezen om een SAP Application Server-exemplaar te hosten. Bijvoorbeeld \<op SID\>-di-1 \<\>naar SID&lt;&gt;-di- n .

> [!NOTE]
> Hiermee wordt de installatie van een SAP NetWeaver-systeem met hoge beschikbaarheid voltooid. Ga vervolgens verder met failovertesten.
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>De failover- en SIOS-replicatie van SAP ASCS/SCS-instantie testen
Het is eenvoudig om een SAP ASCS/SCS-instantiefailover en SIOS-schijfreplicatie te testen en te controleren met behulp van Failover Cluster Manager en het sios DataKeeper-beheer- en configuratiehulpprogramma.

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>SAP ASCS/SCS-exemplaar wordt uitgevoerd op clusterknooppunt A

De SAP PR1-clustergroep wordt uitgevoerd op clusterknooppunt A. Bijvoorbeeld op pr1-ascs-0. Wijs de gedeelde schijfsdrive S, die deel uitmaakt van de SAP PR1-clustergroep, toe aan clusterknooppunt A. De instantie ASCS/SCS maakt ook gebruik van diskdrive S. 

![Figuur 6: Failoverclusterbeheer: \<\> de SAP SID-clustergroep wordt uitgevoerd op clusterknooppunt A][sap-ha-guide-figure-5000]

_**Figuur 6:** Failoverclusterbeheer: de \<\> SAP SID-clustergroep wordt uitgevoerd op clusterknooppunt A_

In het hulpprogramma Beheer en configuratie van SIOS DataKeeper u zien dat de gedeelde schijfgegevens synchroon worden gerepliceerd van de bronvolumedrive S op clusterknooppunt A naar de doelvolumedrive S op clusterknooppunt B. Het wordt bijvoorbeeld gerepliceerd van pr1-ascs-0 [10.0.0.40] naar pr1-ascs-1 [10.0.0.41].

![Figuur 7: In SIOS DataKeeper het lokale volume repliceren van clusterknooppunt A naar clusterknooppunt B][sap-ha-guide-figure-5001]

_**Figuur 7:** In SIOS DataKeeper het lokale volume repliceren van clusterknooppunt A naar clusterknooppunt B_

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>Failover van knooppunt A naar knooppunt B

1. Kies een van deze opties om een \<\> failover van de SAP SID-clustergroep van clusterknooppunt A naar clusterknooppunt B te starten:
   - Failoverclusterbeheer  
   - Failovercluster PowerShell

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Start clusterknooppunt A opnieuw op in het Windows-gastbesturingssysteem. Dit initieert een automatische \<\> failover van de SAP SID-clustergroep van knooppunt A naar knooppunt B.  
3. Start clusterknooppunt A opnieuw op vanuit de Azure-portal. Dit initieert een automatische \<\> failover van de SAP SID-clustergroep van knooppunt A naar knooppunt B.  
4. Start clusterknooppunt A opnieuw met Azure PowerShell. Dit initieert een automatische \<\> failover van de SAP SID-clustergroep van knooppunt A naar knooppunt B.

   Na failover wordt \<\> de SAP SID-clustergroep uitgevoerd op clusterknooppunt B. Het draait bijvoorbeeld op pr1-ascs-1.

   ![Figuur 8: In Failoverclusterbeheer \<\> wordt de SAP SID-clustergroep uitgevoerd op clusterknooppunt B][sap-ha-guide-figure-5002]

   _**Figuur 8**: In Failoverclusterbeheer wordt de SAP \<SID-clustergroep\> uitgevoerd op clusterknooppunt B_

   De gedeelde schijf is nu gemonteerd op clusterknooppunt B. SIOS DataKeeper repliceert gegevens van bronvolumestation S op clusterknooppunt B om volumestation S op clusterknooppunt A te targeten. Het repliceert bijvoorbeeld van pr1-ascs-1 [10.0.0.41] naar pr1-ascs-0 [10.0.0.40].

   ![Figuur 9: SIOS DataKeeper repliceert het lokale volume van clusterknooppunt B naar clusterknooppunt A][sap-ha-guide-figure-5003]

   _**Figuur 9:** SIOS DataKeeper repliceert het lokale volume van clusterknooppunt B naar clusterknooppunt A_
