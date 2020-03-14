---
title: App publiceren-LUIS
titleSuffix: Azure Cognitive Services
description: Wanneer u klaar bent met het ontwikkelen en testen van uw actieve LUIS-app, het beschikbaar stellen aan uw clienttoepassing door deze te publiceren naar het eindpunt.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 676c6d15c4f439543a3ed74627001725632fecfa
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220884"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Uw actieve, getrainde app publiceren naar een staging-of productie-eind punt

Wanneer u klaar bent met het bouwen, trainen en testen van uw actieve LUIS-app, moet u deze beschikbaar maken voor uw client toepassing door deze te publiceren naar het eind punt. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>Publiceren

1. Als u wilt publiceren naar het eind punt, selecteert u in het bovenste deel venster **publiceren** . 

    ![De knop publiceren in de bovenste navigatie balk, rechts](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Selecteer uw instellingen voor het gepubliceerde Voorspellings eindpunt en selecteer vervolgens **publiceren**.

    ![Selecteer publicatie-instellingen en selecteer vervolgens de knop publiceren](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Publicatie sleuven

Selecteer de juiste sleuf wanneer het pop-upvenster wordt weer gegeven: 

* Faseren
* Productie 

Door beide publicatie sleuven te gebruiken, kunt u op deze manier twee verschillende versies van uw app beschikbaar maken voor de gepubliceerde eind punten of dezelfde versie op twee verschillende eind punten. 

### <a name="publishing-regions"></a>Publicatie-regio 's

De app wordt gepubliceerd naar alle regio's die zijn gekoppeld aan de LUIS-Voorspellings eindpunt resources die zijn toegevoegd in de LUIS-Portal op de pagina ->  **[Azure-resources](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** **beheren** . 

Als u bijvoorbeeld een app die is gemaakt op [www.Luis.ai](https://www.luis.ai), een Luis-resource in twee regio's, **westelijke** en **Oost**-as maakt en deze aan de app toevoegt als resources, wordt de app in beide regio's gepubliceerd. Zie [regio's](luis-reference-regions.md)voor meer informatie over Luis regio's.

> [!TIP]
> Er zijn drie ontwerp regio's. U moet ontwerpen in de regio waar u naar wilt publiceren. Als u naar alle regio's wilt publiceren, moet u uw ontwerp proces en het resulterende getrainde model in alle drie de ontwerp regio's beheren. 


## <a name="configuring-publish-settings"></a>Publicatie-instellingen configureren

Nadat u de sleuf hebt geselecteerd, configureert u de publicatie-instellingen voor:

* Sentimentanalyse
* Spelling correctie-v2 alleen Voorspellings eindpunt
* Spraak gebeuren 

Nadat u hebt gepubliceerd, zijn deze instellingen beschikbaar voor controle op de pagina **publicatie-instellingen** van de sectie **beheren** . U kunt de instellingen wijzigen bij elke publicatie. Als u een publicatie annuleert, worden alle wijzigingen die u tijdens het publiceren hebt aangebracht, ook geannuleerd. 

### <a name="when-your-app-is-published"></a>Wanneer uw app wordt gepubliceerd

Als uw app is gepubliceerd, wordt boven aan de browser een melding over een geslaagde poging weer gegeven. De melding bevat ook een koppeling naar de eind punten. 

Als u de eindpunt-URL nodig hebt, selecteert u de koppeling. U kunt ook naar de eind punt-Url's gaan door **beheren** te selecteren in het bovenste menu en vervolgens **Azure-resources** in het linkermenu te selecteren. 

## <a name="sentiment-analysis"></a>Sentimentanalyse

<a name="enable-sentiment-analysis"></a>

Met sentiment analyse kan LUIS worden geïntegreerd met [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) voor het leveren van sentiment-en sleutel woordgroepen analyse. 

U hebt geen een Text Analytics-sleutel opgeven en er zijn geen facturering kosten voor deze service kunt u uw Azure-account. 

Sentimentsgegevens is een score tussen 1 en 0 waarmee wordt aangegeven welke positieve (dichter bij 1) of een negatieve (dichter bij 0) gevoel van de gegevens. Het sentiment-label van `positive`, `neutral`en `negative` is per ondersteunde cultuur. Op dit moment worden alleen sentiment-labels in het Engels ondersteund. 

Zie [sentiment Analysis](luis-concept-data-extraction.md#sentiment-analysis) (Engelstalig) voor meer informatie over het JSON-eindpunt antwoord met sentiment analyse.

## <a name="spelling-correction"></a>Spelling correctie

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

Correcties voor de spelling worden uitgevoerd voordat de LUIS-gebruiker utterance voor spelling. U kunt alle wijzigingen in de oorspronkelijke utterance zien, inclusief de spelling, in het antwoord.

## <a name="speech-priming"></a>Spraak gebeuren

Speech gebeuren is het proces van het gebruik van het verzenden van het LUIS-model naar spraak Services voordat tekst naar spraak wordt geconverteerd. Hiermee kan de spraak service spraak conversie nauw keuriger maken voor uw model. Op die manier kunnen bot spraak-en LUIS-aanvragen en-antwoorden in één aanroep worden gedaan door één spraak aanroep te maken en een LUIS-antwoord terug te krijgen. Het biedt minder latentie in het algemeen.

## <a name="next-steps"></a>Volgende stappen

* Zie [sleutels beheren](./luis-how-to-azure-subscription.md) om sleutels toe te voegen aan de sleutel van een Azure-abonnement op Luis en hoe u de Bing spellingcontrole sleutel instelt en alle intenties in resultaten opneemt.
* Zie [uw app trainen en testen](luis-interactive-test.md) voor instructies over het testen van uw gepubliceerde app in de test console.

