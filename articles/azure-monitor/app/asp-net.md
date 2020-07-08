---
title: Web-app-analyse voor ASP.NET instellen met Azure Application Insights | Microsoft Docs
description: Configureer prestaties, Beschik baarheid en hulpprogram ma's voor analyse van gebruikers gedrag voor uw ASP.NET-website, die on-premises of in azure wordt gehost.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: 7dd60be0a3f2ee7e1a0d306fa007bed166746115
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84886820"
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Application Insights instellen voor uw ASP.NET-website

Met deze procedure wordt uw ASP.NET web-app zo geconfigureerd dat telemetrie wordt verzonden naar de [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)-service. Dit werkt voor ASP.NET-apps die worden gehost op uw eigen on-premises IIS-server of in de cloud. U beschikt over grafieken en een krachtige querytaal waarmee u meer inzicht krijgt in de prestaties van uw app en hoe mensen deze gebruiken. Daarnaast ontvangt u meldingen over fouten of prestatieproblemen. Veel ontwikkelaars gebruiken deze functies graag, maar u kunt de telemetrie desgewenst uitbreiden en aanpassen.

Voor de Setup zijn maar een paar klikken nodig in Visual Studio. U kunt kosten besparen door de hoeveelheid telemetrie te beperken. Met deze functie kunt u experimenteren en fouten opsporen, of een site met niet veel gebruikers bewaken. Wanneer u besluit dat u de productiesite wilt bewaken, kunt u de limiet later eenvoudig verhogen.

## <a name="prerequisites"></a>Vereisten
Als u Application Insights wilt toevoegen aan uw ASP.NET-website, moet u het volgende doen:

- Installeer [Visual Studio 2019 voor Windows](https://www.visualstudio.com/downloads/) met de volgende werk belastingen:
    - ASP.NET en Web Development (de optionele onderdelen niet uitschakelen)
    - Azure-ontwikkeling

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="step-1-add-the-application-insights-sdk"></a><a name="ide"></a> Stap 1: de Application Insights-SDK toevoegen

> [!IMPORTANT]
> De scherm afbeeldingen in dit voor beeld zijn gebaseerd op Visual Studio 2017 versie 15.9.9 en hoger. De ervaring om Application Insights toe te voegen, is afhankelijk van zowel de versie van Visual Studio als het sjabloon type ASP.NET. Oudere versies kunnen alternatieve tekst bevatten, zoals "Configure Application Insights".

Klik met de rechter muisknop op de naam van uw web-app in het Solution Explorer en kies **toevoegen**  >  **Application Insights Telemetry**

![Schermopname van Solution Explorer waarin Application Insights is gemarkeerd](./media/asp-net/add-telemetry-new.png)

(Afhankelijk van de Application Insights-SDK-versie wordt u mogelijk gevraagd om een upgrade uit te voeren naar de meest recente SDK-versie. Als u hierom wordt gevraagd, selecteert u **SDK bijwerken**.)

![Schermopname: Er is een nieuwe versie van Microsoft Application Insights-SDK beschikbaar. SDK bijwerken is gemarkeerd](./media/asp-net/0002-update-sdk.png)

Application Insights-configuratiescherm:

Selecteer **aan de slag**.

![Schermopname van de pagina Uw app registreren bij Application Insights](./media/asp-net/00004-start-free.png)

Als u een resourcegroep of locatie wilt instellen voor het opslaan van uw gegevens, klikt u op **Instellingen configureren**. Resourcegroepen worden gebruikt om toegang tot gegevens te beheren. Als u verschillende apps hebt die deel uitmaken van hetzelfde systeem, kunt u hun Application Insights-gegevens in dezelfde resourcegroep plaatsen.

 Selecteer **Registreren**.

![Schermopname van de pagina Uw app registreren bij Application Insights](./media/asp-net/00005-register-ed.png)

 Selecteer **project**  >  **Manage NuGet**  >  **packages Bron: nuget.org** > Controleer of u de laatste stabiele versie van de Application Insights SDK hebt.

 Er wordt telemetrie verzonden naar [Azure Portal](https://portal.azure.com), zowel tijdens de foutopsporing als na het publiceren van de app.
> [!NOTE]
> Als u geen telemetrie naar de portal wilt verzenden tijdens de foutopsporing, voegt u de Application Insights SDK toe aan de app, maar configureert u geen resource in de portal. U kunt de telemetrie in Visual Studio bekijken tijdens de foutopsporing. U kunt later terugkeren naar deze configuratiepagina, of u kunt wachten tot de app is geïmplementeerd en [telemetrie inschakelen tijdens runtime](../../azure-monitor/app/status-monitor-v2-overview.md).

## <a name="step-2-run-your-app"></a><a name="run"></a> Stap 2: uw app uitvoeren
Voer uw app uit met F5. Open verschillende pagina's om telemetrie te genereren.

In Visual Studio ziet u het aantal gebeurtenissen dat is vastgelegd in een logboek.

![Schermopname van Visual Studio. Tijdens het opsporen van fouten wordt de knop Application Insights weergegeven.](./media/asp-net/00006-Events.png)

## <a name="step-3-see-your-telemetry"></a>Stap 3: uw telemetrie weergeven
U ziet uw telemetrie in Visual Studio of in de Application Insights-webportal. Zoek telemetrie in Visual Studio om fouten in de app op te sporen. Bewaak de prestaties en het gebruik in de webportal wanneer het systeem live is. 

### <a name="see-your-telemetry-in-visual-studio"></a>De telemetrie bekijken in Visual Studio

In Visual Studio om de Application Insights-gegevens te bekijken.  Selecteer **Solution Explorer**  >  **verbonden services** > Klik met de rechter muisknop op **Application Insights**en klik vervolgens op **Live telemetrie zoeken**.

In het zoekvenster van Visual Studio Application Insights ziet u de telemetriegegevens van de toepassing die zijn gegenereerd aan de serverzijde van de app. Experimenteer met de filters en klik op een gebeurtenis voor meer details.

![Schermopname van de weergave Gegevens van foutopsporingssessie in het venster Application Insights.](./media/asp-net/55.png)

> [!Tip]
> Als u geen gegevens ziet, controleert u of het tijdsbereik correct is en klikt u op het pictogram Zoeken.

[Meer informatie over Application Insights-hulpprogramma's in Visual Studio](../../azure-monitor/app/visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Telemetrie bekijken in de webportal

U kunt de telemetrie ook in de Application Insights-webportal bekijken (tenzij u ervoor hebt gekozen alleen de SDK te installeren). De portal bevat meer grafieken, analysefuncties en weergaven met meerdere onderdelen dan Visual Studio. De portal bevat ook waarschuwingen.

Open uw Application Insights-resource. Meld u aan bij [Azure Portal](https://portal.azure.com/) om de telemetrie te bekijken, of selecteer hiervoor **Solution Explorer** > **Verbonden services** > klik met de rechtermuisknop op **Application Insights** > **Application Insights-portal openen**.

De portal wordt geopend met een weergave van de telemetrie van uw app.

![Schermopname van Application Insights-overzichtspagina](./media/asp-net/007.png)

Klik in de portal op een tegel of grafiek om meer details te bekijken.

## <a name="step-4-publish-your-app"></a>Stap 4: uw app publiceren
Publiceer uw app op de IIS-server of op Azure. Bekijk de livestream met metrische gegevens in [Live Metrics Stream](../../azure-monitor/app/live-stream.md) om te controleren of alles goed werkt.

Uw telemetrie bouwt op in de Application Insights Portal, waar u metrische gegevens kunt controleren, uw telemetrie doorzoekt. U kunt ook de krachtige [Kusto-query taal](/azure/kusto/query/) gebruiken om het gebruik en de prestaties te analyseren of om specifieke gebeurtenissen te zoeken.

U kunt ook door gaan met het analyseren van uw telemetrie in [Visual Studio](../../azure-monitor/app/visual-studio.md), met hulpprogram ma's als diagnostische zoek acties en [trends](../../azure-monitor/app/visual-studio-trends.md).

> [!NOTE]
> Als uw app zoveel telemetrie verzendt dat de[beperkingslimieten](../../azure-monitor/app/pricing.md#limits-summary) worden benaderd, worden automatisch [steekproeven](../../azure-monitor/app/sampling.md) ingeschakeld. Met steekproeven vermindert u de hoeveelheid telemetrie die vanuit uw app wordt verzonden, maar behoudt u gecorreleerde gegevens voor diagnostische doeleinden.
>
>

## <a name="youre-all-set"></a><a name="land"></a>U bent klaar

Gefeliciteerd U hebt het pakket Application Insights geïnstalleerd in uw app en dit zo geconfigureerd dat telemetrie wordt verzonden naar de Application Insights-service in Azure.

De Azure-resource die de telemetrie van uw app ontvangt, wordt aangeduid met een *instrumentatiesleutel*. U vindt deze sleutel in het bestand ApplicationInsights.config.


## <a name="upgrade-to-future-sdk-versions"></a>Upgraden naar toekomstige SDK-versies
Als u wilt upgraden naar een [nieuwe release van de SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), opent u **NuGet-pakketbeheer** opnieuw en filtert u op geïnstalleerde pakketten. Selecteer **micro soft. ApplicationInsights. Web**en kies **upgraden**.

Als u aanpassingen in ApplicationInsights.config hebt aangebracht, slaat u hiervan een kopie op voordat u de upgrade uitvoert. Voeg de wijzigingen vervolgens samen in de nieuwe versie.

## <a name="next-steps"></a>Volgende stappen

Er zijn ook andere onderwerpen die u kunt bekijken als u geïnteresseerd bent in:

* [Een web-app instrumenteren tijdens runtime](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md)

### <a name="more-telemetry"></a>Meer telemetrie

* **[Laadgegevens voor browser en pagina](../../azure-monitor/app/javascript.md)**: voeg een codefragment in uw webpagina's in.
* **[Gedetailleerde bewaking van afhankelijkheid en uitzonderingen](../../azure-monitor/app/monitor-performance-live-website-now.md)**: installeer Status Monitor op uw server.
* **[Aangepaste code gebeurtenissen](../../azure-monitor/app/api-custom-events-metrics.md)** om gebruikers acties te tellen, tijd te bepalen of te meten.
* **[Haal logboekgegevens op](../../azure-monitor/app/asp-net-trace-logs.md)**: koppel logboekgegevens aan uw telemetrie.

### <a name="analysis"></a>Analyse

* **[Met Application Insights werken in Visual Studio](../../azure-monitor/app/visual-studio.md)**<br/>Bevat informatie over foutopsporing met telemetrie, het doorzoeken van diagnostische gegevens en het in detail analyseren van code.
* **[Analyse](../../azure-monitor/log-query/get-started-portal.md)**: de krachtige querytaal.

### <a name="alerts"></a>Waarschuwingen

* [Beschikbaarheidstests](../../azure-monitor/app/monitor-web-app-availability.md): maak tests om ervoor te zorgen dat uw site zichtbaar is op internet.
* [Slimme diagnostische gegevens](../../azure-monitor/app/proactive-diagnostics.md): deze tests worden automatisch uitgevoerd, zodat u niets hoeft te doen om ze in te stellen. Deze geeft aan of een app een ongebruikelijk aantal mislukte aanvragen heeft.
* [Metrische waarschuwingen](../../azure-monitor/platform/alerts-log.md): Stel waarschuwingen in om u te waarschuwen als een metriek een drempel waarde overschrijdt. U kunt deze instellen op aangepaste metrische gegevens die u in uw app codeert.

### <a name="automation"></a>Automation

* [Het maken van een Application Insights-resource automatiseren](../../azure-monitor/app/powershell.md)
