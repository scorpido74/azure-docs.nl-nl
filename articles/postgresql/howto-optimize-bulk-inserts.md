---
title: Bulkinserts optimaliseren - Azure Database voor PostgreSQL - Single Server
description: In dit artikel wordt beschreven hoe u bewerkingen voor het invoegen van bulk optimaliseren in een Azure-database voor PostgreSQL - Single Server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4c4bac16917be0064ebb111328753d378d462a2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770132"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Bulkinserts optimaliseren en tijdelijke gegevens gebruiken in een Azure-database voor PostgreSQL - Single Server 
In dit artikel wordt beschreven hoe u bewerkingen voor het invoegen van bulk optimaliseren en tijdelijke gegevens gebruiken op een Azure Database voor PostgreSQL-server.

## <a name="use-unlogged-tables"></a>Niet-geregistreerde tabellen gebruiken
Als u werkbelastingbewerkingen hebt die tijdelijke gegevens bevatten of die grote gegevenssets in bulk invoegen, u overwegen om niet-gelogde tabellen te gebruiken.

Ongeregistreerde tabellen is een PostgreSQL-functie die effectief kan worden gebruikt om bulkinserts te optimaliseren. PostgreSQL maakt gebruik van Write-Ahead Logging (WAL). Het biedt atomiteit en duurzaamheid, standaard. Atomiciteit, consistentie, isolatie en duurzaamheid vormen de ZUUR-eigenschappen. 

Invoegen in een niet-gelogde tabel betekent dat PostgreSQL voegt in zonder te schrijven in het transactielogboek, wat zelf een I/O-bewerking is. Hierdoor zijn deze tabellen aanzienlijk sneller dan gewone tabellen.

Gebruik de volgende opties om een niet-geregistreerde tabel te maken:
- Maak een nieuwe niet-geregistreerde `CREATE UNLOGGED TABLE <tableName>`tabel met behulp van de syntaxis .
- Een bestaande aangemelde tabel converteren naar een `ALTER TABLE <tableName> SET UNLOGGED`niet-aangemelde tabel met behulp van de syntaxis .  

Als u het proces `ALTER TABLE <tableName> SET LOGGED`wilt omkeren, gebruikt u de syntaxis .

## <a name="unlogged-table-tradeoff"></a>Niet-gelogde tafelafweging
Ongeregistreerde tabellen zijn niet crash-safe. Een niet-gelogde tabel wordt automatisch afgekapt na een crash of onderworpen aan een onreine shutdown. De inhoud van een niet-gelogde tabel wordt ook niet gerepliceerd naar stand-byservers. Alle indexen die op een niet-gelogde tabel zijn gemaakt, worden ook automatisch niet geregistreerd. Nadat de invoegbewerking is voltooid, converteert u de tabel naar gelogd, zodat de invoegtoepassing duurzaam is.

Sommige klantworkloads hebben ongeveer 15 tot 20 procent prestatieverbetering ervaren toen niet-gelogde tabellen werden gebruikt.

## <a name="next-steps"></a>Volgende stappen
Controleer uw werkbelasting op het gebruik van tijdelijke gegevens en grote bulkinserts. Zie de volgende PostgreSQL-documentatie:
 
- [Sql-opdrachten voor tabelmaken maken](https://www.postgresql.org/docs/current/static/sql-createtable.html)
