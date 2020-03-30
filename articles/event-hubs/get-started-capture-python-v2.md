---
title: Azure Event Hubs lezen die gegevens uit een Python-app hebben vastgelegd (laatste)
description: In dit artikel ziet u hoe u Python-code schrijft om gegevens vast te leggen die naar een gebeurtenishub worden verzonden en de vastgelegde gebeurtenisgegevens van een Azure-opslagaccount te lezen.
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
ms.openlocfilehash: 34583ef49b2f919391af3fe5700a558b2dc40700
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77187245"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub-version-5"></a>Gebeurtenishubsgegevens vastleggen in Azure Storage en deze lezen met Python (azure-eventhub versie 5)

U een gebeurtenishub zo configureren dat de gegevens die naar een gebeurtenishub worden verzonden, worden vastgelegd in een Azure-opslagaccount of Azure Data Lake Storage Gen 1 of Gen 2. In dit artikel ziet u hoe u Python-code schrijft om gebeurtenissen naar een gebeurtenishub te verzenden en de vastgelegde gegevens uit **Azure Blob-opslag**te lezen. Zie Overzicht van de [functie Gebeurtenishubs vastleggen](event-hubs-capture-overview.md)voor meer informatie over deze functie.

Deze quickstart maakt gebruik van de [Azure Python SDK](https://azure.microsoft.com/develop/python/) om de capture-functie aan te tonen. De *sender.py-app* stuurt gesimuleerde omgevingstelemetrie naar gebeurtenishubs in JSON-indeling. De gebeurtenishub is geconfigureerd om de functie Vastleggen te gebruiken om deze gegevens in batches naar Blob-opslag te schrijven. De *capturereader.py-app* leest deze blobs en maakt een toevoegend bestand voor elk apparaat. De app schrijft de gegevens vervolgens in CSV-bestanden.

> [!IMPORTANT]
> Deze quickstart maakt gebruik van versie 5 van de Azure Event Hubs Python SDK. Zie [dit artikel](event-hubs-capture-python.md)voor een snelle start die versie 1 van de Python SDK gebruikt. 

In deze snelstart, gaat u het volgende doen: 

> [!div class="checklist"]
> * Maak een Azure Blob-opslagaccount en -container in de Azure-portal.
> * Maak een naamruimte voor gebeurtenishubs met behulp van de Azure-portal.
> * Maak een gebeurtenishub met de opnamefunctie ingeschakeld en koppel deze aan uw opslagaccount.
> * Stuur gegevens naar de gebeurtenishub met behulp van een Python-script.
> * Bestanden van Event Hubs Capture lezen en verwerken met een ander Python-script.

## <a name="prerequisites"></a>Vereisten

- Python 2.7 en 3.5 of hoger, met PIP geïnstalleerd en bijgewerkt.  
- Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.  
- Een actieve naamruimte voor Gebeurtenishubs en gebeurtenishub.
[Maak een naamruimte voor gebeurtenishubs en een gebeurtenishub in de naamruimte](event-hubs-create.md). Neem de naam van de naamruimte van gebeurtenishubs, de naam van de gebeurtenishub en de primaire toegangssleutel voor de naamruimte op. Zie [Een verbindingstekenreeks voor gebeurtenishubs ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)voor toegang. De standaardsleutelnaam is *RootManageSharedAccessKey*. Voor deze snelle start hebt u alleen de primaire sleutel nodig. Je hebt de verbindingsstring niet nodig.  
- Een Azure-opslagaccount, een blobcontainer in het opslagaccount en een verbindingstekenreeks met het opslagaccount. Als u deze items niet hebt, gaat u als volgt te werk:  
    1. [Een Azure-opslagaccount maken](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)  
    1. [Een blobcontainer maken in het opslagaccount](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [De verbindingstekenreeks naar het opslagaccount weerleggen](../storage/common/storage-configure-connection-string.md#view-and-copy-a-connection-string)

    Zorg ervoor dat u de verbindingstekenreeks en de containernaam opneemt voor later gebruik in deze quickstart.  
- Schakel de opnamefunctie in voor de gebeurtenishub. Volg hiervoor de instructies in [Gebeurtenishubs vastleggen inschakelen met de Azure-portal.](event-hubs-capture-enable-through-portal.md) Selecteer het opslagaccount en de blobcontainer die u in de vorige stap hebt gemaakt. U de functie ook inschakelen wanneer u een gebeurtenishub maakt.  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Een Python-script maken om gebeurtenissen naar uw gebeurtenishub te verzenden
In deze sectie maakt u een Python-script dat 200 gebeurtenissen (10 apparaten * 20 gebeurtenissen) naar een gebeurtenishub verzendt. Deze gebeurtenissen zijn een voorbeeld van milieumetingen die worden verzonden in JSON-formaat. 

1. Open uw favoriete Python-editor, zoals [Visual Studio Code][Visual Studio Code].
2. Een script maken dat *sender.py*wordt genoemd. 
3. Plak de volgende code in *sender.py*. 
   
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
    * Vervang `EVENT HUBS NAMESPACE CONNECTION STRING` de verbindingstekenreeks voor de naamruimte van uw gebeurtenishubs.  
    * Vervang `EVENT HUB NAME` de naam van uw gebeurtenishub.  
5. Voer het script uit om gebeurtenissen naar de gebeurtenishub te verzenden.  
6. In de Azure-portal u controleren of de gebeurtenishub de berichten heeft ontvangen. Overschakelen naar **berichtenweergave** in de sectie **Statistieken.** Vernieuw de pagina om de grafiek bij te werken. Het kan enkele seconden duren voordat de pagina wordt weergegeven dat de berichten zijn ontvangen. 

    [![Controleren of de gebeurtenishub de berichten heeft ontvangen](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Een Python-script maken om uw Capture-bestanden te lezen
In dit voorbeeld worden de vastgelegde gegevens opgeslagen in Azure Blob-opslag. Het script in deze sectie leest de vastgelegde gegevensbestanden van uw Azure-opslagaccount en genereert CSV-bestanden die u eenvoudig openen en weergeven. U ziet 10 bestanden in de huidige werkmap van de toepassing. Deze bestanden bevatten de milieumetingen voor de 10 apparaten. 

1. Maak in uw Python-editor een script met de naam *capturereader.py*. Dit script leest de vastgelegde bestanden en maakt een bestand voor elk apparaat om de gegevens alleen voor dat apparaat te schrijven.
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
3. Vervang `AZURE STORAGE CONNECTION STRING` de verbindingstekenreeks voor uw Azure-opslagaccount. De naam van de container die u in deze quickstart hebt gemaakt, is *vastleggen.* Als u een andere naam voor de container hebt gebruikt, vervangt *u het vastleggen* door de naam van de container in het opslagaccount. 

## <a name="run-the-scripts"></a>De scripts uitvoeren
1. Open een opdrachtprompt met Python op zijn pad en voer deze opdrachten uit om Python-vereistepakketten te installeren:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Wijzig uw map in de map waarin u *sender.py* hebt opgeslagen en *capturereader.py*en voer deze opdracht uit:
   
   ```
   python sender.py
   ```
   
   Met deze opdracht wordt een nieuw Python-proces gestart om de afzender uit te voeren.
3. Wacht een paar minuten tot de opname is uitgevoerd en voer de volgende opdracht in het oorspronkelijke opdrachtvenster in:
   
   ```
   python capturereader.py
   ```

   Deze opnameprocessor gebruikt de lokale map om alle blobs van het opslagaccount en de container te downloaden. Het verwerkt alle die niet leeg zijn, en het schrijft de resultaten als CSV-bestanden in de lokale directory.

## <a name="next-steps"></a>Volgende stappen
Bekijk [Python-voorbeelden op GitHub.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples) 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
