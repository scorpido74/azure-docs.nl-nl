---
title: 'Quickstart: Vastgelegde gegevens uit een Python-app lezen - Azure Event Hubs'
description: 'Quickstart: Scripts die gebruikmaken van de Azure Python-SDK om de functie Event Hubs Capture te demonstreren.'
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
ms.custom: seodec18, tracking-python
ms.date: 01/15/2020
ms.author: shvija
ms.openlocfilehash: c726b0d11759d30730046e635c701cf23d130dfc
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561567"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python-azure-eventhub-version-1"></a>Quickstart: Stapsgewijze handleiding voor Event Hubs Capture: Python (azure-eventhub versie 1)

Capture is een functie van Azure Event Hubs. U kunt Capture gebruiken om automatisch de streaminggegevens in uw event hub te leveren aan een Azure Blob-opslagaccount van uw keuze. Deze functie maakt het eenvoudig om batchverwerking uit te voeren voor realtime streaminggegevens. In dit artikel wordt beschreven hoe u Event Hubs Capture gebruikt met Python. Zie [Gebeurtenissen vastleggen via Azure Event Hubs][Overview of Event Hubs Capture] voor meer informatie over Event Hubs Capture.

In deze handleiding wordt gebruikgemaakt van de [Azure Python-SDK](https://azure.microsoft.com/develop/python/) om de functie Capture te demonstreren. Het programma *sender.py* verzendt gesimuleerde omgevingstelemetrie naar Event Hubs in de JSON-indeling. De event hub gebruikt de functie Capture om deze gegevens in batches naar Blob Storage te schrijven. De app *capturereader.py* leest deze blobs, maakt een bestand voor toevoegingen voor elk van uw apparaten en schrijft de gegevens naar *CSV*-bestanden op elk apparaat.

> [!WARNING]
> Deze quickstart is voor versie 1 van de Azure Event Hubs Python-SDK. U wordt aangeraden om uw code te [migreren](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md) naar [versie 5 van de Python-SDK](get-started-capture-python-v2.md).

In deze handleiding doet u het volgende: 

> [!div class="checklist"]
> * Een Azure Blob-opslagaccount en container maken in Azure Portal.
> * Event Hubs Capture inschakelen en deze verwijzen naar uw opslagaccount.
> * Gegevens naar uw event hub verzenden met behulp van een Python-script.
> * Bestanden uit Event Hubs Capture lezen en verwerken met behulp van een ander Python-script.

## <a name="prerequisites"></a>Vereisten

- Python 3.4 of hoger, waarbij `pip` is geïnstalleerd en bijgewerkt.
  
- Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
  
- Een actieve Event Hubs-naamruimte en event hub, gemaakt door de instructies te volgen in [Quickstart: Een event hub maken met behulp van Azure Portal](event-hubs-create.md). Noteer uw naamruimte en namen van event hubs voor later gebruik in deze handleiding. 
  
  > [!NOTE]
  > Als u al over een opslagcontainer beschikt die u kunt gebruiken, kunt u Capture inschakelen en de opslagcontainer selecteren wanneer u de event hub maakt. 
  > 
  
- Uw naam voor de sleutel voor gedeelde toegang van Event Hubs en de waarde van de primaire sleutel. Zoek of maak deze waarden onder **Beleid voor gedeelde toegang** op uw Event Hubs-pagina. De naam van de standaardtoegangssleutel is **RootManageSharedAccessKey**. Kopieer de naam van de toegangssleutel en de waarde voor de primaire sleutel voor later gebruik in deze handleiding. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Een Azure Blob-opslagaccount en container maken

Maak een opslagaccount en een container die u gebruikt voor het vastleggen. 

1. Meld u aan bij de [Azure-portal][Azure portal].
2. Selecteer in het linkernavigatievenster de optie **Opslagaccounts** en in het scherm **Opslagaccounts** de optie **Toevoegen**.
3. Selecteer in het scherm voor het maken van een opslagaccount een abonnement en resourcegroep en geef het opslagaccount een naam. U kunt de andere selecties op de standaardinstelling laten staan. Selecteer **Controleren en maken**, controleer de instellingen en selecteer **Maken**. 
   
   ![Een opslagaccount maken][1]
   
4. Wanneer de implementatie is voltooid, selecteert u **Ga naar resource** en selecteert u in het scherm **Overzicht** van het opslagaccount de optie **Containers**.
5. Selecteer in het scherm **Containers** de optie **+ Container**. 
6. Geef in het scherm **Nieuwe container** de container een naam en selecteer vervolgens **OK**. Noteer de naam van de container voor later gebruik in de handleiding. 
7. Selecteer in het linkernavigatievenster van het scherm **Containers** de optie **Toegangssleutels**. Kopieer de **naam van het opslagaccount** en de waarde voor **Sleutel** onder **key1** voor later gebruik in de handleiding.
 
## <a name="enable-event-hubs-capture"></a>Event Hubs Capture inschakelen

1. Ga in Azure Portal naar uw event hub door de bijbehorende Event Hubs-naamruimte te selecteren in **Alle resources**, in het linkernavigatievenster de optie **Event hubs** te selecteren en vervolgens uw event hub te selecteren. 
2. Selecteer in het scherm **Overzicht** voor de event hub de optie **Gebeurtenissen vastleggen**.
3. Selecteer **Aan** in het scherm **Vastleggen**. Selecteer vervolgens onder **Azure Storage-container** de optie **Container selecteren**. 
4. Selecteer in het scherm **Containers** de opslagcontainer die u wilt gebruiken en selecteer vervolgens **Selecteren**. 
5. Selecteer in het scherm **Vastleggen** de optie **Wijzigingen opslaan**. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Een Python-script maken om gebeurtenissen naar een event hub te verzenden
Met dit script worden 200 gebeurtenissen naar uw event hub verzonden. De gebeurtenissen zijn eenvoudige omgevingsleesbewerkingen die in de JSON-indeling worden verzonden.

1. Open uw favoriete Python-editor, bijvoorbeeld [Visual Studio Code][Visual Studio Code].
2. Maak een nieuw bestand met de naam *sender.py*. 
3. Plak de volgende code in *sender.py*. Vervang \<namespace>, \<AccessKeyName>, \<primary key value> en \<eventhub> van de event hubs door uw eigen waarden.
   
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

## <a name="create-a-python-script-to-read-capture-files"></a>Een Python-script maken om Capture-bestanden te lezen

Met dit script worden de vastgelegde bestanden gelezen en wordt voor elk van uw apparaten een bestand gemaakt om uitsluitend de gegevens voor dat apparaat te schrijven.

1. Maak in uw Python-editor een nieuw bestand met de naam *capturereader.py*. 
2. Plak de volgende code in *capturereader.py*. Vervang de waarden door de waarden die u hebt opgeslagen voor de \<storageaccount>, \<storage account access key> en \<storagecontainer>.
   
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

1. Open een opdrachtprompt met Python in het pad en voer de volgende opdrachten uit om de pakketten te installeren die voor Python vereist zijn:
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Als u over een eerdere versie van `azure-storage` of `azure` beschikt, moet u mogelijk de optie `--upgrade` gebruiken.
   
   U moet mogelijk ook de volgende opdracht uitvoeren. Het is in de meeste systemen niet nodig om deze opdracht uit te voeren. 
   
   ```cmd
   pip install cryptography
   ```
   
2. Voer de volgende opdracht uit vanuit de map waarin u *sender.py* en *capturereader.py* hebt opgeslagen:
   
   ```cmd
   start python sender.py
   ```
   
   De opdracht start een nieuw Python-proces om het verzendprogramma uit te voeren.
   
3. Wanneer het vastleggen is voltooid, voert u de volgende opdracht uit:
   
   ```cmd
   python capturereader.py
   ```

   Het Capture-verwerkingsprogramma downloadt alle niet-lege blobs uit de opslagaccountcontainer en schrijft de resultaten als *. CSV*-bestanden naar de lokale map. 

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over Event Hubs kunt u de volgende artikelen lezen: 

* [Overzicht van Event Hubs Capture][Overview of Event Hubs Capture]
* [Voorbeeldtoepassingen die gebruikmaken van Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Event Hubs-overzicht][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
