---
title: Transparent Data Encryption (Portal)
description: Transparent Data Encryption (TDE) in azure Synapse Analytics
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 0c6fc0cdba4607bba55383f2662257322ef23aa2
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85212238"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Aan de slag met Transparent Data Encryption (TDE)

> [!div class="op_single_selector"]
>
> * [Beveiligings overzicht](sql-data-warehouse-overview-manage-security.md)
> * [Verificatie](sql-data-warehouse-authentication.md)
> * [Versleuteling (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Versleuteling (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Vereiste machtigingen

Als u Transparent Data Encryption (TDE) wilt inschakelen, moet u een beheerder of lid van de DBManager-rol zijn.

## <a name="enabling-encryption"></a>Versleuteling inschakelen

Volg de onderstaande stappen om TDE in te scha kelen:

1. Open de data base in de [Azure Portal](https://portal.azure.com)
2. Klik op de Blade Data Base op de knop **instellingen**
3. Selecteer de optie voor het **transparant gegevens versleutelen** ![ Portal instellingen](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Selecteer de instellingen voor het instellen **van** de ![ Portal op](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. Selecteer **Save** 
    ![ Portal instellingen opslaan opslaan](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Versleuteling uitschakelen

Volg de onderstaande stappen om TDE uit te scha kelen:

1. Open de data base in de [Azure Portal](https://portal.azure.com)
2. Klik op de Blade Data Base op de knop **instellingen**
3. Selecteer de optie voor het **transparant gegevens versleutelen** ![ Portal instellingen](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Schakel de Portal-instellingen **uit** instellen ![ uit](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. Selecteer **Save** 
    ![ Portal instelling opslaan 2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>Versleuteling Dmv's

Versleuteling kan worden bevestigd met de volgende Dmv's:

* [sys. data bases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys. dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
