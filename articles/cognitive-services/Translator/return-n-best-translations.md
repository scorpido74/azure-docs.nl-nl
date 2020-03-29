---
title: Return N-Best Translations - Tekst vertaler
titleSuffix: Azure Cognitive Services
description: Retourneer N-Best vertalingen met behulp van de Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ROBOTS: NOINDEX
ms.openlocfilehash: eff25877165ac365e0af77651147fcdd1eebe294
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837244"
---
# <a name="how-to-return-n-best-translations"></a>N-Best vertalingen retourneren

> [!NOTE]
> Deze methode wordt afgeschaft. Het is niet beschikbaar in V3.0 van de Translator Text API.

De methoden GetTranslations() en GetTranslationsArray() van de Microsoft Translator API bevatten een optionele Booleaanse vlag "IncludeMultipleMTAlternatives".
De methode zal terugkeren naar maxTranslations alternatieven waar de delta wordt geleverd uit de N-Best lijst van de vertaler motor.

De handtekening is:

**Syntaxis**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, tekst, van, naar maxTranslations, opties); |

**Parameters**

| Parameter | Beschrijving |
|:---|:---|
| appId | **Vereist** Als de kopautorisatie wordt gebruikt, laat u het appid-veld leeg, anders geeft u een tekenreeks op met 'Drager' + + + toegangstoken.|
| tekst | **Vereist** Een tekenreeks die de tekst weergeeft om te vertalen. De grootte van de tekst mag niet groter zijn dan 10000 tekens.|
| from | **Vereist** Een tekenreeks die de taalcode van de te vertalen tekst weergeeft. |
| tot | **Vereist** Een tekenreeks die de taalcode weergeeft om de tekst naar te vertalen. |
| maxVertalingen | **Vereist** Een int die het maximum aantal vertalingen vertegenwoordigt om terug te keren. |
| opties | **Optioneel** Een object TranslateOptions dat de onderstaande waarden bevat. Ze zijn allemaal optioneel en standaard naar de meest voorkomende instellingen.

* Categorie: De enige ondersteunde optie en de standaardoptie is 'algemeen'.
* ContentType: De enige ondersteunde en de standaardoptie is 'tekst/vlakte'.
* Status: Gebruikersstatus om aanvragen en antwoorden te correleren. Dezelfde inhoud wordt geretourneerd in het antwoord.
* IncludeMultipleMTAlternatives: vlag om te bepalen of meer dan één alternatieven van de MT-engine moeten worden teruggekomen. Standaard is false en bevat slechts 1 alternatief.

## <a name="ratings"></a>Waarderingen
De beoordelingen worden als volgt toegepast: De beste automatische vertaling heeft een beoordeling van 5.
De automatisch gegenereerde (N-Best) vertaalalternatieven hebben een rating van 0 en hebben een matchgraad van 100.

## <a name="number-of-alternatives"></a>Aantal alternatieven
Het aantal geretourneerde alternatieven is maximaalVertalingen, maar kan minder zijn.

## <a name="language-pairs"></a>Taalparen
Deze functionaliteit is niet beschikbaar voor vertalingen tussen vereenvoudigd en traditioneel Chinees, beide richtingen. Het is beschikbaar voor alle andere door Microsoft Translator ondersteunde taalparen.
