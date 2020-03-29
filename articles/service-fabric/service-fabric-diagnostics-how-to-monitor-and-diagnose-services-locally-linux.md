---
title: Azure Service Fabric-apps debuggen in Linux
description: Meer informatie over het monitoren en diagnosticeren van uw Service Fabric-services op een lokale Linux-ontwikkelingsmachine.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d8b5ec2f2190586f5eced5eee112b190a82504c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75526291"
---
# <a name="monitor-and-diagnose-services-in-a-local-linux-machine-development-setup"></a>Services controleren en diagnosticeren in een lokale Linux-machine-installatie


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Met monitoring, detectie, diagnose en probleemoplossing kunnen services doorgaan met een minimale verstoring van de gebruikerservaring. Monitoring en diagnostiek zijn cruciaal in een daadwerkelijk geïmplementeerde productieomgeving. Het aannemen van een vergelijkbaar model tijdens de ontwikkeling van services zorgt ervoor dat de diagnostische pijplijn werkt wanneer u naar een productieomgeving gaat. Service Fabric maakt het voor serviceontwikkelaars eenvoudig om diagnoses te implementeren die naadloos kunnen werken in zowel bedrijfsinstellingen voor één machine als opstellingen voor echte productieclusters.


## <a name="debugging-service-fabric-java-applications"></a>Debugging Service Fabric Java-toepassingen

Voor Java-toepassingen zijn [meerdere logging frameworks](https://en.wikipedia.org/wiki/Java_logging_framework) beschikbaar. Aangezien `java.util.logging` is de standaard optie met de JRE, het wordt ook gebruikt voor de [code voorbeelden in GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started). In de volgende discussie `java.util.logging` wordt uitgelegd hoe u het framework configureert.

Met behulp van java.util.logging u uw toepassingslogboeken omleiden naar geheugen, uitvoerstromen, consolebestanden of sockets. Voor elk van deze opties zijn er standaardhandlers die al in het framework zijn opgenomen. U `app.properties` een bestand maken om de bestandshandler voor uw toepassing te configureren om alle logboeken om te leiden naar een lokaal bestand.

Het volgende codefragment bevat een voorbeeldconfiguratie:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

De map waarnaar `app.properties` door het bestand wordt aangegeven, moet bestaan. Nadat `app.properties` het bestand is gemaakt, moet u ook `entrypoint.sh` uw `<applicationfolder>/<servicePkg>/Code/` toegangspuntscript wijzigen `java.util.logging.config.file` `app.properties` in de map om de eigenschap in te stellen op bestand. De vermelding moet er als volgt uitzien:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Deze configuratie resulteert in logs worden verzameld `/tmp/servicefabric/logs/`in een roterende manier op . Het logbestand in dit geval wordt mysfapp%u.%g.log genoemd waar:
* **%u** is een uniek nummer om conflicten tussen gelijktijdige Java-processen op te lossen.
* **%g** is het generatienummer dat onderscheid maakt tussen roterende logboeken.

Als er geen handler expliciet is geconfigureerd, wordt de consolehandler standaard geregistreerd. Men kan de logs in syslog bekijken onder /var/log/syslog.

Zie voor meer informatie de [codevoorbeelden in GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started).


## <a name="debugging-service-fabric-c-applications"></a>Foutopsporingsservicefabric C#-toepassingen


Er zijn meerdere frameworks beschikbaar voor het traceren van CoreCLR-toepassingen op Linux. Zie [GitHub: logboekregistratie](http:/github.com/aspnet/logging)voor meer informatie.  Aangezien EventSource bekend is bij C# ontwikkelaars,'gebruikt dit artikel EventSource voor tracering in CoreCLR-samples op Linux.

De eerste stap is om System.Diagnostics.Tracing op te nemen, zodat u uw logboeken schrijven naar geheugen-, uitvoerstromen of consolebestanden.  Als u logboekregistratie met EventSource wilt gebruiken, voegt u het volgende project toe aan uw project.json:

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

U een aangepaste EventListener gebruiken om naar de servicegebeurtenis te luisteren en deze vervolgens op de juiste manier om te leiden om bestanden te traceren. In het volgende codefragment ziet u een voorbeeld van logboekregistratie met EventSource en een aangepaste Gebeurtenislistener:


```csharp

public class ServiceEventSource : EventSource
{
        public static ServiceEventSource Current = new ServiceEventSource();

        [NonEvent]
        public void Message(string message, params object[] args)
        {
            if (this.IsEnabled())
            {
                var finalMessage = string.Format(message, args);
                this.Message(finalMessage);
            }
        }

        // TBD: Need to add method for sample event.

}

```


```csharp
internal class ServiceEventListener : EventListener
{

        protected override void OnEventSourceCreated(EventSource eventSource)
        {
            EnableEvents(eventSource, EventLevel.LogAlways, EventKeywords.All);
        }
        protected override void OnEventWritten(EventWrittenEventArgs eventData)
        {
                using (StreamWriter Out = new StreamWriter( new FileStream("/tmp/MyServiceLog.txt", FileMode.Append)))
                {
                        // report all event information
                        Out.Write(" {0} ", Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
                        if (eventData.Message != null)
                                Out.WriteLine(eventData.Message, eventData.Payload.ToArray());
                        else
                        {
                                string[] sargs = eventData.Payload != null ? eventData.Payload.Select(o => o.ToString()).ToArray() : null; 
                                Out.WriteLine("({0}).", sargs != null ? string.Join(", ", sargs) : "");
                        }
                }
        }
}
```


In het voorgaande fragment worden de `/tmp/MyServiceLog.txt`logboeken naar een bestand uitgevoerd in . Deze bestandsnaam moet op de juiste manier worden bijgewerkt. Als u de logboeken wilt omleiden naar de console, gebruikt u het volgende fragment in de klasse EventListener:

```csharp
public static TextWriter Out = Console.Out;
```

De voorbeelden bij [C# Samples](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) gebruiken EventSource en een aangepaste EventListener om gebeurtenissen bij te loggen op een bestand.



## <a name="next-steps"></a>Volgende stappen
Dezelfde traceringscode die aan uw toepassing is toegevoegd, werkt ook met de diagnose van uw toepassing op een Azure-cluster. Bekijk deze artikelen die de verschillende opties voor de tools bespreken en beschrijven hoe ze in te stellen.
* [Logboeken verzamelen met Azure Diagnostics](service-fabric-diagnostics-how-to-setup-lad.md)
