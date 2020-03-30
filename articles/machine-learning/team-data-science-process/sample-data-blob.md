---
title: Voorbeeldgegevens in Azure blob-opslag - Team Data Science-proces
description: Bemonsteringsgegevens die zijn opgeslagen in Azure blob-opslag door deze programmatisch te downloaden en deze vervolgens te samplen met behulp van procedures die in Python zijn geschreven.
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
ms.openlocfilehash: 4832762a88073f4d819925659bf9078e18f60c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720274"
---
# <a name="sample-data-in-azure-blob-storage"></a><a name="heading"></a>Voorbeeldgegevens in Azure Blob-opslag

Dit artikel heeft betrekking op bemonsteringsgegevens die zijn opgeslagen in Azure blob-opslag door deze programmatisch te downloaden en deze vervolgens te samplen met behulp van procedures die in Python zijn geschreven.

**Waarom uw gegevens proeven?**
Als de gegevensset die u wilt analyseren groot is, is het meestal een goed idee om de gegevens te downsamplen om deze te beperken tot een kleinere, maar representatievere en beter beheerbare grootte. Sampling vergemakkelijkt het begrijpen, verkennen en functieengineering van gegevens. Haar rol in het Cortana Analytics-proces is het mogelijk maken van snelle prototypen van de gegevensverwerkingsfuncties en machine learning-modellen.

Deze bemonsteringstaak is een stap in het [Team Data Science Process (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

## <a name="download-and-down-sample-data"></a>Gegevens downloaden en downsamplen
1. Download de gegevens uit Azure blob-opslag met de Blob-service uit de volgende voorbeeldpython-code: 
   
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

2. Lees gegevens in een Pandas data-frame uit het bestand gedownload hierboven.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Down-sample de gegevens `numpy`met `random.choice` behulp van de 's als volgt:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Nu u werken met het bovenstaande gegevensframe met de ene Percent monster voor verdere exploratie en functie generatie.

## <a name="upload-data-and-read-it-into-azure-machine-learning"></a><a name="heading"></a>Gegevens uploaden en inlezen in Azure Machine Learning
U de volgende voorbeeldcode gebruiken om de gegevens te downsamplen en deze rechtstreeks te gebruiken in Azure Machine Learning:

1. Het gegevensframe naar een lokaal bestand schrijven
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Upload het lokale bestand naar een Azure-blob met de volgende voorbeeldcode:
   
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
            print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Lees de gegevens uit de Azure blob met Azure Machine Learning [Import Data](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) zoals weergegeven in de onderstaande afbeelding:

![lezerblob](./media/sample-data-blob/reader_blob.png)

