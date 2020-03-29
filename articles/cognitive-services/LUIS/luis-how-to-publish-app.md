---
title: App publiceren - LUIS
titleSuffix: Azure Cognitive Services
description: Wanneer u klaar bent met het bouwen en testen van uw actieve LUIS-app, maakt u deze beschikbaar voor uw clienttoepassing door deze te publiceren naar het eindpunt.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: c91a3ca73d70dd5fd2848bed0f43f14a817087d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053438"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Uw actieve, getrainde app publiceren naar een endpoint voor fasering of productie

Wanneer u klaar bent met het bouwen, trainen en testen van uw actieve LUIS-app, maakt u deze beschikbaar voor uw clienttoepassing door deze te publiceren naar het eindpunt. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>Publiceren

1. Als u wilt publiceren naar het eindpunt, selecteert u **Publiceren** in het bovenste, rechterdeelvenster. 

    ![Knop Publiceren in de bovenste, rechternavigatiebalk](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Selecteer uw instellingen voor het gepubliceerde voorspellingseindpunt en selecteer **Vervolgens Publiceren**.

    ![Selecteer publicatie-instellingen en selecteer vervolgens knop Publiceren](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Publicatieslots

Selecteer de juiste sleuf wanneer het pop-upvenster wordt weergegeven: 

* Faseren
* Productie 

Door beide publicatieslots te gebruiken, u met deze twee verschillende versies van uw app beschikbaar zijn op de gepubliceerde eindpunten of dezelfde versie op twee verschillende eindpunten. 

### <a name="publishing-regions"></a>Publicatieregio's

De app wordt gepubliceerd in alle regio's die zijn gekoppeld aan de LUIS-voorspellingseindpuntbronnen die zijn toegevoegd in de LUIS-portal vanaf de pagina Azure Resources **beheren.** -> **[Azure Resources](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** 

Voor een app die op [www.luis.ai](https://www.luis.ai)is gemaakt en als u bijvoorbeeld een LUIS-bron maakt in twee regio's, **westus** en **eastus,** en deze als resources aan de app toevoegt, wordt de app in beide regio's gepubliceerd. Zie Regio's voor meer informatie over [LUIS-regio's](luis-reference-regions.md).

> [!TIP]
> Er zijn 3 authoring regio's. U moet een auteur maken in de regio waarop u wilt publiceren. Als u in alle regio's wilt publiceren, moet u uw ontwerpproces en het resulterende getrainde model in alle 3 ontwerpregio's beheren. 


## <a name="configuring-publish-settings"></a>Publicatie-instellingen configureren

Nadat u de sleuf hebt geselecteerd, configureert u de publicatie-instellingen voor:

* Sentimentanalyse
* Alleen spellingcorrectie - v2-voorspellingseindpunt
* Het priming van de toespraak 

Nadat u hebt gepubliceerd, zijn deze instellingen beschikbaar voor controle op de pagina **Publicatie-instellingen van** de sectie **Beheren.** U de instellingen bij elke publicatie wijzigen. Als u een publicatie annuleert, worden alle wijzigingen die u tijdens de publicatie hebt aangebracht, ook geannuleerd. 

### <a name="when-your-app-is-published"></a>Wanneer uw app wordt gepubliceerd

Wanneer uw app is gepubliceerd, wordt boven aan de browser een melding voor succes weergegeven. De melding bevat ook een link naar de eindpunten. 

Als u de URL van het eindpunt nodig hebt, selecteert u de koppeling. U ook naar de URL's van het eindpunt gaan door **Beheren** in het bovenste menu te selecteren en vervolgens **Azure Resources** in het linkermenu te selecteren. 

## <a name="sentiment-analysis"></a>Sentimentanalyse

<a name="enable-sentiment-analysis"></a>

Sentimentanalyse stelt LUIS in staat om te integreren met [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) om sentiment en key phrase analyse te bieden. 

U hoeft geen Text Analytics-sleutel te verstrekken en er zijn geen factureringskosten voor deze service voor uw Azure-account. 

Sentimentgegevens zijn een score tussen 1 en 0 die het positieve (dichter bij 1) of negatieve (dichter bij 0) sentiment van de gegevens aangeven. Het sentiment `positive`label `neutral`van `negative` , en is per ondersteunde cultuur. Momenteel ondersteunt alleen Engels sentimentlabels. 

Zie [Sentimentanalyse](luis-concept-data-extraction.md#sentiment-analysis) voor meer informatie over de JSON-eindpuntrespons met sentimentanalyse

## <a name="spelling-correction"></a>Spellingcorrectie

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

Correcties op de spelling worden uitgevoerd vóór de luis-gebruiker utterance voorspelling. U eventuele wijzigingen in de oorspronkelijke utterance, inclusief spelling, in het antwoord zien.

## <a name="speech-priming"></a>Het priming van de toespraak

Spraakpriming is het proces van het verzenden van het LUIS-model naar spraakservices voorafgaand aan de conversie van tekst naar spraak. Hierdoor kan de spraakservice spraakconversie nauwkeuriger voor uw model bieden. Dit maakt bot spraak en LUIS verzoeken en antwoorden in een gesprek door het maken van een spraakoproep en het krijgen van een LUIS reactie. Het biedt minder latentie in het algemeen.

## <a name="next-steps"></a>Volgende stappen

* Zie [Sleutels beheren](./luis-how-to-azure-subscription.md) om sleutels toe te voegen aan Azure-abonnementssleutel voor LUIS en hoe u de Bing Spell Check-toets instelt en alle intenties in de resultaten opneemt.
* Zie [Uw app trainen en testen](luis-interactive-test.md) voor instructies over het testen van uw gepubliceerde app in de testconsole.

