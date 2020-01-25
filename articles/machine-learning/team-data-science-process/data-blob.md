---
title: Azure blob-gegevens verwerken met geavanceerde analyses - Team Data Science Process
description: Gegevens verkennen en functies van de gegevens die zijn opgeslagen in Azure Blob-opslag met behulp van geavanceerde analyse te genereren.
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
ms.openlocfilehash: 4c47dfb8b221b6cb4b6237669ecd17c1637107a2
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721095"
---
# <a name="heading"></a>Azure blob-gegevens verwerken met geavanceerde analyses
In dit document bevat informatie over gegevens verkennen en genereren functies van de gegevens die zijn opgeslagen in Azure Blob-opslag. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>De gegevens in een gegevensframe Pandas laden
Als u een gegevensset wilt verkennen en bewerken, moet deze worden gedownload van de BLOB-bron naar een lokaal bestand dat vervolgens kan worden geladen in een object met een Panda-gegevens frame. Hier volgen de stappen voor deze procedure:

1. Down load de gegevens van Azure Blob met de volgende python-voorbeeld code met behulp van Blob service. De variabele in de volgende code vervangen door uw eigen specifieke waarden: 
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))
2. De gegevens in een Pandas gegevensframe uit het gedownloade bestand gelezen.
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

U bent nu klaar voor de gegevens verkennen en het genereren van functies voor deze gegevensset.

## <a name="blob-dataexploration"></a>Gegevens verkennen
Hier volgen enkele voorbeelden van manieren om met Pandas gegevens te verkennen:

1. Het aantal rijen en kolommen controleren 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. Inspecteer de eerste of laatste paar rijen in de gegevensset als hieronder:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Controleer het gegevenstype dat elke kolom is geïmporteerd als het gebruik van de volgende voorbeeldcode
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. De algemene statistieken voor de kolommen in de gegevensset als volgt controleren
   
        dataframe_blobdata.describe()
5. Het aantal vermeldingen voor elke waarde in de kolom als volgt bekijken
   
        dataframe_blobdata['<column_name>'].value_counts()
6. Aantal ontbrekende waarden ten opzichte van het werkelijke aantal vermeldingen in elke kolom met behulp van de volgende voorbeeldcode
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Als u ontbrekende waarden voor een bepaalde kolom in de gegevens hebt, kunt u ze als volgt verwijderen:
   
        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape
   
   Een andere manier om de ontbrekende waarden vervangen is met de modusfunctie:
   
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. Maken van een histogram diagram met behulp van het nummer van de variabele van de opslaglocaties voor het tekenen van de distributie van een variabele    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Kijken naar correlaties tussen variabelen met behulp van een teststappen of de ingebouwde correlatiefunctie
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="blob-featuregen"></a>Functie genereren
We kunt functies als volgt met behulp van Python genereren:

### <a name="blob-countfeature"></a>Indicator waarde gebaseerd op onderdelen genereren
Categorische functies kunnen als volgt worden gemaakt:

1. Inspecteer de distributie van de categorische kolom:
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Indicatorwaarden voor elk van de kolomwaarden genereren
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Lid worden van de indicatorkolom met de oorspronkelijke gegevensframe 
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Verwijder de oorspronkelijke variabele zelf:
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="blob-binningfeature"></a>Genereren van de functie binning
Voor het genereren van binned functies, gaan we als volgt:

1. Een reeks van kolommen naar de opslaglocatie van een numerieke kolom toevoegen
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Converteren naar een reeks van Booleaanse variabelen binning
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Ten slotte de dummy variabelen Join terug naar de oorspronkelijke gegevensframe
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)    

## <a name="sql-featuregen"></a>Schrijven van gegevens terug naar Azure-blob en gebruiken in Azure Machine Learning
Nadat u de gegevens hebt bekeken en de benodigde functies hebt gemaakt, kunt u de gegevens (voor beeld of featurized) uploaden naar een Azure-Blob en deze gebruiken in Azure Machine Learning met behulp van de volgende stappen: u kunt extra functies maken in de Azure Machine Learning Studio (klassiek) ook. 

1. Het gegevensframe schrijven naar een lokaal bestand
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Uploaden van de gegevens naar Azure-blob als volgt:
   
        from azure.storage.blob import BlobService
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
3. De gegevens kunnen nu vanuit de BLOB worden gelezen met behulp van de module Azure Machine Learning [gegevens importeren][import-data] , zoals in het onderstaande scherm wordt weer gegeven:

![lezer-blob][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

