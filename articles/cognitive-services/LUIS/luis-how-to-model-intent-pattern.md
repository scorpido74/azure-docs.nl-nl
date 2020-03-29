---
title: Patronen voegen nauwkeurigheid toe - LUIS
titleSuffix: Azure Cognitive Services
description: Voeg patroonsjablonen toe om de nauwkeurigheid van de voorspelling in LUIS-toepassingen (Language Understanding) te verbeteren.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 21afb12bf2464218119ebf52ebd980745e3d731d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76311713"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Patronen toevoegen om de nauwkeurigheid van de voorspelling te verbeteren
Nadat een LUIS-app eindpuntuitingen heeft ontvangen, gebruikt u een [patroon](luis-concept-patterns.md) om de nauwkeurigheid van de voorspelling te verbeteren voor uitingen die een patroon in woordvolgorde en woordkeuze weergeven. Patronen gebruiken specifieke [syntaxis](luis-concept-patterns.md#pattern-syntax) om de locatie aan te geven van: [entiteiten,](luis-concept-entity-types.md) [entiteitrollen](luis-concept-roles.md)en optionele tekst.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> Patronen omvatten alleen machine-geleerde entiteit ouders, niet subcomponenten.

## <a name="adding-example-utterances-as-pattern"></a>Voorbeelduitingen toevoegen als patroon

Als u een patroon voor een entiteit wilt toevoegen, u het patroon op de pagina Intent-details het _gemakkelijkst_ maken. Dit zorgt ervoor dat uw syntaxis overeenkomt met de voorbeeldutterance.

1. Selecteer de app in de [preview LUIS-portal](https://preview.luis.ai)op de pagina **Mijn apps.**
1. Selecteer op de lijst **Intents** de intentienaam van de voorbeeldutterance waaruit u een sjabloonutterance wilt maken.
1. Selecteer op de pagina Intentiedetails de rij voor het voorbeeldutterance dat u als sjabloonutterance wilt gebruiken en selecteer **+ Toevoegen als patroon** op de contextwerkbalk.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van het selecteren van voorbeeldutterance als een sjabloonpatroon op de pagina Intent-details.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. Selecteer **Gereed** op de pagina **Patronen bevestigen** in het pop-upvak. U hoeft de subcomponenten, beperkingen of beschrijvingen van de entiteiten niet te definiëren. U hoeft alleen de door de machine geleerde entiteit op te sommen.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van het bevestigen van voorbeeldutterance als een sjabloonpatroon op de pagina Intent-details.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Als u de sjabloon, zoals het selecteren van tekst `[]` als optioneel, met de (vierkante) haakjes moet bewerken, moet u deze bewerking maken vanaf de pagina **Patronen.**

1. Selecteer in de navigatiebalk **Trainen** om de app te trainen met het nieuwe patroon.

## <a name="add-template-utterance-using-correct-syntax"></a>Sjabloonutterance toevoegen met behulp van de juiste syntaxis

1. Open uw app door de naam ervan te selecteren op de pagina **Mijn apps** en selecteer **Vervolgens Patronen** in het linkerdeelvenster onder **App-prestaties verbeteren**.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van de lijst met patronen](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Selecteer de juiste intentie voor het patroon.

1. Typ in het tekstvak van de sjabloon de sjabloonutterance en selecteer Enter. Wanneer u de entiteitsnaam wilt invoeren, gebruikt u de syntaxis van de juiste patroonentiteit. Begin de syntaxis van de entiteit met `{`. De lijst met entiteiten wordt weergegeven. Selecteer de juiste entiteit.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van entiteit voor patroon](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Als uw entiteit een [rol](luis-concept-roles.md)bevat, geeft `:`u de rol aan `{Location:Origin}`met één dubbele punt, naar de naam van de entiteit, zoals . De lijst met rollen voor de entiteiten wordt weergegeven in een lijst. Selecteer de rol en selecteer Enter.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van entiteit met rol](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Nadat u de juiste entiteit hebt geselecteerd, beëindigt u het invoeren van het patroon en selecteert u Enter. Wanneer u klaar bent met het invoeren van patronen, [traint u](luis-how-to-train.md) uw app.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van het ingevoerde patroon met beide typen entiteiten](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Uw app trainen nadat u het model met patronen hebt gewijzigd
Nadat u een patroon hebt toegevoegd, bewerkt, verwijderd of opnieuw hebt toewijzen, [traint](luis-how-to-train.md) en [publiceert](luis-how-to-publish-app.md) u uw app om uw wijzigingen te laten beïnvloeden op eindpuntquery's.

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Contextuele werkbalk gebruiken

Op de contextuele werkbalk boven de lijst met patronen u:

* Zoeken naar patronen
* Een patroon bewerken
* Afzonderlijk patroon opnieuw toewijzen aan verschillende intenties
* Verschillende patronen opnieuw toewijzen aan verschillende intenties
* Verwijder-een-enkel-patroon
* Verschillende patronen verwijderen
* Lijst met filterpatroon per entiteit
* Lijst-op-intentie filterenpatroon
* Entiteit- of intentiefilter verwijderen
* Patroon toevoegen van bestaande utterance on intent of entiteitspagina

## <a name="next-steps"></a>Volgende stappen

* Leer hoe je [een patroon maken](luis-tutorial-pattern.md) met een patroon.any en rollen met een zelfstudie.
* Meer informatie over het [trainen van](luis-how-to-train.md) uw app.
