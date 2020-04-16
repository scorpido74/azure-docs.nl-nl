---
title: Querylabels gebruiken in Synapse SQL
description: In dit artikel zijn essentiële tips voor het gebruik van querylabels in Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 47b476cbc6997ca5ec63968bdc269e2273662100
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430030"
---
# <a name="use-query-labels-in-synapse-sql"></a>Querylabels gebruiken in Synapse SQL
In dit artikel zijn essentiële tips voor het gebruik van querylabels in Synapse SQL.

> [!NOTE]
> SQL on-demand (preview) biedt geen ondersteuning voor etiketteringsquery's.

## <a name="what-are-query-labels"></a>Wat zijn querylabels
SQL-groep ondersteunt een concept dat querylabels wordt genoemd. Voordat we in gaan op een diepte, laten we eens kijken naar een voorbeeld:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

De laatste regel tagt de tekenreeks 'Mijn querylabel' aan de query. Deze tag is vooral handig omdat het label query-staat is via de DMVs. Zoeken naar labels biedt een mechanisme voor het lokaliseren van probleemquery's en helpt om de voortgang te identificeren via een ELT-run.

Goede naamgeving conventie zijn zeer behulpzaam. Als u het label bijvoorbeeld start met PROJECT, PROCEDURE, STATEMENT of COMMENT, wordt de query op unieke wijze geïdentificeerd tussen alle code in bronbeheer.

In de volgende query wordt een dynamische beheerweergave gebruikt om op label te zoeken:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Het is essentieel om vierkante haakjes of dubbele aanhalingstekens rond het woordetiket te zetten wanneer het vragen. Label is een gereserveerd woord en veroorzaakt een fout wanneer het niet wordt afgebakend. 
> 
> 

## <a name="next-steps"></a>Volgende stappen
Zie voor meer ontwikkelingstips [het ontwikkelingsoverzicht.](develop-overview.md)


