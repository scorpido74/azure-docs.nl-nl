---
title: Diagnostische logboek registratie inschakelen voor apps-Azure App Service
description: Lees hoe u diagnostische logboek registratie kunt inschakelen en instrumentatie kunt toevoegen aan uw toepassing, en hoe u toegang hebt tot de gegevens die zijn geregistreerd door Azure.
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: af6d8b61c5d49ae219e90513abb93185f957222e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074061"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Diagnostische logboek registratie inschakelen voor apps in Azure App Service
## <a name="overview"></a>Overzicht
Azure biedt ingebouwde diagnostische gegevens om te helpen bij het opsporen van fouten in een [app service-app](https://go.microsoft.com/fwlink/?LinkId=529714). In dit artikel leert u hoe u diagnostische logboek registratie kunt inschakelen en instrumentatie kunt toevoegen aan uw toepassing, en hoe u toegang hebt tot de gegevens die zijn geregistreerd door Azure.

In dit artikel wordt gebruikgemaakt van de [Azure Portal](https://portal.azure.com) en Azure CLI voor het werken met Diagnostische logboeken. Zie [problemen met Azure in Visual Studio oplossen](troubleshoot-dotnet-visual-studio.md)voor meer informatie over het werken met Diagnostische logboeken met Visual Studio.

## <a name="whatisdiag"></a>Diagnostische gegevens van webservers en Application Diagnostics
App Service biedt diagnostische functionaliteit voor het vastleggen van logboek gegevens van zowel de webserver als de webtoepassing. Deze worden logisch gescheiden in **webserver diagnostiek** en **toepassings diagnoses**.

### <a name="web-server-diagnostics"></a>Diagnostische gegevens van webserver
U kunt de volgende soorten logboeken in-of uitschakelen:

* **Gedetailleerde fout registratie** : gedetailleerde informatie voor elke aanvraag die resulteert in de HTTP-status code 400 of hoger. Het kan informatie bevatten die kan helpen om te bepalen waarom de server de fout code heeft geretourneerd. Er wordt één HTML-bestand voor elke fout gegenereerd in het bestands systeem van de app en er zijn Maxi maal 50 fouten (bestanden) behouden. Wanneer het aantal HTML-bestanden groter is dan 50, worden de oudste 26 bestanden automatisch verwijderd.
* **Tracering van mislukte aanvragen** -gedetailleerde informatie over mislukte aanvragen, inclusief een tracering van de IIS-onderdelen die worden gebruikt om de aanvraag te verwerken en de tijd die in elk onderdeel is gemaakt. Het is handig als u de prestaties van de site wilt verbeteren of een specifieke HTTP-fout wilt isoleren. Er wordt één map gegenereerd voor elke fout in het bestands systeem van de app. Het Bewaar beleid voor bestanden is hetzelfde als de gedetailleerde fout logboek registratie hierboven.
* **Logboek registratie** van webserver-informatie over http-trans acties met de [uitgebreide W3C-indeling van logboek bestand](/windows/desktop/Http/w3c-logging). Het is handig bij het bepalen van de algemene metrische site gegevens, zoals het aantal verwerkte aanvragen of hoeveel aanvragen van een specifiek IP-adres.

### <a name="application-diagnostics"></a>Application diagnostics
Met Application Diagnostics kunt u gegevens vastleggen die zijn geproduceerd door een webtoepassing. ASP.NET-toepassingen kunnen de klasse [System. Diagnostics. trace](/dotnet/api/system.diagnostics.trace) gebruiken om informatie te registreren in het logboek voor toepassings diagnose. Bijvoorbeeld:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Tijdens runtime kunt u deze logboeken ophalen om u te helpen bij het oplossen van problemen. Zie [problemen met Azure app service oplossen in Visual Studio](troubleshoot-dotnet-visual-studio.md)voor meer informatie.

App Service registreert ook implementatie gegevens wanneer u inhoud naar een app publiceert. Dit gebeurt automatisch en er zijn geen configuratie-instellingen voor de registratie van implementaties. Met de implementatie registratie kunt u bepalen waarom een implementatie is mislukt. Als u bijvoorbeeld een aangepast implementatie script gebruikt, kunt u de implementatie logboek registratie gebruiken om te bepalen waarom het script mislukt.

## <a name="enablediag"></a>Diagnostische gegevens inschakelen
Als u Diagnostische gegevens in de [Azure Portal](https://portal.azure.com)wilt inschakelen, gaat u naar de pagina voor uw app en klikt u op **Instellingen > Diagnostische logboeken**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Maakt deel uit van Logboeken](./media/web-sites-enable-diagnostic-log/logspart.png)

Wanneer u **Application Diagnostics**inschakelt, kiest u ook het **niveau**. In de volgende tabel ziet u de logboeken met elk niveau:

| Niveau| Opgenomen logboek Categorieën |
|-|-|
|**Uitgeschakeld** | Geen |
|**Optreedt** | Fout, kritiek |
|**Waarschuwing** | Waarschuwing, fout, kritiek|
|**Gegevens** | Info, waarschuwing, fout, kritiek|
|**Uitgebreide** | Traceren, fouten opsporen, info, waarschuwing, fout, kritiek (alle categorieën) |
|-|-|

Voor **toepassings logboeken**kunt u de optie Bestands systeem tijdelijk inschakelen voor fout opsporing. Deze optie wordt in 12 uur automatisch uitgeschakeld. U kunt ook de Blob-opslag optie inschakelen om een BLOB-container te selecteren waarnaar logboeken moeten worden geschreven.

> [!NOTE]
> Momenteel kunnen alleen .NET-toepassings logboeken worden geschreven naar de Blob-opslag. Java, PHP, node. js, python-toepassings logboeken kunnen alleen worden opgeslagen op het bestands systeem (zonder code wijzigingen voor het schrijven van logboeken naar externe opslag).
>
>

Voor **logboek registratie**van webserver kunt u **opslag** of **Bestands systeem**selecteren. Als u **opslag** selecteert, kunt u een opslag account selecteren en vervolgens een BLOB-container waarnaar de logboeken worden geschreven. 

Als u Logboeken opslaat op het bestands systeem, kunnen de bestanden worden geopend door FTP of worden gedownload als een zip-archief met behulp van Azure CLI.

Logboeken worden standaard niet automatisch verwijderd (met uitzonde ring van **toepassings Logboeken (bestands systeem)** ). Als u logboeken automatisch wilt verwijderen, stelt u het veld **Bewaar periode (dagen)** in.

> [!NOTE]
> Als u de [toegangs sleutels van uw opslag account opnieuw genereert](../storage/common/storage-create-storage-account.md), moet u de configuratie van de betreffende logboek registratie opnieuw instellen om de bijgewerkte sleutels te gebruiken. Om dit te doen:
>
> 1. Stel op het tabblad **configureren** de respectieve logboek registratie functie in op **uit**. Sla de instelling op.
> 2. Schakel logboek registratie voor de blob van het opslag account opnieuw in. Sla de instelling op.
>
>

Een combi natie van bestands systeem-of Blob-opslag kan tegelijkertijd worden ingeschakeld en er zijn afzonderlijke configuraties voor het logboek niveau. U kunt bijvoorbeeld fouten en waarschuwingen in Blob Storage registreren als een lange termijn oplossing voor logboek registratie, terwijl logboek registratie van het bestands systeem met een uitgebreid niveau wordt ingeschakeld.

Hoewel beide opslag locaties dezelfde basis informatie bieden voor geregistreerde gebeurtenissen, registreert **Blob Storage** extra informatie, zoals de exemplaar-id, de thread-id en een gedetailleerdere tijds tempel (maat streepjes) dan logboek registratie naar **Bestands systeem**.

> [!NOTE]
> Gegevens die zijn opgeslagen in **Blob Storage** , kunnen alleen worden geopend met een Storage-client of een toepassing die rechtstreeks kan worden gebruikt met deze opslag systemen. Visual Studio 2013 bevat bijvoorbeeld een Storage Explorer dat kan worden gebruikt om Blob-opslag te verkennen en HDInsight kan toegang krijgen tot gegevens die zijn opgeslagen in Blob Storage. U kunt ook een toepassing schrijven die toegang heeft tot Azure Storage met behulp van een van de [Azure sdk's](https://azure.microsoft.com/downloads/).
>

## <a name="download"></a>Procedures: Logboeken downloaden
Diagnostische gegevens die zijn opgeslagen in het app-bestands systeem, kunnen rechtstreeks worden geopend via FTP. Het kan ook worden gedownload als een zip-archief met behulp van Azure CLI.

De mapstructuur waarin de logboeken worden opgeslagen, is als volgt:

* **Toepassings logboeken** -/logfiles/Application/. Deze map bevat een of meer tekst bestanden met informatie die is gegenereerd door toepassings Logboeken.
* **Mislukte aanvraag traceringen** -/logfiles/W3SVC # # # # # # # # #/. Deze map bevat een XSL-bestand en een of meer XML-bestanden. Zorg ervoor dat u het XSL-bestand in dezelfde map als de XML-bestanden downloadt, omdat het XSL-bestand functionaliteit biedt voor het opmaken en filteren van de inhoud van de XML-bestanden in Internet Explorer.
* **Gedetailleerde fouten logboeken** -/logfiles/DetailedErrors/. Deze map bevat een of meer htm-bestanden die uitgebreide informatie geven over eventuele HTTP-fouten die zijn opgetreden.
* **Webserver logboeken** -/logfiles/http/RawLogs. Deze map bevat een of meer tekst bestanden die zijn opgemaakt met de [uitgebreide W3C-indeling van logboek bestand](/windows/desktop/Http/w3c-logging).
* **Implementatie logboeken** -/logfiles/git. Deze map bevat logboeken die worden gegenereerd door de interne implementatie processen die door Azure App Service worden gebruikt, evenals logboeken voor git-implementaties. U kunt ook de implementatie logboeken vinden onder D:\home\site\deployments.

### <a name="ftp"></a>FTP

Als u een FTP-verbinding met de FTP-server van uw app wilt openen, raadpleegt u [uw app implementeren op Azure app service met behulp van FTP/s](deploy-ftp.md).

Zodra u verbinding hebt gemaakt met de FTP/S-server van uw app, opent u de map LogFiles waarin de logboek bestanden worden opgeslagen.

### <a name="download-with-azure-cli"></a>Downloaden met Azure CLI
Als u de logboek bestanden wilt downloaden met behulp van de Azure-opdracht regel interface, opent u een nieuwe opdracht prompt, Power shell, bash of terminal sessie en voert u de volgende opdracht in:

    az webapp log download --resource-group resourcegroupname --name appname

Met deze opdracht worden de logboeken voor de app met de naam ' appName ' opgeslagen in een bestand met de naam **webapp_logs. zip** in de huidige map.

> [!NOTE]
> Zie [Azure CLI gebruiken](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)als u Azure cli nog niet hebt geïnstalleerd of niet hebt geconfigureerd voor het gebruik van uw Azure-abonnement.
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Procedure: Logboeken in Application Insights weer geven
Visual Studio Application Insights bevat hulpprogram ma's voor het filteren en zoeken van Logboeken en voor het correleren van de logboeken met aanvragen en andere gebeurtenissen.

1. Voeg de Application Insights SDK toe aan uw project in Visual Studio.
   * Klik in Solution Explorer met de rechter muisknop op het project en kies Application Insights toevoegen. De interface begeleidt u stapsgewijs door de stappen voor het maken van een Application Insights bron. [Meer informatie](../azure-monitor/app/asp-net.md)
2. Voeg het Trace listener-pakket toe aan uw project.
   * Klik met de rechter muisknop op het project en kies NuGet-pakketten beheren. Selecteer `Microsoft.ApplicationInsights.TraceListener` meer [informatie](../azure-monitor/app/asp-net-trace-logs.md)
3. Upload het project en voer het uit om logboek gegevens te genereren.
4. Blader in het [Azure Portal](https://portal.azure.com/)naar uw nieuwe Application Insights resource en open **zoeken**. U moet uw logboek gegevens weer geven, samen met de aanvraag, het gebruik en andere telemetrie. Het kan enkele minuten duren om een telemetrie te ontvangen: Klik op vernieuwen. [Meer informatie](../azure-monitor/app/diagnostic-search.md)

[Meer informatie over het bijhouden van prestaties met Application Insights](../azure-monitor/app/azure-web-apps.md)

## <a name="streamlogs"></a>Procedures: Logboeken streamen
Tijdens het ontwikkelen van een toepassing is het vaak handig om logboek gegevens in vrijwel realtime weer te geven. U kunt logboek registratie gegevens streamen naar uw ontwikkel omgeving met behulp van Azure CLI.

> [!NOTE]
> Sommige typen logboek registratie buffer schrijven naar het logboek bestand, wat kan leiden tot onbestelbare gebeurtenissen in de stroom. Een toepassings logboek vermelding die zich voordoet wanneer een gebruiker een pagina bezoekt, kan bijvoorbeeld worden weer gegeven in de stroom vóór de bijbehorende HTTP-logboek vermelding voor de pagina-aanvraag.
>
> [!NOTE]
> Logboek streaming streamt ook gegevens die zijn geschreven naar een tekst bestand dat is opgeslagen in de map **\\D:\\ basismap\\** .
>
>

### <a name="streaming-with-azure-cli"></a>Streamen met Azure CLI
Als u logboek registratie gegevens wilt streamen, opent u een nieuwe opdracht prompt, Power shell, bash of terminal sessie en voert u de volgende opdracht in:

    az webapp log tail --name appname --resource-group myResourceGroup

Met deze opdracht maakt u verbinding met de app met de naam appName en begint u met het streamen van gegevens naar het venster als er logboek gebeurtenissen in de app plaatsvinden. Alle informatie die wordt geschreven naar bestanden die eindigen op. txt,. log of. htm, die zijn opgeslagen in de/LogFiles-map (d:/Home/logfiles), wordt gestreamd naar de lokale console.

Als u specifieke gebeurtenissen, zoals fouten, wilt filteren, gebruikt u de para meter **--filter** . Bijvoorbeeld:

    az webapp log tail --name appname --resource-group myResourceGroup --filter Error

Als u specifieke logboek typen, zoals HTTP, wilt filteren, gebruikt u de para meter **--Path** . Bijvoorbeeld:

    az webapp log tail --name appname --resource-group myResourceGroup --path http

> [!NOTE]
> Zie [Azure CLI gebruiken](../cli-install-nodejs.md)als u Azure cli nog niet hebt geïnstalleerd of niet hebt geconfigureerd voor het gebruik van uw Azure-abonnement.
>
>

## <a name="understandlogs"></a>Procedures: Diagnose logboeken begrijpen
### <a name="application-diagnostics-logs"></a>Application Diagnostics-logboeken
Application Diagnostics slaat gegevens op in een specifieke indeling voor .NET-toepassingen, afhankelijk van het feit of u Logboeken opslaat in het bestands systeem of Blob-opslag. 

De basisset van opgeslagen gegevens is hetzelfde voor beide opslag typen: de datum en tijd waarop de gebeurtenis is opgetreden, de proces-ID die de gebeurtenis heeft geproduceerd, het gebeurtenis type (informatie, waarschuwing, fout) en het gebeurtenis bericht. Het gebruik van het bestands systeem voor logboek opslag is handig wanneer u directe toegang nodig hebt om een probleem op te lossen, omdat de logboek bestanden dicht bij de hand worden bijgewerkt. Blob-opslag wordt gebruikt voor archiverings doeleinden, omdat de bestanden in de cache worden opgeslagen en vervolgens worden verwijderd naar de opslag container volgens een schema.

**Bestandssysteem**

Elke regel die is aangemeld bij het bestands systeem of die is ontvangen met streaming heeft de volgende indeling:

    {Date}  PID[{process ID}] {event type/level} {message}

Bijvoorbeeld, een fout gebeurtenis lijkt op het volgende voor beeld:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Logboek registratie in het bestands systeem biedt de meest algemene informatie over de drie beschik bare methoden, waarbij alleen de tijd, proces-ID, gebeurtenis niveau en het bericht worden verstrekt.

**Blob Storage**

Bij het vastleggen van Blob-opslag worden gegevens opgeslagen in de indeling met door komma's gescheiden waarden (CSV). Aanvullende velden worden vastgelegd om meer gedetailleerde informatie over de gebeurtenis te bieden. De volgende eigenschappen worden gebruikt voor elke rij in het CSV-bestand:

| Naam van eigenschap | Waarde/notatie |
| --- | --- |
| Date |De datum en tijd waarop de gebeurtenis heeft plaatsgevonden |
| Niveau |Gebeurtenis niveau (bijvoorbeeld fout, waarschuwing, informatie) |
| ApplicationName |De naam van de app |
| InstanceId |Exemplaar van de app waarop de gebeurtenis heeft plaatsgevonden |
| EventTickCount |De datum en tijd waarop de gebeurtenis heeft plaatsgevonden, in Tick-indeling (grotere precisie) |
| Gebeurtenis-id |De gebeurtenis-ID van deze gebeurtenis<p><p>De standaard waarde is 0 als niets is opgegeven |
| Pincode |Proces-id |
| TID |De thread-ID van de thread die de gebeurtenis heeft geproduceerd |
| Message |Gebeurtenis detail bericht |

De gegevens die zijn opgeslagen in een blob, zien er ongeveer uit zoals in het volgende voor beeld:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> Voor ASP.NET Core wordt logboek registratie voltooid met behulp van de [micro soft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) -provider. deze provider registreert extra logboek bestanden in de BLOB-container. Zie [ASP.net core logboek registratie in azure](/aspnet/core/fundamentals/logging)voor meer informatie.
>
>

### <a name="failed-request-traces"></a>Traceringen van mislukte aanvragen
Traceringen van mislukte aanvragen worden opgeslagen in XML-bestanden met de naam **fr # # # # #. XML**. Om het weer geven van de geregistreerde gegevens gemakkelijker te maken, wordt een XSL-opmaak model met de naam **freb. xsl** opgegeven in dezelfde map als de XML-bestanden. Als u een van de XML-bestanden in Internet Explorer opent, gebruikt Internet Explorer het XSL-opmaak model om een opgemaakte weer gave van de tracerings informatie te bieden, zoals in het volgende voor beeld:

![mislukte aanvraag weer gegeven in de browser](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

> [!NOTE]
> Een eenvoudige manier om de opgemaakte traceringen van mislukte aanvragen weer te geven, is door te navigeren naar de pagina van de app in de portal. Selecteer in het linkermenu **problemen vaststellen en oplossen**, zoek naar Logboeken voor tracering van **mislukte aanvragen**en klik vervolgens op het pictogram om de gewenste tracering weer te geven.
>

### <a name="detailed-error-logs"></a>Gedetailleerde fouten logboeken
Gedetailleerde fout logboeken zijn HTML-documenten die meer gedetailleerde informatie geven over HTTP-fouten die zijn opgetreden. Aangezien ze alleen HTML-documenten zijn, kunnen ze worden weer gegeven met een webbrowser.

### <a name="web-server-logs"></a>Webserverlogboeken
De webserver logboeken worden ingedeeld met de [uitgebreide W3C-indeling van logboek bestand](/windows/desktop/Http/w3c-logging). Deze informatie kan worden gelezen met behulp van een tekst editor of geparseerd met behulp van hulpprogram ma's zoals [log parser](https://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> De logboeken die door Azure App Service worden geproduceerd, bieden geen ondersteuning voor de velden **s-ComputerName**, **s-IP**of **CS-version** .
>
>

## <a name="nextsteps"></a> Volgende stappen
* [Azure App Service bewaken](web-sites-monitor.md)
* [Problemen met Azure App Service oplossen in Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [App-Logboeken in HDInsight analyseren](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
