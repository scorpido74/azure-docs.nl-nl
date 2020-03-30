---
title: Aanbevolen procedures voor queryopslag in Azure-database voor PostgreSQL - Enkele server
description: In dit artikel worden aanbevolen procedures beschreven voor de Query Store in Azure Database voor PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 51239f4cf49784dd47470e1272b90508eaf25e6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70764221"
---
# <a name="best-practices-for-query-store"></a>Aanbevolen procedures voor queryarchief

**Geldt voor:** Azure Database voor PostgreSQL - Single Server-versies 9.6, 10, 11

In dit artikel worden aanbevolen procedures beschreven voor het gebruik van Query Store in Azure Database voor PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>De optimale queryopnamemodus instellen
Laat Query Store de gegevens vastleggen die voor u van belang zijn. 

|**pg_qs.query_capture_mode** | **Scenario**|
|---|---|
|_Alle_  |Analyseer uw werklast grondig in termen van alle query's en hun uitvoeringsfrequenties en andere statistieken. Identificeer nieuwe query's in uw werkbelasting. Detecteren of ad-hocquery's worden gebruikt om mogelijkheden voor gebruikers- of automatische parameterisatie te identificeren. _Alles_ wordt geleverd met een verhoogde kosten voor het verbruik van hulpbronnen. |
|_Boven_  |Richt uw aandacht op top query's - die uitgegeven door klanten.
|_Geen_ |U hebt al een queryset en tijdvenster vastgelegd die u wilt onderzoeken en u wilt de afleidingen elimineren die andere query's kunnen introduceren. _Geen_ enkele is geschikt voor het testen en bench-marking omgevingen. _Geen moet_ met de nodige voorzichtigheid worden gebruikt, omdat u de kans mist om belangrijke nieuwe query's bij te houden en te optimaliseren. U geen gegevens herstellen op die vensters uit het verleden. |

Query Store bevat ook een winkel voor wachtstatistieken. Er is een aanvullende opnamemodusquery die wachtstatistieken regelt: **pgms_wait_sampling.query_capture_mode** kan worden ingesteld op _geen_ of _alle_. 

> [!NOTE] 
> **pg_qs,query_capture_mode** vervangt **pgms_wait_sampling,query_capture_mode**. Als pg_qs.query_capture_mode _geen_is, heeft de instelling pgms_wait_sampling,query_capture_mode geen effect. 


## <a name="keep-the-data-you-need"></a>Bewaar de gegevens die u nodig hebt
De parameter **pg_qs.retention_period_in_days** geeft in dagen de bewaarperiode voor queryopslag op. Oudere query- en statistiekengegevens worden verwijderd. Query Store is standaard geconfigureerd om de gegevens 7 dagen te bewaren. Vermijd het bewaren van historische gegevens die u niet van plan bent te gebruiken. Verhoog de waarde als u gegevens langer moet bewaren.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Stel de frequentie van wachtstatistieken bemonstering 
De parameter **pgms_wait_sampling.history_period** geeft aan hoe vaak (in milliseconden) wachtgebeurtenissen worden bemonsterd. Hoe korter de periode, hoe vaker de bemonstering. Meer informatie wordt opgehaald, maar dat komt met de kosten van een groter verbruik van hulpbronnen. Verhoog deze periode als de server onder belasting staat of als u de granulariteit niet nodig hebt


## <a name="get-quick-insights-into-query-store"></a>Krijg snel inzicht in de Query Store
U [QueryPerformance Insight](concepts-query-performance-insight.md) gebruiken in de Azure-portal om snel inzicht te krijgen in de gegevens in de Query Store. De visualisaties geven de langstlopende query's en de langste wachtgebeurtenissen in de tijd weer.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het instellen of instellen van parameters met behulp van de [Azure-portal](howto-configure-server-parameters-using-portal.md) of de [Azure CLI](howto-configure-server-parameters-using-cli.md).