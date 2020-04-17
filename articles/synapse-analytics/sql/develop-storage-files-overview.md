---
title: Queryopslagbestanden met SQL on-demand (preview) binnen Synapse SQL
description: Beschrijft het opvragen van opslagbestanden met SQL on-demand (preview)-bronnen binnen Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8e6fd4955d6d68764895619efd968a957bcb5184
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460593"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Queryopslagbestanden met SQL on-demand (preview)-bronnen binnen Synapse SQL

SQL on-demand (preview) stelt u in staat om gegevens op te vragen in uw datalake. Het biedt een T-SQL-queryoppervlak dat geschikt is voor semi-gestructureerde en ongestructureerde gegevensquery's.

Voor query's worden de volgende T-SQL-aspecten ondersteund:

- Volledig [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) SELECT-oppervlak, inclusief de meeste SQL-functies, operators, enzovoort.
- MAAK EXTERNE TABEL ALS SELECT[(CETAS)](develop-tables-cetas.md)maakt een [externe tabel](develop-tables-external-tables.md) en exporteert vervolgens tegelijkertijd de resultaten van een transact-SQL SELECT-instructie naar Azure Storage.

Voor meer informatie over wat er momenteel niet wordt ondersteund, leest u het [SQL on-demand overzichtsartikel.](on-demand-workspace-overview.md)

Wanneer Azure AD-gebruikers query's uitvoeren, is het standaard dat opslagaccounts worden geopend met het Azure AD-verificatieprotocol voor doorgeefservice. Als zodanig worden gebruikers nagebootst en worden machtigingen gecontroleerd op opslagniveau. U [de toegang tot opslag regelen](develop-storage-files-storage-access-control.md) om aan uw behoeften te voldoen.

## <a name="extensions"></a>Extensies

Sql on-demand gebruikt de [openrowset-functie](develop-openrowset.md) met extra mogelijkheden om een soepele ervaring te ondersteunen voor het opvragen van gegevens die zich in Azure Storage-bestanden bevinden:

- [Meerdere bestanden of mappen opvragen](#query-multiple-files-or-folders)
- [PARQUET-bestandsindeling](#parquet-file-format)
- [Extra opties voor het werken met afgebakende tekst (veldterminator, rijterminator, escape char)](#additional-options-for-working-with-delimited-text)
- [Een gekozen subset van kolommen lezen](#read-a-chosen-subset-of-columns)
- [Schema-gevolgtrekking](#schema-inference)
- [bestandsnaam, functie](#filename-function)
- [filepath, functie](#filepath-function)
- [Werken met complexe typen en geneste of herhaalde gegevensstructuren](#work-with-complex-types-and-nested-or-repeated-data-structures)

### <a name="query-multiple-files-or-folders"></a>Meerdere bestanden of mappen opvragen

Als u een T-SQL-query wilt uitvoeren over een set bestanden in een map of set mappen terwijl u deze als één entiteit of rijset behandelt, geeft u een pad naar een map of een patroon (met jokertekens) over een reeks bestanden of mappen.

De volgende regels zijn van toepassing:

- Patronen kunnen worden weergegeven in een deel van een mappad of in een bestandsnaam.
- Er kunnen verschillende patronen worden weergegeven in dezelfde mapstap of bestandsnaam.
- Als er meerdere jokertekens zijn, worden bestanden binnen alle overeenkomende paden opgenomen in de resulterende bestandsset.

```
N'https://myaccount.blob.core.windows.net/myroot/*/mysubfolder/*.csv'
```

Raadpleeg [querymappen en meerdere bestanden](query-folders-multiple-csv-files.md) voor gebruiksvoorbeelden.

### <a name="parquet-file-format"></a>PARQUET-bestandsindeling

Als u parketbrongegevens wilt opvragen, gebruikt u FORMAT = 'PARQUET'

```sql
OPENROWSET
(
    { BULK 'data_file' ,
    { FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
)
AS table_alias(column_alias,...n)
<bulk_options> ::=
...
[ , FORMAT = {'CSV' | 'PARQUET'} ]
```

Controleer het artikel [Query Parketbestanden](query-parquet-files.md) voor gebruiksvoorbeelden.

### <a name="additional-options-for-working-with-delimited-text"></a>Aanvullende opties voor het werken met afgebakende tekst

Deze aanvullende parameters worden geïntroduceerd voor het werken met CSV-bestanden (afgebakende tekst):

```syntaxsql
<bulk_options> ::=
...
[ , FIELDTERMINATOR = 'char' ]
[ , ROWTERMINATOR = 'char' ]
[ , ESCAPE_CHAR = 'char' ]
...
```

- ESCAPE_CHAR = 'char' Geeft het teken in het bestand op dat wordt gebruikt om aan zichzelf te ontsnappen en alle waardehechtwaarden in het bestand. Als het escape-teken wordt gevolgd door een andere waarde dan zichzelf of een van de waardevan de scheidingsteken, wordt het escape-teken verwijderd bij het lezen van de waarde.
De parameter ESCAPE_CHAR wordt toegepast, ongeacht of de FIELDQUOTE wel of niet is ingeschakeld. Het zal niet worden gebruikt om te ontsnappen aan het citeren karakter. Het quotende teken wordt ontsnapt met dubbele aanhalingstekens in overeenstemming met het excel CSV-gedrag.
- FIELDTERMINATOR ='field_terminator' Geeft de te gebruiken veldterminator aan. De standaardveldbegeindiger is een komma **(" ,**")
- ROWTERMINATOR ='row_terminator' Geeft de rijterminator aan die moet worden gebruikt. De standaardrijbegeindiger is een nieuwregelteken: **\r\n**.

### <a name="read-a-chosen-subset-of-columns"></a>Een gekozen subset van kolommen lezen

Als u kolommen wilt opgeven die u wilt lezen, u een optionele MET-component opgeven in uw OPENROWSET-instructie.

- Als er CSV-gegevensbestanden zijn, om alle kolommen te lezen, geeft u kolomnamen en hun gegevenstypen op. Als u een subset van kolommen wilt, gebruikt u ordinale getallen om de kolommen uit de oorspronkelijke gegevensbestanden te kiezen op ordinaal. Kolommen zullen worden gebonden door de ordinale aanwijzing.
- Als er parketgegevensbestanden zijn, geeft u kolomnamen op die overeenkomen met de kolomnamen in de oorspronkelijke gegevensbestanden. Kolommen zijn gebonden aan de naam.

```syntaxsql
OPENROWSET
...
| BULK 'data_file',
{ FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
) AS table_alias(column_alias,...n) | WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })
```

Raadpleeg [CSV-bestanden voor](query-single-csv-file.md#returning-subset-of-columns)voorbeelden zonder alle kolommen op te geven.

### <a name="schema-inference"></a>Schema-gevolgtrekking

Door de MET-component weg te laten uit de instructie OPENROWSET, u de service instrueren om het schema automatisch uit onderliggende bestanden te detecteren (afleiden).

> [!NOTE]
> Dit werkt momenteel alleen voor PARQUET-bestandsindeling.

```sql
OPENROWSET(
BULK N'path_to_file(s)', FORMAT='PARQUET');

```

### <a name="filename-function"></a>Bestandsnaam, functie

Met deze functie wordt de bestandsnaam geretourneerd waarvan de rij afkomstig is.

Als u specifieke bestanden wilt opvragen, leest u de sectie Bestandsnaam in het artikel [Queryspecifieke bestanden.](query-specific-files.md#filename)

### <a name="filepath-function"></a>Filepath, functie

Met deze functie wordt een volledig pad of een deel van het pad geretourneerd:

- Wanneer u zonder parameter wordt aangeroepen, retourneert u het volledige bestandspad waaruit een rij afkomstig is.
- Wanneer deze met parameter wordt aangeroepen, wordt een deel van het pad geretourneerd dat overeenkomt met de wildcard op de positie die is opgegeven in de parameter. Parameterwaarde 1 geeft bijvoorbeeld een deel van het pad dat overeenkomt met de eerste wildcard.

Lees voor meer informatie de sectie Filepath van het artikel [Queryspecifieke bestanden.](query-specific-files.md#filepath)

### <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Werken met complexe typen en geneste of herhaalde gegevensstructuren

Om een soepele ervaring mogelijk te maken bij het werken met gegevens die zijn opgeslagen in geneste of herhaalde gegevenstypen, zoals in [parketbestanden,](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) heeft SQL on-demand de onderstaande extensies toegevoegd.

#### <a name="project-nested-or-repeated-data"></a>Projectgenese of herhaalde gegevens

Als u gegevens wilt projecteren, voert u een SELECT-instructie uit op het parketbestand met kolommen met geneste gegevenstypen. Bij uitvoer worden geneste waarden geserialiseerd tot JSON en geretourneerd als een SQL-gegevenstype (varchar(8000).

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Raadpleeg voor meer gedetailleerde informatie de sectie Project geneste of herhaalde gegevens van het artikel [Geneste typen query.](query-parquet-nested-types.md#project-nested-or-repeated-data)

#### <a name="access-elements-from-nested-columns"></a>Toegang tot elementen uit geneste kolommen

Als u toegang wilt krijgen tot geneste elementen uit een geneste kolom, zoals Struct, gebruikt u 'puntnotatie' om veldnamen in het pad te voegen. Geef het pad op zoals column_name in de WITH-component van de functie OPENROWSET.

Het voorbeeld van het syntaxisfragment is als volgt:

```sql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

De functie OPENROWSET komt standaard overeen met de naam en het bronveld met de kolomnamen in de WITH-component. Elementen op verschillende nestniveaus binnen hetzelfde bronparketbestand kunnen worden geraadpleegd via de WITH-clausule.

**Retourwaarden**

- De functie retourneert een scalaire waarde, zoals int, decimale en varchar, van het opgegeven element en op het opgegeven pad, voor alle parkettypen die zich niet in de groep Genest type bevinden.
- Als het pad wijst naar een element dat van een genest type is, retourneert de functie een JSON-fragment vanaf het bovenste element op het opgegeven pad. Het JSON-fragment is van het type varchar(8000).
- Als de eigenschap niet kan worden gevonden op de opgegeven column_name, retourneert de functie een fout.
- Als de eigenschap niet kan worden gevonden op de opgegeven column_path, afhankelijk van de [padmodus,](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE)retourneert de functie een fout wanneer deze zich in de strikte modus of null bevindt in de lakse modus.

Controleer de sectie Access-elementen uit geneste kolommen in het artikel [Geneste querytypen](query-parquet-nested-types.md#access-elements-from-nested-columns) voor queryvoorbeelden.

#### <a name="access-elements-from-repeated-columns"></a>Toegang tot elementen uit herhaalde kolommen

Als u toegang wilt krijgen tot elementen uit een herhaalde kolom, zoals een element van een array of kaart, gebruikt u de [functie JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor elk scalaire element dat u moet projecteren en op te geven:

- Geneste of herhaalde kolom, als eerste parameter
- Een [JSON-pad](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) dat het element of de eigenschap aangeeft om toegang te krijgen, als een tweede parameter

Als u niet-scalaire elementen uit een herhaalde kolom wilt openen, gebruikt u de [functie JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor elk niet-scalaiar element dat u moet projecteren en opte geven:

- Geneste of herhaalde kolom, als eerste parameter
- Een [JSON-pad](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) dat het element of de eigenschap aangeeft om toegang te krijgen, als een tweede parameter

Zie syntaxisfragment hieronder:

```sql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

U queryvoorbeelden vinden voor toegang tot elementen uit herhaalde kolommen in het artikel [Geneste querytypen.](query-parquet-nested-types.md#access-elements-from-repeated-columns)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het opvragen van verschillende bestandstypen en het maken en gebruiken van weergaven:

- [Query enkel CSV-bestand](query-single-csv-file.md)
- [Parketbestanden voor query's](query-parquet-files.md)
- [JSON-bestanden opvragen](query-json-files.md)
- [Geneste querygenese](query-parquet-nested-types.md)
- [Querymappen en meerdere CSV-bestanden](query-folders-multiple-csv-files.md)
- [Bestandsmetagegevens gebruiken in query's](query-specific-files.md)
- [Weergaven maken en gebruiken](create-use-views.md)
