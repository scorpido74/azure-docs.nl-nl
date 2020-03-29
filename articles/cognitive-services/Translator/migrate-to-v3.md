---
title: Migreren naar V3 - Api voor vertalertekst
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u de stappen waarmee u migreren van V2 naar V3 van de Azure Cognitive Services Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: eb43d549d3e0cd449c865d533fc8701c4c3912fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837314"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>Translator Text API V2-naar-V3-migratie

> [!NOTE]
> V2 werd afgeschaft op 30 april 2018. Migreer uw applicaties naar V3 om te profiteren van nieuwe functionaliteit die exclusief beschikbaar is in V3.
> 
> De Microsoft Translator Hub wordt op 17 mei 2019 met pensioen gegaan. [Bekijk belangrijke migratie-informatie en -datums](https://www.microsoft.com/translator/business/hub/).  

Het Microsoft Translator-team heeft versie 3 (V3) van de Translator Text API uitgebracht. Deze release bevat nieuwe functies, afgeschafte methoden en een nieuwe indeling voor het verzenden naar en ontvangen van gegevens van de Microsoft Translator Service. Dit document bevat informatie voor het wijzigen van toepassingen om V3 te gebruiken. 

Het einde van dit document bevat handige koppelingen voor meer informatie.

## <a name="summary-of-features"></a>Overzicht van functies

* Geen spoor - In V3 is No-Trace van toepassing op alle prijsniveaus in de Azure-portal. Deze functie betekent dat er geen tekst die is ingediend bij de V3 API, wordt opgeslagen door Microsoft.
* JSON - XML wordt vervangen door JSON. Alle gegevens die naar de service worden verzonden en van de service zijn ontvangen, zijn in JSON-formaat.
* Meerdere doeltalen in één aanvraag - De methode Vertalen accepteert meerdere 'to'-talen voor vertaling in één aanvraag. Een enkele aanvraag kan bijvoorbeeld 'van' Engels en 'naar' Duits, Spaans en Japans zijn, of een andere groep talen.
* Tweetalig woordenboek - Er is een tweetalige woordenboekmethode toegevoegd aan de API. Deze methode omvat 'opzoeken' en 'voorbeelden'.
* Transliterate - Een transliteraat methode is toegevoegd aan de API. Met deze methode worden woorden en zinnen omgezet in één script (bijv. Arabisch) in een ander schrift (bijv. Latijn).
* Talen - Een nieuwe 'talen'-methode levert taalinformatie, in JSON-formaat, voor gebruik met de methoden 'vertalen', 'woordenboek' en 'transliterate'.
* Nieuw bij Vertalen - Nieuwe mogelijkheden zijn toegevoegd aan de 'translate'-methode om een aantal functies die zich in de V2-API bevonden als afzonderlijke methoden te ondersteunen. Een voorbeeld is TranslateArray.
* Spreekmethode - Tekst naar spraak-functionaliteit wordt niet langer ondersteund in de Microsoft Translator API. De functionaliteit voor tekst naar spraak is beschikbaar in [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech).

De volgende lijst met V2- en V3-methoden identificeert de V3-methoden en API's die de functionaliteit bieden die bij V2 is bijgekomen.

| V2-API-methode   | V3-API-compatibiliteit |
|:----------- |:-------------|
| `Translate`     | [Vertalen](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Vertalen](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Talen](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Talen](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Microsoft-spraakservice](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)         |
| `Speak`     | [Microsoft-spraakservice](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)          |
| `Detect`     | [Detecteren](reference/v3-0-detect.md)         |
| `DetectArray`     | [Detecteren](reference/v3-0-detect.md)         |
| `AddTranslation`     | Functie wordt niet meer ondersteund       |
| `AddTranslationArray`    | Functie wordt niet meer ondersteund          |
| `BreakSentences`      | [BreakZin](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | Functie wordt niet meer ondersteund         |
| `GetTranslationsArray`      | Functie wordt niet meer ondersteund         |

## <a name="move-to-json-format"></a>Naar JSON-indeling gaan

Microsoft Translator Text Translation V2 heeft gegevens geaccepteerd en geretourneerd in XML-indeling. In V3 zijn alle verzonden en ontvangen gegevens via de API in JSON-indeling. XML wordt niet langer geaccepteerd of geretourneerd in V3.

Deze wijziging heeft gevolgen voor verschillende aspecten van een toepassing die is geschreven voor de V2 Text Translation API. Als voorbeeld: de API Talen retourneert taalgegevens voor tekstvertaling, transliteratie en de twee woordenboekmethoden. U alle taalinformatie voor alle methoden in één gesprek opvragen of individueel aanvragen.

De methode voor talen vereist geen verificatie; door op de volgende link te klikken, zie je alle taalinformatie voor V3 in JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,woordenboek, transliteratie](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Verificatiesleutel

De verificatiesleutel die u voor V2 gebruikt, wordt geaccepteerd voor V3. U hoeft geen nieuw abonnement te krijgen. U V2 en V3 in uw apps mengen tijdens de migratieperiode van een jaar, waardoor u gemakkelijker nieuwe versies uitbrengen terwijl u nog steeds migreert van V2-XML naar V3-JSON.

## <a name="pricing-model"></a>Prijsmodel

Microsoft Translator V3 is geprijsd op dezelfde manier V2 werd geprijsd; per teken, inclusief spaties. De nieuwe functies in V3 brengen enkele wijzigingen aan in welke tekens worden geteld voor facturering.

| V3-methode   | Tekens die zijn geteld voor facturering |
|:----------- |:-------------|
| `Languages`     | Geen tekens ingediend, geen geteld, geen kosten.          |
| `Translate`     | Count is gebaseerd op het aantal tekens dat voor vertaling wordt ingediend en in hoeveel talen de tekens worden vertaald. 50 tekens ingediend, en 5 gevraagde talen zal 50x5.           |
| `Transliterate`     | Het aantal tekens dat voor transliteratie is ingediend, wordt geteld.         |
| `Dictionary lookup & example`     | Het aantal tekens dat is ingediend voor het opzoeken van woordenlijst en voorbeelden worden geteld.         |
| `BreakSentence`     | Geen kosten.       |
| `Detect`     | Geen kosten.      |

## <a name="v3-end-points"></a>V3-eindpunten

Wereldwijd

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>Methoden voor het vertalen van V3-API-tekst

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Compatibiliteit en aanpassing

> [!NOTE]
> 
> De Microsoft Translator Hub wordt op 17 mei 2019 met pensioen gegaan. [Bekijk belangrijke migratie-informatie en -datums](https://www.microsoft.com/translator/business/hub/).   

Microsoft Translator V3 maakt standaard gebruik van neurale machinevertaling. Als zodanig kan het niet worden gebruikt met de Microsoft Translator Hub. De Translator Hub ondersteunt alleen legacy statistische machine vertaling. Aanpassing voor neurale vertaling is nu beschikbaar met behulp van de aangepaste vertaler. [Meer informatie over het aanpassen van neurale machinevertaling](custom-translator/overview.md)

Neurale vertaling met de V3-tekst-API ondersteunt geen standaardcategorieën (SMT, spraak, tech, generalnn).

| |Eindpunt|    Naleving GDPR-processor|  Vertalerhub gebruiken| Aangepaste vertaler gebruiken (voorbeeld)|
|:-----|:-----|:-----|:-----|:-----|
|Translator Text API Versie 2| api.microsofttranslator.com|    Nee  |Ja    |Nee|
|Translator Text API Versie 3| api.cognitive.microsofttranslator.com|  Ja|    Nee| Ja|

**Translator Text API Versie 3**
* Is algemeen beschikbaar en volledig ondersteund.
* Is GDPR-compatibel als processor en voldoet aan alle ISO 20001 en 20018, evenals SOC 3-certificeringsvereisten. 
* Hiermee u de neurale netwerkvertaalsystemen aanroepen die u hebt aangepast met Custom Translator (Preview), de nieuwe functie voor het aanpassen van vertalers NMT. 
* Biedt geen toegang tot aangepaste vertaalsystemen die zijn gemaakt met behulp van de Microsoft Translator Hub.

U gebruikt versie 3 van de Translator Text API Als u het api.cognitive.microsofttranslator.com eindpunt gebruikt.

**Translator Text API Versie 2**
* Voldoet niet aan alle ISO 20001,20018 en SOC 3 certificeringsvereisten. 
* Hiermee u geen beroep doen op de neurale netwerkvertaalsystemen die u hebt aangepast met de aanpassingsfunctie Translator.
* Biedt toegang tot aangepaste vertaalsystemen die zijn gemaakt met de Microsoft Translator Hub.
* U gebruikt versie 2 van de Translator Text API Als u het api.microsofttranslator.com eindpunt gebruikt.

Geen enkele versie van de Translator API maakt een record van uw vertalingen. Uw vertalingen worden nooit met iemand gedeeld. Meer informatie op de [vertaler no-trace](https://www.aka.ms/NoTrace) webpagina.

## <a name="links"></a>Koppelingen

* [Microsoft Privacy Policy](https://privacy.microsoft.com/privacystatement)
* [Microsoft Azure-juridische gegevens](https://azure.microsoft.com/support/legal)
* [Voorwaarden voor onlineservices](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [V3.0-documentatie bekijken](reference/v3-0-reference.md)
