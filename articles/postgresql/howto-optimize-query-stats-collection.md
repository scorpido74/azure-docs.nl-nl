---
title: Verzameling query statistieken optimaliseren-Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u de verzameling query statistieken kunt optimaliseren op een Azure Database for PostgreSQL-één server
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: a4c2051a3f52ae363d8dc704aef790d8ce77efbd
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116196"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Verzameling query statistieken optimaliseren op een Azure Database for PostgreSQL-één server
In dit artikel wordt beschreven hoe u de verzameling query statistieken op een Azure Database for PostgreSQL server optimaliseert.

## <a name="use-pg_stats_statements"></a>Pg_stats_statements gebruiken
**Pg_stat_statements** is een postgresql-extensie die standaard is ingeschakeld in azure database for PostgreSQL. De uitbrei ding biedt een manier om uitvoerings statistieken bij te houden voor alle SQL-instructies die worden uitgevoerd door een server. Deze module is gekoppeld aan elke uitvoering van query's en wordt geleverd met een niet-trivial prestatie kosten. Het inschakelen van **pg_stat_statements** dwingt query tekst af op het schrijven naar bestanden op schijf.

Als u unieke query's met lange query tekst hebt of als u **pg_stat_statements**niet actief bewaakt, schakelt u **pg_stat_statements** uit voor de beste prestaties. Als u dit wilt doen, wijzigt u de instelling in `pg_stat_statements.track = NONE` .

Sommige werk belastingen van de klant hebben een prestatie verbetering van 50% gezien wanneer **pg_stat_statements** is uitgeschakeld. Wanneer u pg_stat_statements uitschakelt, is het niet meer mogelijk om prestatie problemen op te lossen.

Instellen `pg_stat_statements.track = NONE` :

- Ga in het Azure Portal naar de [pagina postgresql resource management en selecteer de Blade Server parameters](howto-configure-server-parameters-using-portal.md).

  ![Blade met PostgreSQL-server parameters](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Gebruik de [Azure cli](howto-configure-server-parameters-using-cli.md) AZ post gres-server configuratie ingesteld op `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE` .

## <a name="use-the-query-store"></a>Het query archief gebruiken 
De functie [query Store](concepts-query-store.md) in azure database for PostgreSQL biedt een effectievere methode om query statistieken bij te houden. Deze functie wordt aangeraden als alternatief voor het gebruik van *pg_stats_statements*. 

## <a name="next-steps"></a>Volgende stappen
U kunt de instelling `pg_stat_statements.track = NONE` gebruiken in de [Azure Portal](howto-configure-server-parameters-using-portal.md) of via de [Azure cli](howto-configure-server-parameters-using-cli.md).

Zie voor meer informatie: 
- [Query Store-gebruiksscenario's](concepts-query-store-scenarios.md) 
- [Best practices voor Query Store](concepts-query-store-best-practices.md) 
