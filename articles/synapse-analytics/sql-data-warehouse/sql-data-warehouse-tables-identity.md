---
title: Id gebruiken om surrogaatsleutels te maken
description: Aanbevelingen en voorbeelden voor het gebruik van de eigenschap IDENTITY om surrogaatsleutels te maken op tabellen in synapse SQL-groep.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: d4a9880ed7ab26d0127026f49c0bc781cfc2a941
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586336"
---
# <a name="using-identity-to-create-surrogate-keys-in-synapse-sql-pool"></a>ID gebruiken om surrogaatsleutels te maken in de Synapse SQL-groep

Aanbevelingen en voorbeelden voor het gebruik van de eigenschap IDENTITY om surrogaatsleutels te maken op tabellen in synapse SQL-groep.

## <a name="what-is-a-surrogate-key"></a>Wat is een surrogaatsleutel

Een surrogaatsleutel op een tabel is een kolom met een unieke id voor elke rij. De sleutel wordt niet gegenereerd uit de tabelgegevens. Gegevensmodelers maken graag surrogaatsleutels op hun tabellen wanneer ze gegevensmagazijnmodellen ontwerpen. U de eigenschap IDENTITY gebruiken om dit doel eenvoudig en effectief te bereiken zonder de belastingsprestaties te beïnvloeden.  

## <a name="creating-a-table-with-an-identity-column"></a>Een tabel maken met een identiteitskolom

De eigenschap IDENTITY is ontworpen om alle distributies in de Synapse SQL-groep uit te schalen zonder dat dit gevolgen heeft voor de laadprestaties. Daarom is de implementatie van IDENTITY gericht op het bereiken van deze doelen.

U een tabel definiëren als de eigenschap IDENTITY wanneer u de tabel voor het eerst maakt met behulp van syntaxis die vergelijkbaar is met de volgende instructie:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1) NOT NULL
,    C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

U `INSERT..SELECT` vervolgens de tabel vullen.

Deze rest van deze sectie belicht de nuances van de implementatie om u te helpen ze beter te begrijpen.  

### <a name="allocation-of-values"></a>Toewijzing van waarden

De eigenschap IDENTITY garandeert niet de volgorde waarin de surrogaatwaarden worden toegewezen, wat het gedrag van SQL Server en Azure SQL Database weerspiegelt. In synapse SQL-pool is het ontbreken van een garantie echter meer uitgesproken.

Het volgende voorbeeld is een illustratie:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)    NOT NULL
,    C2 VARCHAR(30)                NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

In het voorgaande voorbeeld zijn twee rijen geland in distributie 1. De eerste rij heeft de surrogaatwaarde van 1 in kolom, `C1`en de tweede rij heeft de surrogaatwaarde van 61. Beide waarden zijn gegenereerd door de eigenschap IDENTITY. De toewijzing van de waarden is echter niet aaneengesloten. Dit gedrag is standaard.

### <a name="skewed-data"></a>Scheve gegevens

Het bereik van de waarden voor het gegevenstype wordt gelijkmatig verdeeld over de distributies. Als een gedistribueerde tabel last heeft van scheve gegevens, kan het waardenbereik dat beschikbaar is voor het gegevenstype voortijdig worden uitgeput. Als bijvoorbeeld alle gegevens in één distributie terecht komen, heeft de tabel in feite toegang tot slechts één zestigste van de waarden van het gegevenstype. Om deze reden is de `INT` eigenschap `BIGINT` IDENTITY beperkt tot en alleen gegevenstypen.

### <a name="selectinto"></a>Selecteer.. In

Wanneer een bestaande identiteitskolom in een nieuwe tabel wordt geselecteerd, neemt de nieuwe kolom de eigenschap IDENTITY over, tenzij een van de volgende voorwaarden waar is:

- De instructie SELECT bevat een join.
- Meerdere SELECT-verklaringen worden samengevoegd door UNION te gebruiken.
- De kolom IDENTITEIT wordt meer dan één keer weergegeven in de select-lijst.
- De kolom IDENTITEIT maakt deel uit van een expressie.

Als een van deze voorwaarden waar is, wordt de kolom NIET NULL gemaakt in plaats van de eigenschap IDENTITY te erven.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

CREATE TABLE AS SELECT (CTAS) volgt hetzelfde SQL Server-gedrag dat is gedocumenteerd voor SELECT.. In. U echter geen eigenschap IDENTITEIT opgeven in `CREATE TABLE` de kolomdefinitie van het deel van de instructie. U de functie IDENTITEIT ook `SELECT` niet gebruiken in het deel van de CTAS. Als u een tabel wilt `CREATE TABLE` invullen, moet `INSERT..SELECT` u de tabel definiëren die wordt gevolgd door de tabel te vullen.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Waarden expliciet invoegen in een identiteitskolom

Synapse SQL-groep ondersteunt `SET IDENTITY_INSERT <your table> ON|OFF` syntaxis. U deze syntaxis gebruiken om waarden expliciet in de kolom IDENTITEITS in te voegen.

Veel gegevensmodelers gebruiken graag vooraf gedefinieerde negatieve waarden voor bepaalde rijen in hun afmetingen. Een voorbeeld is de rij -1 of 'onbekend lid'.

In het volgende script ziet u hoe u deze rij expliciet toevoegen met setIDENTITY_INSERT:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT     *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>Gegevens laden

De aanwezigheid van de eigenschap IDENTITY heeft enkele implicaties voor uw code voor het laden van gegevens. In deze sectie worden enkele basispatronen voor het laden van gegevens in tabellen belicht met behulp van IDENTITEIT.

Als u gegevens in een tabel wilt laden en een surrogaatsleutel wilt genereren met behulp van ID, maakt u de tabel en gebruikt u INSERT.. SELECT of INVOEGEN.. WAARDEN om de belasting uit te voeren.

In het volgende voorbeeld wordt het basispatroon benadrukt:

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)
,    C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT     C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> Het is momenteel niet `CREATE TABLE AS SELECT` mogelijk om gegevens te gebruiken bij het laden van gegevens in een tabel met een identiteitskolom.
>

Zie [Uitpakken, laden en transformeren (ELT) voor Synapse SQL-pool](design-elt-data-loading.md) en [Best practices laden](guidance-for-loading-data.md)voor meer informatie over het laden van gegevens.

## <a name="system-views"></a>Systeemweergaven

U de [catalogusweergave sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) gebruiken om een kolom met de eigenschap IDENTITY te identificeren.

Om u te helpen het databaseschema beter te begrijpen, laat dit voorbeeld zien hoe u sys.identity_column' integreren met andere systeemcatalogusweergaven:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Beperkingen

De eigenschap IDENTITY kan niet worden gebruikt:

- Wanneer het kolomgegevenstype niet INT of BIGINT is
- Wanneer de kolom ook de distributiesleutel is
- Wanneer de tabel een externe tabel is

De volgende gerelateerde functies worden niet ondersteund in de Synapse SQL-groep:

- [IDENTITEIT()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)

## <a name="common-tasks"></a>Algemene taken

In deze sectie vindt u een voorbeeldcode die u gebruiken om veelvoorkomende taken uit te voeren wanneer u met identiteitskolommen werkt.

Kolom C1 is de identiteit in alle volgende taken.

### <a name="find-the-highest-allocated-value-for-a-table"></a>De hoogste toegewezen waarde voor een tabel zoeken

Gebruik `MAX()` de functie om de hoogste waarde te bepalen die is toegewezen voor een gedistribueerde tabel:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Zoek het zaad en de verhoging voor de eigenschap IDENTITY

U de catalogusweergaven gebruiken om de waarden voor identiteitstoename en startconfiguratie voor een tabel te ontdekken met behulp van de volgende query:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Volgende stappen

- [Tabeloverzicht](/azure/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [IDENTITEIT VAN DE TABEL MAKEN (Transact-SQL) (Eigenschap)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest)
- [DBCC-CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)
