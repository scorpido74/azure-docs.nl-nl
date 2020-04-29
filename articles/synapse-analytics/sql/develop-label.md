---
title: Labels van query's gebruiken in Synapse SQL
description: In dit artikel vindt u essentiële tips voor het gebruik van query-labels in Synapse SQL.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430030"
---
# <a name="use-query-labels-in-synapse-sql"></a>Labels van query's gebruiken in Synapse SQL
In dit artikel vindt u essentiële tips voor het gebruik van query-labels in Synapse SQL.

> [!NOTE]
> SQL on-demand (preview) biedt geen ondersteuning voor het uitvoeren van query's.

## <a name="what-are-query-labels"></a>Wat zijn query labels?
SQL-pool ondersteunt een concept met de naam query labels. We kijken naar een voor beeld voordat u naar een andere diepte gaat:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Met de laatste regel wordt de teken reeks ' mijn query label ' gelabeld in de query. Deze tag is vooral nuttig omdat het label query's kan uitvoeren via de Dmv's. Het uitvoeren van query's voor labels biedt een mechanisme voor het vinden van probleem query's en helpt bij het identificeren van de voortgang van een ELT-uitvoering.

Goede naam conventies zijn het handigst. Als u bijvoorbeeld het label met PROJECT, PROCEDURE, instructie of opmerking start, wordt de query op unieke wijze geïdentificeerd voor alle code in broncode beheer.

De volgende query gebruikt een dynamische beheer weergave om te zoeken op label:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Het is essentieel om rechte haken of dubbele aanhalings tekens rond het woord label te plaatsen tijdens het uitvoeren van query's. Label is een gereserveerd woord dat een fout veroorzaakt wanneer het niet wordt gescheiden. 
> 
> 

## <a name="next-steps"></a>Volgende stappen
Zie [ontwikkelings overzicht](develop-overview.md)voor meer tips voor ontwikkel aars.


