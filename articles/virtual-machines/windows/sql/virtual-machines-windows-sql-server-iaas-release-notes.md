---
title: Documentatiewijzigingen voor SQL Server op Azure Virtual Machines| Microsoft Documenten
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
ms.date: 01/06/2020
ms.openlocfilehash: 27682863a96e2f190a0dafe6e4d783029e987453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201642"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Documentatiewijzigingen voor SQL Server op Azure Virtual Machines

Met Azure u een virtuele machine (VM) implementeren met een afbeelding van SQL Server ingebouwd. In dit artikel worden de documentatiewijzigingen samengevat die zijn gekoppeld aan nieuwe functies en verbeteringen in de recente versies van [SQL Server op Azure Virtual Machines.](https://azure.microsoft.com/services/virtual-machines/sql-server/) 


## <a name="january-2020"></a>Januari 2020

| Wijzigingen | Details |
| --- | --- |
| **Ondersteuning voor Azure Government** | Het is nu mogelijk om virtuele SQL Server-machines te registreren bij de SQL VM-resourceprovider voor virtuele machines die worden gehost in de [Azure Government-cloud.](https://azure.microsoft.com/global-infrastructure/government/) | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Wijzigingen | Details |
 --- | --- |
| **Gratis DR-replica in Azure** | U een [gratis passief exemplaar](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) voor noodherstel in Azure hosten voor uw on-premises SQL Server-exemplaar als u Software Assurance [hebt.](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3) | 
| **Registratie van bulkresourceprovider** | U SQL-virtuele machines nu [bulkregisteren](virtual-machines-windows-sql-bulk-register-with-resource-provider.md) bij de resourceprovider. | 
|**Geoptimaliseerde opslagconfiguratie voor prestaties** | U nu [uw opslagconfiguratie volledig aanpassen](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) wanneer u een nieuwe SQL Server VM maakt. |
|**Premium bestandsshare voor FCI** | U nu een failoverclusterinstantie maken met een [Premium Bestandsshare](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) in plaats van de oorspronkelijke methode [van Direct opslaan](virtual-machines-windows-portal-sql-create-failover-cluster.md). 
| **Azure dedicated host** | U uw SQL Server VM uitvoeren op een [Azure Dedicated Host.](virtual-machines-windows-sql-dedicated-host.md) | 
| **SQL VM naar andere regio verplaatsen** | Gebruik Azure Site Recovery om [uw SQL Server VM van het ene gebied naar het andere te migreren.](virtual-machines-windows-sql-move-different-region.md) |
|  **Nieuwe SQL IaaS-installatiemodi** | Het is nu mogelijk om de SQL Server IaaS-extensie in [lichtgewicht modus](virtual-machines-windows-sql-server-agent-extension.md) te installeren om te voorkomen dat de SQL Server-service opnieuw wordt opgestart.  |
| **SQL Server-editiewijziging** | U nu de [eigenschap edition](virtual-machines-windows-sql-change-edition.md) voor uw SQL Server VM wijzigen. |
| **Wijzigingen in SQL VM-resourceprovider** | U [uw SQL Server VM registreren bij de SQL VM-resourceprovider](virtual-machines-windows-sql-register-with-resource-provider.md) met behulp van de nieuwe SQL IaaS-modi. Deze mogelijkheid omvat [Windows Server 2008-afbeeldingen.](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)|
| **Afbeeldingen met uw eigen licentie gebruiken Azure Hybrid Benefit** | Bring-your-own-license-afbeeldingen die zijn geïmplementeerd vanuit Azure Marketplace, kunnen nu van licentietype overschakelen [naar betalen per gebruik.](virtual-machines-windows-sql-ahb.md#remarks)| 
| **Nieuw SQL Server VM-beheer in Azure-portal** | Er is nu een manier om uw SQL Server VM te beheren in de Azure-portal. Zie [SQL Server VM's beheren in de Azure-portal](virtual-machines-windows-sql-manage-portal.md)voor meer informatie.  | 
| **Uitgebreide ondersteuning voor SQL Server 2008/2008 R2** | [Breid de ondersteuning](virtual-machines-windows-sql-server-2008-eos-extend-support.md) voor SQL Server 2008 en SQL Server 2008 R2 uit door te migreren *naar* een Azure VM. | 
| **Ondersteuning voor aangepaste afbeeldingen** | U nu de [SQL Server IaaS-extensie](virtual-machines-windows-sql-server-agent-extension.md#installation) installeren op aangepaste OS- en SQL-afbeeldingen, die de beperkte functionaliteit van [flexibele licenties](virtual-machines-windows-sql-ahb.md)biedt. Wanneer u uw aangepaste afbeelding registreert bij de SQL-resourceprovider, geeft u het licentietype op als 'AHUB'. Anders mislukt de registratie. | 
| **Ondersteunde ondersteuning van benoemde instantie** | U nu de [SQL Server IaaS-extensie](virtual-machines-windows-sql-server-agent-extension.md#installation) gebruiken met een benoemde instantie, als de standaardinstantie correct is verwijderd. | 
| **Portal-verbetering** | De Azure-portalervaring voor het implementeren van een SQL Server VM is vernieuwd om de bruikbaarheid te verbeteren. Zie voor meer informatie de korte [quickstart](quickstart-sql-vm-create-portal.md) en een uitgebreidere [handleiding voor het](virtual-machines-windows-portal-sql-server-provision.md) implementeren van een SQL Server VM.|
| **Verbetering van het portaal** | Het is nu mogelijk om het licentiemodel voor een SQL Server VM te wijzigen van pay-as-you-go naar bring-your-own-license met behulp van de [Azure-portal.](virtual-machines-windows-sql-ahb.md#vms-already-registered-with-the-resource-provider)|
| **Vereenvoudiging van de implementatie van beschikbaarheidsgroepen met Azure SQL Server VM CLI** | Het is nu eenvoudiger dan ooit om een beschikbaarheidsgroep te implementeren voor een SQL Server VM in Azure. U de [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) gebruiken om het Windows-failovercluster, interne load balancer en de groep listeners voor de groep beschikbaarheid te maken vanaf de opdrachtregel. Zie [De AZURE SQL Server VM CLI gebruiken om een groep Always On beschikbaarheid voor SQL Server te configureren op een Azure VM.](virtual-machines-windows-sql-availability-group-cli.md) | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Wijzigingen | Details |
| --- | --- |
|  **Nieuwe resourceprovider voor een SQL Server-cluster** | Een nieuwe resourceprovider (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) definieert de metagegevens van het Windows-failovercluster. Als u een SQL Server VM aansluit bij *SqlVirtualMachineGroups,* wordt de WSFC-service (Failover Cluster) van Windows Server failoveren en wordt de VM aan het cluster toegevoegd.  |
| **Geautomatiseerde installatie van een implementatie van een beschikbaarheidsgroep met Azure-quickstartsjablonen** |Het is nu mogelijk om het Windows-failovercluster te maken, SQL Server VM's toe te voegen, de listener te maken en de interne load balancer te configureren met twee Azure quickstart-sjablonen. Zie [Azure quickstart-sjablonen gebruiken om een groep Always On-beschikbaarheid voor SQL Server op een Azure VM te configureren](virtual-machines-windows-sql-availability-group-quickstart-template.md)voor meer informatie. | 
| **Automatische registratie bij de SQL VM-resourceprovider** | SQL Server VM's die na deze maand zijn geïmplementeerd, worden automatisch geregistreerd bij de nieuwe SQL Server-resourceprovider. SQL Server VM's die vóór deze maand zijn geïmplementeerd, moeten nog handmatig worden geregistreerd. Zie [Een virtuele SQL Server-machine in Azure registreren in Azure met de SQL VM-resourceprovider](virtual-machines-windows-sql-register-with-resource-provider.md)voor meer informatie.|
|**Nieuwe SQL VM-bronprovider** |  Een nieuwe resourceprovider (Microsoft.SqlVirtualMachine) biedt een beter beheer van uw SQL Server VM's. Zie [Een virtuele SQL Server-machine in Azure registreren in Azure bij de SQL VM-bronprovider](virtual-machines-windows-sql-register-with-resource-provider.md)voor meer informatie over het registreren van uw VM's. |
|**Switch-licentiemodel** | U nu schakelen tussen de pay-per-usage en bring-your-own-license-modellen voor uw SQL Server VM met behulp van de Azure CLI of PowerShell. Zie [Het licentiemodel voor een virtuele SQL Server-machine in Azure wijzigen](virtual-machines-windows-sql-ahb.md)voor meer informatie. | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Aanvullende bronnen

**Windows VM's:**

* [Overzicht van SQL Server op een Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Een SQL Server Windows VM inrichten](virtual-machines-windows-portal-sql-server-provision.md)
* [Een database migreren naar SQL Server op een Azure VM](virtual-machines-windows-migrate-sql.md)
* [Hoge beschikbaarheid en noodherstel voor SQL Server op Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Aanbevolen procedures voor prestaties voor SQL Server op Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Toepassingspatronen en ontwikkelingsstrategieën voor SQL Server op Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM's:**

* [Overzicht van SQL Server op een Linux VM](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Een virtuele SQL Server Linux-machine inrichten](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Veelgestelde vragen (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Documentatie voor SQL Server op Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
