---
title: Descriptoren-LUIS
titleSuffix: Azure Cognitive Services
description: Gebruik Language Understanding (LUIS) om app-functies toe te voegen die de detectie of voor spelling van intenties en entiteiten die categorieën en patronen kunnen verbeteren
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: diberry
ms.openlocfilehash: 7560fdcbfc77ea2655e8af641794478ead4c11c7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80631453"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Descriptors gebruiken om het signaal van een woorden lijst te verhogen

U kunt functies toevoegen aan uw LUIS-app om de nauw keurigheid te verbeteren. Met functies kunt u LUIS dat bepaalde woorden en zinsdelen deel uitmaken van een app-domein woordenlijst.

Een [descriptor](luis-concept-feature.md) (woordgroepen lijst) bevat een groep waarden (woorden of zinsdelen) die deel uitmaken van dezelfde klasse en moeten op dezelfde manier worden behandeld (bijvoorbeeld namen van steden of producten). Wat LUIS informatie bevat over een van deze wordt automatisch toegepast op de andere. Deze lijst is niet hetzelfde als een [lijst entiteit](reference-entity-list.md) (exacte tekst overeenkomsten) van overeenkomende woorden.

Een descriptor voegt de woorden lijst van het app-domein als een tweede signaal toe aan LUIS over die woorden.

Bekijk de [functie concepten](luis-concept-feature.md) om te begrijpen wanneer en waarom een descriptor moet worden gebruikt.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Descriptor toevoegen

1. Open uw app door te klikken op de naam op de pagina **mijn apps** . Klik vervolgens op **bouwen**en vervolgens op **descriptors** in het linkerdeel venster van de app.

1. Klik op **de pagina** **descriptors op + beschrijving toevoegen**.

1. Voer in het dialoog venster **nieuwe woordgroepen lijst-descriptor maken** een naam in `Cities` , bijvoorbeeld voor de descriptor. Typ in het vak **waarde** de waarden van de descriptors, zoals `Seattle`. U kunt één waarde per keer typen, of een reeks waarden gescheiden door komma's, en vervolgens op **Enter**drukken.

    > [!div class="mx-imgBorder"]
    > ![Descriptor steden toevoegen](./media/luis-add-features/add-phrase-list-cities.png)

    Zodra u voldoende waarden hebt ingevoerd voor LUIS, worden er suggesties weer gegeven. U kunt **+ alle** voorgestelde waarden toevoegen of afzonderlijke voor waarden selecteren.

1. Behoud **deze waarden zijn verwisselbaar** ingeschakeld als de toegevoegde descriptor waarden alternatieven zijn die door elkaar kunnen worden gebruikt.

1. De lijst met zinsdelen kan worden toegepast op de volledige app met de **algemene** instelling of op een specifiek model (intentie of entiteit). Als u de woordgroepen lijst maakt als een _descriptor_ van een intentie of entiteit, wordt de wissel knop ingesteld op niet globaal. In dit geval is de betekenis van de wissel knop dat de descriptor een functie is die alleen voor dat model geldt, dus _niet globaal_ voor de toepassing.

1. Selecteer **Done**. De nieuwe beschrijving wordt toegevoegd aan de **pagina** descriptors.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> U kunt een descriptor verwijderen of deactiveren via de contextuele werk balk op de pagina **descriptors** .

## <a name="next-steps"></a>Volgende stappen

Na het toevoegen, bewerken, verwijderen of deactiveren van een descriptor, [Train en test u de app](luis-interactive-test.md) opnieuw om te zien of de prestaties verbeteren.
