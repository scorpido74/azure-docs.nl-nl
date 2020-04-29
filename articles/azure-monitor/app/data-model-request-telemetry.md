---
title: Gegevens model voor aanvraag-telemetrie-Azure-toepassing Insights
description: Application Insights gegevens model voor aanvraag-telemetrie
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: d8a28063bf6780c3cace4ead81e289779b95eb9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671899"
---
# <a name="request-telemetry-application-insights-data-model"></a>Telemetrie aanvragen: Application Insights gegevens model

Een telemetrie van aanvragen (in [Application Insights](../../azure-monitor/app/app-insights-overview.md)) vertegenwoordigt de logische volg orde van uitvoering die door een externe aanvraag naar uw toepassing wordt geactiveerd. Elke aanvraag uitvoering wordt geïdentificeerd met unieke `ID` en `url` bevat alle uitvoerings parameters. U kunt aanvragen groeperen op logische `name` en de `source` van deze aanvraag definiëren. Uitvoering van code kan resulteren `success` in `fail` of en heeft een `duration`bepaalde. Geslaagde en mislukte uitvoeringen kunnen verder worden gegroepeerd door `resultCode`. Begin tijd voor de telemetrie van de aanvraag die is gedefinieerd op het envelop niveau.

Telemetrie aanvragen ondersteunt het model voor standaard uitbreid `properties` baarheid `measurements`met behulp van aangepast en.

## <a name="name"></a>Naam

De naam van de aanvraag vertegenwoordigt het codepad dat is gemaakt om de aanvraag te verwerken. Lage kardinaliteit waarde om een betere groepering van aanvragen mogelijk te maken. Voor HTTP-aanvragen vertegenwoordigt het de sjabloon HTTP-methode en URL `GET /values/{id}` -pad, `id` zoals zonder de werkelijke waarde.

Application Insights Web-SDK verzendt de naam van de aanvraag ' in de staat ' met betrekking tot letters. Groeperen op de gebruikers interface is hoofdletter gevoelig `GET /Home/Index` , dus wordt afzonderlijk geteld `GET /home/INDEX` , zelfs als ze vaak resulteren in dezelfde controller en uitgevoerde actie. De reden hiervoor is dat url's in het algemeen [hoofdletter gevoelig](https://www.w3.org/TR/WD-html40-970708/htmlweb.html)zijn. U kunt bijvoorbeeld zien of alle `404` url's zijn getypt in hoofd letters. Meer informatie over de aanvraag naam verzameling door ASP.NET Web SDK vindt u in het [blog bericht](https://apmtips.com/blog/2015/02/23/request-name-and-url/).

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

De duur van aanvragen in `DD.HH:MM:SS.MMMMMM`de indeling:. Moet positief en kleiner dan `1000` dagen zijn. Dit veld is vereist omdat de aanvraag-telemetrie de bewerking met het begin en het einde vertegenwoordigt.

## <a name="response-code"></a>Antwoord code

Resultaat van het uitvoeren van een aanvraag. HTTP-status code voor HTTP-aanvragen. Dit kan een `HRESULT` waarde zijn of een uitzonderings type voor andere aanvraag typen.

Maximale lengte: 1024 tekens

## <a name="success"></a>Geslaagd

Indicatie van een geslaagde of mislukte aanroep. Dit veld is vereist. Wanneer niet expliciet wordt ingesteld `false` op-een aanvraag, wordt beschouwd als geslaagd. Stel deze waarde in `false` op als de bewerking is onderbroken door een uitzonde ring of geretourneerde fout resultaat code.

Voor de webtoepassingen Application Insights een aanvraag definiëren als geslaagd wanneer de respons code kleiner is dan `400` of gelijk is `401`aan. Er zijn echter gevallen waarin deze standaard toewijzing niet overeenkomt met de semantiek van de toepassing. De antwoord `404` code kan duiden op "no records", die deel kunnen uitmaken van de normale stroom. Dit kan ook duiden op een verbroken koppeling. Voor de verbroken koppelingen kunt u zelfs meer geavanceerde logica implementeren. U kunt verbroken koppelingen alleen als fouten markeren als deze koppelingen zich op dezelfde site bevinden door het analyseren van URL-verwijzings bronnen. Of markeer ze als storingen wanneer ze worden geopend vanuit de mobiele toepassing van het bedrijf. En `301` `302` duidt op fout bij het openen van de client die geen ondersteuning biedt voor omleiden.

Gedeeltelijk geaccepteerde `206` inhoud kan duiden op een fout in een algemene aanvraag. Application Insights eind punt ontvangt bijvoorbeeld een batch met telemetrie-items als één aanvraag. Er wordt `206` een resultaat weer gegeven wanneer sommige items in de batch niet zijn verwerkt. Een hogere frequentie `206` geeft aan dat er een probleem is dat moet worden onderzocht. Een soort gelijke logica `207` is van toepassing op meerdere statussen, waarbij het resultaat mogelijk het ergste aantal afzonderlijke respons codes kan zijn.

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
