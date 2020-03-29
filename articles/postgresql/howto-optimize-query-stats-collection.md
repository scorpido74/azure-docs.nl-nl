---
title: Querystatistiekenverzameling optimaliseren - Azure Database voor PostgreSQL - Single Server
description: In dit artikel wordt beschreven hoe u de verzameling querystatistieken optimaliseren in een Azure-database voor PostgreSQL - Single Server
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: f467f01118470eb51f7decf3bd6457917c566723
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770166"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Verzameling querystatistieken optimaliseren op een Azure-database voor PostgreSQL - Enkele server
In dit artikel wordt beschreven hoe u de verzameling querystatistieken optimaliseren op een Azure-database voor PostgreSQL-server.

## <a name="use-pg_stats_statements"></a>Gebruik pg_stats_statements
**Pg_stat_statements** is een PostgreSQL-extensie die standaard is ingeschakeld in Azure Database voor PostgreSQL. De extensie biedt een middel om uitvoeringsstatistieken bij te houden voor alle SQL-instructies die door een server worden uitgevoerd. Deze module haakt in elke query uitvoering en wordt geleverd met een niet-triviale prestatiekosten. Het inschakelen **van pg_stat_statements** querytekstschrijft naar bestanden op schijf.

Als u unieke query's hebt met lange querytekst of als u **pg_stat_statements**niet actief controleert, schakelt u **pg_stat_statements** uit voor de beste prestaties. Wijzig hiervoor de instelling `pg_stat_statements.track = NONE`in .

Sommige workloads van klanten hebben tot een prestatieverbetering van 50 procent gezien wanneer **pg_stat_statements** is uitgeschakeld. De afweging die u maakt wanneer u pg_stat_statements uitschakelt, is het onvermogen om prestatieproblemen op te lossen.

Instellen `pg_stat_statements.track = NONE`:

- Ga in de Azure-portal naar de [pagina PostgreSQL-resourcebeheer en selecteer het blade van serverparameters.](howto-configure-server-parameters-using-portal.md)

  ![PostgreSQL-serverparameterblade](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Gebruik de configuratie van de Azure `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`CLI az-postgresserver ingesteld op . [Azure CLI](howto-configure-server-parameters-using-cli.md)

## <a name="use-the-query-store"></a>Het queryarchief gebruiken 
De [functie Query Store](concepts-query-store.md) in Azure Database voor PostgreSQL biedt een effectievere methode om querystatistieken bij te houden. We raden deze functie aan als alternatief voor het gebruik van *pg_stats_statements.* 

## <a name="next-steps"></a>Volgende stappen
Overweeg `pg_stat_statements.track = NONE` in te stellen in de [Azure-portal](howto-configure-server-parameters-using-portal.md) of met de [Azure CLI](howto-configure-server-parameters-using-cli.md).

Zie voor meer informatie: 
- [Gebruiksscenario's voor queryopslag](concepts-query-store-scenarios.md) 
- [Best practices voor Query Store](concepts-query-store-best-practices.md) 
