---
title: Transparent Data Encryption (Portal)
description: Transparent Data Encryption (TDE) in azure Synapse Analytics
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: c121f9c16895a749922525d1ba85ee2c2e60cfb0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195853"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Aan de slag met Transparent Data Encryption (TDE)
> [!div class="op_single_selector"]
> * [Beveiligings overzicht](sql-data-warehouse-overview-manage-security.md)
> * [Verificatie](sql-data-warehouse-authentication.md)
> * [Versleuteling (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Versleuteling (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Vereiste machtigingen
Als u Transparent Data Encryption (TDE) wilt inschakelen, moet u een beheerder of lid van de DBManager-rol zijn.

## <a name="enabling-encryption"></a>Versleuteling inschakelen
Volg de onderstaande stappen om TDE in te scha kelen:

1. Open de data base in de [Azure Portal](https://portal.azure.com)
2. Klik op de Blade Data Base op de knop **instellingen**
3. Selecteer de optie voor **transparante gegevens versleuteling** ![][1]
4. Selecteer de **on** -instelling ![][2]
5. 
   ![][3] **Opslaan** selecteren  

## <a name="disabling-encryption"></a>Versleuteling uitschakelen
Volg de onderstaande stappen om TDE uit te scha kelen:

1. Open de data base in de [Azure Portal](https://portal.azure.com)
2. Klik op de Blade Data Base op de knop **instellingen**
3. Selecteer de optie voor **transparante gegevens versleuteling** ![][1]
4. Selecteer de instelling **uit** ![][4]
5. 
   ![][5] **Opslaan** selecteren  

## <a name="encryption-dmvs"></a>Versleuteling Dmv's
Versleuteling kan worden bevestigd met de volgende Dmv's:

* [sys. data bases]
* [sys. dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys. data bases]: https://msdn.microsoft.com/library/ms178534.aspx
[sys. dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
