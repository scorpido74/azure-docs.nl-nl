---
title: Azure Hybrid Benefit
description: Gebruik bestaande SQL Server-licenties voor SQL Database-kortingen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: d1a59e7ad86191bcc30b7d898d00f327c20fbc5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945625"
---
# <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

In de ingerichte compute-laag van het op vCore gebaseerde inkoopmodel u uw bestaande licenties inruilen voor gereduceerde tarieven op SQL Database met [Azure Hybrid Benefit voor SQL Server.](https://azure.microsoft.com/pricing/hybrid-benefit/) Met dit Azure-voordeel u tot 30 procent of zelfs hoger besparen op Azure SQL Database door uw on-premises SQL Server-licenties te gebruiken met Software Assurance. Gebruik de Azure Hybrid Benefit Calculator via de eerder genoemde koppeling voor de juiste waarden. 

> [!NOTE]
> Voor de overstap naar Azure Hybrid Benefit is geen downtime nodig.

![Prijzen](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Een licentiemodel kiezen

Met Azure Hybrid Benefit u ervoor kiezen om alleen te betalen voor de onderliggende Azure-infrastructuur met behulp van uw bestaande SQL Server-licentie voor de SQL-databaseengine zelf (Base Compute-prijzen), of u betalen voor zowel de onderliggende infrastructuur als de SQL Server licentie (Licentie-inbegrepen prijzen).

U uw licentiemodel kiezen of wijzigen met behulp van de Azure-portal of met een van de volgende API's:

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Ga als een nieuwe toepassing op powershell in of update het licentietype:

- [Nieuwe AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [Nieuw-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Ga als een overzicht van het licentietype of deze bijwerken met Azure CLI:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi maken](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST-API](#tab/rest)

Ga als een te meer met ingeamtoam voor het instellen of bijwerken van het licentietype met de REST API:

- [Databases - Maken of bijwerken](/rest/api/sql/databases/createorupdate)
- [Databases - Bijwerken](/rest/api/sql/databases/update)
- [Beheerde exemplaren - Maken of bijwerken](/rest/api/sql/managedinstances/createorupdate)
- [Beheerde exemplaren - Bijwerken](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>Volgende stappen

- Zie [De juiste implementatieoptie kiezen in Azure SQL](sql-database-paas-vs-sql-server-iaas.md)als u wilt kiezen tussen de implementatieopties van SQL-database.
- Zie [Azure SQL Database-functies](sql-database-features.md)voor een vergelijking van SQL Database-functies.
