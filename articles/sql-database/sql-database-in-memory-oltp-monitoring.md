---
title: XTP in-Memory-opslag controleren
description: XTP in-Memory gebruiken om te schatten en te controleren, capaciteit; capaciteits fout 41823 oplossen
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79209439"
---
# <a name="monitor-in-memory-oltp-storage"></a>OLTP-opslag in het geheugen controleren

Bij gebruik [van in-Memory OLTP](sql-database-in-memory.md)worden gegevens in tabellen die zijn geoptimaliseerd voor geheugen en tabel variabelen bevinden zich in de OLTP-opslag in het geheugen. Elke Premium-en Bedrijfskritiek-servicelaag heeft een maximale in-Memory OLTP-opslag grootte. Zie [op DTU gebaseerde resource limieten-één data base](sql-database-dtu-resource-limits-single-databases.md), [op DTU gebaseerde resource limieten: elastische Pools](sql-database-dtu-resource-limits-elastic-pools.md),[vCore resource limieten-individuele data bases](sql-database-vcore-resource-limits-single-databases.md) en [vCore resource limieten: elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md).

Wanneer deze limiet wordt overschreden, worden INSERT-en update-bewerkingen mogelijk mislukt met fout 41823 voor afzonderlijke data bases en fout 41840 voor elastische Pools. Op dat moment moet u gegevens verwijderen om geheugen vrij te maken of de servicelaag of reken grootte van uw data base upgraden.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Bepalen of gegevens binnen de OLTP-opslag limiet van het geheugen passen

Bepaal de opslag limieten van de verschillende service lagen. Zie [op DTU gebaseerde resource limieten-één data base](sql-database-dtu-resource-limits-single-databases.md), [op DTU gebaseerde resource limieten: elastische Pools](sql-database-dtu-resource-limits-elastic-pools.md),[vCore resource limieten-individuele data bases](sql-database-vcore-resource-limits-single-databases.md) en [vCore resource limieten: elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md).

Het schatten van geheugen vereisten voor een tabel die is geoptimaliseerd voor geheugen werkt op dezelfde SQL Server manier als in Azure SQL Database. Neem een paar minuten de tijd om het artikel op [MSDN](https://msdn.microsoft.com/library/dn282389.aspx)te controleren.

Tabel-en tabel variabele rijen, evenals indexen, tellen bij de maximale grootte van de gebruikers gegevens. Daarnaast heeft ALTER TABLE voldoende ruimte nodig om een nieuwe versie van de volledige tabel en de bijbehorende indexen te maken.

## <a name="monitoring-and-alerting"></a>Bewaking en waarschuwingen
U kunt in-Memory opslag gebruiken als percentage van de opslag limiet voor de berekenings grootte in de [Azure Portal](https://portal.azure.com/): 

1. Ga op de Blade Data Base naar het vak resource gebruik en klik op bewerken.
2. Selecteer de metrische `In-Memory OLTP Storage percentage`gegevens.
3. Als u een waarschuwing wilt toevoegen, klikt u op het vak resource gebruik om de Blade metrische gegevens te openen en klikt u vervolgens op waarschuwing toevoegen.

U kunt ook de volgende query gebruiken om het gebruik van de opslag in het geheugen weer te geven:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Problemen met OLTP-opslag in het geheugen corrigeren-fouten 41823 en 41840

Zoek naar de OLTP-opslag limiet in het geheugen in uw data base resulteert in INSERT-, UPDATE-, ALTER-en CREATE-bewerkingen die mislukken met het fout bericht 41823 (voor afzonderlijke data bases) of fout 41840 (voor elastische Pools). Beide fouten zorgen ervoor dat de actieve trans actie wordt afgebroken.

Fout berichten 41823 en 41840 geven aan dat de door het geheugen geoptimaliseerde tabellen en tabel variabelen in de data base of pool de maximale OLTP-opslag grootte in het geheugen hebben bereikt.

Om deze fout op te lossen, kunt u het volgende doen:

* Verwijder gegevens uit de tabellen die zijn geoptimaliseerd voor geheugen, waardoor het mogelijk is om de gegevens te offloaden naar traditionele, op schijven gebaseerde tabellen. of
* Voer een upgrade uit van de servicelaag met voldoende in-Memory opslag voor de gegevens die u nodig hebt om in geheugen geoptimaliseerde tabellen te bewaren.

> [!NOTE] 
> In zeldzame gevallen kunnen fouten 41823 en 41840 tijdelijk zijn, wat betekent dat er voldoende beschik bare OLTP-opslag in het geheugen beschikbaar is en dat de bewerking opnieuw wordt uitgevoerd. Daarom raden we u aan om de algemeen beschik bare OLTP-opslag in het geheugen te bewaken en opnieuw te proberen wanneer de fout 41823 of 41840 voor het eerst optreedt. Zie [conflict detectie en poging logica met in-Memory OLTP](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic)voor meer informatie over de logica voor opnieuw proberen.

## <a name="next-steps"></a>Volgende stappen
Zie [Azure SQL database bewaken met dynamische beheer weergaven](sql-database-monitoring-with-dmvs.md)voor controle-instructies.
