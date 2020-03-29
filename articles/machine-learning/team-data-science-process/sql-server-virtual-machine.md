---
title: Gegevens verkennen in een virtuele SQL Server-machine - Team Data Science-proces
description: Verken + verwerk gegevens en genereer functies met Python of SQL in een virtuele SQL Server-machine op Azure.
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
ms.openlocfilehash: d3eb4d2faf58d1861fda9d04437f9f9530c77672
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718477"
---
# <a name="process-data-in-sql-server-virtual-machine-on-azure"></a><a name="heading"></a>Gegevens verwerken in virtuele SQL Server-machine in Azure
Dit document heeft betrekking op het verkennen van gegevens en het genereren van functies voor gegevens die zijn opgeslagen in een SQL Server VM op Azure. Dit doel kan worden voltooid door data getouwtrek met SQL of met behulp van een programmeertaal zoals Python.

> [!NOTE]
> De voorbeeldSQL-instructies in dit document gaan ervan uit dat gegevens zich in SQL Server bevindt. Als dit niet het is, raadpleegt u de proceskaart voor cloudgegevenswetenschap om te leren hoe u uw gegevens naar SQL Server verplaatsen.
> 
> 

## <a name="using-sql"></a><a name="SQL"></a>SQL gebruiken
We beschrijven de volgende gegevensruzietaken in deze sectie met SQL:

1. [Gegevensverkenning](#sql-dataexploration)
2. [Functiegeneratie](#sql-featuregen)

### <a name="data-exploration"></a><a name="sql-dataexploration"></a>Gegevensverkenning
Hier volgen een paar voorbeeldSQL-scripts die kunnen worden gebruikt om gegevensopslag in SQL Server te verkennen.

> [!NOTE]
> Voor een praktisch voorbeeld u de [NYC Taxi-gegevensset](https://www.andresmh.com/nyctaxitrips/) gebruiken en verwijzen naar de IPNB getiteld [NYC Data wrangling using IPython Notebook and SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) for an end-to-end walk-through.
> 
> 

1. Haal het aantal waarnemingen per dag op
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. De niveaus in een categorische kolom opbrengen
   
    `select  distinct <column_name> from <databasename>`
3. Het aantal niveaus in combinatie met twee categorische kolommen 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. De verdeling voor numerieke kolommen opvragen
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="feature-generation"></a><a name="sql-featuregen"></a>Functiegeneratie
In deze sectie beschrijven we manieren om functies te genereren met SQL:  

1. [Count based Feature Generation](#sql-countfeature)
2. [Binning Feature Generation](#sql-binningfeature)
3. [De functies uit één kolom uitrollen](#sql-featurerollout)

> [!NOTE]
> Zodra u extra functies genereert, u deze toevoegen als kolommen aan de bestaande tabel of een nieuwe tabel maken met de extra functies en primaire sleutel, die kan worden samengevoegd met de oorspronkelijke tabel. 
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Count based Feature Generation
In de volgende voorbeelden worden twee manieren gedemonstreerd om telfuncties te genereren. De eerste methode maakt gebruik van voorwaardelijke som en de tweede methode maakt gebruik van de "waar"-clausule. Deze resultaten kunnen vervolgens worden samengevoegd met de oorspronkelijke tabel (met behulp van primaire sleutelkolommen) om telfuncties naast de oorspronkelijke gegevens te hebben.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Binning Feature Generation
In het volgende voorbeeld ziet u hoe u binned-functies genereert door een numerieke kolom te binning (met vijf opslaglocaties) die als functie kan worden gebruikt:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>De functies uit één kolom uitrollen
In deze sectie laten we zien hoe je één kolom in een tabel uitrolt om extra functies te genereren. In het voorbeeld wordt ervan uitgegaan dat er een breedte- of lengtekolom in de tabel staat waaruit u functies probeert te genereren.

Hier is een korte inleiding over breedte/lengte locatiegegevens (bemiddeld van stackoverflow [Hoe meet je de nauwkeurigheid van breedte- en lengtegraad?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Deze richtlijnen zijn handig om te begrijpen voordat u locatie opneemt als een of meer functies:

* Het bord vertelt ons of we noord of zuid, oost of west op de aardbol.
* Een niet-nul honderden cijfer vertelt ons dat we gebruik maken van lengte, niet breedtegraad!
* Het tientallen cijfer geeft een positie aan ongeveer 1.000 kilometers. Het geeft ons nuttige informatie over welk continent of oceaan we zijn op.
* De eenheden cijfer (een decimale mate) geeft een positie tot 111 kilometer (60 nautische mijl, ongeveer 69 mijl). Het kan je ongeveer vertellen in welke staat, land of regio je je bevindt.
* De eerste decimale plaats is de moeite waard tot 11,1 km: het kan de positie van een grote stad te onderscheiden van een naburige grote stad.
* De tweede decimale plaats is de moeite waard tot 1,1 km: het kan het ene dorp van het andere scheiden.
* De derde decimale plaats is tot 110 m waard: het kan een groot landbouwveld of institutionele campus identificeren.
* De vierde decimaal is de moeite waard tot 11 m: het kan een perceel grond identificeren. Het is vergelijkbaar met de typische nauwkeurigheid van een ongecorrigeerde GPS-eenheid zonder interferentie.
* De vijfde decimaal is de moeite waard tot 1,1 m: het onderscheidt bomen van elkaar. Nauwkeurigheid op dit niveau met commerciële GPS-eenheden kan alleen worden bereikt met differentiële correctie.
* De zesde decimale plaats is de moeite waard tot 0,11 m: u dit gebruiken voor het in detail inrichten van structuren, voor het ontwerpen van landschappen, het aanleggen van wegen. Het zou meer dan goed genoeg moeten zijn voor het volgen van bewegingen van gletsjers en rivieren. Dit kan worden bereikt door het nemen van nauwgezette maatregelen met GPS, zoals differentieel gecorrigeerde GPS.

De locatie-informatie kan als volgt worden uitgevoerd, het scheiden van regio,locatie en stadsinformatie. U ook een REST-eindpunt aanroepen, zoals bing Maps API dat beschikbaar is bij [Zoek een locatie per punt](https://msdn.microsoft.com/library/ff701710.aspx) om de informatie over de regio/wijk te krijgen.

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
> U de records programmatisch invoegen met uw taal naar keuze. Mogelijk moet u de gegevens in stukken invoegen om de schrijfefficiëntie te verbeteren (voor een voorbeeld van hoe u dit doen met pyodbc, zie [Een HelloWorld-voorbeeld om sqlserver met python te openen).](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python) Een ander alternatief is het invoegen van gegevens in de database met behulp van het [BCP-hulpprogramma.](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Verbinding maken met Azure Machine Learning
De nieuw gegenereerde functie kan als kolom worden toegevoegd aan een bestaande tabel of worden opgeslagen in een nieuwe tabel en worden samengevoegd met de oorspronkelijke tabel voor machine learning. Functies kunnen worden gegenereerd of geopend als deze al zijn gemaakt, met behulp van de module [Gegevens importeren][import-data] in Azure Machine Learning zoals hieronder weergegeven:

![azureml-lezers][1] 

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Een programmeertaal als Python gebruiken
Python gebruiken om gegevens te verkennen en functies te genereren wanneer de gegevens zich in SQL Server bevinden, is vergelijkbaar met het verwerken van gegevens in Azure blob met Python zoals gedocumenteerd in [Process Azure Blob-gegevens in uw datascience-omgeving.](data-blob.md) Laad de gegevens uit de database in een pandas-gegevensframe voor meer verwerking. In deze sectie documenteren we het proces van verbinding maken met de database en het laden van de gegevens in het gegevensframe.

De volgende verbindingstekenreeksindeling kan worden gebruikt om verbinding te maken met een SQL Server-database van Python met pyodbc (servernaam, dbname, gebruikersnaam en wachtwoord vervangen door uw specifieke waarden):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

De [Panda's-bibliotheek](https://pandas.pydata.org/) in Python biedt een rijke set gegevensstructuren en gegevensanalysetools voor gegevensmanipulatie voor Python-programmering. De onderstaande code leest de resultaten die vanuit een SQL Server-database worden geretourneerd in een Pandas-gegevensframe:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Nu u werken met het Pandas-gegevensframe zoals dat wordt weergegeven in het artikel [Azure Blob-gegevens verwerken in uw data science-omgeving.](data-blob.md)

## <a name="azure-data-science-in-action-example"></a>Voorbeeld van Azure Data Science in actie
Zie [Azure Data Science Process in Action](sql-walkthrough.md)voor een end-to-end walkthrough-voorbeeld van het Azure Data Science-proces met behulp van een openbare gegevensset.

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

