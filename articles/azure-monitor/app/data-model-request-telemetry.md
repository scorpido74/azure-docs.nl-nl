---
title: Gegevensmodel voor aanvraagtelemetrie - Azure Application Insights
description: Application Insights-gegevensmodel voor aanvraagtelemetrie
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: d8a28063bf6780c3cace4ead81e289779b95eb9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671899"
---
# <a name="request-telemetry-application-insights-data-model"></a>Telemetrie aanvragen: gegevensmodel Application Insights

Een item voor het telemetrieitem van een aanvraag (in [Application Insights)](../../azure-monitor/app/app-insights-overview.md)vertegenwoordigt de logische reeks uitvoering die wordt geactiveerd door een extern verzoek aan uw toepassing. Elke aanvraaguitvoering wordt geïdentificeerd `ID` `url` door uniek en bevat alle uitvoeringsparameters. U aanvragen `name` op logische `source` wijze groeperen en de van deze aanvraag definiëren. Code uitvoering kan `success` `fail` resulteren in `duration`of en heeft een bepaalde . Zowel succes- als faaluitvoeringen `resultCode`kunnen verder worden gegroepeerd door . Begintijd voor de aanvraagtelemetrie gedefinieerd op het envelopniveau.

Aanvraag telemetrie ondersteunt het standaard `properties` extensibility-model met behulp van aangepaste en `measurements`.

## <a name="name"></a>Name

De naam van de aanvraag vertegenwoordigt het codepad dat is gemaakt om de aanvraag te verwerken. Lage kardinaliteitswaarde om een betere groepering van aanvragen mogelijk te maken. Voor HTTP-aanvragen vertegenwoordigt het de HTTP-methode en URL-padsjabloon zoals `GET /values/{id}` zonder de werkelijke `id` waarde.

Application Insights web SDK stuurt aanvraagnaam "as is" met betrekking tot brief geval. Groeperen op ui is hoofdlettergevoelig, dus `GET /Home/Index` wordt `GET /home/INDEX` apart van elkaar geteld, hoewel ze vaak resulteren in dezelfde controller en actie-uitvoering. De reden hiervoor is dat url's in het algemeen [zijn case-sensitive](https://www.w3.org/TR/WD-html40-970708/htmlweb.html). U zien of `404` alles is gebeurd voor de url's getypt in hoofdletters. U meer lezen over het verzamelen van naam van de aanvraag door ASP.NET Web SDK in de [blogpost](https://apmtips.com/blog/2015/02/23/request-name-and-url/).

Maximale lengte: 1024 tekens

## <a name="id"></a>Id

Id van een instantie voor een aanvraagoproep. Wordt gebruikt voor correlatie tussen aanvraag- en andere telemetrie-items. ID moet wereldwijd uniek zijn. Zie [correlatiepagina voor](../../azure-monitor/app/correlation.md) meer informatie.

Maximale lengte: 128 tekens

## <a name="url"></a>URL

URL aanvragen met alle parameters van de querytekenreeks.

Maximale lengte: 2048 tekens

## <a name="source"></a>Bron

Bron van het verzoek. Voorbeelden zijn de instrumentatiesleutel van de beller of het ip-adres van de beller. Zie [correlatiepagina voor](../../azure-monitor/app/correlation.md) meer informatie.

Maximale lengte: 1024 tekens

## <a name="duration"></a>Duur

Aanvraagduur in `DD.HH:MM:SS.MMMMMM`notatie: . Moet positief zijn `1000` en minder dan dagen. Dit veld is vereist omdat de telemetrie van de aanvraag de bewerking met het begin en het einde vertegenwoordigt.

## <a name="response-code"></a>Reactiecode

Resultaat van een aanvraag uitvoering. HTTP-statuscode voor HTTP-aanvragen. Het kan `HRESULT` waarde- of uitzonderingstype zijn voor andere aanvraagtypen.

Maximale lengte: 1024 tekens

## <a name="success"></a>Geslaagd

Indicatie van een geslaagde of mislukte oproep. Dit veld is vereist. Wanneer niet expliciet `false` ingesteld op - een verzoek wordt beschouwd als succesvol. Stel deze `false` waarde in op als de bewerking is onderbroken door een uitzondering of foutresultaatcode is geretourneerd.

Voor de webtoepassingen definiëren Application Insights een aanvraag als `400` geslaagd wanneer `401`de antwoordcode kleiner is dan of gelijk is aan . Er zijn echter gevallen waarin deze standaardtoewijzing niet overeenkomt met de semantische van de toepassing. Responscode `404` kan duiden op "geen records", die deel kunnen uitmaken van de reguliere stroom. Het kan ook wijzen op een gebroken link. Voor de verbroken koppelingen u zelfs geavanceerdere logica implementeren. U verbroken koppelingen alleen als fouten markeren wanneer deze koppelingen zich op dezelfde site bevinden door url-verwijzer te analyseren. Of markeer ze als fouten wanneer ze worden geopend via de mobiele toepassing van het bedrijf. Op `301` dezelfde `302` manier en geeft een fout aan wanneer deze wordt geopend vanuit de client die geen omleiding ondersteunt.

Gedeeltelijk geaccepteerde `206` inhoud kan duiden op een fout van een algemeen verzoek. Het eindpunt van Application Insights ontvangt bijvoorbeeld een batch telemetrie-items als één aanvraag. Het `206` retourneert wanneer sommige artikelen in de batch niet zijn verwerkt. Toenemende snelheid `206` van geeft een probleem dat moet worden onderzocht. Vergelijkbare logica `207` is van toepassing op Multi-Status waar het succes het ergste van afzonderlijke antwoordcodes kan zijn.

U meer lezen over de resultaatcode en statuscode van het verzoek in het [blogbericht.](https://apmtips.com/blog/2016/12/03/request-success-and-response-code/)

## <a name="custom-properties"></a>Aangepaste eigenschappen

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Aangepaste metingen

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Volgende stappen

- [Aangepaste aanvraagtelemetrie schrijven](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)
- Zie [gegevensmodel](data-model.md) voor toepassingsinzichten en gegevensmodel.
- Meer informatie over het [configureren van ASP.NET Core-toepassing](../../azure-monitor/app/asp-net.md) met Application Insights.
- Bekijk [platforms](../../azure-monitor/app/platforms.md) die worden ondersteund door Application Insights.
