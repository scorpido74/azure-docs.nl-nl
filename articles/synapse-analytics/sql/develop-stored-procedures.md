---
title: Opgeslagen procedures gebruiken
description: Tips voor het implementeren van opgeslagen procedures in Synapse SQL pool (Data Warehouse) voor het ontwikkelen van oplossingen.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 0f88b994104ed8a2d80fb3b16f125f8a087cbe3c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: nl-NL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958375"
---
# <a name="use-stored-procedures-in-sql-pool"></a>Opgeslagen procedures in de SQL-groep gebruiken

Tips voor het implementeren van opgeslagen procedures in Synapse SQL pool (Data Warehouse) voor het ontwikkelen van oplossingen.

## <a name="what-to-expect"></a>Wat u kunt verwachten

De SQL-groep ondersteunt veel van de T-SQL-functies die worden gebruikt in SQL Server. Belang rijker is dat er specifieke functies zijn die u kunt gebruiken om de prestaties van uw oplossing te optimaliseren.

Als u de schaal en prestaties van de SQL-groep wilt behouden, zijn er echter ook enkele functies en functionaliteit die gedrags verschillen hebben en andere niet worden ondersteund.

## <a name="introducing-stored-procedures"></a>Inleiding tot opgeslagen procedures

Opgeslagen procedures zijn een uitstekende manier om uw SQL-code te integreren. Sla het op in de buurt van uw gegevens in het Data Warehouse. Met opgeslagen procedures kunnen ontwikkel aars hun oplossingen modularizeen door de code te integreren in beheer bare eenheden. grotere herbruikbaarheid van code vergemakkelijkt. Elke opgeslagen procedure kan ook para meters accepteren om ze nog flexibeler te maken.

SQL-groep biedt een vereenvoudigde en gestroomlijnde implementatie van opgeslagen procedures. Het grootste verschil ten opzichte van SQL Server is dat de opgeslagen procedure geen vooraf gecompileerde code is. In data warehouses is de compilatie tijd klein in vergelijking met de tijd die nodig is om query's uit te voeren op grote gegevens volumes. Het is belang rijker om ervoor te zorgen dat de opgeslagen procedure code correct is geoptimaliseerd voor grote query's. Het doel is om uren, minuten en seconden op te slaan, niet in milliseconden. Het is daarom handiger om opgeslagen procedures te beschouwen als containers voor SQL-logica.

Wanneer de SQL-groep uw opgeslagen procedure uitvoert, worden de SQL-instructies geparseerd, vertaald en geoptimaliseerd tijdens runtime. Tijdens dit proces wordt elke instructie geconverteerd naar gedistribueerde query's. De SQL-code die wordt uitgevoerd op basis van de gegevens, wijkt af van de query die is verzonden.

## <a name="nesting-stored-procedures"></a>Opgeslagen procedures nesten

Wanneer opgeslagen procedures andere opgeslagen procedures aanroepen of dynamische SQL uitvoeren, wordt de interne opgeslagen procedure of het aanroepen van de code genest.

De SQL-pool ondersteunt Maxi maal acht geneste niveaus. Dit wijkt enigszins af van SQL Server. Het nest niveau in SQL Server is 32.

De op het hoogste niveau opgeslagen procedure aanroep is gelijk aan nest niveau 1.

```sql
EXEC prc_nesting
```

Als de opgeslagen procedure ook een andere EXEC-aanroep maakt, neemt het nest niveau toe tot twee.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

Als de tweede procedure vervolgens een aantal dynamische SQL-procedures uitvoert, neemt het nest niveau toe tot drie.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

> [!NOTE]
> De SQL-pool biedt momenteel geen ondersteuning voor [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). U moet het nest niveau bijhouden. Het is niet onwaarschijnlijk dat u de limiet van acht geneste niveaus overschrijdt, maar als u dat wel doet, moet u uw code opnieuw instellen zodat deze overeenkomt met de geneste niveaus binnen deze limiet.

## <a name="insertexecute"></a>INSERT..EXESCHATTIGE

De SQL-groep staat niet toe dat u de resultatenset van een opgeslagen procedure gebruikt met een instructie INSERT. Er is echter een andere methode die u kunt gebruiken. Zie het artikel over [tijdelijke tabellen](develop-tables-temporary.md)voor een voor beeld.

## <a name="limitations"></a>Beperkingen

Er zijn een aantal aspecten van opgeslagen Transact-SQL-procedures die niet in SQL-groep zijn geïmplementeerd.

Dit zijn:

* tijdelijke opgeslagen procedures
* genummerde opgeslagen procedures
* uitgebreide opgeslagen procedures
* Opgeslagen CLR-procedures
* versleutelings optie
* replicatie optie
* tabelwaardeparameter
* alleen-lezen-para meters
* standaard parameters
* uitvoerings contexten
* instructie return

## <a name="next-steps"></a>Volgende stappen

Zie [ontwikkelings overzicht](develop-overview.md)voor meer tips voor ontwikkel aars.
