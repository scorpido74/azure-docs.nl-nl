---
title: Overzicht van beschikbaarheids groepen
description: In dit artikel worden SQL Server-beschikbaarheids groepen op virtuele machines van Azure geïntroduceerd.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: eddb63f82669821914edefc1b75a7b4dcdc0ca2d
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636152"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Introductie van SQL Server-beschikbaarheids groepen op virtuele machines van Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel worden SQL Server-beschikbaarheids groepen in azure Virtual Machines geïntroduceerd. 

AlwaysOn-beschikbaarheids groepen op Azure Virtual Machines zijn vergelijkbaar met AlwaysOn-beschikbaarheids groepen on-premises. Zie AlwaysOn [Availability groups (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)voor meer informatie. 

Het diagram illustreert de onderdelen van een volledige SQL Server-beschikbaarheids groep in azure Virtual Machines.

![Beschikbaarheidsgroep](./media/availability-group-overview/00-EndstateSampleNoELB.png)

Het belangrijkste verschil voor een beschikbaarheids groep in azure Virtual Machines is dat de virtuele machines van Azure een [Load Balancer](../../../load-balancer/load-balancer-overview.md)vereisen. Het load balancer bevat de IP-adressen voor de beschikbaarheids groep-listener. Als u meer dan één beschikbaarheids groep hebt, is voor elke groep een listener vereist. Een load balancer kan meerdere listeners ondersteunen.

Daarnaast wordt in een Azure IaaS VM-gast-failovercluster aanbevolen één NIC per server (cluster knooppunt) en één subnet. Een Azure-netwerk maakt gebruikt van fysieke redundantie, waardoor extra NIC's en subnetten overbodig zijn voor een gastcluster voor een Azure IaaS-VM. Hoewel het clustervalidatierapport een waarschuwing zal bevatten dat de knooppunten alleen bereikbaar zijn in één netwerk, kan deze waarschuwing zonder problemen worden genegeerd in het geval van failover-gastclusters voor een Azure IaaS-VM. 

Om redundantie en maximale Beschik baarheid te verbeteren, moeten de SQL Server Vm's zich in dezelfde [beschikbaarheidsset](availability-group-manually-configure-prerequisites-tutorial.md#create-availability-sets)bevinden of in verschillende [beschikbaarheids zones](/azure/availability-zones/az-overview). 

|  | Windows Server-versie | SQL Server versie | SQL Server-editie | Configuratie van WSFC-quorum | DR met meerdere regio's | Ondersteuning voor meerdere subnetten | Ondersteuning voor een bestaande AD | DR met meerdere zones met dezelfde regio | Ondersteuning voor dist-AG zonder AD-domein | Ondersteuning voor VERD-AG zonder cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL-VM-CLI](availability-group-az-cli-configure.md) | 2016 | 2017 </br>2016   | ENT | Cloudwitness | Nee | Ja | Ja | Ja | Nee | Nee |
| [Quick Start-sjablonen](availability-group-quickstart-template-configure.md) | 2016 | 2017</br>2016  | ENT | Cloudwitness | Nee | Ja | Ja | Ja | Nee | Nee |
| [Handmatig](availability-group-manually-configure-prerequisites-tutorial.md) | Alles | Alles | Alles | Alles | Ja | Ja | Ja | Ja | Ja | Ja |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

De sjabloon **voor het SQL Server AlwaysOn-cluster (preview)** is verwijderd uit de Azure Marketplace en is niet meer beschikbaar. 

Raadpleeg deze zelf studies wanneer u klaar bent om een SQL Server-beschikbaarheids groep te maken in azure Virtual Machines.

## <a name="manually-with-azure-cli"></a>Hand matig met Azure CLI
Het gebruik van Azure CLI voor het configureren en implementeren van een beschikbaarheids groep is de aanbevolen optie, omdat dit de beste oplossing is en de snelheid van de implementatie. Met Azure CLI is het maken van het Windows-failovercluster, het samen voegen van SQL Server Vm's aan het cluster en het maken van de listener en interne Load Balancer allemaal binnen 30 minuten. Voor deze optie moet de beschikbaarheids groep nog steeds hand matig worden gemaakt, maar alle andere nood zakelijke configuratie stappen worden geautomatiseerd. 

Zie voor meer informatie Azure SQL VM CLI gebruiken voor het configureren van AlwaysOn- [beschikbaarheids groep voor SQL Server op een virtuele machine van Azure](availability-group-az-cli-configure.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automatisch met Azure Quick Start-sjablonen
In de Azure Quick Start-sjablonen wordt gebruikgemaakt van de resource provider van de SQL-VM om het Windows-failovercluster te implementeren, SQL Server Vm's aan toe te voegen, de listener te maken en de interne Load Balancer te configureren. Voor deze optie is nog steeds een hand matig maken van de beschikbaarheids groep en interne Load Balancer (ILB) nodig, maar worden alle andere nood zakelijke configuratie stappen geautomatiseerd en vereenvoudigd (inclusief de configuratie van de ILB). 

Zie voor meer informatie Azure Quick Start-sjabloon gebruiken voor het configureren van AlwaysOn- [beschikbaarheids groep voor SQL Server op een virtuele machine van Azure](availability-group-quickstart-template-configure.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automatisch met een Azure Portal sjabloon

[AlwaysOn AlwaysOn-beschikbaarheids groep configureren in azure VM automatisch-Resource Manager](availability-group-azure-marketplace-template-configure.md)


## <a name="manually-in-azure-portal"></a>Hand matig in Azure Portal

U kunt de virtuele machines ook zelf maken zonder de sjabloon. Vul eerst de vereisten in en maak vervolgens de beschikbaarheids groep. Zie de volgende onderwerpen: 

- [Vereisten configureren voor SQL Server AlwaysOn-beschikbaarheids groepen in azure Virtual Machines](availability-group-manually-configure-prerequisites-tutorial.md)

- [AlwaysOn-beschikbaarheids groep maken om de beschik baarheid en herstel na nood gevallen te verbeteren](availability-group-manually-configure-tutorial.md)

## <a name="next-steps"></a>Volgende stappen

[Een SQL Server always on-beschikbaarheids groep configureren op Azure Virtual Machines in verschillende regio's](availability-group-manually-configure-multiple-regions.md)
