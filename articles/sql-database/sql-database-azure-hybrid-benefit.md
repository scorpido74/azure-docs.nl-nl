---
title: Azure Hybrid Benefit
description: Gebruik bestaande SQL Server licenties voor SQL Database kortingen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: 4aab699ff0141b648066fae0de445c4467be7509
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048394"
---
# <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

In de ingerichte Compute-laag van het op vCore gebaseerde aankoop model kunt u uw bestaande licenties uitwisselen voor kortings tarieven op SQL Database met behulp van [Azure Hybrid Benefit voor SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Met dit voor deel van Azure kunt u tot wel 30% besparen op Azure SQL Database met behulp van uw on-premises SQL Server licenties met Software Assurance. 

> [!NOTE]
> Voor het wijzigen van Azure Hybrid Benefit is geen uitval tijd vereist.

![koers](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Een licentie model kiezen

Met Azure Hybrid Benefit kunt u ervoor kiezen om alleen te betalen voor de onderliggende Azure-infra structuur met behulp van uw bestaande SQL Server licentie voor de SQL database engine zelf (prijzen voor basis berekeningen). u kunt ook betalen voor de onderliggende infra structuur en de SQL Server licentie (inbegrepen prijzen in licentie).

U kunt uw licentie model kiezen of wijzigen met behulp van de Azure Portal of door gebruik te maken van een van de volgende Api's:

- Het licentie type instellen of bijwerken met behulp van Power shell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Het licentie type instellen of bijwerken met behulp van de Azure CLI:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [AZ SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [AZ SQL mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Het licentie type instellen of bijwerken met behulp van de REST API:

  - [Data bases: maken of bijwerken](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Data bases-bijwerken](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Beheerde instanties: maken of bijwerken](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Beheerde instanties-bijwerken](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="next-steps"></a>Volgende stappen

- Zie [de optie juiste implementatie kiezen in Azure SQL](sql-database-paas-vs-sql-server-iaas.md)om te kiezen tussen de SQL database-implementatie opties.
- Zie [Azure SQL database-functies](sql-database-features.md)voor een vergelijking van SQL database functies.
