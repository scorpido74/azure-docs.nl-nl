---
title: Een nieuwe app maken - LUIS
titleSuffix: Azure Cognitive Services
description: Uw toepassingen maken en beheren op de luis-webpagina (Language Understanding).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d3f8696388a33a8ea112aae438c6bbe9af520c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220829"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Een nieuwe LUIS-app maken in de LUIS-portal
Er zijn een paar manieren om een LUIS-app te maken. U een LUIS-app maken in de LUIS-portal of via de [LUIS-ontwerp-API's](developer-reference-resource.md).

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="using-the-luis-portal"></a>De LUIS-portal gebruiken

U op verschillende manieren een nieuwe app in de preview-portal maken:

* Begin met een lege app en maak intents, utterances en entiteiten.
* Begin met een lege app en voeg een [vooraf gebouwd domein toe.](luis-how-to-use-prebuilt-domains.md)
* Importeer een LUIS-app uit een `.lu` of `.json` bestand dat al intents, uitingen en entiteiten bevat.

## <a name="using-the-authoring-apis"></a>Api's voor het maken van de auteur gebruiken
U op een aantal manieren een nieuwe app maken met de ontwerp-API's:

* [Toepassing toevoegen](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) - begin met een lege app en maak intents, uitingen en entiteiten.
* [Prebuilt application toevoegen](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) - begin met een vooraf gebouwd domein, inclusief intents, uitingen en entiteiten.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Nieuwe app maken in LUIS

1. Selecteer op de pagina **Mijn apps** uw abonnement en auteurbron + **Maken**. Als u de gratis proefsleutel gebruikt, leest u hoe [u een ontwerpbron maakt.](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)

    ![Lijst met apps van LUIS](./media/create-app-in-portal.png)


1. Voer in het dialoogvenster de naam van `Pizza Tutorial`uw toepassing in, zoals .

    ![Dialoogvenster Nieuwe app maken](./media/create-pizza-tutorial-app-in-portal.png)

1. Kies uw toepassingscultuur en selecteer **Gereed**. De beschrijving en voorspelling somist zijn op dit moment optioneel. U dan op elk gewenst moment instellen in het gedeelte **Beheren** van de portal.

    > [!NOTE]
    > De cultuur kan niet worden gewijzigd nadat de toepassing is gemaakt. 

    Nadat de app is gemaakt, toont de LUIS-portal de lijst **Intents** met de `None` intentie die al voor u is gemaakt. Je hebt nu een lege app. 
    
    > [!div class="mx-imgBorder"]
    > ![Intents lijst met Geen intentie gemaakt zonder voorbeeld uitingen.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available"></a>Andere beschikbare acties

De contextwerkbalk biedt andere acties:

* Naam-in-app wijzigen
* Importeren uit `.lu` bestand met of`.json`
* App exporteren `.lu` als (voor `.json` `.zip` [LUDown),](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)of (voor [LUIS-container)](luis-container-howto.md)
* Logboeken van containereindpunten importeren om eindpuntuitingen te controleren
* Eindpuntlogboeken exporteren als `.csv`een , voor offline analyse
* App verwijderen

## <a name="next-steps"></a>Volgende stappen

Als uw app-ontwerp intentiedetectie bevat, [maakt u nieuwe intenties](luis-how-to-add-intents.md)en voegt u voorbeelduitingen toe. Als uw app-ontwerp alleen gegevensextractie is, voegt u voorbeelduitingen toe aan de intentie Geen en [maakt u entiteiten](luis-how-to-add-example-utterances.md)en labelt u de voorbeelduitingen met die entiteiten. 
