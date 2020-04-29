---
title: Variabelen toewijzen
description: In dit artikel vindt u essentiële tips voor het toewijzen van T-SQL-variabelen in de SQL-groep.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633412"
---
# <a name="assign-variables-in-synapse-sql-pool"></a>Variabelen toewijzen in de SQL-groep Synapse

In dit artikel vindt u essentiële tips voor het toewijzen van T-SQL-variabelen in de SQL-groep.

## <a name="set-variables-with-declare"></a>Variabelen instellen met DECLAReren

Variabelen in de SQL-groep worden ingesteld `DECLARE` met behulp `SET` van de instructie of de instructie. Initialisatie van variabelen met DECLAReren is een van de meest flexibele manieren om een variabele waarde in de SQL-groep in te stellen.

```sql
DECLARE @v  int = 0
;
```

U kunt DECLARe ook gebruiken om meer dan één variabele per keer in te stellen. U kunt niet selecteren of bijwerken gebruiken om het volgende te doen:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

U kunt een variabele niet initialiseren en gebruiken in dezelfde DECLARe-instructie. Om het punt te illustreren, is het volgende voor beeld **niet** toegestaan @p1 omdat het is geïnitialiseerd en wordt gebruikt in dezelfde Declare-instructie. Het volgende voor beeld geeft een fout:

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>Waarden instellen met SET

SET is een gemeen schappelijke methode voor het instellen van één variabele.

De volgende instructies zijn allemaal geldige manieren om een variabele in te stellen met SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

U kunt slechts één variabele tegelijk instellen met SET. Samengestelde Opera tors zijn echter wel toegestaan.

## <a name="limitations"></a>Beperkingen

U kunt bijwerken niet gebruiken voor het toewijzen van variabelen.

## <a name="next-steps"></a>Volgende stappen

Zie [ontwikkelings overzicht](sql-data-warehouse-overview-develop.md)voor meer tips voor ontwikkel aars.
