---
title: 'Snelstartgids: vastgelegde gegevens van python-app lezen-Azure Event Hubs'
description: 'Quick Start: scripts die gebruikmaken van de Azure python SDK om de functie voor het vastleggen van Event Hubs te demonstreren.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 01/08/2020
ms.author: shvija
ms.openlocfilehash: e81871e27c04f8a43f678110d7f44cc9c3be149c
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940760"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python"></a>Snelstartgids: overzicht van het vastleggen van Event Hubs: python

Vastleggen is een functie van Azure Event Hubs. U kunt vastleggen gebruiken om automatisch de streaming-gegevens in uw Event Hub te leveren naar een Azure Blob Storage-account van uw keuze. Deze mogelijkheid maakt het eenvoudig om batch verwerking uit te voeren op realtime streaming-gegevens. In dit artikel wordt beschreven hoe u Event Hubs Capture gebruiken met Python. Zie [gebeurtenissen vastleggen via Azure Event hubs][Overview of Event Hubs Capture]voor meer informatie over het vastleggen van Event hubs.

In dit scenario wordt de [Azure PYTHON SDK](https://azure.microsoft.com/develop/python/) gebruikt om de Capture-functie te demonstreren. Het *Sender.py* -programma verzendt gesimuleerde omgevings-telemetrie naar Event hubs in JSON-indeling. De Event Hub gebruikt de Capture-functie om deze gegevens in batches te schrijven naar Blob Storage. De *capturereader.py* -app leest deze blobs, maakt een toevoeg bestand voor elk van uw apparaten en schrijft de gegevens naar *CSV* -bestanden op elk apparaat.

> [!IMPORTANT]
> Deze Snelstartgids maakt gebruik van versie 1 van de Azure Event Hubs python SDK. Als u geen ervaring hebt met Azure Event Hubs, gebruikt u versie 5 van de python-SDK. Zie [dit artikel](get-started-capture-python-v2.md)voor een Snelstartgids die versie 5 van de PYTHON-SDK gebruikt. Als u bestaande code van versie 1 naar versie 5 wilt migreren, raadpleegt u de [migratie handleiding](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).

In dit scenario kunt u het volgende doen: 

> [!div class="checklist"]
> * Maak een Azure Blob-opslag account en een container in de Azure Portal.
> * Schakel Event Hubs Capture in en stuur het naar uw opslag account.
> * Gegevens naar uw Event Hub verzenden met behulp van een python-script.
> * Bestanden lezen en verwerken van Event Hubs vastleggen met behulp van een ander python-script.

## <a name="prerequisites"></a>Vereisten

- Python 3,4 of hoger, met `pip` geïnstalleerd en bijgewerkt.
  
- Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
  
- Een actieve Event Hubs naam ruimte en Event Hub, gemaakt door de instructies te volgen op [Quick Start: een event hub maken met Azure Portal](event-hubs-create.md). Noteer uw naam ruimte en Event Hub namen die u later in dit overzicht kunt gebruiken. 
  
  > [!NOTE]
  > Als u al een opslag container hebt om te gebruiken, kunt u vastleggen inschakelen en de opslag container selecteren wanneer u de Event hub maakt. 
  > 
  
- Uw Event Hubs naam van de gedeelde toegangs sleutel en de waarde van de primaire sleutel. Zoek of maak deze waarden onder **beleid voor gedeelde toegang** op de pagina Event hubs. De standaard naam van de toegangs sleutel is **RootManageSharedAccessKey**. Kopieer de naam van de toegangs sleutel en de waarde voor de primaire sleutel die u later in dit overzicht wilt gebruiken. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Een Azure Blob-opslag account en-container maken

Maak een opslag account en een container die moeten worden gebruikt voor de opname. 

1. Meld u aan bij de [Azure Portal][Azure portal].
2. Selecteer in de linkernavigatiebalk **opslag accounts**en selecteer in het scherm **opslag accounts** de optie **toevoegen**.
3. Selecteer een abonnement en resource groep in het scherm voor het maken van het opslag account en geef het opslag account een naam. U kunt de andere selecties standaard laten staan. Selecteer **controleren + maken**, Controleer de instellingen en selecteer vervolgens **maken**. 
   
   ![Een opslagaccount maken][1]
   
4. Wanneer de implementatie is voltooid, selecteert **u Ga naar resource**en selecteert u in het scherm **overzicht** van opslag accounts de optie **containers**.
5. Selecteer op het scherm **containers** **+ container**. 
6. Geef in het scherm **nieuwe container** de container een naam en selecteer **OK**. Noteer de naam van de container die u later in het overzicht moet gebruiken. 
7. Selecteer in de linkernavigatiebalk van het venster **containers** de optie **toegangs sleutels**. Kopieer de **naam van het opslag account**en de **sleutel** waarde onder **key1**om later in de walkthrough te gebruiken.
 
## <a name="enable-event-hubs-capture"></a>Vastleggen van Event Hubs inschakelen

1. Ga in de Azure Portal naar uw Event Hub door de Event Hubs naam ruimte te selecteren uit **alle resources**, **Event hubs** te selecteren in de linkernavigatiebalk en vervolgens uw event hub te selecteren. 
2. Selecteer **gebeurtenissen vastleggen**in het scherm **overzicht** van Event hub.
3. Selecteer **op**het scherm **vastleggen** . Selecteer vervolgens onder **Azure storage container**de optie **container selecteren**. 
4. Selecteer op het scherm **containers** de opslag container die u wilt gebruiken en selecteer vervolgens **selecteren**. 
5. Selecteer op het scherm **vastleggen** de optie **wijzigingen opslaan**. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Een python-script maken voor het verzenden van gebeurtenissen naar Event hub
Met dit script verzonden 200 gebeurtenissen naar uw event hub. De gebeurtenissen zijn eenvoudige omgevings leesingen die in JSON worden verzonden.

1. Open uw favoriete python-editor, zoals [Visual Studio code][Visual Studio Code].
2. Maak een nieuw bestand met de naam *Sender.py*. 
3. Plak de volgende code in *Sender.py*. Vervang uw eigen waarden door de Event Hubs \<naam ruimte >, \<AccessKey >, \<primaire-sleutel waarde > en \<eventhub->.
   
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

## <a name="create-a-python-script-to-read-capture-files"></a>Een python-script maken om opname bestanden te lezen

Met dit script worden de vastgelegde bestanden gelezen en wordt voor elk van uw apparaten een bestand gemaakt om alleen de gegevens voor dat apparaat te schrijven.

1. Maak een nieuw bestand met de naam *capturereader.py*in de python-editor. 
2. Plak de volgende code in *capturereader.py*. Vervang uw opgeslagen waarden door voor uw \<Storage account >, \<toegangs sleutel voor het opslag account > en \<storagecontainer->.
   
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

## <a name="run-the-python-scripts"></a>De python-scripts uitvoeren

1. Open een opdracht prompt met python in het pad en voer deze opdrachten uit om de python-vereiste pakketten te installeren:
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Als u een eerdere versie van `azure-storage` of `azure`hebt, moet u mogelijk de `--upgrade` optie gebruiken.
   
   Mogelijk moet u ook de volgende opdracht uitvoeren. Het is niet nodig om deze opdracht uit te voeren op de meeste systemen. 
   
   ```cmd
   pip install cryptography
   ```
   
2. Voer de volgende opdracht uit in de map waar u *Sender.py* en *capturereader.py*hebt opgeslagen:
   
   ```cmd
   start python sender.py
   ```
   
   De opdracht start een nieuw python-proces om de afzender uit te voeren.
   
3. Wanneer het vastleggen is voltooid, voert u deze opdracht uit:
   
   ```cmd
   python capturereader.py
   ```

   De Capture-processor downloadt alle niet-lege blobs uit de container van het opslag account en schrijft de resultaten als *CSV* -bestanden naar de lokale map. 

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over Event Hubs raadpleegt u: 

* [Overzicht van Event Hubs Capture][Overview of Event Hubs Capture]
* [Voorbeeldtoepassingen die gebruikmaken van Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Event Hubs-overzicht][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
