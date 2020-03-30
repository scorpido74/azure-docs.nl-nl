---
title: Labels gebruiken voor instrumentquery's
description: Tips voor het gebruik van labels voor instrumentquery's in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 828e4a406cd0fb12877af44263ab1f338c20850c
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351682"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Labels gebruiken voor instrumentquery's in Azure SQL Data Warehouse
Tips voor het gebruik van labels voor instrumentquery's in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.


## <a name="what-are-labels"></a>Wat zijn labels?
SQL Data Warehouse ondersteunt een concept dat querylabels wordt genoemd. Voordat we in gaan op een diepte, laten we eens kijken naar een voorbeeld:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

De laatste regel tagt de tekenreeks 'Mijn querylabel' aan de query. Deze tag is vooral handig omdat het label query-staat is via de DMVs. Zoeken naar labels biedt een mechanisme voor het lokaliseren van probleemquery's en het helpen identificeren van de voortgang via een ELT-run.

Een goede naamgeving conventie helpt echt. Als u het label bijvoorbeeld start met PROJECT, PROCEDURE, STATEMENT of COMMENT, u de query op unieke wijze identificeren tussen alle code in bronbeheer.

In de volgende query wordt een dynamische beheerweergave gebruikt om op label te zoeken.

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
Zie voor meer ontwikkelingstips [het ontwikkelingsoverzicht.](sql-data-warehouse-overview-develop.md)


