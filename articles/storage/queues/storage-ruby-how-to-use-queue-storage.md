---
title: Wachtrijopslag gebruiken vanuit Ruby - Azure Storage
description: Meer informatie over het gebruik van de Azure Queue-service om wachtrijen te maken en te verwijderen en berichten in te voegen, op te halen en te verwijderen. Monsters geschreven in Ruby.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: c7211bc805f4ed1d026faedbfdc9d53d3c1dfd93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68721293"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Queue Storage gebruiken met Ruby
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Overzicht
In deze handleiding ziet u hoe u veelvoorkomende scenario's uitvoert met de Microsoft Azure Queue Storage-service. De voorbeelden worden geschreven met behulp van de Ruby Azure API.
De behandelde scenario's omvatten **het invoegen,** **gluren,** **krijgen**en **verwijderen van** wachtrijberichten, evenals het maken en verwijderen **van wachtrijen**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Een Ruby-toepassing maken
Maak een Ruby-toepassing. Zie Een [Ruby App maken in appservice op Linux](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby)voor instructies.

## <a name="configure-your-application-to-access-storage"></a>Uw toepassing configureren voor toegang tot opslag
Als u Azure-opslag wilt gebruiken, moet u het Ruby azure-pakket downloaden en gebruiken, dat een reeks gemaksbibliotheken bevat die communiceren met de REST-opslagservices.

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems gebruiken om het pakket te verkrijgen
1. Gebruik een opdrachtregelinterface zoals **PowerShell** (Windows), **Terminal** (Mac) of **Bash** (Unix).
2. Typ "gem install azure" in het opdrachtvenster om de edelsteen en afhankelijkheden te installeren.

### <a name="import-the-package"></a>Het pakket importeren
Gebruik je favoriete teksteditor en voeg het volgende toe aan de bovenkant van het Ruby-bestand waar je opslag wilt gebruiken:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Een Azure-opslagverbinding instellen
De azure-module leest de omgevingsvariabelen **AZURE\_STORAGE-ACCOUNT\_** en AZURE **\_STORAGE\_ACCESS_KEY** voor informatie die nodig is om verbinding te maken met uw Azure-opslagaccount. Als deze omgevingsvariabelen niet zijn ingesteld, moet u de accountgegevens opgeven voordat **u Azure::QueueService** gebruikt met de volgende code:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

U verkrijgt deze waarden als volgt van een klassiek of Resource Manager-opslagaccount op Azure Portal:

1. Log in bij de [Azure-portal](https://portal.azure.com).
2. Navigeer naar het opslagaccount dat u wilt gebruiken.
3. Klik in de blade Instellingen aan de rechterkant op **Toegangssleutels**.
4. In de blade Toegang die wordt weergegeven, ziet u toegangssleutel 1 en toegangssleutel 2. U kunt een van beide gebruiken. 
5. Klik op het kopieerpictogram om de sleutel naar het klembord kopiëren. 

## <a name="how-to-create-a-queue"></a>How To: Een wachtrij maken
Met de volgende code wordt een **Azure::QueueService-object** gemaakt waarmee u met wachtrijen werken.

```ruby
azure_queue_service = Azure::QueueService.new
```

Gebruik de methode **create_queue()** om een wachtrij met de opgegeven naam te maken.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>How To: Een bericht invoegen in een wachtrij
Als u een bericht in een wachtrij wilt invoegen, gebruikt u de methode **create_message()** om een nieuw bericht te maken en toe te voegen aan de wachtrij.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>How To: Gluren bij het volgende bericht
U het bericht voor in een wachtrij bekijken zonder het uit de wachtrij te verwijderen door de methode **peek\_messages()** aan te roepen. Standaard **gluurt peek\_messages()** naar één bericht. U ook opgeven hoeveel berichten u wilt bekijken.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>How To: Dequeue the Next Message
U een bericht in twee stappen uit een wachtrij verwijderen.

1. Wanneer u **\_lijstberichten aanroept()** krijgt u standaard het volgende bericht in een wachtrij. U ook opgeven hoeveel berichten u wilt ontvangen. De berichten die worden geretourneerd uit **lijstberichten()\_** worden onzichtbaar voor andere codeleesberichten uit deze wachtrij. U passeert in de zichtbaarheid time-out in seconden als parameter.
2. Als u wilt eindigen met het verwijderen van het bericht uit de wachtrij, moet u ook **delete_message bellen()**.

Dit proces in twee stappen van het verwijderen van een bericht zorgt ervoor dat wanneer uw code een bericht niet verwerkt als gevolg van hardware- of softwarefouten, een ander exemplaar van uw code hetzelfde bericht kan ontvangen en het opnieuw kan proberen. Uw codeoproepen **verwijderen\_bericht()** direct nadat het bericht is verwerkt.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>How To: De inhoud van een bericht in de wachtrij wijzigen
U kunt de inhoud van een bericht in de wachtrij wijzigen. De onderstaande code gebruikt de **methode update_message()** om een bericht bij te werken. De methode retourneert een tuple met de popontvangst van het wachtrijbericht en een UTC-datumtijdwaarde die aangeeft wanneer het bericht zichtbaar is in de wachtrij.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>How To: Extra opties voor het dequeuleren van berichten
Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij kunt aanpassen.

1. U een partij van bericht krijgen.
2. U een langere of kortere time-out voor onzichtbaarheid instellen, zodat uw code meer of minder tijd heeft om elk bericht volledig te verwerken.

In het volgende codevoorbeeld wordt de methode **lijstberichten()\_** gebruikt om 15 berichten in één aanroep te ontvangen. Vervolgens wordt elk bericht afgedrukt en verwijderd. De time-out voor onzichtbaarheid wordt ingesteld op vijf minuten voor elk bericht.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>How To: De wachtrijlengte oppakken
U een schatting krijgen van het aantal berichten in de wachtrij. Met de methode **metagegevens\_voor wachtrijgegevens()\_** wordt de wachtrijservice gevraagd om het geschatte aantal berichten en metagegevens over de wachtrij te retourneren.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>How To: Een wachtrij verwijderen
Als u een wachtrij en alle berichten in deze wachtrij wilt verwijderen, roept u de methode **delete\_queue()** aan op het wachtrijobject.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Volgende stappen
Nu u de basisbeginselen van wachtrijopslag hebt geleerd, volgt u deze koppelingen om meer te weten te komen over complexere opslagtaken.

* Ga naar het [Azure Storage Team-blog](https://blogs.msdn.com/b/windowsazurestorage/)
* Ga naar de [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) repository op GitHub

For a comparison between the Azure Queue Service discussed in this article and Azure Service Bus Queues discussed in the [How to use Service Bus Queues](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/) article, see [Azure Queues and Service Bus Queues - Compared and Contrasted](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
