---
title: Met behulp van opgeslagen procedures
description: Tips voor het ontwikkelen van oplossingen door het implementeren van opgeslagen procedures in Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 3ffdf7a66c2562b43fc2ed02bb088ab1095118fb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416160"
---
# <a name="using-stored-procedures-in-synapse-sql-pool"></a>Opgeslagen procedures gebruiken in Synapse SQL-pool

Dit artikel bevat tips voor het ontwikkelen van SQL-pooloplossingen door het implementeren van opgeslagen procedures.

## <a name="what-to-expect"></a>Wat te verwachten

SQL-pool ondersteunt veel van de T-SQL-functies die worden gebruikt in SQL Server. Wat nog belangrijker is, er zijn scale-out specifieke functies die u gebruiken om de prestaties van uw oplossing te maximaliseren.

Om u te helpen de schaal en prestaties van SQL-pool te behouden, zijn er extra functies en functionaliteiten met gedragsverschillen.

## <a name="introducing-stored-procedures"></a>Invoering van opgeslagen procedures

Opgeslagen procedures zijn een geweldige manier om uw SQL-code in te kapselen, die dicht bij uw SQL-poolgegevens wordt opgeslagen. Opgeslagen procedures helpen ontwikkelaars ook hun oplossingen te modulariseren door de code in te kapselen in beheerbare eenheden, waardoor een grotere herbruikbaarheid van code wordt vergemakkelijkt. Elke opgeslagen procedure kan ook parameters accepteren om ze nog flexibeler te maken.

SQL-pool biedt een vereenvoudigde en gestroomlijnde implementatie van opgeslagen procedures. Het grootste verschil met SQL Server is dat de opgeslagen procedure geen vooraf gecompileerde code is.

Over het algemeen is de compilatietijd voor gegevensmagazijnen klein in vergelijking met de tijd die nodig is om query's uit te voeren tegen grote gegevensvolumes. Het is belangrijker om ervoor te zorgen dat de opgeslagen procedurecode correct is geoptimaliseerd voor grote query's.

> [!TIP]
> Het doel is om uren, minuten en seconden te besparen, niet milliseconden. Het is dus handig om opgeslagen procedures te zien als containers voor SQL-logica.

Wanneer SQL-pool uw opgeslagen procedure uitvoert, worden de SQL-instructies ontleed, vertaald en geoptimaliseerd tijdens uitvoering. Tijdens dit proces wordt elke instructie omgezet in gedistribueerde query's. De SQL-code die tegen de gegevens wordt uitgevoerd, is anders dan de query die is ingediend.

## <a name="nesting-stored-procedures"></a>Opgeslagen procedures nesten

Wanneer opgeslagen procedures andere opgeslagen procedures oproepen of dynamische SQL uitvoeren, wordt gezegd dat de interne opgeslagen procedure of codeaanroep is genest.

SQL-pool ondersteunt maximaal acht nestniveaus. Het nestniveau in SQL Server is daarentegen 32.

De opgeslagen procedureaanroep op het hoogste niveau komt overeen met nestniveau 1.

```sql
EXEC prc_nesting
```

Als de opgeslagen procedure ook een andere EXEC-oproep doet, neemt het nestniveau toe tot twee.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

Als de tweede procedure vervolgens een dynamische SQL uitvoert, neemt het nestniveau toe tot drie.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

SQL-groep ondersteunt momenteel geen [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Als zodanig moet je het nestniveau volgen. Het is onwaarschijnlijk dat u de limiet van acht nesten niveau overschrijdt. Maar als je dat doet, moet je je code aanpassen aan de nestniveaus binnen deze limiet.

## <a name="insertexecute"></a>Invoegen.. Uitvoeren

Sql-groep staat u niet toe om de resultatenset van een opgeslagen procedure te gebruiken met een INSERT-instructie. Er is echter een alternatieve aanpak die u gebruiken. Zie bijvoorbeeld het artikel over [tijdelijke tabellen](sql-data-warehouse-tables-temporary.md).

## <a name="limitations"></a>Beperkingen

Er zijn enkele aspecten van transact-SQL opgeslagen procedures die niet zijn geïmplementeerd in SQL-groep, die als volgt zijn:

* tijdelijk opgeslagen procedures
* genummerde opgeslagen procedures
* uitgebreide opgeslagen procedures
* CLR opgeslagen procedures
* versleutelingsoptie
* replicatieoptie
* tabelwaardeparameters
* alleen-lezen parameters
* standaardparameters
* uitvoeringscontexten
* retouroverzicht

## <a name="next-steps"></a>Volgende stappen

Zie voor meer ontwikkelingstips [het ontwikkelingsoverzicht.](sql-data-warehouse-overview-develop.md)
