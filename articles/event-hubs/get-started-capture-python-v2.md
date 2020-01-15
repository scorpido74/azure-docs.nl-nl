---
title: In azure Event Hubs opgenomen gegevens lezen uit de python-app | Microsoft Docs
description: In dit artikel leest u hoe u Python-code kunt schrijven om gegevens vast te leggen die naar een Event Hub worden verzonden en de vastgelegde gebeurtenis gegevens van een Azure Storage te lezen.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2019
ms.author: spelluru
ms.openlocfilehash: d7a0c757f7314e45d5b4d13273df984739912b27
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942410"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-using-python"></a>Event Hubs gegevens vastleggen in Azure Storage en deze lezen met behulp van python 
U kunt een Event Hub configureren gebruiken zodat de gegevens die naar een Event Hub worden verzonden, worden vastgelegd in een Azure Storage of Azure Data Lake Storage. Dit artikel laat u zien hoe u Python-code schrijven kunt gebruiken om gebeurtenissen te verzenden naar een Event Hub en de opgenomen gegevens te lezen uit een Azure Blob-opslag. Zie [Event hubs Capture-functie overzicht](event-hubs-capture-overview.md)voor meer informatie over deze functie.

In dit voorbeeld wordt de [Azure Python SDK](https://azure.microsoft.com/develop/python/) ter illustratie van de Capture-functie. Het programma sender.py stuurt gesimuleerde uitwerking telemetrie naar Event Hubs in JSON-indeling. De event hub is geconfigureerd voor het gebruik van de Capture-functie voor het schrijven van deze gegevens naar Blob-opslag in batches. De app capturereader.py deze blobs leest en maakt een bestand toevoegen per apparaat. De app wordt vervolgens de gegevens schrijft naar CSV-bestanden.

> [!IMPORTANT]
> In deze Quick Start wordt versie 5 van de Azure Event Hubs python-SDK gebruikt. Zie [dit artikel](event-hubs-capture-python.md)voor een snelle start die gebruikmaakt van de oude versie 1 van de PYTHON-SDK. Als u versie 1 van de SDK gebruikt, raden we u aan uw code te migreren naar de nieuwste versie. Zie de [migratie handleiding](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)voor meer informatie.

In deze snelstart, gaat u het volgende doen: 

> [!div class="checklist"]
> * Maak een Azure Blob-opslag account en een container in de Azure Portal.
> * Een Event Hubs naam ruimte maken met behulp van de Azure Portal.
> * Maak een Event Hub met de functie vastleggen ingeschakeld en koppel deze aan uw opslag account.
> * Gegevens naar uw Event Hub verzenden met behulp van een python-script.
> * Bestanden lezen en verwerken van Event Hubs vastleggen met behulp van een ander python-script.

## <a name="prerequisites"></a>Vereisten

- Python 2,7 en 3,5 of hoger, met `pip` geïnstalleerd en bijgewerkt.
- Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
- [Maak een event hubs naam ruimte en een event hub in de naam ruimte](event-hubs-create.md). Noteer de naam van de Event Hubs naam ruimte, de naam van de Event Hub en de primaire toegangs sleutel voor de naam ruimte. Haal de toegangs sleutel op door de volgende instructies te volgen in het artikel: [Get Connection String](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). De naam van de standaardwaarde is: **RootManageSharedAccessKey**. U hebt de connection string voor de zelf studie niet nodig. U hebt alleen de primaire sleutel nodig. 
- Volg deze stappen om een **Azure Storage-account** en een **BLOB-container**te maken:
    1. [Maak een Azure Storage-account](/storage/common/storage-quickstart-create-account.md?tabs=azure-portal).
    2. [Maak een BLOB-container in de opslag](/storage/blobs/storage-quickstart-blobs-portal.md#create-a-container). 
    3. [De connection string ophalen voor het opslag account](/storage/common/storage-configure-connection-string?#view-and-copy-a-connection-string)

        Noteer **Connection String** en de naam van de **container**. U zult deze later in de code gebruiken. 
- Schakel de functie **vastleggen** in voor de Event hub door de volgende instructies uit te voeren: [Schakel Event hubs vastleggen in met behulp van de Azure Portal](event-hubs-capture-enable-through-portal.md). Selecteer het opslag account en de BLOB-container die u in de vorige stap hebt gemaakt. U kunt de functie ook inschakelen bij het maken van een Event Hub. 

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Een Python-script voor het verzenden van gebeurtenissen naar uw event hub maken
In deze sectie maakt u een python-script waarmee 200-gebeurtenissen (10 apparaten * 20 gebeurtenissen) naar een Event Hub worden verzonden. Deze gebeurtenissen zijn een voor beeld van het lezen van een omgeving dat wordt verzonden in JSON-indeling. 

1. Open uw favoriete python-editor, zoals [Visual Studio code][Visual Studio Code].
2. Maken van een script met de naam **sender.py**. 
3. Plak de volgende code in sender.py. Zie de opmerkingen bij de code voor meer informatie. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # this scripts simulates production of events for 10 devices
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # create a producer client to produce/publish events to the event hub
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # for each device, produce 20 events 
        event_data_batch = producer.create_batch() # create a batch. you will add events to the batch later. 
        for dev in devices:
            # create a dummy reading
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # convert reading into a JSON string
            event_data_batch.add(EventData(s)) # add event data to the batch
        producer.send_batch(event_data_batch) # send the batch of events to the event hub
    
    # close the producer    
    producer.close()
    ```
4. Vervang de volgende waarden in de scripts:
    1. Vervang `EVENT HUBS NAMESPACE CONNECTION STRING` door de connection string voor uw Event Hubs naam ruimte.
    2. Vervang `EVENT HUB NAME` door de naam van uw Event Hub. 
5. Voer het script uit om gebeurtenissen naar het Event Hub te verzenden. 
6. In de Azure Portal kunt u controleren of de Event Hub de berichten heeft ontvangen. Schakel over naar de weer gave **berichten** in het gedeelte **metrische gegevens** . Vernieuw de pagina om de grafiek bij te werken. Het kan een paar seconden duren voordat wordt weer gegeven dat de berichten zijn ontvangen. 

    [![controleren of het Event Hub de berichten heeft ontvangen](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Een Python-script om te lezen van de Capture-bestanden maken
In dit voor beeld worden de vastgelegde gegevens opgeslagen in Azure Blob Storage. Met het script in deze sectie worden de gegevens bestanden van uw Azure Storage gelezen en worden CSV-bestanden gegenereerd om de inhoud eenvoudig te openen en weer te geven. Er worden 10 bestanden weer geven in de huidige werkmap van de toepassing. Deze bestanden bevatten de milieu-aflezingen voor de 10 apparaten. 

1. Maak in uw python-editor een script met de naam **capturereader.py**. Met dit script leest de vastgelegde bestanden en maakt u een bestand per apparaat om de gegevens alleen voor dat apparaat te schrijven.
2. Plak de volgende code in capturereader.py. Zie de opmerkingen bij de code voor meer informatie. 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # create a blob container client
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # list all the blobs in the container
        for blob in blob_list:
            #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # create a blob client for the blob
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # construct a file name based on the blob name
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # open the file to write. create if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # write blob contents into the file
                processBlob2(cleanName) # convert the file into a CSV file
                os.remove(cleanName) # remove the original downloaded file
                # delete the blob from the container after it's read
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
4. Vervang `<AZURE STORAGE CONNECTION STRING>` door de connection string voor uw Azure Storage-account. De naam van de container die u in deze zelf studie hebt gemaakt is: **Capture**. Als u een andere naam voor de container hebt gebruikt, vervangt u `capture` door de naam van de container in het opslag account. 

## <a name="run-the-scripts"></a>De scripts uitvoeren
1. Open een opdrachtprompt met Python in het pad en voer vervolgens deze opdrachten om vereiste Python-pakketten te installeren:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Wijzig de directory waar u sender.py en capturereader.py opgeslagen en voer deze opdracht uit:
   
   ```
   python sender.py
   ```
   
   Met deze opdracht start een nieuwe Python-proces voor het uitvoeren van de afzender.
3. Wacht een paar minuten voor het vastleggen om uit te voeren. Typ vervolgens de volgende opdracht in uw oorspronkelijke opdrachtvenster:
   
   ```
   python capturereader.py
   ```

   Deze processor vastleggen maakt gebruik van de lokale map alle blobs downloaden van het opslagaccount /-container. Verwerkt alle die niet leeg zijn en deze schrijft de resultaten als CSV-bestanden naar de lokale map.

## <a name="next-steps"></a>Volgende stappen
Bekijk [hier](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)python-voor beelden op het github. 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
