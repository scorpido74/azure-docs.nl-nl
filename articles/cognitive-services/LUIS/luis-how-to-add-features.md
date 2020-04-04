---
title: Descriptoren - LUIS
titleSuffix: Azure Cognitive Services
description: Gebruik Language Understanding (LUIS) om app-functies toe te voegen die de detectie of voorspelling van intenties en entiteiten die categorieën en patronen kunnen verbeteren
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
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631453"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Beschrijvingen gebruiken om het signaal van de woordenlijst te stimuleren

U functies toevoegen aan uw LUIS-app om de nauwkeurigheid ervan te verbeteren. Functies helpen LUIS door hints te geven dat bepaalde woorden en zinnen deel uitmaken van een vocabulaire van een app-domein.

Een [beschrijving](luis-concept-feature.md) (woordlijst) bevat een groep waarden (woorden of zinnen) die tot dezelfde klasse behoren en op dezelfde manier moeten worden behandeld (bijvoorbeeld namen van steden of producten). Wat LUIS leert over een van hen wordt automatisch toegepast op de anderen ook. Deze lijst is niet hetzelfde als een [lijstentiteit](reference-entity-list.md) (exacte tekstkomt overeen) met overeenkomende woorden.

Een descriptor voegt aan de woordenschat van het appdomein als tweede signaal aan LUIS over die woorden toe.

Bekijk [functieconcepten](luis-concept-feature.md) om te begrijpen wanneer en waarom een beschrijving moet worden gebruikt.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Descriptor toevoegen

1. Open uw app door op de naam van **Mijn apps te** klikken en vervolgens op **Bouwen**te klikken en vervolgens op **Descriptoren** in het linkerdeelvenster van uw app te klikken.

1. Klik op de pagina **Descriptors** op **+ Descriptor toevoegen**.

1. Voer in het dialoogvenster **Descriptor van** de nieuwe `Cities` woordgroep maken een naam in, zoals voor de beschrijving. Typ **in** het vak Waarde de waarden van de `Seattle`beschrijvingen, zoals . U één waarde tegelijk typen of een set waarden die door komma's zijn gescheiden en vervolgens op **Enter drukken.**

    > [!div class="mx-imgBorder"]
    > ![Descriptorsteden toevoegen](./media/luis-add-features/add-phrase-list-cities.png)

    Zodra u voldoende waarden voor LUIS hebt ingevoerd, worden suggesties weergegeven. U **+ Alle** voorgestelde waarden toevoegen of afzonderlijke termen selecteren.

1. Houd **Deze waarden zijn uitwisselbaar** gecontroleerd als de toegevoegde descriptorwaarden alternatieven zijn die door elkaar kunnen worden gebruikt.

1. De woordenlijst kan van toepassing zijn op de hele app met de **globale** instelling of op een specifiek model (intentie of entiteit). Als u de woordgroeplijst maakt, als _beschrijving_ van een intentie of entiteit, is de wisselknop ingesteld op niet globaal. In dit geval is de betekenis van de schakelaar is dat de beschrijving is een functie om alleen dat model, dus _niet globaal_ aan de toepassing.

1. Selecteer **Done**. De nieuwe beschrijving wordt toegevoegd aan de pagina **Descriptors.**

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> U een descriptor verwijderen of deactiveren van de contextuele werkbalk op de pagina **Descriptors.**

## <a name="next-steps"></a>Volgende stappen

Nadat u een beschrijving hebt toegevoegd, bewerkt, verwijderd of gedeactiveerd, [traint en test u de app](luis-interactive-test.md) opnieuw om te zien of de prestaties verbeteren.
