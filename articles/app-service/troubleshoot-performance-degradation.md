---
title: Prestatiedegradatie oplossen
description: Ontdek hoe u trage problemen met de prestaties van apps in Azure App Service oplossen, waaronder het bewaken van app-gedrag, het verzamelen van gegevens en het beperken van het probleem.
tags: top-support-issue
keywords: web app prestaties, trage app, app traag
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.topic: article
ms.date: 08/03/2016
ms.custom: seodec18
ms.openlocfilehash: 98c11a72b5aea0fac15d943977402289dc33a970
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688305"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>Problemen met trage app-prestaties in Azure App-service oplossen
Met dit artikel u trage problemen met de prestaties van apps oplossen in [Azure App Service.](https://go.microsoft.com/fwlink/?LinkId=529714)

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op [de MSDN Azure- en de Stack Overflow-forums.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure Support-site](https://azure.microsoft.com/support/options/) en klik op **Ondersteuning krijgen**.

## <a name="symptom"></a>Symptoom
Wanneer u door de app bladert, worden de pagina's langzaam en soms een time-out geladen.

## <a name="cause"></a>Oorzaak
Dit probleem wordt vaak veroorzaakt door problemen op toepassingsniveau, zoals:

* netwerkaanvragen die lang duren
* toepassingscode of databasequery's die inefficiënt zijn
* toepassing met behulp van een hoog geheugen /CPU
* toepassing crashen als gevolg van een uitzondering

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
Probleemoplossing kan worden onderverdeeld in drie verschillende taken, in opeenvolgende volgorde:

1. [Toepassingsgedrag observeren en controleren](#observe)
2. [Gegevens verzamelen](#collect)
3. [Het probleem beperken](#mitigate)

[App Service](overview.md) biedt u verschillende opties bij elke stap.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Het gedrag van toepassingen observeren en controleren
#### <a name="track-service-health"></a>De status van de service bijhouden
Microsoft Azure maakt elke keer bekend wanneer er een onderbreking van de service of prestatiedegradatie is. U de status van de service volgen op de [Azure-portal.](https://portal.azure.com/) Zie [De status van de service bijhouden](../monitoring-and-diagnostics/insights-service-health.md)voor meer informatie.

#### <a name="monitor-your-app"></a>Uw app controleren
Met deze optie u zien of uw toepassing problemen heeft. Klik in het blad van uw app op de tegel **Aanvragen en fouten.** Het **metrische** blad toont u alle statistieken die u toevoegen.

Enkele van de statistieken die u mogelijk wilt controleren voor uw app, zijn

* Gemiddelde geheugenwerkset
* Gemiddelde responstijd
* CPU-tijd
* Geheugenwerkset
* Aanvragen

![app-prestaties bewaken](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Zie voor meer informatie:

* [Apps controleren in Azure App Service](web-sites-monitor.md)
* [Waarschuwingen ontvangen](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Webeindpuntstatus controleren
Als u uw app **Standard** uitvoert in de standaardprijslaag, u met App Service twee eindpunten vanaf drie geografische locaties controleren.

Endpoint-bewaking configureert webtests vanaf geogedistribueerde locaties die de responstijd en uptime van web-URL's testen. De test voert een HTTP GET-bewerking uit op de web-URL om de reactietijd en uptime vanaf elke locatie te bepalen. Elke geconfigureerde locatie voert elke vijf minuten een test uit.

Uptime wordt gecontroleerd met BEHULP van HTTP-antwoordcodes en de reactietijd wordt gemeten in milliseconden. Een monitoringtest mislukt als de HTTP-responscode groter is dan of gelijk is aan 400 of als de respons meer dan 30 seconden duurt. Een eindpunt wordt als beschikbaar beschouwd als de bewakingstests slagen vanaf alle opgegeven locaties.

Zie Apps controleren [in Azure App Service](web-sites-monitor.md)als u dit wilt instellen.

Zie [Azure-websites behouden plus Endpoint-controle - met Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) voor een video over endpoint-monitoring.

#### <a name="application-performance-monitoring-using-extensions"></a>Toepassingsprestatiebewaking met extensies
U ook de prestaties van uw toepassing controleren met behulp van een *site-extensie.*

Elke App Service-app biedt een uitbreidbaar beheereindpunt waarmee u een krachtige set hulpprogramma's gebruiken die als site-extensies worden geïmplementeerd. Extensies zijn: 

- Broncodeeditors zoals [Azure DevOps](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Beheertools voor verbonden bronnen, zoals een MySQL-database die is verbonden met een app.

[Azure Application Insights](https://azure.microsoft.com/services/application-insights/) is een prestatiebewaking site extensie die ook beschikbaar is. Als u Application Insights wilt gebruiken, bouwt u uw code opnieuw op met een SDK. U ook een extensie installeren die toegang biedt tot extra gegevens. Met de SDK u code schrijven om het gebruik en de prestaties van uw app in meer detail te controleren. Zie [Prestaties controleren in webtoepassingen voor](../azure-monitor/app/web-monitor-performance.md)meer informatie.

<a name="collect" />

### <a name="2-collect-data"></a>2. Gegevens verzamelen
App Service biedt diagnostische functionaliteit voor het registreren van informatie van zowel de webserver als de webtoepassing. De informatie is gescheiden in webserverdiagnostiek en toepassingsdiagnostiek.

#### <a name="enable-web-server-diagnostics"></a>Webserverdiagnose inschakelen
U de volgende soorten logboeken in- of uitschakelen:

* **Gedetailleerde foutlogboekregistratie** - Gedetailleerde foutgegevens voor HTTP-statuscodes die wijzen op een fout (statuscode 400 of hoger). Dit kan informatie bevatten die kan helpen bepalen waarom de server de foutcode heeft geretourneerd.
* **Tracering van mislukte aanvragen** - Gedetailleerde informatie over mislukte aanvragen, inclusief een spoor van de IIS-componenten die worden gebruikt om de aanvraag te verwerken en de tijd die in elk onderdeel is genomen. Dit kan handig zijn als u probeert de prestaties van apps te verbeteren of te isoleren wat een specifieke HTTP-fout veroorzaakt.
* **Webserverlogboekregistratie** - Informatie over HTTP-transacties met de uitgebreide w3C-bestandsindeling. Dit is handig bij het bepalen van algemene app-statistieken, zoals het aantal afgehandelde aanvragen of het aantal aanvragen dat afkomstig is van een specifiek IP-adres.

#### <a name="enable-application-diagnostics"></a>Toepassingsdiagnose inschakelen
Er zijn verschillende opties om prestatiegegevens van toepassingen van App Service te verzamelen, uw toepassing live te profileren vanuit Visual Studio of uw toepassingscode aan te passen om meer informatie en traces te registreren. U de opties kiezen op basis van hoeveel toegang u hebt tot de toepassing en wat u hebt waargenomen vanuit de bewakingstools.

##### <a name="use-application-insights-profiler"></a>Application Insights Profiler gebruiken
U de Application Insights Profiler inschakelen om gedetailleerde prestatiesporen vast te leggen. U toegang krijgen tot sporen die tot vijf dagen geleden zijn vastgelegd wanneer u problemen in het verleden moet onderzoeken. U deze optie kiezen zolang u toegang hebt tot de Application Insights-bron van de app op Azure-portal.

Application Insights Profiler biedt statistieken over de reactietijd voor elke weboproep en traceringen die aangeeft welke regel code de trage reacties heeft veroorzaakt. Soms is de App Service-app traag omdat bepaalde code niet op een performante manier is geschreven. Voorbeelden hiervan zijn sequentiële code die parallel en ongewenste databaselock-stellingen kan worden uitgevoerd. Het verwijderen van deze knelpunten in de code verhoogt de prestaties van de app, maar ze zijn moeilijk te detecteren zonder het instellen van uitgebreide sporen en logboeken. De sporen verzameld door Application Insights Profiler helpt bij het identificeren van de regels code die de toepassing vertraagt en deze uitdaging voor App Service-apps te overwinnen.

 Zie [Live-apps profileren in Azure App Service met Application Insights](../azure-monitor/app/profiler.md)voor meer informatie.

##### <a name="use-remote-profiling"></a>Externe profilering gebruiken
In Azure App Service kunnen web-apps, API-apps, mobiele back-ends en WebJobs op afstand worden geprofileerd. Kies deze optie als u toegang hebt tot de app-bron en u weet hoe u het probleem moet reproduceren of als u het exacte tijdsinterval weet, treedt het prestatieprobleem op.

Remote Profiling is handig als het CPU-gebruik van het proces hoog is en uw proces trager werkt dan verwacht, of de latentie van HTTP-aanvragen hoger is dan normaal, u uw proces op afstand profileren en de CPU-selectiecallstacks krijgt om het proces te analyseren activiteit en code hot paths.

Zie [Ondersteuning voor externe profilering in Azure App Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service)voor meer informatie.

##### <a name="set-up-diagnostic-traces-manually"></a>Diagnostische sporen handmatig instellen
Als u toegang hebt tot de broncode van de webtoepassing, u met toepassingsdiagnose informatie vastleggen die door een webtoepassing is geproduceerd. ASP.NET toepassingen kunnen `System.Diagnostics.Trace` de klasse gebruiken om informatie aan te melden bij het logboek voor toepassingsdiagnostiek. U moet de code echter wijzigen en uw toepassing opnieuw implementeren. Deze methode wordt aanbevolen als uw app wordt uitgevoerd op een testomgeving.

Zie [Diagnostische logboekregistratie inschakelen voor apps in Azure App Service](troubleshoot-diagnostic-logs.md)voor gedetailleerde instructies over het configureren van uw toepassing voor logboekregistratie.

#### <a name="use-the-diagnostics-tool"></a>Het diagnosegereedschap gebruiken
App Service biedt een intelligente en interactieve ervaring om u te helpen problemen op te lossen met uw app zonder configuratie vereist. Wanneer u problemen met uw app tegenkomt, geeft de diagnostische tool aan wat er mis is om u naar de juiste informatie te leiden om het probleem gemakkelijker en sneller op te lossen en op te lossen.

Als u toegang wilt krijgen tot diagnostische gegevens van App Service, navigeert u naar uw App Service-app of App Service-omgeving in de [Azure-portal.](https://portal.azure.com) Klik in de linkernavigatie op **Diagnosticeren en los problemen op.**

#### <a name="use-the-kudu-debug-console"></a>De Kudu-foutopsporingsconsole gebruiken
App Service wordt geleverd met een foutopsporingsconsole die u gebruiken voor het debuggen, verkennen, uploaden van bestanden, evenals JSON-eindpunten voor het verkrijgen van informatie over uw omgeving. Deze console wordt de *Kudu-console* of het *SCM-dashboard* voor uw app genoemd.

U dit dashboard openen door naar de link te gaan **https://&lt;Uw app-naam>.scm.azurewebsites.net/**.

Enkele van de dingen die Kudu biedt zijn:

* omgevingsinstellingen voor uw toepassing
* logboekstroom
* diagnostische dump
* foutopsporingsconsole waarin u Powershell-cmdlets en eenvoudige DOS-opdrachten uitvoeren.

Een ander handig kenmerk van Kudu is dat, in het geval uw toepassing is het gooien van eerste kans uitzonderingen, u Kudu en de SysInternals tool Procdump gebruiken om geheugen dumps te maken. Deze geheugendumps zijn momentopnamen van het proces en kunnen u vaak helpen bij het oplossen van meer gecompliceerde problemen met uw app.

Zie [Azure DevOps-hulpprogramma's die u moet weten](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/)voor meer informatie over functies die beschikbaar zijn in Kudu.

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Het probleem beperken
#### <a name="scale-the-app"></a>De app schalen
In Azure App Service u voor betere prestaties en doorvoer de schaal aanpassen waarop u uw toepassing uitvoert. Het opschalen van een app omvat twee gerelateerde acties: het wijzigen van uw App Service-abonnement naar een hogere prijscategorie en het configureren van bepaalde instellingen nadat u bent overgestapt naar de hogere prijscategorie.

Zie [Een app schalen in Azure App Service](manage-scale-up.md)voor meer informatie over schalen.

Bovendien u ervoor kiezen om uw toepassing op meer dan één exemplaar uit te voeren. Uitschalen biedt u niet alleen meer verwerkingsmogelijkheden, maar geeft u ook een zekere mate van fouttolerantie. Als het proces op één instantie wordt afgebroken, blijven de andere instanties aanvragen weergeven.

U de schaling instellen als Handmatig of automatisch.

#### <a name="use-autoheal"></a>AutoHeal gebruiken
AutoHeal recyclet het werkproces voor uw app op basis van instellingen die u kiest (zoals configuratiewijzigingen, aanvragen, limieten op basis van geheugen of de tijd die nodig is om een aanvraag uit te voeren). De meeste van de tijd, recyclen van het proces is de snelste manier om te herstellen van een probleem. Hoewel u de app altijd rechtstreeks vanuit de Azure-portal opnieuw starten, doet AutoHeal dit automatisch voor u. Het enige wat u hoeft te doen is een aantal triggers toevoegen in de root web.config voor uw app. Deze instellingen zouden op dezelfde manier werken, zelfs als uw toepassing geen .NET-app is.

Zie [Azure-websites voor](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/)meer informatie .

#### <a name="restart-the-app"></a>De app opnieuw starten
Opnieuw opstarten is vaak de eenvoudigste manier om te herstellen van eenmalige problemen. Op de [Azure-portal](https://portal.azure.com/), op het blad van uw app, hebt u de opties om uw app te stoppen of opnieuw te starten.

 ![app opnieuw opstarten om prestatieproblemen op te lossen](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

U uw app ook beheren met Azure Powershell. Zie [Azure PowerShell gebruiken met Azure Resource Manager](../powershell-azure-resource-manager.md) voor meer informatie.
