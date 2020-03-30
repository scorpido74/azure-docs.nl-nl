---
title: Uw toepassing debuggen in Visual Studio
description: Verbeter de betrouwbaarheid en prestaties van uw services door ze te ontwikkelen en te debuggen in Visual Studio op een lokaal ontwikkelingscluster.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: fff8a19d5643f7ce866c9eb9c57486340b6f8a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624143"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Foutopsporing uitvoeren in uw Service Fabric-toepassing met behulp van Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Een lokale Service Fabric-toepassing debuggen
U tijd en geld besparen door uw Azure Service Fabric-toepassing te implementeren en te debuggen in een lokaal cluster voor computerontwikkeling. Visual Studio 2019 of 2015 kan de toepassing implementeren in het lokale cluster en de foutopsporing automatisch verbinden met alle exemplaren van uw toepassing. Visual Studio moet worden uitgevoerd als beheerder om de foutopsporing te verbinden.

1. Start een lokaal ontwikkelcluster door de stappen te volgen in [Het instellen van uw servicefabric-ontwikkelomgeving.](service-fabric-get-started.md)
2. Druk op **F5** of klik op **Foutopsporing** > **starten.**
   
    ![Een toepassing debuggen][startdebugging]
3. Stel breekpunten in uw code in en stap door de toepassing door op opdrachten in het menu **Foutopsporing** te klikken.
   
   > [!NOTE]
   > Visual Studio hecht aan alle exemplaren van uw toepassing. Terwijl u code doorloopt, kunnen breekpunten worden geraakt door meerdere processen, wat resulteert in gelijktijdige sessies. Probeer de breekpunten uit te schakelen nadat ze zijn geraakt, door elk breekpunt afhankelijk te maken van de thread-id of door diagnostische gebeurtenissen te gebruiken.
   > 
   > 
4. Het venster **Diagnostische gebeurtenissen** wordt automatisch geopend, zodat u diagnostische gebeurtenissen in realtime bekijken.
   
    ![Diagnostische gebeurtenissen in realtime weergeven][diagnosticevents]
5. U ook het venster **Diagnostische gebeurtenissen** openen in Cloud Explorer.  Klik **onder Service Fabric**met de rechtermuisknop op een knooppunt en kies **Streamingsporen weergeven.**
   
    ![Het venster diagnostische gebeurtenissen openen][viewdiagnosticevents]
   
    Als u uw traces wilt filteren op een specifieke service of toepassing, schakelt u streamingtraces in op die specifieke service of toepassing.
6. De diagnostische gebeurtenissen kunnen worden gezien in het automatisch gegenereerde **ServiceEventSource.cs** bestand en worden aangeroepen vanuit toepassingscode.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. Het venster **Diagnostische gebeurtenissen** ondersteunt het filteren, onderbreken en inspecteren van gebeurtenissen in realtime.  Het filter is een eenvoudige tekenreekszoekopdracht van het gebeurtenisbericht, inclusief de inhoud ervan.
   
    ![Gebeurtenissen in realtime filteren, onderbreken en hervatten of inspecteren][diagnosticeventsactions]
8. Foutopsporingsservices is als het debuggen van een andere toepassing. Normaal gesproken stel je Breakpoints in via Visual Studio voor eenvoudige debuggen. Hoewel betrouwbare verzamelingen zich over meerdere knooppunten repliceren, implementeren ze ienumerable nog steeds. Deze implementatie betekent dat u de resultatenweergave in Visual Studio gebruiken terwijl u debugt om te zien wat u binnen hebt opgeslagen. Stel hiervoor overal in je code een breekpunt in.
   
    ![Een toepassing debuggen][breakpoint]


### <a name="running-a-script-as-part-of-debugging"></a>Een script uitvoeren als onderdeel van foutopsporing
In bepaalde scenario's moet u mogelijk een script uitvoeren als onderdeel van het starten van een foutopsporingssessie (bijvoorbeeld wanneer u geen standaardservices gebruikt).

In Visual Studio u een bestand met de naam **Start-Service.ps1** toevoegen in de map **Scripts** van het project Service Fabric Application (.sfproj). Dit script wordt aangeroepen nadat de toepassing is gemaakt in het lokale cluster.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Een externe servicefabric-toepassing debuggen
Als uw Service Fabric-toepassingen worden uitgevoerd op een Cluster Servicefabric in Azure, u deze toepassingen op afstand debuggen, rechtstreeks vanuit Visual Studio.

> [!NOTE]
> De functie vereist [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) en [Azure SDK voor .NET 2.9](https://azure.microsoft.com/downloads/).    

<!-- -->
> [!WARNING]
> Het op afstand opsporen van debuggen is bedoeld voor dev/testscenario's en niet te gebruiken in productieomgevingen, vanwege de impact op de lopende toepassingen.

1. Navigeer naar uw cluster in **Cloud Explorer.** Klik met de rechtermuisknop en kies **Foutopsporing inschakelen**
   
    ![Extern foutopsporing inschakelen][enableremotedebugging]
   
    Met deze actie wordt het proces van het inschakelen van de externe foutopsporingsextensie op uw clusterknooppunten en vereiste netwerkconfiguraties afgetrapt.
2. Klik met de rechtermuisknop op het clusterknooppunt in **Cloud Explorer**en kies **Foutopsporing koppelen**
   
    ![Foutopsporing koppelen][attachdebugger]
3. Kies **in het** dialoogvenster Koppelen aan proces het proces dat u wilt debuggen en klik op **Bijvoegen**
   
    ![Proces kiezen][chooseprocess]
   
    De naam van het proces waaraan u wilt koppelen, is gelijk aan de naam van de naam van uw serviceprojectassemblage.
   
    De foutopsporing wordt gekoppeld aan alle knooppunten waarop het proces wordt uitgevoerd.
   
   * In het geval dat u een statusloze service debugt, maken alle exemplaren van de service op alle knooppunten deel uit van de foutopsporingssessie.
   * Als u een stateful service debugt, is alleen de primaire replica van een partitie actief en dus gevangen door de foutopsporing. Als de primaire replica tijdens de foutopsporingssessie wordt verplaatst, maakt de verwerking van die replica nog steeds deel uit van de foutopsporingssessie.
   * Als u alleen relevante partities of exemplaren van een bepaalde service wilt opvangen, u voorwaardelijke breekpunten gebruiken om alleen een specifieke partitie of instantie te breken.
     
     ![Voorwaardelijk breekpunt][conditionalbreakpoint]
     
     > [!NOTE]
     > Momenteel ondersteunen we het debuggen van een Service Fabric-cluster met meerdere exemplaren van dezelfde serviceuitvoernaam niet.
     > 
     > 
4. Zodra u klaar bent met het debuggen van uw toepassing, u de extensie voor foutopsporing op afstand uitschakelen door met de rechtermuisknop op het cluster in **Cloud Explorer** te klikken en **Foutopsporing uitschakelen te** kiezen
   
    ![Foutopsporing op afstand uitschakelen][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Streamingsporen van een extern clusterknooppunt
U ook rechtstreeks van een extern clusterknooppunt naar Visual Studio worden gestreamd. Met deze functie u ETW-tracegebeurtenissen streamen die zijn geproduceerd op een clusterknooppunt van Service Fabric.

> [!NOTE]
> Voor deze functie zijn [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) en [Azure SDK vereist voor .NET 2.9](https://azure.microsoft.com/downloads/).
> Deze functie ondersteunt alleen clusters die in Azure worden uitgevoerd.
> 
> 

<!-- -->
> [!WARNING]
> Streaming traces is bedoeld voor dev/test scenario's en niet te gebruiken in productieomgevingen, vanwege de impact op de lopende applicaties.
> In een productiescenario moet u vertrouwen op het doorsturen van gebeurtenissen met Azure Diagnostics.

1. Navigeer naar uw cluster in **Cloud Explorer.** Klik met de rechtermuisknop en kies **Streamingsporen inschakelen**
   
    ![Externe streamingtraceringen inschakelen][enablestreamingtraces]
   
    Met deze actie wordt het proces van het inschakelen van de extensie streamingsporen op uw clusterknooppunten en vereiste netwerkconfiguraties afgetrapt.
2. Het element **Knooppunten** uitvouwen in **Cloud Explorer,** klikt met de rechtermuisknop op het knooppunt waarvan u sporen wilt streamen en kies **Streamingsporen weergeven**
   
    ![Externe streamingtraceringen weergeven][viewremotestreamingtraces]
   
    Herhaal stap 2 voor zoveel knooppunten als u wilt sporen van te zien. Elke knooppuntenstream wordt weergegeven in een speciaal venster.
   
    U nu de sporen zien die door Service Fabric en uw diensten worden uitgestraald. Als u de gebeurtenissen wilt filteren om alleen een specifieke toepassing weer te geven, typt u de naam van de toepassing in het filter.
   
    ![Streamingtraceringen bekijken][viewingstreamingtraces]
3. Zodra u bent klaar met het streamen van traces van uw cluster, u externe streamingsporen uitschakelen door met de rechtermuisknop op het cluster in **Cloud Explorer** te klikken en **Streamingtraces uitschakelen** te kiezen
   
    ![Externe streamingsporen uitschakelen][disablestreamingtraces]

## <a name="next-steps"></a>Volgende stappen
* [Test een Service Fabric-service.](service-fabric-testability-overview.md)
* [Beheer uw Service Fabric-toepassingen in Visual Studio.](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
