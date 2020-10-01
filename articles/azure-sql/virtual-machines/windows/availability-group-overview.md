---
title: Overzicht van SQL Server AlwaysOn-beschikbaarheidsgroepen
description: In dit artikel vindt u algemene informatie over SQL Server AlwaysOn-beschikbaarheidsgroepen in Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mathoma
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 34d76d7c85a478b5e31a692e653752aa1653884c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91293659"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Algemene informatie over SQL Server AlwaysOn-beschikbaarheidsgroepen in Azure Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel vindt u algemene informatie over SQL Server-beschikbaarheidsgroepen in Azure Virtual Machines. 

AlwaysOn-beschikbaarheidsgroepen in Azure Virtual Machines zijn vergelijkbaar met on-premises AlwaysOn-beschikbaarheidsgroepen. Zie [AlwaysOn-beschikbaarheidsgroepen (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx) voor meer informatie. 

In het volgende diagram ziet u de onderdelen van een volledige SQL Server-beschikbaarheidsgroep in Azure Virtual Machines.

![Beschikbaarheidsgroep](./media/availability-group-overview/00-EndstateSampleNoELB.png)

Het belangrijkste verschil met een beschikbaarheidsgroep in p Azure Virtual Machines is dat voor deze virtuele machines (VM's) een [load balancer](../../../load-balancer/load-balancer-overview.md) vereist is. De load balancer bevat de IP-adressen voor de listener voor de beschikbaarheidsgroep. Als u meer dan één beschikbaarheidsgroep hebt, is voor elke groep een listener vereist. Eén load balancer kan meerdere listeners ondersteunen.

Bij een failover-gastcluster voor een Azure IaaS-VM adviseren we één NIC per server (clusterknooppunt) en één subnet. Een Azure-netwerk maakt gebruikt van fysieke redundantie, waardoor extra NIC's en subnetten overbodig zijn voor een gastcluster voor een Azure IaaS-VM. Hoewel het clustervalidatierapport een waarschuwing zal bevatten dat de knooppunten alleen bereikbaar zijn in één netwerk, kan deze waarschuwing zonder problemen worden genegeerd in het geval van failover-gastclusters voor een Azure IaaS-VM. 

Om redundantie en de maximale beschikbaarheid te vergroten, moeten de SQL Server-VM's zich in dezelfde [beschikbaarheidsset](availability-group-manually-configure-prerequisites-tutorial.md#create-availability-sets) bevinden of in verschillende [beschikbaarheidszones](/azure/availability-zones/az-overview). 

|  | Versie van Windows Server | Versie van SQL Server | Editie van SQL Server | Configuratie van WSFC-quorum | DR met meerdere regio's | Ondersteuning voor meerdere subnetten | Ondersteuning voor een bestaande AD | DR met meerdere zones in dezelfde regio | Ondersteuning voor dist-AG zonder AD-domein | Ondersteuning voor dist-AG zonder cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| **[Azure Portal](availability-group-azure-portal-configure.md)** | 2019 </br> 2016 | 2019 </br>2017 </br>2016   | Ent | Cloudwitness | Nee | Ja | Ja | Ja | Nee | Nee |
| **[Azure CLI / PowerShell](availability-group-az-cli-configure.md)** | 2019 </br> 2016 | 2019 </br>2017 </br>2016   | Ent | Cloudwitness | Nee | Ja | Ja | Ja | Nee | Nee |
| **[Quickstartsjablonen](availability-group-quickstart-template-configure.md)** | 2019 </br> 2016 | 2019 </br>2017 </br>2016  | Ent | Cloudwitness | Nee | Ja | Ja | Ja | Nee | Nee |
| **[Handmatig](availability-group-manually-configure-prerequisites-tutorial.md)** | Alles | Alles | Alles | Alles | Ja | Ja | Ja | Ja | Ja | Ja |

De sjabloon **SQL Server AlwaysOn Cluster (preview)** is verwijderd van Azure Marketplace en is niet meer beschikbaar. 

Raadpleeg deze zelfstudies wanneer u klaar bent om een SQL Server-beschikbaarheidsgroep te gaan maken in Azure Virtual Machines.

## <a name="manually-with-azure-cli"></a>Handmatig met Azure CLI

Het is raadzaam om Azure CLI te gebruiken voor het configureren en implementeren van een beschikbaarheidsgroep omdat dit de eenvoudigste en snelste manier is om te implementeren. Met Azure CLI kunt u binnen 30 minuten het Windows-failovercluster maken, SQL Server-VM's toevoegen aan het cluster, en de listener en interne load balancer maken. De beschikbaarheidsgroep moet nog steeds handmatig worden gemaakt, maar alle andere noodzakelijke configuratiestappen zijn geautomatiseerd. 

Zie [Azure SQL VM CLI gebruiken voor het configureren van AlwaysOn-beschikbaarheidsgroepen voor SQL Server op een Azure-VM](availability-group-az-cli-configure.md) voor meer informatie. 

## <a name="automatically-with-azure-quickstart-templates"></a>Automatisch met Azure-quickstartsjablonen

De Azure quickstart-sjablonen maken gebruik van de SQL VM-provider om het Windows-failovercluster te implementeren, SQL Server-VM's aan het cluster toe te voegen, de listener te maken en de interne load balancer te configureren. U moet nog wel steeds zelf de beschikbaarheidsgroep en de interne load balancer (ILB) maken. Alle andere noodzakelijke configuratiestappen worden echter geautomatiseerd en vereenvoudigd met deze optie, waaronder de configuratie van de ILB. 

Zie [Azure-quickstartsjablonen gebruiken voor het configureren van AlwaysOn-beschikbaarheidsgroepen voor SQL Server op een Azure-VM](availability-group-quickstart-template-configure.md) voor meer informatie.


## <a name="automatically-with-an-azure-portal-template"></a>Automatisch met een sjabloon uit de Azure-portal

[AlwaysOn-beschikbaarheidsgroepen handmatig configureren op Azure-VM - Resource Manager](availability-group-azure-marketplace-template-configure.md)


## <a name="manually-in-the-azure-portal"></a>Handmatig in de Azure-portal

U kunt de virtuele machines ook zelf maken zonder de sjabloon. Zorg eerst dat aan de vereisten is voldaan en maak vervolgens de beschikbaarheidsgroep. Raadpleeg de volgende onderwerpen: 

- [Vereisten configureren voor SQL Server AlwaysOn-beschikbaarheidsgroepen in Azure Virtual Machines](availability-group-manually-configure-prerequisites-tutorial.md)

- [AlwaysOn-beschikbaarheidsgroep maken om beschikbaarheid en herstel na noodgeval te verbeteren](availability-group-manually-configure-tutorial.md)

## <a name="next-steps"></a>Volgende stappen

[Een SQL Server AlwaysOn-beschikbaarheidsgroep configureren in Azure Virtual Machines in verschillende regio's](availability-group-manually-configure-multiple-regions.md)
