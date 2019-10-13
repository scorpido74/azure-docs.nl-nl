---
title: Aan de slag met Blob Storage met Visual Studio (project taak)
description: Aan de slag met het gebruik van Blob Storage in een Webtaak-project nadat verbinding is gemaakt met een Azure-opslag met behulp van Visual Studio Connected Services.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 90aa824b7df575eb2783ece5bd88322f0b55f0a2
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299973"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Aan de slag met Azure Blob Storage en Visual Studio Connected Services (project taak)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Overzicht
In dit artikel C# vindt u voor beelden van code die laten zien hoe u een proces kunt activeren wanneer een Azure-Blob wordt gemaakt of bijgewerkt. De code voorbeelden gebruiken de [Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) versie 1. x. Wanneer u een opslag account toevoegt aan een project taak met behulp van het dialoog venster **verbonden services toevoegen** van Visual Studio, wordt het juiste Azure Storage NuGet-pakket geïnstalleerd, worden de relevante .net-verwijzingen toegevoegd aan het project en worden verbindings reeksen voor de het opslag account wordt bijgewerkt in het bestand app. config.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Een functie activeren wanneer een BLOB wordt gemaakt of bijgewerkt
In deze sectie wordt uitgelegd hoe u het kenmerk **sjabloon blobtrigger** gebruikt.

 **Opmerking:** Met de webjobs SDK worden logboek bestanden gescand om te bekijken of er nieuwe of gewijzigde blobs zijn. Dit proces is inherent traag; een functie wordt mogelijk pas na een paar minuten of langer geactiveerd nadat de blob is gemaakt.  Als uw toepassing blobs onmiddellijk moet verwerken, is de aanbevolen methode om een wachtrij bericht te maken wanneer u de BLOB maakt en het kenmerk **Queue trigger** in plaats van het kenmerk **sjabloon blobtrigger** te gebruiken voor de functie die de BLOB verwerkt .

### <a name="single-placeholder-for-blob-name-with-extension"></a>Enkele tijdelijke aanduiding voor de BLOB-naam met de extensie
In het volgende code voorbeeld wordt tekst-blobs die in de *invoer* container worden weer gegeven, gekopieerd naar de *uitvoer* container:

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

De kenmerk-constructor neemt een teken reeks parameter op die de container naam en een tijdelijke aanduiding voor de naam van de BLOB opgeeft. Als in dit voor beeld een blob met de naam *Blob1. txt* wordt gemaakt in de *invoer* container, maakt de functie een blob met de naam *Blob1. txt* in de *uitvoer* container.

U kunt een naam patroon opgeven met de tijdelijke aanduiding voor de BLOB-naam, zoals wordt weer gegeven in het volgende code voorbeeld:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Met deze code worden alleen blobs gekopieerd met namen die beginnen met ' origineel-'. *Original-Blob1. txt* in de *invoer* container wordt bijvoorbeeld gekopieerd naar *copy-Blob1. txt* in de *uitvoer* container.

Als u een naam patroon wilt opgeven voor BLOB-namen met accolades in de naam, verdubbelt u de accolades. Als u bijvoorbeeld blobs in de container *installatie kopieën* wilt zoeken die namen als volgt hebben:

        {20140101}-soundfile.mp3

Gebruik deze voor uw patroon:

        images/{{20140101}}-{name}

In het voor beeld is de *naam* van de tijdelijke aanduiding *soundfile. mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Afzonderlijke aanduidingen voor de BLOB-naam en-extensie
In het volgende code voorbeeld wordt de bestands extensie gewijzigd, omdat de blobs die in de *invoer* container worden weer gegeven, worden gekopieerd naar de *uitvoer* container. Met de code wordt de extensie van de *invoer* -BLOB vastgelegd en wordt de extensie van de *uitvoer* -BLOB ingesteld op *. txt*.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a name="types-that-you-can-bind-to-blobs"></a>Typen die u aan blobs kunt binden
U kunt het kenmerk **sjabloon blobtrigger** gebruiken voor de volgende typen:

* **tekenreeksexpressie**
* **TextReader**
* **Bitsnelheid**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Andere typen die worden gedeserialiseerd door [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Als u rechtstreeks met het Azure-opslag account wilt werken, kunt u ook een **Cloud Storage account** -para meter toevoegen aan de methode handtekening.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Tekst blob-inhoud ophalen door te binden aan de teken reeks
Als er tekst-blobs worden verwacht, kan **sjabloon blobtrigger** worden toegepast op een **teken reeks** parameter. Het volgende code voorbeeld bindt een tekst-blob aan een **teken reeks** parameter met de naam **logMessage**. De functie gebruikt die para meter om de inhoud van de BLOB naar het SDK-dash board van webjobs te schrijven.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Geserialiseerde blob-inhoud ophalen met behulp van ICloudBlobStreamBinder
In het volgende code voorbeeld wordt een klasse gebruikt die **ICloudBlobStreamBinder** implementeert om het kenmerk **sjabloon blobtrigger** in te scha kelen om een BLOB te binden aan het type **webimage** .

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK",
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

De **Webafbeeldings** binding code wordt gegeven in een **WebImageBinder** -klasse die is afgeleid van **ICloudBlobStreamBinder**.

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input,
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="how-to-handle-poison-blobs"></a>Poison-blobs verwerken
Wanneer een **sjabloon blobtrigger** -functie mislukt, wordt deze opnieuw aangeroepen wanneer de fout is veroorzaakt door een tijdelijke fout. Als de fout wordt veroorzaakt door de inhoud van de blob, mislukt de functie elke keer dat er wordt geprobeerd om de BLOB te verwerken. Standaard roept de SDK een functie op tot vijf keer voor een bepaalde blob. Als de vijfde poging mislukt, voegt de SDK een bericht toe aan een wachtrij met de naam *webjobs-sjabloon blobtrigger-Poison*.

Het maximum aantal nieuwe pogingen kan worden geconfigureerd. Dezelfde **MaxDequeueCount** -instelling wordt gebruikt voor de verwerking van verontreinigde BLOB-verwerking en verontreinigde wachtrij berichten.

Het wachtrij bericht voor verontreinigde blobs is een JSON-object dat de volgende eigenschappen bevat:

* FunctionId (in de indeling *{naam van Webtaak}* ). Vervullen. *{Functie naam}* , bijvoorbeeld: WebJob1. functions. CopyBlob)
* BlobType ("BlockBlob" of "PageBlob")
* ContainerName
* BlobName
* ETag (een BLOB-versie-id, bijvoorbeeld: "0x8D1DC6E70A277EF")

In het volgende code voorbeeld bevat de functie **CopyBlob** code die ervoor zorgt dat het mislukt elke keer dat deze wordt aangeroepen. Nadat de SDK het heeft aangeroepen voor het maximum aantal nieuwe pogingen, wordt er een bericht gemaakt in de BLOB-wachtrij voor verontreinigde berichten. dit bericht wordt verwerkt door de functie **LogPoisonBlob** .

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

De SDK deserialiseren automatisch het JSON-bericht. Dit is de **PoisonBlobMessage** -klasse:

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algoritme voor BLOB-polling
De webjobs SDK scant alle containers die zijn opgegeven door **sjabloon blobtrigger** -kenmerken bij het starten van de toepassing. In een groot opslag account kan deze scan enige tijd duren, dus het kan een tijdje zijn voordat er nieuwe blobs worden gevonden en **sjabloon blobtrigger** -functies worden uitgevoerd.

Om nieuwe of gewijzigde blobs te detecteren nadat de toepassing is gestart, leest de SDK regel matig de logboeken van de Blob-opslag. De BLOB-logboeken worden gebufferd en worden alleen om de 10 minuten fysiek geschreven, waardoor er een aanzienlijke vertraging kan optreden nadat een blob is gemaakt of bijgewerkt voordat de bijbehorende functie **sjabloon blobtrigger** wordt uitgevoerd.

Er is een uitzonde ring voor blobs die u maakt met behulp van het kenmerk **BLOB** . Wanneer de webjobs SDK een nieuwe BLOB maakt, wordt de nieuwe BLOB onmiddellijk door gegeven aan alle overeenkomende **sjabloon blobtrigger** -functies. Als u een keten van BLOB-invoer en-uitvoer hebt, kan de SDK deze daarom efficiënt verwerken. Maar als u wilt dat uw BLOB-verwerkings functies worden uitgevoerd voor blobs die op een andere manier worden gemaakt of bijgewerkt, raden we u aan **Queue trigger** te gebruiken in plaats van **sjabloon blobtrigger**.

### <a name="blob-receipts"></a>BLOB-ontvangst bewijzen
De webjobs SDK zorgt ervoor dat er geen functie **sjabloon blobtrigger** meer dan één keer wordt aangeroepen voor dezelfde nieuwe of bijgewerkte blob. Dit doet u door *BLOB-ontvangst* te onderhouden om te bepalen of een bepaalde BLOB-versie is verwerkt.

BLOB-ontvangstsen worden opgeslagen in een container met de naam *Azure-webjobs-hosts* in het Azure Storage-account dat is opgegeven door de AzureWebJobsStorage-Connection String. Een BLOB-ontvangst heeft de volgende informatie:

* De functie die is aangeroepen voor de BLOB ( *{naam van Webtaak}* ). Vervullen. *{Function name}* , bijvoorbeeld: "WebJob1. functions. CopyBlob")
* De container naam
* Het BLOB-type ("BlockBlob" of "PageBlob")
* De BLOB-naam
* De ETag (een BLOB-versie-id, bijvoorbeeld: "0x8D1DC6E70A277EF")

Als u het opnieuw verwerken van een BLOB wilt forceren, kunt u de BLOB-ontvangst voor die BLOB hand matig verwijderen uit de container *Azure-webjobs-hosts* .

## <a name="related-topics-covered-by-the-queues-article"></a>Verwante onderwerpen die worden behandeld in het artikel wacht rijen
Zie [Azure Queue Storage gebruiken met de Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)voor meer informatie over het verwerken van BLOB-verwerking die wordt geactiveerd door een wachtrij bericht of voor webjobs SDK-scenario's die niet specifiek zijn voor BLOB-verwerking.

Verwante onderwerpen in dit artikel zijn onder andere:

* Async-functies
* Meerdere exemplaren
* Correct afsluiten
* De SDK-kenmerken van webjobs in de hoofd tekst van een functie gebruiken
* De SDK-verbindings reeksen instellen in de code.
* Waarden voor de para meters van de SDK voor webjobs in code instellen
* Configureer **MaxDequeueCount** voor de verwerking van verontreinigde blobs.
* Een functie hand matig activeren
* Logboeken schrijven

## <a name="next-steps"></a>Volgende stappen
In dit artikel vindt u code voorbeelden die laten zien hoe u veelvoorkomende scenario's kunt afhandelen voor het werken met Azure-blobs. Zie [Azure WebJobs documentation resources](https://go.microsoft.com/fwlink/?linkid=390226)(Engelstalig) voor meer informatie over het gebruik van Azure WebJobs en de webjobs SDK.

