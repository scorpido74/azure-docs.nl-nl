---
title: Productie-apps in Azure profiler profiler profiler en profiel
description: Identificeer het hot path in uw webservercode met een profiler met een lage voetafdruk.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ce952bd248640d03fcff43284707614577df8469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671644"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Productietoepassingen profileren in Azure met Toepassingsinzichten
## <a name="enable-application-insights-profiler-for-your-application"></a>Application Insights Profiler inschakelen voor uw toepassing

Azure Application Insights Profiler biedt prestatiesporen voor toepassingen die in productie worden uitgevoerd in Azure. Profiler legt de gegevens automatisch vast op schaal zonder uw gebruikers negatief te beïnvloeden. Profiler helpt u bij het identificeren van het 'hot'-codepad dat het langst duurt wanneer het een bepaalde webaanvraag verwerkt. 

Profiler werkt met .NET-toepassingen die zijn geïmplementeerd op de volgende Azure-services. In de onderstaande koppelingen vindt u specifieke instructies voor het inschakelen van Profiler voor elk servicetype.

* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines en virtuele machineschaalsets](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [**VOORBEELD** ASP.NET Core Azure Linux Web Apps](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Als u Profiler hebt ingeschakeld, maar geen sporen ziet, raadpleegt u de [handleiding Probleemoplossing.](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)

## <a name="view-profiler-data"></a>Profiler-gegevens weergeven

Als Profiler sporen kan uploaden, moet uw toepassing actief aanvragen afhandelen. Als u een experiment doet, u aanvragen voor uw web-app genereren met behulp van [Application Insights-prestatietests.](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test) Als u Profiler onlangs hebt ingeschakeld, u een korte belastingstest uitvoeren. Terwijl de belastingstest wordt uitgevoerd, selecteert u de knop **Profiel nu** in het deelvenster [ **Profiler-instellingen** ](profiler-settings.md). Wanneer Profiler wordt uitgevoerd, profileert het willekeurig ongeveer eenmaal per uur en voor een duur van twee minuten. Als uw toepassing een gestage stroom van aanvragen verwerkt, uploadt Profiler elk uur sporen.

Nadat uw aanvraag wat verkeer heeft ontvangen en Profiler de tijd heeft gehad om de sporen te uploaden, moet u sporen hebben om te bekijken. Dit proces kan 5 tot 10 minuten duren. Als u sporen wilt weergeven, selecteert u in het deelvenster **Uitvoering** de optie **Acties uitvoeren**en selecteert u de knop Sporen **profileren.**

![Voorbeeld van profiler-traces van het deelvenster Toepassingsinzichten][performance-blade]

Selecteer een voorbeeld om een uitsplitsing op codeniveau weer te geven van de tijd die is besteed aan het uitvoeren van de aanvraag.

![Trace explorer voor toepassingsinzichten][trace-explorer]

De traceerverkenner geeft de volgende informatie weer:

* **Toon Hot Path:** Opent het grootste bladknooppunt, of op zijn minst iets in de buurt. In de meeste gevallen is dit knooppunt in de buurt van een prestatieknelpunt.
* **Label:** de naam van de functie of gebeurtenis. De structuur toont een mix van code en gebeurtenissen die zijn opgetreden, zoals SQL- en HTTP-gebeurtenissen. De bovenste gebeurtenis vertegenwoordigt de totale aanvraagduur.
* **Verstreken**: Het tijdsinterval tussen het begin van de bewerking en het einde van de bewerking.
* **Wanneer**: Het tijdstip waarop de functie of gebeurtenis werd uitgevoerd ten opzichte van andere functies.

## <a name="how-to-read-performance-data"></a>Prestatiegegevens lezen

De Microsoft-serviceprofiler gebruikt een combinatie van bemonsteringsmethoden en instrumentatie om de prestaties van uw toepassing te analyseren. Wanneer de gedetailleerde verzameling aan de gang is, bemonstert de serviceprofiler elke milliseconde de instructieaanwijzer van elke machine-CPU. Elk voorbeeld legt de volledige callstack vast van de thread die momenteel wordt uitgevoerd. Het geeft gedetailleerde informatie over wat die draad deed, zowel op een hoog niveau als een laag abstractieniveau. De serviceprofiler verzamelt ook andere gebeurtenissen om de correlatie en causaliteit van activiteiten bij te houden, waaronder contextswitchinggebeurtenissen, TPL-gebeurtenissen (Task Parallel Library) en gebeurtenissen in de threadpool.

De oproepstack die wordt weergegeven in de tijdlijnweergave is het resultaat van de bemonstering en het instrumentatie. Omdat elk voorbeeld de volledige callstack van de thread vastlegt, bevat het code van Microsoft .NET Framework en van andere frameworks waarnaar u verwijst.

### <a name="object-allocation-clrjit_new-or-clrjit_newarr1"></a><a id="jitnewobj"></a>Objecttoewijzing (clr! JIT\_Nieuw of clr! JIT\_Newarr1)

**Clr! JIT\_Nieuw** en **clr! JIT\_Newarr1** zijn helperfuncties in .NET Framework die geheugen toewijzen vanaf een beheerde heap. **clr! JIT\_New** wordt aangeroepen wanneer een object wordt toegewezen. **clr! JIT\_Newarr1** wordt aangeroepen wanneer een objectarray wordt toegewezen. Deze twee functies zijn meestal snel en nemen relatief kleine hoeveelheden tijd in beslag. Als **clr! JIT\_Nieuw** of **clr! JIT\_Newarr1** neemt veel tijd in uw tijdlijn, de code kan het toewijzen van veel objecten en het verbruiken van aanzienlijke hoeveelheden geheugen.

### <a name="loading-code-clrtheprestub"></a><a id="theprestub"></a>Laadcode (clr! ThePrestub)

**clr! ThePreStub** is een helperfunctie in .NET Framework die de code voorbereidt om voor de eerste keer uit te voeren. Deze uitvoering omvat meestal, maar is niet beperkt tot, just-in-time (JIT) compilatie. Voor elke C# methode, **clr! De PreStub** moet maximaal één keer tijdens een proces worden aangeroepen.

Als **clr! DePreStub** duurt lang voor een aanvraag, de aanvraag is de eerste die die methode uitvoert. De tijd voor .NET Framework-runtime om de eerste methode te laden is aanzienlijk. U overwegen een opwarmproces te gebruiken dat dat gedeelte van de code uitvoert voordat uw gebruikers deze openen, of overwegen native imagegenerator (ngen.exe) uit te voeren op uw verzamelingen.

### <a name="lock-contention-clrjitutil_moncontention-or-clrjitutil_monenterworker"></a><a id="lockcontention"></a>Lock stelling (clr! JITutil\_MonContention of clr! JITutil\_MonEnterWorker)

**Clr! JITutil\_MonContention** of **clr! JITutil\_MonEnterWorker** geeft aan dat de huidige thread wacht op een vergrendeling. Deze tekst wordt vaak weergegeven wanneer u een **C#LOCK-instructie** uitvoert, de **methode Monitor.Enter** aanroept of een methode aanroept met het kenmerk **MethodImplOptions.Synchronized.** Lock geschil treedt meestal op wanneer draad _A_ verwerft een slot en draad _B_ probeert te verwerven hetzelfde slot voordat draad _A_ releases.

### <a name="loading-code-cold"></a><a id="ngencold"></a>Laadcode ([COLD])

Als de methodenaam **[COLD]** bevat, zoals **mscorlib.ni![ COLD]System.Reflection.CustomAttribute.IsDefined**, .NET Framework runtime is executing code for the first time that isn't optimized by [profile-guided optimization](/cpp/build/profile-guided-optimizations). Voor elke methode moet het ten hoogste één keer tijdens het proces worden weergegeven.

Als laadcode een aanzienlijke hoeveelheid tijd in beslag neemt voor een aanvraag, is de aanvraag de eerste die het niet-geoptimaliseerde gedeelte van de methode uitvoert. Overweeg een opwarmproces te gebruiken dat dat gedeelte van de code uitvoert voordat uw gebruikers er toegang toe krijgen.

### <a name="send-http-request"></a><a id="httpclientsend"></a>HTTP-aanvraag verzenden

Methoden zoals **HttpClient.Send** geven aan dat de code wacht tot een HTTP-aanvraag is voltooid.

### <a name="database-operation"></a><a id="sqlcommand"></a>Databasebewerking

Methoden zoals **SqlCommand.Execute** geven aan dat de code wacht tot een databasebewerking is voltooid.

### <a name="waiting-await_time"></a><a id="await"></a>Wachten (WACHT TIJD)\_

**Wachttijd\_** geeft aan dat de code wacht op een andere taak. Deze vertraging gebeurt meestal met de C # **WACHTEN** verklaring. Wanneer de code doet een C # **WACHTEN,** de draad afwikkelt en keert de controle naar de thread pool, en er is geen draad die is geblokkeerd te wachten tot de **WACHT** te voltooien. Echter, logisch, de draad die deed de **WACHT** is "geblokkeerd," en het is wachten tot de operatie te voltooien. De instructie **WACHTTIJD\_TIJD** geeft de geblokkeerde tijd aan die wacht tot de taak is voltooid.

### <a name="blocked-time"></a><a id="block"></a>Geblokkeerde tijd

**BLOCKED_TIME** geeft aan dat de code wacht tot er een andere resource beschikbaar is. Het kan bijvoorbeeld wachten tot een synchronisatieobject beschikbaar is, dat een thread beschikbaar is of dat een aanvraag is voltooid.

### <a name="unmanaged-async"></a>Onbeheerde Async

.NET framework zendt ETW-gebeurtenissen uit en geeft activiteits-id's door tussen threads, zodat async-aanroepen over threads kunnen worden gevolgd. Onbeheerde code (native code) en sommige oudere stijlen van asynchrone code missen deze gebeurtenissen en activiteits-id's, zodat de profiler niet kan zien welke thread en welke functies op de thread worden uitgevoerd. Dit heeft het label 'Unmanaged Async' in de callstack. Als u het ETW-bestand downloadt, u [PerfView](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md) gebruiken om meer inzicht te krijgen in wat er gebeurt.

### <a name="cpu-time"></a><a id="cpu"></a>CPU-tijd

De CPU is bezig met het uitvoeren van de instructies.

### <a name="disk-time"></a><a id="disk"></a>Schijftijd

De toepassing voert schijfbewerkingen uit.

### <a name="network-time"></a><a id="network"></a>Netwerktijd

De toepassing voert netwerkbewerkingen uit.

### <a name="when-column"></a><a id="when"></a>Wanneer kolom

De kolom **Wanneer** is een visualisatie van hoe de inclusieve monsters die voor een knooppunt zijn verzameld, in de loop van de tijd variëren. Het totale bereik van de aanvraag is verdeeld in 32 tijdbuckets. De inclusieve monsters voor dat knooppunt worden verzameld in die 32 emmers. Elke emmer wordt weergegeven als een balk. De hoogte van de balk vertegenwoordigt een geschaalde waarde. Voor knooppunten die zijn gemarkeerd **CPU_TIME** of **BLOCKED_TIME**of wanneer er een duidelijke relatie is met het consumeren van een resource (bijvoorbeeld een CPU, schijf of thread), vertegenwoordigt de balk het verbruik van een van de resources tijdens de bucket. Voor deze statistieken is het mogelijk om een waarde van meer dan 100 procent te krijgen door meerdere resources te verbruiken. Als u bijvoorbeeld gemiddeld twee CPU's gebruikt tijdens een interval, krijgt u 200 procent.

## <a name="limitations"></a>Beperkingen

De standaardbewaarperiode voor gegevens is vijf dagen. De maximale gegevens die per dag worden ingenomen, zijn 10 GB.

Er zijn geen kosten verbonden aan het gebruik van de Profiler-service. Als u deze wilt gebruiken, moet uw web-app worden gehost in ten minste de basislaag van de Web Apps-functie van Azure App Service.

## <a name="overhead-and-sampling-algorithm"></a>Overhead- en bemonsteringsalgoritme

Profiler loopt willekeurig twee minuten per uur op elke virtuele machine die de toepassing host die Profiler heeft ingeschakeld voor het vastleggen van sporen. Wanneer Profiler wordt uitgevoerd, voegt het van 5 tot 15 procent CPU overhead toe aan de server.

## <a name="next-steps"></a>Volgende stappen
Application Insights Profiler inschakelen voor uw Azure-toepassing. Zie ook:
* [App Services](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines en virtuele machineschaalsets](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
