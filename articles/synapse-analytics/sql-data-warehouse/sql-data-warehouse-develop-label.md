---
title: Labels gebruiken voor instrument query's
description: Tips voor het gebruik van labels voor instrument query's in Synapse SQL pool voor het ontwikkelen van oplossingen.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d4459547300f5dfc7b7c22d1e531b928a13aa66c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213428"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>Labels gebruiken voor instrument query's in de SQL-groep Synapse

In dit artikel vindt u tips voor het ontwikkelen van oplossingen met behulp van labels voor instrument query's in de SQL-groep.

Tips voor het gebruik van labels voor instrument query's in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.

## <a name="what-are-labels"></a>Wat zijn labels?

SQL-pool ondersteunt een concept met de naam query labels. We kijken naar een voor beeld voordat u naar een andere diepte gaat:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Met de laatste regel wordt de teken reeks ' mijn query label ' gelabeld in de query. Deze tag is handig omdat het label query's kan uitvoeren via de Dmv's.

Het uitvoeren van query's voor labels biedt een mechanisme voor het vinden van probleem query's en het identificeren van de voortgang van een ELT-uitvoering.

Een goede naam Conventie is echt handig. Als u bijvoorbeeld het label met PROJECT, PROCEDURE, instructie of opmerking start, wordt de query op unieke wijze geïdentificeerd voor alle code in broncode beheer.

De volgende query gebruikt een dynamische beheer weergave om te zoeken op label:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Het is essentieel om rechte haken of dubbele aanhalings tekens rond het woord label te plaatsen tijdens het uitvoeren van query's. Label is een gereserveerd woord dat een fout veroorzaakt wanneer het niet wordt gescheiden.

## <a name="next-steps"></a>Volgende stappen

Zie [ontwikkelings overzicht](sql-data-warehouse-overview-develop.md)voor meer tips voor ontwikkel aars.
