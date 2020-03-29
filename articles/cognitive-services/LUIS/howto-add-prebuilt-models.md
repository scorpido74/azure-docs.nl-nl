---
title: Vooraf gebouwde modellen voor taalbegrip
titleSuffix: Azure Cognitive Services
description: LUIS bevat een set vooraf gebouwde modellen voor het snel toevoegen van algemene, conversationele gebruikersscenario's.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013583"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Vooraf gebouwde modellen toevoegen voor veelgebruikte gebruiksscenario's 

LUIS bevat een set vooraf gebouwde modellen voor het snel toevoegen van algemene, conversationele gebruikersscenario's. Dit is een snelle en eenvoudige manier om vaardigheden toe te voegen aan uw conversationele client applicatie zonder de modellen voor die vaardigheden te ontwerpen. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>Een vooraf gedefinieerd domein toevoegen

1. Selecteer **op** de pagina Mijn apps uw app. Hiermee opent u uw app naar het gedeelte **Bouwen** van de app. 

1. Selecteer **Vooraf gebouwde domeinen** op de linkerwerkbalk. 

1. Zoek het domein dat u aan de app wilt toevoegen en selecteer **domeinknop toevoegen.**

    > [!div class="mx-imgBorder"]
    > ![Agenda-vooraf gebouwd domein toevoegen](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Een vooraf gebouwde intentie toevoegen

1. Selecteer **op** de pagina Mijn apps uw app. Hiermee opent u uw app naar het gedeelte **Bouwen** van de app. 

1. Selecteer op de pagina **Intents** de optie **Vooraf gebouwde domeinintentie toevoegen** op de werkbalk boven de lijst met intenties. 

1. Selecteer de intentie **Hulpprogramma's.Annuleren** in het pop-updialoogvenster. 

    > [!div class="mx-imgBorder"]
    > ![Vooraf gebouwde intentie toevoegen](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Selecteer de knop **Gereed.**

## <a name="add-a-prebuilt-entity"></a>Een vooraf gebouwde entiteit toevoegen

1. Open uw app door op de naam van **Mijn apps** te klikken en klik vervolgens op **Entiteiten** aan de linkerkant. 

1. Klik op de pagina **Entiteiten** op **Vooraf opgebouwde entiteit toevoegen**.

1. Selecteer in het dialoogvenster **Vooraf gebouwde entiteiten toevoegen** de vooraf gebouwde entiteit. 

    > [!div class="mx-imgBorder"]
    > ![Dialoogvenster Vooraf gebouwde entiteit toevoegen](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Selecteer **Done**. Nadat de entiteit is toegevoegd, hoeft u de app niet meer te trainen. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publiceren om vooraf gebouwd model weer te geven vanaf het voorspellingseindpunt

De eenvoudigste manier om de waarde van een vooraf gebouwd model weer te geven, is door vanaf het gepubliceerde eindpunt op te vragen. 

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entiteiten die een vooraf opgebouwd entiteitstoken bevatten
 
Als u een door de machine geleerde entiteit hebt die wordt beperkt door een vooraf gebouwde entiteit, voegt u een subcomponent toe aan de door de machine geleerde entiteit en voegt u een beperking van een vooraf gebouwde entiteit toe.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Model bouwen vanaf .csv met REST API's](./luis-tutorial-node-import-utterances-csv.md)
