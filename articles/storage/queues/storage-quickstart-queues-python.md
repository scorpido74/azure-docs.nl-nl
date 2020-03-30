---
title: 'Snelstart: Azure Queue storage library v12 - Python'
description: Meer informatie over het gebruik van de Azure Queue Python v12-bibliotheek om een wachtrij te maken en berichten toe te voegen aan de wachtrij. Vervolgens leert u hoe u berichten uit de wachtrij lezen en verwijderen. U leert ook hoe u een wachtrij verwijdert.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/10/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: d6ccd3cc61f9d8244874823be76496a4f4e1073c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78199764"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Snelstart: Azure Queue storage client library v12 voor Python

Aan de slag met de Azure Queue-opslagclientbibliotheekversie 12 voor Python. Azure Queue-opslag is een service voor het opslaan van grote aantallen berichten voor later ophalen en verwerken. Volg deze stappen om het pakket te installeren en probeer voorbeeldcode uit voor basistaken.

Gebruik de Azure Queue-opslagclientbibliotheek v12 voor Python om:

* Een wachtrij maken
* Berichten toevoegen aan een wachtrij
* Berichten in een wachtrij bekijken
* Een bericht in een wachtrij bijwerken
* Berichten ontvangen vanuit een wachtrij
* Berichten uit een wachtrij verwijderen
* Een wachtrij verwijderen

[API-naslagdocumentatie](https://docs.microsoft.com/python/api/azure-storage-queue/index) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue) | [(Python Package Index)](https://pypi.org/project/azure-storage-queue/) | [Voorbeelden](https://docs.microsoft.com/azure/storage/common/storage-samples-python?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
* Azure-opslagaccount - [een opslagaccount maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Python](https://www.python.org/downloads/) voor uw besturingssysteem - 2,7, 3,5 of hoger

## <a name="setting-up"></a>Instellen

In deze sectie u een project voorbereiden om te werken met de Azure Queue-opslagclientbibliotheek v12 voor Python.

### <a name="create-the-project"></a>Het project maken

Maak een Python-toepassing met de naam *queues-quickstart-v12*.

1. Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor het project.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Schakel over naar de nieuw gemaakte *map snelstart-v12 voor wachtrijen.*

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>Het pakket installeren

Installeer het Azure Blob-opslagclientbibliotheek voor `pip install` Python-pakket met behulp van de opdracht.

```console
pip install azure-storage-queue
```

Met deze opdracht wordt de Azure Queue-opslagclientbibliotheek voor het Python-pakket en alle bibliotheken waarvan deze afhankelijk is geïnstalleerd. In dit geval is dat alleen de Azure-kernbibliotheek voor Python.

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

1. Een nieuw tekstbestand openen in uw codeeditor
1. Instructies `import` toevoegen
1. Maak de structuur voor het programma, inclusief zeer eenvoudige uitzonderingsafhandeling

    Hier is de code:

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

1. Sla het nieuwe bestand op als *queues-quickstart-v12.py* in de map *wachtrijen-quickstart-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objectmodel

Azure Queue Storage is een service om grote aantallen berichten op te slaan. Een wachtrijbericht kan maximaal 64 KB groot zijn. Een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteitslimiet van een opslagaccount. Wachtrijen worden vaak gebruikt om een werkachterstand te maken om asynchroon te verwerken. Wachtrijopslag biedt drie soorten resources:

* Het opslagaccount
* Een wachtrij in het opslagaccount
* Berichten in de wachtrij

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram met wachtrijopslagarchitectuur](./media/storage-queues-introduction/queue1.png)

Gebruik de volgende Python-klassen om met deze bronnen te communiceren:

* [QueueServiceClient:](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient) `QueueServiceClient` Hiermee u alle wachtrijen in uw opslagaccount beheren.
* [QueueClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient): `QueueClient` Met de klasse u een afzonderlijke wachtrij en de bijbehorende berichten beheren en manipuleren.
* [QueueMessage](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage): `QueueMessage` De klasse vertegenwoordigt de afzonderlijke objecten die zijn geretourneerd wanneer [ze receive_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) in een wachtrij aanroepen.

## <a name="code-examples"></a>Codevoorbeelden

In deze voorbeeldcodefragmenten ziet u hoe u de volgende acties uitvoeren met de Azure Queue-opslagclientbibliotheek voor Python:

* [De verbindingsreeks ophalen](#get-the-connection-string)
* [Een wachtrij maken](#create-a-queue)
* [Berichten toevoegen aan een wachtrij](#add-messages-to-a-queue)
* [Berichten in een wachtrij bekijken](#peek-at-messages-in-a-queue)
* [Een bericht in een wachtrij bijwerken](#update-a-message-in-a-queue)
* [Berichten ontvangen vanuit een wachtrij](#receive-messages-from-a-queue)
* [Berichten uit een wachtrij verwijderen](#delete-messages-from-a-queue)
* [Een wachtrij verwijderen](#delete-a-queue)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de verbindingstekenreeks voor het opslagaccount op. De verbindingstekenreeks wordt opgeslagen in de omgevingsvariabele die is gemaakt in de sectie [Uw opslagverbindingstekenreeks configureren.](#configure-your-storage-connection-string)

Voeg deze code `try` toe in het blok:

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

Bepaal een naam voor de nieuwe wachtrij. De onderstaande code voegt een UUID-waarde toe aan de wachtrijnaam om ervoor te zorgen dat deze uniek is.

> [!IMPORTANT]
> Wachtrijnamen mogen alleen kleine letters, cijfers en koppeltekens bevatten en moeten beginnen met een letter of een getal. Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter. De naam moet ook tussen de 3 en 63 tekens lang zijn. Zie [Wachtrijen en metagegevens voor](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata)meer informatie over het benoemen van wachtrijen.

Maak een instantie van de klasse [QueueClient.](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient) Roep vervolgens de [methode create_queue](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) om de wachtrij in uw opslagaccount te maken.

Voeg deze code toe `try` aan het einde van het blok:

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

In het volgende codefragment worden berichten toegevoegd aan de wachtrij door de [methode send_message](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) aan te roepen. Het slaat ook de [QueueMessage](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage) terug van het derde `send_message` gesprek. Het `saved_message` wordt gebruikt om de inhoud van het bericht later in het programma bij te werken.

Voeg deze code toe `try` aan het einde van het blok:

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Berichten in een wachtrij bekijken

Bekijk de berichten in de wachtrij door de [methode peek_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) aan te roepen. De `peek_messages` methode haalt een of meer berichten op aan de voorkant van de wachtrij, maar verandert niets aan de zichtbaarheid van het bericht.

Voeg deze code toe `try` aan het einde van het blok:

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>Een bericht in een wachtrij bijwerken

Werk de inhoud van een bericht bij door de [methode update_message](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) aan te roepen. De `update_message` methode kan de zichtbaarheidstime-out en inhoud van een bericht wijzigen. De inhoud van het bericht moet een utf-8 gecodeerde tekenreeks zijn die maximaal 64 KB groot is. Samen met de nieuwe inhoud geeft u waarden door van het bericht dat eerder in de code is opgeslagen. De `saved_message` waarden bepalen welk bericht moet worden bijgewerkt.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Berichten ontvangen vanuit een wachtrij

Download eerder toegevoegde berichten door de [receive_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) methode aan te roepen.

Voeg deze code toe `try` aan het einde van het blok:

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Berichten uit een wachtrij verwijderen

Verwijder berichten uit de wachtrij nadat ze zijn ontvangen en verwerkt. In dit geval wordt het bericht alleen weergegeven op de console.

De app pauzeert voor `input` gebruikersinvoer door te bellen voordat deze wordt verwerkt en de berichten wordt verwijderd. Controleer in uw [Azure-portal](https://portal.azure.com) of de resources correct zijn gemaakt voordat ze worden verwijderd. Berichten die niet expliciet worden verwijderd, worden uiteindelijk weer zichtbaar in de wachtrij voor een nieuwe kans om ze te verwerken.

Voeg deze code toe `try` aan het einde van het blok:

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

Met de volgende code worden de resources die de app heeft gemaakt door de wachtrij te verwijderen met behulp van de [methode delete_queue.](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-)

Voeg deze code toe `try` aan het einde van het blok en sla het bestand op:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>De code uitvoeren

Deze app maakt en voegt drie berichten toe aan een Azure-wachtrij. De code geeft een lijst van de berichten in de wachtrij en haalt ze op en verwijdert ze, voordat u de wachtrij uiteindelijk verwijdert.

Navigeer in het consolevenster naar de map met het `python` *queues-quickstart-v12.py-bestand* en voer de volgende opdracht uit om de app uit te voeren.

```console
python queues-quickstart-v12.py
```

De uitvoer van de app is vergelijkbaar met het volgende voorbeeld:

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

Wanneer de app wordt onderbroken voordat u berichten ontvangt, controleert u uw opslagaccount in de [Azure-portal.](https://portal.azure.com) Controleer of de berichten in de wachtrij staan.

Druk op **enter** om de berichten te ontvangen en te verwijderen. Druk de sein **enter** opnieuw in om de wachtrij te verwijderen en de demo af te ronden wanneer u daarom wordt gevraagd.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een wachtrij maken en er berichten aan toevoegen met behulp van Python-code. Vervolgens heb je geleerd om te gluren, op te halen en berichten te verwijderen. Ten slotte hebt u geleerd hoe u een berichtenwachtrij verwijderen.

Voor tutorials, voorbeelden, snelle starts en andere documentatie, bezoek:

> [!div class="nextstepaction"]
> [Azure voor Python-ontwikkelaars](https://docs.microsoft.com/azure/python/)

* Zie de Azure [Storage-bibliotheken voor Python voor](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)meer informatie.
* Ga verder naar voorbeeldvoorbeelden van [Azure Queue-opslagv12 Python-clientbibliotheek.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples)
