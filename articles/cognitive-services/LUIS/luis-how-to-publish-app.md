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
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: f92776072038c5684e9334d2dda1690ebb7bcaa8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257788"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Uw actieve, getrainde app publiceren naar een staging-of productie-eind punt

Wanneer u klaar bent met het ontwikkelen en testen van uw actieve LUIS-app, het beschikbaar stellen aan uw clienttoepassing door deze te publiceren naar het eindpunt. 

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Publiceren

1. Als u wilt publiceren naar het eindpunt, selecteer **publiceren** rechts in het bovenste deelvenster. 

    ![De knop publiceren in de bovenste navigatie balk, rechts](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Selecteer uw instellingen voor het gepubliceerde Voorspellings eindpunt en selecteer vervolgens **publiceren**.

    ![Selecteer publicatie-instellingen en selecteer vervolgens de knop publiceren](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Publicatie sleuven

Selecteer de juiste sleuf wanneer het pop-upvenster wordt weer gegeven: 

* Stell.
* Geproduceerd. 

Door beide publicatie sleuven te gebruiken, kunt u op deze manier twee verschillende versies van uw app beschikbaar maken voor de gepubliceerde eind punten of dezelfde versie op twee verschillende eind punten. 

### <a name="publishing-regions"></a>Publicatie-regio 's

De app wordt gepubliceerd naar alle regio's die zijn gekoppeld aan de LUIS-Voorspellings eindpunt resources die zijn toegevoegd in de LUIS-Portal. 

Als u bijvoorbeeld een app die is gemaakt op [www.Luis.ai](https://www.luis.ai), een Luis-resource in twee regio's, **westelijke** en **Oost**-as maakt en deze aan de app toevoegt als resources, wordt de app in beide regio's gepubliceerd. Zie voor meer informatie over regio's voor LUIS [regio's](luis-reference-regions.md).


## <a name="configuring-publish-settings"></a>Publicatie-instellingen configureren

Nadat u de sleuf hebt geselecteerd, configureert u de publicatie-instellingen voor:

* Sentimentanalyse
* Spelling correctie
* Spraak gebeuren 

Nadat u hebt gepubliceerd, zijn deze instellingen beschikbaar voor controle op de pagina **publicatie-instellingen** van de sectie **beheren** . U kunt de instellingen wijzigen bij elke publicatie. Als u een publicatie annuleert, worden alle wijzigingen die u tijdens het publiceren hebt aangebracht, ook geannuleerd. 

### <a name="when-your-app-is-published"></a>Wanneer uw app wordt gepubliceerd

Wanneer uw app is gepubliceerd, wordt een groen geslaagd-melding weergegeven aan de bovenkant van de browser. De groene meldingsbalk bevat ook een koppeling naar de eindpunten. 

![Pop-upvenster publiceren met koppeling naar eind punt](./media/luis-how-to-publish-app/publish-success.png)

Als u de eindpunt-URL nodig hebt, selecteert u de koppeling. U kunt ook naar de eind punt-Url's gaan door **beheren** te selecteren in het bovenste menu en vervolgens **Azure-resources** in het linkermenu te selecteren. 

## <a name="sentiment-analysis"></a>Sentimentanalyse

<a name="enable-sentiment-analysis"></a>

Sentimentanalyse kunt LUIS integreren met [Tekstanalyse](https://azure.microsoft.com/services/cognitive-services/text-analytics/) sentiment en de sleutel phrase analysis opgeven. 

U hebt geen een Text Analytics-sleutel opgeven en er zijn geen facturering kosten voor deze service kunt u uw Azure-account. 

Sentimentsgegevens is een score tussen 1 en 0 waarmee wordt aangegeven welke positieve (dichter bij 1) of een negatieve (dichter bij 0) gevoel van de gegevens. Het sentiment-label `positive`van `neutral`, en `negative` is per ondersteunde cultuur. Op dit moment worden alleen sentiment-labels in het Engels ondersteund. 

Zie voor meer informatie over de JSON-eindpunt-antwoord met sentimentanalyse, [sentimentanalyse](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Spelling correctie

Correcties voor de spelling worden uitgevoerd voordat de LUIS-gebruiker utterance voor spelling. U kunt alle wijzigingen in de oorspronkelijke utterance zien, inclusief de spelling, in het antwoord.

## <a name="speech-priming"></a>Spraak gebeuren

Speech gebeuren is het proces van het gebruik van het verzenden van het LUIS-model naar spraak Services voordat tekst naar spraak wordt geconverteerd. Hiermee kan de spraak service spraak conversie nauw keuriger maken voor uw model. Op die manier kunnen bot spraak-en LUIS-aanvragen en-antwoorden in één aanroep worden gedaan door één spraak aanroep te maken en een LUIS-antwoord terug te krijgen. Het biedt minder latentie in het algemeen.

## <a name="next-steps"></a>Volgende stappen

* Zie [sleutels beheren](./luis-how-to-azure-subscription.md) sleutels toevoegen aan Azure-abonnement om LUIS en het instellen van de Bing Spell Check-sleutel en alle intents opgenomen in de resultaten.
* Zie [trainen en testen van uw app](luis-interactive-test.md) voor instructies over het testen van de gepubliceerde app in de testconsole.

