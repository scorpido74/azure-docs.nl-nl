---
title: Variabelen toewijzen met Synapse SQL
description: In dit artikel vindt u tips voor het toewijzen van T-SQL-variabelen met Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: b2a596b71ee7e5f58e01d5bc10b330f6f54a69d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428665"
---
# <a name="assigning-variables-with-synapse-sql"></a>Variabelen toewijzen met Synapse SQL

In dit artikel vindt u tips voor het toewijzen van T-SQL-variabelen met Synapse SQL.

## <a name="setting-variables-with-declare"></a>Variabelen instellen met DECLARE

Variabelen in Synapse SQL worden `DECLARE` ingesteld `SET` met behulp van de instructie of de instructie. Het initialiseren van variabelen met DECLARE is een van de meest flexibele manieren om een variabele waarde in Synapse SQL in te stellen.

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

U een variabele niet initialiseren en gebruiken in dezelfde DECLARE-instructie. Ter illustratie is het volgende voorbeeld *@p1* niet toegestaan, omdat het zowel geïnitialiseerd als gebruikt wordt in dezelfde DECLARE-instructie. In het volgende voorbeeld wordt een fout weergegeven.

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

Zie het [overzichtsartikel voor de ontwikkeling van Synapse SQL voor](develop-overview.md) meer ontwikkelingstips.