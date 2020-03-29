---
title: Functies maken in SQL Server met SQL en Python - Team Data Science Process
description: Genereer functies voor gegevens die zijn opgeslagen in een SQL Server VM op Azure met SQL en Python - onderdeel van het Team Data Science-proces.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 58fa98005d7d89e84404d99cf4f55e456fd91f21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721741"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Met SQL en Python functies maken voor gegevens in SQL Server
In dit document ziet u hoe u functies genereren voor gegevens die zijn opgeslagen in een SQL Server VM op Azure, waarmee algoritmen efficiënter uit de gegevens kunnen leren. U SQL of een programmeertaal zoals Python gebruiken om deze taak uit te voeren. Beide benaderingen worden hier gedemonstreerd.

Deze taak is een stap in het [Team Data Science Process (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

> [!NOTE]
> Voor een praktisch voorbeeld u de [NYC Taxi-gegevensset](https://www.andresmh.com/nyctaxitrips/) raadplegen en verwijzen naar de IPNB getiteld [NYC Data wrangling using IPython Notebook and SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) for an end-to-end walk-through.
> 
> 

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u:

* Een Azure-opslagaccount maken. Als u instructies nodig hebt, [raadpleegt u Een Azure Storage-account maken](../../storage/common/storage-account-create.md)
* Uw gegevens zijn opgeslagen in SQL Server. Zie Gegevens verplaatsen [naar een Azure SQL Database voor Azure Machine Learning](move-sql-azure.md) voor instructies over het verplaatsen van de gegevens daar.

## <a name="feature-generation-with-sql"></a><a name="sql-featuregen"></a>Functiegeneratie met SQL
In deze sectie beschrijven we manieren om functies te genereren met SQL:  

* [Count based Feature Generation](#sql-countfeature)
* [Binning Feature Generation](#sql-binningfeature)
* [De functies uit één kolom uitrollen](#sql-featurerollout)

> [!NOTE]
> Zodra u extra functies genereert, u deze toevoegen als kolommen aan de bestaande tabel of een nieuwe tabel maken met de extra functies en primaire sleutel, die kan worden samengevoegd met de oorspronkelijke tabel.
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Count based feature generation
Dit document toont twee manieren om count-functies te genereren. De eerste methode maakt gebruik van voorwaardelijke som en de tweede methode maakt gebruik van de "waar"-clausule. Deze nieuwe functies kunnen vervolgens worden samengevoegd met de oorspronkelijke tabel (met behulp van primaire sleutelkolommen) om telfuncties naast de oorspronkelijke gegevens te hebben.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Binning Feature Generation
In het volgende voorbeeld ziet u hoe u binned-functies genereert door een numerieke kolom te binning (met vijf opslaglocaties) die als functie kan worden gebruikt:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>De functies uit één kolom uitrollen
In deze sectie laten we zien hoe je één kolom in een tabel uitrolt om extra functies te genereren. In het voorbeeld wordt ervan uitgegaan dat er een breedte- of lengtekolom in de tabel staat waaruit u functies probeert te genereren.

Hier is een korte inleiding over breedte/lengte locatiegegevens (bemiddeld van stackoverflow). `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude` Hier volgen enkele handige dingen om te begrijpen over locatiegegevens voordat u functies uit het veld maakt:

* Het bord geeft aan of we noord of zuid, oost of west op de aardbol zijn.
* Een niet-nul honderden cijfer geeft lengte, geen breedte wordt gebruikt.
* Het tientallen cijfer geeft een positie aan ongeveer 1.000 kilometers. Het geeft nuttige informatie over welk continent of oceaan we zijn op.
* De eenheden cijfer (een decimale mate) geeft een positie tot 111 kilometer (60 nautische mijl, ongeveer 69 mijl). Het geeft ruwweg aan in welke grote staat of land/regio we ons bevinden.
* De eerste decimale plaats is de moeite waard tot 11,1 km: het kan de positie van een grote stad te onderscheiden van een naburige grote stad.
* De tweede decimale plaats is de moeite waard tot 1,1 km: het kan het ene dorp van het andere scheiden.
* De derde decimale plaats is tot 110 m waard: het kan een groot landbouwveld of institutionele campus identificeren.
* De vierde decimaal is de moeite waard tot 11 m: het kan een perceel grond identificeren. Het is vergelijkbaar met de typische nauwkeurigheid van een ongecorrigeerde GPS-eenheid zonder interferentie.
* De vijfde decimaal is de moeite waard tot 1,1 m: het onderscheidt bomen van elkaar. Nauwkeurigheid op dit niveau met commerciële GPS-eenheden kan alleen worden bereikt met differentiële correctie.
* De zesde decimale plaats is de moeite waard tot 0,11 m: u dit niveau gebruiken voor het in detail inrichten van structuren, voor het ontwerpen van landschappen, het aanleggen van wegen. Het zou meer dan goed genoeg moeten zijn voor het volgen van bewegingen van gletsjers en rivieren. Dit doel kan worden bereikt door het nemen van nauwgezette maatregelen met GPS, zoals differentieel gecorrigeerde GPS.

De locatie-informatie kan worden gefeaturiseerd door het scheiden van regio, locatie en stad informatie. Eenmaal kan ook een REST-eindpunt aanroepen, `https://msdn.microsoft.com/library/ff701710.aspx` zoals Bing Maps API (zie om de regio/districtinformatie te krijgen).

    select
        <location_columnname>
        ,round(<location_columnname>,0) as l1        
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

Deze locatiegebaseerde functies kunnen verder worden gebruikt om extra telfuncties te genereren, zoals eerder beschreven.

> [!TIP]
> U de records programmatisch invoegen met uw taal naar keuze. Mogelijk moet u de gegevens in stukjes invoegen om de schrijfefficiëntie te verbeteren. [Hier is een voorbeeld van hoe dit te doen met behulp van pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Een ander alternatief is het invoegen van gegevens in de database met behulp van [BCP-hulpprogramma](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Verbinding maken met Azure Machine Learning
De nieuw gegenereerde functie kan als kolom worden toegevoegd aan een bestaande tabel of worden opgeslagen in een nieuwe tabel en worden samengevoegd met de oorspronkelijke tabel voor machine learning. Functies kunnen worden gegenereerd of geopend als deze al zijn gemaakt, met behulp van de module [Gegevens importeren](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) in Azure ML zoals hieronder wordt weergegeven:

![Azure ML-lezers](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Een programmeertaal als Python gebruiken
Python gebruiken om functies te genereren wanneer de gegevens zich in SQL Server bevinden, is vergelijkbaar met het verwerken van gegevens in Azure blob met Python. Zie Azure [Blob-gegevens verwerken in uw data science-omgeving](data-blob.md)voor vergelijking. Laad de gegevens uit de database in een pandas-gegevensframe om deze verder te verwerken. Het proces van verbinding maken met de database en het laden van de gegevens in het gegevensframe wordt gedocumenteerd in deze sectie.

De volgende verbindingstekenreeksindeling kan worden gebruikt om verbinding te maken met een SQL Server-database van Python met pyodbc (servernaam, dbname, gebruikersnaam en wachtwoord vervangen door uw specifieke waarden):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

De [Panda's-bibliotheek](https://pandas.pydata.org/) in Python biedt een rijke set gegevensstructuren en gegevensanalysetools voor gegevensmanipulatie voor Python-programmering. In de volgende code worden de resultaten van een SQL Server-database in een Pandas-gegevensframe gelezen:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Nu u werken met het Pandas-gegevensframe zoals dat in onderwerpen wordt behandeld [Functies maken voor Azure blob-opslaggegevens met Panda.](create-features-blob.md)

