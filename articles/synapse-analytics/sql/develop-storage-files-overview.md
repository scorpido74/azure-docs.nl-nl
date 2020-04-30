---
title: Query's uitvoeren op opslag bestanden met behulp van SQL on-demand (preview) in Synapse SQL
description: Hierin wordt beschreven hoe u een query uitvoert op opslag bestanden met behulp van SQL on-demand-bronnen (preview) in Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 2126996620d6f891dde4e7530c057d2c7f31a996
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81676677"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Query's uitvoeren op opslag bestanden met behulp van SQL on-demand-bronnen (preview) in Synapse SQL

Met SQL on-demand (preview) kunt u gegevens opvragen in uw data Lake. Het biedt een T-SQL-query surface area die voldoet aan semi-gestructureerde en ongestructureerde gegevens query's.

Voor het uitvoeren van query's worden de volgende T-SQL-aspecten ondersteund:

- Volledige [selectie](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Surface Area, inclusief het meren deel van SQL-functies, Opera Tors, enzovoort.
- EXTERNE tabel maken als SELECT ([CETAS](develop-tables-cetas.md)) maakt een [externe tabel](develop-tables-external-tables.md) en exporteert vervolgens parallel de resultaten van een Transact-SQL SELECT-instructie om te Azure Storage.

Lees het [overzichts artikel SQL on demand](on-demand-workspace-overview.md) voor meer informatie over wat er nog niet wordt ondersteund.

Wanneer Azure AD-gebruikers query's uitvoeren, is de standaard instelling voor opslag accounts die worden geopend met het Pass-Through-verificatie Protocol van Azure AD. Als zodanig worden gebruikers geïmiteerd en worden de machtigingen gecontroleerd op het opslag niveau. U kunt de toegang tot uw [opslag beheren](develop-storage-files-storage-access-control.md) op basis van uw behoeften.

## <a name="extensions"></a>Uitbreidingen

Om een goede ervaring te bieden voor het uitvoeren van query's op gegevens die zich in Azure Storage bestanden bevinden, gebruikt SQL op aanvraag de functie [OPENrowset](develop-openrowset.md) met aanvullende mogelijkheden:

- [Meerdere bestanden of mappen opvragen](#query-multiple-files-or-folders)
- [PARQUET-bestands indeling](#parquet-file-format)
- [Aanvullende opties voor het werken met gescheiden tekst (veld Terminator, rij-Terminator, escape-teken)](#additional-options-for-working-with-delimited-text)
- [Een gekozen subset kolommen lezen](#read-a-chosen-subset-of-columns)
- [Schema-deinterferentie](#schema-inference)
- [de functie filename](#filename-function)
- [de functie filepath](#filepath-function)
- [Werken met complexe typen en geneste of herhaalde gegevens structuren](#work-with-complex-types-and-nested-or-repeated-data-structures)

### <a name="query-multiple-files-or-folders"></a>Meerdere bestanden of mappen opvragen

Als u een T-SQL-query wilt uitvoeren voor een set bestanden in een map of een set mappen, terwijl u ze als één entiteit of rijenset behandelt, geeft u een pad naar een map of een patroon (met Joker tekens) op voor een set bestanden of mappen.

De volgende regels zijn van toepassing:

- Patronen kunnen in een deel van een mappad of in een bestands naam worden weer gegeven.
- Verschillende patronen kunnen worden weer gegeven in dezelfde directory-stap of bestands naam.
- Als er meerdere joker tekens zijn, worden de bestanden in alle overeenkomende paden opgenomen in de resulterende bestandenset.

```
N'https://myaccount.blob.core.windows.net/myroot/*/mysubfolder/*.csv'
```

Raadpleeg [query mappen en meerdere bestanden](query-folders-multiple-csv-files.md) voor voor beelden van gebruik.

### <a name="parquet-file-format"></a>PARQUET-bestands indeling

Als u een query wilt uitvoeren op Parquet-bron gegevens, gebruikt u FORMAT = ' PARQUET '

```syntaxsql
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

Raadpleeg het artikel [query Parquet files](query-parquet-files.md) voor gebruiks voorbeelden.

### <a name="additional-options-for-working-with-delimited-text"></a>Aanvullende opties voor het werken met tekst met scheidings tekens

Deze aanvullende para meters worden geïntroduceerd voor het werken met CSV-bestanden (tekst met scheidings tekens):

```syntaxsql
<bulk_options> ::=
...
[ , FIELDTERMINATOR = 'char' ]
[ , ROWTERMINATOR = 'char' ]
[ , ESCAPE_CHAR = 'char' ]
...
```

- ESCAPE_CHAR = ' char ' geeft het teken op in het bestand dat wordt gebruikt om zichzelf te escapen en alle scheidings waarden in het bestand. Als het escape teken wordt gevolgd door een andere waarde dan zichzelf of een van de scheidings waarden, wordt het escape-teken verwijderd bij het lezen van de waarde.
De para meter ESCAPE_CHAR wordt toegepast, ongeacht of de FIELDQUOTE is of niet is ingeschakeld. Het wordt niet gebruikt om het aanhalings teken te escapepen. Het aanhalings teken wordt met dubbele aanhalings tekens in uitlijning met het CSV-gedrag van Excel.
- FIELDTERMINATOR = ' field_terminator ' geeft de veld-Terminator op die moet worden gebruikt. De standaard veld scheidings teken is een komma ('**,**')
- ROWTERMINATOR = ' row_terminator ' geeft het rij-einde op dat moet worden gebruikt. Het standaard einde van de rij is een teken voor een nieuwe regel: **\r\n**.

### <a name="read-a-chosen-subset-of-columns"></a>Een gekozen subset kolommen lezen

Als u kolommen wilt opgeven die u wilt lezen, kunt u een optionele WITH-component opgeven in de instructie OPENROWSET.

- Als er CSV-gegevens bestanden zijn, kunt u alle kolommen lezen door kolom namen en de bijbehorende gegevens typen op te geven. Als u een subset met kolommen wilt, gebruikt u ordinale getallen om de kolommen uit de oorspronkelijke gegevens bestanden op rang telwoord te kiezen. Kolommen worden gebonden aan de rang nummer aanduiding.
- Als er Parquet-gegevens bestanden zijn, geeft u kolom namen op die overeenkomen met de kolom namen in de oorspronkelijke gegevens bestanden. Kolommen worden gebonden op naam.

```syntaxsql
OPENROWSET
...
| BULK 'data_file',
{ FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
) AS table_alias(column_alias,...n) | WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })
```

Zie voor voor beelden [CSV-bestanden lezen zonder alle kolommen](query-single-csv-file.md#returning-subset-of-columns)op te geven.

### <a name="schema-inference"></a>Schema-deinterferentie

Als u de WITH-component van de instructie OPENROWSET weglaat, kunt u de service de opdracht geven het schema van onderliggende bestanden automatisch te detecteren (af te leiden).

> [!NOTE]
> Dit werkt momenteel alleen voor de PARQUET-bestands indeling.

```sql
OPENROWSET(
BULK N'path_to_file(s)', FORMAT='PARQUET');
```

### <a name="filename-function"></a>De functie filename

Deze functie retourneert de naam van het bestand waaruit de rij afkomstig is.

Lees de sectie filename in het artikel [query-specifieke bestanden](query-specific-files.md#filename) om specifieke bestanden op te vragen.

### <a name="filepath-function"></a>De functie filepath

Deze functie retourneert een volledig pad of een deel van het pad:

- Als u zonder para meter aangeroepen, wordt het volledige bestandspad geretourneerd waaruit een rij afkomstig is.
- Wanneer wordt aangeroepen met de para meter, wordt een deel van het pad geretourneerd dat overeenkomt met het Joker teken op de positie die is opgegeven in de para meter. Zo retourneert parameter waarde 1 deel van het pad dat overeenkomt met het eerste Joker teken.

Lees voor meer informatie de sectie filepath van het artikel [query-specifieke bestanden](query-specific-files.md#filepath) .

### <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Werken met complexe typen en geneste of herhaalde gegevens structuren

Om een goede ervaring te bieden bij het werken met gegevens die zijn opgeslagen in geneste of herhaalde gegevens typen, zoals in [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) -bestanden, heeft SQL on demand de volgende extensies toegevoegd.

#### <a name="project-nested-or-repeated-data"></a>Geneste of herhaalde gegevens projecteren

Als u gegevens wilt projecteren, voert u een SELECT-instructie uit voor het Parquet-bestand dat kolommen met geneste gegevens typen bevat. Bij uitvoer worden geneste waarden geserialiseerd in JSON en geretourneerd als een SQL-gegevens type van varchar (8000).

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Raadpleeg voor meer informatie de sectie geneste of herhaalde gegevens in project van het artikel [Parquet geneste typen query](query-parquet-nested-types.md#project-nested-or-repeated-data) .

#### <a name="access-elements-from-nested-columns"></a>Toegang tot elementen vanuit geneste kolommen

Om toegang te krijgen tot geneste elementen van een geneste kolom, zoals struct, gebruikt u ' punt notatie ' om veld namen in het pad samen te voegen. Geef het pad op als column_name in de component WITH van de functie OPENROWSET.

Een voor beeld van een syntaxis fragment is als volgt:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Standaard komt de functie OPENROWSET overeen met de naam en het pad van het Bron veld met de namen van de kolommen in de WITH-component. Elementen die deel uitmaken van verschillende nest niveaus binnen hetzelfde bron Parquet-bestand, kunnen worden geopend via de WITH-component.

**Retourwaarden**

- De functie retourneert een scalaire waarde, zoals int, decimal en varchar, van het opgegeven element en op het opgegeven pad voor alle Parquet-typen die zich niet in de geneste type groep bevinden.
- Als het pad naar een element van een genest type wijst, retourneert de functie een JSON-fragment vanaf het bovenste element in het opgegeven pad. Het JSON-fragment is van het type varchar (8000).
- Als de eigenschap niet kan worden gevonden op de opgegeven column_name, retourneert de functie een fout.
- Als de eigenschap niet kan worden gevonden op de opgegeven column_path, kan de functie, afhankelijk van de modus van het [pad](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE), een fout retour neren in de Strict-modus of null in de modus slordig.

Bekijk voor query voorbeelden de sectie toegangs elementen van geneste kolommen in het artikel [Parquet geneste typen query](query-parquet-nested-types.md#access-elements-from-nested-columns) .

#### <a name="access-elements-from-repeated-columns"></a>Toegang tot elementen van herhaalde kolommen

Voor toegang tot elementen uit een herhaalde kolom, zoals een element van een matrix of kaart, gebruikt u de functie [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor elk scalair element dat u moet projecteren en bieden:

- Geneste of herhaalde kolom, zoals de eerste para meter
- Een [JSON-pad](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) dat het element of de eigenschap voor toegang opgeeft, als een tweede para meter

Als u niet-scalaire elementen wilt openen vanuit een herhaalde kolom, gebruikt u de functie [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor elk niet-scalair element dat u nodig hebt om te projecteren en te bieden:

- Geneste of herhaalde kolom, zoals de eerste para meter
- Een [JSON-pad](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) dat het element of de eigenschap voor toegang opgeeft, als een tweede para meter

Zie syntaxis fragment hieronder:

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

U kunt query voorbeelden voor het verkrijgen van toegang tot elementen uit herhaalde kolommen vinden in het artikel [Parquet geneste typen query](query-parquet-nested-types.md#access-elements-from-repeated-columns) .

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het opvragen van verschillende bestands typen en het maken en gebruiken van weer gaven:

- [Query uitvoeren op één CSV-bestand](query-single-csv-file.md)
- [Query uitvoeren op Parquet-bestanden](query-parquet-files.md)
- [Query uitvoeren op JSON-bestanden](query-json-files.md)
- [Query uitvoeren op met Parquet geneste typen](query-parquet-nested-types.md)
- [Query mappen en meerdere CSV-bestanden](query-folders-multiple-csv-files.md)
- [Meta gegevens van bestanden gebruiken in query's](query-specific-files.md)
- [Weergaven maken en gebruiken](create-use-views.md)
