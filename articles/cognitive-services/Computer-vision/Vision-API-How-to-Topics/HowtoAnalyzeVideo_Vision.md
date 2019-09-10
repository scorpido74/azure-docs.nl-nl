---
title: 'Voorbeeld: Video-analyse in realtime: Computer Vision'
titleSuffix: Azure Cognitive Services
description: Leer hoe u met de Computer Vision-API vrijwel in realtime een analyse kunt uitvoeren van frames afkomstig uit een live-videostream.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 25aed0f042050ebadbc6054fcbf0c68dbf782e5e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859086"
---
# <a name="how-to-analyze-videos-in-real-time"></a>Video's in realtime analyseren

In deze handleiding wordt uitgelegd hoe u bijna in realtime een analyse kunt uitvoeren van frames die afkomstig zijn uit een live-videostream. Dit zijn de basisstappen van een dergelijk systeem:

- Frames verkrijgen uit een videobron
- Selecteren welke frames u wilt analyseren
- Deze frames verzenden naar de API
- De analyseresultaten verbruiken die worden geretourneerd door de API-aanroep

Deze voorbeelden zijn geschreven in C# en de code vindt u hier op GitHub: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>De benadering

Er zijn meerdere manieren om het probleem op te lossen voor het bijna in realtime analyseren van videostreams. We gaan eerst drie benaderingen beschrijven die steeds uitgebreider worden.

### <a name="a-simple-approach"></a>Een eenvoudige benadering

Het eenvoudigste ontwerp voor een systeem voor bijna in realtime analyseren is een oneindige lus, waarbij we bij elke iteratie een frame pakken, dit frame analyseren en vervolgens het resultaat gebruiken:

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

Als onze analyse bestaat uit een lichtgewicht algoritme op de client, is dit een geschikte benadering. Wanneer onze analyse echter in de cloud plaatsvindt, zou een API-aanroep door de betrokken latentie mogelijk enkele seconden duren. In die tijd leggen we geen afbeeldingen vast en doet onze thread als het ware niets. Onze maximale framesnelheid wordt beperkt door de latentie van de API-aanroepen.

### <a name="parallelizing-api-calls"></a>API-aanroepen parallel maken

Waar een eenvoudige lus met één thread nog wel kan worden gebruikt voor een lichtgewicht algoritme aan de clientzijde, is deze minder geschikt voor de latentie waarmee u in API-aanroepen via de cloud te maken hebt. Als u dit probleem wilt oplossen, staat u toe dat de langlopende API-aanroepen tegelijkertijd worden uitgevoerd met het ophalen van de frames. In C# kan dit worden bereikt met behulp van op taken gebaseerd parallellisme, bijvoorbeeld:

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

Bij deze benadering wordt elke analyse gestart in een afzonderlijke taak die op de achtergrond kan worden uitgevoerd, terwijl u nieuwe frames blijft ophalen. Het voorkomt dat de hoofdthread wordt geblokkeerd tijdens het wachten op de terugkeer van een API-oproep. Enkele van de garanties die de eenvoudige versie bood, zijn echter verdwenen – meerdere API-oproepen kunnen parallel plaatsvinden, en de resultaten kunnen dan in de verkeerde volgorde worden geretourneerd. Deze benadering kan ook tot gevolg hebben dat meerdere threads tegelijkertijd de functie ConsumeResult() betreden, wat gevaarlijk kan zijn als de functie niet thread-veilig is. Ten slotte houdt deze eenvoudige code niet bij welke taken er worden gemaakt, wat betekent dat uitzonderingen geruisloos verdwijnen. Het laatste onderdeel is dan ook het toevoegen van een 'consumer'-thread die de analysetaken volgt, uitzonderingen genereert, langlopende taken beëindigt en ervoor zorgt dat de resultaten in de juiste volgorde en één voor één worden verbruikt.

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

Om uw app zo snel mogelijk draaiend te krijgen, hebben we het hierboven beschreven systeem geïmplementeerd, met de bedoeling dat het flexibel genoeg is voor het implementeren van verschillende scenario's en tegelijkertijd eenvoudig te gebruiken is. Ga voor toegang tot de code naar [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

De bibliotheek bevat de klasse FrameGrabber, waarmee het hierboven beschreven systeem van producers en consumers wordt geïmplementeerd voor het verwerken van videoframes van een webcam. De gebruiker kan de exacte vorm van de API-aanroep opgeven en de klasse maakt gebruik van gebeurtenissen om aan de aanroepende code door te geven wanneer er een nieuw frame is verkregen of een nieuw analyseresultaat beschikbaar is.

Ter illustratie van enkele van de mogelijkheden, zijn er twee voorbeeld-apps die gebruikmaken van de bibliotheek. De eerste is een eenvoudige console-app, waarvan u hieronder een vereenvoudigde versie ziet. De app legt frames van de standaardwebcam vast en verzendt deze naar de Face-API voor gezichtsherkenning.

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

        private static void Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face API Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face API.
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

            // Tell grabber when to call API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

De tweede voorbeeld-app is iets interessanter, en stelt u in staat om te kiezen welke API u wilt aanroepen voor de videoframes. Aan de linkerkant toont de app een voorbeeld van de live-video en aan de rechterkant ziet u het meest recente API-resultaat als overlay over het bijbehorende frame.

In de meeste modi is er een zichtbare vertraging tussen de livevideo aan de linkerkant en de gevisualiseerde analyse aan de rechterkant. Deze vertraging is de tijd die nodig is om de API-aanroep te maken. De uitzondering hierop is in de modus EmotionsWithClientFaceDetect, waarin gezichtsdetectie lokaal wordt uitgevoerd op de clientcomputer met behulp van OpenCV, voordat er afbeeldingen naar Cognitive Services worden verzonden. Op deze manier wordt het gedetecteerde gezicht direct gevisualiseerd en worden de emoties later bijgewerkt zodra de API-aanroep is uitgevoerd. Dit demonstreert de mogelijkheid van een 'hybride' benadering, waarbij bepaalde eenvoudige verwerkingen kunnen worden uitgevoerd op de client, waarna de API's van Cognitive Services kunnen worden gebruikt om dit waar nodig te verbeteren met een meer geavanceerde analyse.

![Schermafbeelding van de LiveCameraSample-app waarin een afbeelding met tags wordt weergegeven](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integreren in de codebase

Als u met dit voorbeeld aan de slag wilt, volgt u deze stappen:

1. Haal API-sleutels voor de Vision-API's op uit [Abonnementen](https://azure.microsoft.com/try/cognitive-services/). Voor analyse van videoframes zijn dit de relevante API's:
    - [Computer Vision-API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Face-API](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Kloon de GitHub-opslagplaats [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)

3. Open het voor beeld in Visual Studio 2015 of hoger, bouw en voer de voorbeeld toepassingen uit:
    - De Face-API-sleutel voor BasicConsoleSample is in code vastgelegd in [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Voor LiveCameraSample moeten de sleutels worden ingevoerd in het instellingenvenster van de app. De sleutels worden als gebruikersgegevens gehandhaafd tussen sessies.

Wanneer u klaar om te gaan integreren, **verwijst u vanuit uw eigen projecten naar de bibliotheek VideoFrameAnalyzer**.

De voorzieningen voor afbeeldingen, spraak, video of tekstbegrip van VideoFrameAnalyzer maken gebruik van Azure Cognitive Services. Microsoft ontvangt de afbeeldingen, audio, video en andere gegevens die u uploadt (via deze app) en kan deze gebruiken met als doel de service te verbeteren. We vragen uw hulp bij het beschermen van de personen van wie uw app gegevens naar Azure Cognitive Services verzendt.

## <a name="summary"></a>Samenvatting

In deze hand leiding hebt u geleerd hoe u bijna realtime analyses kunt uitvoeren op live video streams met behulp van het gezicht en Computer Vision Api's, en hoe u onze voorbeeld code gebruikt om aan de slag te gaan. U kunt uw eigen app gaan bouwen met behulp van API-sleutels die u gratis kunt ophalen op de [registratiepagina voor Azure Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

U kunt feedback en suggesties achterlaten in de [GitHub-opslagplaats](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/). Voor meer uitgebreide API-feedback kunt u terecht op onze [UserVoice-site](https://cognitive.uservoice.com/).

