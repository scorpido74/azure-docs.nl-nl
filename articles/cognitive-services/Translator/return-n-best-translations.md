---
title: N-beste vertalingen retour neren-Translator
titleSuffix: Azure Cognitive Services
description: N-beste vertalingen retour neren met behulp van het conversie programma.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ROBOTS: NOINDEX
ms.openlocfilehash: 1fe370070aa97befb418d27def32725bf0a148b3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592675"
---
# <a name="how-to-return-n-best-translations"></a>N-beste vertalingen retour neren

> [!NOTE]
> Deze methode is afgeschaft. Het is niet beschikbaar in V 3.0 van het conversie programma.

De methoden GetTranslations () en GetTranslationsArray () van de vertaler bevatten een optionele Booleaanse vlag "IncludeMultipleMTAlternatives".
De-methode retourneert maxTranslations alternatieven waarbij de Delta wordt verstrekt uit de N-beste lijst van de Vertaal engine.

De hand tekening is:

**Syntaxis**

| C# |
|:---|
| GetTranslationsResponse micro soft. Translator. GetTranslations (appId, Text, van, to, maxTranslations, Options); |

**Parameters**

| Parameter | Beschrijving |
|:---|:---|
| appId | **Vereist** Als de autorisatie-header wordt gebruikt, laat u het veld AppID leeg een teken reeks opgeven met ' Bearer ' + ' "+ toegangs token.|
| tekst | **Vereist** Een teken reeks voor de tekst die moet worden vertaald. De tekst mag niet langer zijn dan 10000 tekens.|
| from | **Vereist** Een teken reeks die de taal code vertegenwoordigt van de tekst die moet worden vertaald. |
| tot | **Vereist** Een teken reeks die de taal code vertegenwoordigt voor het vertalen van de tekst in. |
| maxTranslations | **Vereist** Een geheel getal dat het maximum aantal vertalingen vertegenwoordigt dat moet worden geretourneerd. |
| opties | **Optioneel** Een TranslateOptions-object met daarin de waarden die hieronder worden weer gegeven. Ze zijn allemaal optioneel en standaard ingesteld op de meest voorkomende instellingen.

* Categorie: de enige ondersteunde en standaard optie is "algemeen".
* Content type: de enige ondersteunde en standaard optie is ' text/plain '.
* Status: gebruikers status voor het correleren van aanvragen en antwoorden. Dezelfde inhoud wordt in het antwoord geretourneerd.
* IncludeMultipleMTAlternatives: vlag om te bepalen of er meer dan één alternatief moet worden geretourneerd van de MT-engine. De standaard waarde is False en bevat slechts één alternatief.

## <a name="ratings"></a>Waarderingen
De classificaties worden als volgt toegepast: de beste automatische vertaling heeft de classificatie 5.
De automatisch gegenereerde alternatieven voor vertaling (N-best) hebben een classificatie van 0 en hebben een afstemmings graad van 100.

## <a name="number-of-alternatives"></a>Aantal alternatieven
Het aantal geretourneerde alternatieven is Maxi maal maxTranslations, maar kan minder zijn.

## <a name="language-pairs"></a>Taal paren
Deze functionaliteit is niet beschikbaar voor vertalingen tussen vereenvoudigd en traditioneel Chinees, beide richtingen. Het is beschikbaar voor alle andere ondersteunde taal paren van micro soft Translator.
