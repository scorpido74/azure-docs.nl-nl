---
title: Transparante gegevensversleuteling inschakelen voor stretchdatabase
description: Transparent Data Encryption (TDE) inschakelen voor SQL Server Stretch Database op Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034016"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Transparent Data Encryption (TDE) inschakelen voor Stretch Database op Azure
> [!div class="op_single_selector"]
> * [Azure-portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparent Data Encryption (TDE) helpt beschermen tegen de dreiging van kwaadaardige activiteiten door het uitvoeren van real-time encryptie en decryptie van de database, bijbehorende back-ups, en transactie log bestanden in rust zonder dat wijzigingen in de toepassing.

TDE versleutelt de opslag van een hele database met behulp van een symmetrische sleutel (de zogeheten databaseversleutelingssleutel). De databaseversleutelingssleutel wordt beschermd door een ingebouwd servercertificaat. Het ingebouwde servercertificaat is uniek voor elke Azure-server. Microsoft draait deze certificaten automatisch ten minste om de 90 dagen. Zie [Transparante gegevensversleuteling (TDE)]voor een algemene beschrijving van TDE.

## <a name="enabling-encryption"></a>Versleuteling inschakelen
Ga als volgt te werk om TDE in te schakelen voor een Azure-database die de gegevens opslaat die zijn gemigreerd uit een SQL Server-database met Stretch:

1. De database openen in de [Azure-portal](https://portal.azure.com)
2. Klik in het databaseblad op de knop **Instellingen**
3. Selecteer de optie **Transparante gegevensversleuteling**![][1]
4. Selecteer de instelling **Aan** en selecteer **Opslaan**
   ![][2]

## <a name="disabling-encryption"></a>Versleuteling uitschakelen
Ga als volgt te werk om TDE uit te schakelen voor een Azure-database die de gegevens opslaat die zijn gemigreerd uit een SQL Server-database met Stretch:

1. De database openen in de [Azure-portal](https://portal.azure.com)
2. Klik in het databaseblad op de knop **Instellingen**
3. Selecteer de optie **Transparante gegevensversleuteling**
4. Selecteer de **instelling Uit** en selecteer **Opslaan**

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
