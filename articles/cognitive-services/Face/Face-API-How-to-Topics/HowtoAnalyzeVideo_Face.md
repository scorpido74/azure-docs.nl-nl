---
title: 'Voor beeld: realtime video analyse-face'
titleSuffix: Azure Cognitive Services
description: Gebruik de face-service om bijna realtime analyses uit te voeren op frames die zijn gemaakt met een live video stroom.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: ab3f596000216e8555bb84d0d47aff9a6e969eeb
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76169888"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>Voorbeeld: video's in realtime analyseren

In deze handleiding wordt uitgelegd hoe u bijna in realtime een analyse kunt uitvoeren van de frames die uit een livevideostream zijn gehaald. Dit zijn de basisstappen van een dergelijk systeem:

- Frames verkrijgen uit een videobron
- Selecteren welke frames u wilt analyseren
- Deze frames verzenden naar de API
- De analyseresultaten verbruiken die worden geretourneerd door de API-aanroep

Deze voor beelden zijn geschreven in C# en de code kunt u hier vinden op GitHub [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/):.

## <a name="the-approach"></a>De benadering

Er zijn meerdere manieren om het probleem op te lossen voor het bijna in realtime analyseren van videostreams. We gaan eerst drie benaderingen beschrijven die steeds uitgebreider worden.

### <a name="a-simple-approach"></a>Een eenvoudige benadering

Het eenvoudigste ontwerp voor een systeem voor bijna in realtime analyseren is een oneindige lus, waarbij bij elke iteratie een frame wordt genomen, dit frame wordt geanalyseerd en vervolgens het resultaat wordt gebruikt:

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

Als onze analyse bestaat uit een lichtgewicht algoritme op de client, is dit een geschikte benadering. Wanneer de analyse echter in de cloud plaatsvindt, betekent de latentie dat een API-aanroep enkele seconden kan duren. Gedurende deze tijd worden er geen afbeeldingen geregistreerd, en onze thread doet in feite niets. Onze maximale framesnelheid wordt beperkt door de latentie van de API-aanroepen.

### <a name="parallelizing-api-calls"></a>API-aanroepen parallel maken

Waar een eenvoudige lus met één thread nog wel kan worden gebruikt voor een lichtgewicht algoritme aan de clientzijde, is deze minder geschikt voor de latentie waarmee u in API-aanroepen via de cloud te maken hebt. Als u dit probleem wilt oplossen, staat u toe dat de langlopende API-aanroepen tegelijkertijd worden uitgevoerd met het ophalen van de frames. In C# kan dit worden bereikt met behulp van op taken gebaseerd parallelisme, bijvoorbeeld:

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

Met deze code wordt elke analyse in een afzonderlijke taak gestart, die op de achtergrond kan worden uitgevoerd terwijl we doorgaan met het pakken van nieuwe frames. Met deze methode wordt voorkomen dat de belangrijkste thread wordt geblokkeerd tijdens het wachten op een API-aanroep om terug te keren, maar we verliezen enkele van de garanties die de eenvoudige versie bood. Er kunnen nu meerdere parallelle API-aanroepen worden gedaan, en de resultaten zouden in de verkeerde volgorde kunnen worden geretourneerd. Dit kan ook tot gevolg hebben dat meerdere threads tegelijkertijd de functie ConsumeResult() betreden, wat gevaarlijk kan zijn als de functie niet thread-veilig is. Ten slotte houdt deze eenvoudige code niet bij welke taken er worden gemaakt, wat betekent dat uitzonderingen geruisloos verdwijnen. De laatste stap is dan ook het toevoegen van een 'consumer'-thread die de analysetaken volgt, uitzonderingen genereert, langlopende taken beëindigt en ervoor zorgt dat de resultaten in de juiste volgorde worden verbruikt.

### <a name="a-producer-consumer-design"></a>Een ontwerp voor producers en consumers

In ons uiteindelijke systeem met producers en consumers hebben we een producer-thread die lijkt op onze vorige oneindige lus. Echter in plaats van analyseresultaten direct te verbruiken zodra deze beschikbaar zijn, plaatst de producer de taken in een wachtrij om ze te volgen.

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

We hebben ook een consumer-thread, die taken uit de wachtrij haalt, wacht tot ze zijn voltooid, en vervolgens het resultaat of de uitzondering die is gegenereerd, weergeeft. Met behulp van de wachtrij kunnen we garanderen dat resultaten één voor één worden verbruikt, in de juiste volgorde, zonder dat er een limiet wordt afgedwongen voor de maximale framesnelheid van het systeem.

```csharp
// Consumer thread. 
while (true)
{
    // Get the oldest task. 
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed. 
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

## <a name="implementing-the-solution"></a>De oplossing implementeren

### <a name="getting-started"></a>Aan de slag

Om uw app zo snel mogelijk aan de praat te krijgen, gebruikt u een flexibele implementatie van het hierboven beschreven systeem. Als u toegang wilt krijgen tot de [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis)code, gaat u naar.

De bibliotheek bevat de klasse FrameGrabber, waarmee het hierboven beschreven systeem van producers en consumers wordt geïmplementeerd voor het verwerken van videoframes van een webcam. De gebruiker kan de exacte vorm van de API-aanroep opgeven. De klasse maakt tevens gebruik van gebeurtenissen om aan de aanroepende code door te geven wanneer er een nieuw frame is verkregen of een nieuw analyseresultaat beschikbaar is.

Ter illustratie van enkele van de mogelijkheden, zijn er twee voorbeeld-apps die gebruikmaken van de bibliotheek. De eerste is een eenvoudige console-app, waarvan u hieronder een vereenvoudigde versie ziet. Er worden frames van de standaard webcam geplaatst en verzonden naar de gezichts service voor gezichts detectie.

```csharp
using System;
using VideoFrameAnalyzer;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;
     
namespace VideoFrameConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create grabber, with analysis type Face[]. 
            FrameGrabber<Face[]> grabber = new FrameGrabber<Face[]>();
            
            // Create Face Client. Insert your Face API key here.
            private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });

            // Set up our Face API call.
            grabber.AnalysisFunction = async frame => return await faceClient.DetectAsync(frame.Image.ToMemoryStream(".jpg"));

            // Set up a listener for when we receive a new result from an API call. 
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.Analysis != null)
                    Console.WriteLine("New result received for frame acquired at {0}. {1} faces detected", e.Frame.Metadata.Timestamp, e.Analysis.Length);
            };
            
            // Tell grabber to call the Face API every 3 seconds.
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for keypress to stop
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();
            
            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

De tweede voorbeeld-app is iets interessanter, en stelt u in staat om te kiezen welke API u wilt aanroepen voor de videoframes. Aan de linkerkant toont de app een voorbeeld van de live-video en aan de rechterkant ziet u het meest recente API-resultaat als overlay over het bijbehorende frame.

In de meeste modi is er een zichtbare vertraging tussen de livevideo aan de linkerkant en de gevisualiseerde analyse aan de rechterkant. Deze vertraging is de tijd die nodig is om de API-aanroep te maken. Een uitzondering hierop is de modus EmotionsWithClientFaceDetect, waarin gezichtsdetectie lokaal wordt uitgevoerd op de clientcomputer met behulp van OpenCV, voordat er afbeeldingen naar Cognitive Services worden verzonden. Op deze manier wordt het gedetecteerde gezicht direct gevisualiseerd en worden de emoties bijgewerkt nadat de API-aanroep is uitgevoerd. Dit is een voorbeeld van een 'hybride' benadering, waarbij de client wat eenvoudige bewerkingen kan uitvoeren en Cognitive Services-API's dit indien nodig kunnen aanvullen met meer geavanceerde analyse.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integreren in de codebase

Als u met dit voorbeeld aan de slag wilt, volgt u deze stappen:

1. Haal API-sleutels voor de Vision-API's op uit [Abonnementen](https://azure.microsoft.com/try/cognitive-services/). Voor analyse van videoframes zijn dit de relevante API's:
    - [Computer Vision-API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Face-API](https://docs.microsoft.com/azure/cognitive-services/face/overview)

2. Kloon de GitHub-opslagplaats [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)

3. Open het voorbeeld in Visual Studio 2015, bouw de voorbeeldtoepassingen en voer ze uit:
    - Voor BasicConsoleSample is de face-sleutel rechtstreeks vastgelegd in [BasicConsoleSample/Program. cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Voor LiveCameraSample moeten de sleutels worden ingevoerd in het instellingenvenster van de app. De sleutels worden als gebruikersgegevens gehandhaafd tussen sessies.
        

Wanneer u klaar om te gaan integreren, **verwijst u vanuit uw eigen projecten naar de bibliotheek VideoFrameAnalyzer**. 

## <a name="summary"></a>Samenvatting

In deze handleiding hebt u geleerd hoe u bijna in realtime analyses van live-videostreams kunt uitvoeren met behulp van de Face-, Computer Vision- en Emotion-API's en hoe u onze voorbeeldcode kunt gebruiken om aan de slag te gaan. U kunt uw eigen app bouwen met behulp van API-sleutels die u gratis kunt ophalen op de [registratiepagina voor Azure Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). 

U kunt feedback en suggesties achterlaten in de [GitHub-opslagplaats](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/). Voor meer uitgebreide API-feedback kunt u terecht op onze  [UserVoice-site](https://cognitive.uservoice.com/).

## <a name="related-topics"></a>Verwante onderwerpen
- [Gezichten in een afbeelding identificeren](HowtoIdentifyFacesinImage.md)
- [Gezichten in een afbeelding detecteren](HowtoDetectFacesinImage.md)
