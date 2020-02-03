---
title: Gegevens verkennen in SQL Server-VM - Team Data Science Process
description: Klik hier voor meer informatie over het verkennen van gegevens die zijn opgeslagen in een SQL Server-VM op Azure met behulp van SQL- of een programmeertaal zoals Python.
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
ms.openlocfilehash: ae8c7c43ecbf9bc625e1e46be3e2c71c8d57b6f7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720092"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Gegevens in virtuele SQL Server-machine verkennen in Azure

In dit artikel wordt uitgelegd hoe u gegevens die zijn opgeslagen in een SQL Server-VM op Azure te verkennen. Gebruik SQL of python voor het onderzoeken van de gegevens.

Deze taak is een stap in het [team data Science process](overview.md).

> [!NOTE]
> De voorbeeld-SQL-instructies in dit document wordt ervan uitgegaan dat gegevens in SQL Server. Als dit niet is, kunt u verwijzen naar de cloud data science process kaart voor informatie over het verplaatsen van uw gegevens naar SQL Server.
> 
> 

## <a name="sql-dataexploration"></a>SQL-gegevens verkennen met SQL-scripts
Hier volgen enkele voorbeelden SQL-scripts die kunnen worden gebruikt voor het verkennen van SQL Server voor gegevensopslag.

1. Het aantal opmerkingen per dag
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. De niveaus in een categorische kolom ophalen
   
    `select  distinct <column_name> from <databasename>`
3. Het aantal niveaus in de combinatie van twee categorische kolommen ophalen 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. De verdeling van de numerieke kolommen ophalen
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Voor een praktijk voorbeeld kunt u de [NYC taxi-gegevensset](https://www.andresmh.com/nyctaxitrips/) gebruiken en verwijzen naar de IPNB getiteld [NYC data wrangling met behulp van IPython notebook en SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) voor een end-to-end-instructies.
> 
> 

## <a name="python"></a>SQL-gegevens verkennen met python
Met python voor het verkennen van gegevens en het genereren van functies als de gegevens zich in SQL Server bevinden, is vergelijkbaar met het verwerken van gegevens in azure-blobs met behulp van python, zoals beschreven in [Azure Blob-gegevens in uw data Science-omgeving verwerken](data-blob.md). Laad de gegevens uit de data base in een Panda data frame en kan vervolgens verder worden verwerkt. We leggen het proces van verbinding met de database en het laden van de gegevens in het DataFrame in deze sectie.

De volgende indeling van de verbindingsreeks kan worden gebruikt voor het verbinding maken met een SQL Server-database vanuit Python met behulp van pyodbc (vervang servername, dbname, gebruikersnaam en wachtwoord met uw specifieke waarden):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

De [bibliotheek Pandas](https://pandas.pydata.org/) in python biedt een uitgebreide set gegevens structuren en hulpprogram ma's voor gegevens analyse voor het bewerken van gegevens voor python-programmering. De volgende code leest de resultaten van een SQL Server-database in een gegevensframe Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

U kunt nu met de Pandas data frame werken, zoals wordt behandeld in het onderwerp [Azure Blob-gegevens in uw data Science-omgeving verwerken](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Het Team Data Science Process in actie voorbeeld
Voor een end-to-end-scenario voor beeld van het Cortana Analytics-proces met behulp van een open bare gegevensset raadpleegt u [het team data Science process in actie: using SQL Server](sql-walkthrough.md).

