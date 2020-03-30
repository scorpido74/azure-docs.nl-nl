---
title: Werken met R- en SQL-gegevenstypen en -objecten
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Meer informatie over het werken met gegevenstypen en gegevensobjecten in R met Azure SQL Database met Machine Learning Services (voorbeeld), inclusief veelvoorkomende problemen die u tegenkomen.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 0bb3abc7b7102da55c9ededcadd7a301f74065ab
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349325"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Werken met R- en SQL-gegevens in Azure SQL Database Machine Learning Services (voorbeeld)

In dit artikel worden enkele veelvoorkomende problemen besproken die u tegenkomen bij het verplaatsen van gegevens tussen R en SQL Database in [Machine Learning Services (met R) in Azure SQL Database.](sql-database-machine-learning-services-overview.md) De ervaring die je opdoet door middel van deze oefening biedt essentiële achtergrond bij het werken met gegevens in je eigen script.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Veelvoorkomende problemen die u tegenkomen zijn:

- Gegevenstypen komen soms niet overeen
- Impliciete conversies kunnen plaatsvinden
- Bewerkingen casten en converteren zijn soms vereist
- R en SQL gebruiken verschillende gegevensobjecten

## <a name="prerequisites"></a>Vereisten

- Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/) voordat u begint.

- Als u de voorbeeldcode in deze oefeningen wilt uitvoeren, moet u eerst een Azure SQL-database hebben waarbij Machine Learning Services (met R) is ingeschakeld. Na de onboarding voor de openbare preview wordt Machine Learning voor u ingeschakeld voor uw bestaande of nieuwe database. Volg de stappen in [Meld u aan voor de preview-versie](sql-database-machine-learning-services-overview.md#signup).

- Zorg ervoor dat u de nieuwste [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) hebt geïnstalleerd. U R-scripts uitvoeren met andere databasebeheer- of querytools, maar in deze quickstart gebruikt u SSMS.

## <a name="working-with-a-data-frame"></a>Werken met een gegevensframe

Wanneer uw script de resultaten van R naar SQL retourneert, moet het de gegevens retourneren als een **data.frame.** Elk ander type object dat u in uw script genereert - of dat nu een lijst, factor, vector of binaire gegevens is - moet worden geconverteerd naar een gegevensframe als u het wilt uitvoeren als onderdeel van de opgeslagen procedureresultaten. Gelukkig zijn er meerdere R-functies om het wijzigen van andere objecten in een gegevensframe te ondersteunen. U zelfs een binair model serialiseren en retourneren in een gegevensframe, wat u later in dit artikel zult doen.

Laten we eerst experimenteren met een aantal basisR-objecten - vectoren, matrices en lijsten - en zien hoe conversie naar een gegevensframe de uitvoer verandert in SQL.

Vergelijk deze twee "Hello World" scripts in R. De scripts zien er bijna identiek uit, maar de eerste retourneert een enkele kolom van drie waarden, terwijl de tweede drie kolommen met elk één waarde retourneert.

**Voorbeeld 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**Voorbeeld 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

Waarom zijn de resultaten zo verschillend?

Het antwoord kan meestal worden `str()` gevonden met behulp van de Opdracht R. Voeg de `str(object_name)` functie overal in uw R-script toe om het gegevensschema van het opgegeven R-object als informatief bericht terug te sturen. U de berichten weergeven op het tabblad **Berichten** in SSMS.

Als u wilt achterhalen waarom voorbeeld 1 en voorbeeld `str(OutputDataSet)` 2 zulke `@script` verschillende resultaten hebben, voegt u de regel aan het einde van de variabele definitie in elke instructie in, zoals deze:

**Voorbeeld 1 met str-functie toegevoegd**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**Voorbeeld 2 met str-functie toegevoegd**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Bekijk nu de tekst in **Berichten** om te zien waarom de uitvoer anders is.

**Resultaten - Voorbeeld 1**

```text
STDOUT message(s) from external script:
'data.frame':    3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Resultaten - Voorbeeld 2**

```text
STDOUT message(s) from external script:
'data.frame':    1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Zoals u zien, had een kleine wijziging in de Syntaxis van R een groot effect op het schema van de resultaten. Voor alle details worden de verschillen in R-gegevenstypen in details uitgelegd in de sectie *Gegevensstructuren* in ["Advanced R" van Hadley Wickham.](http://adv-r.had.co.nz)

Voor nu, gewoon zich ervan bewust dat je nodig hebt om de verwachte resultaten te controleren bij het afzetten van R-objecten in dataframes.

> [!TIP]
> U ook R-identiteitsfuncties `is.matrix` `is.vector`gebruiken, zoals, om informatie over de interne gegevensstructuur terug te sturen.

## <a name="implicit-conversion-of-data-objects"></a>Impliciete conversie van gegevensobjecten

Elk R-gegevensobject heeft zijn eigen regels voor de manier waarop waarden worden verwerkt in combinatie met andere gegevensobjecten als de twee gegevensobjecten hetzelfde aantal dimensies hebben of als een gegevensobject heterogene gegevenstypen bevat.

Stel dat u matrixvermenigvuldiging wilt uitvoeren met R. U wilt een matrix met één kolom vermenigvuldigen met de drie waarden met een array met vier waarden en een 4x3-matrix verwachten.

Maak eerst een kleine tabel met testgegevens.

```sql
CREATE TABLE RTestData (col1 INT NOT NULL)

INSERT INTO RTestData
VALUES (1);

INSERT INTO RTestData
VALUES (10);

INSERT INTO RTestData
VALUES (100);
GO
```

Voer nu het volgende script uit.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

Onder de omslagen wordt de kolom met drie waarden geconverteerd naar een matrix met één kolom. Omdat een matrix slechts een speciaal geval is `y` van een array in R, wordt de array impliciet afgesloten tot een matrix met één kolom om de twee argumenten te laten conformeren.

**Resultaten**

|Kolom 1|Kolom 2|Col3 (Col3)|Col4 (Col4)|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

Houd er echter rekening mee wat er `y`gebeurt als u de grootte van de array wijzigt.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

Nu geeft R als resultaat één waarde terug.

**Resultaten**
    
|Kolom 1|
|---|
|1542|

Hoe komt dat? In dit geval, omdat de twee argumenten kunnen worden behandeld als vectoren van dezelfde lengte, retourneert R het binnenste product als een matrix.  Dit is het verwachte gedrag volgens de regels van de lineaire algebra. Het kan echter problemen veroorzaken als uw downstream-toepassing verwacht dat het uitvoerschema nooit zal veranderen!

## <a name="merge-or-multiply-columns-of-different-length"></a>Kolommen van verschillende lengte samenvoegen of vermenigvuldigen

R biedt een grote flexibiliteit voor het werken met vectoren van verschillende grootte, en voor het combineren van deze kolom-achtige structuren in data frames. Lijsten met vectoren kunnen eruit zien als een tabel, maar ze volgen niet alle regels die gelden voor databasetabellen.

In het volgende script wordt bijvoorbeeld een numerieke array van `df1`lengte 6 gedefinieerd en wordt deze opgeslagen in de R-variabele . De numerieke array wordt vervolgens gecombineerd met de gehele getallen van de RTestData-tabel (hierboven gemaakt) `df2`die drie (3) waarden bevat, om een nieuw gegevensframe te maken, .

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

Om het gegevensframe in te vullen, herhaalt R de elementen die uit RTestData zijn `df1`opgehaald zo vaak als nodig is om het aantal elementen in de array te evenaren.

**Resultaten**
    
|*Kolom 2*|*Col3 (Col3)*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Vergeet niet dat een gegevensframe er alleen uitziet als een tabel, maar eigenlijk een lijst met vectoren is.

## <a name="cast-or-convert-sql-data"></a>SQL-gegevens casten of converteren

R en SQL gebruiken niet dezelfde gegevenstypen, dus wanneer u een query uitvoert in SQL om gegevens te krijgen en dat vervolgens door te geven aan de R-runtime, vindt meestal een soort impliciete conversie plaats. Een andere set conversies vindt plaats wanneer u gegevens van R naar SQL retourneert.

- SQL duwt de gegevens van de query naar het R-proces en zet deze om in een interne representatie voor een grotere efficiëntie.
- De R-runtime laadt de gegevens in een variabele data.frame en voert zijn eigen bewerkingen uit op de gegevens.
- De databaseengine retourneert de gegevens naar SQL via een beveiligde interne verbinding en presenteert de gegevens in termen van SQL-gegevenstypen.
- U krijgt de gegevens door verbinding te maken met SQL via een client- of netwerkbibliotheek die SQL-query's kan uitgeven en tabelgegevenssets kan verwerken. Deze clienttoepassing kan mogelijk invloed hebben op de gegevens op andere manieren.

Voer een query zoals deze uit in het datawarehouse [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) om te zien hoe dit werkt. Met deze weergave worden verkoopgegevens geretourneerd die worden gebruikt bij het maken van prognoses.

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> U elke versie van AdventureWorks gebruiken of een andere query maken met een eigen database. Het punt is om te proberen bepaalde gegevens te verwerken die tekst, datumtijd en numerieke waarden bevatten.

Probeer deze query nu te gebruiken als invoer voor de opgeslagen procedure.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

Als er een fout optreedt, moet u waarschijnlijk enkele bewerkingen aan de querytekst uitvoeren. Het tekenreekspredicaat in de WHERE-component moet bijvoorbeeld worden ingesloten door twee sets enkele aanhalingstekens.

Nadat u de query hebt laten `str` werken, controleert u de resultaten van de functie om te zien hoe R de invoergegevens behandelt.

**Resultaten**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- De kolom datetime is verwerkt met behulp van het R-gegevenstype **POSIXct**.
- De tekstkolom "ProductSeries" is geïdentificeerd als een **factor,** wat een categorische variabele betekent. Tekenreekswaarden worden standaard als factoren behandeld. Als u een tekenreeks doorgeeft aan R, wordt deze geconverteerd naar een geheel getal voor intern gebruik en vervolgens weer toegewezen aan de tekenreeks op uitvoer.

## <a name="summary"></a>Samenvatting

Uit zelfs deze korte voorbeelden u de noodzaak zien om de effecten van gegevensconversie te controleren bij het doorgeven van SQL-query's als invoer. Omdat sommige SQL-gegevenstypen niet door R worden ondersteund, u rekening houden met deze manieren om fouten te voorkomen:

- Test uw gegevens vooraf en verifieer kolommen of waarden in uw schema die een probleem kunnen zijn wanneer deze worden doorgegeven aan R-code.
- Geef kolommen in uw invoergegevensbron afzonderlijk `SELECT *`op in plaats van te gebruiken en weet hoe elke kolom wordt behandeld.
- Voer expliciete casts uit als dat nodig is bij het voorbereiden van uw invoergegevens, om verrassingen te voorkomen.
- Vermijd het doorgeven van kolommen met gegevens (zoals GUIDS of roeiguids) die fouten veroorzaken en niet nuttig zijn voor modellering.

Zie [R-bibliotheken en gegevenstypen voor](/sql/advanced-analytics/r/r-libraries-and-data-types)meer informatie over ondersteunde en niet-ondersteunde R-gegevenstypen.
