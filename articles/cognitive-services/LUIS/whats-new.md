---
title: Wat is er nieuw-Language Understanding (LUIS)
description: Dit artikel wordt regel matig bijgewerkt met nieuws over de Azure Cognitive Services Language Understanding-API.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 716860b54e7d8e75984c0365cac61d14153c09ff
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137795"
---
# <a name="whats-new-in-language-understanding"></a>Wat is er nieuw in Language Understanding

Meer informatie over wat er nieuw is in de service. Deze items zijn release opmerkingen, Video's, blog berichten en andere soorten informatie. Bladwijzer deze pagina om up-to-date te blijven met de service.

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="november-4-2019---ignite"></a>4 november 2019-Ignite

* Video- [geavanceerde natuurlijke language Understanding-modellen (NLU) met Luis en Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Verbeterde productiviteit van ontwikkel aars
    * Algemene Beschik baarheid van ons [Voorspellings eindpunt v3](luis-migration-api-v3.md).
    * De mogelijkheid om apps te importeren en exporteren met de. lu-indeling ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)). Dit wordt de manier om een effectief CI/CD-proces uit te voeren.
* Taal uitbreiding
    * [Arabisch en Hindi](luis-language-support.md) in open bare preview.
* Vooraf gemaakte modellen
    * [Vooraf gebouwde domeinen](luis-reference-prebuilt-domains.md) zijn nu algemeen beschikbaar (ga)
    * Japanse [vooraf gemaakte entiteiten](luis-reference-prebuilt-entities.md#japanese-entity-support) : leeftijd, valuta, getal en percentage worden niet ondersteund in v3.
    * Italiaanse [vooraf gemaakte entiteiten](luis-reference-prebuilt-entities.md#italian-entity-support) -leeftijd, valuta, dimensie, getal en percentage resolutie worden gewijzigd van v2.
* Verbeterde gebruikers ervaring in de [Preview.Luis.ai-Portal](https://preview.luis.ai) , vernieuwd, waarmee u complexe modellen kunt bouwen en fouten kunt opsporen. Probeer de preview-portal zelf studies:
    * [Alleen intentie](tutorial-intents-only.md)
    * [Door de computer geleerde entiteit](tutorial-machine-learned-entity.md)
* Geavanceerde taal mogelijkheden: [Geavanceerde taal modellen bouwen](luis-concept-entity-types.md) met minder inspanning.
* Definieer machine learning functies op model niveau en zorg ervoor dat modellen kunnen worden gebruikt als signalen voor andere modellen, bijvoorbeeld door entiteiten te gebruiken als functies voor intenties en andere entiteiten.
* Nieuwe, uitgebreide [limieten](luis-boundaries.md) : hoger maximum voor woordgroepen lijsten en totaal aantal zinsdelen, nieuw model als functie limieten
* Haal informatie op uit tekst in de indeling van een diepe hiërarchie structuur, zodat conversatie toepassingen krachtiger worden.

    ![door de machine geleerde entiteits afbeelding](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 september 2019

* Azure authoring resource- [nu migreren](luis-migration-authoring.md).
    * 500-apps per Azure-resource
    * 100-versies per app
* Turkse ondersteuning voor vooraf gemaakte entiteiten
* Italiaanse ondersteuning voor datetimeV2

### <a name="july-23-2019"></a>23 juli 2019

* De [recognizers bijwerken-tekst](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) naar 1.2.3
    * De kenmerken voor leeftijd, Tempe ratuur, dimensie en valuta in het Italiaans.
    * Verbetering van de erkenning van feest dagen in het Engels om op juiste wijze op Thanksgiving gebaseerde datums te berekenen.
    * Verbeteringen in Franse datum tijd om de fout-positieven van niet-datum-en niet-tijd-entiteiten te verminderen.
    * Ondersteuning voor calendar/school/boek jaar en acroniemen in de Engelse DateRange.
    * Verbeterde PhoneNumber-herkenning in Chinees en Japans.
    * Verbeterde ondersteuning voor NumberRange in het Engels.
    * Prestatie verbeteringen.

### <a name="june-24-2019"></a>24 juni 2019

* [OrdinalV2 preconstrueerde entiteit](luis-reference-prebuilt-ordinal-v2.md) ter ondersteuning van de volg orde zoals volgende, vorige en laatste. Alleen Engelse cultuur.

### <a name="may-6-2019---build-conference"></a>6 mei 2019-build-conferentie

De volgende functies zijn uitgebracht op de build 2019-conferentie:

* [Preview van de V3 API-migratie handleiding](luis-migration-api-v3.md)
* [Verbeterd analyse dashboard](luis-how-to-use-dashboard.md)
* [Verbeterde, vooraf ontwikkelde domeinen](luis-reference-prebuilt-domains.md)
* [Dynamische lijst entiteiten](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Externe entiteiten](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogs

[Bot-Framework](https://blog.botframework.com/)

## <a name="videos"></a>Video's

### <a name="2019-ignite-videos"></a>2019 Ignite-Video's

[Geavanceerde natuurlijke Language Understanding-modellen (NLU) met LUIS en Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 Video's bouwen

[Azure-conversatie AI gebruiken om uw bedrijf te schalen voor de volgende generatie](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Service-updates

[Meldingen voor Azure update voor Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
