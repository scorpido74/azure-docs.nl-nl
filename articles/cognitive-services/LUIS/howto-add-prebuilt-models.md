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
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: abf149d621d283744a71e08ea4ac2199a1994d57
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680952"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Vooraf samengestelde modellen voor veelvoorkomende gebruiks scenario's toevoegen

LUIS bevat een reeks vooraf gemaakte modellen voor het snel toevoegen van veelvoorkomende, gespreks gebruikers scenario's. Dit is een snelle en eenvoudige manier om mogelijkheden toe te voegen aan uw gesprek-client toepassing zonder dat u de modellen voor deze mogelijkheden hoeft te ontwerpen.

## <a name="add-a-prebuilt-domain"></a>Een vooraf gedefinieerd domein toevoegen

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai)en selecteer uw **abonnement** en de resource voor het **ontwerpen** van de apps die zijn toegewezen aan die ontwerp bron.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.

1. Selecteer vooraf **ontwikkelde domeinen** op de werk balk links.

1. Zoek het domein dat u wilt toevoegen aan de app en selecteer vervolgens de knop **domein toevoegen** .

    > [!div class="mx-imgBorder"]
    > ![Kalender vooraf gebouwd domein toevoegen](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Een vooraf ontwikkelde intentie toevoegen

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai)en selecteer uw **abonnement** en de resource voor het **ontwerpen** van de apps die zijn toegewezen aan die ontwerp bron.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.

1. Selecteer op de pagina **intenties** vooraf **gemaakte domein intentie toevoegen** in de werk balk boven de lijst met doel stellingen.

1. Selecteer een opzet in het pop-upvenster.

    > [!div class="mx-imgBorder"]
    > ![Vooraf gebouwde intentie toevoegen](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Selecteer de knop **gereed** .

## <a name="add-a-prebuilt-entity"></a>Een vooraf samengestelde entiteit toevoegen
1. Meld u aan bij de [Luis-Portal](https://www.luis.ai)en selecteer uw **abonnement** en de resource voor het **ontwerpen** van de apps die zijn toegewezen aan die ontwerp bron.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.
1. Selecteer **entiteiten** aan de linkerkant.

1. Selecteer op de pagina **entiteiten** vooraf **samengestelde entiteit toevoegen**.

1. Selecteer in het dialoog venster **vooraf gemaakte entiteiten toevoegen** de vooraf samengestelde entiteit.

    > [!div class="mx-imgBorder"]
    > ![Het dialoog venster vooraf samengestelde entiteit toevoegen](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Selecteer **Done**. Nadat de entiteit is toegevoegd, hoeft u de app niet te trainen.

## <a name="add-a-prebuilt-domain-entity"></a>Een vooraf gedefinieerde domein entiteit toevoegen
1. Meld u aan bij de [Luis-Portal](https://www.luis.ai)en selecteer uw **abonnement** en de resource voor het **ontwerpen** van de apps die zijn toegewezen aan die ontwerp bron.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.
1. Selecteer **entiteiten** aan de linkerkant.

1. Selecteer op de pagina **entiteiten** vooraf **ingebouwde domein entiteit toevoegen**.

1. Selecteer in het dialoog venster **vooraf gedefinieerde domein modellen toevoegen** de vooraf ontwikkelde domein entiteit.

1. Selecteer **Done**. Nadat de entiteit is toegevoegd, hoeft u de app niet te trainen.

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publiceren om het vooraf ontwikkelde model te bekijken vanuit het Voorspellings eindpunt

De eenvoudigste manier om de waarde van een vooraf gebouwd model weer te geven, is door een query uit te zoeken vanuit het gepubliceerde eind punt.

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entiteiten met een vooraf gebouwd entiteits token

Als u een machine learning-entiteit hebt waarvoor een vereiste functie van een vooraf samengestelde entiteit nodig is, voegt u een subentiteit toe aan de machine learning-entiteit en voegt u vervolgens een _vereiste_ functie van een vooraf gedefinieerde entiteit toe.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Model bouwen vanuit. CSV met REST Api's](./luis-tutorial-node-import-utterances-csv.md)
