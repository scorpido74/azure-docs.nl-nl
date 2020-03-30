---
title: Foutopsporing in Visual Studio met Azure Application Insights
description: Analyse van web-app-prestaties en diagnostische gegevens tijdens foutopsporing en algemeen gebruik.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/07/2017
ms.reviewer: mbullwin
ms.custom: vs-azure
ms.openlocfilehash: 8905222214d58eeba24ecf50da768ffa1d65c39d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670879"
---
# <a name="debug-your-applications-with-azure-application-insights-in-visual-studio"></a>Uw toepassingen debuggen met Azure Application Insights in Visual Studio
In Visual Studio (2015 en hoger) kunt u de prestaties analyseren en problemen in uw ASP.NET web-app identificeren tijdens de foutopsporing en algemeen gebruik. Dit gebeurt aan de hand van telemetrie uit [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

Als u uw ASP.NET-web-app hebt gemaakt met Visual Studio 2017 of hoger, beschikt deze al over de Application Insights-SDK. Anders [voegt u Application Insights aan uw app toe](../../azure-monitor/app/asp-net.md) als u dit nog niet hebt gedaan.

Als uw app wilt bewaken wanneer deze in live productie is, bekijkt u normaal gesproken de Application Insights-telemetrie in de [Azure-portal](https://portal.azure.com), waar u waarschuwingen kunt instellen en krachtige bewakingsprogramma's kunt toepassen. Voor foutopsporing kunt u echter ook de telemetrie in Visual Studio zoeken en analyseren. U Visual Studio gebruiken om telemetrie te analyseren, zowel vanaf uw productiesite als vanaf foutopsporingsuitvoeringen op uw ontwikkelingsmachine. In het laatste geval kunt u foutopsporingsruns al analyseren nog voordat u de SDK hebt geconfigureerd om telemetrie naar de Azure-portal te verzenden. 

## <a name="debug-your-project"></a><a name="run"></a> Fouten opsporen in uw project
Voer uw web-app in de lokale foutopsporingsmodus door op F5 te drukken. Open verschillende pagina's om telemetrie te genereren.

In Visual Studio ziet u een aantal gebeurtenissen die zijn vastgelegd door de module Application Insights in uw project.

![Tijdens het opsporen van fouten wordt in Visual Studio de knop Application Insights weergegeven.](./media/visual-studio/appinsights-09eventcount.png)

Klik op deze knop om uw telemetrie te doorzoeken. 

## <a name="application-insights-search"></a>Application Insights-zoekopdracht
In het zoekvenster van Application Insights worden geregistreerde gebeurtenissen weergegeven. (Als u zich bij Azure hebt aangemeld toen u Toepassingsinzichten instelt, u dezelfde gebeurtenissen zoeken in de Azure-portal.)

![Klik met de rechtermuisknop op het project en kies Application Insights > Zoeken.](./media/visual-studio/34.png)

> [!NOTE] 
> Nadat u filters hebt in- of uitgeschakeld, klikt u op de knop Zoeken aan het einde van het veld Tekst zoeken.
>

U kunt zoeken met vrije tekst gebruiken voor alle velden in de gebeurtenissen. Zoek bijvoorbeeld naar een deel van de URl van een pagina, naar de waarde van een eigenschap, zoals de clientlocatie, of naar specifieke woorden in een traceringslogboek.

Klik op een gebeurtenis om de gedetailleerde eigenschappen ervan weer te geven.

Voor aanvragen voor uw web-app, kunt u verder klikken naar de code.

![Klik onder Aanvraagdetails door naar de code](./media/visual-studio/31.png)

U kunt ook de Verwante items openen om mislukte aanvragen of uitzonderingen te diagnosticeren.

![Blader onder Aanvraagdetails naar beneden, naar gerelateerde items](./media/visual-studio/41.png)

## <a name="view-exceptions-and-failed-requests"></a>Uitzonderingen en mislukte aanvragen weergeven
Uitzonderingsrapporten worden weergegeven in het venster Zoeken. (In enkele oudere typen ASP.NET-toepassingen moet u [uitzonderingencontrole instellen](../../azure-monitor/app/asp-net-exceptions.md) om te zien welke uitzonderingen door het framework worden afgehandeld.)

Klik op een uitzondering voor een stack-trace. Als de code van de app in Visual Studio is geopend, kunt u via de stack-trace doorklikken naar de relevante coderegel.

![Uitzondering voor stack-trace](./media/visual-studio/17.png)

## <a name="view-request-and-exception-summaries-in-the-code"></a>Overzichten voor aanvragen en uitzonderingen weergeven in de code
In de codelensregel boven elke handlermethode ziet u een aantal aanvragen en uitzonderingen die in de afgelopen 24 uur zijn vastgelegd door Application Insights.

![Uitzondering voor stack-trace](./media/visual-studio/21.png)

> [!NOTE] 
> Code Lens toont alleen Application Insights-gegevens als u [uw app hebt geconfigureerd om telemetrie te verzenden naar de Application Insights-portal](../../azure-monitor/app/asp-net.md).
>

[Meer informatie over Application Insights in Code Lens](../../azure-monitor/app/visual-studio-codelens.md)

## <a name="trends"></a>Trends
Trends is een hulpprogramma waarmee u de werking van uw app gedurende een bepaalde periode kunt visualiseren. 

Kies **Telemetrietrends verkennen** op de Application Insights-werkbalkknop of in het Application Insights-zoekvenster. Kies een van de vijf algemene query's om te beginnen. U kunt verschillende gegevenssets analyseren op basis van telemetrietypen, tijdsbereik en andere eigenschappen. 

Als u wilt zoeken naar afwijkingen in uw gegevens, kiest u een van de afwijkingsopties onder de vervolgkeuzelijst Type weergave. Met de filteropties aan de onderkant van het venster kunt u eenvoudig specifieke subreeksen van uw telemetrie selecteren.

![Trends](./media/visual-studio/51.png)

[Meer informatie over Trends](../../azure-monitor/app/visual-studio-trends.md).

## <a name="local-monitoring"></a>Lokale bewaking
(Van Visual Studio 2015 Update 2) Als u de SDK niet hebt geconfigureerd om telemetrie naar de Application Insights-portal te verzenden (zodat er geen instrumentatiesleutel in ApplicationInsights.config is) wordt in het diagnostische venster telemetrie van uw laatste foutopsporingssessie weergegeven. 

Dit is handig als u al een eerdere versie van uw app hebt gepubliceerd. Zo voorkomt u dat de telemetrie van uw foutopsporingssessies in de Application Insights-portal wordt verward met de telemetrie over de gepubliceerde app.

Ook als u beschikt over [aangepaste telemetrie](../../azure-monitor/app/api-custom-events-metrics.md) waarin u fouten wilt opsporen voordat u deze naar de portal verzendt, komt deze functie van pas.

* *In eerste instantie heb ik Application Insights volledig geconfigureerd om telemetrie naar de portal te sturen. Maar nu wil ik de telemetrie alleen in Visual Studio zien.*
  
  * De instellingen van het venster Zoeken bevat een optie om lokale diagnostische gegevens te doorzoeken, zelfs als uw app telemetrie verzendt naar de portal.
  * Als u wilt voorkomen dat telemetrie `<instrumentationkey>...` naar de portal wordt verzonden, geeft u commentaar op de regel van ApplicationInsights.config. Wanneer u klaar bent om telemetrie weer naar de portal te verzenden, geeft u geen commentaar.


## <a name="next-steps"></a>Volgende stappen
|  |  |
| --- | --- |
| **[Meer gegevens toevoegen](../../azure-monitor/app/asp-net-more.md)**<br/>Bewaak het gebruik, de beschikbaarheid, de afhankelijkheden en de uitzonderingen. Integreer bijgehouden informatie uit frameworks voor logboekregistratie. Schrijf aangepaste telemetrie. |![Visual Studio](./media/visual-studio/64.png) |
| **[Werken met de Application Insights-portal](../../azure-monitor/app/overview-dashboard.md)**<br/>Bekijk dashboards, krachtige diagnostische en analytische hulpprogramma's, waarschuwingen, een live afhankelijkheidskaart van uw toepassing en geëxporteerde telemetriegegevens. |![Visual Studio](./media/visual-studio/62.png) |

