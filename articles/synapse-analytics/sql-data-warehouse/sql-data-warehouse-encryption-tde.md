---
title: Transparante gegevensversleuteling (portal)
description: Transparante gegevensversleuteling (TDE) in Azure Synapse Analytics
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 669a02bb45ef61d1e66d719850defcfaa19b838d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350538"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Aan de slag met Transparent Data Encryption (TDE)
> [!div class="op_single_selector"]
> * [Overzicht van beveiliging](sql-data-warehouse-overview-manage-security.md)
> * [Verificatie](sql-data-warehouse-authentication.md)
> * [Versleuteling (portal)](sql-data-warehouse-encryption-tde.md)
> * [Versleuteling (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Vereiste machtigingen
Als u Transparent Data Encryption (TDE) wilt inschakelen, moet u een beheerder of lid zijn van de rol dbmanager.

## <a name="enabling-encryption"></a>Versleuteling inschakelen
Volg de onderstaande stappen om TDE in te schakelen:

1. De database openen in de [Azure-portal](https://portal.azure.com)
2. Klik in het databaseblad op de knop **Instellingen**
3. De ![ **portalinstellingen voor transparante gegevensversleuteling** selecteren](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Selecteer **On** de ![instellingen voor het instellen van portal op](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. Selecteer **Portal-instellingen opslaan**
   ![](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Versleuteling uitschakelen
Ga als volgt te werk om TDE uit te schakelen:

1. De database openen in de [Azure-portal](https://portal.azure.com)
2. Klik in het databaseblad op de knop **Instellingen**
3. De ![ **portalinstellingen voor transparante gegevensversleuteling** selecteren](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. De instellingen ![voor de instelling **strekken** uit selecteren](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. Selecteer **Portal-instelling opslaan**
   ![opslaan 2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>Versleuteling DMVs
Versleuteling kan worden bevestigd met de volgende DMVs:

* [sys.databases]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
