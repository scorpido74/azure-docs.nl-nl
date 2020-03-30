---
title: Azure Service Fabric-apps debuggen in Windows
description: Meer informatie over het bewaken en diagnosticeren van uw services die zijn geschreven met Microsoft Azure Service Fabric op een lokale ontwikkelingsmachine.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8435bb82afddd0070679768bb8d22ad9290f2279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258510"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Services controleren en diagnosticeren in een ontwikkelomgeving met lokale machines
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Met monitoring, detectie, diagnose en probleemoplossing kunnen services doorgaan met een minimale verstoring van de gebruikerservaring. Hoewel monitoring en diagnostiek van cruciaal belang zijn in een daadwerkelijk geïmplementeerde productieomgeving, is de efficiëntie afhankelijk van het aannemen van een vergelijkbaar model tijdens de ontwikkeling van services om ervoor te zorgen dat ze werken wanneer u overstapt naar een echte installatie. Service Fabric maakt het voor serviceontwikkelaars eenvoudig om diagnoses te implementeren die naadloos kunnen werken in zowel bedrijfsinstellingen voor één machine als opstellingen voor echte productieclusters.

## <a name="event-tracing-for-windows"></a>Gebeurtenistracering voor Windows
[Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) is de aanbevolen technologie voor het traceren van berichten in Service Fabric. Enkele voordelen van het gebruik van ETW zijn:

* **ETW is snel.** Het werd gebouwd als een traceringstechnologie die een minimale impact heeft op de uitvoeringstijden van code.
* **ETW tracing werkt naadloos in lokale ontwikkelomgevingen en ook real-world cluster setups.** Dit betekent dat u uw traceringscode niet hoeft te herschrijven wanneer u klaar bent om uw code te implementeren in een echt cluster.
* **Service Fabric-systeemcode maakt ook gebruik van ETW voor interne tracering.** Hiermee u uw applicatiesporen met servicefabriceersysteemsporen bekijken. Het helpt u ook om de sequenties en onderlinge relaties tussen uw toepassingscode en gebeurtenissen in het onderliggende systeem gemakkelijker te begrijpen.
* **Er is ingebouwde ondersteuning in Service Fabric Visual Studio-tools om ETW-gebeurtenissen te bekijken.** ETW-gebeurtenissen worden weergegeven in de weergave Diagnostische gebeurtenissen van Visual Studio zodra Visual Studio correct is geconfigureerd met Service Fabric. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Service Fabric-systeemgebeurtenissen weergeven in Visual Studio
Service Fabric zendt ETW-gebeurtenissen uit om applicatieontwikkelaars te helpen begrijpen wat er in het platform gebeurt. Als u dit nog niet hebt gedaan, gaat u verder en volgt u de stappen in [Het maken van uw eerste toepassing in Visual Studio.](service-fabric-tutorial-create-dotnet-app.md) Met deze informatie u een toepassing aan de praat krijgen met de Diagnostic Events Viewer met de traceerberichten.

1. Als het venster diagnostische gebeurtenissen niet automatisch wordt weergegeven, gaat u naar het tabblad **Weergave** in Visual Studio, kiest **u Andere Windows** en vervolgens Diagnostische gebeurtenissen **viewer**.
2. Elke gebeurtenis heeft standaard metagegevens die u het knooppunt, de toepassing en de service vertelt waar de gebeurtenis vandaan komt. U de lijst met gebeurtenissen ook filteren met het vak **Gebeurtenissen filteren** boven aan het venster gebeurtenissen. U bijvoorbeeld filteren op **Knooppuntnaam** of **Servicenaam.** En als je naar gebeurtenisdetails kijkt, kun je ook pauzeren met de knop **Pauze** boven aan het evenementenvenster en later hervatten zonder verlies van gebeurtenissen.
   
   ![Visual Studio Diagnostics Events Viewer](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Uw eigen aangepaste traces toevoegen aan de toepassingscode
De projectsjablonen van Service Fabric Visual Studio bevatten voorbeeldcode. De code laat zien hoe je aangepaste toepassingscode ETW-sporen toevoegt die worden weergegeven in de Visual Studio ETW-viewer naast systeemsporen van Service Fabric. Het voordeel van deze methode is dat metagegevens automatisch worden toegevoegd aan traces en dat de Visual Studio Diagnostic Events Viewer al is geconfigureerd om ze weer te geven.

Zoek voor projecten die zijn gemaakt met de **servicesjablonen** (stateloos of stateful) alleen naar de `RunAsync` implementatie:

1. De aanroep `ServiceEventSource.Current.ServiceMessage` `RunAsync` naar de methode toont een voorbeeld van een aangepaste ETW-tracering uit de toepassingscode.
2. In het **ServiceEventSource.cs** bestand, vindt u `ServiceEventSource.ServiceMessage` een overbelasting voor de methode die moet worden gebruikt voor hoogfrequente gebeurtenissen als gevolg van prestatieredenen.

Voor projecten die zijn gemaakt met de **actorsjablonen** (stateloos of stateful):

1. Open het bestand **'ProjectNaam'.cs** waarin *ProjectName* de naam is die u voor uw Visual Studio-project hebt gekozen.  
2. Zoek de `ActorEventSource.Current.ActorMessage(this, "Doing Work");` code in de *DoWorkAsync-methode.*  Dit is een voorbeeld van een aangepaste ETW-tracering geschreven uit toepassingscode.  
3. In bestand **ActorEventSource.cs,** vindt u `ActorEventSource.ActorMessage` een overbelasting voor de methode die moet worden gebruikt voor hoogfrequente gebeurtenissen als gevolg van prestatieredenen.

Nadat u aangepaste ETW-tracering aan uw servicecode hebt toegevoegd, u de toepassing opnieuw bouwen, implementeren en uitvoeren om uw gebeurtenis(s) te bekijken in de Diagnostische gebeurtenissenviewer. Als u de toepassing met **F5**debugt, wordt de Diagnostische gebeurtenissenviewer automatisch geopend.

## <a name="next-steps"></a>Volgende stappen
Dezelfde traceringscode die u hierboven aan uw toepassing hebt toegevoegd voor lokale diagnostiek, werkt met hulpprogramma's die u gebruiken om deze gebeurtenissen weer te geven wanneer u uw toepassing uitvoert op een Azure-cluster. Bekijk deze artikelen waarin de verschillende opties voor de tools worden besproken en hoe u ze instellen.

* [Logboeken verzamelen met Azure Diagnostics](service-fabric-diagnostics-how-to-setup-wad.md)
* [Gebeurtenisaggregatie en verzameling met EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)

