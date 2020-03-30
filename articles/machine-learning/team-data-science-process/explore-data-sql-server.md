---
title: Gegevens verkennen in sql server virtuele machine - Team Data Science Process
description: Gegevens verkennen die zijn opgeslagen in een SQL Server VM op Azure met SQL of een programmeertaal zoals Python.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720092"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Gegevens in virtuele SQL Server-machine verkennen in Azure

In dit artikel wordt ingaan op het verkennen van gegevens die zijn opgeslagen in een SQL Server VM op Azure. Gebruik SQL of Python om de gegevens te onderzoeken.

Deze taak is een stap in het [Team Data Science Process.](overview.md)

> [!NOTE]
> De voorbeeldSQL-instructies in dit document gaan ervan uit dat gegevens zich in SQL Server bevindt. Als dit niet het is, raadpleegt u de proceskaart voor cloudgegevenswetenschap om te leren hoe u uw gegevens naar SQL Server verplaatsen.
> 
> 

## <a name="explore-sql-data-with-sql-scripts"></a><a name="sql-dataexploration"></a>SQL-gegevens verkennen met SQL-scripts
Hier volgen een paar voorbeeldSQL-scripts die kunnen worden gebruikt om gegevensopslag in SQL Server te verkennen.

1. Haal het aantal waarnemingen per dag op
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. De niveaus in een categorische kolom opbrengen
   
    `select  distinct <column_name> from <databasename>`
3. Het aantal niveaus in combinatie met twee categorische kolommen 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. De verdeling voor numerieke kolommen opvragen
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Voor een praktisch voorbeeld u de [NYC Taxi-gegevensset](https://www.andresmh.com/nyctaxitrips/) gebruiken en verwijzen naar de IPNB getiteld [NYC Data wrangling using IPython Notebook and SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) for an end-to-end walk-through.
> 
> 

## <a name="explore-sql-data-with-python"></a><a name="python"></a>SQL-gegevens verkennen met Python
Python gebruiken om gegevens te verkennen en functies te genereren wanneer de gegevens zich in SQL Server bevinden, is vergelijkbaar met het verwerken van gegevens in Azure blob met Python, zoals gedocumenteerd in [Process Azure Blob-gegevens in uw datascience-omgeving.](data-blob.md) Laad de gegevens uit de database in een pandas DataFrame en kan vervolgens verder worden verwerkt. In deze sectie documenteren we het proces van verbinding maken met de database en het laden van de gegevens in het DataFrame.

De volgende verbindingstekenreeksindeling kan worden gebruikt om verbinding te maken met een SQL Server-database van Python met pyodbc (servernaam, dbname, gebruikersnaam en wachtwoord vervangen door uw specifieke waarden):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

De [Panda's-bibliotheek](https://pandas.pydata.org/) in Python biedt een rijke set gegevensstructuren en gegevensanalysetools voor gegevensmanipulatie voor Python-programmering. In de volgende code worden de resultaten van een SQL Server-database in een Pandas-gegevensframe gelezen:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Nu u werken met de Pandas DataFrame zoals behandeld in het onderwerp [Proces Azure Blob gegevens in uw data science omgeving](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Het Team Data Science Process in actievoorbeeld
Zie [Het Team Data Science Process in actie voor](sql-walkthrough.md)een end-to-end walkthrough-voorbeeld van het Cortana Analytics-proces met behulp van een openbare gegevensset.

