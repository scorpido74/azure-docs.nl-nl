---
title: Migreren van DTU naar vCore
description: Migreer van het DTU-model naar het vCore-model. Migreren naar vCore is vergelijkbaar met upgraden of downgraden tussen de standaard- en premiumlagen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 693065046f92e0e9eade14c43e9942772440937d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945403"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migreren van het DTU-model naar het vCore-model

## <a name="migrate-a-database"></a>Een database migreren

Het migreren van een database van het Op DTU gebaseerde inkoopmodel naar het vCore-gebaseerde inkoopmodel is vergelijkbaar met het upgraden of verlagen tussen de standaard- en premiumservicelagen in het Op DTU gebaseerde inkoopmodel.

## <a name="migrate-databases-that-use-geo-replication"></a>Databases migreren die georeplicatie gebruiken

Migreren van het DTU-model naar het vCore-gebaseerde inkoopmodel is vergelijkbaar met het upgraden of downgraden van de georeplicatierelaties tussen databases in de standaard- en premiumservicelagen. Tijdens de migratie hoeft u georeplicatie niet te stoppen, maar moet u de volgende regels volgen:

- Bij het upgraden moet u eerst de secundaire database upgraden en vervolgens de primaire database upgraden.
- Bij het downgraden u de volgorde omkeren: u moet eerst de primaire database downgraden en vervolgens de secundaire indeling verlagen.

Wanneer u georeplicatie tussen twee elastische groepen gebruikt, raden we u aan de ene groep aan te wijzen als de primaire en de andere als secundaire. In dat geval moet u bij het migreren van elastische pools dezelfde sequencingrichtlijnen gebruiken. Als u echter elastische pools hebt die zowel primaire als secundaire databases bevatten, behandelt u de groep met het hogere gebruik als de primaire en volgt u de sequencingregels dienovereenkomstig.  

In de volgende tabel vindt u richtlijnen voor specifieke migratiescenario's:

|Huidige servicelaag|Doelservicelaag|Migratietype|Gebruikersacties|
|---|---|---|---|
|Standard|Algemeen doel|Laterale|Kan migreren in elke volgorde, maar moeten zorgen voor de juiste vCore-grootte *|
|Premium|Bedrijfskritisch|Laterale|Kan migreren in elke volgorde, maar moeten zorgen voor de juiste vCore-grootte *|
|Standard|Bedrijfskritisch|Upgraden|Moet eerst secundair migreren|
|Bedrijfskritisch|Standard|Downgrade|Moet eerst primaire migreren|
|Premium|Algemeen doel|Downgrade|Moet eerst primaire migreren|
|Algemeen doel|Premium|Upgraden|Moet eerst secundair migreren|
|Bedrijfskritisch|Algemeen doel|Downgrade|Moet eerst primaire migreren|
|Algemeen doel|Bedrijfskritisch|Upgraden|Moet eerst secundair migreren|
||||

\*Als vuistregel geldt dat elke 100 DTU's in de standaardlaag ten minste 1 vCore vereisen en elke 125 DTU's in de premiumlaag ten minste 1 vCore vereisen. Zie voor meer informatie [het op vCore gebaseerde inkoopmodel](https://docs.microsoft.com/azure/sql-database/sql-database-purchase-models#vcore-based-purchasing-model).

## <a name="migrate-failover-groups"></a>Failovergroepen migreren

Migratie van failovergroepen met meerdere databases vereist individuele migratie van de primaire en secundaire databases. Tijdens dat proces gelden dezelfde overwegingen en volgorderegels. Nadat de databases zijn geconverteerd naar het op vCore gebaseerde inkoopmodel, blijft de failovergroep van kracht met dezelfde beleidsinstellingen.

### <a name="create-a-geo-replication-secondary-database"></a>Een secundaire georeplicatiedatabase maken

U een secundaire georeplicatiedatabase (een geo-secundaire) alleen maken met dezelfde servicelaag als u voor de primaire database hebt gebruikt. Voor databases met een hoge loggeneratiesnelheid raden we aan de geo-secundaire te maken met dezelfde rekengrootte als de primaire.

Als u een geo-secundaire instelling maakt in de elastische `maxVCore` groep voor één primaire database, controleert u of de instelling voor de groep overeenkomt met de rekengrootte van de primaire database. Als u een geo-secundaire instelling maakt voor een primaire in een `maxVCore` andere elastische groep, raden we u aan dat de groepen dezelfde instellingen hebben.

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Databasekopie gebruiken om een DTU-database om te zetten naar een vCore-database

U elke database met een Op DTU gebaseerde rekengrootte kopiëren naar een database met een op vCore gebaseerde rekengrootte zonder beperkingen of speciale sequencing, zolang de doelgrootte de maximale databasegrootte van de brondatabase ondersteunt. De databasekopie maakt een momentopname van de gegevens vanaf de begintijd van de kopieerbewerking en synchroniseert geen gegevens tussen de bron en het doel.

## <a name="next-steps"></a>Volgende stappen

- Zie [SQL Database vCore-gebaseerde resourcelimieten voor afzonderlijke databases voor](sql-database-vcore-resource-limits-single-databases.md)de specifieke rekengroottes en opslaggroottekeuzes die beschikbaar zijn voor afzonderlijke databases.
- Zie [SQL Database vCore-gebaseerde resourcelimieten voor elastische pools voor](sql-database-vcore-resource-limits-elastic-pools.md)de specifieke rekengroottes en opties voor opslaggroottes die beschikbaar zijn voor elastische pools.
