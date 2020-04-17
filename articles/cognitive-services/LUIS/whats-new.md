---
title: Wat is er nieuw - Taalbegrip (LUIS)
description: Dit artikel wordt regelmatig bijgewerkt met nieuws over de Azure Cognitive Services Language Understanding API.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 6334b157f68f09f2b165c6073ba63f45a0caf3c4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538015"
---
# <a name="whats-new-in-language-understanding"></a>Nieuwe informatie in taalbegrip

Meer informatie over wat er nieuw is in de service. Deze items omvatten release notes, video's, blog posts en andere soorten informatie. Bladwijzer deze pagina om up-to-date te blijven met de service.

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="march-2020"></a>Maart 2020

* TLS 1.2 wordt nu afgedwongen voor alle HTTP-aanvragen voor deze service. Zie [Azure Cognitive Services-beveiliging](../cognitive-services-security.md)voor meer informatie.

### <a name="november-4-2019---ignite"></a>4 november 2019 - Ignite

* Video - [NlU-modellen (Advanced Natural Language Understanding) gebruiken LUIS- en Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Verbeterde productiviteit van ontwikkelaars
    * Algemene beschikbaarheid van ons [voorspellingseindpunt V3](luis-migration-api-v3.md).
    * Mogelijkheid om apps te importeren en exporteren met .lu[(LUDown)](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)indeling. Dit maakt de weg vrij voor een effectief CI/CD-proces.
* Taaluitbreiding
    * [Arabisch en Hindi](luis-language-support.md) in openbare voorvertoning.
* Vooraf gemaakte modellen
    * [Vooraf gebouwde domeinen](luis-reference-prebuilt-domains.md) zijn nu algemeen beschikbaar (GA)
    * Japanse [vooraf gebouwde entiteiten](luis-reference-prebuilt-entities.md#japanese-entity-support) - leeftijd, valuta, aantal en percentage worden niet ondersteund in V3.
    * Italiaanse [vooraf gebouwde entiteiten](luis-reference-prebuilt-entities.md#italian-entity-support) - leeftijd, valuta, dimensie, aantal en procentuele resolutie gewijzigd van V2.
* Verbeterde gebruikerservaring in [preview.luis.ai portal](https://preview.luis.ai) - vernieuwde labelervaring om complexe modellen te bouwen en debuggen mogelijk te maken. Probeer de zelfstudies van de preview-portal:
    * [Alleen intenties](tutorial-intents-only.md)
    * [Onbestaandeerbare door de machine geleerde entiteit](tutorial-machine-learned-entity.md)
* Geavanceerde mogelijkheden voor taalbegrip - [het bouwen van geavanceerde taalmodellen](luis-concept-entity-types.md) met minder inspanning.
* Definieer machine learning-functies op modelniveau en maak het mogelijk om modellen te gebruiken als signalen naar andere modellen, bijvoorbeeld door entiteiten te gebruiken als functies voor intents en naar andere entiteiten.
* Nieuwe, uitgebreide [limieten](luis-boundaries.md) - hoger maximum voor woordgroeplijsten en totale woordgroepen, nieuw model als functielimieten
* Haal informatie uit tekst in de vorm van een diepe hiërarchiestructuur, waardoor gesprekstoepassingen krachtiger worden.

    ![afbeelding van door machine geleerde entiteit](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 september 2019

* Azure-ontwerpbron - [migreer nu](luis-migration-authoring.md).
    * 500 apps per Azure-bron
    * 100 versies per app
* Turkse steun voor voorgebouwde entiteiten
* Italiaanse steun voor datetimeV2

### <a name="july-23-2019"></a>23 juli 2019

* De [recognizers-tekst bijwerken](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) naar 1.2.3
    * Leeftijd, temperatuur, dimensie en valuta-erkenden in het Italiaans.
    * Verbetering in de erkenning van de vakantie in het Engels om correct te berekenen Thanksgiving-gebaseerde data.
    * Verbeteringen in Franse DateTime om valse positieven van niet-datum- en niet-time entiteiten te verminderen.
    * Ondersteuning voor agenda/school/boekjaar en afkortingen in Het Engels DateRange.
    * Verbeterde telefoonNummerherkenning in het Chinees en Japans.
    * Verbeterde ondersteuning voor NumberRange in het Engels.
    * Prestatieverbeteringen.

### <a name="june-24-2019"></a>24 juni 2019

* [OrdinalV2 vooraf gebouwde entiteit](luis-reference-prebuilt-ordinal-v2.md) ter ondersteuning van het bestellen, zoals volgende, vorige en laatste. Alleen Engelse cultuur.

### <a name="may-6-2019---build-conference"></a>6 mei 2019 - Build Conference

De volgende functies zijn uitgebracht op de Build 2019-conferentie:

* [Voorbeeld van V3 API-migratiehandleiding](luis-migration-api-v3.md)
* [Verbeterd analysedashboard](luis-how-to-use-dashboard.md)
* [Verbeterde vooraf gebouwde domeinen](luis-reference-prebuilt-domains.md)
* [Dynamische lijstentiteiten](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Externe entiteiten](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogs

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>Video's

### <a name="2019-ignite-videos"></a>Ignite video's 2019

[NLU-modellen (Advanced Natural Language Understanding) gebruiken LUIS- en Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 Bouw video's

[Azure Conversational AI gebruiken om uw bedrijf te schalen voor de volgende generatie](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Service-updates

[Azure-update-aankondigingen voor Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
