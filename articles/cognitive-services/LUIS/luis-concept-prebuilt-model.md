---
title: Voorgebouwde modellen - LUIS
titleSuffix: Azure Cognitive Services
description: Vooraf gebouwde modellen bieden domeinen, intenties, uitingen en entiteiten. U uw app starten met een vooraf gebouwd domein of later een relevant domein aan uw app toevoegen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: e0b5b95a7524b60a7c3367035a15a7158fa7908a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280844"
---
# <a name="prebuilt-models"></a>Vooraf gemaakte modellen

Vooraf gebouwde modellen bieden domeinen, intenties, uitingen en entiteiten. U uw app starten met een vooraf gebouwd model of later een relevant model toevoegen aan uw app. 

## <a name="types-of-prebuilt-models"></a>Typen voorgebouwde modellen

LUIS biedt drie soorten voorgebouwde modellen. Elk model kan op elk gewenst moment aan uw app worden toegevoegd. 

|Modeltype|Omvat|
|--|--|
|[Domain](luis-reference-prebuilt-domains.md)|Intents, uitingen, entiteiten|
|Intents|Intents, uitingen|
|[Entiteiten](luis-reference-prebuilt-entities.md)|Alleen entiteiten| 

## <a name="prebuilt-domains"></a>Vooraf gemaakte domeinen

Language Understanding (LUIS) biedt *vooraf gebouwde domeinen,* die vooraf getrainde modellen van [intents](luis-how-to-add-intents.md) en [entiteiten](luis-concept-entity-types.md) zijn die samenwerken voor domeinen of algemene categorieën clienttoepassingen. 

De vooraf gebouwde domeinen zijn getraind en klaar om toe te voegen aan uw LUIS-app. De intenties en entiteiten van een vooraf gebouwd domein zijn volledig aanpasbaar zodra u ze aan uw app hebt toegevoegd. 

> [!TIP]
> De intenties en entiteiten in een vooraf gebouwd domein werken het beste samen. Het is beter om intents en entiteiten uit hetzelfde domein te combineren wanneer dat mogelijk is.
> Het vooraf gebouwde domein Utilities heeft intenties die u aanpassen voor gebruik in elk domein. U bijvoorbeeld `Utilities.Repeat` toevoegen aan uw app en deze trainen herkennen welke acties de gebruiker mogelijk wil herhalen in uw toepassing. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Het gedrag van een vooraf gebouwde domeinintentie wijzigen

Het is mogelijk dat een vooraf gebouwd domein een intentie bevat die vergelijkbaar is met een intentie die u in uw LUIS-app wilt hebben, maar u wilt dat het zich anders gedraagt. Het vooraf gebouwde domein **Plaatsen** `MakeReservation` biedt bijvoorbeeld de intentie om een restaurantreservering te maken, maar u wilt dat uw app die intentie gebruikt om hotelreserveringen te maken. In dat geval u het gedrag van die intentie wijzigen door voorbeelduitingen toe te voegen aan de intentie om hotelreserveringen te maken en de app vervolgens om te scholen. 

U vindt een volledige lijst van de vooraf gebouwde domeinen in de [verwijzing prebuilt domeinen.](./luis-reference-prebuilt-domains.md)

## <a name="prebuilt-intents"></a>Vooraf gebouwde intenties

LUIS biedt vooraf gebouwde intenties en hun uitingen voor elk van de vooraf gebouwde domeinen. Intents kunnen worden toegevoegd zonder het hele domein toe te voegen. Het toevoegen van een intentie is het proces van het toevoegen van een intentie en de uitingen ervan aan uw app. Zowel de intentienaam als de utterancelijst kunnen worden gewijzigd.  

## <a name="prebuilt-entities"></a>Vooraf gemaakte entiteiten

LUIS bevat een set vooraf gebouwde entiteiten voor het herkennen van veelvoorkomende soorten informatie, zoals datums, tijden, getallen, metingen en valuta. De ondersteuning van vooraf gebouwde entiteiten verschilt per cultuur van uw LUIS-app. Zie de [vooraf gebouwde entiteitsverwijzing](./luis-reference-prebuilt-entities.md)voor een volledige lijst met de vooraf gebouwde entiteiten die LUIS ondersteunt, inclusief ondersteuning door cultuur.

Wanneer een vooraf gebouwde entiteit in uw toepassing is opgenomen, worden de voorspellingen ervan opgenomen in uw gepubliceerde toepassing. Het gedrag van vooraf gebouwde entiteiten is vooraf opgeleid en **kan niet** worden gewijzigd. 

> [!NOTE]
> **builtin.datetime** is afgeschaft. Het wordt vervangen door [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), die erkenning van datum en tijd varieert biedt, evenals verbeterde erkenning van dubbelzinnige data en tijden.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [toevoegen van vooraf gebouwde entiteiten](luis-prebuilt-entities.md) aan uw app.
