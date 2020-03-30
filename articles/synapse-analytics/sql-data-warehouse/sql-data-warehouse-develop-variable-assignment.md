---
title: Variabelen toewijzen
description: Tips voor het toewijzen van T-SQL-variabelen in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.
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
ms.openlocfilehash: 0adcd9bdf92b7ec649b7d91ca0e655fc006b3549
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351651"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Variabelen toewijzen in Azure SQL Data Warehouse

Tips voor het toewijzen van T-SQL-variabelen in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.

## <a name="setting-variables-with-declare"></a>Variabelen instellen met DECLARE

Variabelen in SQL Data Warehouse `DECLARE` worden ingesteld `SET` met behulp van de instructie of de instructie. Het initialiseren van variabelen met DECLARE is een van de meest flexibele manieren om een variabele waarde in SQL Data Warehouse in te stellen.

```sql
DECLARE @v  int = 0
;
```

U DECLARE ook gebruiken om meer dan één variabele tegelijk in te stellen. U SELECT of UPDATE niet gebruiken om het volgende te doen:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

U een variabele niet initialiseren en gebruiken in dezelfde DECLARE-instructie. Ter illustratie van het punt **not** is het @p1 volgende voorbeeld niet toegestaan, omdat het zowel geïnitialiseerd als gebruikt wordt in dezelfde DECLARE-instructie. In het volgende voorbeeld wordt een fout weergegeven.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Waarden instellen met SET

SET is een veelgebruikte methode voor het instellen van één variabele.

De volgende instructies zijn allemaal geldige manieren om een variabele in te stellen met SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

U slechts één variabele tegelijk instellen met SET. Samengestelde operatoren zijn echter toegestaan.

## <a name="limitations"></a>Beperkingen

U UPDATE niet gebruiken voor variabele toewijzing.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer ontwikkelingstips [het ontwikkelingsoverzicht.](sql-data-warehouse-overview-develop.md)
