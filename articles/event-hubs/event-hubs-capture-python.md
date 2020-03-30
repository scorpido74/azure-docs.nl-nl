---
title: 'Snelstart: lees vastgelegde gegevens uit python-app - Azure Event Hubs'
description: 'Snelstart: scripts die de Azure Python SDK gebruiken om de functie Gebeurtenishubs vastleggen aan te tonen.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 01/15/2020
ms.author: shvija
ms.openlocfilehash: 6c830cf871c2ae650bb61e8b3712a664e9e405d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77187294"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python-azure-eventhub-version-1"></a>Snelstart: Gebeurtenishubs Maak walkthrough: Python (azure-eventhub versie 1)

Vastleggen is een functie van Azure Event Hubs. U Vastleggen gebruiken om de streaminggegevens in uw gebeurtenishub automatisch te leveren aan een Azure Blob-opslagaccount naar keuze. Deze mogelijkheid maakt het gemakkelijk om batchverwerking te doen op realtime streaminggegevens. In dit artikel wordt beschreven hoe u Event Hubs Capture with Python gebruiken. Zie Gebeurtenissen vastleggen via Azure Event Hubs voor meer informatie over het vastleggen van [gebeurtenishubs.][Overview of Event Hubs Capture]

Deze walkthrough maakt gebruik van de [Azure Python SDK](https://azure.microsoft.com/develop/python/) om de Capture-functie aan te tonen. Het *sender.py* programma stuurt gesimuleerde omgevingstelemetrie naar Event Hubs in JSON-indeling. De gebeurtenishub gebruikt de functie Vastleggen om deze gegevens in batches naar Blob-opslag te schrijven. De *capturereader.py-app* leest deze blobs, maakt een toevoegend bestand voor elk van uw apparaten en schrijft de gegevens naar *CSV-bestanden* op elk apparaat.

> [!WARNING]
> Deze quickstart is voor versie 1 van de Azure Event Hubs Python SDK. We raden u aan uw code te [migreren](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md) naar [versie 5 van de Python SDK.](get-started-capture-python-v2.md)

In deze walkthrough, u: 

> [!div class="checklist"]
> * Maak een Azure Blob-opslagaccount en -container in de Azure-portal.
> * Schakel Event Hubs Vastleggen in en stuur deze door naar uw opslagaccount.
> * Stuur gegevens naar de gebeurtenishub met behulp van een Python-script.
> * Bestanden van Event Hubs Capture lezen en verwerken met een ander Python-script.

## <a name="prerequisites"></a>Vereisten

- Python 3.4 of `pip` hoger, met geïnstalleerd en bijgewerkt.
  
- Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
  
- Een actieve naamruimte voor gebeurtenishubs en gebeurtenishub, gemaakt door de instructies te volgen bij [Quickstart: Een gebeurtenishub maken met Azure-portal](event-hubs-create.md). Noteer uw namen van uw naamruimte en gebeurtenishub die u later in deze walkthrough gebruiken. 
  
  > [!NOTE]
  > Als u al een opslagcontainer hebt om te gebruiken, u Vastleggen inschakelen en de opslagcontainer selecteren wanneer u de gebeurtenishub maakt. 
  > 
  
- Uw Gebeurtenishubs hebben de naam van de toegangssleutel en de primaire sleutelwaarde gedeeld. Zoek of maak deze waarden onder **Beleid voor gedeelde toegang** op de pagina Gebeurtenishubs. De standaardnaam van de toegangssleutel is **RootManageSharedAccessKey**. Kopieer de naam van de toegangssleutel en de primaire sleutelwaarde die u later in deze walkthrough wilt gebruiken. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Een Azure Blob-opslagaccount en -container maken

Maak een opslagaccount en container om te gebruiken voor het vastleggen. 

1. Meld u aan bij [Azure Portal][Azure portal].
2. Selecteer in de linkernavigatie **Opslagaccounts**en selecteer in het scherm **Opslagaccounts** de optie **Toevoegen**.
3. Selecteer in het scherm voor het maken van een opslagaccount een abonnements- en brongroep en geef het opslagaccount een naam. U de andere selecties standaard laten staan. Selecteer **Controleren + maken,** controleer de instellingen en selecteer **Vervolgens Maken**. 
   
   ![Een opslagaccount maken][1]
   
4. Wanneer de implementatie is voltooid, selecteert u **Ga naar resource**en selecteert u in het scherm **Overzicht** van het opslagaccount de optie **Containers**.
5. Selecteer **+** **Container**in het scherm Containers . 
6. Geef de container in het scherm **Nieuw container** een naam en selecteer **OK**. Noteer de containernaam die u later in de walkthrough wilt gebruiken. 
7. Selecteer **Access-toetsen**in de linkernavigatie van het scherm **Containers** . Kopieer de **naam van het opslagaccount**en de **waarde Sleutel** onder **toets1**om later in de walkthrough te gebruiken.
 
## <a name="enable-event-hubs-capture"></a>Vastleggen van gebeurtenishubs inschakelen

1. Navigeer in de Azure-portal naar de gebeurtenishub door de naamruimte van gebeurtenishubs te selecteren uit **alle bronnen,** **gebeurtenishubs** in de linkernavigatie te selecteren en vervolgens uw gebeurtenishub te selecteren. 
2. Selecteer gebeurtenissen vastleggen in het **overzichtsscherm** **van**de gebeurtenishub .
3. Selecteer **op** het scherm Vastleggen de optie **Aan**. Selecteer vervolgens onder **Azure Storage Container**de optie Container **selecteren**. 
4. Selecteer **in** het scherm Containers de opslagcontainer die u wilt gebruiken en selecteer **Selecteer .** 
5. Selecteer **wijzigingen opslaan**in het scherm **Vastleggen** . 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Een Python-script maken om gebeurtenissen naar gebeurtenishub te verzenden
Dit script stuurt 200 gebeurtenissen naar uw gebeurtenishub. De gebeurtenissen zijn eenvoudige milieumetingen verzonden in JSON.

1. Open uw favoriete Python-editor, zoals [Visual Studio Code][Visual Studio Code].
2. Maak een nieuw bestand met de naam *sender.py*. 
3. Plak de volgende code in *sender.py*. Vervang uw eigen waarden \<voor de \<naamruimte van \<gebeurtenishubs>, \<AccessKeyName>, primaire> en eventhub>.
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='<namespace>', shared_access_key_name='<AccessKeyName>', shared_access_key_value='<primary key value>')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('<eventhub>', s)
       print(y)
   ```
4. Sla het bestand op.

## <a name="create-a-python-script-to-read-capture-files"></a>Een Python-script maken om Vastleggen-bestanden te lezen

Dit script leest de vastgelegde bestanden en maakt een bestand voor elk van uw apparaten om de gegevens alleen voor dat apparaat te schrijven.

1. Maak in uw Python-editor een nieuw bestand met de naam *capturereader.py*. 
2. Plak de volgende code in *capturereader.py*. Vervang uw opgeslagen \<waarden voor \<uw opslagaccount>, \<toegangssleutel voor opslagaccount> en>.
   
   ```python
   import os
   import string
   import json
   import avro.schema
   from avro.datafile import DataFileReader, DataFileWriter
   from avro.io import DatumReader, DatumWriter
   from azure.storage.blob import BlockBlobService
   
   def processBlob(filename):
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
           deviceFile = open(device + '.csv', "a")
           for r in dict[device]:
               deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
   
   def startProcessing(accountName, key, container):
       print('Processor started using path: ' + os.getcwd())
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = str.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('<storageaccount>', '<storage account access key>', '<storagecontainer>')
   ```

## <a name="run-the-python-scripts"></a>De Python-scripts uitvoeren

1. Open een opdrachtprompt met Python op zijn pad en voer deze opdrachten uit om de vereistepakketten van Python te installeren:
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Als u een eerdere `azure-storage` versie `azure`van of , `--upgrade` moet u mogelijk de optie gebruiken.
   
   Mogelijk moet u ook de volgende opdracht uitvoeren. Het uitvoeren van deze opdracht is niet nodig op de meeste systemen. 
   
   ```cmd
   pip install cryptography
   ```
   
2. Voer deze opdracht uit in de map waar u *sender.py* hebt opgeslagen en *capturereader.py:*
   
   ```cmd
   start python sender.py
   ```
   
   De opdracht start een nieuw Python-proces om de afzender uit te voeren.
   
3. Wanneer de opname is voltooid, voert u de opdracht uit:
   
   ```cmd
   python capturereader.py
   ```

   De opnameprocessor downloadt alle niet-lege blobs uit de opslagaccountcontainer en schrijft de resultaten als *CSV-bestanden* in de lokale map. 

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over gebeurtenishubs: 

* [Overzicht van het vastleggen van gebeurtenishubs][Overview of Event Hubs Capture]
* [Voorbeeldtoepassingen die gebruikmaken van Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Overzicht van gebeurtenishubs][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
