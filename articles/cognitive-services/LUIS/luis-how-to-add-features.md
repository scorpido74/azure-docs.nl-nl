---
title: Woordgroepen lijsten-LUIS
titleSuffix: Azure Cognitive Services
description: Gebruik Language Understanding (LUIS) om app-functies toe te voegen die de detectie of voor spelling van intenties en entiteiten die categorieën en patronen kunnen verbeteren
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 0e3e4226eaaa0505eea96d8b3aca820f2327349e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467611"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Woordgroepen lijsten gebruiken om het signaal van een woorden lijst te verhogen

U kunt functies toevoegen aan uw LUIS-app om de nauw keurigheid te verbeteren. Met functies kunt u LUIS dat bepaalde woorden en zinsdelen deel uitmaken van een app-domein woordenlijst. 

Een [woordgroepen lijst](luis-concept-feature.md) bevat een groep waarden (woorden of zinsdelen) die deel uitmaken van dezelfde klasse en moeten op dezelfde manier worden behandeld (bijvoorbeeld namen van steden of producten). Wat LUIS informatie bevat over een van deze wordt automatisch toegepast op de andere. Deze lijst is niet hetzelfde als een [lijst entiteit](reference-entity-list.md) (exacte tekst overeenkomsten) van overeenkomende woorden.

Een woordgroepen lijst wordt toegevoegd aan de vocabulaire van het app-domein als een tweede signaal voor LUIS over die woorden.

Bekijk de [functie concepten](luis-concept-feature.md) om te begrijpen wanneer en waarom een woordgroepen lijst moet worden gebruikt. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-phrase-list"></a>Woordgroepen lijst toevoegen

LUIS biedt Maxi maal tien woordgroepen lijsten per app. 

1. Open uw app door te klikken op de naam op de pagina **mijn apps** . Klik vervolgens op **bouwen**en vervolgens op **woordgroepen lijsten** in het linkerdeel venster van uw app. 

1. Klik op de pagina **woordgroepen lijsten** op **nieuwe woordgroepen lijst maken**. 
 
1. In het dialoog venster **woordgroepen lijst toevoegen** typt u `Cities` als de naam van de lijst met zinsdelen. Typ in het vak **waarde** de waarden van de lijst met zinsdelen. U kunt één waarde per keer typen, of een reeks waarden gescheiden door komma's, en vervolgens op **Enter**drukken.

    ![Woordgroepen lijst plaatsen toevoegen](./media/luis-add-features/add-phrase-list-cities.png)

1. LUIS kunnen gerelateerde waarden Voorst Ellen die aan uw woordgroepen lijst moeten worden toegevoegd. Klik op **Aanbevolen** om een groep voorgestelde waarden te verkrijgen die semantisch gerelateerd zijn aan de toegevoegde waarde (n). U kunt op een van de voorgestelde waarden klikken of op **Alles toevoegen** klikken om ze allemaal toe te voegen.

    ![Voorgestelde waarden van de woordgroepen lijst-alles toevoegen](./media/luis-add-features/related-values.png)

1. Klik op **deze waarden zijn verwisselbaar** als de toegevoegde waarden van de woordgroepen lijst alternatieven zijn die door elkaar kunnen worden gebruikt.

    ![Woordgroepen lijst voorgestelde waarden: Selecteer een verwisselbaar vak](./media/luis-add-features/interchangeable.png)

1. Klik op **Gereed**. De lijst "steden" wordt toegevoegd aan de pagina **woordgroepen lijsten** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> U kunt een woordgroepen lijst verwijderen of deactiveren van de contextuele werk balk op de pagina **woordgroepen lijsten** .

## <a name="next-steps"></a>Volgende stappen

Na het toevoegen, bewerken, verwijderen of deactiveren van een woordgroepen lijst, [Train en test u de app](luis-interactive-test.md) opnieuw om te zien of de prestaties verbeteren.
