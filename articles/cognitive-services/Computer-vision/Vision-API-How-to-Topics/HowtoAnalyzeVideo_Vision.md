---
title: Video's in bijna realtime analyseren - Computer Vision
titleSuffix: Azure Cognitive Services
description: Meer informatie over het uitvoeren van bijna realtime analyses op frames die zijn afkomstig van een live videostream met behulp van de Computer Vision API.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 18b158b7a4881619b93ab404de67f7bb25f92b6a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166836"
---
# <a name="analyze-videos-in-near-real-time"></a>Video's in bijna realtime analyseren

Dit artikel laat zien hoe je bijna realtime analyses uitvoert op frames die afkomstig zijn van een live videostream met behulp van de Computer Vision API. De basiselementen van een dergelijke analyse zijn:

- Frames verkrijgen van een videobron.
- Selecteren welke frames u wilt analyseren.
- Deze frames verzenden naar de API.
- Het consumeren van elk analyseresultaat dat wordt geretourneerd uit de API-aanroep.

De voorbeelden in dit artikel zijn geschreven in C#. Als u toegang wilt krijgen tot de code, gaat u naar de [voorbeeldpagina videoframeanalyse](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) op GitHub.

## <a name="approaches-to-running-near-real-time-analysis"></a>Benaderingen van het uitvoeren van bijna real-time analyse

U het probleem oplossen van het uitvoeren van bijna realtime analyse op videostreams met behulp van verschillende benaderingen. Dit artikel schetst drie van hen, in toenemende mate van verfijning.

### <a name="design-an-infinite-loop"></a>Een oneindige lus ontwerpen

Het eenvoudigste ontwerp voor bijna real-time analyse is een oneindige lus. In elke iteratie van deze lus pak je een frame, analyseer je het en verbruikt je het resultaat:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

Als uw analyse zou bestaan uit een lichtgewicht, client-side algoritme, zou deze aanpak geschikt zijn. Wanneer de analyse echter plaatsvindt in de cloud, betekent de resulterende latentie dat een API-aanroep enkele seconden kan duren. Gedurende deze tijd, je bent niet het vastleggen van beelden, en je draad is in wezen niets te doen. Uw maximale framesnelheid wordt beperkt door de latentie van de API-aanroepen.

### <a name="allow-the-api-calls-to-run-in-parallel"></a>Toestaan dat de API-aanroepen parallel worden uitgevoerd

Hoewel een eenvoudige, single-threaded lus zinvol is voor een lichtgewicht algoritme aan de clientzijde, past deze niet goed bij de latentie van een cloud-API-aanroep. De oplossing voor dit probleem is om de langlopende API-aanroep parallel met het framegrijpen te laten lopen. In C#, u dit doen door op taken gebaseerde parallellisme te gebruiken. U bijvoorbeeld de volgende code uitvoeren:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () =>
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

Met deze aanpak start u elke analyse in een aparte taak. De taak kan op de achtergrond worden uitgevoerd terwijl u doorgaat met het grijpen van nieuwe frames. De aanpak voorkomt het blokkeren van de hoofdthread als u wacht tot een API-aanroep terugkeert. De aanpak kan echter bepaalde nadelen met zich meebrengen:
* Het kost u een aantal van de garanties dat de eenvoudige versie verstrekt. Dat wil zeggen dat meerdere API-aanroepen parallel kunnen plaatsvinden en de resultaten in de verkeerde volgorde kunnen worden geretourneerd. 
* Het kan er ook voor zorgen dat meerdere threads tegelijkertijd de functie ConsumeResult() invoeren, wat gevaarlijk kan zijn als de functie niet thread-safe is. 
* Ten slotte houdt deze eenvoudige code niet bij welke taken er worden gemaakt, zodat uitzonderingen in stilte verdwijnen. U moet dus een thread van "consument" toevoegen die de analysetaken bijhoudt, uitzonderingen opwerpt, langlopende taken doodt en ervoor zorgt dat de resultaten één voor één in de juiste volgorde worden verbruikt.

### <a name="design-a-producer-consumer-system"></a>Een producenten-consumentensysteem ontwerpen

Voor uw uiteindelijke aanpak, het ontwerpen van een "producent-consument" systeem, bouwt u een producent draad die lijkt op uw eerder genoemde oneindige lus. Echter, in plaats van het consumeren van de analyse resultaten zodra ze beschikbaar zijn, de producent plaatst gewoon de taken in een wachtrij om bij te houden van hen.

```csharp
// Queue that will contain the API call tasks.
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();

// Producer thread.
while (true)
{
    // Grab a frame.
    Frame f = GrabFrame();

    // Decide whether to analyze the frame.
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread.
        var analysisTask = Task.Run(async () =>
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }

        // Push the task onto the queue.
        taskQueue.Add(analysisTask);
    }
}
```

U maakt ook een consumententhread, die taken uit de wachtrij haalt, wacht tot ze zijn voltooid en het resultaat weergeeft of de uitzondering die is gegenereerd, verhoogt. Door de wachtrij te gebruiken, u garanderen dat de resultaten één voor één worden verbruikt, in de juiste volgorde, zonder de maximale framesnelheid van het systeem te beperken.

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Wait until the task is completed.
    var output = await analysisTask;

    // Consume the exception or result.
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implement-the-solution"></a>De oplossing implementeren

### <a name="get-started-quickly"></a>Snel aan de slag

Om uw app zo snel mogelijk aan de praat te krijgen, hebben we het systeem geïmplementeerd dat in de vorige sectie is beschreven. Het is bedoeld om flexibel genoeg te zijn om aan vele scenario's tegemoet te komen, terwijl het gemakkelijk te gebruiken is. Als u toegang wilt krijgen tot de code, gaat u naar de [voorbeeldpagina videoframeanalyse](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) op GitHub.

De bibliotheek `FrameGrabber` bevat de klasse, die het eerder besproken producent-consumentensysteem implementeert om videoframes van een webcam te verwerken. Gebruikers kunnen de exacte vorm van de API-aanroep opgeven en de klasse gebruikt gebeurtenissen om de aanroepende code te laten weten wanneer een nieuw frame is aangeschaft of wanneer een nieuw analyseresultaat beschikbaar is.

Om een aantal van de mogelijkheden te illustreren, hebben we twee voorbeeld-apps geleverd die de bibliotheek gebruiken. 

De eerste voorbeeld-app is een eenvoudige console-app die frames van de standaardwebcam grijpt en deze vervolgens naar de Face-service verzendt voor gezichtsherkenning. Een vereenvoudigde versie van de app wordt weergegeven in de volgende code:

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
using VideoFrameAnalyzer;

namespace BasicConsoleSample
{
    internal class Program
    {
        const string ApiKey = "<your API key>";
        const string Endpoint = "https://<your API region>.api.cognitive.microsoft.com";

        private static async Task Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up a Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face service.
                return (await faceClient.Face.DetectWithStreamAsync(frame.Image.ToMemoryStream(".jpg"))).ToArray();
            };

            // Set up a listener for when we receive a new result from an API call.
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.TimedOut)
                    Console.WriteLine("API call timed out.");
                else if (e.Exception != null)
                    Console.WriteLine("API call threw an exception.");
                else
                    Console.WriteLine($"New result received for frame acquired at {e.Frame.Metadata.Timestamp}. {e.Analysis.Length} faces detected");
            };

            // Tell grabber when to call the API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            await grabber.StartProcessingCameraAsync();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            await grabber.StopProcessingAsync();
        }
    }
}
```

De tweede steekproef app is een beetje interessanter. Hiermee u kiezen welke API u wilt aanroepen op de videoframes. Aan de linkerkant toont de app een voorbeeld van de live video. Aan de rechterkant wordt het meest recente API-resultaat op het bijbehorende frame geplaatst.

In de meeste modi is er een zichtbare vertraging tussen de live video aan de linkerkant en de gevisualiseerde analyse aan de rechterkant. Deze vertraging is de tijd die nodig is om de API-aanroep te maken. Een uitzondering is in de modus 'EmotionsWithClientDetect', die gezichtsherkenning lokaal uitvoert op de clientcomputer met Behulp van OpenCV voordat deze afbeeldingen naar Azure Cognitive Services verzendt. 

Door deze aanpak te gebruiken, u het gedetecteerde gezicht onmiddellijk visualiseren. U de emoties later bijwerken, nadat de API-aanroep is geretourneerd. Dit toont de mogelijkheid van een "hybride" aanpak aan. Dat wil zeggen, sommige eenvoudige verwerking kan worden uitgevoerd op de client, en vervolgens Cognitive Services API's kunnen worden gebruikt om deze verwerking te vergroten met meer geavanceerde analyse wanneer dat nodig is.

![De App LiveCameraSample die een afbeelding met tags weergeeft](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>Integreer de samples in uw codebase

Ga als volgt te werk om met dit voorbeeld aan de slag te gaan:

1. Haal API-sleutels voor de Vision-API's op uit [Abonnementen](https://azure.microsoft.com/try/cognitive-services/). Voor videoframeanalyse zijn de toepasselijke services:
    - [Computer Visie](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Face](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Kloon de [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub repo.

3. Open het voorbeeld in Visual Studio 2015 of hoger en bouw en voer de voorbeeldtoepassingen uit:
    - Voor BasicConsoleSample is de Face-toets rechtstreeks in [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs)hard gecodeerd.
    - Voer voor LiveCameraSample de toetsen in het deelvenster **Instellingen** van de app in. De sleutels blijven bestaan in sessies als gebruikersgegevens.

Wanneer u klaar bent om de voorbeelden te integreren, verwijst u naar de VideoFrameAnalyzer-bibliotheek van uw eigen projecten.

De mogelijkheden voor beeld-, spraak-, video- en tekstinzicht van VideoFrameAnalyzer maken gebruik van Azure Cognitive Services. Microsoft ontvangt de afbeeldingen, audio, video en andere gegevens die u uploadt (via deze app) en kan deze gebruiken voor serviceverbeteringsdoeleinden. We vragen uw hulp bij het beschermen van de personen van wie uw app gegevens naar Azure Cognitive Services verzendt.

## <a name="summary"></a>Samenvatting

In dit artikel leerde u hoe u bijna realtime analyses uitvoeren op live videostreams met behulp van de Face- en Computer Vision-services. Je hebt ook geleerd hoe je onze voorbeeldcode gebruiken om aan de slag te gaan. Ga naar de [aanmeldingspagina azure cognitive services](https://azure.microsoft.com/try/cognitive-services/)om aan de slag te gaan met het bouwen van uw app met behulp van gratis API-sleutels.

Voel je vrij om feedback en suggesties te geven in de [GitHub repository.](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) Ga naar onze [UserVoice-site](https://cognitive.uservoice.com/)om bredere API-feedback te geven.

