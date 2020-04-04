---
title: Variabelen toewijzen
description: In dit artikel vindt u essentiële tips voor het toewijzen van T-SQL-variabelen in SQL-pool.
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
ms.openlocfilehash: 2dcf706ea59657abc2718a69e59191604dc2849d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633412"
---
# <a name="assign-variables-in-synapse-sql-pool"></a>Variabelen toewijzen in de Synapse SQL-groep

In dit artikel vindt u essentiële tips voor het toewijzen van T-SQL-variabelen in SQL-pool.

## <a name="set-variables-with-declare"></a>Variabelen instellen met DECLARE

Variabelen in SQL-groep worden `DECLARE` ingesteld `SET` met behulp van de instructie of de instructie. Het initialiseren van variabelen met DECLARE is een van de meest flexibele manieren om een variabele waarde in SQL-groep in te stellen.

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

U een variabele niet initialiseren en gebruiken in dezelfde DECLARE-instructie. Ter illustratie van het punt **not** is het @p1 volgende voorbeeld niet toegestaan, omdat het zowel geïnitialiseerd als gebruikt wordt in dezelfde DECLARE-instructie. Als zodanig geeft het volgende voorbeeld een foutmelding:

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>Waarden instellen met SET

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
