---
title: Apps bewaken-Azure App Service | Microsoft Docs
description: Meer informatie over het bewaken van apps in Azure App Service met behulp van de Azure Portal.
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/11/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 7edff127bb981db985bebb41740744f325306bc8
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546199"
---
# <a name="monitor-apps-in-azure-app-service"></a>Apps in Azure App Service bewaken
[Azure app service](https://go.microsoft.com/fwlink/?LinkId=529714) biedt ingebouwde bewakings functionaliteit voor web-apps, mobiele back-ends en API-apps in de [Azure Portal](https://portal.azure.com).

In de Azure Portal kunt u *quota's* en *metrische gegevens* voor een app bekijken en app service plannen, en *waarschuwingen* en *Automatische schaling* instellen die zijn gebaseerd op metrische gegevens.

## <a name="understand-quotas"></a>Meer informatie over quota's

Voor apps die worden gehost in App Service gelden bepaalde beperkingen voor de resources die ze kunnen gebruiken. De limieten worden gedefinieerd door het App Service plan dat is gekoppeld aan de app.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Als de app wordt gehost in een *gratis* of *gedeeld* abonnement, worden de limieten voor de resources die de app kan gebruiken door quota gedefinieerd.

Als de app wordt gehost in een *Basic*-, *Standard*-of *Premium* -abonnement, worden de limieten voor de resources die ze kunnen gebruiken, ingesteld op de *grootte* (klein, gemiddeld, groot) en aantal *instanties* (1, 2, 3, enzovoort) van het app service plan.

Quota voor gratis of gedeelde apps zijn:

| Quota | Beschrijving |
| --- | --- |
| **CPU (kort)** | De hoeveelheid CPU die is toegestaan voor deze app in een interval van 5 minuten. Dit quotum wordt om de vijf minuten opnieuw ingesteld. |
| **CPU (dag)** | De totale hoeveelheid CPU die op een dag is toegestaan voor deze app. Dit quotum wordt om de 24 uur opnieuw ingesteld om middernacht UTC. |
| **Geheugenmetabase** | De totale hoeveelheid geheugen die is toegestaan voor deze app. |
| **Bandbreedte** | De totale hoeveelheid uitgaande band breedte die op een dag is toegestaan voor deze app. Dit quotum wordt om de 24 uur opnieuw ingesteld om middernacht UTC. |
| **System** | De totale hoeveelheid toegestane opslag ruimte. |

Het enige quotum dat van toepassing is op apps die worden gehost in *Basic*, *Standard*en *Premium* , is bestands systeem.

Zie [service limieten voor Azure-abonnementen](../azure-subscription-service-limits.md#app-service-limits)voor meer informatie over de specifieke quota's, limieten en functies die beschikbaar zijn voor de verschillende app service sku's.

### <a name="quota-enforcement"></a>Quota afdwingen

Als een app de *CPU (kort)* , *CPU (dag)* of *bandbreedte* quotum overschrijdt, wordt de app gestopt totdat het quotum opnieuw wordt ingesteld. Gedurende deze periode resulteert alle inkomende aanvragen in een HTTP 403-fout.

![403-fout bericht][http403]

Als het geheugen quotum van de app wordt overschreden, wordt de app opnieuw gestart.

Als het bestandssysteem quotum wordt overschreden, mislukt elke schrijf bewerking. Fouten bij schrijf bewerkingen zijn onder andere schrijf bewerkingen naar Logboeken.

U kunt quota's verhogen of verwijderen uit uw app door uw App Service-abonnement bij te werken.

## <a name="understand-metrics"></a>Metrische gegevens begrijpen

> [!NOTE]
> Het gebruik van het **Bestands systeem** is een nieuwe waarde die wereld wijd wordt getotaliseerd, er worden geen gegevens verwacht, tenzij u White List hebt voor een persoonlijke preview.
> 

Metrische gegevens geven informatie over de app of het gedrag van het App Service plan.

De beschik bare metrische gegevens voor een app zijn:

| Gegevens | Beschrijving |
| --- | --- |
| **Gemiddelde reactie tijd** | De gemiddelde tijd die nodig is voor het verwerken van aanvragen in de app. |
| **Gemiddelde werkset geheugen** | De gemiddelde hoeveelheid geheugen die wordt gebruikt door de app, in mega bytes (MiB). |
| **Verbindingen** | Het aantal gekoppelde sockets dat in de sandbox aanwezig is (W3wp. exe en de onderliggende processen).  Een gebonden socket wordt gemaakt door binding-Api's ()/Connect () aan te roepen en blijft totdat de andere socket is gesloten met CloseHandle ()/closesocket (). |
| **CPU-tijd** | De hoeveelheid CPU die wordt verbruikt door de app, in seconden. Zie [CPU-tijd versus CPU-percentage](#cpu-time-vs-cpu-percentage)voor meer informatie over deze metrische gegevens. |
| **Huidige Assembly's** | Het huidige aantal Assembly's dat is geladen in alle AppDomains in deze toepassing. |
| **Gegevens in** | De hoeveelheid inkomende band breedte die door de app wordt gebruikt in MiB. |
| **Gegevens uit** | De hoeveelheid uitgaande band breedte die door de app wordt gebruikt in MiB. |
| **Gebruik van bestands systeem** | Percentage van het bestandssysteem quotum dat door de app wordt gebruikt. |
| **Schone verzamelingen van 0 gen** | Het aantal keren dat de generatie 0-objecten permanent zijn verzameld sinds het begin van het app-proces. Een hogere generatie GCs bevatten alle lagere GCs.|
| **1 garbagecollection-verzamelingen** | Het aantal keren dat de generatie 1-objecten permanent zijn verzameld sinds het begin van het app-proces. Een hogere generatie GCs bevatten alle lagere GCs.|
| **Opschoon verzamelingen van generatie 2** | Het aantal keren dat de generatie 2-objecten permanent zijn verzameld sinds het begin van het app-proces.|
| **Aantal ingangen** | Het totale aantal ingangen dat momenteel door het app-proces is geopend.|
| **Http-2xx** | Het aantal aanvragen dat resulteert in een HTTP-status code ≥ 200, maar < 300. |
| **HTTP-3xx** | Het aantal aanvragen dat resulteert in een HTTP-status code ≥ 300, maar < 400. |
| **HTTP 401** | Het aantal aanvragen dat resulteert in de HTTP 401-status code. |
| **HTTP 403** | Het aantal aanvragen dat resulteert in de HTTP 403-status code. |
| **Http 404** | Het aantal aanvragen dat resulteert in de HTTP 404-status code. |
| **Http 406** | Het aantal aanvragen dat resulteert in de HTTP 406-status code. |
| **Http-4xx** | Het aantal aanvragen dat resulteert in een HTTP-status code ≥ 400, maar < 500. |
| **Http-server fouten** | Het aantal aanvragen dat resulteert in een HTTP-status code ≥ 500, maar < 600. |
| **Andere i/o-bytes per seconde** | De snelheid waarmee het app-proces bytes uitgeeft aan I/O-bewerkingen die geen gegevens bevatten, zoals controle bewerkingen.|
| **Andere i/o-bewerkingen per seconde** | De snelheid waarmee I/O-bewerkingen worden uitgevoerd die geen lees-of schrijf bewerkingen zijn.|
| **I/o gelezen bytes per seconde** | De snelheid waarmee het app-proces bytes van I/O-bewerkingen leest.|
| **I/o-Lees bewerkingen per seconde** | De snelheid waarmee het app-proces Lees-I/O-bewerkingen uitgeeft.|
| **I/o-schrijf bewerkingen in bytes per seconde** | De snelheid waarmee het app-proces bytes naar I/O-bewerkingen schrijft.|
| **I/o-schrijf bewerkingen per seconde** | De snelheid waarmee het app-proces I/O-schrijf bewerkingen uitgeeft.|
| **Werkset geheugen** | De huidige hoeveelheid geheugen die wordt gebruikt door de app in MiB. |
| **Privé-bytes** | Eigen bytes is de huidige grootte, in bytes, van het geheugen dat het app-proces heeft toegewezen en dat niet met andere processen kan worden gedeeld.|
| **Aanvragen** | Het totale aantal aanvragen, ongeacht de resulterende HTTP-status code. |
| **Aanvragen in de wachtrij van de toepassing** | Het aantal aanvragen in de wachtrij voor toepassings aanvragen.|
| **Aantal threads** | Het aantal threads dat momenteel actief is in het app-proces.|
| **Totaal aantal app-domeinen** | Het huidige aantal AppDomains dat is geladen in deze toepassing.|
| **Totaal aantal verwijderde app-domeinen** | Het totale aantal AppDomains dat sinds het begin van de toepassing is verwijderd.|


De beschik bare metrische gegevens voor een App Service plan zijn:

> [!NOTE]
> De metrische gegevens voor het App Service plan zijn alleen beschikbaar voor abonnementen in de lagen *Basic*, *Standard*en *Premium* .
> 

| Gegevens | Beschrijving |
| --- | --- |
| **CPU-percentage** | De gemiddelde CPU die wordt gebruikt voor alle exemplaren van het plan. |
| **Geheugen percentage** | Het gemiddelde geheugen dat wordt gebruikt voor alle exemplaren van het plan. |
| **Gegevens in** | De gemiddelde binnenkomende band breedte die wordt gebruikt voor alle exemplaren van het abonnement. |
| **Gegevens uit** | De gemiddelde uitgaande band breedte die wordt gebruikt voor alle exemplaren van het abonnement. |
| **Wachtrij lengte voor schijf** | Het gemiddelde aantal lees-en schrijf aanvragen dat in de wachtrij is geplaatst op opslag. Een hoge wachtrij lengte voor de schijf is een indicatie van een app die mogelijk vertraagd is vanwege buitensporige schijf-I/O's. |
| **Lengte van http-wachtrij** | Het gemiddelde aantal HTTP-aanvragen dat aan de wachtrij is gewacht voordat wordt voldaan. Een hoge of verhoogde HTTP-wachtrij lengte is een symptoom van een plan onder zware belasting. |

### <a name="cpu-time-vs-cpu-percentage"></a>CPU-tijd versus CPU-percentage
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Er zijn twee metrische gegevens die het CPU-gebruik weer spie gelen:

**CPU-tijd**: handig voor apps die worden gehost in een gratis of gedeeld abonnement, omdat een van de quota's is gedefinieerd in CPU-minuten die door de app worden gebruikt.

**CPU-percentage**: handig voor apps die worden gehost in Basic-, Standard-en Premium-abonnementen, omdat ze kunnen worden uitgeschaald. CPU-percentage is een goede indicatie van het totale gebruik voor alle exemplaren.

## <a name="metrics-granularity-and-retention-policy"></a>Granulatie van metrische gegevens en bewaar beleid
Metrische gegevens voor een app-en app service-plan worden vastgelegd en geaggregeerd door de service, met de volgende granulaties en bewaar beleid:

* De metrische gegevens over **de nauw** keurigheid van de granulatie worden 30 uur bewaard.
* De metrische granulatie waarden voor het **uur** worden 30 dagen bewaard.
* **Dag** granulatie waarden worden gedurende 30 dagen bewaard.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Quota en metrische gegevens bewaken in de Azure Portal
Als u de status van de verschillende quota's en metrische gegevens wilt bekijken die van invloed zijn op een app, gaat u naar de [Azure Portal](https://portal.azure.com).

![De grafiek quota's in het Azure Portal][quotas]

Als u quota's wilt zoeken, selecteert u **instellingen** > **quota's**. In de grafiek kunt u het volgende controleren: 
1. De naam van het quotum.
1. Het interval voor opnieuw instellen.
1. De huidige limiet.
1. De huidige waarde.

![grafiek met metrische gegevens in de Azure Portal][metrics] u de gegevens rechtstreeks vanaf de pagina **resource** kunt openen. De grafiek aanpassen: 
1. Selecteer de grafiek.
1. Selecteer **grafiek bewerken**.
1. Bewerk het **tijds bereik**.
1. Bewerk het **grafiek type**.
1. Bewerk de metrische gegevens die u wilt weer geven.  

Zie [metrische service gegevens bewaken](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)voor meer informatie over metrische gegevens.

## <a name="alerts-and-autoscale"></a>Waarschuwingen en automatisch schalen
Metrische gegevens voor een app of een App Service plan kunnen worden aangesloten op waarschuwingen. Zie [Meldingen van waarschuwingen ontvangen](../monitoring-and-diagnostics/insights-alerts-portal.md) voor meer informatie.

App Service-apps die worden gehost in Basic-, Standard-of Premium-App Service plannen ondersteunen automatisch schalen. Met automatisch schalen kunt u regels configureren die de metrische gegevens van het App Service plan bewaken. Regels kunnen het aantal instanties verg Roten of verkleinen, zodat u indien nodig aanvullende bronnen kunt opgeven. Regels kunnen u helpen bij het besparen van geld wanneer de app te veel is ingericht.

Zie [How to scale](../monitoring-and-diagnostics/insights-how-to-scale.md) and [Best practices for Azure monitor automatisch schalen](../azure-monitor/platform/autoscale-best-practices.md)voor meer informatie over automatisch schalen.

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png