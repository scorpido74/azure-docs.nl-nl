---
title: Wijzigingen in de documentatie voor SQL Server op Azure Virtual Machines | Microsoft Docs
description: Meer informatie over de nieuwe functies en verbeteringen voor SQL Server op een Azure VM
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/01/2019
ms.openlocfilehash: c122baa21c5d94b57f29cb0530f0a2655faa87d0
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790371"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Wijzigingen in de documentatie voor SQL Server op Azure Virtual Machines

Met Azure kunt u een virtuele machine (VM) implementeren met een installatie kopie van SQL Server ingebouwde. In dit artikel vindt u een overzicht van de wijzigingen in de documentatie die zijn gekoppeld aan nieuwe functies en verbeteringen in de recente releases van [SQL Server op Azure virtual machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). 


## <a name="october-2019"></a>Oktober 2019

| Gewijzigde | Details |
| --- | --- |
| **Registratie van bulk bron provider** | U kunt SQL virtual machines nu [bulksgewijs registreren](virtual-machines-windows-sql-bulk-register-with-resource-provider.md) bij de resource provider. | 
| **Opslag Configuratie geoptimaliseerd voor prestaties** | U kunt nu [uw opslag configuratie volledig aanpassen](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) bij het maken van een nieuwe SQL Server VM. |
| **Premium-bestands share voor FCI** | U kunt nu een failover-cluster exemplaar maken met behulp van een [Premium-bestands share](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) in plaats van de oorspronkelijke methode van [opslagruimten direct](virtual-machines-windows-portal-sql-create-failover-cluster.md). 
| &nbsp; | &nbsp; |

## <a name="august-2019"></a>Augustus 2019

| Gewijzigde | Details |
| --- | --- |
| **Voor Azure toegewezen host** | U kunt uw SQL Server-VM uitvoeren op een [toegewezen Azure-host](virtual-machines-windows-sql-dedicated-host.md). |
| &nbsp; | &nbsp; |


## <a name="july-2019"></a>Juli 2019


| Gewijzigde | Details |
| --- | --- |
| **SQL-VM naar een andere regio verplaatsen** | Gebruik Azure Site Recovery om [uw SQL Server-VM van de ene regio naar de andere te migreren](virtual-machines-windows-sql-move-different-region.md). |
| &nbsp; | &nbsp; |

## <a name="june-2019"></a>Juni 2019


| Gewijzigde | Details |
| --- | --- |
| **Nieuwe SQL IaaS-installatie modi** | Het is nu mogelijk om de SQL Server IaaS-uitbrei ding in de [Lightweight-modus](virtual-machines-windows-sql-server-agent-extension.md) te installeren om te voor komen dat de SQL Server-service opnieuw wordt gestart.  |
| **Aanpassing van SQL Server-editie** | U kunt nu de [eigenschap Edition](virtual-machines-windows-sql-change-edition.md) voor uw SQL Server virtuele machine wijzigen. |
| **Wijzigingen in de SQL-VM-resource provider** | U kunt [uw SQL Server-VM registreren bij de resource provider van de SQL-VM](virtual-machines-windows-sql-register-with-resource-provider.md) door gebruik te maken van de nieuwe SQL IaaS-modi. Deze mogelijkheid omvat [Windows Server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) -installatie kopieën.|
| **Gebruik Azure Hybrid Benefit om uw eigen licentie kopieën te maken** | Met uw eigen licentie-installatie kopieën die zijn geïmplementeerd vanuit Azure Marketplace, kunnen nu het [licentie type overschakelen naar betalen per](virtual-machines-windows-sql-ahb.md#remarks)gebruik.| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>Mei 2019

| Gewijzigde | Details |
| --- | --- |
| **Nieuw SQL Server VM-beheer in Azure Portal** | Er is nu een manier om uw SQL Server-VM te beheren in de Azure Portal. Zie [SQL Server Vm's beheren in de Azure Portal](virtual-machines-windows-sql-manage-portal.md)voor meer informatie.  | 
| &nbsp; | &nbsp; |



## <a name="april-2019"></a>April 2019

| Gewijzigde | Details |
| --- | --- |
| **Uitgebreide ondersteuning voor SQL Server 2008/2008 R2** | [Uitgebreide ondersteuning](virtual-machines-windows-sql-server-2008-eos-extend-support.md) voor SQL Server 2008 en SQL Server 2008 R2 door te migreren *naar een* virtuele machine van Azure. | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>Maart 2019

| Gewijzigde | Details |
| --- | --- |
| **Ondersteuning voor aangepaste installatie kopieën** | U kunt nu de [SQL Server IaaS-extensie](virtual-machines-windows-sql-server-agent-extension.md#installation) installeren op aangepaste besturings systemen en SQL-installatie kopieën, die de beperkte functionaliteit van [flexibele licenties](virtual-machines-windows-sql-ahb.md)bieden. Wanneer u uw aangepaste installatie kopie met de SQL-resource provider wilt registreren, geeft u het licentie type op als ' AHUB '. Anders mislukt de registratie. | 
| **Ondersteunings ondersteuning voor benoemde instanties** | U kunt nu de [SQL Server IaaS-extensie](virtual-machines-windows-sql-server-agent-extension.md#installation) gebruiken met een benoemd exemplaar als het standaard exemplaar op de juiste wijze is verwijderd. | 
| **Portal verbetering** | De Azure Portal ervaring voor het implementeren van een SQL Server VM is vernieuwd om de bruikbaarheid te verbeteren. Meer informatie vindt u in de korte [Snelstartgids](quickstart-sql-vm-create-portal.md) en uitgebreidere [hand leiding](virtual-machines-windows-portal-sql-server-provision.md) voor het implementeren van een SQL Server-VM.|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>Februari 2019

| Gewijzigde | Details |
| --- | --- |
| **Portal verbetering** | Het is nu mogelijk om het licentie model voor een SQL Server-VM te wijzigen van betalen per gebruik naar uw eigen licentie met behulp van de [Azure Portal](virtual-machines-windows-sql-ahb.md#change-the-license-for-vms-already-registered-with-the-resource-provider).|
|**Vereenvoudiging van de implementatie van de beschikbaarheids groep met Azure SQL Server VM CLI** | Het is nu eenvoudiger dan ooit om een beschikbaarheids groep te implementeren op een SQL Server VM in Azure. U kunt de [Azure cli](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) gebruiken om de Windows-failovercluster, interne Load Balancer en beschikbaarheids groep-listeners te maken vanaf de opdracht regel. Zie [de azure SQL Server VM CLI gebruiken voor het configureren van een AlwaysOn-beschikbaarheids groep voor SQL Server op een virtuele Azure-machine](virtual-machines-windows-sql-availability-group-cli.md)voor meer informatie. | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018


### <a name="december-2018"></a>December 2018

| Gewijzigde | Details |
| --- | --- |
| **Nieuwe resource provider voor een SQL Server cluster** | Met een nieuwe resource provider (micro soft. SqlVirtualMachine/SqlVirtualMachineGroups) worden de meta gegevens van het Windows-failovercluster gedefinieerd. Het toevoegen van een SQL Server VM aan *SqlVirtualMachineGroups* Boots traps de WSFC-service (Windows Server failover cluster) en koppelt de virtuele machine aan het cluster.  |
|**Automatische installatie van een implementatie van een beschikbaarheids groep met Azure Quick Start-sjablonen** |Het is nu mogelijk om het Windows-failovercluster te maken, SQL Server Vm's aan toe te voegen, de listener te maken en de interne load balancer met twee Azure Quick Start-sjablonen te configureren. Zie Azure Quick Start- [sjablonen gebruiken voor het configureren van een AlwaysOn-beschikbaarheids groep voor SQL Server op een virtuele machine van Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md)voor meer informatie. | 
| **Automatische registratie voor de resource provider van de SQL-VM** | SQL Server Vm's die na deze maand zijn geïmplementeerd, worden automatisch geregistreerd bij de resource provider van het nieuwe SQL Server. SQL Server Vm's die vóór deze maand zijn geïmplementeerd, moeten nog steeds hand matig worden geregistreerd. Zie voor meer informatie [registreren van een SQL Server virtuele machine in azure met de resource provider van de SQL-VM](virtual-machines-windows-sql-register-with-resource-provider.md).|
| &nbsp; | &nbsp; |


### <a name="november-2018"></a>November 2018

| Gewijzigde | Details |
| --- | --- |
| **Nieuwe SQL-VM-resource provider** |  Een nieuwe resource provider (micro soft. SqlVirtualMachine) biedt een beter beheer van uw SQL Server-Vm's. Zie voor meer informatie over het registreren van uw Vm's [een SQL Server virtuele machine registreren in azure met de resource provider van de SQL-VM](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Licentie model scha kelen** | U kunt nu scha kelen tussen de modellen van betalen per gebruik en uw eigen licentie voor uw SQL Server-VM met behulp van Azure CLI of Power shell. Zie [How to Change the License model for a SQL Server Virtual Machine in azure](virtual-machines-windows-sql-ahb.md)(Engelstalig) voor meer informatie. | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Aanvullende bronnen

**Virtuele Windows-machines**:

* [Overzicht van SQL Server op een Windows-VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Een SQL Server Windows-VM inrichten](virtual-machines-windows-portal-sql-server-provision.md)
* [Een Data Base migreren naar SQL Server op een virtuele Azure-machine](virtual-machines-windows-migrate-sql.md)
* [Hoge Beschik baarheid en herstel na nood gevallen voor SQL Server op Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Aanbevolen procedures voor het uitvoeren van SQL Server op Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Toepassings patronen en ontwikkelings strategieën voor het SQL Server op Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuele Linux-machines**:

* [Overzicht van SQL Server op een Linux-VM](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Een virtuele machine met SQL Server Linux inrichten](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Veelgestelde vragen (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Documentatie voor SQL Server op Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
