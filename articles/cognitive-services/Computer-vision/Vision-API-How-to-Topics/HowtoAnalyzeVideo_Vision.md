---
title: Video's in bijna realtime analyseren-Computer Vision
titleSuffix: Azure Cognitive Services
description: Leer hoe u bijna realtime analyses kunt uitvoeren op frames die worden opgehaald uit een live video stroom met behulp van de Computer Vision-API.
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
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166836"
---
# <a name="analyze-videos-in-near-real-time"></a>Video's in bijna realtime analyseren

In dit artikel wordt beschreven hoe u bijna realtime analyses kunt uitvoeren op frames die worden opgehaald uit een live video stroom met behulp van de Computer Vision-API. De basis elementen van een dergelijke analyse zijn:

- Bezig met het ophalen van frames van een video bron.
- Selecteren welke frames moeten worden geanalyseerd.
- Deze frames verzenden naar de API.
- Elk analyse resultaat dat wordt geretourneerd door de API-aanroep verbruikt.

De voor beelden in dit artikel zijn geschreven C#in. Als u toegang wilt krijgen tot de code, gaat u naar de voorbeeld pagina van de [analyse van video frames](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) op github.

## <a name="approaches-to-running-near-real-time-analysis"></a>Benaderingen voor het uitvoeren van bijna realtime analyse

U kunt het probleem met het uitvoeren van bijna realtime analyses op video stromen oplossen door verschillende benaderingen te gebruiken. In dit artikel vindt u een overzicht van drie deze items in toenemende niveaus van verfijning.

### <a name="design-an-infinite-loop"></a>Een oneindige lus ontwerpen

Het eenvoudigste ontwerp voor bijna realtime analyse is een oneindige lus. In elke iteratie van deze lus haalt u een kader op, analyseert u het en gebruikt u vervolgens het resultaat:

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

Als uw analyse bestaat uit een licht gewicht aan de client zijde, zou deze benadering geschikt zijn. Als de analyse echter plaatsvindt in de Cloud, betekent de resulterende latentie dat een API-aanroep enkele seconden kan duren. Gedurende deze tijd legt u geen installatie kopieën vast en maakt de thread in feite niets. De maximale frame frequentie wordt beperkt door de latentie van de API-aanroepen.

### <a name="allow-the-api-calls-to-run-in-parallel"></a>Toestaan dat de API-aanroepen parallel worden uitgevoerd

Hoewel een eenvoudige lus met één thread duidelijk is voor een licht gewicht, aan de client zijde, komt dit niet goed met de latentie van een API-aanroep van de Cloud. De oplossing voor dit probleem is om de langlopende API-aanroep parallel te laten uitvoeren met het frame-innemen. In C#kunt u dit doen met behulp van parallelle uitvoering op basis van een taak. U kunt bijvoorbeeld de volgende code uitvoeren:

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

Met deze methode start u elke analyse in een afzonderlijke taak. De taak kan op de achtergrond worden uitgevoerd terwijl u nieuwe frames gaat vastzetten. Met deze benadering wordt voor komen dat de hoofd thread wordt geblokkeerd terwijl u wacht tot een API-aanroep wordt geretourneerd. De aanpak kan echter bepaalde nadelen opleveren:
* Het kost een aantal van de garanties dat de eenvoudige versie wordt verstrekt. Dat wil zeggen dat meerdere API-aanroepen parallel kunnen optreden en dat de resultaten in de verkeerde volg orde worden geretourneerd. 
* Het kan ook ertoe leiden dat meerdere threads tegelijkertijd de functie ConsumeResult () kunnen invoeren, wat gevaarlijk kan zijn als de functie niet thread-veilig is. 
* Ten slotte houdt deze eenvoudige code geen bijhouden van de taken die worden gemaakt, waardoor uitzonde ringen op de achtergrond verdwijnen. Daarom moet u een ' consument '-thread toevoegen die de analyse taken bijhoudt, uitzonde ringen veroorzaakt, langlopende taken beëindigt en ervoor zorgt dat de resultaten in de juiste volg orde worden verbruikt.

### <a name="design-a-producer-consumer-system"></a>Een systeem voor de consumenten producent ontwerpen

Voor uw laatste benadering, het ontwerpen van een ' producer-consumer '-systeem, bouwt u een producer-thread die lijkt op uw eerder vermelde oneindige lus. In plaats van de resultaten van de analyse te verbruiken zodra ze beschikbaar zijn, plaatst de producent de taken in een wachtrij gewoon om ze bij te houden.

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

U maakt ook een Consumer thread waarmee taken van de wachtrij worden uitgevoerd, wacht totdat ze zijn voltooid en het resultaat wordt weer gegeven of de uitzonde ring wordt gegenereerd. Met behulp van de wachtrij kunt u garanderen dat de resultaten per keer worden verbruikt, in de juiste volg orde, zonder de maximale frame snelheid van het systeem te beperken.

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

Om ervoor te zorgen dat uw app zo snel mogelijk op de juiste wijze kan worden uitgevoerd, is het systeem geïmplementeerd dat wordt beschreven in de vorige sectie. Het is bedoeld om flexibel genoeg te zijn om te voldoen aan veel scenario's, terwijl u ze eenvoudig kunt gebruiken. Als u toegang wilt krijgen tot de code, gaat u naar de voorbeeld pagina van de [analyse van video frames](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) op github.

De bibliotheek bevat de `FrameGrabber`-klasse, waarmee het eerder besproken productie systeem van de consument wordt geïmplementeerd om video frames van een webcam te verwerken. Gebruikers kunnen de exacte vorm van de API-aanroep opgeven en de klasse gebruikt gebeurtenissen om de aanroepende code te laten weten wanneer er een nieuw frame wordt verkregen of wanneer er een nieuw resultaat van de analyse beschikbaar is.

Om enkele van de mogelijkheden te illustreren, hebben we twee voor beelden gegeven van apps die gebruikmaken van de-bibliotheek. 

De eerste voor beeld-app is een eenvoudige console-app die frames ophaalt van de standaard webcam en deze vervolgens verzendt naar de face-service voor gezichts detectie. Een vereenvoudigde versie van de app wordt in de volgende code gereproduceerd:

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

De tweede voor beeld-app is iets interessanter. Hiermee kunt u kiezen welke API moet worden aangeroepen op de video frames. Aan de linkerkant ziet u een voor beeld van de live video in de app. Aan de rechter kant wordt het meest recente API-resultaat op het bijbehorende frame bedekt.

In de meeste modi is er een zicht bare vertraging tussen de live video aan de linkerkant en de gevisualiseerde analyse aan de rechter kant. Deze vertraging is de tijd die nodig is om de API-aanroep uit te voeren. Er bevindt zich een uitzonde ring in de modus ' EmotionsWithClientFaceDetect ', die de detectie van het gezicht lokaal uitvoert op de client computer met behulp van OpenCV voordat er installatie kopieën naar Azure Cognitive Services worden verzonden. 

Met deze methode kunt u het gedetecteerde gezicht direct visualiseren. U kunt de emoties later bijwerken nadat de API-aanroep is geretourneerd. Hiermee wordt de mogelijkheid van een hybride benadering gedemonstreerd. Dat wil zeggen dat er een eenvoudige verwerking kan worden uitgevoerd op de client en vervolgens Cognitive Services-API's kan worden gebruikt om deze verwerking uit te breiden met meer geavanceerde analyse wanneer dat nodig is.

![De LiveCameraSample-app waarin een afbeelding met tags wordt weer gegeven](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>De voor beelden in de code basis integreren

Ga als volgt te werk om aan de slag te gaan met dit voor beeld:

1. Haal API-sleutels voor de Vision-API's op uit [Abonnementen](https://azure.microsoft.com/try/cognitive-services/). Voor video frame analyse zijn de volgende services van toepassing:
    - [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Face](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Kloon de [cognitieve-samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) github opslag plaats.

3. Open het voor beeld in Visual Studio 2015 of hoger en bouw en voer de voorbeeld toepassingen uit:
    - Voor BasicConsoleSample is de face-sleutel rechtstreeks vastgelegd in [BasicConsoleSample/Program. cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Voor LiveCameraSample voert u de sleutels in het deel venster **instellingen** van de app in. De sleutels worden opgeslagen in verschillende sessies als gebruikers gegevens.

Wanneer u klaar bent om de voor beelden te integreren, verwijst u naar de VideoFrameAnalyzer-bibliotheek van uw eigen projecten.

Met de mogelijkheden van de afbeeldings-, spraak-, video-en tekst-informatie over VideoFrameAnalyzer Azure Cognitive Services. Micro soft ontvangt de afbeeldingen, audio, video en andere gegevens die u uploadt (via deze app) en kan deze gebruiken voor de verbetering van de service. We vragen uw hulp bij het beschermen van de personen van wie uw app gegevens naar Azure Cognitive Services verzendt.

## <a name="summary"></a>Samenvatting

In dit artikel hebt u geleerd hoe u bijna realtime analyses kunt uitvoeren op live video streams met behulp van het gezichts-en Computer Vision Services. U hebt ook geleerd hoe u onze voorbeeld code kunt gebruiken om aan de slag te gaan. Ga naar de [registratie pagina van Azure Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)om aan de slag te gaan met het bouwen van uw app met behulp van gratis API-sleutels.

U kunt feedback en suggesties bieden in de [github-opslag plaats](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/). Ga naar onze [UserVoice-site](https://cognitive.uservoice.com/)om uitgebreidere API-feedback te bieden.

