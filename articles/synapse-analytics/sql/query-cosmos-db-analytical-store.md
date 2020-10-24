---
title: Query's uitvoeren op Azure Cosmos DB gegevens met behulp van serverloze SQL-groep in azure Synapse-koppeling (preview-versie)
description: In dit artikel leert u hoe u Azure Cosmos DB query's kunt uitvoeren met behulp van SQL op aanvraag in azure Synapse link (preview).
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 99fcdd0232e2991acaceb6838bff0b00c6824dfb
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92474900"
---
# <a name="query-azure-cosmos-db-data-with-serverless-sql-pool-in-azure-synapse-link-preview"></a>Query's uitvoeren op Azure Cosmos DB gegevens met serverloze SQL-groep in azure Synapse-koppeling (preview-versie)

Met een Synapse serverloze SQL-pool (voorheen SQL on-demand) kunt u gegevens in uw Azure Cosmos DB containers [die in bijna](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) realtime zijn ingeschakeld, analyseren zonder dat dit van invloed is op de prestaties van uw transactionele werk belastingen. Het biedt een bekende T-SQL-syntaxis voor het opvragen van gegevens uit de [analytische opslag](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) en de geïntegreerde connectiviteit met een breed scala aan bi-en ad-hoc hulp middelen voor query's via de T-SQL-interface.

Voor het uitvoeren van query's in Azure Cosmos DB wordt de volledige [selectie](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15) Surface Area ondersteund via de [OpenRowSet](develop-openrowset.md) -functie, inclusief het meren deel van [SQL-functies en-Opera tors](overview-features.md). U kunt ook resultaten van de query opslaan die gegevens uit Azure Cosmos DB leest, samen met gegevens in Azure Blob Storage of Azure Data Lake Storage met de [optie externe tabel maken als selecteren](develop-tables-cetas.md#cetas-in-sql-on-demand). U kunt momenteel geen serverloze SQL-groeps query resultaten opslaan in Azure Cosmos DB met behulp van [CETAS](develop-tables-cetas.md#cetas-in-sql-on-demand).

In dit artikel leert u hoe u een query kunt schrijven met een serverloze SQL-groep waarmee gegevens worden opgevraagd van Azure Cosmos DB containers die Synapse-koppeling zijn ingeschakeld. In [deze](./tutorial-data-analyst.md) zelf studie vindt u meer informatie over het bouwen van SERVERloze SQL-pool weergaven over Azure Cosmos DB containers en het verbinden ervan met Power bi modellen. 

## <a name="overview"></a>Overzicht

Voor het ondersteunen van het uitvoeren van query's en het analyseren van gegevens in Azure Cosmos DB Analytical Store gebruikt de serverloze SQL-pool de volgende `OPENROWSET` syntaxis:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

Met de Azure Cosmos DB connection string geeft u de Azure Cosmos DB account naam, de database naam, de hoofd sleutel van het database account en een optionele regio naam te `OPENROWSET` gebruiken. 

> [!IMPORTANT]
> Zorg ervoor dat u alias gebruikt na `OPENROWSET` . Er is een [bekend probleem](#known-issues) waardoor het verbindings probleem met het Synapse SQL-eind punt zonder server wordt veroorzaakt als u de alias na-functie niet opgeeft `OPENROWSET` .

De connection string heeft de volgende indeling:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

De naam van de Azure Cosmos DB container is opgegeven zonder aanhalings tekens in de `OPENROWSET` syntaxis. Als de naam van de container speciale tekens bevat (bijvoorbeeld een streepje '-'), moet de naam tussen `[]` vier Kante haken worden geplaatst in de `OPENROWSET` syntaxis.

> [!NOTE]
> Een serverloze SQL-groep biedt geen ondersteuning voor het uitvoeren van query's Azure Cosmos DB transactionele opslag.

## <a name="sample-data-set"></a>Set voorbeeldgegevens

De voor beelden in dit artikel zijn gebaseerd op gegevens van het [Euro pees centrum voor ziekte preventie en-controle (ECDC) COVID-19 gevallen](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) en [COVID-19 open Research DataSet (koord-19), DOI: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). 

U kunt de licentie en de structuur van de gegevens op deze pagina's bekijken en voorbeeld gegevens downloaden voor [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) -en [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) -gegevens sets.

Volg de instructies in dit artikel voor informatie over het opvragen van Cosmos DB gegevens met een serverloze SQL-groep, zorg ervoor dat u de volgende resources maakt:
* Een Azure Cosmos DB database account dat [Synapse-koppeling is ingeschakeld](../../cosmos-db/configure-synapse-link.md)
* Een Azure Cosmos DB-Data Base met de naam `covid`
* Twee Azure Cosmos DB containers met de naam `EcdcCases` en `Cord19` met de bovenstaande voorbeeld gegevens sets geladen.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Azure Cosmos DB gegevens verkennen met een automatische schema-deinterferentie

De gemakkelijkste manier om gegevens in Azure Cosmos DB te verkennen, is door gebruik te maken van de automatische functionaliteit voor het afwijzen van schema's. Door de `WITH` component van de instructie te weglaten `OPENROWSET` , kunt u een SERVERloze SQL-groep instrueren om het schema van de analytische opslag van de Azure Cosmos DB-container automatisch te detecteren (af te leiden).

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
In het bovenstaande voor beeld geven we een serverloze SQL-groep om verbinding te maken met de `covid` Data base in azure Cosmos DB account dat is `MyCosmosDbAccount` geverifieerd met behulp van de Azure Cosmos DB sleutel (pop in het bovenstaande voor beeld). Vervolgens opent u de `EcdcCases` analytische opslag van de container in de `West US 2` regio. Omdat er geen projectie van specifieke eigenschappen is, `OPENROWSET` retourneert functie alle eigenschappen van de Azure Cosmos DB items.

Als u gegevens uit de andere container in dezelfde Azure Cosmos DB-Data Base wilt verkennen, kunt u dezelfde connection string en de vereiste container referentie gebruiken als derde para meter:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Expliciet schema opgeven

In het geval van automatische schema-deinterferentie in `OPENROWSET` biedt een eenvoudige, gebruiks vriendelijke querience, uw bedrijfs scenario's vereisen mogelijk dat u het schema expliciet moet opgeven voor alleen-lezen relevante eigenschappen van de Azure Cosmos DB gegevens.

`OPENROWSET` Hiermee kunt u expliciet opgeven welke eigenschappen u wilt lezen uit de gegevens in de container en de gegevens typen ervan opgeven. Stel dat we gegevens hebben geïmporteerd uit [ECDC COVID gegevensset](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) met de volgende structuur in azure Cosmos DB:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Deze platte JSON-documenten in Azure Cosmos DB kunnen worden weer gegeven als een set rijen en kolommen in Synapse SQL. `OPENROWSET` met de functie kunt u een subset van eigenschappen opgeven die u wilt lezen en de exacte kolom typen in de `WITH` component:

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Het resultaat van deze query kan er als volgt uitzien:

| date_rep | meldingen | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Raadpleeg de [regels voor SQL-type toewijzingen](#azure-cosmos-db-to-sql-type-mappings) aan het einde van het artikel voor meer informatie over de SQL-typen die moeten worden gebruikt voor Azure Cosmos DB waarde.

## <a name="querying-nested-objects-and-arrays"></a>Query's uitvoeren op geneste objecten en matrices

Met Azure Cosmos DB kunt u complexere gegevens modellen vertegenwoordigen door ze als geneste objecten of matrices samen te stellen. Met de functie voor automatische synchronisatie van de Synapse-koppeling voor Azure Cosmos DB wordt de schema weergave in de analytische out-of-the-box beheerd. Dit omvat het verwerken van geneste gegevens typen voor uitgebreide query's vanuit een serverloze SQL-pool.

De gegevensset van de koord bevat bijvoorbeeld JSON [-](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) documenten die volgen op de volgende structuur:

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

De geneste objecten en matrices in Azure Cosmos DB worden weer gegeven als JSON-teken reeksen in het query resultaat wanneer de `OPENROWSET` functie deze leest. Een optie voor het lezen van de waarden van deze complexe typen, omdat SQL-kolommen gebruikmaken van SQL-JSON-functies:

```sql
SELECT
    title = JSON_VALUE(metadata, '$.title'),
    authors = JSON_QUERY(metadata, '$.authors'),
    first_author_name = JSON_VALUE(metadata, '$.authors[0].first')
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( metadata varchar(MAX) ) AS docs;
```

Het resultaat van deze query kan er als volgt uitzien:

| titel | verbergen | first_autor_name |
| --- | --- | --- |
| Aanvullende informatie een eco-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien |  

Als alternatief kunt u ook de paden naar geneste waarden in de objecten opgeven bij het gebruik van de `WITH` component:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( title varchar(1000) '$.metadata.title',
           authors varchar(max) '$.metadata.authors'
    ) AS docs;
```

Meer informatie over het analyseren van [complexe gegevens typen in Synapse-koppeling](../how-to-analyze-complex-schema.md) en [geneste structuren in een serverloze SQL-groep](query-parquet-nested-types.md).

> [!IMPORTANT]
> Als er onverwachte tekens worden weer geven in uw tekst zoals `MÃƒÂ©lade` in plaats van `Mélade` de database sortering is niet ingesteld op [utf8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) -sortering. 
> [Wijzig de sortering van de data base](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) in een utf8-sortering met behulp van een SQL-instructie zoals `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .


## <a name="flattening-nested-arrays"></a>Geneste matrices afvlakken

Azure Cosmos DB gegevens kunnen geneste submatrixen hebben, zoals de matrix van de auteur uit de [Cord19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) -gegevensset:

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

In sommige gevallen moet u mogelijk de eigenschappen van het bovenste item (meta gegevens) toevoegen aan alle elementen van de matrix (auteurs). Met serverloze SQL-pool kunt u geneste structuren plat maken door de functie toe te passen `OPENJSON` op de geneste matrix:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

Het resultaat van deze query kan er als volgt uitzien:

| titel | verbergen | instantie | duren | betrokkenheid |
| --- | --- | --- | --- | --- |
| Aanvullende informatie een eco-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Aanvullende informatie een eco-epidemi... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 3 # |`{"laboratory":"","institution":"U…` | 
| Aanvullende informatie een eco-epidemi... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Aanvullende informatie een eco-epidemi... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Als er onverwachte tekens worden weer geven in uw tekst zoals `MÃƒÂ©lade` in plaats van `Mélade` de database sortering is niet ingesteld op [utf8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) -sortering. 
> [Wijzig de sortering van de data base](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) in een utf8-sortering met behulp van een SQL-instructie zoals `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Toewijzingen van het SQL-type Azure Cosmos DB

Het is belang rijk om eerst te weten dat hoewel Azure Cosmos DB transactionele Store schema-neutraal is, de analytische opslag geschematiseerde kan worden geoptimaliseerd voor de prestaties van analytische query's. Met de functie voor automatische synchronisatie van de Synapse-koppeling beheert Azure Cosmos DB de schema weergave in de analytische out-of-the-box. Dit omvat het verwerken van geneste gegevens typen. Omdat serverloze SQL-pool de analytische opslag opvraagt, is het belang rijk om te begrijpen hoe Azure Cosmos DB invoer gegevens typen worden toegewezen aan SQL-gegevens typen.

Azure Cosmos DB-accounts van de SQL-API (core) ondersteunen JSON-eigenschaps typen Number, String, Boolean, null, genest object of matrix. U moet SQL-typen kiezen die overeenkomen met deze JSON-typen als u `WITH` component gebruikt in `OPENROWSET` . Zie onder de SQL-kolom typen die moeten worden gebruikt voor verschillende eigenschaps typen in Azure Cosmos DB.

| Azure Cosmos DB eigenschaps type | SQL-kolom Type |
| --- | --- |
| Boolean-waarde | bit |
| Geheel getal | bigint |
| Decimaal | float |
| Tekenreeks | varchar (UTF8-database sortering) |
| Datum/tijd (ISO-indelings teken reeks) | varchar (30) |
| Datum en tijd (UNIX-tijds tempel) | bigint |
| Null | `any SQL type` 
| Genest object of matrix | varchar (max) (UTF8-database sortering), geserialiseerd als JSON-tekst |

Voor het uitvoeren van query's op Azure Cosmos DB accounts van de Mongo DB-API-soort, kunt u meer te weten komen over de schema weergave voor volledige beeld kwaliteit in het analytische archief en de uitgebreide eigenschapnamen die [hier](../../cosmos-db/analytical-store-introduction.md#analytical-schema)worden gebruikt.

## <a name="known-issues"></a>Bekende problemen

- Alias **moet** worden opgegeven na `OPENROWSET` -functie (bijvoorbeeld `OPENROWSET (...) AS function_alias` ). Het weglaten van een alias kan leiden tot een verbindings probleem en Synapse SQL-eind punt zonder server mogelijk tijdelijk niet beschikbaar. Dit probleem wordt opgelost in november 2020.
- Een serverloze SQL-pool biedt momenteel geen ondersteuning voor [Azure Cosmos DB schema met volledige betrouw baarheid](../../cosmos-db/analytical-store-introduction.md#schema-representation). Gebruik serverloze SQL-pool alleen voor toegang tot Cosmos DB goed gedefinieerd schema.

In de volgende tabel worden mogelijke fouten en acties voor het oplossen van problemen weer gegeven:

| Fout | Hoofdoorzaak |
| --- | --- |
| Syntaxis fouten:<br/> -Onjuiste syntaxis bij OPENROWSET<br/> - `...` is geen herkende optie voor BULKSGEWIJZE OPENROWSET-provider.<br/> -Onjuiste syntaxis bij `...` | Mogelijke hoofd oorzaken<br/> -Niet ' CosmosDB ' gebruiken als eerste para meter,<br/> -Letterlijke teken reeks gebruiken in plaats van id in derde para meter,<br/> -Niet opgeven derde para meter (container naam) |
| Er is een fout opgetreden in de CosmosDB-connection string | -Account, Data Base, sleutel is niet opgegeven <br/> -Er is een optie in connection string die niet wordt herkend.<br/> -Punt komma `;` wordt toegevoegd aan het einde van Connection String |
| Het omzetten van het CosmosDB-pad is mislukt met de fout ' onjuiste account naam ' of ' onjuiste database naam ' | De opgegeven account naam, database naam of container kan niet worden gevonden of de analyse opslag heeft geen o de opgegeven verzameling ingeschakeld|
| Het omzetten van het CosmosDB-pad is mislukt met de fout ' onjuiste geheime waarde ' of ' geheim is null of leeg ' | De account sleutel is ongeldig of ontbreekt. |
| Kolom `column name` van het type `type name` is niet compatibel met het externe gegevens type `type name` | Het opgegeven kolom Type in de `WITH` component komt niet overeen met het type in Cosmos DB container. Probeer het kolom type te wijzigen, omdat dit wordt beschreven in de sectie [Azure Cosmos DB aan de SQL-type toewijzingen](#azure-cosmos-db-to-sql-type-mappings) of het `VARCHAR` type gebruik. |
| Kolom bevat `NULL` waarden in alle cellen. | Mogelijk verkeerde kolom naam of pad-expressie in `WITH` component. De kolom naam (of padexpressie na de kolom Type) in- `WITH` component moet overeenkomen met een eigenschaps naam in Cosmos DB verzameling. Vergelijking is **hoofdletter gevoelig**  (bijvoorbeeld `productCode` en `ProductCode` andere eigenschappen). |

U kunt suggesties en problemen melden op de [pagina met feedback over Azure Synapse](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen:

- [Power BI en serverloze Synapse SQL-pool gebruiken met Azure Synapse-koppeling](../../cosmos-db/synapse-link-power-bi.md)
- [Weer gaven maken en gebruiken in SQL op aanvraag](create-use-views.md) 
- [Zelf studie over het bouwen van on-demand weer gaven van SQL over Azure Cosmos DB en verbinding maken met Power BI modellen via DirectQuery](./tutorial-data-analyst.md)
