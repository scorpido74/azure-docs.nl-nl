---
title: Wat is de Translator Text-API? - Tekst-API van Translator
titlesuffix: Azure Cognitive Services
description: Integreer de Translator Text-API in uw toepassingen, websites, hulpprogramma's en andere oplossingen om gebruikerservaringen in meerdere talen te bieden.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 12/09/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: f6b55163440b25bf69c76e9b1217c439b0884d5d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78331039"
---
# <a name="what-is-the-translator-text-api"></a>Wat is de Translator Text-API?

De Translator Text-API is makkelijk in uw toepassingen, websites, hulpprogramma's en oplossingen te integreren. U kunt er meertalige gebruikerservaringen in [meer dan zestig talen](languages.md) aan toevoegen en u kunt het gebruiken op elk hardwareplatform onder elk willekeurig besturingssysteem voor vertalingen van tekst naar tekst.

De Translator Text-API is onderdeel van de Azure [Cognitive Services API](https://docs.microsoft.com/azure/?pivot=products&panel=ai)-verzameling voor Machine Learning en AI-algoritmen in de cloud en is direct te gebruiken in uw ontwikkelprojecten.

## <a name="about-microsoft-translator"></a>Over Microsoft Translator

Microsoft Translator is een cloudservice voor machinevertaling. De basis van deze service is de Translator Text-API, die een aantal Microsoft-producten en -services aanstuurt en die door duizenden bedrijven over de hele wereld wordt gebruikt in hun toepassingen en werkstromen, zodat hun inhoud beschikbaar wordt gemaakt voor een wereldwijd publiek.

Spraakomzetting, mogelijk gemaakt door de Translator Text-API, is ook beschikbaar via de [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/). Het combineert functionaliteit van de Translator Speech API en de Custom Speech Service in een uniforme en volledig aanpasbare service.Speech Service vervangt de Translator Speech-API, die buiten bedrijf wordt genomen op 15 oktober 2019.

## <a name="language-support"></a>Taalondersteuning

Microsoft Translator biedt meertalige ondersteuning voor vertalingen, transliteraties, taaldetectie en woordenboeken. Zie [taalondersteuning](language-support.md) voor een volledige lijst of open de lijst programmatisch met de [REST API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).  

## <a name="microsoft-translator-neural-machine-translation"></a>Neurale machinevertalingen van Microsoft Translator

Neurale machinevertalingen (NMT) is de nieuwe standaard voor AI-machinevertalingen van hoge kwaliteit. Dit vervangt de verouderde technologie voor statistische machinevertaling waarvan de kwaliteit niet meer is verbeterd sinds het midden van de jaren 10.

NMT biedt betere vertalingen dan SMT, niet alleen op basis van de kwaliteitsscores van onbewerkte vertalingen, maar ook omdat deze soepeler en menselijker klinken. De belangrijkste reden hiervoor is dat NMT gebruikmaakt van de volledige context van een zin bij het vertalen van woorden. Bij SMT werd alleen gekeken naar de directe context van een paar woorden voor en na elk woord.

NMT-modellen vormen de kern van de API en zijn niet zichtbaar voor eindgebruikers. Het enige merkbare verschil is de verbeterde vertaalkwaliteit, met name voor talen zoals Chinees, Japans en Arabisch.

Meer informatie over [de werking van NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="language-customization"></a>Taal aanpassen

Custom Translator is een uitbreiding op de Microsoft Translator-kernservice en kan samen met de Translator Text-API worden gebruikt om het neurale vertaalsysteem aan te passen en de vertalingen te verbeteren voor uw specifieke terminologie en stijl.

Met Custom Translator kunt u vertaalsystemen maken waarin de terminologie wordt verwerkt die wordt gebruikt in uw bedrijf of sector. U kunt uw aangepaste vertaalsysteem vervolgens eenvoudig integreren in uw bestaande toepassingen, werkstromen en websites voor meerdere typen apparaten via de gewone Microsoft Translator Text-API, met behulp van de categorieparameter.

Meer informatie over [taalaanpassing](customization.md)

## <a name="next-steps"></a>Volgende stappen

- [Meld u aan](translator-text-how-to-signup.md) voor een toegangssleutel.
- [API-naslag](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) bevat de technische documentatie voor de API's.
- [Prijsdetails](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
