---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 8aef4a5eeeec27bfb2ca3edf9ff6823b88431319
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591829"
---
Met de entiteit pattern.any kunt u vrije gegevens vinden waarbij het lastig is om uit de tekst van de entiteit het einde van de entiteit te bepalen op basis van de rest van de utterance.

Met een Human resources-app kunnen werk nemers bedrijfs formulieren vinden.

|Utterance|
|--|
|Waar is **HRF-123456**?|
|Wie is de auteur van **HRF 123234**?|
|Is **HRF-456098** gepubliceerd in het Frans?|

Elk formulier heeft echter zowel een opgemaakte naam, gebruikt in de voorgaande tabel, als een beschrijvende naam, zoals `Request relocation from employee new to the company 2018 version 5`.

Utterances met de beschrijvende naam van het formulier zien er als volgt uit:

|Utterance|
|--|
|Waar is de **Aanvraag voor relocatie van nieuwe medewerker in het bedrijf 2018 versie 5**?|
|Wie heeft **Aanvraag voor relocatie van nieuwe medewerker in het bedrijf 2018 versie 5** geschreven?|
|Is **Aanvraag voor relocatie van nieuwe medewerker in het bedrijf 2018 versie 5** gepubliceerd in het Frans?|

De lengte varieert en er zijn woorden die verwarrend kunnen zijn voor LUIS om te bepalen waar de entiteit eindigt. Met behulp van een entiteit Pattern.any in een patroon, kunt u het begin en einde van de naam van het formulier opgeven, zodat de naam van het formulier correct worden geëxtraheerd door LUIS.

|Voorbeeld van sjabloon-utterance|
|--|
|Waar is {FormName}[?]|
|Wie heeft {FormName} geschreven[?]|
|Is {FormName} gepubliceerd in het Frans[?]|

Beoordelings patroon. alle [referentie](../reference-entity-pattern-any.md) gegevens