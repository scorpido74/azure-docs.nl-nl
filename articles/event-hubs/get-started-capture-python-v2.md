---
title: In azure Event Hubs opgenomen gegevens lezen van een python-app (meest recent)
description: In dit artikel leest u hoe u Python-code kunt schrijven om gegevens vast te leggen die naar een Event Hub worden verzonden en de vastgelegde gebeurtenis gegevens van een Azure-opslag account te lezen.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: 191a2246afdc60953d8c353f9ccdc2339130f910
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "82159332"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub-version-5"></a>Event Hubs gegevens vastleggen in Azure Storage en deze lezen met behulp van python (Azure-eventhub versie 5)

U kunt een Event Hub zodanig configureren dat de gegevens die naar een Event Hub worden verzonden, worden vastgelegd in een Azure-opslag account of Azure Data Lake Storage gen 1 of gen 2. In dit artikel wordt beschreven hoe u een python-code schrijft voor het verzenden van gebeurtenissen naar een Event Hub en het lezen van de vastgelegde gegevens uit **Azure Blob Storage**. Zie [Event hubs Capture-functie overzicht](event-hubs-capture-overview.md)voor meer informatie over deze functie.

In deze Quick Start wordt de [Azure PYTHON SDK](https://azure.microsoft.com/develop/python/) gebruikt om de Capture-functie te demonstreren. De *Sender.py* -app stuurt gesimuleerde omgevings-telemetrie naar Event hubs in JSON-indeling. De Event Hub is geconfigureerd voor het gebruik van de Capture-functie voor het schrijven van deze gegevens naar Blob-opslag in batches. De *capturereader.py* -app leest deze blobs en maakt een toevoeg bestand voor elk apparaat. De app schrijft de gegevens vervolgens naar CSV-bestanden.

> [!IMPORTANT]
> In deze Quick Start wordt versie 5 van de Azure Event Hubs python-SDK gebruikt. Zie [dit artikel](event-hubs-capture-python.md)voor een Snelstartgids die gebruikmaakt van versie 1 van de PYTHON-SDK. 

In deze snelstart, gaat u het volgende doen: 

> [!div class="checklist"]
> * Maak een Azure Blob-opslag account en een container in de Azure Portal.
> * Een Event Hubs naam ruimte maken met behulp van de Azure Portal.
> * Maak een Event Hub met de functie vastleggen ingeschakeld en koppel deze aan uw opslag account.
> * Gegevens naar uw Event Hub verzenden met behulp van een python-script.
> * Bestanden lezen en verwerken van Event Hubs vastleggen met behulp van een ander python-script.

## <a name="prerequisites"></a>Vereisten

- Python 2,7 en 3,5 of hoger, waarbij PIP is geïnstalleerd en bijgewerkt.  
- Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.  
- Een actieve Event Hubs naam ruimte en Event Hub.
[Maak een event hubs naam ruimte en een event hub in de naam ruimte](event-hubs-create.md). Noteer de naam van de Event Hubs naam ruimte, de naam van de Event Hub en de primaire toegangs sleutel voor de naam ruimte. Als u de toegangs sleutel wilt ophalen, raadpleegt u [een event hubs-Connection String ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). De naam van de standaard sleutel is *RootManageSharedAccessKey*. Voor deze Quick Start hebt u alleen de primaire sleutel nodig. U hebt de connection string niet nodig.  
- Een Azure-opslag account, een BLOB-container in het opslag account en een connection string naar het opslag account. Als u deze items niet hebt, gaat u als volgt te werk:  
    1. [Een Azure-opslag account maken](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)  
    1. [Een BLOB-container maken in het opslag account](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [De connection string ophalen voor het opslag account](../storage/common/storage-configure-connection-string.md)

    Zorg ervoor dat u de connection string en de container naam vastlegt voor later gebruik in deze Quick Start.  
- Schakel de opname functie in voor de Event Hub. Volg hiervoor de instructies in [enable Event hubs Capture met behulp van de Azure Portal](event-hubs-capture-enable-through-portal.md). Selecteer het opslag account en de BLOB-container die u in de vorige stap hebt gemaakt. U kunt de functie ook inschakelen wanneer u een Event Hub maakt.  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Een python-script maken voor het verzenden van gebeurtenissen naar uw Event Hub
In deze sectie maakt u een python-script waarmee 200-gebeurtenissen (10 apparaten * 20 gebeurtenissen) naar een Event Hub worden verzonden. Deze gebeurtenissen zijn een voor beeld van het lezen van een omgeving dat wordt verzonden in JSON-indeling. 

1. Open uw favoriete python-editor, zoals [Visual Studio code][Visual Studio Code].
2. Maak een script met de naam *Sender.py*. 
3. Plak de volgende code in *Sender.py*. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # This script simulates the production of events for 10 devices.
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # Create a producer client to produce and publish events to the event hub.
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # For each device, produce 20 events. 
        event_data_batch = producer.create_batch() # Create a batch. You will add events to the batch later. 
        for dev in devices:
            # Create a dummy reading.
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # Convert the reading into a JSON string.
            event_data_batch.add(EventData(s)) # Add event data to the batch.
        producer.send_batch(event_data_batch) # Send the batch of events to the event hub.
    
    # Close the producer.    
    producer.close()
    ```
4. Vervang de volgende waarden in de scripts:  
    * Vervang `EVENT HUBS NAMESPACE CONNECTION STRING` door de Connection String voor uw event hubs naam ruimte.  
    * Vervang `EVENT HUB NAME` door de naam van uw event hub.  
5. Voer het script uit om gebeurtenissen naar het Event Hub te verzenden.  
6. In de Azure Portal kunt u controleren of de Event Hub de berichten heeft ontvangen. Schakel over naar de weer gave **berichten** in het gedeelte **metrische gegevens** . Vernieuw de pagina om de grafiek bij te werken. Het kan een paar seconden duren voordat de pagina wordt weer gegeven dat de berichten zijn ontvangen. 

    [![Controleer of het Event Hub de berichten heeft ontvangen](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Een python-script maken om de opname bestanden te lezen
In dit voor beeld worden de vastgelegde gegevens opgeslagen in Azure Blob-opslag. Met het script in deze sectie worden de vastgelegde gegevens bestanden uit uw Azure Storage-account gelezen en worden CSV-bestanden gegenereerd zodat u ze eenvoudig kunt openen en weer geven. Er worden 10 bestanden weer geven in de huidige werkmap van de toepassing. Deze bestanden bevatten de milieu-aflezingen voor de 10 apparaten. 

1. Maak in uw python-editor een script met de naam *capturereader.py*. Met dit script worden de vastgelegde bestanden gelezen en wordt voor elk apparaat een bestand gemaakt om alleen de gegevens voor dat apparaat te schrijven.
2. Plak de volgende code in *capturereader.py*. 
   
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
        # Create a blob container client.
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # List all the blobs in the container.
        for blob in blob_list:
            # Content_length == 508 is an empty file, so process only content_length > 508 (skip empty files).        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # Create a blob client for the blob.
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # Construct a file name based on the blob name.
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # Open the file to write. Create it if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # Write blob contents into the file.
                processBlob2(cleanName) # Convert the file into a CSV file.
                os.remove(cleanName) # Remove the original downloaded file.
                # Delete the blob from the container after it's read.
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
3. Vervang `AZURE STORAGE CONNECTION STRING` door de Connection String voor uw Azure Storage-account. De naam van de container die u in deze Quick Start hebt gemaakt, wordt *vastgelegd*. Als u een andere naam voor de container hebt gebruikt, vervangt u *vastleggen* door de naam van de container in het opslag account. 

## <a name="run-the-scripts"></a>De scripts uitvoeren
1. Open een opdracht prompt met python in het pad en voer deze opdrachten uit om python-vereiste pakketten te installeren:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Wijzig de map in de map waar u *Sender.py* en *capturereader.py*hebt opgeslagen en voer deze opdracht uit:
   
   ```
   python sender.py
   ```
   
   Met deze opdracht wordt een nieuw python-proces gestart om de afzender uit te voeren.
3. Wacht enkele minuten totdat de opname is uitgevoerd en voer de volgende opdracht in het oorspronkelijke opdracht venster in:
   
   ```
   python capturereader.py
   ```

   Deze Capture-processor gebruikt de lokale map om alle blobs uit het opslag account en de container te downloaden. Deze verwerkt alle die niet leeg zijn en schrijft de resultaten als CSV-bestanden naar de lokale map.

## <a name="next-steps"></a>Volgende stappen
Bekijk [python-voor beelden op github](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
