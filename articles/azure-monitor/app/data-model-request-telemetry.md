---
title: Azure-toepassing Insights-gegevens model voor telemetrie-aanvraag-telemetrie aanvragen | Microsoft Docs
description: Application Insights gegevens model voor aanvraag-telemetrie
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: ff7b52cbd88e4927db275dee4d7fbc4691ad076b
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677332"
---
# <a name="request-telemetry-application-insights-data-model"></a>Telemetrie aanvragen: Application Insights gegevens model

Een telemetrie van aanvragen (in [Application Insights](../../azure-monitor/app/app-insights-overview.md)) vertegenwoordigt de logische volg orde van uitvoering die door een externe aanvraag naar uw toepassing wordt geactiveerd. Elke uitvoering van een aanvraag wordt geïdentificeerd door unieke `ID` en `url` met alle uitvoerings parameters. U kunt aanvragen groeperen op logische `name` en de `source` van deze aanvraag definiëren. Uitvoering van code kan resulteren in `success` of `fail` en een bepaalde `duration` heeft. Geslaagde en mislukte uitvoeringen kunnen verder worden gegroepeerd door `resultCode`. Begin tijd voor de telemetrie van de aanvraag die is gedefinieerd op het envelop niveau.

Telemetrie van aanvragen ondersteunt het model voor standaard uitbreid baarheid met aangepaste `properties` en `measurements`.

## <a name="name"></a>Naam

De naam van de aanvraag vertegenwoordigt het codepad dat is gemaakt om de aanvraag te verwerken. Lage kardinaliteit waarde om een betere groepering van aanvragen mogelijk te maken. Voor HTTP-aanvragen vertegenwoordigt deze de HTTP-methode en het URL-pad sjabloon zoals `GET /values/{id}` zonder de daad werkelijke `id` waarde.

Application Insights Web-SDK verzendt de naam van de aanvraag ' in de staat ' met betrekking tot letters. Groeperen op de gebruikers interface is hoofdletter gevoelig, dus `GET /Home/Index` wordt afzonderlijk van `GET /home/INDEX` geteld, zelfs als ze vaak worden geretourneerd door dezelfde controller en actie-uitvoering. De reden hiervoor is dat url's in het algemeen [hoofdletter gevoelig](https://www.w3.org/TR/WD-html40-970708/htmlweb.html)zijn. U kunt bijvoorbeeld zien of alle `404` heeft plaatsgevonden voor de url's die in hoofd letters worden getypt. Meer informatie over de aanvraag naam verzameling door ASP.NET Web SDK vindt u in het [blog bericht](https://apmtips.com/blog/2015/02/23/request-name-and-url/).

Maximale lengte: 1024 tekens

## <a name="id"></a>Id

Id van een exemplaar van een aanvraag aanroep. Wordt gebruikt voor correlatie tussen aanvraag en andere telemetrie-items. De ID moet globaal uniek zijn. Zie [correlatie](../../azure-monitor/app/correlation.md) pagina voor meer informatie.

Maximale lengte: 128 tekens

## <a name="url"></a>URL

Aanvraag-URL met alle query reeks parameters.

Maximale lengte: 2048 tekens

## <a name="source"></a>Bron

De bron van de aanvraag. Voor beelden zijn de instrumentatie sleutel van de aanroeper of het IP-adres van de aanroeper. Zie [correlatie](../../azure-monitor/app/correlation.md) pagina voor meer informatie.

Maximale lengte: 1024 tekens

## <a name="duration"></a>Duur

De duur van de aanvraag in de indeling: `DD.HH:MM:SS.MMMMMM`. Moet positief zijn en kleiner zijn dan `1000` dagen. Dit veld is vereist omdat de aanvraag-telemetrie de bewerking met het begin en het einde vertegenwoordigt.

## <a name="response-code"></a>Antwoord code

Resultaat van het uitvoeren van een aanvraag. HTTP-status code voor HTTP-aanvragen. Dit kan `HRESULT` waarde zijn of het type uitzonde ring voor andere aanvraag typen.

Maximale lengte: 1024 tekens

## <a name="success"></a>Geslaagd

Indicatie van een geslaagde of mislukte aanroep. Dit veld is verplicht. Wanneer niet expliciet worden ingesteld op `false`, wordt een aanvraag als geslaagd beschouwd. Stel deze waarde in op `false` als de bewerking is onderbroken door een uitzonde ring of geretourneerde fout code.

Voor de webtoepassingen Application Insights een aanvraag definiëren als geslaagd wanneer de respons code kleiner is dan `400` of gelijk is aan `401`. Er zijn echter gevallen waarin deze standaard toewijzing niet overeenkomt met de semantiek van de toepassing. De reactie code `404` kan duiden op "no records", die deel kunnen uitmaken van de normale stroom. Dit kan ook duiden op een verbroken koppeling. Voor de verbroken koppelingen kunt u zelfs meer geavanceerde logica implementeren. U kunt verbroken koppelingen alleen als fouten markeren als deze koppelingen zich op dezelfde site bevinden door het analyseren van URL-verwijzings bronnen. Of markeer ze als storingen wanneer ze worden geopend vanuit de mobiele toepassing van het bedrijf. Evenzo `301` en `302` geeft aan dat er een fout is opgetreden bij het openen van de client die geen ondersteuning biedt voor omleiden.

Gedeeltelijk geaccepteerde inhoud `206` kan duiden op een fout in een algemene aanvraag. Application Insights eind punt ontvangt bijvoorbeeld een batch met telemetrie-items als één aanvraag. Het retourneert `206` als sommige items in de batch niet zijn verwerkt. Het toenemende aantal `206` geeft aan dat er een probleem is dat moet worden onderzocht. Soort gelijke logica is van toepassing op `207` meerdere statussen, waarbij het succes het slechtste aantal afzonderlijke antwoord codes kan zijn.

Meer informatie over de aanvraag resultaat code en status code vindt u in het [blog bericht](https://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Aangepaste eigenschappen

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Aangepaste metingen

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Volgende stappen

- [Telemetrie van aangepaste aanvraag schrijven](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)
- Zie [gegevens model](data-model.md) voor Application Insights typen en gegevens model.
- Meer informatie over het [configureren van ASP.net core](../../azure-monitor/app/asp-net.md) toepassing met Application Insights.
- Bekijk de [platforms](../../azure-monitor/app/platforms.md) die door Application Insights worden ondersteund.
