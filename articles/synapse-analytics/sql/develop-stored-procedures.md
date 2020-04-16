---
title: Met behulp van opgeslagen procedures
description: Tips voor het implementeren van opgeslagen procedures in Synapse SQL pool (data warehouse) voor het ontwikkelen van oplossingen.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: a431df1ff4ef0984d1197933e7ca78979fa23089
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430979"
---
# <a name="using-stored-procedures-in-sql-pool"></a>Opgeslagen procedures gebruiken in SQL-pool

Tips voor het implementeren van opgeslagen procedures in Synapse SQL pool (data warehouse) voor het ontwikkelen van oplossingen.

## <a name="what-to-expect"></a>Wat te verwachten

SQL-pool ondersteunt veel van de T-SQL-functies die worden gebruikt in SQL Server. Wat nog belangrijker is, er zijn scale-out specifieke functies die u gebruiken om de prestaties van uw oplossing te maximaliseren.

Echter, om de schaal en prestaties van SQL pool te behouden zijn er ook enkele functies en functionaliteit die gedragsverschillen en anderen die niet worden ondersteund.

## <a name="introducing-stored-procedures"></a>Invoering van opgeslagen procedures

Opgeslagen procedures zijn een geweldige manier om uw SQL-code in te kapselen; het opslaan van het dicht bij uw gegevens in het datawarehouse. Opgeslagen procedures helpen ontwikkelaars hun oplossingen te modulariseren door de code in te kapselen in beheerbare eenheden; het vergemakkelijken van een grotere herbruikbaarheid van code. Elke opgeslagen procedure kan ook parameters accepteren om ze nog flexibeler te maken.

SQL-pool biedt een vereenvoudigde en gestroomlijnde implementatie van opgeslagen procedures. Het grootste verschil met SQL Server is dat de opgeslagen procedure geen vooraf gecompileerde code is. In gegevensmagazijnen is de compilatietijd klein in vergelijking met de tijd die nodig is om query's uit te voeren tegen grote gegevensvolumes. Het is belangrijker om ervoor te zorgen dat de opgeslagen procedurecode correct is geoptimaliseerd voor grote query's. Het doel is om uren, minuten en seconden te besparen, niet milliseconden. Het is daarom nuttiger om opgeslagen procedures te zien als containers voor SQL-logica.

Wanneer SQL-pool uw opgeslagen procedure uitvoert, worden de SQL-instructies ontleed, vertaald en geoptimaliseerd tijdens uitvoering. Tijdens dit proces wordt elke instructie omgezet in gedistribueerde query's. De SQL-code die tegen de gegevens wordt uitgevoerd, is anders dan de query die is ingediend.

## <a name="nesting-stored-procedures"></a>Opgeslagen procedures nesten

Wanneer opgeslagen procedures andere opgeslagen procedures oproepen of dynamische SQL uitvoeren, wordt gezegd dat de interne opgeslagen procedure of codeaanroep is genest.

SQL-pool ondersteunt maximaal acht nestniveaus. Dit is iets anders dan SQL Server. Het nestniveau in SQL Server is 32.

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

> [!NOTE]
> SQL-groep biedt momenteel geen ondersteuning [voor @@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Je moet het nestniveau volgen. Het is onwaarschijnlijk dat u de limiet van acht nestniveaus overschrijdt, maar als u dat doet, moet u uw code opnieuw bewerken om de nestniveaus binnen deze limiet te passen.

## <a name="insertexecute"></a>Invoegen.. Uitvoeren

Sql-groep staat u niet toe om de resultatenset van een opgeslagen procedure te gebruiken met een INSERT-instructie. Er is echter een alternatieve aanpak die u gebruiken. Zie bijvoorbeeld het artikel over [tijdelijke tabellen](develop-tables-temporary.md).

## <a name="limitations"></a>Beperkingen

Er zijn enkele aspecten van transact-SQL opgeslagen procedures die niet zijn geïmplementeerd in SQL-pool.

Dit zijn:

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

Zie voor meer ontwikkelingstips [het ontwikkelingsoverzicht.](develop-overview.md)
