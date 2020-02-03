---
title: Functies maken in SQL Server met behulp van SQL en Python - Team Data Science Process
description: Functies voor gegevens die zijn opgeslagen in een SQL Server-VM op Azure met behulp van SQL en Python - onderdeel van het Team Data Science Process genereren.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721741"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Met SQL en Python functies maken voor gegevens in SQL Server
Dit document laat zien hoe voor het genereren van functies voor gegevens die zijn opgeslagen op een SQL Server-VM in Azure waarmee algoritmen efficiënter Leer van de gegevens. U kunt SQL of een programmeertaal zoals Python gebruiken om deze taak te volbrengen. Beide methoden die hier.

Deze taak is een stap in het [team data Science process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

> [!NOTE]
> Voor een praktijk voorbeeld kunt u de [NYC taxi-gegevensset](https://www.andresmh.com/nyctaxitrips/) raadplegen en verwijzen naar de IPNB getiteld [NYC data wrangling met behulp van IPython notebook en SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) voor een end-to-end-instructies.
> 
> 

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u hebt:

* Een Azure storage-account gemaakt. Als u instructies nodig hebt, raadpleegt u [een Azure Storage account maken](../../storage/common/storage-account-create.md)
* Uw gegevens opgeslagen in SQL Server. Als dat niet het geval is, raadpleegt u [gegevens verplaatsen naar een Azure SQL database voor Azure machine learning](move-sql-azure.md) voor instructies over het verplaatsen van de gegevens.

## <a name="sql-featuregen"></a>Onderdelen genereren met SQL
In deze sectie wordt beschreven manieren voor het genereren van functies met behulp van SQL:  

* [Telling op basis van onderdelen genereren](#sql-countfeature)
* [Binning-functie generatie](#sql-binningfeature)
* [De functies uit één kolom implementeren](#sql-featurerollout)

> [!NOTE]
> Nadat u extra functies genereert, kunt u ze als kolommen toevoegen aan de bestaande tabel of een nieuwe tabel maken met de aanvullende functies en de primaire sleutel, die kan worden samengevoegd met de oorspronkelijke tabel.
> 
> 

### <a name="sql-countfeature"></a>Telling op basis van onderdelen genereren
Dit document ziet u twee manieren voor het genereren van het aantal functies. De eerste methode maakt gebruik van Voorwaardelijke som en de tweede methode maakt gebruik van de component 'where'. Deze nieuwe functies kunnen vervolgens worden gekoppeld aan de oorspronkelijke tabel (met behulp van primaire-sleutel kolommen) om onderdelen naast de oorspronkelijke gegevens te tellen.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>Binning-functie generatie
Het volgende voorbeeld laat zien hoe voor het genereren van binned functies door binning (met behulp van vijf opslaglocaties) een numerieke kolom die kan worden gebruikt als een functie in plaats daarvan:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>De functies uit één kolom implementeren
In deze sectie laten we zien hoe u één kolom in een tabel voor het genereren van extra functies worden uitgerold. Het voorbeeld wordt ervan uitgegaan dat er een kolom voor breedtegraad of lengtegraad in de tabel waaruit u wilt genereren van functies.

Hier volgt een korte primer op de locatie gegevens voor de breedte graad/lengte graad (vanaf stack overflow `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Hier volgen enkele handige dingen om te begrijpen over Locatiegegevens voordat u functies maakt vanuit het veld:

* Het teken geeft aan of we Noord of -Zuid, Oost- of westen van de hele wereld.
* Een andere waarde dan nul honderden cijfer lengtegraad aangeeft, niet breedtegraad wordt gebruikt.
* De tientallen cijfer biedt een positie naar ongeveer 1000 kilometer zijn verwijderd. Het biedt nuttige informatie over welke continent of in de Indische Oceaan worden op.
* De eenheden cijfers (één decimaal graad) biedt een positie tot 111 kilometer (60 zeemijl, ongeveer 69 mijl). Het geeft, ongeveer, in welke staat of welk land of welke regio we zich bevinden.
* De eerste decimaal de moeite waard tot 11.1 km is: het de positie van een grote plaats van een nabijgelegen grote plaats kunt onderscheiden.
* De tweede decimaalpositie de moeite waard tot 1.1 km is: kan het één village scheiden van de volgende.
* De derde decimaal de moeite waard is maximaal 110 m: dat het een grote agricultural veld of institutionele campus kunt identificeren.
* De vierde decimaal de moeite waard is maximaal 11 m: dat er een pakket van de grond kunt identificeren. Het is vergelijkbaar met de typische nauwkeurigheid van een niet-gecorrigeerde GPS-eenheid zonder storing.
* De vijfde decimaal de moeite waard is tot 1.1 m: dat deze structuren van elkaar worden onderscheiden. Nauwkeurigheid van de gegevens op dit niveau met commerciële GPS-eenheden kan alleen worden bereikt met differentiële correctie.
* De zesde decimaal positie is Maxi maal 0,11 m: u kunt dit niveau gebruiken voor het indelen van structuren, voor het ontwerpen van landschappen, het bouwen van wegen. Dit moet meer dan goed genoeg voor het bijhouden van bewegingen van glaciers en rivieren. Dit doel kan worden bereikt door Painstaking-maat regelen te nemen met GPS, zoals een differentieeel gecorrigeerde GPS.

De locatie-informatie kan boommodel zijn door te scheiden van regio, locatie en plaats. Eenmaal kan ook een REST-eind punt aanroepen, zoals de Bing Maps-API (Zie `https://msdn.microsoft.com/library/ff701710.aspx` om de informatie over de regio/district op te halen).

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
> U kunt de records die met de taal van keuze programmatisch invoegen. U moet mogelijk de gegevens invoegen in segmenten schrijven efficiëntie te verbeteren. [Hier volgt een voor beeld van hoe u dit kunt doen met behulp van pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Een andere mogelijkheid is om gegevens in de data base in te voegen met het [hulp programma BCP](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="sql-aml"></a>Verbinding maken met Azure Machine Learning
De nieuwe functie kan worden toegevoegd als een kolom aan een bestaande tabel of die zijn opgeslagen in een nieuwe tabel en samengevoegd met de oorspronkelijke tabel voor machine learning. Functies kunnen worden gegenereerd of geopend als deze al zijn gemaakt met behulp van de module [gegevens importeren](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) in azure ml, zoals hieronder wordt weer gegeven:

![Azure ML-lezers](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>Een programmeer taal zoals python gebruiken
Met behulp van Python voor het genereren van functies als de gegevens in SQL Server is vergelijkbaar met het verwerken van gegevens in Azure blob met behulp van Python. Zie [Azure Blob-gegevens in uw data Science-omgeving verwerken voor een](data-blob.md)vergelijking. Laad de gegevens uit de database in een gegevensframe pandas verdere verwerking. Het proces van verbinding met de database en de gegevens in het kader van de gegevens te laden wordt in deze sectie beschreven.

De volgende indeling van de verbindingsreeks kan worden gebruikt voor het verbinding maken met een SQL Server-database vanuit Python met behulp van pyodbc (vervang servername, dbname, gebruikersnaam en wachtwoord met uw specifieke waarden):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

De [bibliotheek Pandas](https://pandas.pydata.org/) in python biedt een uitgebreide set gegevens structuren en hulpprogram ma's voor gegevens analyse voor het bewerken van gegevens voor python-programmering. De volgende code leest de resultaten van een SQL Server-database in een gegevensframe Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Nu kunt u samen werken met het gegevens frame van de Panda-Data, zoals beschreven in onderwerpen, [functies maken voor Azure Blob Storage-gegevens met behulp van Panda](create-features-blob.md).

