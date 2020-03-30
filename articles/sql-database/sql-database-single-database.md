---
title: Wat is een enkele database
description: Meer informatie over één database in Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: fc63de4057def632d3ac1980e8cb3eaedbff2175
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500757"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Wat is één database in Azure SQL Database

Met de optie voor één database-implementatie wordt een database in Azure SQL Database met een eigen set resources gemaakt en wordt deze beheerd via een SQL Database-server. Met één database is elke database geïsoleerd van elkaar en draagbaar, elk met een eigen servicelaag binnen het [Op DTU gebaseerde inkoopmodel](sql-database-service-tiers-dtu.md) of [vCore-gebaseerd inkoopmodel](sql-database-service-tiers-vcore.md) en een gegarandeerde rekengrootte.

> [!IMPORTANT]
> Eén database is een van de drie implementatieopties voor Azure SQL Database. De andere twee zijn [elastische pools](sql-database-elastic-pool.md) en [beheerde instantie](sql-database-managed-instance.md).
> [!NOTE]
> Zie [SQL Database-termenwoordenlijst](sql-database-glossary-terms.md) voor een woordenlijst met termen in Azure SQL Database

## <a name="dynamic-scalability"></a>Dynamische schaalbaarheid

U uw eerste app bouwen op een kleine, enkele database tegen lage kosten in de serverless compute-laag of een kleine rekengrootte in de ingerichte compute-laag. U wijzigt de [reken- of servicelaag](sql-database-single-database-scale.md) op elk gewenst moment handmatig of programmatisch om aan de behoeften van uw oplossing te voldoen. U kunt het prestatieniveau aanpassen zonder uitvaltijd voor uw app of voor uw klanten. Dankzij dynamische schaalbaarheid kan uw database op een transparante manier snel reageren op veranderende resourcevereisten en betaalt u alleen voor de resources die u nodig hebt wanneer u ze nodig.

## <a name="single-databases-and-elastic-pools"></a>Individuele databases en elastische pools

Een enkele database kan worden verplaatst naar of uit een [elastische groep](sql-database-elastic-pool.md) voor het delen van bronnen. Voor veel bedrijven en toepassingen is het kunnen maken van enkele databases en het naar wens omhoog of omlaag schalen van de prestaties al voldoende, vooral als de gebruikspatronen redelijk voorspelbaar zijn. Bij onvoorspelbare gebruikspatronen kan het echter lastig zijn uw kosten en bedrijfsmodel effectief te beheren. Elastische pools zijn ontworpen om dit probleem te verhelpen. Het concept is eenvoudig. U wijst prestatieresources toe aan een groep in plaats van aan een afzonderlijke database en betaalt voor de collectieve prestatiebronnen van de groep in plaats van voor afzonderlijke databaseprestaties.

## <a name="monitoring-and-alerting"></a>Bewaking en waarschuwingen

U maakt gebruik van de ingebouwde [prestatiebewaking-](sql-database-performance-guidance.md) en [waarschuwingstools,](sql-database-insights-alerts-portal.md)gecombineerd met de prestatiebeoordelingen. Met behulp van deze tools kunt u snel beoordelen wat de impact is van het aanpassen van de schaal op basis van uw huidige prestatiebehoeften of de prestatiebehoeften van uw project. Bovendien kan SQL Database [statistieken en bronlogboeken uitzenden](sql-database-metrics-diag-logging.md) voor eenvoudigere monitoring.

## <a name="availability-capabilities"></a>Beschikbaarheid

Afzonderlijke databases, elastische pools en beheerde instanties bieden allemaal veel beschikbaarheidskenmerken. Zie [Beschikbaarheidskenmerken](sql-database-technical-overview.md#availability-capabilities)voor meer informatie.

## <a name="transact-sql-differences"></a>Transact-SQL-verschillen

De meeste Transact-SQL-functies die toepassingen gebruiken, worden volledig ondersteund in zowel Microsoft SQL Server als Azure SQL Database. De belangrijkste SQL-componenten, zoals gegevenstypen, operatoren, tekenreeksen, rekenkundige, logische en cursorfuncties, werken bijvoorbeeld identiek in SQL Server en SQL Database. Er zijn echter een paar T-SQL-verschillen in DDL-elementen (data-definition taal) en DML (datamanipulatietaal) wat resulteert in T-SQL-instructies en query's die slechts gedeeltelijk worden ondersteund (die we later in dit artikel bespreken).
Daarnaast zijn er enkele functies en syntaxis die helemaal niet worden ondersteund omdat Azure SQL Database is ontworpen om functies te isoleren van afhankelijkheden van de hoofddatabase en het besturingssysteem. Als zodanig zijn de meeste activiteiten op serverniveau niet geschikt voor SQL Database. T-SQL-instructies en -opties zijn niet beschikbaar als ze opties op serverniveau, onderdelen van het besturingssysteem configureren of bestandssysteemconfiguratie opgeven. Wanneer dergelijke mogelijkheden vereist zijn, is een geschikt alternatief vaak op een andere manier beschikbaar vanuit SQL Database of vanuit een andere Azure-functie of -service.

Zie [Transact-SQL-verschillen oplossen tijdens de migratie naar SQL Database voor](sql-database-transact-sql-information.md)meer informatie.

## <a name="security"></a>Beveiliging

SQL Database biedt een reeks [ingebouwde beveiligings- en nalevingsfuncties](sql-database-security-overview.md) om uw toepassing te helpen voldoen aan verschillende beveiligings- en nalevingsvereisten.

> [!IMPORTANT]
> Azure SQL Database (alle implementatieopties) is gecertificeerd volgens een aantal nalevingsstandaarden. Zie het Microsoft [Azure Trust Center,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) waar u de meest recente lijst met SQL Database-nalevingscertificeringen vinden voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Om snel aan de slag te gaan met één database, begint u met de [quickstart-handleiding voor één database.](sql-database-single-database-quickstart-guide.md)
- Zie Migreren naar Azure SQL Database voor meer informatie over het migreren van een SQL [Server-database](sql-database-single-database-migrate.md)naar Azure.
- Zie [Functies voor](sql-database-features.md)informatie over ondersteunde functies.
