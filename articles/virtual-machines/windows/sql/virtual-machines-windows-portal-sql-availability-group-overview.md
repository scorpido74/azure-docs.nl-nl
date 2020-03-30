---
title: Overzicht van beschikbaarheidsgroepen
description: In dit artikel worden SQL Server-beschikbaarheidsgroepen geïntroduceerd op virtuele Azure-machines.
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
ms.openlocfilehash: 8119990ab4ab4a918e325976092100086a547aa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037498"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Sql Server-beschikbaarheidsgroepen introduceren op virtuele Azure-machines

In dit artikel worden SQL Server-beschikbaarheidsgroepen geïntroduceerd op Azure Virtual Machines. 

Always On availability groups on Azure Virtual Machines are similar to Always On availability groups on premises. Zie [Altijd beschikbaarheidsgroepen (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)voor meer informatie. 

Het diagram toont de onderdelen van een complete SQL Server Availability Group in Azure Virtual Machines.

![Beschikbaarheidsgroep](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Het belangrijkste verschil voor een beschikbaarheidsgroep in Azure Virtual Machines is dat de virtuele Azure-machines een [load balancer](../../../load-balancer/load-balancer-overview.md)vereisen. De load balancer bevat de IP-adressen voor de listener van de beschikbaarheidsgroep. Als u meer dan één beschikbaarheidsgroep hebt, heeft elke groep een listener nodig. Eén load balancer kan meerdere luisteraars ondersteunen.

Bovendien raden we op een Azure IaaS VM-failovercluster één NIC per server (clusterknooppunt) en één subnet aan. Een Azure-netwerk maakt gebruikt van fysieke redundantie, waardoor extra NIC's en subnetten overbodig zijn voor een gastcluster voor een Azure IaaS-VM. Hoewel het clustervalidatierapport een waarschuwing zal bevatten dat de knooppunten alleen bereikbaar zijn in één netwerk, kan deze waarschuwing zonder problemen worden genegeerd in het geval van failover-gastclusters voor een Azure IaaS-VM. 

Om de redundantie en hoge beschikbaarheid te verhogen, moeten de SQL Server-VM's zich in dezelfde [beschikbaarheidsset](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets)of verschillende [beschikbaarheidszones bevinden](/azure/availability-zones/az-overview). 

|  | Windows Server-versie | SQL Server-versie | SQL Server Edition | WSFC Quorum Config | DR met multiregio | Ondersteuning voor meerdere subnet | Ondersteuning voor een bestaand AD | DR met meerdere zones dezelfde regio | Ondersteuning voor Dist-AG zonder AD-domein | Ondersteuning voor Dist-AG zonder cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | Cloudwitness | Nee | Ja | Ja | Ja | Nee | Nee |
| [Quickstart-sjablonen](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | Cloudwitness | Nee | Ja | Ja | Ja | Nee | Nee |
| [Portalsjabloon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | Bestandsshare | Nee | Nee | Nee | Nee | Nee | Nee |
| [Handmatig](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Alle | Alle | Alle | Alle | Ja | Ja | Ja | Ja | Ja | Ja |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Wanneer u klaar bent om een SQL Server-beschikbaarheidsgroep op Azure Virtual Machines te bouwen, raadpleegt u deze zelfstudies.

## <a name="manually-with-azure-cli"></a>Handmatig met Azure CLI
Azure CLI gebruiken om een beschikbaarheidsgroep te configureren en implementeren is de aanbevolen optie, omdat dit de beste is in termen van eenvoud en snelheid van implementatie. Met Azure CLI kan het maken van het Windows Failovercluster, het samenvoegen van SQL Server VM's met het cluster, evenals het maken van de listener en internal load balancer binnen 30 minuten worden bereikt. Deze optie vereist nog steeds een handmatige creatie van de beschikbaarheidsgroep, maar automatiseert alle andere noodzakelijke configuratiestappen. 

Zie Azure SQL VM CLI gebruiken om de [groep Always On-beschikbaarheid voor SQL Server op een Azure VM te configureren](virtual-machines-windows-sql-availability-group-cli.md)voor meer informatie. 

## <a name="automatically-with-azure-quickstart-templates"></a>Automatisch met Azure Quickstart-sjablonen
De Azure Quickstart-sjablonen maken gebruik van de SQL VM-bronprovider om het Windows Failovercluster te implementeren, SQL Server VM's eraan toe te voegen, de listener te maken en de interne load balancer te configureren. Deze optie vereist nog steeds een handmatige creatie van de beschikbaarheidsgroep en Internal Load Balancer (ILB), maar automatiseert en vereenvoudigt alle andere noodzakelijke configuratiestappen (inclusief de configuratie van de ILB). 

Zie [Azure Quickstart-sjabloon gebruiken om de groep Always On-beschikbaarheid voor SQL Server op een Azure VM te configureren](virtual-machines-windows-sql-availability-group-quickstart-template.md)voor meer informatie.


## <a name="automatically-with-an-azure-portal-template"></a>Automatisch met een Azure-portalsjabloon

[Beschikbaarheidsgroep Always On configureren in Azure VM - Resourcebeheer](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Handmatig in Azure-portal

U de virtuele machines ook zelf maken zonder de sjabloon. Vul eerst de vereisten in en maak vervolgens de beschikbaarheidsgroep. Zie de volgende onderwerpen: 

- [Vereisten configureren voor SQL Server Always On beschikbaarheidsgroepen op Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Always On Availability Group maken om de beschikbaarheid en het herstel na noodgevallen te verbeteren](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Volgende stappen

[Een SQL Server Always On Availability Group configureren op Azure Virtual Machines in verschillende regio's](virtual-machines-windows-portal-sql-availability-group-dr.md)
