---
title: 'Quick Start: Azure Queue-opslag bibliotheek V12-python'
description: Meer informatie over het gebruik van de Azure Queue python V12-bibliotheek voor het maken van een wachtrij en het toevoegen van berichten aan de wachtrij. Vervolgens leert u hoe u berichten leest en verwijdert uit de wachtrij. U leert ook hoe u een wachtrij verwijdert.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/10/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: a34fdc2f6d6698f53dc2ff7fdc11d0a985b23415
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473127"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Quick Start: Azure Queue Storage-client bibliotheek V12 voor python

Ga aan de slag met de Azure Queue Storage-client bibliotheek versie 12 voor python. Azure Queue-opslag is een service voor het opslaan van grote aantallen berichten die later worden opgehaald en verwerkt. Volg deze stappen om het pakket te installeren en voorbeeld code voor basis taken uit te proberen.

Gebruik de Azure Queue Storage-client bibliotheek V12 voor python voor het volgende:

* Een wachtrij maken
* Berichten toevoegen aan een wachtrij
* Berichten in een wachtrij bekijken
* Een bericht in een wachtrij bijwerken
* Berichten van een wachtrij ontvangen
* Berichten uit een wachtrij verwijderen
* Een wachtrij verwijderen

[API-referentie documentatie](https://docs.microsoft.com/python/api/azure-storage-queue/index) | [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue) | [pakket (Python-pakket index)](https://pypi.org/project/azure-storage-queue/) | voor [beelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* Azure Storage-account: [een opslag account maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Python](https://www.python.org/downloads/) voor uw besturings systeem-2,7, 3,5 of hoger

## <a name="setting-up"></a>Instellen

In deze sectie wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Queue Storage-client bibliotheek V12 voor python.

### <a name="create-the-project"></a>Het project maken

Maak een python-toepassing *met de naam queues-Quick Start-V12*.

1. Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor het project.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Schakel over naar de nieuwe map *wacht rijen-Quick Start-V12* .

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>Het pakket installeren

Installeer de Azure Blob Storage-client bibliotheek voor python-pakket met behulp van de opdracht `pip install`.

```console
pip install azure-storage-queue
```

Met deze opdracht wordt de Azure Queue Storage-client bibliotheek voor python-pakket en alle bibliotheken waarvan deze afhankelijk is, geïnstalleerd. In dit geval is dat alleen de Azure core-bibliotheek voor python.

### <a name="set-up-the-app-framework"></a>Het app-Framework instellen

1. Een nieuw tekst bestand openen in de code-editor
1. `import`-instructies toevoegen
1. De structuur voor het programma maken, inclusief zeer eenvoudige uitzonde ringen verwerken

    Hier volgt de code:

    ```python
    import os, uuid
    from azure.storage.queue import QueueServiceClient, QueueClient, QueueMessage

    try:
        print("Azure Queue storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)

    ```

1. Sla het nieuwe bestand op als *Queues-QuickStart-V12.py* in de map *Queues-Quick Start-V12* .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Object model

Azure Queue Storage is een service om grote aantallen berichten op te slaan. Een wachtrij bericht kan Maxi maal 64 KB groot zijn. Een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteits limiet van een opslag account. Wacht rijen worden vaak gebruikt om een achterstand te maken voor het asynchroon verwerken van werk. Queue Storage biedt drie typen resources:

* Het opslag account
* Een wachtrij in het opslag account
* Berichten in de wachtrij

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de architectuur van de wachtrij opslag](./media/storage-queues-introduction/queue1.png)

Gebruik de volgende python-klassen om te communiceren met deze resources:

* [QueueServiceClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient): met de `QueueServiceClient` kunt u de alle wacht rijen in uw opslag account beheren.
* [QueueClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient): de klasse `QueueClient` biedt u de mogelijkheid om een afzonderlijke wachtrij en de bijbehorende berichten te beheren en te bewerken.
* [QueueMessage](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage): de klasse `QueueMessage` vertegenwoordigt de afzonderlijke objecten die worden geretourneerd bij het aanroepen van [receive_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) op een wachtrij.

## <a name="code-examples"></a>Codevoorbeelden

In deze voorbeeld code fragmenten ziet u hoe u de volgende acties kunt uitvoeren met de Azure Queue Storage-client bibliotheek voor python:

* [De connection string ophalen](#get-the-connection-string)
* [Een wachtrij maken](#create-a-queue)
* [Berichten toevoegen aan een wachtrij](#add-messages-to-a-queue)
* [Berichten in een wachtrij bekijken](#peek-at-messages-in-a-queue)
* [Een bericht in een wachtrij bijwerken](#update-a-message-in-a-queue)
* [Berichten van een wachtrij ontvangen](#receive-messages-from-a-queue)
* [Berichten uit een wachtrij verwijderen](#delete-messages-from-a-queue)
* [Een wachtrij verwijderen](#delete-a-queue)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de connection string voor het opslag account op. De connection string wordt de omgevings variabele opgeslagen die u hebt gemaakt in de sectie [uw opslag Connection String configureren](#configure-your-storage-connection-string) .

Voeg deze code toe binnen het `try` blok:

```python
    # Retrieve the connection string for use with the application. The storage
    # connection string is stored in an environment variable on the machine
    # running the application called AZURE_STORAGE_CONNECTION_STRING. If the
    # environment variable is created after the application is launched in a
    # console or with Visual Studio, the shell or application needs to be
    # closed and reloaded to take the environment variable into account.
    connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-queue"></a>Een wachtrij maken

Kies een naam voor de nieuwe wachtrij. De onderstaande code voegt een UUID-waarde toe aan de naam van de wachtrij om ervoor te zorgen dat deze uniek is.

> [!IMPORTANT]
> De naam van een wachtrij mag alleen kleine letters, cijfers en afbreek streepjes bevatten en moet beginnen met een letter of een cijfer. Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter. De naam moet ook tussen de 3 en 63 tekens lang zijn. Zie de [naamgeving van wacht rijen en meta gegevens](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata)voor meer informatie over de naamgeving van wacht rijen.

Maak een instantie van de klasse [QueueClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient) . Roep vervolgens de [create_queue](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) -methode aan om de wachtrij in uw opslag account te maken.

Voeg deze code toe aan het einde van het `try` blok:

```python
    # Create a unique name for the queue
    queue_name = "quickstartqueues-" + str(uuid.uuid4())

    print("Creating queue: " + queue_name)

    # Instantiate a QueueClient which will be
    # used to create and manipulate the queue
    queue_client = QueueClient.from_connection_string(connect_str, queue_name)

    # Create the queue
    queue_client.create_queue()
```

### <a name="add-messages-to-a-queue"></a>Berichten toevoegen aan een wachtrij

Met het volgende code fragment worden berichten toegevoegd aan de wachtrij door de [send_message](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) methode aan te roepen. Ook wordt de [QueueMessage](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage) die wordt geretourneerd door de derde `send_message`-aanroep opgeslagen. De `saved_message` wordt gebruikt om de inhoud van het bericht later in het programma bij te werken.

Voeg deze code toe aan het einde van het `try` blok:

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Berichten in een wachtrij bekijken

Bekijk de berichten in de wachtrij door de [peek_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) methode aan te roepen. Met de methode `peek_messages` worden een of meer berichten van het begin van de wachtrij opgehaald, maar wordt de zicht baarheid van het bericht niet gewijzigd.

Voeg deze code toe aan het einde van het `try` blok:

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>Een bericht in een wachtrij bijwerken

De inhoud van een bericht bijwerken door het aanroepen van de [update_message](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) methode. Met de methode `update_message` kan de time-out en inhoud van de zicht baarheid van een bericht worden gewijzigd. De inhoud van het bericht moet een teken reeks met UTF-8-code ring zijn met een grootte van Maxi maal 64 KB. Naast de nieuwe inhoud, geeft u waarden uit het bericht dat eerder in de code is opgeslagen. De `saved_message` waarden bepalen welk bericht moet worden bijgewerkt.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen

Down load eerder toegevoegde berichten door de [receive_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) -methode aan te roepen.

Voeg deze code toe aan het einde van het `try` blok:

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Berichten uit een wachtrij verwijderen

Berichten uit de wachtrij verwijderen nadat ze zijn ontvangen en verwerkt. In dit geval wordt de verwerking gewoon het bericht op de console weer gegeven.

De app wordt gepauzeerd voor gebruikers invoer door `input` aan te roepen voordat de berichten worden verwerkt en verwijderd. Controleer in uw [Azure Portal](https://portal.azure.com) dat de resources correct zijn gemaakt, voordat ze worden verwijderd. Berichten die niet expliciet worden verwijderd, worden uiteindelijk weer zichtbaar in de wachtrij om ze te kunnen verwerken.

Voeg deze code toe aan het einde van het `try` blok:

```python
    print("\nPress Enter key to 'process' messages and delete them from the queue...")
    input()

    for msg_batch in messages.by_page():
            for msg in msg_batch:
                # "Process" the message
                print(msg.content)
                # Let the service know we're finished with
                # the message and it can be safely deleted.
                queue_client.delete_message(msg)
```

### <a name="delete-a-queue"></a>Een wachtrij verwijderen

Met de volgende code wordt de resources opgeschoond die de app heeft gemaakt door de wachtrij te verwijderen met behulp van de [delete_queue](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) methode.

Voeg deze code toe aan het einde van de `try` blok keer en sla het bestand op:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>De code uitvoeren

Met deze app worden drie berichten gemaakt en toegevoegd aan een Azure-wachtrij. De code vermeldt de berichten in de wachtrij, haalt deze vervolgens op en verwijdert deze voordat u de wachtrij definitief verwijdert.

Navigeer in uw console venster naar de map met het *Queues-QuickStart-V12.py* -bestand en voer de volgende `python` opdracht uit om de app uit te voeren.

```console
python queues-quickstart-v12.py
```

De uitvoer van de app is vergelijkbaar met het volgende voor beeld:

```output
Azure Queue storage v12 - Python quickstart sample
Creating queue: quickstartqueues-cac365be-7ce6-4065-bd65-3756ea052cb8

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

First message
Second message
Third message has been updated

Press Enter key to delete the queue...

Deleting queue...
Done
```

Wanneer de app wordt onderbroken voordat er berichten worden ontvangen, controleert u uw opslag account in de [Azure Portal](https://portal.azure.com). Controleer of de berichten in de wachtrij staan.

Druk op **Enter** om de berichten te ontvangen en te verwijderen. Wanneer u hierom wordt gevraagd, drukt u nogmaals op **Enter** om de wachtrij te verwijderen en de demo te volt ooien.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een wachtrij maakt en er berichten aan toevoegt met behulp van python-code. Vervolgens hebt u geleerd hoe u berichten kunt bekijken, ophalen en verwijderen. Ten slotte hebt u geleerd hoe u een berichten wachtrij verwijdert.

Voor zelf studies, voor beelden, snel starten en andere documentatie gaat u naar:

> [!div class="nextstepaction"]
> [Ontwikkel aars van Azure voor python](https://docs.microsoft.com/azure/python/)

* Zie de [Azure Storage libraries voor python voor](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)meer informatie.
* Als u meer voor beelden wilt zien van Azure Queue Storage-voor beeld-apps, gaat u door naar [Azure Queue Storage V12 python-client bibliotheek voorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples).
