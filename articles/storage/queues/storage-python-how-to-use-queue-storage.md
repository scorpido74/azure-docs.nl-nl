---
title: Azure-wachtrij opslag gebruiken v 2.1 van python-Azure Storage
description: Meer informatie over het gebruik van Azure Queue-service v 2.1 van python voor het maken en verwijderen van wacht rijen en het invoegen, ophalen en verwijderen van berichten.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2019
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: seo-javascript-october2019, tracking-python
ms.openlocfilehash: 46d144a95708ac834478871ca27763f0ebd3b201
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84805246"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>Azure-wachtrij opslag gebruiken v 2.1 van python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

In dit artikel worden algemene scenario's beschreven die gebruikmaken van de Azure Queue Storage-service. De gedekte scenario's zijn het invoegen, inspecteren, ophalen en verwijderen van wachtrij berichten, en het maken en verwijderen van wacht rijen.

> [!IMPORTANT]
> In dit artikel wordt verwezen naar de oudere versie van de Azure Storage-client bibliotheek voor python. Om aan de slag te gaan met de nieuwste versie, raadpleegt u [Quick Start: client bibliotheek voor Azure Queue Storage voor python](storage-quickstart-queues-python.md)

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Overzicht

De voor beelden in dit artikel zijn geschreven in Python en gebruiken de [Microsoft Azure Storage SDK voor python]. Zie de sectie [volgende stappen](#next-steps) voor meer informatie over wacht rijen.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Azure Storage-SDK voor Python downloaden en installeren

De [Azure Storage SDK voor python](https://github.com/azure/azure-storage-python) vereist Python versie 2,7, 3,3 of hoger.
 
### <a name="install-via-pypi"></a>Installeren via PyPi

Als u wilt installeren via de python-pakket index (PyPI), typt u:

```bash
pip install azure-storage-queue==2.1.0
```

> [!NOTE]
> Als u een upgrade uitvoert van de Azure Storage SDK voor python versie 0,36 of eerder, verwijdert u de oudere SDK met `pip uninstall azure-storage` voordat u het meest recente pakket installeert.

Zie [Azure Storage SDK voor python](https://github.com/Azure/azure-storage-python/)voor een alternatieve installatie methode.

## <a name="view-the-sample-application"></a>De voorbeeld toepassing weer geven

Zie [Azure Storage: aan de slag met Azure queues in python](https://github.com/Azure-Samples/storage-queue-python-getting-started)voor informatie over het weer geven en uitvoeren van een voorbeeld toepassing die laat zien hoe u python kunt gebruiken met Azure-wacht rijen. 

Als u de voorbeeld toepassing wilt uitvoeren, moet u ervoor zorgen dat u zowel de als-pakketten hebt geïnstalleerd `azure-storage-queue` `azure-storage-common` .

## <a name="create-a-queue"></a>Een wachtrij maken

Met het [QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) -object kunt u met wacht rijen werken. Met de volgende code wordt een- `QueueService` object gemaakt. Voeg het volgende toe aan de bovenkant van een python-bestand waarin u programmatisch toegang wilt krijgen Azure Storage:

```python
from azure.storage.queue import QueueService
```

Met de volgende code wordt een `QueueService` object gemaakt met behulp van de naam van het opslag account en de account sleutel. Vervang *MyAccount* en *MyKey* door de naam en sleutel van uw account.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>Een bericht in een wachtrij invoegen

Als u een bericht in een wachtrij wilt invoegen, gebruikt u de methode [put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) om een nieuw bericht te maken en dit aan de wachtrij toe te voegen.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Azure-wachtrij berichten worden opgeslagen als tekst. Als u binaire gegevens wilt opslaan, moet u de functies van base64-code ring en decodering instellen op het object Queue-service voordat u een bericht in de wachtrij plaatst.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>Bekijken van het volgende bericht

U kunt het bericht aan de voor kant van een wachtrij bekijken zonder het uit de wachtrij te verwijderen door de [peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) -methode aan te roepen. In wordt standaard weer `peek_messages` gegeven als één bericht.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>Bericht uit een wachtrij verwijderen

Uw code verwijdert een bericht uit een wachtrij in twee stappen. Wanneer u [get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)aanroept, wordt standaard het volgende bericht in een wachtrij weer gegeven. Een bericht dat wordt geretourneerd van is niet `get_messages` zichtbaar voor andere code die berichten uit deze wachtrij leest. Standaard blijft het bericht onzichtbaar gedurende 30 seconden. Als u het verwijderen van het bericht uit de wachtrij wilt volt ooien, moet u ook [delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-)aanroepen. Dit proces met twee stappen voor het verwijderen van een bericht zorgt ervoor dat wanneer uw code een bericht niet kan verwerken als gevolg van een hardware-of software fout, een ander exemplaar van uw code hetzelfde bericht kan ophalen en het opnieuw proberen. Uw code aanroepen `delete_message` direct nadat het bericht is verwerkt.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij kunt aanpassen. Ten eerste kunt u berichten batchgewijs (maximaal 32) ophalen. Ten tweede kunt u een langere of kortere time-out voor onzichtbaarheid instellen, zodat uw code meer of minder tijd krijgt voor het volledig verwerken van elk bericht. In het volgende code voorbeeld wordt de `get_messages` methode gebruikt om 16 berichten in één aanroep op te halen. Vervolgens wordt elk bericht verwerkt met behulp van een for-lus. De time-out voor onzichtbaarheid wordt ingesteld op vijf minuten voor elk bericht.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>De inhoud van een bericht in de wachtrij wijzigen

U kunt de inhoud van een bericht in de wachtrij wijzigen. Als het bericht een werktaak vertegenwoordigt, kunt u deze functie gebruiken om de status van de werktaak bij te werken. In de onderstaande code wordt gebruikgemaakt van de [update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) -methode om een bericht bij te werken. De time-out voor de zicht baarheid is ingesteld op 0, wat betekent dat het bericht onmiddellijk wordt weer gegeven en dat de inhoud wordt bijgewerkt.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>Lengte van de wachtrij ophalen

U kunt een schatting ophalen van het aantal berichten in de wachtrij. De methode [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) vraagt de Queue-service om meta gegevens over de wachtrij te retour neren en de `approximate_message_count` . Het resultaat is alleen van benadering omdat berichten kunnen worden toegevoegd of verwijderd nadat de wachtrij service op uw aanvraag reageert.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>Een wachtrij verwijderen

Als u een wachtrij en alle berichten erin wilt verwijderen, roept u de [delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-) -methode aan.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes van wachtrij opslag hebt geleerd, volgt u deze koppelingen voor meer informatie.

* [Azure queues python API-verwijzing](/python/api/azure-storage-queue)
* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [REST-API voor Azure Storage-services](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK voor python]: https://github.com/Azure/azure-storage-python
