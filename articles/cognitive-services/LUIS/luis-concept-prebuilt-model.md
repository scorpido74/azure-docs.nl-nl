---
title: Preconstrueerde modellen-LUIS
titleSuffix: Azure Cognitive Services
description: Preconstrueerde modellen bieden domeinen, intenties, uitingen en entiteiten. U kunt uw app starten met een vooraf gebouwd domein of later een relevant domein aan uw app toevoegen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b88801ded3dea7c7514ff117361feba3e95444ed
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264384"
---
# <a name="prebuilt-domain-intent-and-entity-models"></a>Vooraf gebouwde domein-, intentie-en entiteits modellen

Preconstrueerde modellen bieden domeinen, intenties, uitingen en entiteiten. U kunt uw app starten met een vooraf gebouwd domein of later een relevant domein aan uw app toevoegen. 

## <a name="types-of-prebuilt-models"></a>Typen vooraf gebouwde modellen

Er zijn drie soorten vooraf gebouwde modellen die LUIS biedt. Elk model kan op elk gewenst moment aan uw app worden toegevoegd. 

|Model type|Bevat|
|--|--|
|Domain|Intenties, uitingen, entiteiten|
|Intents|Intenties, uitingen|
|Entiteiten|Alleen entiteiten| 

## <a name="prebuilt-domains"></a>Vooraf gemaakte domeinen

Language Understanding (LUIS) biedt *vooraf ontwikkelde domeinen*, die bestaan uit vooraf ontwikkelde sets van [intenties](luis-how-to-add-intents.md) en [entiteiten](luis-concept-entity-types.md) die samen werken voor domeinen of algemene categorieën van client toepassingen. 

De vooraf gemaakte domeinen zijn getraind en klaar om toe te voegen aan uw LUIS-app. De intenties en entiteiten in een vooraf gebouwd domein kunnen volledig worden aangepast nadat u ze aan uw app hebt toegevoegd. 

Als u begint met het aanpassen van een volledig vooraf gebouwd domein, verwijdert u de intenties en entiteiten die uw app niet hoeft te gebruiken. U kunt ook sommige intents of entiteiten toevoegen aan de set die het vooraf ontwikkelde domein al biedt. Als u bijvoorbeeld het vooraf gebouwde **gebeurtenissen** gebruikt voor een sport gebeurtenis-app, kunt u entiteiten voor sport teams toevoegen. Wanneer u begint met het [leveren van uitingen](luis-how-to-add-example-utterances.md) aan Luis, moet u de termen die specifiek zijn voor uw app bevatten. LUIS leert hoe deze en staart de intenties en entiteiten van het vooraf ontwikkelde domein kunnen herkennen aan de behoeften van uw app. 

> [!TIP]
> De intenties en entiteiten in een vooraf gebouwd domein werken het beste samen. Het is beter om de intenties en entiteiten uit hetzelfde domein, indien mogelijk, te combi neren.
> Het vooraf ontwikkelde hulp programma voor Hulpprogram Ma's heeft intenties die u kunt aanpassen voor gebruik in elk domein. U kunt bijvoorbeeld `Utilities.Repeat` toevoegen aan uw app en traint dat alle acties die door de gebruiker in uw toepassing mogelijk moeten worden herhaald. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Het gedrag van een vooraf gebouwd domein opzet wijzigen

Het is mogelijk dat een vooraf gebouwd domein een intentie bevat die vergelijkbaar is met een intentie die u in uw LUIS-app wilt hebben, maar u wilt dat deze anders werkt. Het voor bereide domein bevat bijvoorbeeld een `MakeReservation` intentie **voor het maken** van een restaurant, maar u wilt dat uw app die intentie gebruikt om hotel reserveringen te maken. In dat geval kunt u het gedrag van die intentie aanpassen door uitingen te bieden aan LUIS over het maken van Hotel Reserveringen en labels te geven met behulp van het `MakeReservation`-intentie, zodat LUIS opnieuw kan worden getraind om de `MakeReservation` intentie te herkennen aan een aanvraag om een hotel te boeken.

U vindt een volledige lijst met vooraf gemaakte domeinen in de [Naslag Gids met vooraf gedefinieerde domeinen](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Preconstrueerde intenties

LUIS biedt vooraf ontwikkelde intenten en hun uitingen. Intenties kunnen worden toegevoegd zonder het hele domein toe te voegen. Het toevoegen van een intentie is het proces van het toevoegen van een intentie en de uitingen. Zowel de naam van de doel groep als de utterance-lijst kan worden gewijzigd.  

## <a name="prebuilt-entities"></a>Vooraf gemaakte entiteiten

LUIS bevat een reeks vooraf gemaakte entiteiten voor het herkennen van veelvoorkomende typen informatie, zoals datums, tijden, getallen, metingen en valuta. Vooraf ontwikkelde entiteits ondersteuning is afhankelijk van de cultuur van uw LUIS-app. Zie de [vooraf samengestelde entiteits referentie](./luis-reference-prebuilt-entities.md)voor een volledige lijst van de vooraf gemaakte entiteiten die door Luis worden ondersteund, inclusief ondersteuning door de cultuur.

Wanneer een vooraf samengestelde entiteit is opgenomen in uw toepassing, zijn de voor spellingen opgenomen in de gepubliceerde toepassing. Het gedrag van vooraf gemaakte entiteiten is vooropgeleid en **kan niet** worden gewijzigd. 

> [!NOTE]
> **Builtin. datetime** is afgeschaft. Het wordt vervangen door [**Builtin. datetimeV2**](luis-reference-prebuilt-datetimev2.md), waarmee u de datum-en tijds bereik herkenning kunt herkennen, evenals een verbeterde herkenning van onduidelijke datum en tijd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [toevoegen van vooraf gemaakte entiteiten](luis-prebuilt-entities.md) aan uw app.
