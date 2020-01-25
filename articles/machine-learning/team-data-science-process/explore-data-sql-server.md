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

Deze taak is een stap in de [Team Data Science Process](overview.md).

> [!NOTE]
> De voorbeeld-SQL-instructies in dit document wordt ervan uitgegaan dat gegevens in SQL Server. Als dit niet is, kunt u verwijzen naar de cloud data science process kaart voor informatie over het verplaatsen van uw gegevens naar SQL Server.
> 
> 

## <a name="sql-dataexploration"></a>Verken SQL-gegevens met SQL-scripts
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
> Voor een voorbeeld, kunt u de [NYC Taxi gegevensset](https://www.andresmh.com/nyctaxitrips/) en verwijzen naar de met de titel IPNB [NYC Data wrangling met behulp van IPython Notebook en SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) voor een overzicht van end-to-end.
> 
> 

## <a name="python"></a>Verken SQL-gegevens met Python
Met behulp van Python voor gegevens verkennen en het genereren van functies als de gegevens in SQL Server is vergelijkbaar met het verwerken van gegevens in Azure blob met behulp van Python, zoals beschreven in [proces Azure Blob-gegevens in uw data science-omgeving](data-blob.md). Laad de gegevens uit de data base in een Panda data frame en kan vervolgens verder worden verwerkt. We leggen het proces van verbinding met de database en het laden van de gegevens in het DataFrame in deze sectie.

De volgende indeling van de verbindingsreeks kan worden gebruikt voor het verbinding maken met een SQL Server-database vanuit Python met behulp van pyodbc (vervang servername, dbname, gebruikersnaam en wachtwoord met uw specifieke waarden):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

De [Pandas bibliotheek](https://pandas.pydata.org/) in Python biedt u een uitgebreide set gegevensstructuren en hulpprogramma's voor gegevensanalyse voor gegevensmanipulatie voor Python programmeren. De volgende code leest de resultaten van een SQL Server-database in een gegevensframe Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Nu u met de Pandas DataFrame werken kunt zoals beschreven in het onderwerp [proces Azure Blob-gegevens in uw data science-omgeving](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Het Team Data Science Process in actie voorbeeld
Zie voor een overzicht van end-to-end-voorbeeld van het Cortana Analytics-proces met behulp van een openbare gegevensset, [het Team Data Science Process in actie: met behulp van SQL Server](sql-walkthrough.md).

