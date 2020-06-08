---
title: Query-opslagbestanden die SQL on-demand (preview) gebruiken binnen Synapse SQL
description: Beschrijft query-opslagbestanden die SQL on-demand-resources (preview) gebruiken binnen Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 204fd1b1a0a2984886684bbabf33dc7e73c1b45c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653535"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Query-opslagbestanden die SQL on-demand-resources (preview) gebruiken binnen Synapse SQL

Met SQL on-demand (preview) kunt u een query uitvoeren op uw gegevens in uw data lake. Het biedt een T-SQL query-surface area dat geschikt is voor semi-gestructureerde en ongestructureerde gegevensquery's.

Voor het uitvoeren van query's worden de volgende T-SQL-aspecten ondersteund:

- Volledige [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-surface area, met inbegrip van de meeste SQL-functies, -operatoren en meer.
- Met CREATE EXTERNAL TABLE AS SELECT ([CETAS](develop-tables-cetas.md)) wordt een [externe tabel](develop-tables-external-tables.md) gemaakt en parallel de resultaten van een Transact-SQL SELECT-instructie geëxporteerd naar Azure Storage.

Lees het artikel [SQL on-demand-overzicht](on-demand-workspace-overview.md) voor meer informatie ten opzichte van wat momenteel niet wordt ondersteund.

Wanneer Azure AD-gebruikers qurey's uitvoeren worden de opslagaccounts standaard geopend met het Azure AD-pass-through-verificatieprotocol. Gebruikers worden op die manier geïmiteerd en de machtigingen gecontroleerd op het opslagniveau. U kunt [opslagtoegang aanpassen](develop-storage-files-storage-access-control.md) aan uw behoeften.

## <a name="extensions"></a>Extensies

Om een soepele ervaring te kunnen bieden voor het lokaal zoeken naar gegevens die zich bevinden in Azure-opslagbestanden gebruikt SQL on-demand de functie [OPENROWSET](develop-openrowset.md) met aanvullende mogelijkheden:

- [Meerdere bestanden of mappen doorzoeken](#query-multiple-files-or-folders)
- [Bestandsindeling PARQUET](#parquet-file-format)
- [Aanvullende opties voor werken met gescheiden tekst (veldeindteken, rij-eindteken, escape-teken)](#additional-options-for-working-with-delimited-text)
- [Een gekozen subset van kolommen lezen](#read-a-chosen-subset-of-columns)
- [Schema-deductie](#schema-inference)
- [Bestandsnaamfunctie](#filename-function)
- [Bestandspadfunctie](#filepath-function)
- [Werken met complexe types en geneste of herhaalde gegevensstructuren](#work-with-complex-types-and-nested-or-repeated-data-structures)

### <a name="query-multiple-files-or-folders"></a>Meerdere bestanden of mappen doorzoeken

Geef een pad of een map of een patroon (met jokertekens) over een verzameling bestanden of mappen op om een T-SQL-query uit te voeren op een verzameling bestanden in een map of een verzameling mappen en ze te behandelen als een enkele entiteit of rijenset.

De volgende regels zijn van toepassing:

- Patronen kunnen voorkomen in een deel van een mappad of in een bestandsnaam.
- Verschillende patronen kunnen in dezelfde mapstap of bestandsnaam verschijnen.
- Als er meerdere jokertekens zijn gebruikt, worden alle bestanden in overeenkomende paden inbegrepen in de resulterende bestandsverzameling.

```
N'https://myaccount.blob.core.windows.net/myroot/*/mysubfolder/*.csv'
```

Raadpleeg [Query uitvoeren op mappen en meerdere bestanden](query-folders-multiple-csv-files.md) voor gebruiksvoorbeelden.

### <a name="parquet-file-format"></a>Bestandsindeling PARQUET

FORMAT = 'PARQUET' gebruiken om een query uit te voeren op Parquet-gegevens

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

Lees het artikel [Query uitvoeren op Parquet-bestanden](query-parquet-files.md) voor gebruiksvoorbeelden.

### <a name="additional-options-for-working-with-delimited-text"></a>Aanvullende opties voor werken met gescheiden tekst

Deze extra parameters zijn uitgebracht voor het werken met CSV-bestanden (gescheiden tekst):

```syntaxsql
<bulk_options> ::=
...
[ , FIELDTERMINATOR = 'char' ]
[ , ROWTERMINATOR = 'char' ]
[ , ESCAPE_CHAR = 'char' ]
...
```

- ESCAPE_CHAR = 'char' Het teken in het bestand dat wordt gebruikt om zichzelf te escapen evenals alle scheidingstekens in het bestand. Als het escape-teken wordt gevolgd door ofwel een andere waarde dan het teken zelf, of een van de scheidingstekens, wordt het escape-teken genegeerd bij het lezen van de waarde.
De parameter ESCAPE_CHAR wordt toegepast ongeacht of FIELDQUOTE al dan niet is ingeschakeld. De parameter wordt niet gebruikt om het aanhalingsteken te escapen. Escapen van het aanhalingsteken gebeurt met dubbele aanhalingstekens, net zoals in CSV-bestanden van Excel.
- FIELDTERMINATOR ='field_terminator' Geeft aan welk teken moet worden gebruikt om het einde van een veld aan te geven. Het standaardeindteken voor velden is een komma (' **,** ')
- ROWTERMINATOR ='row_terminator' Geeft aan welk teken moet worden gebruikt om het einde van een regel aan te geven. Het standaardeindteken voor riijen is een teken voor nieuwe regel: **\r\n**.

### <a name="read-a-chosen-subset-of-columns"></a>Een gekozen subset van kolommen lezen

Om aan te geven welke kolommen u witl lezen, kunt u een optionele WITH-clausule opgeven binnen uw OPENROWSET-opdracht.

- Als u wilt dat alle kolommen worden gelezen uit CSV-gegevensbestanden, geeft u de kolomnamen en de bijbehorende gegevenstypen op. Als u een subset van deze kolommen wilt opvragen, gebruikt u rangtelwoorden om de kolommen uit de oorspronkelijke gegevensbestanden te kiezen op rangtelwoord. Kolommen worden gebonden op aanduiding via rangnummer.
- Als het Parquet-gegevensbestanden betreft, geeft u kolomnamen op die overeenkomen met de kolomnamen in de oorspronkelijke gegevensbestanden. Kolommen worden op naam gebonden.

```syntaxsql
OPENROWSET
...
| BULK 'data_file',
{ FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
) AS table_alias(column_alias,...n) | WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })
```

Raadpleeg [CSV-bestanden lezen zonder alle kolommen op te geven](query-single-csv-file.md#returning-subset-of-columns) voor voorbeelden.

### <a name="schema-inference"></a>Schema-deductie

Door de WITH-clausule weg te laten in de OPENROWSET-opdracht, kunt u de service de opdracht geven automatisch het schema te detecteren (deduceren) van de onderliggende bestanden.

> [!NOTE]
> Dit werkt momenteel alleen voor de PARQUET-bestandsindeling.

```sql
OPENROWSET(
BULK N'path_to_file(s)', FORMAT='PARQUET');
```

Zorg ervoor dat [de juiste gededuceerde datatypes](best-practices-sql-on-demand.md#check-inferred-data-types) worden gebruikt voor de beste prestatie. 

### <a name="filename-function"></a>Bestandsnaamfunctie

Deze functie retourneert de bestandsnaam waaruit de rij afkomstig is. 

Lees de sectie Bestandsnaam in het artikel [Query's uitvoeren op specifieke bestanden](query-specific-files.md#filename)om een query uit te voeren op specifieke bestanden.

Het retourgegevenstype is nvarchar(1024). Cast het resultaat van de functie bestandsnaam altijd naar het juiste gegevenstype voor de beste prestatie. Als u een tekengegevenstype gebruikt, zorg er dan voor dat de juiste lengte wordt gebruikt.

### <a name="filepath-function"></a>Bestandspadfunctie

Deze functie retourneert een volledig pad of een deel van een pad:

- Wanneer de functie wordt aangeroepen zonder parameter, wordt het volledige bestandspad geretourneerd waaruit een rij afkomstig is.
- Wanneer de functie wordt aangeroepen met parameter, wordt het deel van het pad geretourneerd dat overeenkomt met het jokerteken op de positie die is gespecificeerd in de parameter. Parameterwaarde 1 zou bijvoorbeeld het deel van het pad retourneren dat overeenkomt met het eerste jokerteken.

Lees de sectie Bestandspad van het artikel [Query uitvoeren op specifieke bestanden](query-specific-files.md#filepath) voor meer informatie.

Het retourgegevenstype is nvarchar(1024). Cast het resultaat van de functie bestandspad altijd naar het juiste gegevenstype voor de beste prestatie. Als u een tekengegevenstype gebruikt, zorg er dan voor dat de juiste lengte wordt gebruikt.

### <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Werken met complexe types en geneste of herhaalde gegevensstructuren

SQL on-demand heeft onderstaande extensies toegevoegd om een soepele ervaring te kunnen bieden bij het werken met gegevens die zijn opgeslagen in geneste of herhaalde gegevenstypes, zoals in [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types)-bestanden.

#### <a name="project-nested-or-repeated-data"></a>Geneste of herhaalde gegevens projecteren

Voer een SELECT-opdracht uit op het Parquet-bestand dat kolommen of geneste gegevenstypes bevat om gegevens te projecteren. Geneste waarden worden met serienummer in JSON geplaatst en geretourneerd als een SQL-gegevenstype varchar(8000).

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Raadpleeg de sectie Geneste of herhaalde gegevens projecteren van het artikel [Query uitvoeren op met Parquet geneste typen](query-parquet-nested-types.md#project-nested-or-repeated-data) voor meer informatie.

#### <a name="access-elements-from-nested-columns"></a>Elementen benaderen vanuit geneste kolommen

Gebruik 'puntnotatie' om veldnamen samen te voegen in het pad om geneste elementen te benaderen van een geneste kolom, zoals Struct. Geef het pad als kolomnaam in de WITH-clausule van de functie OPENROWSET op.

Het voorbeeld van het syntaxisfragment ziet er als volgt uit:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

De functie OPENROWSET zoekt standaard naar overeenkomsten van de bronveldnaam en het bronveldpad met de kolomnamen die zijn opgegeven in de WITH-clausule. Elementen binnen verschillende nestniveaus binnen hetzelfde Parquet-bronbestand kunnen worden benaderd via de WITH-clausule.

**Retourwaarden**

- Voor alle Parquet-types die zich niet in de groep Genest type bevinden, retourneert deze functie een scalaire waarde, zoals integer, decimaal en varchar van het opgegeven element en op het opgegeven pad.
- Als het pad wijst naar een element van het Geneste type, retourneert de functie een JSON-fragment dat begint met het hoogste element in het opgegeven pad. Het JSON-fragment is van het type varchar(8000).
- Als de eigenschap niet kan worden gevonden in de opgegeven kolomnaam, retourneert de functie een fout.
- Als de eigenschap niet kan worden gevonden in de opgegeven kolomnaam, retourneert de functie, afhankelijk van [Padmodus](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE), een fout in de strict-modus of null in de lax-modus.

Raadpleeg de sectie Elementen benaderen vanuit geneste kolommen in het artikel [Query uitvoeren op met Parquet geneste typen](query-parquet-nested-types.md#access-elements-from-nested-columns) voor voorbeelden van query's.

#### <a name="access-elements-from-repeated-columns"></a>Elementen benaderen vanuit herhaalde kolommen

Gebruik de functie [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor elk scalair element dat u wilt projecteren om elementen van een herhaalde kolom te benaderen, zoals een element van een Matrix of Map en geef het volgende op:

- als eerste parameter Geneste of herhaalde kolom
- als tweede parameter een [JSON-pad](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) dat de elementen of de eigenschappen opgeeft die moeten worden benaderd

Gebruik de functie [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor elk niet-scalair element dat u wilt projecteren om niet-scalaire elementen van een herhaalde kolom te benaderen en geef het volgende op:

- als eerste parameter Geneste of herhaalde kolom
- als tweede parameter een [JSON-pad](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) dat de elementen of de eigenschappen opgeeft die moeten worden benaderd

Zie hieronder een syntaxis-fragment:

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

U kunt voorbeelden van query's voor het benaderen van elementen van herhaalde kolommen vinden in het artikel [Query uitvoeren op met Parquet geneste typen](query-parquet-nested-types.md#access-elements-from-repeated-columns).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het uitvoeren van query's op verschillende bestandstypes en het maken en gebruiken van weergaven:

- [Query's uitvoeren op één CSV-bestand](query-single-csv-file.md)
- [Query's uitvoeren op Parquet-bestanden](query-parquet-files.md)
- [Query's uitvoeren op JSON-bestanden](query-json-files.md)
- [Query uitvoeren op met Parquet geneste typen](query-parquet-nested-types.md)
- [Query's uitvoeren op mappen en meerdere CSV-bestanden](query-folders-multiple-csv-files.md)
- [Metagegevens van bestand gebruiken in query's](query-specific-files.md)
- [Weergaven maken en gebruiken](create-use-views.md)
