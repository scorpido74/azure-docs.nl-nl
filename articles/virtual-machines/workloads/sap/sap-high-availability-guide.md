---
title: Azure Virtual Machines hoge beschikbaarheid voor SAP NetWeaver | Microsoft Documenten
description: Handleiding voor hoge beschikbaarheid voor SAP NetWeaver op Azure Virtual Machines
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
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 65037ec0cc8b10b176622a7047beb7d912c7f701
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617550"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver"></a>Azure Virtual Machines hoge beschikbaarheid voor SAP NetWeaver

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



Azure Virtual Machines is de oplossing voor organisaties die reken-, opslag- en netwerkbronnen nodig hebben, in een minimale tijd en zonder lange inkoopcycli. U Azure Virtual Machines gebruiken om klassieke toepassingen te implementeren, zoals OP SAP NetWeaver gebaseerde ABAP, Java en een ABAP+Java-stack. Breid de betrouwbaarheid en beschikbaarheid uit zonder extra on-premises resources. Azure Virtual Machines ondersteunt cross-premises connectiviteit, zodat u Azure Virtual Machines integreren in de on-premises domeinen, private clouds en SAP-systeemlandschap van uw organisatie.

In dit artikel behandelen we de stappen die u nemen om SAP-systemen met hoge beschikbaarheid in Azure te implementeren met behulp van het Azure Resource Manager-implementatiemodel. Wij nemen u mee door deze belangrijke taken:

* Zoek de juiste SAP-notities en installatiehandleidingen, die worden vermeld in de sectie [Resources.][sap-ha-guide-2] Dit artikel vormt een aanvulling op sap-installatiedocumentatie en SAP Notes, de belangrijkste bronnen die u kunnen helpen bij het installeren en implementeren van SAP-software op specifieke platforms.
* Meer informatie over de verschillen tussen het Azure Resource Manager-implementatiemodel en het klassieke azure-implementatiemodel.
* Meer informatie over quorummodi voor failoverclustering van Windows Server, zodat u het model selecteren dat geschikt is voor uw Azure-implementatie.
* Meer informatie over gedeelde opslag voor Windows Server Failover Clustering in Azure-services.
* Meer informatie over het beschermen van single-point-of-failure componenten zoals Advanced Business Application Programming (ABAP) SAP Central Services (ASCS)/SAP Central Services (SCS) en database management systems (DBMS) en redundante componenten zoals SAP Application Server, in Azure.
* Volg een stapsgewijs voorbeeld van een installatie en configuratie van een SAP-systeem met hoge beschikbaarheid in een Windows Server Failover clusteringcluster in Azure met Azure Resource Manager.
* Meer informatie over aanvullende stappen die nodig zijn om Windows Server Failover Clustering in Azure te gebruiken, maar die niet nodig zijn in een on-premises implementatie.

Om de implementatie en configuratie te vereenvoudigen, gebruiken we in dit artikel de RESOURCE Manager-sjablonen met hoge beschikbaarheid met hoge beschikbaarheid met hoge beschikbaarheid met hoge niveaus. De sjablonen automatiseren de implementatie van de volledige infrastructuur die u nodig hebt voor een SAP-systeem met hoge beschikbaarheid. De infrastructuur ondersteunt ook SAP Application Performance Standard (SAPS) grootte van uw SAP-systeem.

## <a name="prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a>Voorwaarden
Zorg ervoor dat u voldoet aan de vereisten die in de volgende secties worden beschreven voordat u begint. Controleer ook alle bronnen die in de sectie [Resources][sap-ha-guide-2] worden vermeld.

In dit artikel gebruiken we Azure Resource [Manager-sjablonen voor SAP NetWeaver met drie lagen met beheerde schijven](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md/). Zie SAP Azure Resource [Manager-sjablonen](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)voor een handig overzicht van sjablonen.

## <a name="resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a>Middelen
Deze artikelen hebben betrekking op SAP-implementaties in Azure:

* [Azure Virtual Machines planning en implementatie voor SAP NetWeaver][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP NetWeaver][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP NetWeaver][dbms-guide]
* [Azure Virtual Machines hoge beschikbaarheid voor SAP NetWeaver (deze handleiding)][sap-ha-guide]

> [!NOTE]
> Waar mogelijk geven wij u een link naar de verwijzende SAP installatiegids (zie de [SAP installatiegidsen).][sap-installation-guides] Voor vereisten en informatie over het installatieproces is het een goed idee om de installatiehandleidingen van SAP NetWeaver zorgvuldig te lezen. In dit artikel worden alleen specifieke taken voor SAP NetWeaver-systemen behandeld die u gebruiken met Azure Virtual Machines.
>
>

Deze SAP Notes zijn gerelateerd aan het onderwerp SAP in Azure:

| Notitienummer | Titel |
| --- | --- |
| [1928533] |SAP-toepassingen op Azure: ondersteunde producten en grootte |
| [2015553] |SAP op Microsoft Azure: vereisten voor ondersteuning |
| [1999351] |Verbeterde Azure-controle voor SAP |
| [2178632] |Belangrijkste monitoringstatistieken voor SAP op Microsoft Azure |
| [1999351] |Virtualisatie op Windows: verbeterde controle |
| [2243692] |Gebruik van Azure Premium SSD-opslag voor SAP DBMS-instantie |

Meer informatie over de [beperkingen van Azure-abonnementen,][azure-resource-manager/management/azure-subscription-service-limits-subscription]inclusief algemene standaardbeperkingen en maximale beperkingen.

## <a name="high-availability-sap-with-azure-resource-manager-vs-the-azure-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Sap met hoge beschikbaarheid met Azure Resource Manager versus het klassieke Azure-implementatiemodel
De klassieke implementatiemodellen Azure Resource Manager en Azure zijn verschillend in de volgende gebieden:

- Resourcegroepen
- Azure internal load balancer afhankelijkheid van de Azure-resourcegroep
- Ondersteuning voor SAP multi-SID-scenario's

### <a name="resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a>Brongroepen
In Azure Resource Manager u resourcegroepen gebruiken om alle toepassingsbronnen in uw Azure-abonnement te beheren. Een geïntegreerde aanpak, in een resourcegroep, alle middelen hebben dezelfde levenscyclus. Alle bronnen worden bijvoorbeeld tegelijkertijd gemaakt en tegelijkertijd verwijderd. Meer informatie over [resourcegroepen](../../../azure-resource-manager/management/overview.md#resource-groups).

### <a name="azure-internal-load-balancer-dependency-on-the-azure-resource-group"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a>Azure internal load balancer afhankelijkheid van de Azure-resourcegroep

In het klassieke implementatiemodel azure is er een afhankelijkheid tussen de azure internal load balancer (Azure Load Balancer service) en de cloudservice. Elke interne load balancer heeft één cloudservice nodig.

In Azure Resource Manager moet elke Azure-bron in een Azure-brongroep worden geplaatst en dit geldt ook voor Azure Load Balancer. Er is echter geen noodzaak om één Azure-brongroep per Azure-load balancer te hebben, bijvoorbeeld één Azure-brongroep kan meerdere Azure Load Balancers bevatten. Het milieu is eenvoudiger en flexibeler. 

### <a name="support-for-sap-multi-sid-scenarios"></a>Ondersteuning voor SAP multi-SID-scenario's

In Azure Resource Manager u meerdere SID-systeem-id-instanties (SID) ASCS/SCS-exemplaren in één cluster installeren. Multi-SID-exemplaren zijn mogelijk vanwege ondersteuning voor meerdere IP-adressen voor elke interne laadbakvan Azure.

Als u het klassieke implementatiemodel azure wilt gebruiken, volgt u de procedures die zijn beschreven in [SAP NetWeaver in Azure: SAP ASCS/SCS-exemplaren clusteren met Windows Server Failover Clustering in Azure met SIOS DataKeeper](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> We raden u ten zeerste aan het Azure Resource Manager-implementatiemodel te gebruiken voor uw SAP-installaties. Het biedt veel voordelen die niet beschikbaar zijn in het klassieke implementatiemodel. Meer informatie over [Azure-implementatiemodellen][virtual-machines-azure-resource-manager-architecture-benefits-arm].   
>
>

## <a name="windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a>Windows Server Failover Clustering
Windows Server Failover Clustering is de basis van een SAP ASCS/SCS-installatie met hoge beschikbaarheid en DBMS in Windows.

Een failovercluster is een groep van 1+n onafhankelijke servers (knooppunten) die samenwerken om de beschikbaarheid van toepassingen en services te vergroten. Als er een knooppuntfout optreedt, berekent Windows Server Failover Clustering het aantal fouten dat kan optreden met behoud van een gezond cluster om toepassingen en services te leveren. U kiezen uit verschillende quorummodi om failoverclustering te bereiken.

### <a name="quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a>Quorummodi
U kiezen uit vier quorummodi wanneer u Windows Server Failover Clustering gebruikt:

* **Knooppunt Meerderheid**. Elk knooppunt van het cluster kan stemmen. Het cluster functioneert alleen met een meerderheid van de stemmen, dat wil zeggen met meer dan de helft van de stemmen. We raden deze optie aan voor clusters met een ongelijk aantal knooppunten. Drie knooppunten in een cluster met zeven knooppunten kunnen bijvoorbeeld mislukken en de clusterfoto's bereiken een meerderheid en blijven worden uitgevoerd.  
* **Knooppunt en schijfmeerderheid**. Elk knooppunt en een aangewezen schijf (een schijfgetuige) in de clusteropslag kunnen stemmen wanneer ze beschikbaar zijn en in communicatie. Het cluster functioneert alleen met een meerderheid van de stemmen, dat wil zeggen met meer dan de helft van de stemmen. Deze modus is zinvol in een clusteromgeving met een even aantal knooppunten. Als de helft van de knooppunten en de schijf online zijn, blijft het cluster in een gezonde staat.
* **Meerderheid voor knooppunt en bestandsaandeel**. Elk knooppunt plus een aangewezen bestandsshare (een getuige voor bestandsshare) die de beheerder maakt, kan stemmen, ongeacht of de knooppunten en bestandsshare beschikbaar zijn en in communicatie. Het cluster functioneert alleen met een meerderheid van de stemmen, dat wil zeggen met meer dan de helft van de stemmen. Deze modus is zinvol in een clusteromgeving met een even aantal knooppunten. Het is vergelijkbaar met de node en Disk Majority-modus, maar het maakt gebruik van een getuigenbestand aandeel in plaats van een getuige schijf. Deze modus is eenvoudig te implementeren, maar als het bestandsaandeel zelf niet beschikbaar is, kan het een single point of failure worden.
* **Geen meerderheid: alleen schijf**. Het cluster heeft een quorum als er één knooppunt beschikbaar is en in communicatie met een specifieke schijf in de clusteropslag. Alleen de knooppunten die ook met die schijf communiceren, kunnen lid worden van het cluster. We raden u aan deze modus niet te gebruiken.
 

## <a name="windows-server-failover-clustering-on-premises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a>On-premises clustering van Windows Server-failoverclustering
Figuur 1 toont een cluster van twee knooppunten. Als de netwerkverbinding tussen de knooppunten mislukt en beide knooppunten operationeel blijven, bepaalt een quorumschijf of bestandsshare welk knooppunt de toepassingen en services van het cluster blijft leveren. Het knooppunt dat toegang heeft tot de quorumschijf of bestandsshare is het knooppunt dat ervoor zorgt dat services worden voortgezet.

Omdat in dit voorbeeld een cluster met twee delen wordt gebruikt, gebruiken we de quorummodus Knooppunt en Bestandsmeerderheidsmeerderheid. De Node en Disk Majority is ook een geldige optie. In een productieomgeving raden we u aan een quorumschijf te gebruiken. U netwerk- en opslagsysteemtechnologie gebruiken om deze zeer beschikbaar te maken.

![Figuur 1: Voorbeeld van een Windows Server Failover clustering-configuratie voor SAP ASCS/SCS in Azure][sap-ha-guide-figure-1000]

_**Figuur 1:** Voorbeeld van een Windows Server Failover clustering-configuratie voor SAP ASCS/SCS in Azure_

### <a name="shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a>Gedeelde opslag
Figuur 1 toont ook een gedeeld opslagcluster met twee nodes. In een on-premises gedeeld opslagcluster detecteren alle knooppunten in het cluster gedeelde opslag. Een vergrendelingsmechanisme beschermt de gegevens tegen corruptie. Alle knooppunten kunnen detecteren of een ander knooppunt uitvalt. Als één knooppunt mislukt, wordt het resterende knooppunt eigenaar van de opslagresources en wordt de beschikbaarheid van services bepaald.

> [!NOTE]
> U hebt geen gedeelde schijven nodig voor hoge beschikbaarheid met sommige DBMS-toepassingen, zoals bij SQL Server. SQL Server Always On repliceert DBMS-gegevens en logboekbestanden van de lokale schijf van een clusterknooppunt naar de lokale schijf van een ander clusterknooppunt. In dat geval heeft de Windows-clusterconfiguratie geen gedeelde schijf nodig.
>
>

### <a name="networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a>Netwerk- en naamomzetting
Clientcomputers bereiken het cluster via een virtueel IP-adres en een virtuele hostnaam die de DNS-server biedt. De on-premises knooppunten en de DNS-server kunnen meerdere IP-adressen verwerken.

In een typische installatie gebruikt u twee of meer netwerkverbindingen:

* Een speciale verbinding met de opslag
* Een cluster-interne netwerkverbinding voor de hartslag
* Een openbaar netwerk dat clients gebruiken om verbinding te maken met het cluster

## <a name="windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a>Windows Server Failoverclustering in Azure
In vergelijking met bare metal- of private cloudimplementaties vereist Azure Virtual Machines extra stappen om Windows Server Failover Clustering te configureren. Wanneer u een gedeelde clusterschijf bouwt, moet u verschillende IP-adressen en virtuele hostnamen instellen voor het exemplaar SAP ASCS/SCS.

In dit artikel bespreken we de belangrijkste concepten en de extra stappen die nodig zijn om een centraal cluster voor centrale services met hoge beschikbaarheid van SAP in Azure te bouwen. We laten u zien hoe u de tool SIOS DataKeeper van derden instelt en hoe u de interne loadbalancer van Azure configureert. U deze hulpprogramma's gebruiken om een Windows-failovercluster te maken met een getuige voor bestandsshare in Azure.

![Figuur 2: Configuratie van failoverclustering van Windows Server in Azure zonder gedeelde schijf][sap-ha-guide-figure-1001]

_**Figuur 2:** Configuratie van Windows Server Failoverclustering in Azure zonder gedeelde schijf_

### <a name="shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a>Gedeelde schijf in Azure met SIOS DataKeeper
U hebt gedeelde clusteropslag nodig voor een SAP ASCS/SCS-exemplaar met hoge beschikbaarheid. Vanaf september 2016 biedt Azure geen gedeelde opslag die u gebruiken om een gedeeld opslagcluster te maken. U software SIOS DataKeeper Cluster Edition van derden gebruiken om een gespiegelde opslag te maken die gedeelde clusteropslag simuleert. De SIOS-oplossing biedt real-time synchrone gegevensreplicatie. Zo u een gedeelde schijfbron voor een cluster maken:

1. Voeg een extra schijf toe aan elk van de virtuele machines (VM's) in een Windows-clusterconfiguratie.
2. Voer SIOS DataKeeper Cluster Edition uit op beide virtuele machineknooppunten.
3. Configureer SIOS DataKeeper Cluster Edition zodat deze de inhoud van het extra schijfvolume van de virtuele bronmachine weerspiegelt op het extra schijfvolume van de beoogde virtuele machine. SIOS DataKeeper abstraheert de bron en target lokale volumes en presenteert deze vervolgens aan Windows Server Failover Clustering als één gedeelde schijf.

Meer informatie over [SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/).

![Figuur 3: Configuratie van windows server-failoverclustering in Azure met SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figuur 3:** Configuratie van Windows Server Failoverclustering in Azure met SIOS DataKeeper_

> [!NOTE]
> U hebt geen gedeelde schijven nodig voor hoge beschikbaarheid met sommige DBMS-producten, zoals SQL Server. SQL Server Always On repliceert DBMS-gegevens en logboekbestanden van de lokale schijf van een clusterknooppunt naar de lokale schijf van een ander clusterknooppunt. In dit geval heeft de Windows-clusterconfiguratie geen gedeelde schijf nodig.
>
>

### <a name="name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a>Naamomzetting in Azure
Het Azure-cloudplatform biedt niet de optie om virtuele IP-adressen te configureren, zoals zwevende IP-adressen. U hebt een alternatieve oplossing nodig om een virtueel IP-adres in te stellen om de clusterbron in de cloud te bereiken.
Azure heeft een interne load balancer in de Azure Load Balancer-service. Met de interne load balancer bereiken clients het cluster via het virtuele IP-adres van het cluster.
U moet de interne load balancer implementeren in de resourcegroep die de clusterknooppunten bevat. Configureer vervolgens alle benodigde poortdoorstuurregels met de sondepoortenpoorten van de interne load balancer.
De clients kunnen verbinding maken via de naam van de virtuele host. De DNS-server lost het IP-adres van het cluster op en de interne load balancer verwerkt poortdoorsturen naar het actieve knooppunt van het cluster.

## <a name="sap-netweaver-high-availability-in-azure-infrastructure-as-a-service-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a>SAP NetWeaver hoge beschikbaarheid in Azure Infrastructure-as-a-Service (IaaS)
Om de hoge beschikbaarheid van SAP-toepassingen te bereiken, zoals voor SAP-softwarecomponenten, moet u de volgende componenten beschermen:

* SAP Application Server-exemplaar
* SAP ASCS/SCS-exemplaar
* DBMS-server

Zie Azure Virtual Machines planning en implementatie voor [SAP NetWeaver voor][planning-guide-11]meer informatie over het beveiligen van SAP-componenten in scenario's met hoge beschikbaarheid.

### <a name="high-availability-sap-application-server"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a>Sap-toepassingsserver met hoge beschikbaarheid
U hebt meestal geen specifieke oplossing met hoge beschikbaarheid nodig voor de SAP Application Server en dialoogvensters. U bereikt een hoge beschikbaarheid door redundantie en u configureert meerdere dialoogvensters in verschillende exemplaren van Azure Virtual Machines. U moet ten minste twee SAP-toepassingsexemplaren hebben geïnstalleerd in twee exemplaren van Azure Virtual Machines.

![Figuur 4: SAP-toepassingsserver met hoge beschikbaarheid][sap-ha-guide-figure-2000]

_**Figuur 4:** Sap-toepassingsserver met hoge beschikbaarheid_

U moet alle virtuele machines die SAP Application Server-exemplaren hosten, in dezelfde Azure-beschikbaarheidsset plaatsen. Een Azure-beschikbaarheidsset zorgt ervoor dat:

* Alle virtuele machines maken deel uit van hetzelfde upgradedomein. Een upgradedomein zorgt er bijvoorbeeld voor dat de virtuele machines niet tegelijkertijd worden bijgewerkt tijdens geplande onderhoudsdowntime.
* Alle virtuele machines maken deel uit van hetzelfde storingsdomein. Een foutdomein zorgt er bijvoorbeeld voor dat virtuele machines worden geïmplementeerd, zodat geen enkel storingspunt van invloed is op de beschikbaarheid van alle virtuele machines.

Meer informatie over het [beheren van de beschikbaarheid van virtuele machines][virtual-machines-manage-availability].

Alleen niet-beheerde schijf: omdat het Azure-opslagaccount een mogelijk single point of failure is, is het belangrijk om ten minste twee Azure-opslagaccounts te hebben, waarin ten minste twee virtuele machines worden gedistribueerd. In een ideale installatie worden de schijven van elke virtuele machine waarop een SAP-dialoogvenster wordt uitgevoerd, geïmplementeerd in een ander opslagaccount.

### <a name="high-availability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a>SAP ASCS/SCS-exemplaar met hoge beschikbaarheid
Figuur 5 is een voorbeeld van een SAP ASCS/SCS-exemplaar met hoge beschikbaarheid.

![Figuur 5: SAP ASCS/SCS-exemplaar met hoge beschikbaarheid][sap-ha-guide-figure-2001]

_**Figuur 5:** SAP ASCS/SCS-exemplaar met hoge beschikbaarheid_

#### <a name="sap-ascsscs-instance-high-availability-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a>SAP ASCS/SCS-instantie hoge beschikbaarheid met Windows Server Failover Clustering in Azure
In vergelijking met bare metal- of private cloudimplementaties vereist Azure Virtual Machines extra stappen om Windows Server Failover Clustering te configureren. Als u een Windows-failovercluster wilt bouwen, hebt u een gedeelde clusterschijf, verschillende IP-adressen, verschillende virtuele hostnamen en een interne laadbalansr van Azure nodig voor het clusteren van een SAP ASCS/SCS-exemplaar. We bespreken dit in meer detail later in het artikel.

![Figuur 6: Windows Server Failoverclustering voor een SAP ASCS/SCS-configuratie in Azure met Behulp van SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figuur 6:** Windows Server Failoverclustering voor een SAP ASCS/SCS-configuratie in Azure met SIOS DataKeeper_

### <a name="high-availability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>DbMS-exemplaar met hoge beschikbaarheid
De DBMS is ook een enkel aanspreekpunt in een SAP-systeem. U moet het beschermen door een oplossing met hoge beschikbaarheid te gebruiken. Figuur 7 toont een SQL Server Always On high-availability solution in Azure, met Windows Server Failover Clustering en de interne load balancer van Azure. SQL Server Always On repliceert DBMS-gegevens en logbestanden met behulp van een eigen DBMS-replicatie. In dit geval hebt u geen gedeelde clusterschijven nodig, wat de hele installatie vereenvoudigt.

![Figuur 7: Voorbeeld van een SAP DBMS met hoge beschikbaarheid, met SQL Server Always On][sap-ha-guide-figure-2003]

_**Figuur 7:** Voorbeeld van een SAP DBMS met hoge beschikbaarheid, met SQL Server Always On_

Zie de volgende artikelen voor meer informatie over het clusteren van SQL Server in Azure met behulp van het Azure Resource Manager-implementatiemodel:

* [Altijd beschikbaarheidconfigureren in Azure Virtual Machines handmatig met Behulp van Resourcebeheer] [virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Een azure internal load balancer configureren voor een groep Always On beschikbaarheid in Azure] [virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="end-to-end-high-availability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a>End-to-end implementatiescenario's met hoge beschikbaarheid

### <a name="deployment-scenario-using-architectural-template-1"></a>Implementatiescenario met architecturale sjabloon 1

Figuur 8 toont een voorbeeld van een SAP NetWeaver-architectuur met hoge beschikbaarheid in Azure voor **één** SAP-systeem. Dit scenario is als volgt ingesteld:

- Voor het exemplaar SAP ASCS/SCS wordt één speciaal cluster gebruikt.
- Voor het DBMS-exemplaar wordt één speciaal cluster gebruikt.
- SAP Application Server-exemplaren worden geïmplementeerd in hun eigen speciale VM's.

![Figuur 8: SAP architecturale sjabloon 1 met hoge beschikbaarheid, met speciaal cluster voor ASCS/SCS en DBMS][sap-ha-guide-figure-2004]

_**Figuur 8:** SAP architecturale sjabloon 1 met hoge beschikbaarheid, speciale clusters voor ASCS/SCS en DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Implementatiescenario met architecturale sjabloon 2

Figuur 9 toont een voorbeeld van een SAP NetWeaver-architectuur met hoge beschikbaarheid in Azure voor **één** SAP-systeem. Dit scenario is als volgt ingesteld:

- Voor zowel de SAP ASCS/SCS-instantie als het DBMS wordt **één** speciaal cluster gebruikt.
- SAP Application Server-exemplaren worden geïmplementeerd in eigen speciale VM's.

![Figuur 9: SAP architecturale sjabloon 2 met hoge beschikbaarheid, met een speciaal cluster voor ASCS/SCS en een speciaal cluster voor DBMS][sap-ha-guide-figure-2005]

_**Figuur 9:** SAP architecturale sjabloon 2 met hoge beschikbaarheid, met een speciaal cluster voor ASCS/SCS en een speciaal cluster voor DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Implementatiescenario met architecturale sjabloon 3

Figuur 10 toont een voorbeeld van een SAP NetWeaver-architectuur met hoge&gt; beschikbaarheid &lt;in&gt;Azure voor **twee** SAP-systemen, met &lt;SID1 en SID2. Dit scenario is als volgt ingesteld:

- Voor **zowel** de SAP ASCS/SCS SID1-instantie *als* de SAP ASCS/SCS SID2-instantie (één cluster) wordt één speciaal cluster gebruikt.
- Een speciaal cluster wordt gebruikt voor DBMS SID1 en een ander speciaal cluster wordt gebruikt voor DBMS SID2 (twee clusters).
- SAP Application Server-exemplaren voor het SAP-systeem SID1 hebben hun eigen speciale VM's.
- SAP Application Server-exemplaren voor het SAP-systeem SID2 hebben hun eigen speciale VM's.

![Figuur 10: SAP architecturale sjabloon 3 met hoge beschikbaarheid, met een speciaal cluster voor verschillende ASCS/SCS-exemplaren][sap-ha-guide-figure-6003]

_**Figuur 10:** SAP architecturale sjabloon 3 met hoge beschikbaarheid, met een speciaal cluster voor verschillende ASCS/SCS-exemplaren_

## <a name="prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a>De infrastructuur voorbereiden

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>De infrastructuur voorbereiden op architectuursjabloon 1
Azure Resource Manager-sjablonen voor SAP helpen de implementatie van vereiste resources te vereenvoudigen.

De drielaagse sjablonen in Azure Resource Manager ondersteunen ook scenario's met hoge beschikbaarheid, zoals in Architectural Template 1, dat twee clusters heeft. Elk cluster is een SAP single point of failure voor SAP ASCS/SCS en DBMS.

Hier u Azure Resource Manager-sjablonen krijgen voor het voorbeeldscenario dat we in dit artikel beschrijven:

* [Azure Marketplace-afbeelding](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Azure Marketplace-afbeelding met beheerde schijven](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Aangepaste afbeelding](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Aangepaste afbeelding met beheerde schijven](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

De infrastructuur voorbereiden op architecturale sjabloon 1:

- Selecteer **HA**in de Azure-portal op het blad **Parameters** in het vak **SYSTEEMBESCHIKBAARHEID.**

  ![Figuur 11: Azure Resource Manager-parameters met hoge beschikbaarheid instellen][sap-ha-guide-figure-3000]

_**Figuur 11:** Azure Resource Manager-parameters met hoge beschikbaarheid instellen_


  De sjablonen maken:

  * **Virtuele machines**:
    * VIRTUELE MACHINES VAN SAP Application Server: <*SAPSystemSID*>-di-<-nummer *Number*>
    * VIRTUELE MACHINES van ASCS/SCS-cluster: <*SAPSystemSID*>-ascs-<-nummer *Number*>
    * DBMS-cluster: <*SAPSystemSID*>-db-<-nummer *Number*>

  * **Netwerkkaarten voor alle virtuele machines, met bijbehorende IP-adressen:**
    * <*SAPSystemSID*>-nic-di-<-nummer *Number*>
    * <*SAPSystemSID*>-nic-ascs-<*Nummer*>
    * <*SAPSystemSID*>-nic-db-<-nummer *Number*>

  * **Azure-opslagaccounts (alleen niet-beheerde schijven)**

  * **Beschikbaarheidsgroepen** voor:
    * SAP Application Server virtuele machines: <*SAPSystemSID*>-avset-di
    * SAP ASCS/SCS cluster virtuele machines: <*SAPSystemSID*>-avset-ascs
    * DBMS cluster virtuele machines: <*SAPSystemSID*>-avset-db

  * **Azure interne load balancer:**
    * Met alle poorten voor het ASCS/SCS-exemplaar en het IP-adres <*SAPSystemSID*>-lb-ascs
    * Met alle poorten voor de SQL Server DBMS en IP-adres <*SAPSystemSID*>-lb-db

  * **Netwerkbeveiligingsgroep**: <*SAPSystemSID*>-nsg-ascs-0  
    * Met een open externe RDP-poort (Remote Desktop Protocol) naar de <*SAPSystemSID*>-ascs-0 virtuele machine

> [!NOTE]
> Alle IP-adressen van de netwerkkaarten en Azure internal load balancers zijn standaard **dynamisch.** Wijzig ze in **statische** IP-adressen. We beschrijven hoe dit later in het artikel te doen.
>
>

### <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Implementeer virtuele machines met bedrijfsnetwerkconnectiviteit (cross-premises) om te gebruiken in de productie
Voor de productie van SAP-systemen implementeert u virtuele Azure-machines met bedrijfsnetwerkconnectiviteit met behulp van Azure Site-to-Site VPN of Azure ExpressRoute.

> [!NOTE]
> U uw Azure Virtual Network-exemplaar gebruiken. Het virtuele netwerk en subnet zijn al gemaakt en voorbereid.
>
>

1. Selecteer in de Azure-portal op het blad **Parameters** in het vak **NEWOREXISTINGSUBNET** **bestaande**.
2. Voeg in het vak **SUBNETID** de volledige tekenreeks van uw voorbereide Azure-netwerkSubnetID toe waar u van plan bent uw virtuele Azure-machines te implementeren.
3. Voer de opdracht PowerShell uit om een lijst met alle Azure-netwerksubnetten te krijgen:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   In het veld **ID** wordt de **SUBNETID**weergegeven.
4. Voer de opdracht PowerShell uit om een lijst met alle **SUBNETID-waarden** te krijgen:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   De **SUBNETID** ziet er als volgt uit:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Cloud-only SAP-exemplaren implementeren voor test en demo
U uw SAP-systeem met hoge beschikbaarheid implementeren in een implementatiemodel voor alleen de cloud. Dit soort implementatie is vooral handig voor demo- en testusecases. Het is niet geschikt voor productie use cases.

- Selecteer in de Azure-portal op het blad **Parameters** in het vak **NEWOREXISTINGSUBNET** de optie **nieuw**. Laat het **SUBNETID-veld** leeg.

  Met de sjabloon SAP Azure Resource Manager wordt automatisch het virtuele Azure-netwerk en subnet gemaakt.

> [!NOTE]
> U moet ook ten minste één speciale virtuele machine implementeren voor Active Directory en DNS in hetzelfde Azure Virtual Network-exemplaar. De sjabloon maakt deze virtuele machines niet.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>De infrastructuur voorbereiden op architecturale sjabloon 2

U deze Azure Resource Manager-sjabloon voor SAP gebruiken om de implementatie van vereiste infrastructuurbronnen voor SAP-architecturale sjabloon 2 te vereenvoudigen.

Hier u Azure Resource Manager-sjablonen voor dit implementatiescenario krijgen:

* [Azure Marketplace-afbeelding](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Azure Marketplace-afbeelding met beheerde schijven](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Aangepaste afbeelding](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Aangepaste afbeelding met beheerde schijven](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>De infrastructuur voorbereiden op architectuursjabloon 3

U de infrastructuur voorbereiden en SAP configureren voor **multi-SID.** U bijvoorbeeld een extra SAP ASCS/SCS-exemplaar toevoegen aan een *bestaande* clusterconfiguratie. Zie [Een extra SAP ASCS/SCS-exemplaar configureren in een bestaande clusterconfiguratie om een SAP multi-SID-configuratie te maken in Azure Resource Manager][sap-ha-multi-sid-guide]voor meer informatie.

Als u een nieuw multi-SID-cluster wilt maken, u de multi-SID [quickstart-sjablonen op GitHub](https://github.com/Azure/azure-quickstart-templates)gebruiken.
Als u een nieuw multi-SID-cluster wilt maken, moet u de volgende drie sjablonen implementeren:

* [ASCS/SCS-sjabloon](#ASCS-SCS-template)
* [Databasesjabloon](#database-template)
* [Sjabloon toepassingsservers](#application-servers-template)

In de volgende secties vindt u meer informatie over de sjablonen en de parameters die u in de sjablonen moet opgeven.

#### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a>ASCS/SCS-sjabloon

De ASCS/SCS-sjabloon implementeert twee virtuele machines die u gebruiken om een failovercluster van Windows Server te maken dat meerdere ASCS/SCS-exemplaren host.

Als u de multi-SID-sjabloon ASCS/SCS wilt instellen, voert u in de [ASCS/SCS multi-SID-sjabloon][sap-templates-3-tier-multisid-xscs-marketplace-image] of [ASCS/SCS multi-SID-sjabloon met managed disks][sap-templates-3-tier-multisid-xscs-marketplace-image-md]waarden in voor de volgende parameters:

  - **Bronvoorvoegsel**.  Stel het bronvoorvoegsel in, dat wordt gebruikt om alle resources die tijdens de implementatie zijn gemaakt, vooraf aan te maken. Omdat de resources niet tot slechts één SAP-systeem behoren, is het voorvoegsel van de bron niet de sid van één SAP-systeem.  Het voorvoegsel moet tussen **de drie en zes tekens bevatten.**
  - **Stapeltype**. Selecteer het stacktype van het SAP-systeem. Afhankelijk van het stacktype heeft Azure Load Balancer één (alleen ABAP- of Java- of Java-) of twee (ABAP+Java) privé-IP-adressen per SAP-systeem.
  -  **OS-type**. Selecteer het besturingssysteem van de virtuele machines.
  -  **SAP-systeem tellen**. Selecteer het aantal SAP-systemen dat u in dit cluster wilt installeren.
  -  **Beschikbaarheid van het systeem**. Selecteer **HA**.
  -  **Gebruikersnaam en beheerderswachtwoord voor beheerders**. Maak een nieuwe gebruiker die kan worden gebruikt om zich aan te melden bij de machine.
  -  **Nieuw of bestaand subnet**. Stel in of een nieuw virtueel netwerk en subnet moet worden gemaakt of dat een bestaand subnet moet worden gebruikt. Als u al een virtueel netwerk hebt dat is verbonden met uw on-premises netwerk, selecteert u **bestaande**.
  -  **Subnet-id**. Als u de VM wilt implementeren in een bestaand VNet waarbij een subnet is gedefinieerd waaraan de VM moet worden toegewezen, geeft u de id van dat specifieke subnet een naam. De ID ziet er meestal als volgt uit: /subscriptions/<*subscription id*>/resourceGroups/<resource *group name*>/providers/Microsoft.Network/virtualNetworks/<virtuele *netwerknaam*>/subnets/<*subnetnaam*>

De sjabloon implementeert één Azure Load Balancer-instantie, die meerdere SAP-systemen ondersteunt.

- De ASCS-exemplaren zijn geconfigureerd voor bijvoorbeeld nummer 00, 10, 20...
- De SCS-exemplaren zijn geconfigureerd voor bijvoorbeeld nummer 01, 11, 21...
- De exemplaren VAN DE ASCS Enqueue Replication Server (ERS) (alleen Linux) zijn geconfigureerd voor bijvoorbeeld nummer 02, 12, 22...
- De SCS ERS (alleen Linux) exemplaren zijn geconfigureerd voor bijvoorbeeld nummer 03, 13, 23...

De load balancer bevat 1 (2 voor Linux) VIP(s), 1x VIP voor ASCS/SCS en 1x VIP voor ERS (alleen Linux).

De volgende lijst bevat alle regels voor het balanceren van de lasten (waarbij x het nummer van het SAP-systeem is, bijvoorbeeld 1, 2, 3...):
- Windows-specifieke poorten voor elk SAP-systeem: 445, 5985
- ASCS-poorten (instantienummer x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS-poorten (instantienummer x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- ASCS ERS poorten op Linux (instantie nummer x2): 33x2, 5x213, 5x214, 5x216
- SCS ERS poorten op Linux (instantie nummer x3): 33x3, 5x313, 5x314, 5x316

De load balancer is geconfigureerd om de volgende sondepoortenpoorten te gebruiken (waarbij x het nummer is van het SAP-systeem, bijvoorbeeld 1, 2, 3...):
- ASCS/SCS interne load balancer sonde poort: 620x0
- ERS interne load balancer probe port (alleen Linux): 621x2

#### <a name="database-template"></a><a name="database-template"></a>Databasesjabloon

De databasesjabloon implementeert een of twee virtuele machines die u gebruiken om het relationele databasebeheersysteem (RDBMS) voor één SAP-systeem te installeren. Als u bijvoorbeeld een ASCS/SCS-sjabloon implementeert voor vijf SAP-systemen, moet u deze sjabloon vijf keer implementeren.

Voer waarden in voor het instellen van de multi-SID-sjabloon voor de database in de [database multi-SID-sjabloon][sap-templates-3-tier-multisid-db-marketplace-image] of [database multi-SID-sjabloon met beheerde schijven:][sap-templates-3-tier-multisid-db-marketplace-image-md]

- **Sap-systeem-id**. Voer de SAP-systeem-ID in van het SAP-systeem dat u wilt installeren. De ID wordt gebruikt als voorvoegsel voor de resources die worden geïmplementeerd.
- **Os Type**. Selecteer het besturingssysteem van de virtuele machines.
- **Dbtype**. Selecteer het type database dat u op het cluster wilt installeren. Selecteer **SQL** als u Microsoft SQL Server wilt installeren. Selecteer **HANA** als u SAP HANA op de virtuele machines wilt installeren. Selecteer het juiste type besturingssysteem: selecteer **Windows** voor SQL en selecteer een Linux-distributie voor HANA. De Azure Load Balancer die is verbonden met de virtuele machines, wordt geconfigureerd om het geselecteerde databasetype te ondersteunen:
  * **SQL**. De load balancer zal load-balance poort 1433. Zorg ervoor dat u deze poort gebruikt voor uw SQL Server Always On setup.
  * **HANA**. De load balancer zal load-balance poorten 35015 en 35017. Zorg ervoor dat sap HANA met instantienummer **50**wordt geïnstalleerd.
  De load balancer zal sondepoort 62550 gebruiken.
- **Sap-systeemgrootte**. Stel het aantal SAPS in dat het nieuwe systeem zal bieden. Als u niet zeker weet hoeveel SAPS het systeem nodig heeft, vraag het dan aan uw SAP Technology Partner of System Integrator.
- **Beschikbaarheid van het systeem**. Selecteer **HA**.
- **Gebruikersnaam en beheerderswachtwoord voor beheerders**. Maak een nieuwe gebruiker die kan worden gebruikt om zich aan te melden bij de machine.
- **Subnet-id**. Voer de id in van het subnet dat u hebt gebruikt tijdens de implementatie van de ASCS/SCS-sjabloon of de ID van het subnet dat is gemaakt als onderdeel van de implementatie van de ASCS/SCS-sjabloon.

#### <a name="application-servers-template"></a><a name="application-servers-template"></a>Sjabloon toepassingsservers

De sjabloon toepassingsservers implementeert twee of meer virtuele machines die kunnen worden gebruikt als SAP Application Server-exemplaren voor één SAP-systeem. Als u bijvoorbeeld een ASCS/SCS-sjabloon implementeert voor vijf SAP-systemen, moet u deze sjabloon vijf keer implementeren.

Als u de multi-SID-sjabloon voor toepassingsservers wilt instellen, voert u in de [multi-SID-sjabloon voor toepassingsservers][sap-templates-3-tier-multisid-apps-marketplace-image] of [multi-SID-sjabloon voor toepassingsservers met beheerde schijven][sap-templates-3-tier-multisid-apps-marketplace-image-md]waarden in voor de volgende parameters:

  -  **Sap-systeem-id**. Voer de SAP-systeem-ID in van het SAP-systeem dat u wilt installeren. De ID wordt gebruikt als voorvoegsel voor de resources die worden geïmplementeerd.
  -  **Os Type**. Selecteer het besturingssysteem van de virtuele machines.
  -  **Sap-systeemgrootte**. Het aantal SAPS dat het nieuwe systeem zal leveren. Als u niet zeker weet hoeveel SAPS het systeem nodig heeft, vraag het dan aan uw SAP Technology Partner of System Integrator.
  -  **Beschikbaarheid van het systeem**. Selecteer **HA**.
  -  **Gebruikersnaam en beheerderswachtwoord voor beheerders**. Maak een nieuwe gebruiker die kan worden gebruikt om zich aan te melden bij de machine.
  -  **Subnet-id**. Voer de id in van het subnet dat u hebt gebruikt tijdens de implementatie van de ASCS/SCS-sjabloon of de ID van het subnet dat is gemaakt als onderdeel van de implementatie van de ASCS/SCS-sjabloon.


### <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Virtueel Azure-netwerk
In ons voorbeeld is de adresruimte van het virtuele Azure-netwerk 10.0.0.0/16. Er is een subnet genaamd **Subnet**, met een adresbereik van 10.0.0.0/24. Alle virtuele machines en interne load balancers worden ingezet in dit virtuele netwerk.

> [!IMPORTANT]
> Breng geen wijzigingen aan in de netwerkinstellingen in het gastbesturingssysteem. Dit omvat IP-adressen, DNS-servers en subnet. Configureer al uw netwerkinstellingen in Azure. De DHCP-service (Dynamic Host Configuration Protocol) geeft uw instellingen door.
>
>

### <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>DNS-IP-adressen

Als u de vereiste DNS-IP-adressen wilt instellen, neemt u de volgende stappen uit.

1. Controleer in de Azure-portal op het **DNS-serversblad** of de optie **dns-servers** van uw virtuele netwerk is ingesteld op **Aangepaste DNS.**
2. Selecteer uw instellingen op basis van het type netwerk dat u hebt. Zie de volgende bronnen voor meer informatie:
   * Voeg de IP-adressen van de on-premises DNS-servers toe.  
   U on-premises DNS-servers uitbreiden naar de virtuele machines die in Azure worden uitgevoerd. In dat scenario u de IP-adressen toevoegen van de virtuele Azure-machines waarop u de DNS-service uitvoert.
   * Voor VM-implementaties geïsoleerd in Azure: implementeer een extra virtuele machine in dezelfde instantie van het virtuele netwerk die als DNS-server fungeert. Voeg de IP-adressen toe van de virtuele Azure-machines die u hebt ingesteld om de DNS-service uit te voeren.

   ![Figuur 12: DNS-servers configureren voor Azure Virtual Network][sap-ha-guide-figure-3001]

   _**Figuur 12:** DNS-servers configureren voor Azure Virtual Network_

   > [!NOTE]
   > Als u de IP-adressen van de DNS-servers wijzigt, moet u de virtuele Azure-machines opnieuw starten om de wijziging toe te passen en de nieuwe DNS-servers te verspreiden.
   >
   >

In ons voorbeeld wordt de DNS-service geïnstalleerd en geconfigureerd op deze virtuele Windows-machines:

| Rol virtuele machine | Naam virtuele machinehost | Naam van netwerkkaart | Statisch IP-adres |
| --- | --- | --- | --- |
| Eerste DNS-server |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Tweede DNS-server |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Hostnamen en statische IP-adressen voor de geclusterde SAP ASCS/SCS-instantie en de geclusterde DBMS-instantie

Voor on-premises implementatie hebt u deze gereserveerde hostnamen en IP-adressen nodig:

| Rol virtuele hostnaam | Virtuele hostnaam | Virtueel statisch IP-adres |
| --- | --- | --- |
| SAP ASCS/SCS eerste cluster virtuele hostnaam (voor clusterbeheer) |pr1-ascs-vir |10.0.0.42 |
| Virtuele hostnaam SAP ASCS/SCS-instantie |pr1-ascs-sap |10.0.0.43 |
| SAP DBMS tweede cluster virtuele hostnaam (clusterbeheer) |pr1-dbms-vir |10.0.0.32 |

Wanneer u het cluster maakt, maakt u de virtuele hostnamen **pr1-ascs-vir** en **pr1-dbms-vir** en de bijbehorende IP-adressen die het cluster zelf beheren. Zie [Clusterknooppunten verzamelen in een clusterconfiguratie][sap-ha-guide-8.12.1]voor informatie over hoe u dit doen.

U handmatig de andere twee virtuele hostnamen, **pr1-ascs-sap** en **pr1-dbms-sap,** en de bijbehorende IP-adressen, op de DNS-server maken. De geclusterde SAP ASCS/SCS-instantie en de geclusterde DBMS-instantie gebruiken deze bronnen. Zie [Een virtuele hostnaam maken voor een geclusterd SAP ASCS/SCS-exemplaar voor][sap-ha-guide-9.1.1]informatie over hoe u dit doen.

### <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Statische IP-adressen instellen voor de virtuele SAP-machines
Nadat u de virtuele machines hebt geïmplementeerd om in uw cluster te gebruiken, moet u statische IP-adressen instellen voor alle virtuele machines. Doe dit in de Azure Virtual Network-configuratie en niet in het gastbesturingssysteem.

1. Selecteer in de Azure-portal > **IP-adres** **brongroepnetwerkkaartinstellingen** > **Network Card** > **Settings**.
2. Selecteer **Statisch**op het **blad IP-adressen** onder **Toewijzing**. Voer in het vak **IP-adres** het IP-adres in dat u wilt gebruiken.

   > [!NOTE]
   > Als u het IP-adres van de netwerkkaart wijzigt, moet u de virtuele Azure-machines opnieuw starten om de wijziging toe te passen.  
   >
   >

   ![Figuur 13: Statische IP-adressen instellen voor de netwerkkaart van elke virtuele machine][sap-ha-guide-figure-3002]

   _**Figuur 13:** Statische IP-adressen instellen voor de netwerkkaart van elke virtuele machine_

   Herhaal deze stap voor alle netwerkinterfaces, dat wil zeggen voor alle virtuele machines, inclusief virtuele machines die u wilt gebruiken voor uw Active Directory/DNS-service.

In ons voorbeeld hebben we deze virtuele machines en statische IP-adressen:

| Rol virtuele machine | Naam virtuele machinehost | Naam van netwerkkaart | Statisch IP-adres |
| --- | --- | --- | --- |
| Eerste SAP Application Server-instantie |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Tweede EXEMPLAAR SAP Application Server |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Laatste EXEMPLAAR SAP Application Server |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Eerste clusterknooppunt voor ASCS/SCS-exemplaar |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Tweede clusterknooppunt voor ASCS/SCS-exemplaar |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Eerste clusterknooppunt voor DBMS-exemplaar |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Tweede clusterknooppunt voor DBMS-exemplaar |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Een statisch IP-adres instellen voor de interne loadbalancer van Azure

Met de sjabloon SAP Azure Resource Manager wordt een azure internal load balancer gemaakt die wordt gebruikt voor het SAP ASCS/SCS-instantiecluster en het DBMS-cluster.

> [!IMPORTANT]
> Het IP-adres van de virtuele hostnaam van de SAP ASCS/SCS is hetzelfde als het IP-adres van de interne load balancer van SAP ASCS/SCS: **pr1-lb-ascs**.
> Het IP-adres van de virtuele naam van de DBMS is hetzelfde als het IP-adres van de interne load balancer van DBMS: **pr1-lb-dbms**.
>
>

Ga als lid van het nieuwe IP-adres van de interne lastenbalans van Azure:

1. Bij de eerste implementatie wordt het IP-adres van de interne load balancer ingesteld op **Dynamic**. Selecteer **Statisch**in de Azure-portal op het **ip-adresblad** onder **Toewijzing**.
2. Stel het IP-adres van de interne load balancer **pr1-lb-ascs** in op het IP-adres van de virtuele hostnaam van het exemplaar SAP ASCS/SCS.
3. Stel het IP-adres van de interne load balancer **pr1-lb-dbms** in op het IP-adres van de virtuele hostnaam van de DBMS-instantie.

   ![Figuur 14: Statische IP-adressen instellen voor de interne load balancer voor de instantie SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Figuur 14:** Statische IP-adressen instellen voor de interne load balancer voor de instantie SAP ASCS/SCS_

In ons voorbeeld hebben we twee azure interne load balancers die deze statische IP-adressen hebben:

| Azure interne load balancer-rol | Naam van Azure-interne lastenbalans | Statisch IP-adres |
| --- | --- | --- |
| Interne load balancer van SAP ASCS/SCS-instantie |pr1-lb-ascs |10.0.0.43 |
| SAP DBMS interne load balancer |pr1-lb-dbms |10.0.0.33 |


### <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Standaardregels voor ASCS/SCS-taakverdeling voor de interne loadbalancer van Azure

Met de sjabloon SAP Azure Resource Manager worden de poorten gemaakt die u nodig hebt:
* Een ABAP ASCS-instantie, met het standaardinstantienummer **00**
* Een Java SCS-exemplaar, met het standaardinstantienummer **01**

Wanneer u uw SAP ASCS/SCS-exemplaar installeert, moet u het standaardinstantienummer **00** gebruiken voor uw ABAP ASCS-exemplaar en het standaardexemplaarnummer **01** voor uw Java SCS-exemplaar.

Maak vervolgens de vereiste interne taakverdelingseindpunten voor de SAP NetWeaver-poorten.

Als u vereiste interne taakverdelingseindpunten wilt maken, maakt u eerst deze eindpunten voor het balanceren van de lasten voor de SAP NetWeaver ABAP ASCS-poorten:

| Regelnaam service/taakverdeling | Standaardpoortnummers | Betonpoorten voor (ASCS-instantie met instantienummer 00) (ERS met 10) |
| --- | --- | --- |
| Server in enqueue / *lbrule3200* |32<*InstanceNumber*> |3200 |
| ABAP Message Server / *lbrule3600* |36<*InstanceNumber*> |3600 |
| Intern ABAP-bericht / *lbrule3900* |39<*InstanceNumber*> |3900 |
| Message Server HTTP / *Lbrule8100* |81<*instancenumber*> |8100 |
| SAP Start Service ASCS HTTP / *Lbrule50013* |5<*InstanceNumber*>13 |50013 |
| SAP Start Service ASCS HTTPS / *Lbrule50014* |5<*InstanceNumber*>14 |50014 |
| Replicatie indewachtrij / *Lbrule50016* |5<*InstanceNumber*>16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5<*InstanceNumber*>13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5<*InstanceNumber*>14 |51014 |
| Win RM *Lbrule5985* | |5985 |
| File Share *Lbrule445* | |445 |

_**Tabel 1:** Poortnummers van de SAP NetWeaver ABAP ASCS-exemplaren_

Maak vervolgens deze eindpunten voor het balanceren van de lastenvoor de SAP NetWeaver Java SCS-poorten:

| Regelnaam service/taakverdeling | Standaardpoortnummers | Betonpoorten voor (SCS-exemplaar met instantienummer 01) (ERS met 11) |
| --- | --- | --- |
| Server in enqueue / *lbrule3201* |32<*InstanceNumber*> |3201 |
| Gateway Server / *lbrule3301* |33<*InstanceNumber*> |3301 |
| Java Message Server / *lbrule3900* |39<*InstanceNumber*> |3901 |
| Message Server HTTP / *Lbrule8101* |81<*instancenumber*> |8101 |
| SAP Start Service SCS HTTP / *Lbrule50113* |5<*InstanceNumber*>13 |50113 |
| SAP Start Service SCS HTTPS / *Lbrule50114* |5<*InstanceNumber*>14 |50114 |
| Replicatie indewachtrij / *Lbrule50116* |5<*InstanceNumber*>16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5<*InstanceNumber*>13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5<*InstanceNumber*>14 |51114 |
| Win RM *Lbrule5985* | |5985 |
| File Share *Lbrule445* | |445 |

_**Tabel 2:** Poortnummers van de SAP NetWeaver Java SCS-exemplaren_

![Figuur 15: Standaardregels voor ascs/SCS-taakverdeling voor de interne loadbalancer van Azure][sap-ha-guide-figure-3004]

_**Figuur 15:** Standaardregels voor ASCS/SCS-taakverdeling voor de interne loadbalancer van Azure_

Stel het IP-adres van de load balancer **pr1-lb-dbms** in op het IP-adres van de virtuele hostnaam van het DBMS-exemplaar.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>De standaardregels voor taakverdeling van ASCS/SCS wijzigen voor de interne loadbalancer van Azure

Als u verschillende nummers wilt gebruiken voor de SAP ASCS- of SCS-exemplaren, moet u de namen en waarden van hun poorten wijzigen van standaardwaarden.

1. Selecteer SID ** < *SID*>-lb-ascs load balancer** > **Load Balancing Rules**in de Azure-portal.
2. Wijzig de volgende waarden voor alle regels voor het balanceren van de taak die deel uitmaken van de SAP ASCS- of SCS-instantie:

   * Name
   * Poort
   * Back-endpoort

   Als u bijvoorbeeld het standaard ASCS-instantienummer wilt wijzigen van 00 naar 31, moet u de wijzigingen aanbrengen voor alle poorten die in tabel 1 worden vermeld.

   Hier is een voorbeeld van een update voor *poortlbrule3200*.

   ![Figuur 16: De standaardregels voor taakverdeling van ASCS/SCS wijzigen voor de interne loadbalancer van Azure][sap-ha-guide-figure-3005]

   _**Figuur 16:** De standaardregels voor taakverdeling van ASCS/SCS wijzigen voor de interne loadbalancer van Azure_

### <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Virtuele Windows-machines toevoegen aan het domein

Nadat u een statisch IP-adres aan de virtuele machines hebt toegewezen, voegt u de virtuele machines toe aan het domein.

![Figuur 17: Een virtuele machine toevoegen aan een domein][sap-ha-guide-figure-3006]

_**Figuur 17:** Een virtuele machine toevoegen aan een domein_

### <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Registervermeldingen toevoegen op beide clusterknooppunten van de instantie SAP ASCS/SCS

Azure Load Balancer heeft een interne load balancer die verbindingen sluit wanneer de verbindingen niet actief zijn voor een bepaalde periode (een niet-actieve time-out). SAP-werkprocessen in dialoogvensters openen verbindingen met het SAP-wachtrijproces zodra de eerste aanvraag voor wachtrij/wachtrij moet worden verzonden. Deze verbindingen blijven meestal tot stand totdat het werkproces of het wachtrijproces opnieuw wordt opgestart. Als de verbinding echter gedurende een bepaalde periode niet actief is, worden de verbindingen met de interne lastenbalancer van Azure gesloten. Dit is geen probleem omdat het SAP-werkproces de verbinding met het wachtrijproces opnieuw vaststelt als deze niet meer bestaat. Deze activiteiten worden gedocumenteerd in de ontwikkelaarssporen van SAP-processen, maar ze creëren een grote hoeveelheid extra inhoud in die traces. Het is een goed idee om `KeepAliveTime` de `KeepAliveInterval` TCP/IP en op beide clusterknooppunten te wijzigen. Combineer deze wijzigingen in de TCP/IP-parameters met SAP-profielparameters, die later in het artikel worden beschreven.

Als u registervermeldingen wilt toevoegen op beide clusterknooppunten van de instantie SAP ASCS/SCS, voegt u eerst deze Windows-registervermeldingen toe op beide Windows-clusterknooppunten voor SAP ASCS/SCS:

| Pad | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Naam van de variabele |`KeepAliveTime` |
| Variabel type |REG_DWORD (Decimaal) |
| Waarde |120000 |
| Koppeling naar documentatie |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

_**Tabel 3:** De eerste PARAMETER TCP/IP wijzigen_

Voeg vervolgens deze Windows-registervermeldingen toe aan beide Windows-clusterknooppunten voor SAP ASCS/SCS:

| Pad | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Naam van de variabele |`KeepAliveInterval` |
| Variabel type |REG_DWORD (Decimaal) |
| Waarde |120000 |
| Koppeling naar documentatie |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

_**Tabel 4:** De tweede PARAMETER TCP/IP wijzigen_

**Als u de wijzigingen wilt toepassen, start u beide clusterknooppunten opnieuw**.

### <a name="set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Een Clusteringcluster voor Failover van Windows Server instellen voor een SAP ASCS/SCS-exemplaar

Het instellen van een Windows Server Failover clustering cluster voor een SAP ASCS/SCS-exemplaar omvat de volgende taken:

- De clusterknooppunten verzamelen in een clusterconfiguratie
- Een clusterbestandsshare-getuige configureren

#### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>De clusterknooppunten verzamelen in een clusterconfiguratie

1. Voeg in de wizard Rol en onderdelen toevoegen failoverclustering toe aan beide clusterknooppunten.
2. Stel het failovercluster in met Failoverclusterbeheer. Selecteer in Failoverclusterbeheer **clusterbeheer**en voeg vervolgens alleen de naam van het eerste cluster, knooppunt A, toe. Voeg het tweede knooppunt nog niet toe; u voegt het tweede knooppunt in een latere stap toe.

   ![Figuur 18: De naam van de server of virtuele machine van het eerste clusterknooppunt toevoegen][sap-ha-guide-figure-3007]

   _**Figuur 18:** De naam van de server of virtuele machine van het eerste clusterknooppunt toevoegen_

3. Voer de netwerknaam (virtuele hostnaam) van het cluster in.

   ![Figuur 19: Voer de clusternaam in][sap-ha-guide-figure-3008]

   _**Figuur 19:** Voer de clusternaam in_

4. Nadat u het cluster hebt gemaakt, voert u een clustervalidatietest uit.

   ![Figuur 20: De clustervalidatiecontrole uitvoeren][sap-ha-guide-figure-3009]

   _**Figuur 20:** De clustervalidatiecontrole uitvoeren_

   U waarschuwingen over schijven op dit punt in het proces negeren. U voegt later een getuige voor bestandsshare toe en de gedeelde SIOS-schijven. In dit stadium hoeft u zich geen zorgen te maken over het hebben van een quorum.

   ![Figuur 21: Er wordt geen quorumschijf gevonden][sap-ha-guide-figure-3010]

   _**Figuur 21:** Er wordt geen quorumschijf gevonden_

   ![Figuur 22: Kernclusterbron heeft een nieuw IP-adres nodig][sap-ha-guide-figure-3011]

   _**Figuur 22:** Kernclusterbron heeft een nieuw IP-adres nodig_

5. Wijzig het IP-adres van de kernclusterservice. Het cluster kan pas worden gestart als u het IP-adres van de kernclusterservice wijzigt, omdat het IP-adres van de server naar een van de knooppunten voor virtuele machines verwijst. Doe dit op de pagina **Eigenschappen** van de IP-bron van de kernclusterservice.

   We moeten bijvoorbeeld een IP-adres (in ons voorbeeld **10.0.0.42)** toewijzen voor de virtuele hostnaam van het cluster **pr1-ascs-vir.**

   ![Figuur 23: Wijzig in het dialoogvenster Eigenschappen het IP-adres][sap-ha-guide-figure-3012]

   _**Figuur 23:** Wijzig het IP-adres in het dialoogvenster **Eigenschappen**_

   ![Figuur 24: Het IP-adres toewijzen dat is gereserveerd voor het cluster][sap-ha-guide-figure-3013]

   _**Figuur 24:** Het IP-adres toewijzen dat is gereserveerd voor het cluster_

6. Breng de naam van de virtuele host van het cluster online.

   ![Figuur 25: Clustercore-service is operationeel en met het juiste IP-adres][sap-ha-guide-figure-3014]

   _**Figuur 25:** Clustercore-service is operationeel en met het juiste IP-adres_

7. Voeg het tweede clusterknooppunt toe.

   Nu de kernclusterservice operationeel is, u het tweede clusterknooppunt toevoegen.

   ![Figuur 26: Het tweede clusterknooppunt toevoegen][sap-ha-guide-figure-3015]

   _**Figuur 26:** Het tweede clusterknooppunt toevoegen_

8. Voer een naam in voor de status van het tweede clusterknooppunt.

   ![Figuur 27: Voer de hostnaam van het tweede clusterknooppunt in][sap-ha-guide-figure-3016]

   _**Figuur 27:** De naam van de hostnaam van het tweede clusterknooppunt invoeren_

   > [!IMPORTANT]
   > Zorg ervoor dat het selectievakje **Alle in aanmerking komende opslag toevoegen aan het cluster** **NIET** is ingeschakeld.  
   >
   >

   ![Figuur 28: Schakel het selectievakje niet in][sap-ha-guide-figure-3017]

   _**Figuur 28:** Schakel het selectievakje **niet** in_

   U waarschuwingen over quorum en schijven negeren. U stelt het quorum in en deelt de schijf later, zoals beschreven [in het installeren van SIOS DataKeeper Cluster Edition voor SAP ASCS/SCS-clustershareschijf.][sap-ha-guide-8.12.3]

   ![Figuur 29: Waarschuwingen over het schijfquorum negeren][sap-ha-guide-figure-3018]

   _**Figuur 29:** Waarschuwingen over het schijfquorum negeren_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Een clusterbestandsshare-getuige configureren

Het configureren van een clusterbestandssharegetuige omvat de volgende taken:

- Een bestandsshare maken
- Het quorum van de bestandsshare-getuige instellen in Failoverclusterbeheer

##### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Een bestandsshare maken

1. Selecteer een getuige voor bestandsshare in plaats van een quorumschijf. SIOS DataKeeper ondersteunt deze optie.

   In de voorbeelden in dit artikel bevindt de getuige voor bestandsshare zich op de Active Directory/DNS-server die in Azure wordt uitgevoerd. Het dossier aandeel getuige heet **domcontr-0**. Omdat u een VPN-verbinding met Azure zou hebben geconfigureerd (via Site-to-Site VPN of Azure ExpressRoute), is uw Active Directory/DNS-service on-premises en is het niet geschikt om een getuige voor bestandsshare uit te voeren.

   > [!NOTE]
   > Als uw Active Directory/DNS-service alleen on-premises wordt uitgevoerd, configureert u de getuige voor bestandsshare niet op het Active Directory/DNS Windows-besturingssysteem dat on-premises wordt uitgevoerd. De netwerklatentie tussen clusterknooppunten die in Azure worden uitgevoerd en Active Directory/DNS on-premises zijn mogelijk te groot en veroorzaken verbindingsproblemen. Zorg ervoor dat u de getuige voor bestandsshare configureert op een virtuele Azure-machine die dicht bij het clusterknooppunt wordt uitgevoerd.  
   >
   >

   Het quorumstation heeft minstens 1.024 MB vrije ruimte nodig. We raden 2.048 MB vrije ruimte aan voor het quorumstation.

2. Voeg het object clusternaam toe.

   ![Figuur 30: De machtigingen voor het aandeel toewijzen voor het clusternaamobject][sap-ha-guide-figure-3019]

   _**Figuur 30:** De machtigingen voor de share toewijzen voor het clusternaamobject_

   Zorg ervoor dat de machtigingen de bevoegdheid bevatten om gegevens in het aandeel voor het clusternaamobject te wijzigen (in ons voorbeeld **pr1-ascs-vir$**).

3. Als u het object clusternaam aan de lijst wilt toevoegen, selecteert u **Toevoegen**. Wijzig het filter om te controleren op computerobjecten, naast de objecten in figuur 31.

   ![Figuur 31: De objecttypen wijzigen om computers op te nemen][sap-ha-guide-figure-3020]

   _**Figuur 31:** De objecttypen wijzigen om computers op te nemen_

   ![Figuur 32: Schakel het selectievakje Computers in][sap-ha-guide-figure-3021]

   _**Figuur 32:** Schakel het selectievakje **Computers** in_

4. Voer het clusternaamobject in zoals weergegeven in figuur 31. Omdat de record al is gemaakt, u de machtigingen wijzigen, zoals weergegeven in figuur 30.

5. Selecteer het tabblad **Beveiliging** van het aandeel en stel vervolgens meer gedetailleerde machtigingen in voor het clusternaamobject.

   ![Figuur 33: De beveiligingskenmerken instellen voor het clusternaamobject in het quorum voor bestandsdelen][sap-ha-guide-figure-3022]

   _**Figuur 33:** De beveiligingskenmerken instellen voor het clusternaamobject in het quorum voor bestandsshare_

##### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Het quorum van de bestandsshare-getuige instellen in Failoverclusterbeheer

1. Open de wizard Quoruminstelling configureren.

   ![Figuur 34: De wizard Clusterquoruminstelling configureren starten][sap-ha-guide-figure-3023]

   _**Figuur 34:** De wizard Clusterquoruminstelling configureren starten_

2. Selecteer op de pagina **Quorumconfiguratie selecteren** de optie **De quorumgetuige selecteren**.

   ![Figuur 35: Quorumconfiguraties waaruit u kiezen][sap-ha-guide-figure-3024]

   _**Figuur 35:** Quorumconfiguraties waaruit u kiezen_

3. Selecteer op de pagina **Quorumgetuige selecteren** de optie **Een getuige voor bestandsshare configureren**.

   ![Figuur 36: Selecteer de getuige van het bestandsdelen][sap-ha-guide-figure-3025]

   _**Figuur 36:** De getuige voor bestandsshare selecteren_

4. Voer het UNC-pad naar de bestandsshare in (in ons voorbeeld \\domcontr-0\FSW). Als u een lijst wilt zien met de wijzigingen die u aanbrengen, selecteert u **Volgende**.

   ![Figuur 37: De locatie van de bestandsshare voor het getuigenaandeel definiëren][sap-ha-guide-figure-3026]

   _**Figuur 37:** De locatie voor bestandsshare voor het getuigenaandeel definiëren_

5. Selecteer de gewenste wijzigingen en selecteer **Volgende**. U moet de clusterconfiguratie opnieuw configureren zoals weergegeven in figuur 38.  

   ![Figuur 38: Bevestiging dat u het cluster opnieuw hebt geconfigureerd][sap-ha-guide-figure-3027]

   _**Figuur 38:** Bevestiging dat u het cluster opnieuw hebt geconfigureerd_

Nadat het Windows Failovercluster is geïnstalleerd, moeten er wijzigingen worden aangebracht in een aantal drempelwaarden om failoverdetectie aan te passen aan de omstandigheden in Azure. De te wijzigen parameters zijn gedocumenteerd https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/ in deze blog: . Ervan uitgaande dat uw twee VM's die de Windows-clusterconfiguratie voor ASCS/SCS bouwen, zich in hetzelfde subnet bevinden, moeten de volgende parameters in deze waarden worden gewijzigd:
- SamesubnetDelay = 2
- SameSubNetThreshold = 15

Deze instellingen werden getest met klanten en op voorwaarde dat een goed compromis veerkrachtig genoeg aan de ene kant. Aan de andere kant die instellingen waren het verstrekken van snel genoeg failover in echte fout omstandigheden op SAP-software of node / VM-storing. 

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>SIOS DataKeeper Cluster Edition installeren voor de SCHIJF VOOR het delen van SAP ASCS/SCS-clusterdelen

U hebt nu een werkende Windows Server Failover Clustering-configuratie in Azure. Maar om een SAP ASCS/SCS-exemplaar te installeren, hebt u een gedeelde schijfbron nodig. U de gedeelde schijfbronnen die u nodig hebt in Azure niet maken. SIOS DataKeeper Cluster Edition is een oplossing van derden die u gebruiken om bronnen voor gedeelde schijven te maken.

Het installeren van SIOS DataKeeper Cluster Edition voor de SAP ASCS/SCS-clustershareschijf omvat de volgende taken:

- Het .NET Framework 3.5 toevoegen
- SIOS DataKeeper installeren
- SIOS DataKeeper instellen

#### <a name="add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Voeg het .NET Framework 3.5 toe
Het Microsoft .NET Framework 3.5 wordt niet automatisch geactiveerd of geïnstalleerd op Windows Server 2012 R2. Omdat SIOS DataKeeper vereist dat het .NET Framework zich op alle knooppunten bevindt waarop u DataKeeper installeert, moet u het .NET Framework 3.5 installeren op het gastbesturingssysteem van alle virtuele machines in het cluster.

Er zijn twee manieren om het .NET Framework 3.5 toe te voegen:

- Gebruik de wizard Rollen en onderdelen toevoegen in Windows zoals weergegeven in figuur 39.

  ![Figuur 39: Installeer het .NET Framework 3.5 met de wizard Rollen en functies toevoegen][sap-ha-guide-figure-3028]

  _**Figuur 39:** De .NET Framework 3.5 installeren met de wizard Rollen en functies toevoegen_

  ![Figuur 40: Voortgangsbalk installeren wanneer u de .NET Framework 3.5 installeert met de wizard Rollen en functies toevoegen][sap-ha-guide-figure-3029]

  _**Figuur 40:** Voortgangsbalk voor installatie wanneer u de .NET Framework 3.5 installeert met de wizard Rollen en functies toevoegen_

- Gebruik het opdrachtregelgereedschap dism.exe. Voor dit type installatie moet u toegang krijgen tot de SxS-map op de Windows-installatiemedia. Typ bij een opdrachtprompt met verhoogde bevoegdheid:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a>SIOS DataKeeper installeren

Installeer SIOS DataKeeper Cluster Edition op elk knooppunt in het cluster. Als u virtuele gedeelde opslag wilt maken met SIOS DataKeeper, maakt u een gesynchroniseerde spiegel en simuleert u gedeelde clusteropslag.

Voordat u de SIOS-software installeert, maakt u de domeingebruiker **DataKeeperSvc.**

> [!NOTE]
> Voeg de **DataKeeperSvc-gebruiker** toe aan de groep **Lokale beheerder** op beide clusterknooppunten.
>
>

SIOS DataKeeper installeren:

1. Installeer de SIOS-software op beide clusterknooppunten.

   ![SIOS-installateur][sap-ha-guide-figure-3030]

   ![Figuur 41: Eerste pagina van de Installatie van SIOS DataKeeper][sap-ha-guide-figure-3031]

   _**Figuur 41:** Eerste pagina van de Installatie van SIOS DataKeeper_

2. Selecteer **ja**in het dialoogvenster in figuur 42.

   ![Figuur 42: DataKeeper informeert u dat een service wordt uitgeschakeld][sap-ha-guide-figure-3032]

   _**Figuur 42:** DataKeeper informeert u dat een service wordt uitgeschakeld_

3. In het dialoogvenster in figuur 43 raden we u aan **domein- of serveraccount te**selecteren.

   ![Figuur 43: Gebruikersselectie voor SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**Figuur 43:** Gebruikersselectie voor SIOS DataKeeper_

4. Voer de gebruikersnaam en wachtwoorden van het domeinaccount in die u hebt gemaakt voor SIOS DataKeeper.

   ![Figuur 44: Voer de domeinnaam en het wachtwoord voor de Installatie van SIOS DataKeeper in][sap-ha-guide-figure-3034]

   _**Figuur 44:** Voer de domeinnaam en het wachtwoord van het domein in voor de installatie Van SIOS DataKeeper_

5. Installeer de licentiesleutel voor uw SIOS DataKeeper-exemplaar zoals weergegeven in figuur 45.

   ![Figuur 45: Voer uw SIOS DataKeeper-licentiesleutel in][sap-ha-guide-figure-3035]

   _**Figuur 45:** Voer uw SIOS DataKeeper-licentiesleutel in_

6. Start de virtuele machine opnieuw op wanneer u daarom wordt gevraagd.

#### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>SIOS DataKeeper instellen

Nadat u SIOS DataKeeper op beide knooppunten hebt geïnstalleerd, moet u de configuratie starten. Het doel van de configuratie is om synchrone gegevensreplicatie te hebben tussen de extra schijven die aan elk van de virtuele machines zijn gekoppeld.

1. Start het gereedschap Beheer en configuratie van DataKeeper en selecteer **Vervolgens Server verbinden**. (In figuur 46 is deze optie rood omcirkeld.)

   ![Figuur 46: SIOS DataKeeper-beheer- en configuratietool][sap-ha-guide-figure-3036]

   _**Figuur 46:** SIOS DataKeeper-beheer- en configuratietool_

2. Voer de naam of het TCP/IP-adres van het eerste knooppunt in waarmee het gereedschap Beheer en configuratie verbinding moet maken en in een tweede stap het tweede knooppunt.

   ![Figuur 47: De naam of het TCP/IP-adres van het eerste knooppunt invoegen waarmee het gereedschap Beheer en configuratie verbinding moet maken en in een tweede stap het tweede knooppunt][sap-ha-guide-figure-3037]

   _**Figuur 47:** De naam of het TCP/IP-adres van het eerste knooppunt invoegen, het gereedschap Beheer en configuratie moet verbinding maken met en in een tweede stap het tweede knooppunt_

3. Maak de replicatietaak tussen de twee knooppunten.

   ![Figuur 48: Een replicatietaak maken][sap-ha-guide-figure-3038]

   _**Figuur 48:** Een replicatietaak maken_

   Een wizard begeleidt u bij het maken van een replicatietaak.
4. Definieer de naam, het TCP/IP-adres en het schijfvolume van het bronknooppunt.

   ![Figuur 49: De naam van de replicatietaak definiëren][sap-ha-guide-figure-3039]

   _**Figuur 49:** De naam van de replicatietaak definiëren_

   ![Figuur 50: Definieer de basisgegevens voor het knooppunt, dat het huidige bronknooppunt moet zijn][sap-ha-guide-figure-3040]

   _**Figuur 50:** Definieer de basisgegevens voor het knooppunt, dat het huidige bronknooppunt moet zijn_

5. Definieer de naam, het TCP/IP-adres en het schijfvolume van het doelknooppunt.

   ![Figuur 51: Definieer de basisgegevens voor het knooppunt, dat het huidige doelknooppunt moet zijn][sap-ha-guide-figure-3041]

   _**Figuur 51:** Definieer de basisgegevens voor het knooppunt, dat het huidige doelknooppunt moet zijn_

6. Definieer de compressiealgoritmen. In ons voorbeeld raden we u aan de replicatiestream te comprimeren. Vooral in resynchronisatiesituaties vermindert de compressie van de replicatiestroom de synchronisatietijd aanzienlijk. Houd er rekening mee dat compressie gebruik maakt van de CPU- en RAM-bronnen van een virtuele machine. Naarmate de compressiesnelheid toeneemt, neemt ook het volume van de gebruikte CPU-bronnen toe. U deze instelling ook later aanpassen.

7. Een andere instelling die u moet controleren, is of de replicatie asynchroon of synchroon plaatsvindt. *Wanneer u SAP ASCS/SCS-configuraties beschermt, moet u synchrone replicatie gebruiken.*  

   ![Figuur 52: replicatiedetails definiëren][sap-ha-guide-figure-3042]

   _**Figuur 52:** Replicatiedetails definiëren_

8. Definieer of het volume dat door de replicatietaak wordt gerepliceerd, moet worden weergegeven in een clusterconfiguratie van Windows Server Failover clustering als een gedeelde schijf. Selecteer Voor de SAP ASCS/SCS-configuratie **Ja,** zodat het Windows-cluster het gerepliceerde volume ziet als een gedeelde schijf die het als clustervolume kan gebruiken.

   ![Figuur 53: Ja selecteren om het gerepliceerde volume in te stellen als clustervolume][sap-ha-guide-figure-3043]

   _**Figuur 53:** Ja **Yes** selecteren om het gerepliceerde volume in te stellen als clustervolume_

   Nadat het volume is gemaakt, geeft het hulpprogramma Gegevenskeeperbeheer en -configuratie aan dat de replicatietaak actief is.

   ![Figuur 54: DataKeeper synchrone spiegeling voor de SAP ASCS/SCS-sharedisk is actief][sap-ha-guide-figure-3044]

   _**Figuur 54:** DataKeeper synchrone mirroring voor de SAP ASCS/SCS share disk is actief_

   Failoverclusterbeheer toont de schijf nu als een DataKeeper-schijf, zoals weergegeven in figuur 55.

   ![Figuur 55: Failoverclusterbeheer toont de schijf die DataKeeper heeft gerepliceerd][sap-ha-guide-figure-3045]

   _**Figuur 55:** Failoverclusterbeheer toont de schijf die DataKeeper heeft gerepliceerd_

## <a name="install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a>Het SAP NetWeaver-systeem installeren

We beschrijven de DBMS-setup niet omdat de instellingen variëren afhankelijk van het DBMS-systeem dat u gebruikt. We gaan er echter van uit dat problemen met hoge beschikbaarheid bij de DBMS worden aangepakt met de functionaliteiten die de verschillende DBMS-leveranciers voor Azure ondersteunen. Bijvoorbeeld Always On of database mirroring voor SQL Server en Oracle Data Guard voor Oracle-databases. In het scenario dat we in dit artikel gebruiken, hebben we niet meer bescherming toegevoegd aan de DBMS.

Er zijn geen speciale overwegingen wanneer verschillende DBMS-services communiceren met dit soort geclusterde SAP ASCS/SCS-configuratie in Azure.

> [!NOTE]
> De installatieprocedures van SAP NetWeaver ABAP-systemen, Java-systemen en ABAP+Java-systemen zijn vrijwel identiek. Het belangrijkste verschil is dat een SAP ABAP-systeem één ASCS-instantie heeft. Het SAP Java-systeem heeft één SCS-exemplaar. Het SAP ABAP+Java-systeem heeft één ASCS-instantie en één SCS-exemplaar dat wordt uitgevoerd in dezelfde Microsoft-failoverclustergroep. Eventuele installatieverschillen voor elke SAP NetWeaver-installatiestack worden expliciet vermeld. U ervan uitgaan dat alle andere onderdelen hetzelfde zijn.  
>
>

### <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>SAP installeren met een ASCS/SCS-exemplaar met hoge beschikbaarheid

> [!IMPORTANT]
> Zorg ervoor dat u uw paginabestand niet op gespiegelde volumes van DataKeeper plaatst. DataKeeper ondersteunt geen gespiegelde volumes. U uw paginabestand achterlaten op het tijdelijke station D van een virtuele Azure-machine, wat de standaardoptie is. Als het er nog niet is, verplaatst u het Windows-paginabestand naar station D: van uw virtuele Azure-machine.
>
>

Het installeren van SAP met een ASCS/SCS-exemplaar met hoge beschikbaarheid omvat de volgende taken:

- Een virtuele hostnaam maken voor het geclusterde SAP ASCS/SCS-exemplaar
- Het eerste clusterknooppunt van SAP installeren
- Het SAP-profiel van het instantieASCS/SCS wijzigen
- Een sondepoort toevoegen
- De sondepoort van de Windows-firewall openen

#### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Een virtuele hostnaam maken voor het geclusterde SAP ASCS/SCS-exemplaar

1. Maak in Windows DNS-beheer een DNS-vermelding voor de virtuele hostnaam van het ascs/SCS-exemplaar.

   > [!IMPORTANT]
   > Het IP-adres dat u aan de virtuele hostnaam van het ASCS/SCS-exemplaar toewijst, moet hetzelfde zijn als het IP-adres dat u hebt toegewezen aan Azure Load Balancer**<*(SID*>-lb-ascs).**  
   >
   >

   Het IP-adres van de virtuele SAP ASCS/SCS-hostnaam **(pr1-ascs-sap)** is hetzelfde als het IP-adres van Azure Load Balancer **(pr1-lb-ascs).**

   ![Figuur 56: De DNS-vermelding definiëren voor de virtuele naam en tcp/IP-adres van het SAP ASCS/SCS-cluster][sap-ha-guide-figure-3046]

   _**Figuur 56:** De DNS-vermelding definiëren voor de virtuele naam en TCP/IP-adres van het SAP ASCS/SCS-cluster_

2. Als u het IP-adres wilt definiëren dat is toegewezen aan de naam van de virtuele host, selecteert u **DNS-beheerdomein** > **Domain**.

   ![Figuur 57: Nieuwe virtuele naam en TCP/IP-adres voor SAP ASCS/SCS-clusterconfiguratie][sap-ha-guide-figure-3047]

   _**Figuur 57:** Nieuwe virtuele naam en TCP/IP-adres voor SAP ASCS/SCS-clusterconfiguratie_

#### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Het eerste clusterknooppunt van SAP installeren

1. Voer de eerste clusterknooppuntoptie uit op clusterknooppunt A. Bijvoorbeeld op de **pr1-ascs-0 host.**
2. Als u de standaardpoorten voor de interne lastenbalancer van Azure wilt behouden, selecteert u:

   * **ABAP-systeem**: **ASCS-instantienummer** **00**
   * **Java-systeem**: **SCS-instantienummer** **01**
   * **ABAP+Java-systeem**: **ASCS-instantienummer** **00** en **SCS-instantienummer** **01**

   Als u andere instantienummers dan 00 wilt gebruiken voor de ABAP ASCS-instantie en 01 voor de Java SCS-instantie, moet u eerst de standaardregels voor de standaardtaakverdeling van azure load balancer wijzigen, beschreven in [De standaardtaakregels voor de taakverdeling van ASCS/SCS voor de interne load balancer van Azure][sap-ha-guide-8.9]wijzigen.

De volgende taken worden niet beschreven in de standaard SAP-installatiedocumentatie.

> [!NOTE]
> De SAP-installatiedocumentatie beschrijft hoe u het eerste ASCS/SCS-clusterknooppunt installeert.
>
>

#### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Het SAP-profiel van het instantieASCS/SCS wijzigen

U moet een nieuwe profielparameter toevoegen. De profielparameter voorkomt dat verbindingen tussen SAP-werkprocessen en de wachtrijserver worden gesloten wanneer deze te lang inactief zijn. We noemden het probleemscenario in [Registervermeldingen toevoegen op beide clusterknooppunten van de SAP ASCS/SCS-instantie.][sap-ha-guide-8.11] In die sectie hebben we ook twee wijzigingen aangebracht in een aantal basisparameters voor TCP/IP-verbindingen. In een tweede stap moet u de wachtrijserver `keep_alive` zo instellen dat een signaal wordt verzonden, zodat de verbindingen de niet-actieve drempeldrempel van de interne lastenbalancer van Azure niet raken.

Ga als lid van het SAP-profiel van het exemplaar ASCS/SCS:

1. Voeg deze profielparameter toe aan het SAP ASCS/SCS-instantieprofiel:

   ```
   enque/encni/set_so_keepalive = true
   ```
   In ons voorbeeld is het pad:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Bijvoorbeeld naar het SAP SCS-instantieprofiel en het bijbehorende pad:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Als u de wijzigingen wilt toepassen, start u de instantie SAP ASCS /SCS opnieuw.

#### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Een sondepoort toevoegen

Gebruik de sondefunctionaliteit van de interne load balancer om de volledige clusterconfiguratie te laten werken met Azure Load Balancer. De interne load balancer van Azure verdeelt de binnenkomende werkbelasting meestal gelijk tussen deelnemende virtuele machines. Dit werkt echter niet in sommige clusterconfiguraties omdat slechts één exemplaar actief is. De andere instantie is passief en kan geen van de werkbelasting accepteren. Een sondefunctionaliteit helpt wanneer de interne load balancer van Azure alleen werk toewijst aan een actieve instantie. Met de sondefunctionaliteit kan de interne load balancer detecteren welke exemplaren actief zijn en vervolgens alleen de instantie met de werkbelasting targeten.

Ga als lid van het werk om een sondepoort toe te voegen:

1. Controleer de huidige **ProbePort-instelling** door de volgende PowerShell-opdracht uit te voeren. Voer het uit vanuit een van de virtuele machines in de clusterconfiguratie.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. Definieer een sondepoort. Het standaardsondepoortnummer is **0**. In ons voorbeeld gebruiken we sondepoort **62000.**

   ![Figuur 58: De sondepoort van de clusterconfiguratie is standaard 0][sap-ha-guide-figure-3048]

   _**Figuur 58:** De standaardpoort voor clusterconfiguratiessonde is 0_

   Het poortnummer wordt gedefinieerd in SAP Azure Resource Manager-sjablonen. U het poortnummer toewijzen in PowerShell.

   Voer het volgende PowerShell-script uit als u een nieuwe ProbePort-waarde wilt instellen voor de **SAP-<*SID->* IP-clusterbron.** Werk de PowerShell-variabelen voor uw omgeving bij. Nadat het script is uitgevoerd, wordt u gevraagd de SAP-clustergroep opnieuw te starten om de wijzigingen te activeren.

   ```powershell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

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

   Nadat u de **SAP-<*SID-clusterrol* > ** online hebt geplaatst, controleert u of **ProbePort** is ingesteld op de nieuwe waarde.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![Figuur 59: De clusterpoort onderzoeken nadat u de nieuwe waarde hebt ingesteld][sap-ha-guide-figure-3049]

   _**Figuur 59:** Sonde de clusterpoort nadat u de nieuwe waarde hebt ingesteld_

#### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>De sondepoort van de Windows-firewall openen

U moet een Windows-firewallsondepoort openen op beide clusterknooppunten. Gebruik het volgende script om een Windows-firewallsondepoort te openen. Werk de PowerShell-variabelen voor uw omgeving bij.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

De **ProbePort** is ingesteld op **62000**. Nu u toegang krijgen tot de bestandsshare ** \\\ascsha-clsap\sapmnt** van andere hosts, zoals van **ascsha-dbas**.

### <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>De database-instantie installeren

Als u de database-instantie wilt installeren, volgt u het proces dat wordt beschreven in de SAP-installatiedocumentatie.

### <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>Het tweede clusterknooppunt installeren

Als u het tweede cluster wilt installeren, voert u de stappen in de SAP-installatiehandleiding uit.

### <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Het starttype van het Windows-serviceexemplaar SAP ERS wijzigen

Wijzig het starttype van de SAP ERS Windows-service in **Automatisch (Vertraagde start)** op beide clusterknooppunten.

![Figuur 60: Het servicetype voor de SAP ERS-instantie wijzigen in vertraagde automatische][sap-ha-guide-figure-3050]

_**Figuur 60:** Het servicetype voor de SAP ERS-instantie wijzigen in vertraagde automatische_

### <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>De SAP Primary Application Server installeren

Installeer de instantie Primary Application Server (PAS) <*SID*>-di-0 op de virtuele machine die u hebt aangewezen om het PAS te hosten. Er zijn geen afhankelijkheden van Azure- of DataKeeper-specifieke instellingen.

### <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>De SAP Extra Application Server installeren

Installeer een SAP Additional Application Server (AAS) op alle virtuele machines die u hebt aangewezen om een SAP Application Server-exemplaar te hosten. Bijvoorbeeld op <*SID*>-di-1 naar <&lt; *SID*>-di- n&gt;.

> [!NOTE]
> Hiermee wordt de installatie van een SAP NetWeaver-systeem met hoge beschikbaarheid voltooid. Ga vervolgens verder met failovertesten.
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>De failover- en SIOS-replicatie van SAP ASCS/SCS-instantie testen
Het is eenvoudig om een SAP ASCS/SCS-instantiefailover en SIOS-schijfreplicatie te testen en te controleren met behulp van Failover Cluster Manager en het sios DataKeeper-beheer- en configuratiehulpprogramma.

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>SAP ASCS/SCS-exemplaar wordt uitgevoerd op clusterknooppunt A

De **SAP PR1-clustergroep** wordt uitgevoerd op clusterknooppunt A. Bijvoorbeeld op **pr1-ascs-0**. Wijs de gedeelde schijfsdrive S, die deel uitmaakt van de **SAP PR1-clustergroep** en die de instantie ASCS/SCS gebruikt, toe aan clusterknooppunt A.

![Figuur 61: Failoverclusterbeheer: de SAP-<SID->-clustergroep wordt uitgevoerd op clusterknooppunt A][sap-ha-guide-figure-5000]

_**Figuur 61:** Failoverclusterbeheer: de CLUSTERgroep SAP-<*SID*> wordt uitgevoerd op clusterknooppunt A_

In het hulpprogramma Beheer en configuratie van SIOS DataKeeper u zien dat de gedeelde schijfgegevens synchroon worden gerepliceerd van de bronvolumedrive S op clusterknooppunt A naar de doelvolumedrive S op clusterknooppunt B. Het wordt bijvoorbeeld gerepliceerd van **pr1-ascs-0 [10.0.0.40]** naar **pr1-ascs-1 [10.0.0.41]**.

![Figuur 62: In SIOS DataKeeper het lokale volume repliceren van clusterknooppunt A naar clusterknooppunt B][sap-ha-guide-figure-5001]

_**Figuur 62:** In SIOS DataKeeper het lokale volume repliceren van clusterknooppunt A naar clusterknooppunt B_

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>Failover van knooppunt A naar knooppunt B

1. Kies een van deze opties om een failover van de SAP-<*SID*>-clustergroep van clusterknooppunt A naar clusterknooppunt B te starten:
   - Failoverclusterbeheer gebruiken  
   - Failovercluster PowerShell gebruiken

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Start clusterknooppunt A opnieuw op in het Windows-gastbesturingssysteem (hiermee wordt een automatische failover van de SAP-<*SID*>-clustergroep van knooppunt A naar knooppunt B) gestart.  
3. Start clusterknooppunt A opnieuw op vanuit de Azure-portal (hiermee *SID* wordt een automatische failover van de SAP-<SID->-clustergroep van knooppunt A naar knooppunt B) gestart.  
4. Start clusterknooppunt A opnieuw op met Azure PowerShell (hiermee wordt een automatische failover van de SAP-<*SID*>-clustergroep van knooppunt A naar knooppunt B) gestart.

   Na failover wordt de SAP-<*SID*>-clustergroep uitgevoerd op clusterknooppunt B. Het draait bijvoorbeeld op **pr1-ascs-1.**

   ![Figuur 63: In Failoverclusterbeheer wordt de SAP-<SID->-clustergroep uitgevoerd op clusterknooppunt B][sap-ha-guide-figure-5002]

   _**Figuur 63**: In Failoverclusterbeheer wordt de *>-clustergroep* SAP <uitgevoerd op clusterknooppunt B_

   De gedeelde schijf is nu gemonteerd op clusterknooppunt B. SIOS DataKeeper repliceert gegevens van bronvolumestation S op clusterknooppunt B om volumestation S op clusterknooppunt A te targeten. Het repliceert bijvoorbeeld van **pr1-ascs-1 [10.0.0.41]** naar **pr1-ascs-0 [10.0.0.40]**.

   ![Figuur 64: SIOS DataKeeper repliceert het lokale volume van clusterknooppunt B naar clusterknooppunt A][sap-ha-guide-figure-5003]

   _**Figuur 64:** SIOS DataKeeper repliceert het lokale volume van clusterknooppunt B naar clusterknooppunt A_
