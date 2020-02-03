---
title: Voorbeeldgegevens in SQL Server op Azure - Team Data Science Process
description: Voorbeeldgegevens die zijn opgeslagen in SQL Server op Azure met SQL- of de Python-programmeertaal en verplaatsen naar Azure Machine Learning.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717643"
---
# <a name="heading"></a>Voorbeeld gegevens in SQL Server op Azure

In dit artikel laat zien hoe sample van gegevens die zijn opgeslagen in SQL Server op Azure met behulp van SQL of de Python-programmeertaal. Ook ziet u hoe sample om gegevens te verplaatsen naar Azure Machine Learning door naar een bestand opslaat, uploaden naar een Azure-blob en vervolgens te lezen in Azure Machine Learning Studio.

In de python-steek proef wordt gebruikgemaakt van de [pyodbc](https://code.google.com/p/pyodbc/) ODBC-bibliotheek om verbinding te maken met SQL Server op Azure en de [Panda](https://pandas.pydata.org/) -bibliotheek om de steek proeven te doen.

> [!NOTE]
> De SQL-voorbeeldcode in dit document wordt ervan uitgegaan dat de gegevens in een SQL Server op Azure. Als dat niet het geval is, raadpleegt u [gegevens verplaatsen naar SQL Server in azure](move-sql-server-virtual-machine.md) article voor instructies over het verplaatsen van uw gegevens naar SQL Server in Azure.
> 
> 

**Waarom een voor beeld van uw gegevens?**
Als de gegevensset die u van plan bent om te analyseren groot is, is het doorgaans een goed idee om down-sampling van de gegevens om deze aan de grootte van een kleiner, maar representatieve en gemakkelijker. Bemonstering vereenvoudigt het leren van gegevens, het verkennen en functie-engineering. De rol van het [team data Science process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) is om snel een prototype van de functies voor gegevens verwerking en machine learning modellen mogelijk te maken.

Deze steekproef taak is een stap in het [team data Science process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="SQL"></a>SQL gebruiken
Deze sectie beschrijft de verschillende methoden voor het uitvoeren van eenvoudige steekproeven ten opzichte van de gegevens in de database met behulp van SQL. Kies een methode op basis van de gegevensgrootte van uw en de distributie hiervan.

De volgende twee items laten zien hoe u `newid` in SQL Server kunt gebruiken om de steek proef uit te voeren. De methode die u kiest, is afhankelijk van hoe wille keurig het voor beeld moet worden (pk_id in de volgende voorbeeld code wordt aangenomen dat het een automatisch gegenereerde primaire sleutel is).

1. Minder strikte steekproef
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Meer steekproef 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Component TABLESAMPLE kan worden gebruikt voor de gegevens ook steekproeven. Deze optie is mogelijk een betere benadering als de grootte van uw gegevens groot is (ervan uitgaande dat de gegevens op verschillende pagina's niet worden gecorreleerd) en de query in een redelijke periode wordt voltooid.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> U kunt verkennen en functies van deze sample gegevens genereren door in een nieuwe tabel op te slaan
> 
> 

### <a name="sql-aml"></a>Verbinding maken met Azure Machine Learning
U kunt de bovenstaande voorbeeld query's rechtstreeks in de module Azure Machine Learning [gegevens importeren][import-data] gebruiken om de gegevens op de vlucht te verlagen en deze naar een Azure machine learning experiment te brengen. Een scherm opname van het gebruik van de Lees module om de voorbeeld gegevens te lezen, worden hier weer gegeven:

![lezer-sql][1]

## <a name="python"></a>De python-programmeer taal gebruiken
In deze sectie wordt gedemonstreerd hoe u de [pyodbc-bibliotheek](https://code.google.com/p/pyodbc/) gebruikt om een ODBC-verbinding te maken met een SQL server-data base in python. De data base connection string is als volgt: (Vervang servername, dbname, username en password door uw configuratie):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

De bibliotheek [Pandas](https://pandas.pydata.org/) in python biedt een uitgebreide set gegevens structuren en hulpprogram ma's voor gegevens analyse voor het bewerken van gegevens voor python-programmering. Met de volgende code wordt een voor beeld van 0,1% gelezen van de gegevens uit een tabel in Azure SQL Database naar een Panda-gegevens:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)

Nu kunt u werken met de sample gegevens in het Pandas dataframe. 

### <a name="python-aml"></a>Verbinding maken met Azure Machine Learning
De volgende voorbeeldcode kunt u de gegevens naar beneden steekproef opslaan naar een bestand en upload het naar een Azure-blob. De gegevens in de BLOB kunnen rechtstreeks in een Azure Machine Learning experiment worden gelezen met behulp van de module [gegevens importeren][import-data] . De stappen zijn als volgt: 

1. Het pandas dataframe schrijven naar een lokaal bestand
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Lokaal bestand te uploaden naar Azure-blob
   
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
3. Gegevens lezen uit een Azure-Blob met behulp van Azure Machine Learning [gegevens module importeren][import-data] , zoals wordt weer gegeven in de volgende scherm afbeelding:

![lezer-blob][2]

## <a name="the-team-data-science-process-in-action-example"></a>Het Team Data Science Process in actie voorbeeld
Voor een voor beeld van het team data Science process a met behulp van een open bare gegevensset raadpleegt u [team data Science process in actie: using SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
