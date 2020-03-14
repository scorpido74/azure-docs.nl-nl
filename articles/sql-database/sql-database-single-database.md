---
title: Wat is een enkele data base?
description: Meer informatie over de afzonderlijke data base in Azure SQL Database
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
ms.openlocfilehash: 7bca36bdb41c5800a8718f76d48afcfde6f9333e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79208841"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Wat is een enkele data base in Azure SQL Database

De implementatie optie voor één data base maakt een data base in Azure SQL Database met een eigen set resources en wordt beheerd via een SQL Database-Server. Met één data base is elke Data Base geïsoleerd van elkaar en draagbaar, elk met een eigen servicelaag in het [op DTU gebaseerde aankoop model](sql-database-service-tiers-dtu.md) of [op vCore gebaseerde aankoop model](sql-database-service-tiers-vcore.md) en een gegarandeerde reken grootte.

> [!IMPORTANT]
> Eén data base is een van de drie implementatie opties voor Azure SQL Database. De andere twee zijn [elastische Pools](sql-database-elastic-pool.md) en een [beheerd exemplaar](sql-database-managed-instance.md).
> [!NOTE]
> Zie voor een verklarende woorden lijst in Azure SQL Database [SQL database termen woorden lijst](sql-database-glossary-terms.md)

## <a name="dynamic-scalability"></a>Dynamische schaalbaarheid

U kunt uw eerste app bouwen op een kleine, afzonderlijke data base tegen lage kosten in de serverloze Compute-laag of een kleine reken grootte in de ingerichte Compute-laag. U kunt de [Compute-of servicelaag](sql-database-single-database-scale.md) hand matig of via een programma op elk gewenst moment wijzigen om te voldoen aan de behoeften van uw oplossing. U kunt het prestatieniveau aanpassen zonder uitvaltijd voor uw app of voor uw klanten. Dankzij dynamische schaalbaarheid kan uw database op een transparante manier snel reageren op veranderende resourcevereisten en betaalt u alleen voor de resources die u nodig hebt wanneer u ze nodig.

## <a name="single-databases-and-elastic-pools"></a>Individuele databases en elastische pools

U kunt één data base verplaatsen naar of van een [elastische pool](sql-database-elastic-pool.md) voor het delen van resources. Voor veel bedrijven en toepassingen is het kunnen maken van enkele databases en het naar wens omhoog of omlaag schalen van de prestaties al voldoende, vooral als de gebruikspatronen redelijk voorspelbaar zijn. Bij onvoorspelbare gebruikspatronen kan het echter lastig zijn uw kosten en bedrijfsmodel effectief te beheren. Elastische Pools zijn ontworpen om dit probleem op te lossen. Het concept is eenvoudig. U wijst prestatie resources toe aan een pool in plaats van een afzonderlijke data base en betaalt voor de collectieve prestaties van de pool in plaats van voor de prestaties van één data base.

## <a name="monitoring-and-alerting"></a>Bewaking en waarschuwingen

U gebruikt de ingebouwde [hulpprogram ma's](sql-database-insights-alerts-portal.md)voor [prestatie bewaking](sql-database-performance.md) en waarschuwingen, gecombineerd met de prestatie classificaties. Met behulp van deze tools kunt u snel beoordelen wat de impact is van het aanpassen van de schaal op basis van uw huidige prestatiebehoeften of de prestatiebehoeften van uw project. Daarnaast kunnen SQL Database [metrische gegevens en bron logboeken verzenden](sql-database-metrics-diag-logging.md) voor een betere controle.

## <a name="availability-capabilities"></a>Beschikbaarheid

Afzonderlijke data bases, elastische Pools en beheerde exemplaren bieden allemaal een groot aantal beschikbaarheids kenmerken. Zie [beschikbaarheids kenmerken](sql-database-technical-overview.md#availability-capabilities)voor meer informatie.

## <a name="transact-sql-differences"></a>Verschillen in Transact-SQL

De meeste Transact-SQL-functies die toepassingen gebruiken, worden volledig ondersteund in zowel Microsoft SQL Server als Azure SQL Database. De belangrijkste SQL-onderdelen, zoals gegevens typen, Opera Tors, teken reeks, reken kundige, logische en cursor functies, werken bijvoorbeeld hetzelfde in SQL Server en SQL Database. Er zijn echter enkele T-SQL-verschillen in DDL-elementen (Data Definition Language) en DML (Data Manipulation Language) die resulteren in T-SQL-instructies en query's die slechts gedeeltelijk worden ondersteund (wat verderop in dit artikel wordt besproken).
Bovendien zijn er enkele functies en syntaxis die niet wordt ondersteund omdat Azure SQL Database is ontworpen om functies te isoleren op basis van afhankelijkheden van de hoofd database en het besturings systeem. Daarom zijn de meeste activiteiten op server niveau niet geschikt voor SQL Database. T-SQL-instructies en-opties zijn niet beschikbaar als ze opties op server niveau, onderdelen van het besturings systeem of de configuratie van het bestands systeem opgeven. Wanneer dergelijke mogelijkheden vereist zijn, is een geschikt alternatief op een andere manier vaak beschikbaar vanaf SQL Database of vanuit een andere Azure-functie of-service.

Zie voor meer informatie [Transact-SQL-verschillen oplossen tijdens de migratie naar SQL database](sql-database-transact-sql-information.md).

## <a name="security"></a>Beveiliging

SQL Database biedt een reeks ingebouwde functies voor [beveiliging en naleving](sql-database-security-overview.md) om uw toepassing te helpen voldoen aan verschillende vereisten op het gebied van beveiliging en naleving.

> [!IMPORTANT]
> Azure SQL Database (alle implementatie opties), is gecertificeerd op basis van een aantal nalevings standaarden. Zie het [vertrouwens centrum van Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) voor meer informatie over de meest recente lijst met SQL database nalevings certificeringen.

## <a name="next-steps"></a>Volgende stappen

- Als u snel aan de slag wilt gaan met één data base, begint u met de [Snelstartgids voor één data base](sql-database-single-database-quickstart-guide.md).
- Zie [migreren naar Azure SQL database](sql-database-single-database-migrate.md)voor meer informatie over het migreren van een SQL Server Data Base naar Azure.
- Zie [Functies](sql-database-features.md) voor meer informatie over ondersteunde functies.
