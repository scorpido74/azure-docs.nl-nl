---
title: Azure Queue storage v2.1 gebruiken vanuit Python - Azure Storage
description: Meer informatie over het gebruik van de Azure Queue-service v2.1 van Python om wachtrijen te maken en te verwijderen en berichten in te voegen, op te halen en te verwijderen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-october2019
ms.openlocfilehash: ca0831fd7554058d21e315b67d6965579af1d38b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060923"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>Azure Queue storage v2.1 van Python gebruiken

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

In dit artikel worden veelvoorkomende scenario's getoond met de Azure Queue-opslagservice. De behandelde scenario's omvatten het invoegen, gluren, opvragen en verwijderen van wachtrijberichten en het maken en verwijderen van wachtrijen.

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Overzicht

De voorbeelden in dit artikel zijn geschreven in Python en gebruiken de [Microsoft Azure Storage SDK voor Python.] Zie de sectie Volgende [stappen](#next-steps) voor meer informatie over wachtrijen.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Azure Storage-SDK voor Python downloaden en installeren

De [Azure Storage SDK voor Python](https://github.com/azure/azure-storage-python) vereist Python-versie 2.7, 3.3 of hoger.
 
### <a name="install-via-pypi"></a>Installeren via PyPi

Als u wilt installeren via de Python Package Index (PyPI), typt u:

```bash
pip install azure-storage-queue==2.1.0
```

> [!NOTE]
> Als u een upgrade uitvoert van de Azure Storage SDK voor Python-versie `pip uninstall azure-storage` 0.36 of eerder, verwijdert u de oudere SDK voordat u het nieuwste pakket installeert.

Zie [Azure Storage SDK voor Python voor](https://github.com/Azure/azure-storage-python/)alternatieve installatiemethoden.

## <a name="view-the-sample-application"></a>De voorbeeldtoepassing weergeven

Zie [Azure Storage: Aan de slag met Azure Queues in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started)om een voorbeeldtoepassing weer te geven en uit te voeren die laat zien hoe u Python gebruiken met Azure-wachtrijen. 

Als u de voorbeeldtoepassing wilt uitvoeren, `azure-storage-queue` `azure-storage-common` controleert u of u zowel de pakketten als de pakketten hebt geïnstalleerd.

## <a name="create-a-queue"></a>Een wachtrij maken

Met het object [QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) u werken met wachtrijen. Met de volgende `QueueService` code wordt een object gemaakt. Voeg het volgende toe boven aan een Python-bestand waarin u programmatisch toegang wilt krijgen tot Azure Storage:

```python
from azure.storage.queue import QueueService
```

Met de volgende `QueueService` code wordt een object gemaakt met de naam van het opslagaccount en de accountsleutel. Vervang *mijn account* en mijn *sleutel* door uw accountnaam en sleutel.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>Een bericht in een wachtrij invoegen

Als u een bericht in een wachtrij wilt invoegen, gebruikt u de [methode put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) om een nieuw bericht te maken en toe te voegen aan de wachtrij.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Azure-wachtrijberichten worden opgeslagen als tekst. Als u binaire gegevens wilt opslaan, stelt u base64-coderings- en decoderingsfuncties in op het wachtrijserviceobject voordat u een bericht in de wachtrij plaatst.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>Bekijken van het volgende bericht

U het bericht voor in een wachtrij bekijken zonder het uit de wachtrij te verwijderen door de [methode peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) aan te roepen. Standaard `peek_messages` gluurt u naar één bericht.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>Wachtrijberichten dewachtrij

Uw code verwijdert een bericht uit een wachtrij in twee stappen. Wanneer u [get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)belt, krijgt u standaard het volgende bericht in een wachtrij. Een bericht `get_messages` dat wordt geretourneerd, wordt onzichtbaar voor andere codeleesberichten uit deze wachtrij. Standaard blijft het bericht onzichtbaar gedurende 30 seconden. Als u wilt eindigen met het verwijderen van het bericht uit de wachtrij, moet u ook [delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-)bellen. Dit proces in twee stappen van het verwijderen van een bericht zorgt ervoor dat wanneer uw code een bericht niet verwerkt als gevolg van hardware- of softwarefouten, een ander exemplaar van uw code hetzelfde bericht kan ontvangen en het opnieuw kan proberen. Uw code `delete_message` roept direct nadat het bericht is verwerkt.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij kunt aanpassen. Ten eerste kunt u berichten batchgewijs (maximaal 32) ophalen. Ten tweede kunt u een langere of kortere time-out voor onzichtbaarheid instellen, zodat uw code meer of minder tijd krijgt voor het volledig verwerken van elk bericht. In het volgende `get_messages` codevoorbeeld wordt de methode gebruikt om 16 berichten in één gesprek te ontvangen. Vervolgens verwerkt het elk bericht met behulp van een voor lus. De time-out voor onzichtbaarheid wordt ingesteld op vijf minuten voor elk bericht.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>De inhoud van een bericht in de wachtrij wijzigen

U kunt de inhoud van een bericht in de wachtrij wijzigen. Als het bericht een werktaak vertegenwoordigt, kunt u deze functie gebruiken om de status van de werktaak bij te werken. De onderstaande code maakt gebruik van de [update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) methode om een bericht bij te werken. De time-out voor zichtbaarheid is ingesteld op 0, wat betekent dat het bericht onmiddellijk wordt weergegeven en de inhoud wordt bijgewerkt.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>Lengte van de wachtrij ophalen

U kunt een schatting ophalen van het aantal berichten in de wachtrij. De [get_queue_metadata-methode](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) vraagt de wachtrijservice om metagegevens over de wachtrij en de `approximate_message_count`. Het resultaat is slechts bij benadering omdat berichten kunnen worden toegevoegd of verwijderd nadat de wachtrijservice op uw verzoek reageert.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>Een wachtrij verwijderen

Als u een wachtrij en alle berichten in deze wachtrij wilt verwijderen, roept u de [methode delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-) aan.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Volgende stappen

Nu u de basisprincipes van wachtrijopslag hebt geleerd, volgt u deze koppelingen voor meer informatie.

* [Verwijzing naar Python-api voor Azure-wachtrijen](/python/api/azure-storage-queue)
* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [REST-API voor Azure Storage-services](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK voor Python]: https://github.com/Azure/azure-storage-python
