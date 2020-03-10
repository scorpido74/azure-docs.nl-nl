---
title: Migreren van DTU naar vCore
description: Migreer vanuit het DTU-model naar het vCore-model. Migreren naar vCore is vergelijkbaar met het bijwerken of downgradeen tussen de Standard-en Premium-lagen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 693065046f92e0e9eade14c43e9942772440937d
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945403"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migreren van het DTU-model naar het model op basis van vCore

## <a name="migrate-a-database"></a>Een database migreren

Het migreren van een Data Base van het DTU-gebaseerde aankoop model naar het op vCore gebaseerde aankoop model is vergelijkbaar met het bijwerken of downgradeen tussen de Standard-en Premium-Service lagen in het op DTU gebaseerde aankoop model.

## <a name="migrate-databases-that-use-geo-replication"></a>Data bases migreren die gebruikmaken van geo-replicatie

Migreren van het DTU-model naar het op vCore gebaseerde aankoop model is vergelijkbaar met het bijwerken of verlagen van de geo-replicatie relaties tussen data bases in de Standard-en Premium-Service lagen. Tijdens de migratie hoeft u geo-replicatie niet te stoppen, maar moet u deze regels voor volgorde bepaling volgen:

- Wanneer u een upgrade uitvoert, moet u eerst de secundaire data base upgraden en vervolgens een upgrade uitvoeren van de primaire.
- Wanneer u een downgrade uitvoert, maakt u de volg orde ongedaan: u moet eerst de primaire data base opdowngradeen en vervolgens het secundaire database downgrade verlagen.

Wanneer u geo-replicatie tussen twee elastische Pools gebruikt, raden we u aan om één groep als primair en als de secundaire groep aan te wijzen. In dat geval moet u bij het migreren van elastische Pools dezelfde richt lijnen voor sequentiëren gebruiken. Als u echter elastische Pools hebt die zowel primaire als secundaire data bases bevatten, behandelt u de pool met het hogere gebruik als primair en volgt u de regels voor volgorde bepaling dienovereenkomstig.  

De volgende tabel bevat richt lijnen voor specifieke migratie scenario's:

|Huidige servicelaag|Doel service-laag|Type migratie|Gebruikers acties|
|---|---|---|---|
|Standard|Algemeen doel|Zij|Kan in een wille keurige volg orde worden gemigreerd, maar moet de juiste vCore-grootte worden gegarandeerd *|
|Premium|Bedrijfs kritiek|Zij|Kan in een wille keurige volg orde worden gemigreerd, maar moet de juiste vCore-grootte worden gegarandeerd *|
|Standard|Bedrijfs kritiek|Upgraden|Moet secundair eerst worden gemigreerd|
|Bedrijfs kritiek|Standard|Downgrade|Moet primair eerst worden gemigreerd|
|Premium|Algemeen doel|Downgrade|Moet primair eerst worden gemigreerd|
|Algemeen doel|Premium|Upgraden|Moet secundair eerst worden gemigreerd|
|Bedrijfs kritiek|Algemeen doel|Downgrade|Moet primair eerst worden gemigreerd|
|Algemeen doel|Bedrijfs kritiek|Upgraden|Moet secundair eerst worden gemigreerd|
||||

\* als vuist regel, is voor elke 100-Dtu's in de laag standaard Mini maal 1 vCore vereist en moet elke 125 Dtu's in de laag Premium ten minste één vCore vereisen. Zie [vCore-gebaseerd inkoop model](https://docs.microsoft.com/azure/sql-database/sql-database-purchase-models#vcore-based-purchasing-model)voor meer informatie.

## <a name="migrate-failover-groups"></a>Failover-groepen migreren

Migratie van failover-groepen met meerdere data bases vereist een afzonderlijke migratie van de primaire en secundaire data bases. Tijdens dat proces gelden dezelfde aandachtspunten en sequentiëren. Nadat de data bases zijn geconverteerd naar het op vCore gebaseerde aankoop model, blijft de failovergroep van kracht met dezelfde beleids instellingen.

### <a name="create-a-geo-replication-secondary-database"></a>Een secundaire data base met geo-replicatie maken

U kunt een secundaire data base met geo-replicatie (een geo-secundair) alleen maken door dezelfde servicelaag te gebruiken als u hebt gebruikt voor de primaire data base. Voor data bases met een hoge frequentie voor het genereren van het logboek, wordt aangeraden om de geo-secundair te maken met dezelfde reken grootte als de primaire.

Als u een geo-secundair maakt in de elastische pool voor één primaire data base, moet u ervoor zorgen dat de `maxVCore` instelling voor de pool overeenkomt met de reken grootte van de primaire data base. Als u een geo-secundair maakt voor een primaire groep in een andere elastische pool, raden we aan dat de groepen dezelfde `maxVCore`-instellingen hebben.

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Database kopie gebruiken om een DTU-gebaseerde data base te converteren naar een op vCore gebaseerde data base

U kunt elke Data Base met een op DTU gebaseerde reken grootte kopiëren naar een Data Base met een op vCore gebaseerde reken grootte zonder beperkingen of speciale sequentiëren, zolang de doel berekenings grootte de maximale database grootte van de bron database ondersteunt. De kopie van de data base maakt een moment opname van de gegevens vanaf de begin tijd van de Kopieer bewerking en synchroniseert geen gegevens tussen de bron en het doel.

## <a name="next-steps"></a>Volgende stappen

- Zie [SQL database resource limieten op basis van vCore voor afzonderlijke data](sql-database-vcore-resource-limits-single-databases.md)bases voor de specifieke berekenings grootte en beschik bare opslag grootte voor afzonderlijke data bases.
- Zie [SQL database op vCore gebaseerde resource limieten voor elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md)voor de specifieke berekenings grootten en opties voor opslag grootte die beschikbaar zijn voor elastische Pools.
