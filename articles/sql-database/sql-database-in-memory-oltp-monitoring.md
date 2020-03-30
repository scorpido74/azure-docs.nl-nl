---
title: XTP-opslag in het geheugen controleren
description: Schatting en monitor XTP In-memory storage gebruik, capaciteit; capaciteitsfout oplossen 41823
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 22ff83b1ccd009624082e45073123a45006df70f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209439"
---
# <a name="monitor-in-memory-oltp-storage"></a>Monitor IN-Memory OLTP-opslag

Bij het gebruik van [In-Memory OLTP](sql-database-in-memory.md)bevinden gegevens in geheugengeoptimaliseerde tabellen en tabelvariabelen zich in OlTP-opslag in geheugengeheugen. Elke Premium- en Business Critical-servicelaag heeft een maximale OLTP-opslaggrootte in het geheugen. Zie [Op DTU gebaseerde resourcelimieten - enkele database,](sql-database-dtu-resource-limits-single-databases.md) [Op DTU gebaseerde resourcelimieten - elastische pools,](sql-database-dtu-resource-limits-elastic-pools.md)[vCore-gebaseerde resourcelimieten - afzonderlijke databases](sql-database-vcore-resource-limits-single-databases.md) en [vCore-gebaseerde resourcelimieten - elastische pools](sql-database-vcore-resource-limits-elastic-pools.md).

Zodra deze limiet is overschreden, kunnen invoegen en bijwerken bewerkingen beginnen te mislukken met fout 41823 voor afzonderlijke databases en fout 41840 voor elastische pools. Op dat moment moet u gegevens verwijderen om geheugen terug te winnen, of de servicelaag of de rekengrootte van uw database upgraden.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Bepalen of gegevens binnen de OLTP-opslagdop in geheugenpassen

Bepaal de opslaglimieten van de verschillende servicelagen. Zie [Op DTU gebaseerde resourcelimieten - enkele database,](sql-database-dtu-resource-limits-single-databases.md) [Op DTU gebaseerde resourcelimieten - elastische pools,](sql-database-dtu-resource-limits-elastic-pools.md)[vCore-gebaseerde resourcelimieten - afzonderlijke databases](sql-database-vcore-resource-limits-single-databases.md) en [vCore-gebaseerde resourcelimieten - elastische pools](sql-database-vcore-resource-limits-elastic-pools.md).

Het schatten van geheugenvereisten voor een voor geheugen geoptimaliseerde tabel werkt op dezelfde manier voor SQL Server als in Azure SQL Database. Neem een paar minuten om dat artikel op [MSDN](https://msdn.microsoft.com/library/dn282389.aspx)te herzien.

Tabel- en tabelvariabele rijen en indexen tellen mee voor de maximale grootte van gebruikersgegevens. Daarnaast heeft ALTER TABLE voldoende ruimte nodig om een nieuwe versie van de hele tabel en de bijbehorende indexen te maken.

## <a name="monitoring-and-alerting"></a>Bewaking en waarschuwingen
U het gebruik van opslag in het geheugen controleren als een percentage van de opslagdop voor uw rekengrootte in de [Azure-portal:](https://portal.azure.com/) 

1. Zoek in het databaseblad het vak Resourcegebruik en klik op Bewerken.
2. Selecteer de `In-Memory OLTP Storage percentage`statistiek .
3. Als u een waarschuwing wilt toevoegen, klikt u op het vak Resourcegebruik om het metrische blad te openen en klikt u vervolgens op Waarschuwing toevoegen.

Of gebruik de volgende query om het gebruik van opslag in het geheugen weer te geven:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Out-of-In-Memory OLTP-opslagsituaties corrigeren - Fouten 41823 en 41840

Als u de OLTP-opslagdop in uw database in het geheugen raakt, resulteert dit in INSERT-, UPDATE-, ALTER- en CREATE-bewerkingen die mislukken met foutbericht 41823 (voor afzonderlijke databases) of fout 41840 (voor elastische groepen). Beide fouten zorgen ervoor dat de actieve transactie wordt afgebroken.

Foutberichten 41823 en 41840 geven aan dat de geheugengeoptimaliseerde tabellen en tabelvariabelen in de database of groep de maximale OLTP-opslaggrootte in het geheugen hebben bereikt.

Als u deze fout wilt oplossen, gaat u als volgt te werk:

* Verwijder gegevens uit de voor het geheugen geoptimaliseerde tabellen, waardoor de gegevens mogelijk kunnen worden verwijderd naar traditionele tabellen op schijfbasis; Of
* Upgrade de servicelaag naar een met voldoende geheugenopslag voor de gegevens die u in geheugengeoptimaliseerde tabellen moet bewaren.

> [!NOTE] 
> In zeldzame gevallen kunnen fouten 41823 en 41840 van voorbijgaande aard zijn, wat betekent dat er voldoende in-memory OLTP-opslag beschikbaar is en het opnieuw proberen van de bewerking slaagt. We raden daarom aan om zowel de algehele beschikbare OLTP-opslag in het geheugen te controleren als opnieuw te proberen wanneer u voor het eerst fout 41823 of 41840 tegenkomt. Zie [Conflictdetectie en Logica opnieuw proberen met in-memory OLTP](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic)voor meer informatie over logica opnieuw proberen.

## <a name="next-steps"></a>Volgende stappen
Zie [Azure SQL Database bewaken met dynamische beheerweergaven](sql-database-monitoring-with-dmvs.md)voor bewakingsrichtlijnen.
