---
title: Problemen met Azure Application Insights Profiler oplossen
description: In dit artikel worden stappen en informatie over probleemoplossing gepresenteerd om ontwikkelaars te helpen die problemen hebben met het inschakelen of gebruiken van Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: f284d4dfbe550c357f81c01fa0a66aa9878b6c1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671559"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Problemen oplossen bij het inschakelen of weergeven van Application Insights Profiler

## <a name="active-issues"></a>Actieve problemen

* Profilering voor ASP.NET Core 3.x-toepassingen wordt nog niet ondersteund.
  * Als u Profiler aan moet hebben, moet u [Application Insights Profiler gebruiken voor ASP.NET Core.](https://github.com/microsoft/ApplicationInsights-Profiler-AspNetCore) De profiler is gelabeld voor Linux, maar het werkt ook met .NET Core 3.0 + applicaties op Windows. Zie Ondersteunde [versies](https://github.com/microsoft/ApplicationInsights-Profiler-AspNetCore#supported-versions)voor meer informatie.

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Algemene probleemoplossing

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profielen worden alleen geüpload als er aanvragen zijn voor uw toepassing terwijl Profiler wordt uitgevoerd

Azure Application Insights Profiler verzamelt profileringsgegevens gedurende twee minuten per uur. Het verzamelt ook gegevens wanneer u de knop **Profiel nu** selecteert in het deelvenster Profiler **van toepassingsinzichten configureren.** Maar de profileringsgegevens worden alleen geüpload wanneer deze kunnen worden gekoppeld aan een verzoek dat is uitgevoerd terwijl Profiler werd uitgevoerd. 

Profiler schrijft traceberichten en aangepaste gebeurtenissen naar uw Application Insights-bron. U deze gebeurtenissen gebruiken om te zien hoe Profiler wordt uitgevoerd. Als u denkt dat Profiler sporen moet uitvoeren en vastleggen, maar deze niet worden weergegeven in het deelvenster **Prestaties,** u controleren hoe Profiler wordt uitgevoerd:

1. Zoek naar traceerberichten en aangepaste gebeurtenissen die door Profiler naar uw Application Insights-bron zijn verzonden. U deze zoektekenreeks gebruiken om de relevante gegevens te vinden:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    In de volgende afbeelding worden twee voorbeelden weergegeven van zoekopdrachten uit twee AI-bronnen: 
    
   * Aan de linkerkant ontvangt de toepassing geen aanvragen terwijl Profiler wordt uitgevoerd. In het bericht wordt uitgelegd dat de upload is geannuleerd omdat er geen activiteit is. 

   * Aan de rechterkant startte en verzond Profiler aangepaste gebeurtenissen toen het aanvragen detecteerde die plaatsvonden terwijl Profiler werd uitgevoerd. Als de aangepaste gebeurtenis ServiceProfilerSample wordt weergegeven, betekent dit dat Profiler een trace aan een aanvraag heeft gekoppeld en dat u het traceringsvenster bekijken in het **deelvenster Prestatievenster Voor toepassingsinzichten.**

     Als er geen telemetrie wordt weergegeven, wordt Profiler niet uitgevoerd. Zie de secties voor het oplossen van problemen voor uw specifieke app-type later in dit artikel als u problemen wilt oplossen.  

     ![Profiler-telemetrie zoeken][profiler-search-telemetry]

1. Als er aanvragen zijn uitgevoerd terwijl Profiler is uitgevoerd, moet u ervoor zorgen dat de aanvragen worden afgehandeld door het deel van uw toepassing dat Profiler heeft ingeschakeld. Hoewel toepassingen soms uit meerdere componenten bestaan, is Profiler slechts voor een deel van de componenten ingeschakeld. In het deelvenster **Profiler van toepassingsinzichten configureren** worden de onderdelen weergegeven die sporen hebben geüpload.

### <a name="other-things-to-check"></a>Andere dingen om te controleren
* Controleer of uw app wordt uitgevoerd op .NET Framework 4.6.
* Als uw web-app een ASP.NET Core-toepassing is, moet deze ten minste ASP.NET Core 2.0 worden uitgevoerd.
* Als de gegevens die u probeert weer te geven ouder zijn dan een paar weken, probeert u uw tijdfilter te beperken en probeert u het opnieuw. Sporen worden na zeven dagen verwijderd.
* Zorg ervoor dat proxy's of een firewall https://gateway.azureserviceprofiler.netde toegang tot .
* Profiler wordt niet ondersteund in gratis of gedeelde app-serviceplannen. Als u een van deze plannen gebruikt, probeert u opschalen naar een van de basisplannen en moet Profiler gaan werken.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Dubbel tellen in parallelle draden

In sommige gevallen is de totale tijdstatistiek in de stackviewer groter dan de duur van de aanvraag.

Deze situatie kan zich voordoen wanneer twee of meer threads zijn gekoppeld aan een aanvraag en ze parallel werken. In dat geval is de totale draadtijd meer dan de verstreken tijd. Een draad kan wachten op de andere te voltooien. De kijker probeert deze situatie te detecteren en laat het oninteressante wachten weg. Daarbij, het errs aan de kant van het weergeven van te veel informatie in plaats van weg te laten wat zou kunnen worden kritische informatie.

Wanneer u parallelle draden in uw sporen ziet, bepaalt u welke threads wachten, zodat u het kritieke pad voor het verzoek vaststellen. Meestal is de draad die snel gaat in een wachtstaat is gewoon te wachten op de andere threads. Concentreer je op de andere threads en negeer de tijd in de wachtende draden.

### <a name="error-report-in-the-profile-viewer"></a>Foutrapport in de profielviewer
Stuur een ondersteuningsticket in in de portal. Zorg ervoor dat u de correlatie-ID van het foutbericht opneemt.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Problemen met Profiler oplossen op Azure App Service
Als profiler goed kan werken:
* Uw web-app-serviceabonnement moet basic-laag of hoger zijn.
* Uw web-app moet Application Insights hebben ingeschakeld.
* Uw web-app moet de volgende app-instellingen hebben:

    |App-instelling    | Waarde    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey voor uw Application Insights-bron    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* De **ApplicationInsightsProfiler3** webjob moet worden uitgevoerd. Ga als een te gekeer naar de webjob:
   1. Ga naar [Kudu.](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/)
   1. Selecteer **webjobs-dashboard**in het menu **Extra** .  
      Het deelvenster **WebJobs** wordt geopend. 
   
      ![profiler-webjob]   
   
   1. Als u de details van de webjob wilt bekijken, inclusief het logboek, selecteert u de koppeling **ApplicationInsightsProfiler3.**  
     Het deelvenster **Continue WebJob-details** wordt geopend.

      ![profiler-webjob-log]

Als u niet weet waarom Profiler niet voor u werkt, u het logboek downloaden serviceprofilerhelp@microsoft.comen naar ons team sturen voor hulp,. 
    
### <a name="manual-installation"></a>Handmatige installatie

Wanneer u Profiler configureert, worden de instellingen van de web-app bijgewerkt. Als uw omgeving dit vereist, u de updates handmatig toepassen. Een voorbeeld hiervan is dat uw toepassing wordt uitgevoerd in een Web Apps-omgeving voor PowerApps. Ga als u updates handmatig toepassen:

1. Open Instellingen in het deelvenster Besturingselement **voor** **webapps** .

1. Stel **.NET Framework-versie** in op **v4.6**.

1. **Altijd instellen op** **Aan**.
1. Maak deze app-instellingen:

    |App-instelling    | Waarde    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey voor uw Application Insights-bron    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>Te veel actieve profileringssessies

Momenteel u Profiler inschakelen op maximaal vier Azure-webapps en implementatiesleuven die in hetzelfde serviceplan worden uitgevoerd. Als u meer dan vier web-apps in één app-serviceabonnement hebt uitgevoerd, kan Profiler een *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException*gooien. Profiler wordt afzonderlijk uitgevoerd voor elke web-app en probeert voor elke app een Event Tracing for Windows (ETW)-sessie te starten. Maar een beperkt aantal ETW-sessies kan in één keer actief zijn. Als de WebJob van Profiler te veel actieve profileringssessies rapporteert, verplaatst u sommige web-apps naar een ander serviceplan.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Implementatiefout: Directory Not Empty\\\\'D:\\\\home site\\wwwroot App_Data jobs'

Als u uw web-app opnieuw implementeert naar een Web Apps-bron met Profiler ingeschakeld, ziet u mogelijk het volgende bericht:

*Directory Not Empty\\'D:\\home\\\\site\\wwwroot App_Data jobs'*

Deze fout treedt op als u Webdeploy uitvoert vanuit scripts of vanuit de Azure DevOps-implementatiepijplijn. De oplossing is om de volgende extra implementatieparameters toe te voegen aan de webimplementatietaak:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Met deze parameters wordt de map verwijderd die wordt gebruikt door Application Insights Profiler en wordt het proces voor het opnieuw implementeren gedeblokkeerd. Ze hebben geen invloed op de instantie Profiler die momenteel wordt uitgevoerd.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Hoe bepaal ik of Application Insights Profiler actief is?

Profiler draait als een continue webjob in de web-app. U de web-app-bron openen in de [Azure-portal.](https://portal.azure.com) Controleer in het deelvenster **WebJobs** de status van **ApplicationInsightsProfiler**. Als deze niet wordt uitgevoerd, opent u **Logboeken** om meer informatie te krijgen.

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>Problemen met Profiler en Azure Diagnostics oplossen

>**De bug in de profiler die schepen in de WAD voor Cloud Services is opgelost.** De nieuwste versie van WAD (1.12.2.0) voor Cloud Services werkt met alle recente versies van de App Insights SDK. Cloud Service-hosts upgraden WAD automatisch, maar het is niet onmiddellijk. Als u een upgrade wilt forceren, u uw service opnieuw implementeren of het knooppunt opnieuw opstarten.

Ga als volgt te werk om te zien of Profiler correct is geconfigureerd door Azure Diagnostics: 
1. Controleer eerst of de inhoud van de Azure Diagnostics-configuratie die is geïmplementeerd, is wat u verwacht. 

1. Controleer ten tweede of Azure Diagnostics de juiste iKey doorgeeft op de opdrachtregel Profiler. 

1. Controleer ten derde het logboekbestand Profiler om te zien of Profiler is uitgevoerd, maar een fout heeft geretourneerd. 

Ga als eerste voor de instellingen die zijn gebruikt om Azure Diagnostics te configureren:

1. Meld u aan bij de virtuele machine (VM) en open het logboekbestand op deze locatie. (Het station kan worden c: of d: en de plugin versie kan anders zijn.)

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```
    of
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```

1. In het bestand u zoeken naar de tekenreeks **WadCfg** om de instellingen te vinden die zijn doorgegeven aan de VM om Azure Diagnostics te configureren. U controleren of de iKey die door de Profiler-gootsteen wordt gebruikt, correct is.

1. Controleer de opdrachtregel die wordt gebruikt om Profiler te starten. De argumenten die worden gebruikt om Profiler te starten, staan in het volgende bestand. (Het station kan c: of d:)

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Controleer of de iKey op de opdrachtregel Profiler correct is. 

1. Controleer het logboekbestand Profiler met behulp van het pad in het vorige *bestand config.json.* Het toont de foutopsporingsgegevens die de instellingen aangeeft die Profiler gebruikt. Het toont ook status- en foutmeldingen van Profiler.  

    Als Profiler wordt uitgevoerd terwijl uw toepassing aanvragen ontvangt, wordt het volgende bericht weergegeven: *Activiteit gedetecteerd vanaf iKey*. 

    Wanneer het traceeringwordt geüpload, wordt het volgende bericht weergegeven: *Start om tracering te uploaden*. 


## <a name="edit-network-proxy-or-firewall-rules"></a>Netwerkproxy- of firewallregels bewerken

Als uw toepassing verbinding maakt met internet via een proxy of een firewall, moet u mogelijk de regels bewerken om uw toepassing te laten communiceren met de Application Insights Profiler-service. De IP's die worden gebruikt door Application Insights Profiler zijn opgenomen in de Azure Monitor-servicetag.


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








