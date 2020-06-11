---
title: Wijzigingen in de documentatie voor SQL Server op Azure Virtual Machines | Microsoft Docs
description: Meer informatie over de nieuwe functies en verbeteringen voor SQL Server op Azure Virtual Machines.
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: 46bde8a075beccc0f9f6462823acfc66361caf05
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669066"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Wijzigingen in de documentatie voor SQL Server op Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Met Azure kunt u een virtuele machine (VM) implementeren met een installatie kopie van SQL Server ingebouwde. In dit artikel vindt u een overzicht van de wijzigingen in de documentatie die zijn gekoppeld aan nieuwe functies en verbeteringen in de recente releases van [SQL Server op Azure virtual machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). 


## <a name="january-2020"></a>Januari 2020

| Wijzigingen | Details |
| --- | --- |
| **Ondersteuning voor Azure Government** | Het is nu mogelijk om SQL Server virtuele machines te registreren met de resource provider van de SQL-VM voor virtuele machines die worden gehost in de [Azure Government](https://azure.microsoft.com/global-infrastructure/government/) Cloud. | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Wijzigingen | Details |
 --- | --- |
| **Gratis DR-replica in azure** | Als u [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)hebt, kunt u een [gratis passief exemplaar](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) hosten voor herstel na nood gevallen in azure voor uw on-premises SQL Server exemplaar. | 
| **Registratie van bulk bron provider** | U kunt SQL Server virtuele machines nu [bulksgewijs registreren](sql-vm-resource-provider-bulk-register.md) bij de resource provider. | 
|**Opslag Configuratie geoptimaliseerd voor prestaties** | U kunt nu [uw opslag configuratie volledig aanpassen](storage-configuration.md#new-vms) bij het maken van een nieuwe SQL Server VM. |
|**Premium-bestands share voor FCI** | U kunt nu een failover-cluster exemplaar maken met behulp van een [Premium-bestands share](failover-cluster-instance-premium-file-share-manually-configure.md) in plaats van de oorspronkelijke methode van [opslagruimten direct](failover-cluster-instance-storage-spaces-direct-manually-configure.md). 
| **Azure Dedicated Host** | U kunt uw SQL Server-VM uitvoeren op een [toegewezen Azure-host](dedicated-host.md). | 
| **SQL Server VM verplaatsen naar een andere regio** | Gebruik Azure Site Recovery om [uw SQL Server-VM van de ene regio naar de andere te migreren](move-sql-vm-different-region.md). |
|  **Nieuwe SQL IaaS-installatie modi** | Het is nu mogelijk om de SQL Server IaaS-uitbrei ding in de [Lightweight-modus](sql-server-iaas-agent-extension-automate-management.md) te installeren om te voor komen dat de SQL Server-service opnieuw wordt gestart.  |
| **Aanpassing van SQL Server-editie** | U kunt nu de [eigenschap Edition](change-sql-server-edition.md) voor uw SQL Server virtuele machine wijzigen. |
| **Wijzigingen in de SQL-VM-resource provider** | U kunt [uw SQL Server-VM registreren bij de resource provider van de SQL-VM](sql-vm-resource-provider-register.md) door gebruik te maken van de nieuwe SQL IaaS-modi. Deze mogelijkheid omvat [Windows Server 2008](sql-vm-resource-provider-register.md#management-modes) -installatie kopieën.|
| **Gebruik Azure Hybrid Benefit om uw eigen licentie kopieën te maken** | Met uw eigen licentie-installatie kopieën die zijn geïmplementeerd vanuit Azure Marketplace, kunnen nu het [licentie type overschakelen naar betalen per](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks)gebruik.| 
| **Nieuw SQL Server VM-beheer in Azure Portal** | Er is nu een manier om uw SQL Server-VM te beheren in de Azure Portal. Zie [SQL Server Vm's beheren in de Azure Portal](manage-sql-vm-portal.md)voor meer informatie.  | 
| **Uitgebreide ondersteuning voor SQL Server 2008/2008 R2** | [Uitgebreide ondersteuning](sql-server-2008-extend-end-of-support.md) voor SQL Server 2008 en SQL Server 2008 R2 door te migreren *naar een* virtuele machine van Azure. | 
| **Ondersteuning voor aangepaste installatie kopieën** | U kunt nu de [SQL Server IaaS-extensie](sql-server-iaas-agent-extension-automate-management.md#installation) installeren op aangepaste besturings systemen en SQL Server installatie kopieën, die de beperkte functionaliteit van [flexibele licenties](licensing-model-azure-hybrid-benefit-ahb-change.md)bieden. Wanneer u uw aangepaste installatie kopie met de resource provider van de SQL-VM registreert, geeft u het licentie type op als ' AHUB '. Anders mislukt de registratie. | 
| **Ondersteunings ondersteuning voor benoemde instanties** | U kunt nu de [SQL Server IaaS-extensie](sql-server-iaas-agent-extension-automate-management.md#installation) gebruiken met een benoemd exemplaar als het standaard exemplaar op de juiste wijze is verwijderd. | 
| **Portal verbetering** | De Azure Portal ervaring voor het implementeren van een SQL Server VM is vernieuwd om de bruikbaarheid te verbeteren. Meer informatie vindt u in de korte [Snelstartgids](sql-vm-create-portal-quickstart.md) en uitgebreidere [hand leiding](create-sql-vm-portal.md) voor het implementeren van een SQL Server-VM.|
| **Portal verbetering** | Het is nu mogelijk om het licentie model voor een SQL Server-VM te wijzigen van betalen per gebruik naar uw eigen licentie met behulp van de [Azure Portal](licensing-model-azure-hybrid-benefit-ahb-change.md#vms-already-registered-with-the-resource-provider).|
| **Vereenvoudiging van de implementatie van de beschikbaarheids groep met Azure SQL Server VM CLI** | Het is nu eenvoudiger dan ooit om een beschikbaarheids groep te implementeren op een SQL Server VM in Azure. U kunt de [Azure cli](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) gebruiken om de Windows-failovercluster, interne Load Balancer en beschikbaarheids groep-listeners te maken vanaf de opdracht regel. Zie [de azure SQL Server VM CLI gebruiken voor het configureren van een AlwaysOn-beschikbaarheids groep voor SQL Server op een virtuele Azure-machine](availability-group-az-cli-configure.md)voor meer informatie. | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Wijzigingen | Details |
| --- | --- |
|  **Nieuwe resource provider voor een SQL Server cluster** | Met een nieuwe resource provider (micro soft. SqlVirtualMachine/SqlVirtualMachineGroups) worden de meta gegevens van het Windows-failovercluster gedefinieerd. Het toevoegen van een SQL Server VM aan *SqlVirtualMachineGroups* Boots traps de WSFC-service (Windows Server failover cluster) en koppelt de virtuele machine aan het cluster.  |
| **Automatische installatie van een implementatie van een beschikbaarheids groep met Azure Quick Start-sjablonen** |Het is nu mogelijk om het Windows-failovercluster te maken, SQL Server Vm's aan toe te voegen, de listener te maken en de interne load balancer met twee Azure Quick Start-sjablonen te configureren. Zie Azure Quick Start- [sjablonen gebruiken voor het configureren van een AlwaysOn-beschikbaarheids groep voor SQL Server op een virtuele machine van Azure](availability-group-quickstart-template-configure.md)voor meer informatie. | 
| **Automatische registratie voor de resource provider van de SQL-VM** | SQL Server Vm's die na deze maand zijn geïmplementeerd, worden automatisch geregistreerd bij de resource provider van de nieuwe SQL-VM. SQL Server Vm's die vóór deze maand zijn geïmplementeerd, moeten nog steeds hand matig worden geregistreerd. Zie voor meer informatie [registreren van een SQL Server virtuele machine in azure met de resource provider van de SQL-VM](sql-vm-resource-provider-register.md).|
|**Nieuwe SQL-VM-resource provider** |  Een nieuwe resource provider (micro soft. SqlVirtualMachine) biedt een beter beheer van uw SQL Server-Vm's. Zie voor meer informatie over het registreren van uw Vm's [een SQL Server virtuele machine registreren in azure met de resource provider van de SQL-VM](sql-vm-resource-provider-register.md). |
|**Licentie model scha kelen** | U kunt nu scha kelen tussen de modellen van betalen per gebruik en uw eigen licentie voor uw SQL Server-VM met behulp van Azure CLI of Power shell. Zie [How to Change the License model for a SQL Server Virtual Machine in azure](licensing-model-azure-hybrid-benefit-ahb-change.md)(Engelstalig) voor meer informatie. | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Aanvullende bronnen

**Windows-vm's**:

* [Overzicht van SQL Server op een Windows-VM](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server inrichten op een Windows-VM](create-sql-vm-portal.md)
* [Een Data Base migreren naar SQL Server op een virtuele Azure-machine](migrate-to-vm-from-sql-server.md)
* [Hoge Beschik baarheid en herstel na nood gevallen voor SQL Server op Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Aanbevolen procedures voor het uitvoeren van SQL Server op Azure Virtual Machines](performance-guidelines-best-practices.md)
* [Toepassings patronen en ontwikkelings strategieën voor het SQL Server op Azure Virtual Machines](application-patterns-development-strategies.md)

**Virtuele Linux-machines**:

* [Overzicht van SQL Server op een Linux-VM](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [SQL Server inrichten op een virtuele Linux-machine](../linux/sql-vm-create-portal-quickstart.md)
* [Veelgestelde vragen (Linux)](../linux/frequently-asked-questions-faq.md)
* [Documentatie over SQL Server on Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
