---
title: Vooraf gebouwde modellen voor Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS bevat een reeks vooraf gemaakte modellen voor het snel toevoegen van veelvoorkomende, gespreks gebruikers scenario's.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74013583"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Vooraf samengestelde modellen voor veelvoorkomende gebruiks scenario's toevoegen 

LUIS bevat een reeks vooraf gemaakte modellen voor het snel toevoegen van veelvoorkomende, gespreks gebruikers scenario's. Dit is een snelle en eenvoudige manier om mogelijkheden toe te voegen aan uw gesprek-client toepassing zonder dat u de modellen voor deze mogelijkheden hoeft te ontwerpen. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>Een vooraf gedefinieerd domein toevoegen

1. Selecteer uw app op de pagina **mijn apps** . Hiermee opent u uw app in de sectie **Build** van de app. 

1. Selecteer vooraf **ontwikkelde domeinen** op de werk balk links. 

1. Zoek het domein dat u wilt toevoegen aan de app en selecteer vervolgens de knop **domein toevoegen** .

    > [!div class="mx-imgBorder"]
    > ![Kalender vooraf gebouwd domein toevoegen](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Een vooraf ontwikkelde intentie toevoegen

1. Selecteer uw app op de pagina **mijn apps** . Hiermee opent u uw app in de sectie **Build** van de app. 

1. Selecteer op de pagina **intenties** vooraf **gemaakte domein intentie toevoegen** in de werk balk boven de lijst met doel stellingen. 

1. Selecteer de optie **Utilities. annuleren** in het pop-updialoogvenster. 

    > [!div class="mx-imgBorder"]
    > ![Vooraf gebouwde intentie toevoegen](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Selecteer de knop **gereed** .

## <a name="add-a-prebuilt-entity"></a>Een vooraf samengestelde entiteit toevoegen

1. Open uw app door te klikken op de naam op de pagina **mijn apps** en klik vervolgens op **entiteiten** aan de linkerkant. 

1. Klik op de pagina **entiteiten** op **vooraf samengestelde entiteit toevoegen**.

1. Selecteer in het dialoog venster **vooraf gemaakte entiteiten toevoegen** de vooraf samengestelde entiteit. 

    > [!div class="mx-imgBorder"]
    > ![Het dialoog venster vooraf samengestelde entiteit toevoegen](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Selecteer **Done**. Nadat de entiteit is toegevoegd, hoeft u de app niet te trainen. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publiceren om het vooraf ontwikkelde model te bekijken vanuit het Voorspellings eindpunt

De eenvoudigste manier om de waarde van een vooraf gebouwd model weer te geven, is door een query uit te zoeken vanuit het gepubliceerde eind punt. 

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entiteiten met een vooraf gebouwd entiteits token
 
Als u een door de machine geleerde entiteit hebt die wordt beperkt door een vooraf samengestelde entiteit, voegt u een subonderdeel toe aan de door de machine geleerde entiteit en voegt u vervolgens een beperking van een vooraf gedefinieerde entiteit toe.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Model bouwen vanuit. CSV met REST Api's](./luis-tutorial-node-import-utterances-csv.md)
