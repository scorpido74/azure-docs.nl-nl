---
title: Transparent Data Encryption voor Stretch Database inschakelen
description: Transparent Data Encryption (TDE) inschakelen voor SQL Server Stretch Database in azure
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/14/2016
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 465338bb793ad7b4fc406e9d1c073b9dc0dc06c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "74034016"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Transparent Data Encryption (TDE) inschakelen voor Stretch Database in azure
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Met Transparent Data Encryption (TDE) kunt u zich beschermen tegen de dreiging van schadelijke activiteiten door real-time versleuteling en ontsleuteling van de data base, gekoppelde back-ups en transactie logboek bestanden in rust te brengen zonder dat de toepassing hoeft te worden gewijzigd.

TDE versleutelt de opslag van een hele database met behulp van een symmetrische sleutel (de zogeheten databaseversleutelingssleutel). De database versleutelings sleutel wordt beveiligd door een ingebouwd server certificaat. Het ingebouwde server certificaat is uniek voor elke Azure-server. Micro soft roteert deze certificaten ten minste elke 90 dagen automatisch. Zie [transparent Data Encryption (TDE)]voor een algemene beschrijving van TDe.

## <a name="enabling-encryption"></a>Versleuteling inschakelen
Ga als volgt te werk om TDE in te scha kelen voor een Azure-Data Base waarin de gegevens worden opgeslagen die zijn gemigreerd vanuit een SQL Server Data Base met stretch-functionaliteit:

1. Open de data base in de [Azure Portal](https://portal.azure.com)
2. Klik op de Blade Data Base op de knop **instellingen**
3. Selecteer de optie voor **transparante gegevens versleuteling**![][1]
4. Selecteer de instelling **bij** en selecteer vervolgens **Opslaan** .
   ![][2]

## <a name="disabling-encryption"></a>Versleuteling uitschakelen
Ga als volgt te werk om TDE uit te scha kelen voor een Azure-Data Base waarin de gegevens worden opgeslagen die zijn gemigreerd vanuit een SQL Server Data Base met stretch-functionaliteit:

1. Open de data base in de [Azure Portal](https://portal.azure.com)
2. Klik op de Blade Data Base op de knop **instellingen**
3. Selecteer de optie voor **transparante gegevens versleuteling**
4. Selecteer de instelling **uit** en selecteer vervolgens **Opslaan** .

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
