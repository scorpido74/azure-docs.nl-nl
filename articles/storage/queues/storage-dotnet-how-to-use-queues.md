---
title: Aan de slag met Azure Queue Storage met .NET-Azure Storage
description: Azure Queues biedt betrouwbare, asynchrone uitwisseling van berichten tussen toepassingsonderdelen. Met Cloud Messaging kunnen onderdelen van uw toepassing onafhankelijk van elkaar worden opgeschaald.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/08/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: devx-track-csharp
ms.openlocfilehash: e8dadc999f3bd26671b5a8ee4da26f051a822a26
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001107"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Aan de slag met Azure Queue Storage met .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Overzicht

Azure Queue Storage biedt uitwisseling van berichten tussen toepassingsonderdelen in de cloud. Bij het ontwerpen van toepassingen voor schalen worden toepassings onderdelen vaak losgekoppeld zodat ze onafhankelijk kunnen worden geschaald. Queue Storage biedt asynchrone berichten tussen toepassings onderdelen, ongeacht of ze worden uitgevoerd in de Cloud, op het bureau blad, op een on-premises server of op een mobiel apparaat. Queue Storage biedt daarnaast ondersteuning voor het beheren van asynchrone taken en het samenstellen van proceswerkstromen.

### <a name="about-this-tutorial"></a>Over deze zelfstudie

Deze zelfstudie laat zien hoe u .NET-code kunt schrijven voor een aantal algemene scenario's die gebruikmaken van Azure Queue Storage. Scenario's die aan bod komen, zijn onder meer het maken en verwijderen van wachtrijen en het toevoegen, lezen en verwijderen van wachtrijberichten.

**Geschatte duur:** 45 minuten

### <a name="prerequisites"></a>Vereisten

- [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
- [Algemene client bibliotheek voor .NET Azure Storage](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
- [Client bibliotheek voor Azure Storage wachtrij voor .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)
- [Azure Configuration Manager voor .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)
- Een [Azure-opslag account](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen

Vervolgens stelt u in Visual Studio uw ontwikkelomgeving in, zodat u de codevoorbeelden in deze handleiding kunt uitproberen.

### <a name="create-a-windows-console-application-project"></a>Een Windows-consoletoepassingsproject maken

Maak in Visual Studio een nieuwe Windows-consoletoepassing. In de volgende stappen ziet u hoe u een consoletoepassing maakt in Visual Studio 2019. De stappen zijn nagenoeg gelijk in andere versies van Visual Studio.

1. Selecteer **bestand**  >  **Nieuw**  >  **project**
2. **Platform**  >  **Vensters** selecteren
3. **Console-app selecteren (.NET Framework)**
4. Selecteer **Volgende**
5. Voer in het veld **project naam** een naam in voor uw toepassing
6. Selecteer **Maken**

Alle code voorbeelden in deze zelf studie kunnen worden toegevoegd aan de methode **Main ()** van het **Program.cs** -bestand van uw console toepassing.

U kunt de Azure Storage-client bibliotheken gebruiken in elk type .NET-toepassing, waaronder een Azure-Cloud service of web-app, en desktop-en mobiele toepassingen. In deze gids gebruiken we een consoletoepassing voor de eenvoud.

### <a name="use-nuget-to-install-the-required-packages"></a>NuGet gebruiken om de vereiste pakketten te installeren

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

U moet verwijzen naar de volgende vier pakketten in uw project om deze zelf studie te volt ooien:

- [Azure core library voor .net](https://www.nuget.org/packages/Azure.Core/): dit pakket biedt gedeelde primitieven, abstracties en helpers voor moderne .net Azure SDK-client bibliotheken.
- [Azure Storage algemene client bibliotheek voor .net](https://www.nuget.org/packages/Azure.Storage.Common/): dit pakket biedt een infra structuur die wordt gedeeld door de andere Azure Storage-client bibliotheken.
- [Azure Storage wachtrij bibliotheek voor .net](https://www.nuget.org/packages/Azure.Storage.Queues/): met dit pakket kunt u werken met de Azure Storage Queue-service voor het opslaan van berichten die toegankelijk zijn voor een client.
- [Configuration Manager-bibliotheek voor .net](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/): dit pakket biedt toegang tot configuratie bestanden voor client toepassingen.

U kunt NuGet gebruiken om deze pakketten te verkrijgen. Volg deze stappen:

1. Klik met de rechter muisknop op uw project in **Solution Explorer**en kies **NuGet-pakketten beheren**.
1. **Bladeren** selecteren
1. Zoek online naar ' Azure. storage. queues ' en selecteer **installeren** om de Storage-client bibliotheek en de afhankelijkheden ervan te installeren. Hiermee worden ook de bibliotheken Azure. storage. common en Azure. core geïnstalleerd. Dit zijn afhankelijkheden van de wachtrij bibliotheek.
1. Zoek online naar ' System.Configuration.ConfigurationManager ' en selecteer **installeren** om de Configuration Manager te installeren.

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

U moet verwijzen naar de volgende drie pakketten in uw project om deze zelf studie te volt ooien:

- [Microsoft Azure Storage algemene client bibliotheek voor .net](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): dit pakket biedt programmatische toegang tot gegevens bronnen in uw opslag account.
- [Microsoft Azure Storage wachtrij bibliotheek voor .net](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/): deze client bibliotheek biedt de mogelijkheid om te werken met de Microsoft Azure Storage Queue-service voor het opslaan van berichten die toegankelijk zijn voor een client.
- [Configuration Manager-bibliotheek van Microsoft Azure voor .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): dit pakket biedt een klasse voor het parseren van een verbindingsreeks in een configuratiebestand, ongeacht waar de toepassing wordt uitgevoerd.

U kunt NuGet gebruiken om deze pakketten te verkrijgen. Volg deze stappen:

1. Klik met de rechter muisknop op uw project in **Solution Explorer**en kies **NuGet-pakketten beheren**.
1. **Bladeren** selecteren
1. Zoek online naar ' micro soft. Azure. storage. Queue ' en selecteer **installeren** om de Storage-client bibliotheek en de afhankelijkheden ervan te installeren. Hiermee wordt ook de bibliotheek micro soft. Azure. storage. common geïnstalleerd. Dit is een afhankelijkheid van de wachtrij bibliotheek.
1. Zoek online naar ' Microsoft.Azure.ConfigurationManager ' en selecteer **installeren** om de Azure-Configuration Manager te installeren.

---

> [!NOTE]
> De pakketten voor Storage-client bibliotheken zijn ook opgenomen in de [Azure SDK voor .net](https://azure.microsoft.com/downloads/). We raden u echter aan om ook de Storage-client bibliotheken van NuGet te installeren, zodat u altijd over de nieuwste versies beschikt.
>
> De ODataLib-afhankelijkheden in de Storage-client bibliotheken voor .NET worden opgelost door de ODataLib-pakketten die beschikbaar zijn op NuGet, niet van WCF Data Services. U kunt de ODataLib-bibliotheken rechtstreeks downloaden of u kunt er via NuGet in uw codeproject naar verwijzen. De specifieke ODataLib-pakketten die worden gebruikt door de opslag-client bibliotheken zijn [OData](https://nuget.org/packages/Microsoft.Data.OData/), [EDM](https://nuget.org/packages/Microsoft.Data.Edm/)en [ruimtelijke](https://nuget.org/packages/System.Spatial/). Hoewel deze bibliotheken worden gebruikt door de Azure Table-opslag klassen, zijn ze vereist afhankelijkheden voor het Program meren met de-client bibliotheken voor opslag.

### <a name="determine-your-target-environment"></a>De doelomgeving bepalen

U kunt de voorbeelden in deze gids in twee omgevingen uitvoeren:

- U kunt de code uitvoeren met een Azure Storage-account in de cloud.
- U kunt uw code uitvoeren op basis van de Azurite-opslag emulator. Azurite is een lokale omgeving die een Azure Storage-account in de Cloud emuleert. Azurite is een gratis optie voor het testen en opsporen van fouten in uw code tijdens het ontwikkelen van uw toepassing. De emulator maakt gebruik van een bekend account en een bekende sleutel. Zie voor meer informatie [de Azurite-emulator gebruiken voor het ontwikkelen en testen van lokale Azure Storage](../common/storage-use-azurite.md).

> [!NOTE]
> Gebruik de opslagemulator als u mogelijke kosten in verband met Azure-opslag wilt vermijden. Als u er echter voor kiest om een Azure-opslagaccount in de cloud te gebruiken, zijn de kosten voor de uitvoering van deze zelfstudie te verwaarlozen.

## <a name="get-your-storage-connection-string"></a>Uw opslag connection string ophalen

De Azure Storage-client bibliotheken voor .NET-ondersteuning met behulp van een opslag connection string voor het configureren van eind punten en referenties voor toegang tot opslag Services. Zie [Toegangssleutels voor een opslagaccount beheren](../common/storage-account-keys-manage.md) voor meer informatie.

### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopieer uw referenties van de Azure Portal

De voorbeeldcode moet de toegang tot uw opslagaccount autoriseren. Om te autoriseren geeft u de toepassing de referenties van uw opslagaccount in de vorm van een verbindingsreeks. Om uw opslagaccountreferenties te zien, doet u het volgende:

1. Navigeer naar [Azure Portal](https://portal.azure.com).
2. Zoek uw opslagaccount.
3. In de sectie **Instellingen** van het overzicht met opslagaccounts selecteert u **Toegangssleutels**. De toegangssleutels van uw account worden weergegeven, evenals de volledige verbindingsreeks voor elke sleutel.
4. Zoek de waarde van de **Verbindingsreeks** onder **key1** en klik op de knop **Kopiëren** om de verbindingsreeks te kopiëren. U gaat in de volgende stap de waarde voor de verbinding toevoegen aan een omgevingsvariabele.

    ![Schermopname waarin een verbindingsreeks vanuit de Azure-portal wordt gekopieerd](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

Zie [Azure Storage-verbindingsreeksen configureren](../common/storage-configure-connection-string.md) voor meer informatie over verbindingsreeksen.

> [!NOTE]
> De sleutel van uw opslagaccount is vergelijkbaar met het hoofdwachtwoord voor uw opslagaccount. Zorg dat de sleutel van uw opslagaccount altijd is beveiligd. Geef deze niet aan andere gebruikers en bewaar of noteer de sleutel op een veilige manier en plaats. Genereer een nieuwe sleutel via de Azure-portal als er mogelijk inbreuk op de sleutel heeft plaatsgevonden.

De beste manier om de opslagverbindingsreeks te onderhouden, is met een configuratiebestand. Als u uw connection string wilt configureren, opent u het *app.config* -bestand vanuit Solution Explorer in Visual Studio. Voeg de inhoud van het element `\<appSettings\>` hieronder toe. Vervang de *verbindings reeks* door de waarde die u hebt gekopieerd uit uw opslag account in de portal:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="connection-string" />
    </appSettings>
</configuration>
```

De configuratie-instelling ziet er ongeveer als volgt uit:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==EndpointSuffix=core.windows.net" />
```

Als u de Azurite-opslag emulator wilt richten, kunt u een snelkoppeling gebruiken die is toegewezen aan de bekende account naam en-sleutel. In dat geval heeft de verbindingsreeks de volgende instelling:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
```

### <a name="add-using-directives"></a>Using-instructies toevoegen

Voeg de volgende `Program.cs`-instructies aan het begin van het bestand `using` toe:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
using System; // Namespace for Console output
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

---

### <a name="create-the-queue-service-client"></a>De Queue-serviceclient maken

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Met de [QueueClient](/dotnet/api/azure.storage.queues.queueclient) -klasse kunt u wacht rijen ophalen die zijn opgeslagen in wachtrij opslag. Hier volgt één manier om de serviceclient te maken:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Met de [CloudQueueClient](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy)-klasse kunt u wachtrijen ophalen die zijn opgeslagen in Queue Storage. Hier volgt één manier om de serviceclient te maken:

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

---

U bent nu klaar om code te schrijven die gegevens leest uit en schrijft naar Queue Storage.

## <a name="create-a-queue"></a>Een wachtrij maken

In dit voor beeld ziet u hoe u een wachtrij maakt:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateQueue":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

---

## <a name="insert-a-message-into-a-queue"></a>Een bericht in een wachtrij invoegen

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Als u een bericht wilt invoegen in een bestaande wachtrij, roept u de [SendMessage](/dotnet/api/azure.storage.queues.queueclient.sendmessage) -methode aan. Een bericht kan een `string` (in UTF-8-indeling) of een `byte` matrix zijn. Met de volgende code wordt een wachtrij gemaakt (als deze niet bestaat) en wordt er een bericht ingevoegd:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Voor het invoegen van een bericht in een bestaande wachtrij maakt u eerst een nieuwe [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy). Daarna roept u de methode [AddMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy) aan. `CloudQueueMessage`U kunt een maken van een `string` (in UTF-8-indeling) of een `byte` matrix. Hier is code die een wachtrij maakt (als deze niet bestaat) en het bericht ' Hallo, wereld ' invoegt:

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();

// Create a message and add it to the queue
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

---

## <a name="peek-at-the-next-message"></a>Bekijken van het volgende bericht

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

U kunt de berichten in de wachtrij bekijken zonder ze uit de wachtrij te verwijderen door de [PeekMessages](/dotnet/api/azure.storage.queues.queueclient.peekmessages) -methode aan te roepen. Als u geen waarde doorgeeft voor de para meter *maxMessages* , is de standaard instelling om één bericht te bekijken.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

U kunt het bericht vooraan in een wachtrij bekijken zonder het uit de wachtrij te verwijderen, door de methode [PeekMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy) aan te roepen.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

---

## <a name="change-the-contents-of-a-queued-message"></a>De inhoud van een bericht in de wachtrij wijzigen

U kunt de inhoud van een bericht in de wachtrij wijzigen. Als het bericht een werktaak vertegenwoordigt, kunt u deze functie gebruiken om de status van de werktaak bij te werken. Met de volgende code wordt het bericht in de wachtrij bijgewerkt met nieuwe inhoud en wordt de time-out voor de zichtbaarheid met 60 seconden verlengd. Hiermee wordt de status van de werkitems die aan het bericht zijn gekoppeld, opgeslagen en krijgt de client een extra minuut om aan het bericht te blijven werken. U kunt deze techniek gebruiken om uit meerdere stappen bestaande werkstromen in berichten in de wachtrij te volgen zonder dat u helemaal opnieuw hoeft te beginnen als een verwerkingsstap vanwege een hardware- of softwarefout is mislukt. Doorgaans houdt u ook het aantal nieuwe pogingen bij en als het bericht meer dan *n* keer opnieuw is geprobeerd, verwijdert u het. Dit biedt bescherming tegen berichten die een toepassingsfout activeren telkens wanneer ze worden verwerkt.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UpdateMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

---

## <a name="de-queue-the-next-message"></a>Het volgende bericht uit de wachtrij verwijderen

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Een bericht uit een wachtrij in twee stappen uit de wachtrij verwijderen. Wanneer u [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages)aanroept, wordt het volgende bericht in een wachtrij weer gegeven. Een bericht dat wordt geretourneerd van is niet `ReceiveMessages` zichtbaar voor andere code die berichten uit deze wachtrij leest. Standaard blijft het bericht onzichtbaar gedurende 30 seconden. Om het bericht definitief uit de wachtrij te verwijderen, moet u ook [DeleteMessage](/dotnet/api/azure.storage.queues.queueclient.deletemessage) aanroepen. Dit proces in twee stappen voor het verwijderen van een bericht zorgt ervoor dat als de code er niet in slaagt een bericht te verwerken vanwege hardware- of softwareproblemen, een ander exemplaar van uw code hetzelfde bericht kan ophalen en het opnieuw kan proberen. Uw code aanroepen `DeleteMessage` direct nadat het bericht is verwerkt.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Met uw code wordt een bericht in twee stappen uit de wachtrij verwijderd. Wanneer u [GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy) aanroept, wordt het volgende bericht in een wachtrij opgehaald. Een bericht dat wordt geretourneerd van is niet `GetMessage` zichtbaar voor andere code die berichten uit deze wachtrij leest. Standaard blijft het bericht onzichtbaar gedurende 30 seconden. Om het bericht definitief uit de wachtrij te verwijderen, moet u ook [DeleteMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy) aanroepen. Dit proces in twee stappen voor het verwijderen van een bericht zorgt ervoor dat als de code er niet in slaagt een bericht te verwerken vanwege hardware- of softwareproblemen, een ander exemplaar van uw code hetzelfde bericht kan ophalen en het opnieuw kan proberen. Uw code aanroepen `DeleteMessage` direct nadat het bericht is verwerkt.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

---

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Het Async-Await-patroon gebruiken met algemene Queue Storage-API's

Dit voorbeeld laat zien hoe u het Async-Await-patroon gebruikt met algemene Queue Storage-API's. In het voorbeeld wordt de asynchrone versie aangeroepen van elk van de opgegeven methoden. Dit ziet u aan het achtervoegsel *Async* van elke methode. Wanneer er een asynchrone methode wordt gebruikt, schort het Async-Await-patroon lokale uitvoering uit totdat de aanroep is voltooid. Dit gedrag stelt de huidige thread in staat andere bewerkingen uit te voeren, zodat knelpunten in de prestaties worden voorkomen en de algehele respons van uw toepassing verbetert. Zie [Async en Await (C# en Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) voor meer informatie over het gebruik van het Async-Await-patroon in .NET.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_AsyncQueue":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

---

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Gebruikmaken van aanvullende opties voor het verwijderen van berichten uit de wachtrij

Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij kunt aanpassen. Ten eerste kunt u berichten batchgewijs (maximaal 32) ophalen. Ten tweede kunt u een langere of kortere time-out voor onzichtbaarheid instellen, zodat uw code meer of minder tijd krijgt voor het volledig verwerken van elk bericht.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

In het volgende code voorbeeld wordt de methode [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages) gebruikt om 20 berichten in één aanroep op te halen. Vervolgens wordt elk bericht met een `foreach` lus verwerkt. De time-out voor onzichtbaarheid wordt ingesteld op vijf minuten voor elk bericht. Houd er rekening mee dat de 5 minuten voor alle berichten tegelijk worden gestart, dus na 5 minuten sinds de aanroep van `ReceiveMessages` , worden alle berichten die niet zijn verwijderd, weer zichtbaar.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

In het volgende codevoorbeeld wordt de methode [GetMessages](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy) gebruikt om 20 berichten in één aanroep op te halen. Vervolgens wordt elk bericht met een `foreach` lus verwerkt. De time-out voor onzichtbaarheid wordt ingesteld op vijf minuten voor elk bericht. Houd er rekening mee dat de 5 minuten voor alle berichten tegelijk worden gestart, dus na 5 minuten sinds de aanroep van `GetMessages` , worden alle berichten die niet zijn verwijderd, weer zichtbaar.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

---

## <a name="get-the-queue-length"></a>Lengte van de wachtrij ophalen

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

U kunt een schatting ophalen van het aantal berichten in de wachtrij. De methode [GetProperties](/dotnet/api/azure.storage.queues.queueclient.getproperties) vraagt de Queue-service om de wachtrij-eigenschappen op te halen, met inbegrip van het aantal berichten. De eigenschap [ApproximateMessagesCount](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount) bevat het geschatte aantal berichten in de wachtrij. Dit getal is niet lager dan het werkelijke aantal berichten in de wachtrij, maar dit kan hoger zijn.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

U kunt een schatting ophalen van het aantal berichten in de wachtrij. De methode [FetchAttributes](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy) vraagt de Queue-service de wachtrij-kenmerken, zoals het aantal berichten, op te halen. De eigenschap [ApproximateMessageCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy) retourneert de laatste waarde die door de `FetchAttributes` methode is opgehaald, zonder de Queue-service aan te roepen.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

---

## <a name="delete-a-queue"></a>Een wachtrij verwijderen

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Als u een wachtrij en alle berichten hierin wilt verwijderen, roept u de methode [Delete](/dotnet/api/azure.storage.queues.queueclient.delete) aan in het wachtrijobject.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DeleteQueue":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Als u een wachtrij en alle berichten hierin wilt verwijderen, roept u de methode [Delete](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy) aan in het wachtrijobject.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

---

## <a name="next-steps"></a>Volgende stappen

Nu u de basisprincipes van Queue Storage hebt geleerd, volgt u deze koppelingen voor meer informatie over complexere opslagtaken.

- Bekijk de naslagdocumentatie over de Queue-service voor meer informatie over beschikbare API's:
  - [Naslaginformatie over de Storage-clientbibliotheek voor .NET](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  - [Naslaginformatie over REST API](https://msdn.microsoft.com/library/azure/dd179355)
- Leer hoe u de code die u schrijft om te werken met Azure Storage, kunt vereenvoudigen met behulp van de [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
- Bekijk meer functiehandleidingen voor informatie over aanvullende mogelijkheden voor het opslaan van gegevens in Azure.
  - [Aan de slag met Azure Table Storage met .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) voor het opslaan van gestructureerde gegevens.
  - [Aan de slag met Azure Blob Storage met .NET](../blobs/storage-dotnet-how-to-use-blobs.md) voor het opslaan van niet-gestructureerde gegevens.
  - [Verbinding maken met SQL Database met behulp van .NET (C#)](../../azure-sql/database/connect-query-dotnet-core.md) voor het opslaan van relationele gegevens.

[Download and install the Azure SDK for .NET]: /develop/net/
[.NET client library reference]: https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Creating an Azure Project in Visual Studio]: https://msdn.microsoft.com/library/azure/ee405487.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[OData]: https://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: https://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: https://nuget.org/packages/System.Spatial/5.0.2
