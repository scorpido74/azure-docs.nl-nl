---
title: Aan de slag met Azure Queue-opslag met .NET - Azure Storage
description: Azure Queues biedt betrouwbare, asynchrone uitwisseling van berichten tussen toepassingsonderdelen. Met Cloud Messaging kunnen onderdelen van uw toepassing onafhankelijk van elkaar worden opgeschaald.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 0806c1101c0bc93a1b917cb2d18709721ff0c6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75968309"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Aan de slag met Azure Queue Storage met .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="overview"></a>Overzicht

Azure Queue Storage biedt uitwisseling van berichten tussen toepassingsonderdelen in de cloud. Bij het ontwerpen van schaalbare toepassingen worden toepassingsonderdelen vaak ontkoppeld, zodat ze onafhankelijk van elkaar kunnen worden geschaald. Queue Storage biedt asynchrone uitwisseling van berichten voor communicatie tussen toepassingsonderdelen, of deze nu worden uitgevoerd in de cloud, op een desktopcomputer, op een on-premises server of op een mobiel apparaat. Queue Storage biedt daarnaast ondersteuning voor het beheren van asynchrone taken en het samenstellen van proceswerkstromen.

### <a name="about-this-tutorial"></a>Over deze zelfstudie

Deze zelfstudie laat zien hoe u .NET-code kunt schrijven voor een aantal algemene scenario's die gebruikmaken van Azure Queue Storage. Scenario's die aan bod komen, zijn onder meer het maken en verwijderen van wachtrijen en het toevoegen, lezen en verwijderen van wachtrijberichten.

**Geschatte duur:** 45 minuten

### <a name="prerequisites"></a>Vereisten

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Algemene clientbibliotheek azure-opslag voor .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
* [Azure Storage Queue-clientbibliotheek voor .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)
* [Azure Configuration Manager voor .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)
* Een [Azure-opslagaccount](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen

Vervolgens stelt u in Visual Studio uw ontwikkelomgeving in, zodat u de codevoorbeelden in deze handleiding kunt uitproberen.

### <a name="create-a-windows-console-application-project"></a>Een Windows-consoletoepassingsproject maken

Maak in Visual Studio een nieuwe Windows-consoletoepassing. In de volgende stappen ziet u hoe u een consoletoepassing maakt in Visual Studio 2019. De stappen zijn nagenoeg gelijk in andere versies van Visual Studio.

1. **Nieuw project bestand** > **New** > **selecteren**
2.  > Platform **Platform****Windows selecteren**
3. Selecteer **Consoletoepassing (.NET Framework)**
4. Selecteer **Volgende**
5. Voer in het veld **Projectnaam** een naam in voor uw toepassing
6. Selecteer **Maken**

Alle codevoorbeelden in deze zelfstudie kunnen worden toegevoegd aan de **methode Main()** van het **Program.cs** bestand van uw consoletoepassing.

U de Azure Storage-clientbibliotheken gebruiken in elk type .NET-toepassing, inclusief een Azure-cloudservice of web-app en desktop- en mobiele toepassingen. In deze gids gebruiken we een consoletoepassing voor de eenvoud.

### <a name="use-nuget-to-install-the-required-packages"></a>NuGet gebruiken om de vereiste pakketten te installeren

U moet naar de volgende drie pakketten in uw project verwijzen om deze zelfstudie te voltooien:

* [Algemene clientbibliotheek voor Microsoft Azure Storage voor .NET:](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)dit pakket biedt programmatische toegang tot gegevensbronnen in uw opslagaccount.
* [Microsoft Azure Storage Queue Library for .NET:](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)This client library enables working with the Microsoft Azure Storage Queue service for storage messages that may be accessed by a client.
* [Configuration Manager-bibliotheek van Microsoft Azure voor .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): dit pakket biedt een klasse voor het parseren van een verbindingsreeks in een configuratiebestand, ongeacht waar de toepassing wordt uitgevoerd.

U NuGet gebruiken om deze pakketten te verkrijgen. Volg deze stappen:

1. Klik met de rechtermuisknop op uw project in **Solution Explorer**en kies **NuGet-pakketten beheren.**
2. Selecteer **Bladeren**
3. Zoek online naar 'Microsoft.Azure.Storage.Queue' en selecteer **Installeren** om de opslagclientbibliotheek en de afhankelijkheden ervan te installeren. Hiermee wordt ook de Microsoft.Azure.Storage.Common-bibliotheek geïnstalleerd, die afhankelijk is van de wachtrijbibliotheek.
4. Zoek online naar 'Microsoft.Azure.ConfigurationManager' en selecteer **Installeren** om Azure Configuration Manager te installeren.

> [!NOTE]
> De pakketten voor opslagclientbibliotheken zijn ook opgenomen in de [Azure SDK voor .NET](https://azure.microsoft.com/downloads/). We raden u echter aan ook de clientbibliotheken voor opslag van NuGet te installeren om ervoor te zorgen dat u altijd over de nieuwste versies beschikt.
>
> De ODataLib-afhankelijkheden in de opslagclientbibliotheken voor .NET worden opgelost door de ODataLib-pakketten die beschikbaar zijn op NuGet, niet van WCF Data Services. U kunt de ODataLib-bibliotheken rechtstreeks downloaden of u kunt er via NuGet in uw codeproject naar verwijzen. De specifieke ODataLib-pakketten die door de clientbibliotheken voor opslag worden gebruikt, zijn [OData](https://nuget.org/packages/Microsoft.Data.OData/), [Edm](https://nuget.org/packages/Microsoft.Data.Edm/)en [Spatial](https://nuget.org/packages/System.Spatial/). Hoewel deze bibliotheken worden gebruikt door de opslagklassen van Azure Table, zijn ze afhankelijkvan het programmeren met de opslagclientbibliotheken.

### <a name="determine-your-target-environment"></a>De doelomgeving bepalen

U kunt de voorbeelden in deze gids in twee omgevingen uitvoeren:

* U kunt de code uitvoeren met een Azure Storage-account in de cloud.
* U kunt de code uitvoeren met de Azure-opslagemulator. De opslagemulator is een lokale omgeving die een Azure Storage-account in de cloud emuleert. De emulator is een gratis optie waarmee u uw code kunt testen en fouten in de code kunt opsporen terwijl de toepassing nog in ontwikkeling is. De emulator maakt gebruik van een bekend account en een bekende sleutel. Zie [De Azure-opslagemulator gebruiken voor ontwikkeling en testen voor](../common/storage-use-emulator.md)meer informatie.

Als u een opslagaccount in de cloud wilt gebruiken, kopieert u de primaire toegangssleutel voor uw opslagaccount vanuit Azure Portal. Zie [Toegangssleutels voor opslagaccount beheren](../common/storage-account-keys-manage.md)voor meer informatie .

> [!NOTE]
> Gebruik de opslagemulator als u mogelijke kosten in verband met Azure-opslag wilt vermijden. Als u er echter voor kiest om een Azure-opslagaccount in de cloud te gebruiken, zijn de kosten voor de uitvoering van deze zelfstudie te verwaarlozen.

### <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren

De Azure Storage-clientbibliotheken voor .NET-ondersteuning met behulp van een tekenreeks voor opslagverbindingen om eindpunten en referenties te configureren voor toegang tot opslagservices. De beste manier om de opslagverbindingsreeks te onderhouden, is met een configuratiebestand.

Zie [Azure Storage-verbindingsreeksen configureren](../common/storage-configure-connection-string.md) voor meer informatie over verbindingsreeksen.

> [!NOTE]
> De sleutel van uw opslagaccount is vergelijkbaar met het hoofdwachtwoord voor uw opslagaccount. Zorg dat de sleutel van uw opslagaccount altijd is beveiligd. Geef deze niet aan andere gebruikers en bewaar of noteer de sleutel op een veilige manier en plaats. Genereer een nieuwe sleutel via de Azure-portal als er mogelijk inbreuk op de sleutel heeft plaatsgevonden.

Als u uw verbindingstekenreeks wilt configureren, opent u het bestand **app.config** vanuit Solution Explorer in Visual Studio. Voeg de inhoud ** \<\> ** van het element appInstellingen hieronder toe. Vervang *de accountnaam* door de naam van uw opslagaccount en *accountsleutel* door uw accounttoegangssleutel:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

De configuratie-instelling ziet er ongeveer als volgt uit:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Als u de opslagemulator wilt gebruiken, kunt u de bekende accountnaam en -sleutel op een snelle manier toewijzen. In dat geval heeft de verbindingsreeks de volgende instelling:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Using-instructies toevoegen

Voeg de volgende `Program.cs`-instructies aan het begin van het bestand `using` toe:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopieer uw referenties van de Azure Portal

De voorbeeldcode moet de toegang tot uw opslagaccount autoriseren. Om te autoriseren geeft u de toepassing de referenties van uw opslagaccount in de vorm van een verbindingsreeks. Om uw opslagaccountreferenties te zien, doet u het volgende:

1. Navigeer naar de [Azure-portal](https://portal.azure.com).
2. Zoek uw opslagaccount.
3. In de sectie **Instellingen** van het overzicht met opslagaccounts selecteert u **Toegangssleutels**. De toegangssleutels van uw account worden weergegeven, evenals de volledige verbindingsreeks voor elke sleutel.
4. Zoek de waarde van de **Verbindingsreeks** onder **key1** en klik op de knop **Kopiëren** om de verbindingsreeks te kopiëren. U gaat in de volgende stap de waarde voor de verbinding toevoegen aan een omgevingsvariabele.

    ![Schermopname waarin een verbindingsreeks vanuit de Azure-portal wordt gekopieerd](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

### <a name="parse-the-connection-string"></a>De verbindingsreeks parseren

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>De Queue-serviceclient maken

Met de [CloudQueueClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet)-klasse kunt u wachtrijen ophalen die zijn opgeslagen in Queue Storage. Hier volgt één manier om de serviceclient te maken:

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

U bent nu klaar om code te schrijven die gegevens leest uit en schrijft naar Queue Storage.

## <a name="create-a-queue"></a>Een wachtrij maken

In dit voorbeeld ziet u hoe u een wachtrij kunt maken als deze nog niet bestaat:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a container.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

## <a name="insert-a-message-into-a-queue"></a>Een bericht in een wachtrij invoegen

Voor het invoegen van een bericht in een bestaande wachtrij maakt u eerst een nieuwe [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet). Daarna roept u de methode [AddMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet) aan. Een **CloudQueueMessage** kan worden gemaakt vanuit een tekenreeks (in UTF-8-indeling) of een **byte**matrix. Met deze code wordt er een wachtrij gemaakt (als deze nog niet bestaat) en het bericht 'Hello, World' toegevoegd:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist.
queue.CreateIfNotExists();

// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

## <a name="peek-at-the-next-message"></a>Bekijken van het volgende bericht

U kunt het bericht vooraan in een wachtrij bekijken zonder het uit de wachtrij te verwijderen, door de methode [PeekMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet) aan te roepen.

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

## <a name="change-the-contents-of-a-queued-message"></a>De inhoud van een bericht in de wachtrij wijzigen

U kunt de inhoud van een bericht in de wachtrij wijzigen. Als het bericht een werktaak vertegenwoordigt, kunt u deze functie gebruiken om de status van de werktaak bij te werken. Met de volgende code wordt het bericht in de wachtrij bijgewerkt met nieuwe inhoud en wordt de time-out voor de zichtbaarheid met 60 seconden verlengd. Hiermee wordt de status van de werkitems die aan het bericht zijn gekoppeld, opgeslagen en krijgt de client een extra minuut om aan het bericht te blijven werken. U kunt deze techniek gebruiken om uit meerdere stappen bestaande werkstromen in berichten in de wachtrij te volgen zonder dat u helemaal opnieuw hoeft te beginnen als een verwerkingsstap vanwege een hardware- of softwarefout is mislukt. Doorgaans houdt u ook het aantal nieuwe pogingen bij en als het bericht meer dan *n* keer opnieuw is geprobeerd, verwijdert u het. Dit biedt bescherming tegen berichten die een toepassingsfout activeren telkens wanneer ze worden verwerkt.

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

## <a name="de-queue-the-next-message"></a>Het volgende bericht uit de wachtrij verwijderen

Met uw code wordt een bericht in twee stappen uit de wachtrij verwijderd. Wanneer u [GetMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet) aanroept, wordt het volgende bericht in een wachtrij opgehaald. Een bericht dat wordt geretourneerd door **GetMessage**, wordt onzichtbaar voor andere codes die berichten lezen uit deze wachtrij. Standaard blijft het bericht onzichtbaar gedurende 30 seconden. Om het bericht definitief uit de wachtrij te verwijderen, moet u ook [DeleteMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet) aanroepen. Dit proces in twee stappen voor het verwijderen van een bericht zorgt ervoor dat als de code er niet in slaagt een bericht te verwerken vanwege hardware- of softwareproblemen, een ander exemplaar van uw code hetzelfde bericht kan ophalen en het opnieuw kan proberen. Uw code haalt **DeleteMessage** op zodra het bericht is verwerkt.

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

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Het Async-Await-patroon gebruiken met algemene Queue Storage-API's

Dit voorbeeld laat zien hoe u het Async-Await-patroon gebruikt met algemene Queue Storage-API's. In het voorbeeld wordt de asynchrone versie aangeroepen van elk van de opgegeven methoden. Dit ziet u aan het achtervoegsel *Async* van elke methode. Wanneer er een asynchrone methode wordt gebruikt, schort het Async-Await-patroon lokale uitvoering uit totdat de aanroep is voltooid. Dit gedrag stelt de huidige thread in staat andere bewerkingen uit te voeren, zodat knelpunten in de prestaties worden voorkomen en de algehele respons van uw toepassing verbetert. Zie [Async en Await (C# en Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) voor meer informatie over het gebruik van het Async-Await-patroon in .NET.

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

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Gebruikmaken van aanvullende opties voor het verwijderen van berichten uit de wachtrij

Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij kunt aanpassen. Ten eerste kunt u berichten batchgewijs (maximaal 32) ophalen. Ten tweede kunt u een langere of kortere time-out voor onzichtbaarheid instellen, zodat uw code meer of minder tijd krijgt voor het volledig verwerken van elk bericht. In het volgende codevoorbeeld wordt de methode [GetMessages](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet) gebruikt om 20 berichten in één aanroep op te halen. Vervolgens wordt elk bericht verwerkt met behulp van een **foreach**-lus. De time-out voor onzichtbaarheid wordt ingesteld op vijf minuten voor elk bericht. Houd voor ogen dat de periode van 5 minuten voor alle berichten op hetzelfde moment start. Nadat er 5 minuten zijn verstreken sinds de aanroep van **GetMessages**, worden dus alle berichten die niet zijn verwijderd, opnieuw zichtbaar.

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

## <a name="get-the-queue-length"></a>Lengte van de wachtrij ophalen

U kunt een schatting ophalen van het aantal berichten in de wachtrij. De methode [FetchAttributes](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet) vraagt de Queue-service de wachtrij-kenmerken, zoals het aantal berichten, op te halen. De eigenschap [ApproximateMessageCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet) retourneert de laatste waarde die is opgehaald door de methode **FetchAttributes**, zonder de Queue-service aan te roepen.

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
int? cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="delete-a-queue"></a>Een wachtrij verwijderen

Als u een wachtrij en alle berichten hierin wilt verwijderen, roept u de methode [Delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet) aan in het wachtrijobject.

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

## <a name="next-steps"></a>Volgende stappen

Nu u de basisprincipes van Queue Storage hebt geleerd, volgt u deze koppelingen voor meer informatie over complexere opslagtaken.

* Bekijk de naslagdocumentatie over de Queue-service voor meer informatie over beschikbare API's:
  * [Naslaginformatie over de Storage-clientbibliotheek voor .NET](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  * [VERWIJZING NAAR REST-API](https://msdn.microsoft.com/library/azure/dd179355)
* Leer hoe u de code die u schrijft om te werken met Azure Storage, kunt vereenvoudigen met behulp van de [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
* Bekijk meer functiehandleidingen voor informatie over aanvullende mogelijkheden voor het opslaan van gegevens in Azure.
  * [Aan de slag met Azure Table Storage met .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) voor het opslaan van gestructureerde gegevens.
  * [Aan de slag met Azure Blob Storage met .NET](../blobs/storage-dotnet-how-to-use-blobs.md) voor het opslaan van niet-gestructureerde gegevens.
  * [Verbinding maken met SQL Database met behulp van .NET (C#)](../../sql-database/sql-database-connect-query-dotnet-core.md) voor het opslaan van relationele gegevens.

[Download and install the Azure SDK for .NET]: /develop/net/
[.NET client library reference]: https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Creating an Azure Project in Visual Studio]: https://msdn.microsoft.com/library/azure/ee405487.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[OData]: https://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: https://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: https://nuget.org/packages/System.Spatial/5.0.2
