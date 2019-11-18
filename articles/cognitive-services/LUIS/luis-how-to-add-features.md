---
title: Descriptoren-LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) gebruiken om toe te voegen van app-functies ter verbetering van de detectie of de voorspelling van intenties en entiteiten die categorieën en -patronen
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: diberry
ms.openlocfilehash: 2b5046bb61dcafbba0b0540935e08777fbd747a5
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123144"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Descriptors gebruiken om het signaal van een woorden lijst te verhogen

U kunt functies toevoegen aan uw LUIS-app voor het verbeteren van de nauwkeurigheid. Functies LUIS u helpen met hints die bepaalde woorden en woordgroepen maken deel uit van een app-domein vocabulaire. 

Een [descriptor](luis-concept-feature.md) (woordgroepen lijst) bevat een groep waarden (woorden of zinsdelen) die deel uitmaken van dezelfde klasse en moeten op dezelfde manier worden behandeld (bijvoorbeeld namen van steden of producten). LUIS leert over een van beide wordt automatisch toegepast op de andere. Deze lijst is niet hetzelfde als een [lijst entiteit](reference-entity-list.md) (exacte tekst overeenkomsten) van overeenkomende woorden.

Een descriptor voegt de woorden lijst van het app-domein als een tweede signaal toe aan LUIS over die woorden.

Bekijk de [functie concepten](luis-concept-feature.md) om te begrijpen wanneer en waarom een descriptor moet worden gebruikt. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Descriptor toevoegen

1. Open uw app door te klikken op de naam op de pagina **mijn apps** . Klik vervolgens op **bouwen**en vervolgens op **descriptors** in het linkerdeel venster van de app. 

1. Klik op **de pagina** **descriptors op + beschrijving toevoegen**. 
 
1. Voer in het dialoog venster **nieuwe woordgroepen lijst-descriptor maken** een naam in, zoals `Cities` voor de descriptor. Typ in het vak **waarde** de waarden van de descriptors, zoals `Seattle`. Typ een waarde op een tijdstip of een set met door komma's gescheiden waarden en druk vervolgens op **Enter**.

    > [!div class="mx-imgBorder"]
    > ![descriptor steden toevoegen](./media/luis-add-features/add-phrase-list-cities.png)

    Zodra u voldoende waarden hebt ingevoerd voor LUIS, worden er suggesties weer gegeven. U kunt **+ alle** voorgestelde waarden toevoegen of afzonderlijke voor waarden selecteren.

1. Behoud **deze waarden zijn verwisselbaar** ingeschakeld als de toegevoegde descriptor waarden alternatieven zijn die door elkaar kunnen worden gebruikt.

1. Selecteer **Done**. De nieuwe beschrijving wordt toegevoegd aan de **pagina** descriptors.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> U kunt een descriptor verwijderen of deactiveren via de contextuele werk balk op de pagina **descriptors** .

## <a name="next-steps"></a>Volgende stappen

Na het toevoegen, bewerken, verwijderen of deactiveren van een descriptor, [Train en test u de app](luis-interactive-test.md) opnieuw om te zien of de prestaties verbeteren.
