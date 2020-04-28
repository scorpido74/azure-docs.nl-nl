---
title: Key Vault integreren met SQL Server op Windows-Vm's in azure (Resource Manager) | Microsoft Docs
description: Meer informatie over het automatiseren van de configuratie van SQL Server versleuteling voor gebruik met Azure Key Vault. In dit onderwerp wordt uitgelegd hoe u Azure Key Vault integratie kunt gebruiken met SQL Server virtuele machines die zijn gemaakt met Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: cad70169e88e1fafa129c02f30d5288d39e30a9c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "70102138"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Azure Key Vault-integratie configureren voor SQL Server op virtuele Azure-machines (Resource Manager)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Klassiek](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Overzicht
Er zijn meerdere SQL Server versleutelings functies, zoals [transparante gegevens versleuteling (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [versleuteling op kolom niveau (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)en [back-upcodering](https://msdn.microsoft.com/library/dn449489.aspx). Voor deze coderings vormen moet u de cryptografische sleutels die u voor versleuteling gebruikt, beheren en opslaan. De Azure Key Vault-service (Azure) is ontworpen voor het verbeteren van de beveiliging en het beheer van deze sleutels op een veilige en Maxi maal beschik bare locatie. Met de [SQL Server-connector](https://www.microsoft.com/download/details.aspx?id=45344) kan SQL Server deze sleutels vanuit Azure Key Vault gebruiken.

Als u SQL Server met on-premises machines uitvoert, zijn er [stappen die u kunt volgen om toegang te krijgen tot Azure Key Vault vanaf uw on-premises SQL Server computer](https://msdn.microsoft.com/library/dn198405.aspx). Maar voor SQL Server in azure-Vm's kunt u tijd besparen door gebruik te maken van de functie voor *Azure Key Vault integratie* .

Als deze functie is ingeschakeld, wordt de SQL Server-connector automatisch geïnstalleerd, wordt de EKM-provider geconfigureerd voor toegang tot Azure Key Vault en wordt de referentie gemaakt waarmee u toegang krijgt tot uw kluis. Als u de stappen in de eerder genoemde on-premises documentatie hebt bekeken, kunt u zien dat deze functie stap 2 en 3 automatiseert. Het enige wat u nog steeds hand matig moet doen, is door de sleutel kluis en sleutels te maken. Van daaruit wordt de volledige installatie van uw SQL-VM geautomatiseerd. Zodra deze functie is voltooid, kunt u T-SQL-instructies uitvoeren om te beginnen met het versleutelen van uw data bases of back-ups zoals u dat gewend bent.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > EKM-provider versie 1.0.4.0 wordt geïnstalleerd op de SQL Server virtuele machine via de [SQL IaaS-extensie](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). Bij het bijwerken van de SQL IaaS-extensie wordt de provider versie niet bijgewerkt. Overweeg, indien nodig, hand matig een upgrade van de versie van de EKM-provider (bijvoorbeeld bij het migreren naar een beheerd exemplaar van SQL).


## <a name="enabling-and-configuring-akv-integration"></a>Azure-integratie inschakelen en configureren
U kunt de Azure-integratie tijdens het inrichten inschakelen of configureren voor bestaande virtuele machines.

### <a name="new-vms"></a>Nieuwe Vm's
Als u een nieuwe SQL Server virtuele machine met Resource Manager inricht, biedt de Azure Portal een manier om Azure Key Vault integratie in te scha kelen. De functie Azure Key Vault is alleen beschikbaar voor de edities Enter prise, Developer en Evaluation van SQL Server.

![Integratie van Azure Sleutelkluis in SQL](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Zie [een SQL Server virtuele machine inrichten in de Azure Portal](virtual-machines-windows-portal-sql-server-provision.md)voor een gedetailleerd overzicht van het inrichten.

### <a name="existing-vms"></a>Bestaande Vm's

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Voor bestaande SQL Server virtuele machines opent u de [resource virtuele SQL-machines](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) en selecteert u **beveiliging** onder **instellingen**. Selecteer **inschakelen** om Azure Key Vault integratie in te scha kelen. 

![SQL Azure-integratie voor bestaande Vm's](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Wanneer u klaar bent, selecteert u de knop **Toep assen** aan de onderkant van de pagina **beveiliging** om uw wijzigingen op te slaan.

> [!NOTE]
> De referentie naam die we hier maken, wordt later aan een SQL-aanmelding toegewezen. Hiermee kan de SQL-aanmelding toegang krijgen tot de sleutel kluis. 


> [!NOTE]
> U kunt ook Azure-integratie configureren met behulp van een sjabloon. Zie Azure Quick Start- [sjabloon voor Azure Key Vault-integratie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update)voor meer informatie.


[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
