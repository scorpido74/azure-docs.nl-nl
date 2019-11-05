---
title: App publiceren-LUIS
titleSuffix: Azure Cognitive Services
description: Wanneer u klaar bent met het maken en testen van uw actieve LUIS-app, moet u deze beschikbaar maken voor uw client toepassing door deze te publiceren naar het eind punt.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 0d3a413249cb9058e4098f2836131494670a1727
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491321"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Uw actieve, getrainde app publiceren naar een staging-of productie-eind punt

Wanneer u klaar bent met het maken en testen van uw actieve LUIS-app, moet u deze beschikbaar maken voor uw client toepassing door deze te publiceren naar het eind punt. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Publiceren

1. Als u wilt publiceren naar het eind punt, selecteert u in het bovenste deel venster **publiceren** . 

    ![De knop publiceren in de bovenste navigatie balk, rechts](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Selecteer uw instellingen voor het gepubliceerde Voorspellings eindpunt en selecteer vervolgens **publiceren**.

    ![Selecteer publicatie-instellingen en selecteer vervolgens de knop publiceren](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Publicatie sleuven

Selecteer de juiste sleuf wanneer het pop-upvenster wordt weer gegeven: 

* Stell.
* Geproduceerd. 

Door beide publicatie sleuven te gebruiken, kunt u op deze manier twee verschillende versies van uw app beschikbaar maken voor de gepubliceerde eind punten of dezelfde versie op twee verschillende eind punten. 

### <a name="publishing-regions"></a>Publicatie regio's

De app wordt gepubliceerd naar alle regio's die zijn gekoppeld aan de LUIS-Voorspellings eindpunt resources die zijn toegevoegd in de LUIS-Portal. 

Als u bijvoorbeeld een app die is gemaakt op [www.Luis.ai](https://www.luis.ai), een Luis-resource in twee regio's, **westelijke** en **Oost**-as maakt en deze aan de app toevoegt als resources, wordt de app in beide regio's gepubliceerd. Zie [regio's](luis-reference-regions.md)voor meer informatie over Luis regio's.


## <a name="configuring-publish-settings"></a>Publicatie-instellingen configureren

Nadat u de sleuf hebt geselecteerd, configureert u de publicatie-instellingen voor:

* Sentimentanalyse
* Spelling correctie
* Spraak gebeuren 

Nadat u hebt gepubliceerd, zijn deze instellingen beschikbaar voor controle op de pagina **publicatie-instellingen** van de sectie **beheren** . U kunt de instellingen wijzigen bij elke publicatie. Als u een publicatie annuleert, worden alle wijzigingen die u tijdens het publiceren hebt aangebracht, ook geannuleerd. 

### <a name="when-your-app-is-published"></a>Wanneer uw app wordt gepubliceerd

Als uw app is gepubliceerd, wordt boven aan de browser een melding over een groen succes weer gegeven. De groene meldings balk bevat ook een koppeling naar de eind punten. 

![Pop-upvenster publiceren met koppeling naar eind punt](./media/luis-how-to-publish-app/publish-success.png)

Als u de URL van het eind punt nodig hebt, selecteert u de koppeling. U kunt ook naar de eind punt-Url's gaan door **beheren** te selecteren in het bovenste menu en vervolgens **Azure-resources** in het linkermenu te selecteren. 

## <a name="sentiment-analysis"></a>Sentimentanalyse

<a name="enable-sentiment-analysis"></a>

Met sentiment analyse kan LUIS worden geïntegreerd met [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) voor het leveren van sentiment-en sleutel woordgroepen analyse. 

U hoeft geen Text Analytics sleutel op te geven en er worden geen kosten in rekening gebracht voor deze service voor uw Azure-account. 

Sentiment-gegevens is een score tussen 1 en 0 die de positieve waarde (dichter bij 1) of negatief (dichter bij 0) sentiment van de gegevens aangeeft. Het sentiment-label van `positive`, `neutral`en `negative` is per ondersteunde cultuur. Op dit moment worden alleen sentiment-labels in het Engels ondersteund. 

Zie [sentiment Analysis](luis-concept-data-extraction.md#sentiment-analysis) (Engelstalig) voor meer informatie over het JSON-eindpunt antwoord met sentiment analyse.

## <a name="spelling-correction"></a>Spelling correctie

Correcties voor de spelling worden uitgevoerd voordat de LUIS-gebruiker utterance voor spelling. U kunt alle wijzigingen in de oorspronkelijke utterance zien, inclusief de spelling, in het antwoord.

## <a name="speech-priming"></a>Spraak gebeuren

Speech gebeuren is het proces van het gebruik van het verzenden van het LUIS-model naar spraak Services voordat tekst naar spraak wordt geconverteerd. Hiermee kan de spraak service spraak conversie nauw keuriger maken voor uw model. Op die manier kunnen bot spraak-en LUIS-aanvragen en-antwoorden in één aanroep worden gedaan door één spraak aanroep te maken en een LUIS-antwoord terug te krijgen. Het biedt minder latentie in het algemeen.

## <a name="next-steps"></a>Volgende stappen

* Zie [sleutels beheren](./luis-how-to-azure-subscription.md) om sleutels toe te voegen aan de sleutel van een Azure-abonnement op Luis en hoe u de Bing spellingcontrole sleutel instelt en alle intenties in resultaten opneemt.
* Zie [uw app trainen en testen](luis-interactive-test.md) voor instructies over het testen van uw gepubliceerde app in de test console.

