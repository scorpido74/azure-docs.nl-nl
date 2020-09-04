---
title: Video's in bijna realtime analyseren - Computer Vision
titleSuffix: Azure Cognitive Services
description: Leer hoe u met de Computer Vision-API in bijna realtime een analyse kunt uitvoeren voor frames die afkomstig zijn uit een livevideostream.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: c86b06ff46f1ddc8d22d2ab7ec4bc8620a8c862f
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933168"
---
# <a name="analyze-videos-in-near-real-time"></a>Video's in bijna realtime analyseren

In dit artikel leest u hoe u met de Computer Vision-API in bijna realtime een analyse kunt uitvoeren voor frames die afkomstig zijn uit een livevideostream. De basisstappen van een dergelijke analyse zijn:

- Frames verkrijgen uit een videobron.
- Selecteren welke frames u wilt analyseren.
- Deze frames verzenden naar de API.
- Elk analyseresultaat verbruiken dat wordt geretourneerd via de API-aanroep.

De voorbeelden in dit artikel zijn geschreven in C#. Ga naar de pagina [Analysevoorbeeld van videoframe](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) in GitHub voor toegang tot de code.

## <a name="approaches-to-running-near-real-time-analysis"></a>Benaderingen voor het in bijna in realtime uitvoeren van een analyse

Er zijn verschillende benaderingen om het probleem op te lossen voor het bijna in realtime analyseren van videostreams. In dit artikel vindt u een overzicht van drie van deze benaderingen, in een oplopend verfijningsniveau.

### <a name="design-an-infinite-loop"></a>Een oneindige lus ontwerpen

Het eenvoudigste ontwerp voor in bijna realtime analyse is een oneindige lus. In elke herhaling van deze lus haalt u een frame op, analyseert u dit frame en verbruikt u vervolgens het resultaat:

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

Dit is een geschikte methode voor een lichtgewicht algoritme aan de clientzijde. Wanneer de analyse echter in de cloud plaatsvindt, betekent de resulterende latentie dat een API-aanroep enkele seconden kan duren. Gedurende deze tijd worden er geen afbeeldingen vastgelegd, en doet de thread in feite niets. De maximale framesnelheid wordt beperkt door de latentie van de API-aanroepen.

### <a name="allow-the-api-calls-to-run-in-parallel"></a>Toestaan dat de API-aanroepen parallel worden uitgevoerd

Een eenvoudige lus met één thread is weliswaar geschikt voor een lichtgewicht algoritme aan de clientzijde, maar deze past minder goed bij de latentie van een API-aanroep in de cloud. Als u dit probleem wilt oplossen, staat u toe dat de langlopende API-aanroep parallel wordt uitgevoerd met het ophalen van frames. U kunt dit in C# doen met behulp van parallelle uitvoering op basis van een taak. U kunt bijvoorbeeld de volgende code uitvoeren:

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

Met deze methode start u elke analyse in een afzonderlijke taak. De taak kan op de achtergrond worden uitgevoerd terwijl u nieuwe frames blijft ophalen. Deze benadering voorkomt dat de hoofdthread wordt geblokkeerd terwijl u wacht op het retourneren van een API-aanroep. De methode kan echter bepaalde nadelen opleveren:
* Het gaat ten koste van bepaalde garanties die de eenvoudige versie biedt. Er kunnen nu namelijk meerdere parallelle API-aanroepen plaatsvinden, en de resultaten kunnen in de verkeerde volgorde worden geretourneerd. 
* Het kan ook tot gevolg hebben dat meerdere threads tegelijkertijd de functie ConsumeResult() gebruiken, wat gevaarlijk kan zijn als de functie niet thread-veilig is. 
* Ten slotte houdt deze eenvoudige code niet bij welke taken worden gemaakt, waardoor uitzonderingen geruisloos verdwijnen. Daarom moet u een 'consumer'-thread toevoegen die de analysetaken volgt, uitzonderingen genereert, langlopende taken beëindigt en ervoor zorgt dat de resultaten in de juiste volgorde, één voor één, worden verbruikt.

### <a name="design-a-producer-consumer-system"></a>Een ‘producer-consumer'-systeem ontwerpen

Voor de laatste benadering, het ontwerpen van een 'producer-consumer '-systeem, bouwt u een ‘producer'-thread die lijkt op de eerder vermelde oneindige lus. Echter in plaats van de analyseresultaten direct te verbruiken zodra deze beschikbaar zijn, plaatst de producer de taken in een wachtrij om ze bij te houden.

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

U maakt ook een ‘consumer'-thread, die taken uit de wachtrij haalt, wacht tot ze zijn voltooid, en vervolgens het resultaat of de uitzondering die is gegenereerd, weergeeft. Met behulp van de wachtrij kunt u garanderen dat resultaten één voor één worden verbruikt, in de juiste volgorde, zonder dat er een limiet wordt afgedwongen voor de maximale framesnelheid van het systeem.

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

### <a name="get-started-quickly"></a>Snel aan de slag gaan

Het systeem dat in de vorige sectie is beschreven, is geïmplementeerd om u te helpen de app zo snel mogelijk actief en werkend te krijgen. Het is bedoeld om flexibel genoeg te zijn voor vele soorten scenario's, terwijl het eenvoudig te gebruiken blijft. Ga naar de pagina [Analysevoorbeeld van videoframe](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) in GitHub voor toegang tot de code.

De bibliotheek bevat de klasse `FrameGrabber`, waarmee het eerder beschreven ‘producer-consumer'-systeem wordt geïmplementeerd voor het verwerken van videoframes van een webcam. Gebruikers kunnen de exacte vorm van de API-aanroep opgeven. De klasse maakt gebruik van gebeurtenissen om aan de aanroepende code door te geven wanneer er een nieuw frame is verkregen, of wanneer een nieuw analyseresultaat beschikbaar is.

Ter illustratie van enkele van de mogelijkheden bieden we twee voorbeeld-apps die gebruikmaken van de bibliotheek. 

De eerste voorbeeld-app is een eenvoudige console-app waarmee frames worden opgehaald van de standaardwebcam. Deze worden vervolgens voor gezichtsdetectie verzonden naar de Face-service. Een vereenvoudigde versie van de app wordt gereproduceerd in de volgende code:

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

De tweede voorbeeld-app is iets interessanter. Hierin kunt u kiezen welke API moet worden aangeroepen voor de videoframes. Aan de linkerkant ziet u een voorbeeld van de livevideo in de app. Aan de rechterkant ziet u een overlapping van het meest recente API-resultaat in het bijbehorende frame.

In de meeste modi is er een zichtbare vertraging tussen de livevideo aan de linkerkant en de gevisualiseerde analyse aan de rechterkant. Deze vertraging geeft de tijd aan die nodig is om de API-aanroep te maken. Een uitzondering vindt plaats in de modus EmotionsWithClientFaceDetect, waarin gezichtsdetectie lokaal wordt uitgevoerd op de clientcomputer met behulp van OpenCV, voordat afbeeldingen worden verzonden naar Cognitive Services. 

Met deze benadering kunt u het gedetecteerde gezicht onmiddellijk visualiseren. U kunt de emoties later bijwerken nadat de API-aanroep is geretourneerd. Hiermee wordt de mogelijkheid van een hybride benadering gedemonstreerd. Bepaalde eenvoudige verwerkingen kunnen dus worden uitgevoerd op de client, waarna de API's van Cognitive Services kunnen worden gebruikt om dit proces, waar nodig, te verbeteren met een meer geavanceerde analyse.

![De LiveCameraSample-app waarin een afbeelding met tags wordt weergegeven](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>De voorbeelden integreren in de codebasis

Voer de volgende stappen uit om met dit voorbeeld aan de slag te gaan:

1. Maak een [Azure-account](https://azure.microsoft.com/free/cognitive-services/). Ga naar de volgende stap als u al een account hebt.
2. Maak resources voor Computer Vision en Face in Azure Portal om uw sleutel en eindpunt op te halen. Zorg ervoor dat u tijdens de installatie de gratis laag (F0) selecteert.
   - [Computer Vision](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
   - [Face](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) Nadat de resources zijn geïmplementeerd, klikt u op **Ga naar resource** om uw sleutel en eindpunt te verzamelen voor elke resource. 
3. Kloon de GitHub-opslagplaats [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).
4. Open het voorbeeld in Visual Studio 2015 of later, bouw de voorbeeldtoepassingen en voer ze uit:
    - De Face-sleutel voor BasicConsoleSample is in code vastgelegd in [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Voor LiveCameraSample voert u de sleutels in het deelvenster **Instellingen** van de app in. De sleutels worden als gebruikersgegevens gehandhaafd tussen sessies.

Wanneer u klaar bent om de voorbeelden te integreren, verwijst u vanuit uw eigen projecten naar de bibliotheek VideoFrameAnalyzer.

De mogelijkheden voor afbeeldingen, spraak, video of tekstbegrip van VideoFrameAnalyzer maken gebruik van Azure Cognitive Services. Microsoft ontvangt de afbeeldingen, audio, video en andere gegevens die u uploadt (via deze app), en kan deze gebruiken met als doel de service te verbeteren. We vragen uw hulp bij het beschermen van de personen van wie uw app gegevens naar Azure Cognitive Services verzendt.

## <a name="summary"></a>Samenvatting

In dit artikel hebt u geleerd hoe u in bijna realtime een analyse kunt uitvoeren voor livevideostreams met behulp van de Face- en Computer Vision-services. U hebt ook geleerd hoe u onze voorbeeldcode kunt gebruiken om aan de slag te gaan.

U kunt feedback geven en suggesties doen in de [GitHub-opslagplaats](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/). Ga naar onze [UserVoice-site](https://cognitive.uservoice.com/) als u uitgebreidere API-feedback wilt geven.

