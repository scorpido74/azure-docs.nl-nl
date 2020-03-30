---
title: Aan de slag met wachtrijopslag met Visual Studio (WebJob-projecten)
description: Aan de slag met Azure Queue-opslag in een WebJob-project nadat u verbinding hebt gemaakt met een opslagaccount met behulp van met Visual Studio verbonden services.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ffba203bafaf3837cd2d7fc1a6fd962a6926b186
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298743"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Aan de slag met Azure Queue-opslag en Visual Studio connected services (WebJob-projecten)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Overzicht
In dit artikel wordt beschreven hoe u aan de slag gaat met Azure Queue-opslag in een Visual Studio Azure WebJob-project nadat u een Azure-opslagaccount hebt gemaakt of ernaar hebt verwezen met het dialoogvenster Verbonden services voor toevoegen van Visual **Studio.** Wanneer u een opslagaccount toevoegt aan een WebJob-project met behulp van het dialoogvenster **Verbonden services voor toevoegen van** Visual Studio,worden de juiste Azure Storage NuGet-pakketten geïnstalleerd, worden de juiste .NET-verwijzingen aan het project toegevoegd en worden verbindingstekenreeksen voor het opslagaccount bijgewerkt in het app.config-bestand.  

In dit artikel worden C#-codevoorbeelden weergegeven die laten zien hoe u de Azure WebJobs SDK-versie 1.x gebruiken met de Azure Queue-opslagservice.

Azure Queue Storage is een service voor de opslag van grote aantallen berichten die via HTTP of HTTPS overal vandaan kunnen worden opgevraagd met geverifieerde aanroepen. Een enkel wachtrijbericht mag maximaal 64 KB groot zijn en een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteitslimiet van een opslagaccount. Zie [Aan de slag met Azure Queue Storage met .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) voor meer informatie. Zie [ASP.NET](https://www.asp.net)voor meer informatie over ASP.NET.

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Een functie activeren wanneer een wachtrijbericht wordt ontvangen
Als u een functie wilt schrijven die de WebJobs SDK aanroept wanneer een wachtrijbericht wordt ontvangen, gebruikt u het kenmerk **QueueTrigger.** De kenmerkconstructor neemt een tekenreeksparameter die de naam van de wachtrij opgeeft om te peilen. Als u wilt zien hoe u de naam van de wachtrij dynamisch instelt, raadpleegt u [Configuratieopties instellen.](#how-to-set-configuration-options)

### <a name="string-queue-messages"></a>Berichten in wachtrijwachtrijen
In het volgende voorbeeld bevat de wachtrij een tekenreeksbericht, zodat **QueueTrigger** wordt toegepast op een tekenreeksparameter met de naam **logMessage** die de inhoud van het wachtrijbericht bevat. De functie [schrijft een logboekbericht naar het dashboard](#how-to-write-logs).

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

Naast **tekenreeks**kan de parameter een bytearray, een **CloudQueueMessage-object** of een POCO zijn die u definieert.

### <a name="poco-plain-old-clr-object-queue-messages"></a>PoCO [(Plain Old CLR Object)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)wachtrij berichten
In het volgende voorbeeld bevat het wachtrijbericht JSON voor een **BlobInformation-object** dat een eigenschap **BlobName** bevat. De SDK deserialiseert het object automatisch.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

De SDK gebruikt het [Newtonsoft.Json NuGet-pakket](https://www.nuget.org/packages/Newtonsoft.Json) om berichten te serialiseren en te deserialiseren. Als u wachtrijberichten maakt in een programma dat de WebJobs SDK niet gebruikt, u code schrijven zoals het volgende voorbeeld om een POCO-wachtrijbericht te maken dat de SDK kan ontweken.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

### <a name="async-functions"></a>Async-functies
Met de volgende async-functie [wordt een logboek op het dashboard geschreven.](#how-to-write-logs)

```csharp
public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    await logger.WriteLineAsync(logMessage);
}
```

Async-functies kunnen een [annuleringstoken](https://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken)gebruiken, zoals in het volgende voorbeeld wordt weergegeven, waarbij een blob wordt kopieën. (Zie de sectie [Blobs](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) voor een uitleg van de tijdelijke aanduiding **QueueTrigger.)**

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
    CancellationToken token)
{
    await blobInput.CopyToAsync(blobOutput, 4096, token);
}
```

## <a name="types-the-queuetrigger-attribute-works-with"></a>Hiermee wordt het kenmerk QueueTrigger afgespeeld
U **QueueTrigger** gebruiken met de volgende typen:

* **tekenreeks**
* Een POCO-type geserialiseerd als JSON
* **byte[]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Polling-algoritme
De SDK implementeert een willekeurig exponentieel back-off algoritme om het effect van idle-queue polling op de kosten van opslagtransacties te verminderen.  Wanneer een bericht wordt gevonden, wacht de SDK twee seconden en controleert vervolgens op een ander bericht; wanneer er geen bericht wordt gevonden wacht het ongeveer vier seconden voordat u het opnieuw probeert. Na daaropvolgende mislukte pogingen om een wachtrijbericht te krijgen, blijft de wachttijd toenemen totdat deze de maximale wachttijd bereikt, die standaard één minuut is. [De maximale wachttijd is configureerbaar.](#how-to-set-configuration-options)

## <a name="multiple-instances"></a>Meerdere exemplaren
Als uw web-app op meerdere exemplaren wordt uitgevoerd, wordt op elke machine een continue WebJobs uitgevoerd en wacht elke machine op triggers en probeert ze functies uit te voeren. In sommige scenario's kan dit leiden tot sommige functies die dezelfde gegevens twee keer verwerken, dus functies moeten idempotent zijn (geschreven zodat ze herhaaldelijk met dezelfde invoergegevens worden aangeroepen, geen dubbele resultaten oplevert).  

## <a name="parallel-execution"></a>Parallelle uitvoering
Als u meerdere functies hebt die in verschillende wachtrijen luisteren, belt de SDK ze parallel wanneer berichten tegelijkertijd worden ontvangen.

Hetzelfde geldt wanneer meerdere berichten worden ontvangen voor één wachtrij. Standaard krijgt de SDK een batch van 16 wachtrijberichten tegelijk en voert de functie uit die ze parallel verwerkt. [De batchgrootte is configureerbaar](#how-to-set-configuration-options). Wanneer het aantal dat wordt verwerkt tot de helft van de batchgrootte wordt, krijgt de SDK een andere batch en begint de verwerking van deze berichten. Daarom is het maximum aantal gelijktijdige berichten dat per functie wordt verwerkt anderhalf keer de batchgrootte. Deze limiet is afzonderlijk van toepassing op elke functie met een **kenmerk QueueTrigger.** Als u geen parallelle uitvoering wilt voor berichten die in één wachtrij zijn ontvangen, stelt u de batchgrootte in op 1.

## <a name="get-queue-or-queue-message-metadata"></a>Metagegevens van wachtrij- of wachtrijberichten ontvangen
U de volgende berichteigenschappen krijgen door parameters toe te voegen aan de methodehandtekening:

* **Expiratiedatum datumverschuiving**
* **Invoegtijd van TimeTimeOffset**
* **DateTimeOffset** nextVisibleTime
* **tekenreekswachtrijTrigger** (bevat berichttekst)
* **tekenreeks-id**
* **tekenreeks** popReceipt
* **int** dequeueCount

Als u rechtstreeks met de Azure-opslag-API wilt werken, u ook een **parameter CloudStorageAccount** toevoegen.

In het volgende voorbeeld worden al deze metagegevens naar een INFO-toepassingslogboek geschreven. In het voorbeeld bevatten zowel logMessage als queueTrigger de inhoud van het wachtrijbericht.

```csharp
public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
    DateTimeOffset expirationTime,
    DateTimeOffset insertionTime,
    DateTimeOffset nextVisibleTime,
    string id,
    string popReceipt,
    int dequeueCount,
    string queueTrigger,
    CloudStorageAccount cloudStorageAccount,
    TextWriter logger)
{
    logger.WriteLine(
        "logMessage={0}\n" +
        "expirationTime={1}\ninsertionTime={2}\n" +
        "nextVisibleTime={3}\n" +
        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
        "queue endpoint={7} queueTrigger={8}",
        logMessage, expirationTime,
        insertionTime,
        nextVisibleTime, id,
        popReceipt, dequeueCount,
        cloudStorageAccount.QueueEndpoint,
        queueTrigger);
}
```

Hier is een voorbeeldlogboek geschreven door de voorbeeldcode:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Sierlijke shutdown
Een functie die wordt uitgevoerd in een continue WebJob kan een **Parameter CancellationToken** accepteren waarmee het besturingssysteem de functie kan melden wanneer de WebJob op het punt staat te worden beëindigd. U deze melding gebruiken om ervoor te zorgen dat de functie niet onverwacht wordt beëindigd op een manier die gegevens in een inconsistente status achterlaat.

In het volgende voorbeeld ziet u hoe u controleren op dreigende WebJob-beëindiging in een functie.

```csharp
public static void GracefulShutdownDemo(
            [QueueTrigger("inputqueue")] string inputText,
            TextWriter logger,
            CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(1000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

**Let op:** Het dashboard geeft mogelijk niet de status en uitvoer weer van functies die zijn uitgeschakeld.

Zie [WebJobs Graceful Shutdown](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR)voor meer informatie.   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Een wachtrijbericht maken tijdens het verwerken van een wachtrijbericht
Als u een functie wilt schrijven waarmee een nieuw wachtrijbericht wordt gemaakt, gebruikt u het kenmerk **Wachtrij.** Net als **QueueTrigger**geeft u de naam van de wachtrij door als tekenreeks of u [de wachtrijnaam dynamisch instellen.](#how-to-set-configuration-options)

### <a name="string-queue-messages"></a>Berichten in wachtrijwachtrijen
Met het volgende voorbeeld van niet-asynccode wordt een nieuw wachtrijbericht gemaakt in de wachtrij met de naam 'uitvoerwachtrij' met dezelfde inhoud als het wachtrijbericht dat is ontvangen in de wachtrij met de naam 'invoerwachtrij'. (Voor async-functies gebruikt u **IAsyncCollector\<T->** zoals later in deze sectie wordt weergegeven.)

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-object-queue-messages"></a>PoCO [(Plain Old CLR Object)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)wachtrij berichten
Als u een wachtrijbericht wilt maken dat een POCO bevat in plaats van een tekenreeks, geeft u het POCO-type als uitvoerparameter door aan de constructor van het **kenmerk Wachtrij.**

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

De SDK serialiseert het object automatisch naar JSON. Er wordt altijd een wachtrijbericht gemaakt, zelfs als het object null is.

### <a name="create-multiple-messages-or-in-async-functions"></a>Meerdere berichten of in async-functies maken
Als u meerdere berichten wilt maken, maakt u het parametertype voor de uitvoerwachtrij **ICollector\<T>** of **IAsyncCollector\<T->, **zoals in het volgende voorbeeld wordt weergegeven.

```csharp
public static void CreateQueueMessages(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
    TextWriter logger)
{
    logger.WriteLine("Creating 2 messages in outputqueue");
    outputQueueMessage.Add(queueMessage + "1");
    outputQueueMessage.Add(queueMessage + "2");
}
```

Elk wachtrijbericht wordt onmiddellijk gemaakt wanneer de methode **Toevoegen** wordt aangeroepen.

### <a name="types-that-the-queue-attribute-works-with"></a>Typen waarmee het kenmerk Wachtrij werkt
U het kenmerk **Wachtrij** gebruiken voor de volgende parametertypen:

* **out string** (maakt wachtrijbericht als parameterwaarde niet-null is wanneer de functie eindigt)
* **out byte[]** (werken als **tekenreeks**)
* **out CloudQueueMessage** (werkt als **tekenreeks)**
* **out POCO** (een serializable type, maakt een bericht met een null object als de parameter null is wanneer de functie eindigt)
* **ICollector (ICollector)**
* **IAsyncCollector**
* **CloudQueue** (voor het handmatig handmatig maken van berichten met de Azure Storage API)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>WebJobs SDK-kenmerken gebruiken in de hoofdtekst van een functie
Als u wat werk in uw functie moet doen voordat u een WebJobs SDK-kenmerk zoals **Queue,** **Blob**of **Table**gebruikt, u de **IBinder-interface** gebruiken.

In het volgende voorbeeld wordt een invoerwachtrijbericht weergegeven en wordt een nieuw bericht gemaakt met dezelfde inhoud in een uitvoerwachtrij. De naam van de uitvoerwachtrij wordt ingesteld op code in de hoofdtekst van de functie.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

De **IBinder-interface** kan ook worden gebruikt met de **tabel-** en **blobkenmerken.**

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Blobs en tabellen lezen en schrijven tijdens het verwerken van een wachtrijbericht
Met de kenmerken **Blob** en **Tabel** u blobs en tabellen lezen en schrijven. De voorbeelden in deze sectie zijn van toepassing op blobs. Zie [Azure blob-opslag gebruiken met de WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)voor codevoorbeelden die laten zien hoe u processen activeren wanneer blobs worden gemaakt of bijgewerkt.
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Wachtrijberichten voor tekenreeksen die blobbewerkingen activeren
Voor een wachtrijbericht dat een tekenreeks bevat, is **queueTrigger** een tijdelijke aanduiding die u gebruiken in **de** **parameter Blob-attribuut BlobPath** die de inhoud van het bericht bevat.

In het volgende voorbeeld worden **streamobjecten** gebruikt om blobs te lezen en te schrijven. Het wachtrijbericht is de naam van een blob in de tekstblobscontainer. Een kopie van de blob met "-nieuwe" toegevoegd aan de naam wordt gemaakt in dezelfde container.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

De **Blob** blob-kenmerkconstructor neemt een parameter **blobPath** die de container- en blobnaam opgeeft. Zie [Azure blob-opslag gebruiken met de WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)voor meer informatie over deze tijdelijke aanduiding.

Wanneer het kenmerk een **streamobject** versiert, geeft een andere constructorparameter de **FileAccess-modus** op als lezen, schrijven of lezen/schrijven.

In het volgende voorbeeld wordt een **CloudBlockBlob-object** gebruikt om een blob te verwijderen. Het wachtrijbericht is de naam van de blob.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-object-queue-messages"></a>PoCO [(Plain Old CLR Object)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)wachtrij berichten
Voor een POCO die als JSON is opgeslagen in het wachtrijbericht, u tijdelijke aanduidingen gebruiken die eigenschappen van het object noemen in de **parameter BlobPath** van het kenmerk **Wachtrij.** U ook namen van de metagegevens van wachtrijen als tijdelijke aanduidingen gebruiken. Zie [Metagegevens van wachtrij- of wachtrijberichten ontvangen](#get-queue-or-queue-message-metadata).

In het volgende voorbeeld wordt een blob gekopieerd naar een nieuwe blob met een andere extensie. Het wachtrijbericht is een **blobinformatieobject** met de eigenschappen **BlobName** en **BlobNameWithoutExtension.** De eigenschapsnamen worden gebruikt als tijdelijke **Blob** aanduidingen in het blobpad voor de Blob-kenmerken.

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

De SDK gebruikt het [Newtonsoft.Json NuGet-pakket](https://www.nuget.org/packages/Newtonsoft.Json) om berichten te serialiseren en te deserialiseren. Als u wachtrijberichten maakt in een programma dat de WebJobs SDK niet gebruikt, u code schrijven zoals het volgende voorbeeld om een POCO-wachtrijbericht te maken dat de SDK kan ontweken.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Als u wat werk in uw functie moet doen voordat u een blob aan een object bindt, u het kenmerk in de hoofdtekst van de functie gebruiken, zoals weergegeven in [WebJobs SDK-kenmerken gebruiken in de hoofdtekst van een functie.](#use-webjobs-sdk-attributes-in-the-body-of-a-function)

### <a name="types-you-can-use-the-blob-attribute-with"></a>Typen waarmee u het kenmerk Blob gebruiken
Het kenmerk **Blob** kan worden gebruikt met de volgende typen:

* **Streamen** (lezen of schrijven, opgegeven met de parameter FileAccess constructor)
* **Tekstlezer**
* **TextWriter**
* **tekenreeks** (lees)
* **out string** (write; maakt alleen een blob als de tekenreeksparameter niet-null is wanneer de functie terugkeert)
* POCO (lees)
* out POCO (write; maakt altijd een blob, maakt als null object als POCO parameter null is wanneer de functie terugkeert)
* **CloudBlobStream** (schrijven)
* **ICloudBlob** (lezen of schrijven)
* **CloudBlockBlob** (lezen of schrijven)
* **CloudPageBlob** (lezen of schrijven)

## <a name="how-to-handle-poison-messages"></a>Hoe om te gaan met gifberichten
Berichten waarvan de inhoud ervoor zorgt dat een functie mislukt, worden *gifberichten*genoemd. Wanneer de functie mislukt, wordt het wachtrijbericht niet verwijderd en uiteindelijk weer opgehaald, waardoor de cyclus wordt herhaald. De SDK kan de cyclus automatisch onderbreken na een beperkt aantal iteraties, of u het handmatig doen.

### <a name="automatic-poison-message-handling"></a>Automatische behandeling van gifberichten
De SDK roept tot 5 keer een functie aan om een wachtrijbericht te verwerken. Als de vijfde poging mislukt, wordt het bericht verplaatst naar een gifwachtrij. U zien hoe u het maximum aantal nieuwe pogingen configureert in [Hoe configuratieopties in te stellen.](#how-to-set-configuration-options)

De gifwachtrij heet *{originalqueuename}*-poison. U een functie schrijven om berichten uit de gifwachtrij te verwerken door ze te loggen of een melding te sturen dat handmatige aandacht nodig is.

In het volgende voorbeeld mislukt de functie **CopyBlob** wanneer een wachtrijbericht de naam bevat van een blob die niet bestaat. Wanneer dat gebeurt, wordt het bericht verplaatst van de wachtrij voor copyblobs naar de wachtrij voor copyblobs-gif. De **ProcessPoisonMessage** registreert vervolgens het gifbericht.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}

public static void ProcessPoisonMessage(
    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
{
    logger.WriteLine("Failed to copy blob, name=" + blobName);
}
```

In de volgende afbeelding ziet u de console-uitvoer van deze functies wanneer een gifbericht wordt verwerkt.

![Console-uitvoer voor het verwerken van gifberichten](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Handmatige behandeling van gifberichten
U het aantal keren dat een bericht is opgehaald voor verwerking krijgen door een **intparameter** met de naam **dequeueCount** aan uw functie toe te voegen. U vervolgens het aantal wachtrijen in de functiecode controleren en uw eigen gifberichtverwerken uitvoeren wanneer het getal een drempelwaarde overschrijdt, zoals in het volgende voorbeeld wordt weergegeven.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
    TextWriter logger)
{
    if (dequeueCount > 3)
    {
        logger.WriteLine("Failed to copy blob, name=" + blobName);
    }
    else
    {
        blobInput.CopyTo(blobOutput, 4096);
    }
}
```

## <a name="how-to-set-configuration-options"></a>Configuratieopties instellen
U het type **JobHostConfiguration** gebruiken om de volgende configuratieopties in te stellen:

* Stel de sdk-verbindingstekenreeksen in code in.
* **QueueTrigger-instellingen** configureren, zoals het maximale aantal wachtrijen.
* Wachtrijnamen ophalen uit de configuratie.

### <a name="set-sdk-connection-strings-in-code"></a>SDK-verbindingstekenreeksen instellen in code
Als u de tekenreeksen van de SDK-verbinding in code instelt, u uw eigen namen van de verbindingstekenreeks gebruiken in configuratiebestanden of omgevingsvariabelen, zoals in het volgende voorbeeld wordt weergegeven.

```csharp
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    var _serviceBusConn = ConfigurationManager
        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    config.ServiceBusConnectionString = _serviceBusConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="configure-queuetrigger--settings"></a>QueueTrigger-instellingen configureren
U de volgende instellingen configureren die van toepassing zijn op de verwerking van wachtrijberichten:

* Het maximum aantal wachtrijberichten dat tegelijkertijd wordt opgehaald om parallel uit te voeren (standaard is 16).
* Het maximum aantal nieuwe pogingen voordat een wachtrijbericht naar een gifwachtrij wordt verzonden (standaard is 5).
* De maximale wachttijd voordat u opnieuw wordt polling wanneer een wachtrij leeg is (standaard is 1 minuut).

In het volgende voorbeeld ziet u hoe u deze instellingen configureert:

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Waarden instellen voor WebJobs SDK-constructorparameters in code
Soms wilt u een wachtrijnaam, een blobnaam of -container of een tabelnaam in code opgeven in plaats van deze te hardcoderen. U bijvoorbeeld de wachtrijnaam voor **QueueTrigger** opgeven in een configuratiebestand of omgevingsvariabele.

U dit doen door een **nameresolver-object** door te geven aan het type **JobHostConfiguration.** U omvat speciale tijdelijke aanduidingen omringd door procenten (%) hiermee worden webjobs SDK-kenmerkparameters weergegeven en geeft uw **NameResolver-code** de werkelijke waarden op die moeten worden gebruikt in plaats van deze tijdelijke aanduidingen.

Stel dat u een wachtrij met de naam logqueuetest in de testomgeving en een met de naam logqueueprod in productie wilt gebruiken. In plaats van een harde wachtrijnaam wilt u de naam opgeven van een item in de **appSettings-verzameling** met de werkelijke wachtrijnaam. Als de **appSettings-toets** een logboekwachtrij is, kan uw functie er als volgt uitzien.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

De klasse **NameResolver** kan vervolgens de wachtrijnaam ophalen bij **appInstellingen** zoals in het volgende voorbeeld wordt weergegeven:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

U geeft de klasse **NameResolver** door aan het object **JobHost,** zoals in het volgende voorbeeld wordt weergegeven.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Let op:** Wachtrij-, tabel- en blobnamen worden elke keer opgelost wanneer een functie wordt aangeroepen, maar blobcontainernamen worden alleen opgelost wanneer de toepassing wordt gestart. U de naam van de blobcontainer niet wijzigen terwijl de taak wordt uitgevoerd.

## <a name="how-to-trigger-a-function-manually"></a>Een functie handmatig activeren
Als u een functie handmatig wilt activeren, gebruikt u de methode **Aanroepen** of **CallAsync** op het object **JobHost** en het kenmerk **NoAutomaticTrigger** op de functie, zoals in het volgende voorbeeld wordt weergegeven.

```csharp
public class Program
{
    static void Main(string[] args)
    {
        JobHost host = new JobHost();
        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
    }

    [NoAutomaticTrigger]
    public static void CreateQueueMessage(
        TextWriter logger,
        string value,
        [Queue("outputqueue")] out string message)
    {
        message = value;
        logger.WriteLine("Creating queue message: ", message);
    }
}
```

## <a name="how-to-write-logs"></a>Logboeken schrijven
Het dashboard toont logboeken op twee plaatsen: de pagina voor de WebJob en de pagina voor een bepaalde WebJob-aanroep.

![Logboeken op de pagina WebJob](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Aanroeppagina voor functie-aanroep aan te meldt](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Uitvoer van consolemethoden die u aanroept in een functie of in de methode **Main()** wordt weergegeven op de dashboardpagina voor de WebJob, niet op de pagina voor een bepaalde aanroepmethode. Uitvoer van het TextWriter-object dat u krijgt van een parameter in uw methodehandtekening, wordt weergegeven op de dashboardpagina voor een aanroep methode.

Console-uitvoer kan niet worden gekoppeld aan een bepaalde methode aanroepen omdat de console is single-threaded, terwijl veel taakfuncties kunnen worden uitgevoerd op hetzelfde moment. Daarom biedt de SDK elke functie aanroep zijn eigen unieke log writer object.

Als u [logboeken voor het traceren van toepassingen](../app-service/troubleshoot-dotnet-visual-studio.md#logsoverview)wilt schrijven, gebruikt u **Console.Out** (hiermee worden logboeken gemaakt die zijn gemarkeerd als INFO) en **Console.Error** (hiermee worden logboeken gemaakt die als FOUT zijn gemarkeerd). Een alternatief is het gebruik [van Trace of TraceSource](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), die Verbose, Waarschuwing en Kritieke niveaus biedt in aanvulling op info en fout. Logboeken voor het traceren van toepassingen worden weergegeven in de logboekbestanden van de web-app, Azure-tabellen of Azure-blobs, afhankelijk van hoe u uw Azure-web-app configureert. Zoals het geval is voor alle console-uitvoer, worden de meest recente 100 toepassingslogboeken ook weergegeven op de dashboardpagina voor de WebJob, niet de pagina voor een functieaanroep.

Consoleuitvoer wordt alleen in het dashboard weergegeven als het programma wordt uitgevoerd in een Azure WebJob, niet als het programma lokaal of in een andere omgeving wordt uitgevoerd.

U logboekregistratie uitschakelen door de tekenreeks Dashboardverbinding in te stellen op null. Zie [Configuratieopties instellen](#how-to-set-configuration-options)voor meer informatie.

In het volgende voorbeeld worden verschillende manieren weergegeven om logboeken te schrijven:

```csharp
public static void WriteLog(
    [QueueTrigger("logqueue")] string logMessage,
    TextWriter logger)
{
    Console.WriteLine("Console.Write - " + logMessage);
    Console.Out.WriteLine("Console.Out - " + logMessage);
    Console.Error.WriteLine("Console.Error - " + logMessage);
    logger.WriteLine("TextWriter - " + logMessage);
}
```

In het WebJobs SDK-dashboard wordt de uitvoer van het object **TextWriter** weergegeven wanneer u naar de pagina gaat voor een bepaalde functieaanroep en selecteer **Uitvoer schakelen:**

![Aanroepkoppeling](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Aanroeppagina voor functie-aanroep aan te meldt](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

In het WebJobs SDK-dashboard worden de meest recente 100 regels console-uitvoer weergegeven wanneer u naar de pagina voor de WebJob gaat (niet voor de aanroep van de functie) en selecteer **U-uitvoer schakelen**.

![Uitvoer in- of uitschakelen](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

In een continue WebJob worden toepassingslogboeken weergegeven in /data/jobs/continuous/*{webjobname}*/job_log.txt in het webapp-bestandssysteem.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

In een Azure blob zien de toepassingslogboeken er als volgt uit: 2014-09-26T21:01:13,Informatie,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hallo wereld!, 2014-09-26T21:01:13,Fout,contosoadsnew,491e54, 635473620738373502,0,17404,19,Console.Error - Hallo wereld!, 2014-09-26T21:01:13,Informatie,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hallo wereld!,

En in een Azure-tabel zien de logboeken **Console.Out** en **Console.Error** er als volgt uit:

![Inlogtabel Info](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Inlogtabel voor fout](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Volgende stappen
In dit artikel worden codevoorbeelden weergegeven waarin wordt uitgelegd hoe u veelvoorkomende scenario's voor het werken met Azure-wachtrijen verwerken. Zie [Azure WebJobs-documentatiebronnen](https://go.microsoft.com/fwlink/?linkid=390226)voor meer informatie over het gebruik van Azure WebJobs en de WebJobs SDK.

