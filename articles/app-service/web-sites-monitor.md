---
title: Apps bewaken
description: Meer informatie over het bewaken van apps in Azure App Service met behulp van de Azure-portal. Inzicht in de quota en statistieken die worden gerapporteerd.
author: btardif
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.topic: article
ms.date: 01/11/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: d84340730391abd7dba4d13202503d37941c09b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500438"
---
# <a name="monitor-apps-in-azure-app-service"></a>Apps controleren in Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) biedt ingebouwde bewakingsfunctionaliteit voor web-apps, mobiele en API-apps in de [Azure-portal.](https://portal.azure.com)

In de Azure-portal u *quota's* en *statistieken* voor een app- en app-serviceabonnement bekijken en *waarschuwingen* en regels voor *automatisch schalen* instellen op basis van statistieken.

## <a name="understand-quotas"></a>Inzicht in quota

Apps die worden gehost in App Service zijn onderworpen aan bepaalde beperkingen op de resources die ze kunnen gebruiken. De limieten worden gedefinieerd door het App Service-abonnement dat aan de app is gekoppeld.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Als de app wordt gehost in een *gratis* of *gedeeld* abonnement, worden de limieten voor de resources die de app kan gebruiken gedefinieerd door quota.

Als de app wordt gehost in een *Basis-,* *Standaard-* of *Premium-abonnement,* worden de limieten voor de resources die ze kunnen gebruiken ingesteld op de *grootte* (Klein, Gemiddeld, Groot) en *instantietelling* (1, 2, 3, enzovoort) van het App Service-abonnement.

Quota voor gratis of gedeelde apps zijn:

| Quota | Beschrijving |
| --- | --- |
| **CPU (Kort)** | De hoeveelheid CPU toegestaan voor deze app in een interval van 5 minuten. Dit quotum wordt elke vijf minuten gereset. |
| **CPU (Dag)** | De totale hoeveelheid CPU toegestaan voor deze app in een dag. Dit quotum wordt elke 24 uur om middernacht ingesteld. |
| **Geheugen** | De totale hoeveelheid geheugen toegestaan voor deze app. |
| **Bandbreedte** | De totale hoeveelheid uitgaande bandbreedte toegestaan voor deze app in een dag. Dit quotum wordt elke 24 uur om middernacht ingesteld. |
| **Bestandssysteem** | De totale hoeveelheid opslagruimte is toegestaan. |

Het enige quotum dat van toepassing is op apps die worden gehost in *Basic,* *Standard*en *Premium* is Filesystem.

Zie [Azure Subscription service limits](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)voor meer informatie over de specifieke quota, limieten en functies die beschikbaar zijn voor de verschillende SKU's van de App Service.

### <a name="quota-enforcement"></a>Quotahandhaving

Als een app de *CPU (short),* *CPU (Day)* of *bandbreedtequotum* overschrijdt, wordt de app gestopt totdat het quotum wordt gereset. Gedurende deze periode resulteren alle binnenkomende aanvragen in een HTTP 403-fout.

![403 foutbericht][http403]

Als het app-geheugenquotum wordt overschreden, wordt de app opnieuw gestart.

Als het bestandssysteemquotum wordt overschreden, mislukt elke schrijfbewerking. Schrijfbewerkingsfouten omvatten eventuele schrijfbewerkingen naar logboeken.

U quota uit uw app verhogen of verwijderen door uw App Service-abonnement te upgraden.

## <a name="understand-metrics"></a>Statistieken begrijpen

> [!NOTE]
> **Bestandssysteemgebruik** is een nieuwe statistiek die wereldwijd wordt uitgerold, er worden geen gegevens verwacht, tenzij u op de witte lijst staat voor een privévoorbeeld.
> 

> [!IMPORTANT]
> **De gemiddelde responstijd** wordt afgeschaft om verwarring met metrische aggregaties te voorkomen. Gebruik **de responstijd** als vervanging.

Statistieken geven informatie over het gedrag van de app of het App-serviceplan.

Voor een app zijn de beschikbare statistieken:

| Gegevens | Beschrijving |
| --- | --- |
| **Reactietijd** | De tijd die nodig is voor de app om verzoeken te dienen, in seconden. |
| **Gemiddelde responstijd (afgeschaft)** | De gemiddelde tijd die nodig is voor de app om verzoeken te serveren, in seconden. |
| **Gemiddelde geheugenwerkset** | De gemiddelde hoeveelheid geheugen die door de app wordt gebruikt, in megabytes (MiB). |
| **Verbindingen** | Het aantal gebonden sockets dat in de sandbox aanwezig is (w3wp.exe en de onderliggende processen).  Er wordt een gebonden socket gemaakt door api's van binding()/connect() aan te roepen en blijft totdat deze socket is gesloten met CloseHandle()/closesocket(). |
| **CPU-tijd** | De hoeveelheid CPU verbruikt door de app, in seconden. Zie [CPU-tijd vs CPU-percentage](#cpu-time-vs-cpu-percentage)voor meer informatie over deze statistiek. |
| **Huidige vergaderingen** | Het huidige aantal verzamelingen dat in alle AppDomains in deze toepassing is geladen. |
| **Gegevens in** | De hoeveelheid inkomende bandbreedte verbruikt door de app, in MiB. |
| **Gegevens uit** | De hoeveelheid uitgaande bandbreedte verbruikt door de app, in MiB. |
| **Gebruik van bestandssysteem** | Percentage bestandssysteemquotum dat door de app wordt verbruikt. |
| **Gen 0 Garbage Collections** | Het aantal keren dat de generatie 0-objecten vuilnis zijn verzameld sinds het begin van het app-proces. GCs van hogere generatie omvatten alle gc's van de lagere generatie.|
| **Gen 1 Garbage Collections** | Het aantal keren dat de generatie 1-objecten vuilnis zijn verzameld sinds het begin van het app-proces. GCs van hogere generatie omvatten alle gc's van de lagere generatie.|
| **Gen 2 Garbage Collections** | Het aantal keren dat de generatie 2-objecten vuilnis zijn verzameld sinds het begin van het app-proces.|
| **Aantal ingangen** | Het totale aantal handgrepen dat momenteel door het app-proces wordt geopend.|
| **Http 2xx** | Het aantal aanvragen dat resulteert in een HTTP-statuscode ≥ 200, maar < 300. |
| **Http 3xx** | Het aantal aanvragen dat resulteert in een HTTP-statuscode ≥ 300 maar < 400. |
| **Http 401** | Het aantal aanvragen dat resulteert in http 401-statuscode. |
| **Http 403** | Het aantal aanvragen dat resulteert in http 403-statuscode. |
| **Http 404** | Het aantal aanvragen dat resulteert in http 404-statuscode. |
| **Http 406** | Het aantal aanvragen dat resulteert in http 406-statuscode. |
| **Http 4xx** | Het aantal aanvragen dat resulteert in een HTTP-statuscode ≥ 400 maar < 500. |
| **Http-serverfouten** | Het aantal aanvragen dat resulteert in een HTTP-statuscode ≥ 500, maar < 600. |
| **IO Andere bytes per seconde** | De snelheid waarmee het app-proces bytes uitgeeft aan I/O-bewerkingen die geen gegevens bevatten, zoals controlebewerkingen.|
| **IO Andere bewerkingen per seconde** | De snelheid waarmee het app-proces I/O-bewerkingen uitgeeft die geen lees- of schrijfbewerkingen zijn.|
| **IO Leesbytes per seconde** | De snelheid waarmee het app-proces bytes van I/O-bewerkingen leest.|
| **IO-leesbewerkingen per seconde** | De snelheid waarmee het app-proces wordt uitgegeven, leest I/O-bewerkingen.|
| **IO-schrijfbytes per seconde** | De snelheid waarmee het app-proces bytes voor I/O-bewerkingen schrijft.|
| **IO-schrijfbewerkingen per seconde** | De snelheid waarmee het app-proces wordt uitgegeven, schrijft I/O-bewerkingen.|
| **Geheugenwerkset** | De huidige hoeveelheid geheugen die door de app wordt gebruikt, in MiB. |
| **Privébytes** | Private Bytes is de huidige grootte, in bytes, van het geheugen dat het app-proces heeft toegewezen dat niet kan worden gedeeld met andere processen.|
| **Verzoeken** | Het totale aantal aanvragen, ongeacht de resulterende HTTP-statuscode. |
| **Aanvragen in toepassingswachtrij** | Het aantal aanvragen in de wachtrij voor toepassingsaanvragen.|
| **Aantal draad** | Het aantal threads dat momenteel actief is in het app-proces.|
| **Totaal aantal app-domeinen** | Het huidige aantal AppDomains dat in deze toepassing is geladen.|
| **Totaal aantal app-domeinen gelost** | Het totale aantal AppDomains dat sinds het begin van de toepassing is gelost.|


Voor een App Service-abonnement zijn de beschikbare statistieken:

> [!NOTE]
> Statistieken van het App Service-abonnement zijn alleen beschikbaar voor abonnementen in *basis-,* *standaard-* en *premiumlagen.*
> 

| Gegevens | Beschrijving |
| --- | --- |
| **CPU-percentage** | De gemiddelde CPU die wordt gebruikt in alle exemplaren van het plan. |
| **Geheugenpercentage** | Het gemiddelde geheugen dat wordt gebruikt voor alle exemplaren van het plan. |
| **Gegevens in** | De gemiddelde binnenkomende bandbreedte die wordt gebruikt voor alle exemplaren van het plan. |
| **Gegevens uit** | De gemiddelde uitgaande bandbreedte die wordt gebruikt voor alle exemplaren van het plan. |
| **Lengte van de schijfwachtrij** | Het gemiddelde aantal lees- en schrijfverzoeken dat in de wachtrij stond bij opslag. Een hoge schijfwachtrijlengte is een indicatie van een app die mogelijk vertraagt vanwege overmatige schijf-I/O. |
| **Http-wachtrijlengte** | Het gemiddelde aantal HTTP-aanvragen dat in de wachtrij moest zitten voordat het werd uitgevoerd. Een hoge of toenemende HTTP-wachtrijlengte is een symptoom van een plan onder zware belasting. |

### <a name="cpu-time-vs-cpu-percentage"></a>CPU-tijd vs CPU-percentage
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Er zijn twee statistieken die het CPU-gebruik weerspiegelen:

**CPU-tijd:** handig voor apps die worden gehost in gratis of gedeelde abonnementen, omdat een van hun quota is gedefinieerd in CPU-minuten die door de app worden gebruikt.

**CPU-percentage:** handig voor apps die worden gehost in basis-, standaard- en premiumabonnementen, omdat ze kunnen worden opgeschaald. CPU-percentage is een goede indicatie van het totale gebruik in alle instanties.

## <a name="metrics-granularity-and-retention-policy"></a>Beleid voor gedetailleerdheid en behoud van statistieken
Statistieken voor een app- en app-serviceplan worden geregistreerd en samengevoegd door de service, met de volgende granulaire en bewaarbeleid:

* **Minute** granulariteitsstatistieken worden gedurende 30 uur bewaard.
* **Hour** Uurgranulariteitsstatistieken worden 30 dagen bewaard.
* **Day** Daggranulariteitsstatistieken worden 30 dagen bewaard.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Quota en statistieken controleren in de Azure-portal
Als u de status wilt controleren van de verschillende quota en statistieken die van invloed zijn op een app, gaat u naar de [Azure-portal](https://portal.azure.com).

![Quotagrafiek in de Azure-portal][quotas]

Als u quota wilt vinden, selecteert u > **Instellingenquota**. **Settings** In de grafiek u het als: 
1. De naam van het quotum.
1. Het reset-interval.
1. De huidige limiet.
1. De huidige waarde.

![Metrische grafiek in][metrics] de Azure-portal U hebt rechtstreeks toegang tot statistieken vanaf de pagina **Resourceoverzicht.** Hier ziet u grafieken die enkele van de statistieken van apps weergeven.

Als u op een van deze grafieken klikt, gaat u naar de weergave met statistieken waarin u aangepaste grafieken maken, verschillende statistieken opvragen en nog veel meer. 

Zie [Servicestatistieken controleren](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)voor meer informatie over statistieken .

## <a name="alerts-and-autoscale"></a>Waarschuwingen en automatisch schalen
Statistieken voor een app of een App Service-abonnement kunnen worden aangesloten op waarschuwingen. Zie [Meldingen van waarschuwingen ontvangen](../monitoring-and-diagnostics/insights-alerts-portal.md) voor meer informatie.

App Service-apps die worden gehost in basisplannen of hoger app-serviceabonnementen ondersteunen automatisch schalen. Met autoscale u regels configureren die de statistieken van het App Service-abonnement bewaken. Regels kunnen het aantal instance's verhogen of verlagen, wat mogelijk extra resources biedt. Regels kunnen u ook helpen geld te besparen wanneer de app over-ingericht is.

Zie Hoe u [schalen](../monitoring-and-diagnostics/insights-how-to-scale.md) en [Aanbevolen procedures voor automatisch schalen voor Azure Monitor](../azure-monitor/platform/autoscale-best-practices.md)voor meer informatie over automatisch schalen.

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png