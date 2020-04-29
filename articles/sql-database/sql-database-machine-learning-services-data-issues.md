---
title: Werken met R-en SQL-gegevens typen en-objecten
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Meer informatie over het werken met gegevens typen en gegevens objecten in R met Azure SQL Database met behulp van Machine Learning Services (preview), waaronder veelvoorkomende problemen die kunnen optreden.
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
ROBOTS: NOINDEX
ms.openlocfilehash: e81cca3e20d5b6c050489e80b91d013d5e934cce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453196"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Werken met R-en SQL-gegevens in Azure SQL Database Machine Learning Services (preview-versie)

In dit artikel worden enkele veelvoorkomende problemen besproken die kunnen optreden bij het verplaatsen van gegevens tussen R en SQL Database in [Machine Learning Services (met R) in Azure SQL database](sql-database-machine-learning-services-overview.md). De ervaring die u in deze oefening krijgt, biedt essentiële achtergrond bij het werken met gegevens in uw eigen script.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Veelvoorkomende problemen die u kunt tegen komen, zijn onder andere:

- Gegevens typen komen soms niet overeen
- Impliciete conversies kunnen plaatsvinden
- Conversie-en conversie bewerkingen zijn soms vereist
- R en SQL gebruiken verschillende gegevens objecten

## <a name="prerequisites"></a>Vereisten

- Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/) voordat u begint.

- Als u de voorbeeld code in deze oefeningen wilt uitvoeren, moet u eerst [Azure SQL database hebben met Machine Learning Services (met R)](sql-database-machine-learning-services-overview.md) ingeschakeld.

- Zorg ervoor dat u de nieuwste versie van [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) hebt geïnstalleerd. U kunt R-scripts uitvoeren met andere database beheer-of query hulpprogramma's, maar in deze Snelstartgids gebruikt u SSMS.

## <a name="working-with-a-data-frame"></a>Werken met een gegevens frame

Wanneer uw script resultaten van R naar SQL retourneert, moeten de gegevens worden geretourneerd als een **gegevens. frame**. Elk ander type object dat u in uw script genereert: of dit een lijst, factor, vector of binaire gegevens is, moet worden geconverteerd naar een gegevens frame als u het wilt uitvoeren als onderdeel van de opgeslagen procedure resultaten. Gelukkig kunnen er meerdere R-functies worden ondersteund voor het wijzigen van andere objecten in een gegevens frame. U kunt zelfs een binair model serialiseren en dit retour neren in een gegevens frame, dat u later in dit artikel gaat doen.

Eerst gaan we experimenteren met enkele Basic R-objecten-vectoren,-matrices en-lijsten, en zien hoe conversie naar een gegevens frame de uitvoer wijzigt die is door gegeven aan SQL.

Vergelijk deze twee ' Hallo wereld-scripts in R. De scripts zien er bijna identiek uit, maar de eerste retourneert één kolom van drie waarden, terwijl de tweede drie kolommen retourneert met één waarde elk.

**Voor beeld 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**Voor beeld 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

Waarom zijn de resultaten anders?

Het antwoord kan meestal worden gevonden met behulp van `str()` de R-opdracht. Voeg de functie `str(object_name)` overal in uw R-script toe zodat het gegevens schema van het opgegeven R-object als een informatief bericht wordt geretourneerd. U kunt de berichten weer geven op het tabblad **berichten** in SSMS.

Als u wilt weten waarom voor beeld 1 en voor beeld 2 dergelijke verschillende resultaten hebben, `str(OutputDataSet)` voegt u de regel aan `@script` het einde van de definitie van de variabele in elke instructie in, zoals:

**Voor beeld 1 met de functie Str toegevoegd**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**Voor beeld 2 met de functie Str toegevoegd**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Bekijk nu de tekst in **berichten** om te zien waarom de uitvoer afwijkt.

**Resultaten-voor beeld 1**

```text
STDOUT message(s) from external script:
'data.frame':    3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Resultaten-voor beeld 2**

```text
STDOUT message(s) from external script:
'data.frame':    1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Zoals u kunt zien, heeft een kleine wijziging in de R-syntaxis een groot effect op het schema van de resultaten. Voor alle details worden de verschillen in R-gegevens typen uitgelegd in de sectie *gegevens structuren* in [' geavanceerde R ' door Hadley Wickham](http://adv-r.had.co.nz).

U moet er nu voor kiezen dat u de verwachte resultaten wilt controleren bij het afdwingen van R-objecten in gegevens frames.

> [!TIP]
> U kunt ook R-identiteits functies, zoals `is.matrix`, `is.vector`gebruiken om informatie over de interne gegevens structuur te retour neren.

## <a name="implicit-conversion-of-data-objects"></a>Impliciete conversie van gegevens objecten

Elk R-gegevens object heeft zijn eigen regels voor het verwerken van waarden in combi natie met andere gegevens objecten als de twee gegevens objecten hetzelfde aantal dimensies hebben, of als een gegevens object heterogene gegevens typen bevat.

Stel dat u een matrix vermenigvuldiging wilt uitvoeren met R. U wilt een matrix met één kolom met de drie waarden vermenigvuldigen met een matrix met vier waarden en een 4x3 matrix verwachten als resultaat.

Maak eerst een kleine tabel met test gegevens.

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

Onder de kaften wordt de kolom van drie waarden geconverteerd naar een matrix met één kolom. Omdat een matrix slechts een speciaal geval van een matrix in R is, wordt de `y` matrix impliciet gedwongen naar een matrix met één kolom om de twee argumenten te laten overeenkomen.

**Resultaten**

|Kolom 1|Kolom 2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

Houd er echter rekening mee wat er gebeurt wanneer u de grootte van `y`de matrix wijzigt.

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

R retourneert nu één waarde als resultaat.

**Resultaten**
    
|Kolom 1|
|---|
|1542|

Hoe komt dat? In dit geval, omdat de twee argumenten als vectoren van dezelfde lengte kunnen worden verwerkt, retourneert R het binnenste product als een matrix.  Dit is het verwachte gedrag volgens de regels van lineaire algebra. Dit kan echter problemen veroorzaken als uw downstream-toepassing verwacht dat het uitvoer schema nooit wordt gewijzigd.

## <a name="merge-or-multiply-columns-of-different-length"></a>Kolommen met een andere lengte samen voegen of vermenigvuldigen

R biedt een uitstekende flexibiliteit voor het werken met vectoren van verschillende grootten en voor het combi neren van deze kolom achtige structuren in gegevens frames. Lijsten met vectoren kunnen eruitzien als een tabel, maar ze volgen niet alle regels die database tabellen regelen.

Het volgende script definieert bijvoorbeeld een numerieke matrix met de lengte 6 en slaat deze op in de R- `df1`variabele. De numerieke matrix wordt vervolgens gecombineerd met de gehele getallen van de RTestData-tabel (gemaakt hierboven) die drie (3) waarden bevat, om een nieuw gegevens frame te `df2`maken.

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

Om het gegevens frame in te vullen, herhaalt R de elementen die worden opgehaald uit RTestData zo vaak als nodig is om overeen te komen met het aantal elementen `df1`in de matrix.

**Resultaten**
    
|*Kolom 2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Houd er rekening mee dat een gegevens frame alleen eruitziet als een tabel, maar wel in feite een lijst met vectoren.

## <a name="cast-or-convert-sql-data"></a>SQL-gegevens casten of converteren

R en SQL gebruiken niet dezelfde gegevens typen, dus wanneer u een query uitvoert in SQL om gegevens op te halen en vervolgens door te geven aan de R-runtime, vindt er meestal een type impliciete conversie plaats. Er vindt een andere set conversies plaats wanneer u gegevens van R naar SQL retourneert.

- SQL duwt de gegevens van de query naar het R-proces en converteert deze naar een interne representatie voor een grotere efficiëntie.
- De R-runtime laadt de gegevens in een gegevens. frame variabele en voert zijn eigen bewerkingen uit op de gegevens.
- De data base-engine retourneert de gegevens naar SQL met een beveiligde interne verbinding en geeft de gegevens weer in termen van SQL-gegevens typen.
- U krijgt de gegevens door verbinding te maken met SQL via een client-of netwerk bibliotheek die SQL-query's kan uitgeven en tabellaire gegevens sets kan verwerken. Deze client toepassing kan mogelijk van invloed zijn op de gegevens op andere manieren.

Als u wilt zien hoe dit werkt, voert u een query uit, zoals deze in het [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) -Data Warehouse. Deze weer gave retourneert verkoop gegevens die worden gebruikt bij het maken van prognoses.

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
> U kunt een wille keurige versie van AdventureWorks gebruiken of een andere query maken met behulp van een eigen data base. Het punt is om te proberen bepaalde gegevens af te handelen die tekst, DateTime en numerieke waarden bevatten.

U kunt deze query nu gebruiken als invoer voor de opgeslagen procedure.

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

Als er een fout optreedt, moet u waarschijnlijk enkele wijzigingen aanbrengen in de query tekst. Het teken reeks-predikaat in de component WHERE moet bijvoorbeeld tussen twee sets met enkele aanhalings tekens worden geplaatst.

Nadat u de query hebt uitgevoerd, controleert u de resultaten van `str` de functie om te zien hoe R de invoer gegevens verwerkt.

**Resultaten**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- De datum/tijd-kolom is verwerkt met behulp van het R-gegevens type **POSIXct**.
- De tekst kolom "ProductSeries" is geïdentificeerd als een **factor**, wat een categorische-variabele is. Teken reeks waarden worden standaard verwerkt als factoren. Als u een teken reeks doorgeeft aan R, wordt deze geconverteerd naar een geheel getal voor intern gebruik en vervolgens weer terug naar de teken reeks in uitvoer.

## <a name="summary"></a>Samenvatting

Van zelfs deze korte voor beelden kunt u zien dat de gevolgen van gegevens conversie moeten worden gecontroleerd wanneer SQL-query's worden door gegeven als invoer. Omdat sommige SQL-gegevens typen niet worden ondersteund door R, moet u rekening houden met de volgende manieren om fouten te voor komen:

- Test uw gegevens vooraf en controleer of de kolommen of waarden in uw schema goed zijn en of er een probleem is met de door gegeven R-code.
- Geef de kolommen in de invoer gegevens bron afzonderlijk op, in `SELECT *`plaats van met en u weet hoe elke kolom wordt verwerkt.
- Voer indien nodig expliciete casts uit wanneer u uw invoer gegevens voorbereidt, om verrassingen te voor komen.
- Vermijd het door geven van kolommen met gegevens (zoals GUID'S of rowguids) die fouten veroorzaken en die niet nuttig zijn voor het model leren.

Zie [r-bibliotheken en-gegevens typen](/sql/advanced-analytics/r/r-libraries-and-data-types)voor meer informatie over ondersteunde en niet-ondersteunde R-gegevens typen.
