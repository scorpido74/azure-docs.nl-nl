---
title: Gegevens verkennen in een computer met SQL Server-machine - Team Data Science Process
description: Verken + proces gegevens en Genereer functies met behulp van python of SQL in een SQL Server virtuele machine in Azure.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718477"
---
# <a name="heading"></a>Gegevens verwerken in SQL Server virtuele machine op Azure
Dit document wordt uitgelegd hoe u gegevens verkennen en functies voor gegevens die zijn opgeslagen in een SQL Server-VM op Azure te genereren. Dit doel kan worden voltooid door gegevens wrangling met behulp van SQL of door gebruik te maken van een programmeer taal zoals python.

> [!NOTE]
> De voorbeeld-SQL-instructies in dit document wordt ervan uitgegaan dat gegevens in SQL Server. Als dit niet is, kunt u verwijzen naar de cloud data science process kaart voor informatie over het verplaatsen van uw gegevens naar SQL Server.
> 
> 

## <a name="SQL"></a>SQL gebruiken
We beschrijven de volgende data wrangling taken in deze sectie met behulp van SQL:

1. [Gegevens verkennen](#sql-dataexploration)
2. [Onderdelen genereren](#sql-featuregen)

### <a name="sql-dataexploration"></a>Gegevens verkennen
Hier volgen enkele voorbeelden SQL-scripts die kunnen worden gebruikt voor het verkennen van SQL Server voor gegevensopslag.

> [!NOTE]
> Voor een praktijk voorbeeld kunt u de [NYC taxi-gegevensset](https://www.andresmh.com/nyctaxitrips/) gebruiken en verwijzen naar de IPNB getiteld [NYC data wrangling met behulp van IPython notebook en SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) voor een end-to-end-instructies.
> 
> 

1. Het aantal opmerkingen per dag
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. De niveaus in een categorische kolom ophalen
   
    `select  distinct <column_name> from <databasename>`
3. Het aantal niveaus in de combinatie van twee categorische kolommen ophalen 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. De verdeling van de numerieke kolommen ophalen
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="sql-featuregen"></a>Onderdelen genereren
In deze sectie wordt beschreven manieren voor het genereren van functies met behulp van SQL:  

1. [Telling op basis van onderdelen genereren](#sql-countfeature)
2. [Binning-functie generatie](#sql-binningfeature)
3. [De functies uit één kolom implementeren](#sql-featurerollout)

> [!NOTE]
> Nadat u extra functies genereert, kunt u ze als kolommen toevoegen aan de bestaande tabel of een nieuwe tabel maken met de aanvullende functies en de primaire sleutel, die kan worden samengevoegd met de oorspronkelijke tabel. 
> 
> 

### <a name="sql-countfeature"></a>Telling op basis van onderdelen genereren
De volgende voorbeelden ziet twee manieren voor het genereren van het aantal functies. De eerste methode maakt gebruik van Voorwaardelijke som en de tweede methode maakt gebruik van de component 'where'. Deze resultaten kunnen vervolgens worden samengevoegd met de oorspronkelijke tabel (met behulp van primaire-sleutel kolommen), zodat het aantal functies naast de oorspronkelijke gegevens wordt weer gegeven.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>Binning-functie generatie
Het volgende voorbeeld laat zien hoe voor het genereren van binned functies door binning (met behulp van vijf opslaglocaties) een numerieke kolom die kan worden gebruikt als een functie in plaats daarvan:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>De functies uit één kolom implementeren
In deze sectie laten we zien hoe u één kolom in een tabel voor het genereren van extra functies worden uitgerold. Het voorbeeld wordt ervan uitgegaan dat er een kolom voor breedtegraad of lengtegraad in de tabel waaruit u wilt genereren van functies.

Hier volgt een korte beschrijving van de locatie gegevens voor de breedte graad/lengte graad (vanaf stack overflow [hoe de nauw keurigheid van de breedte graad en lengte graad wordt gemeten?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Deze richt lijnen zijn handig voor het opnemen van locaties als een of meer functies:

* De aanmelding kan worden achterhaald of we Noord of -Zuid, Oost- of -west op de hele wereld.
* Een andere waarde dan nul honderden cijfers kan worden achterhaald dat we niet breedtegraad lengtegraad gebruiken!
* De tientallen cijfer biedt een positie naar ongeveer 1000 kilometer zijn verwijderd. Deze geeft ons voor nuttige informatie over welke continent of in de Indische Oceaan worden op.
* De eenheden cijfers (één decimaal graad) biedt een positie tot 111 kilometer (60 zeemijl, ongeveer 69 mijl). U kunt de status, het land of de regio waarin u zich bevindt, melden.
* De eerste decimaal de moeite waard tot 11.1 km is: het de positie van een grote plaats van een nabijgelegen grote plaats kunt onderscheiden.
* De tweede decimaalpositie de moeite waard tot 1.1 km is: kan het één village scheiden van de volgende.
* De derde decimaal de moeite waard is maximaal 110 m: dat het een grote agricultural veld of institutionele campus kunt identificeren.
* De vierde decimaal de moeite waard is maximaal 11 m: dat er een pakket van de grond kunt identificeren. Het is vergelijkbaar met de typische nauwkeurigheid van een niet-gecorrigeerde GPS-eenheid zonder storing.
* De vijfde decimaal de moeite waard is tot 1.1 m: dat deze structuren van elkaar worden onderscheiden. Nauwkeurigheid van de gegevens op dit niveau met commerciële GPS-eenheden kan alleen worden bereikt met differentiële correctie.
* De zesde decimaal is waard tot 0.11 m: die u kunt deze gebruiken voor het opmaken van structuren in detail voor het ontwerpen van landschappen, het bouwen van wegen. Dit moet meer dan goed genoeg voor het bijhouden van bewegingen van glaciers en rivieren. Dit kan worden bereikt door middel van hele maatregelen met GPS, zoals differentially gecorrigeerde GPS.

De locatie-informatie kan worden boommodel als volgt scheiden van regio, locatie en plaats. U kunt ook een REST-eind punt aanroepen, zoals Bing Kaarten-API beschikbaar op [een locatie zoeken op basis van](https://msdn.microsoft.com/library/ff701710.aspx) de regio/district-informatie ophalen.

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

Deze functies op basis van locatie kunnen verder worden gebruikt voor het genereren van aantal extra functies, zoals eerder beschreven. 

> [!TIP]
> U kunt de records die met de taal van keuze programmatisch invoegen. Mogelijk moet u de gegevens in segmenten invoegen om de schrijf efficiëntie te verbeteren (Zie voor een voor beeld van hoe u dit doet met pyodbc het voor beeld van [een HelloWorld om toegang te krijgen tot sqlserver met python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Een andere mogelijkheid is om gegevens in de data base in te voegen met behulp van het [bcp-hulp programma](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="sql-aml"></a>Verbinding maken met Azure Machine Learning
De nieuwe functie kan worden toegevoegd als een kolom aan een bestaande tabel of die zijn opgeslagen in een nieuwe tabel en samengevoegd met de oorspronkelijke tabel voor machine learning. Functies kunnen worden gegenereerd of geopend als deze al zijn gemaakt met behulp van de module [gegevens importeren][import-data] in azure machine learning, zoals hieronder wordt weer gegeven:

![lezers van azureml][1] 

## <a name="python"></a>Een programmeer taal zoals python gebruiken
Met python voor het verkennen van gegevens en het genereren van functies als de gegevens zich in SQL Server bevinden, is vergelijkbaar met het verwerken van gegevens in Azure-Blob met behulp van python zoals beschreven in [Azure Blob-gegevens in uw data Science-omgeving verwerken](data-blob.md). Laad de gegevens uit de data base in een Pandas-gegevens frame voor meer verwerking. We leggen het proces van verbinding met de database en het laden van de gegevens in het kader van de gegevens in deze sectie.

De volgende indeling van de verbindingsreeks kan worden gebruikt voor het verbinding maken met een SQL Server-database vanuit Python met behulp van pyodbc (vervang servername, dbname, gebruikersnaam en wachtwoord met uw specifieke waarden):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

De [bibliotheek Pandas](https://pandas.pydata.org/) in python biedt een uitgebreide set gegevens structuren en hulpprogram ma's voor gegevens analyse voor het bewerken van gegevens voor python-programmering. De onderstaande code leest de resultaten van een SQL Server-database in een gegevensframe Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Nu kunt u met het frame Pandas-gegevens werken, zoals is beschreven in het artikel [Azure Blob-gegevens in uw data Science-omgeving verwerken](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Azure Data Science in actie voorbeeld
Zie voor een volledig overzicht van het Azure data Science-proces met behulp van een open bare gegevensset het [Azure data Science-proces in actie](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

