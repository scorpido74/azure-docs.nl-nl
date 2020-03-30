---
title: Key Vault integreren met SQL Server in Windows VM's in Azure (Resource Manager) | Microsoft Documenten
description: Meer informatie over het automatiseren van de configuratie van SQL Server-versleuteling voor gebruik met Azure Key Vault. In dit onderwerp wordt uitgelegd hoe u Azure Key Vault Integration gebruiken met virtuele SQL Server-machines die zijn gemaakt met Resource Manager.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70102138"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Azure Key Vault-integratie configureren voor SQL Server op virtuele Azure-machines (Resource Manager)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Klassiek](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Overzicht
Er zijn meerdere SQL Server-versleutelingsfuncties, zoals [transparante gegevensversleuteling (TDE),](https://msdn.microsoft.com/library/bb934049.aspx) [versleuteling op kolomniveau (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)en [back-upversleuteling](https://msdn.microsoft.com/library/dn449489.aspx). Deze vormen van versleuteling vereisen dat u de cryptografische sleutels beheert en opslaat die u voor versleuteling gebruikt. De AKV-service (Azure Key Vault) is ontworpen om de beveiliging en het beheer van deze sleutels op een veilige en zeer beschikbare locatie te verbeteren. Met [de SQL Server Connector](https://www.microsoft.com/download/details.aspx?id=45344) kan SQL Server deze sleutels gebruiken vanuit Azure Key Vault.

Als u SQL Server met on-premises machines uitvoert, zijn er [stappen die u volgen om toegang te krijgen tot Azure Key Vault vanaf uw on-premises SQL Server-machine.](https://msdn.microsoft.com/library/dn198405.aspx) Maar voor SQL Server in Azure VM's u tijd besparen met de azure *key vault-integratiefunctie.*

Wanneer deze functie is ingeschakeld, installeert deze automatisch de SQL Server Connector, configureert u de EKM-provider om toegang te krijgen tot Azure Key Vault en maakt u de referenties waarmee u toegang hebt tot uw kluis. Als u de stappen in de eerder genoemde on-premises documentatie bekijkt, u zien dat deze functie stap 2 en 3 automatiseert. Het enige wat je nog handmatig hoeft te doen, is het maken van de sleutelkluis en toetsen. Van daaruit wordt de volledige installatie van uw SQL VM geautomatiseerd. Zodra deze functie deze installatie heeft voltooid, u T-SQL-instructies uitvoeren om te beginnen met het versleutelen van uw databases of back-ups zoals u dat normaal zou doen.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > EKM Provider versie 1.0.4.0 is geïnstalleerd op de SQL Server VM via de [SQL IaaS extensie](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). Het upgraden van de SQL IaaS-extensie werkt de providerversie niet bij. Overweeg de eKM-providerversie handmatig te upgraden indien nodig (bijvoorbeeld bij het migreren naar een SQL Managed Instance).


## <a name="enabling-and-configuring-akv-integration"></a>AKV-integratie inschakelen en configureren
U AKV-integratie inschakelen tijdens het inrichten of configureren voor bestaande VM's.

### <a name="new-vms"></a>Nieuwe VM's
Als u een nieuwe VIRTUELE SQL Server-machine indient met Resource Manager, biedt de Azure-portal een manier om Azure Key Vault-integratie in te schakelen. De Azure Key Vault-functie is alleen beschikbaar voor de Enterprise-, Developer- en Evaluation Editions van SQL Server.

![Integratie van Azure Sleutelkluis in SQL](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Zie [Een virtuele SQL Server-machine inrichten in de Azure-portal](virtual-machines-windows-portal-sql-server-provision.md)voor een gedetailleerde doorloop van de inrichting.

### <a name="existing-vms"></a>Bestaande VM's

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Open uw [SQL virtual machines-bron](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) voor bestaande virtuele SQL Server-machines en selecteer **Beveiliging** onder **Instellingen**. Selecteer **Inschakelen** om Azure Key Vault-integratie in te schakelen. 

![SQL AKV-integratie voor bestaande VM's](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Als u klaar bent, selecteert u de knop **Toepassen** onder aan de pagina **Beveiliging** om de wijzigingen op te slaan.

> [!NOTE]
> De referentienaam die we hier hebben gemaakt, wordt later toegewezen aan een SQL-login. Hierdoor kan de SQL-login toegang krijgen tot de sleutelkluis. 


> [!NOTE]
> U akv-integratie ook configureren met behulp van een sjabloon. Zie [Azure quickstart-sjabloon voor Azure Key Vault-integratie voor](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update)meer informatie.


[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
