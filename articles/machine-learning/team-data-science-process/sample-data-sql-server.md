---
title: Voorbeeldgegevens in SQL Server op Azure - Team Data Science-proces
description: Voorbeeldgegevens die zijn opgeslagen in SQL Server op Azure met SQL of de Python-programmeertaal en deze vervolgens verplaatsen naar Azure Machine Learning.
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
ms.openlocfilehash: 71a2ec9dc4d644fb8739db3817e2cd1d09913da7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717643"
---
# <a name="sample-data-in-sql-server-on-azure"></a><a name="heading"></a>Voorbeeldgegevens in SQL Server op Azure

In dit artikel ziet u hoe u gegevens bemonsteren die zijn opgeslagen in SQL Server op Azure met SQL of de Programmeertaal Python. Het laat ook zien hoe u gesamplede gegevens verplaatsen naar Azure Machine Learning door deze op te slaan in een bestand, deze te uploaden naar een Azure-blob en deze vervolgens in Azure Machine Learning Studio te lezen.

De Python-sampling maakt gebruik van de [ODBC-bibliotheek](https://code.google.com/p/pyodbc/) om verbinding te maken met SQL Server op Azure en de [Pandas-bibliotheek](https://pandas.pydata.org/) om de bemonstering uit te brengen.

> [!NOTE]
> De voorbeeld-SQL-code in dit document gaat ervan uit dat de gegevens zich in een SQL Server op Azure bevinden. Als dit niet het is, raadpleegt u [Gegevens verplaatsen naar SQL Server in Azure-artikel](move-sql-server-virtual-machine.md) voor instructies over het verplaatsen van uw gegevens naar SQL Server op Azure.
> 
> 

**Waarom uw gegevens proeven?**
Als de gegevensset die u wilt analyseren groot is, is het meestal een goed idee om de gegevens te downsamplen om deze te beperken tot een kleinere, maar representatievere en beter beheerbare grootte. Sampling vergemakkelijkt het begrijpen, verkennen en functieengineering van gegevens. Haar rol in het [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) is het mogelijk maken van snelle prototyping van de data processing functies en machine learning modellen.

Deze bemonsteringstaak is een stap in het [Team Data Science Process (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

## <a name="using-sql"></a><a name="SQL"></a>SQL gebruiken
In deze sectie worden verschillende methoden beschreven die SQL gebruiken om eenvoudige steekproeven uit te voeren tegen de gegevens in de database. Kies een methode op basis van uw gegevensgrootte en de verdeling ervan.

In de volgende twee `newid` items ziet u hoe u sql server gebruikt om de sampling uit te voeren. De methode die u kiest, is afhankelijk van hoe willekeurig het voorbeeld moet zijn (pk_id in de volgende voorbeeldcode wordt verondersteld een automatisch gegenereerde primaire sleutel te zijn).

1. Minder strikte willekeurige steekproef
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Meer willekeurige steekproef 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Tabelmonster kan ook worden gebruikt voor het bemonsteren van de gegevens. Deze optie kan een betere benadering zijn als uw gegevensgrootte groot is (ervan uitgaande dat gegevens op verschillende pagina's niet gecorreleerd zijn) en als de query binnen een redelijke tijd is voltooid.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> U functies uit deze gesamplede gegevens verkennen en genereren door deze op te slaan in een nieuwe tabel
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Verbinding maken met Azure Machine Learning
U de bovenstaande voorbeeldquery's direct gebruiken in de azure machine [learning-importgegevensmodule][import-data] om de gegevens on the fly te downsamplen en deze in een Azure Machine Learning-experiment te brengen. Een screenshot van het gebruik van de lezer module om de gesamplede gegevens te lezen wordt hier weergegeven:

![reader sql][1]

## <a name="using-the-python-programming-language"></a><a name="python"></a>De programmeertaal Python gebruiken
In deze sectie wordt aangetoond dat u de [pyodbc-bibliotheek](https://code.google.com/p/pyodbc/) gebruikt om een ODBC-verbinding met een SQL-serverdatabase in Python tot stand te brengen. De tekenreeks voor databaseverbinding is als volgt: (servernaam, dbname, gebruikersnaam en wachtwoord vervangen door uw configuratie):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

De [Panda's-bibliotheek](https://pandas.pydata.org/) in Python biedt een rijke set gegevensstructuren en gegevensanalysetools voor gegevensmanipulatie voor Python-programmering. In de volgende code wordt een voorbeeld van 0,1% van de gegevens uit een tabel in Azure SQL Database in een Pandas-gegevens gelezen:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)

U nu werken met de gesamplede gegevens in het Pandas-gegevensframe. 

### <a name="connecting-to-azure-machine-learning"></a><a name="python-aml"></a>Verbinding maken met Azure Machine Learning
U de volgende voorbeeldcode gebruiken om de gegevens met downsampled op te slaan in een bestand en deze te uploaden naar een Azure-blob. De gegevens in de blob kunnen rechtstreeks worden uitgelezen in een Azure Machine Learning Experiment met behulp van de module [Gegevens importeren.][import-data] De stappen zijn als volgt: 

1. Schrijf het panda's-gegevensframe naar een lokaal bestand
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Lokaal bestand uploaden naar Azure-blob
   
        from azure.storage import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading blob:"+BLOBNAME)
3. Lees gegevens uit Azure blob met azure machine learning [importgegevensmodule][import-data] zoals weergegeven in de volgende schermgreep:

![lezerblob][2]

## <a name="the-team-data-science-process-in-action-example"></a>Het team data science proces in actie voorbeeld
Zie [Team Data Science Process in Action: SQL Server gebruiken als](sql-walkthrough.md)u een voorbeeld wilt doorlopen van het Team Data Science Process a met behulp van een openbare gegevensset.

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
