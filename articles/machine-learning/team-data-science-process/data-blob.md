---
title: Azure blob-gegevens verwerken met geavanceerde analyses - Team Data Science-proces
description: Verken gegevens en genereer functies uit gegevens die zijn opgeslagen in Azure Blob-opslag met behulp van geavanceerde analyses.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721095"
---
# <a name="process-azure-blob-data-with-advanced-analytics"></a><a name="heading"></a>Azure Blob-gegevens verwerken met geavanceerde analyses
Dit document omvat het verkennen van gegevens en het genereren van functies uit gegevens die zijn opgeslagen in Azure Blob-opslag. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>De gegevens in een Pandas-gegevensframe laden
Om een gegevensset te verkennen en te manipuleren, moet deze worden gedownload van de blobbron naar een lokaal bestand dat vervolgens in een Pandas-gegevensframe kan worden geladen. Hier volgen de volgende stappen voor deze procedure:

1. Download de gegevens van Azure blob met de volgende voorbeeld python-code met blobservice. Vervang de variabele in de onderstaande code door uw specifieke waarden: 
   
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
2. Lees de gegevens in een Pandas data-frame uit het gedownloade bestand.
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Nu bent u klaar om de gegevens te verkennen en functies op deze gegevensset te genereren.

## <a name="data-exploration"></a><a name="blob-dataexploration"></a>Gegevensverkenning
Hier zijn een paar voorbeelden van manieren om gegevens te verkennen met behulp van Panda's:

1. Het aantal rijen en kolommen controleren 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. Controleer de eerste of laatste paar rijen in de onderstaande gegevensset:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Controleer het gegevenstype dat elke kolom is geïmporteerd met behulp van de volgende voorbeeldcode
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Controleer de basisstatistieken voor de kolommen in de gegevensset als volgt
   
        dataframe_blobdata.describe()
5. Bekijk het aantal vermeldingen voor elke kolomwaarde als volgt
   
        dataframe_blobdata['<column_name>'].value_counts()
6. Ontbrekende waarden tellen ten opzichte van het werkelijke aantal vermeldingen in elke kolom met behulp van de volgende voorbeeldcode
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Als u ontbrekende waarden hebt voor een specifieke kolom in de gegevens, u deze als volgt neerzetten:
   
        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape
   
   Een andere manier om ontbrekende waarden te vervangen is met de modusfunctie:
   
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. Een histogramplot maken met behulp van een variabel aantal opslaglocaties om de verdeling van een variabele in kaart te brengen    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Bekijk correlaties tussen variabelen met behulp van een scatterplot of met behulp van de ingebouwde correlatiefunctie
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="feature-generation"></a><a name="blob-featuregen"></a>Functiegeneratie
We kunnen functies genereren met Python als volgt:

### <a name="indicator-value-based-feature-generation"></a><a name="blob-countfeature"></a>Op indicatorwaarde gebaseerde functiegeneratie
Categorische functies kunnen als volgt worden gemaakt:

1. Controleer de verdeling van de categorische kolom:
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Indicatorwaarden genereren voor elk van de kolomwaarden
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Deelnemen aan de indicatorkolom met het oorspronkelijke gegevensframe 
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Verwijder de oorspronkelijke variabele zelf:
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="binning-feature-generation"></a><a name="blob-binningfeature"></a>Binning Feature Generation
Voor het genereren van binned-functies gaan we als volgt te werk:

1. Een reeks kolommen toevoegen om een numerieke kolom op te laten vallen
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Binning converteren naar een reeks booleaanse variabelen
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Ten slotte u de dummyvariabelen weer naar het oorspronkelijke gegevensframe voegen
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)    

## <a name="writing-data-back-to-azure-blob-and-consuming-in-azure-machine-learning"></a><a name="sql-featuregen"></a>Gegevens terugschrijven naar Azure blob en verbruiken in Azure Machine Learning
Nadat u de gegevens hebt verkend en de benodigde functies hebt gemaakt, u de gegevens (gesampled of gesampled) uploaden naar een Azure-blob en deze gebruiken in Azure Machine Learning met behulp van de volgende stappen: aanvullende functies kunnen worden gemaakt in de Azure Machine Learning Studio (klassiek) ook. 

1. Het gegevensframe naar lokaal bestand schrijven
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Upload de gegevens als volgt naar Azure blob:
   
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
3. Nu kunnen de gegevens worden gelezen van de blob met behulp van de Azure Machine Learning [Import Data-module][import-data] zoals weergegeven in het onderstaande scherm:

![lezerblob][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

