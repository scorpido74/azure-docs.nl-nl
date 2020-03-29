---
title: Vertaling aanpassen - Vertaler Tekst API
titleSuffix: Azure Cognitive Services
description: Gebruik de Microsoft Translator Hub om uw eigen machinevertaalsysteem te bouwen met uw gewenste terminologie en stijl.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: be9c06d45da4d83c26f82343c9cb7b19ba19b4ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71257626"
---
# <a name="customize-your-text-translations"></a>Uw tekstvertalingen aanpassen

De Microsoft Custom Translator is een functie van de Microsoft Translator-service, waarmee gebruikers de geavanceerde neurale machinevertaling van Microsoft Translator kunnen aanpassen bij het vertalen van tekst met behulp van de Translator Text API (alleen versie 3).

De functie kan ook worden gebruikt om spraakvertaling aan te passen wanneer deze wordt gebruikt met [Cognitive Services Speech.](https://docs.microsoft.com/azure/cognitive-services/speech-service/)

## <a name="custom-translator"></a>Custom Translator

Met Custom Translator u neurale vertaalsystemen bouwen die inzicht hebben in de terminologie die wordt gebruikt in uw eigen bedrijf en branche. Het aangepaste vertaalsysteem wordt vervolgens geïntegreerd in bestaande toepassingen, workflows en websites.

### <a name="how-does-it-work"></a>Hoe werkt het?

Gebruik uw eerder vertaalde documenten (folders, webpagina's, documentatie, enz.) om een vertaalsysteem te bouwen dat uw domeinspecifieke terminologie en stijl weerspiegelt, beter dan een standaard vertaalsysteem. Gebruikers kunnen TMX-, XLIFF-, TXT-, DOCX- en XLSX-documenten uploaden.  

Het systeem accepteert ook gegevens die parallel lopen op documentniveau, maar nog niet zijn uitgelijnd op zinsniveau. Als gebruikers toegang hebben tot versies van dezelfde inhoud in meerdere talen, maar in afzonderlijke documenten kan Aangepaste Vertaler zinnen automatisch koppelen aan documenten.  Het systeem kan ook eentalige gegevens in een of beide talen gebruiken om de parallelle trainingsgegevens aan te vullen om de vertalingen te verbeteren.

Het aangepaste systeem is dan beschikbaar via een regelmatige oproep naar de Microsoft Translator Text API met behulp van de categorieparameter.

Gezien het juiste type en de hoeveelheid trainingsgegevens is het niet ongewoon om winsten tussen 5 en 10 te verwachten, of zelfs meer BLEU-punten op de vertaalkwaliteit met behulp van Custom Translator.

Meer details over de verschillende aanpassingsniveaus op basis van beschikbare gegevens vindt u in de [Gebruikershandleiding Voor aangepaste vertalers.](https://aka.ms/CustomTranslatorDocs)


## <a name="microsoft-translator-hub"></a>Microsoft Translator Hub

> [!NOTE]
> De verouderde Microsoft Translator Hub wordt op 17 mei 2019 met pensioen gegaan. [Bekijk belangrijke migratie-informatie en -datums](https://www.microsoft.com/translator/business/hub/).  

## <a name="custom-translator-versus-hub"></a>Aangepaste vertaler versus hub

|   | **Hub** | **Custom Translator**|
|:-----|:----:|:----:|
|Functiestatus aanpassen   | Algemene beschikbaarheid  | Algemene beschikbaarheid |
| Text API-versie  | V2 alleen   | V3 alleen |
| SMT-aanpassing | Ja   | Nee |
| NMT-aanpassing | Nee    | Ja |
| Nieuwe uniforme Spraakservices aanpassing | Nee    | Ja |
| [Geen spoor](https://www.aka.ms/notrace) | Ja  | Ja |

## <a name="collaborative-translations-framework"></a>Samenwerkingskader voor vertalingen

> [!NOTE]
> Vanaf 1 februari 2018 zijn AddTranslation() en AddTranslationArray() niet meer beschikbaar voor gebruik met de Translator Text API V2.0. Deze methoden zullen mislukken en niets zal worden geschreven. De Translator Text API V3.0 ondersteunt deze methoden niet.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een aangepast taalsysteem instellen met aangepaste vertaler](https://aka.ms/CustomTranslatorDocs)
