---
title: Wat is de Translator Speech-service?
titleSuffix: Azure Cognitive Services
description: Gebruik de API van de Translator Speech-service om spraak-naar-spraak- en spraak-naar-tekstvertaling toe te voegen aan uw toepassingen.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: overview
ms.date: 3/5/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: eaa6e9a7e8e7edd4241594ad7c2188c3fb50295e
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966442"
---
# <a name="what-is-translator-speech-api"></a>Wat is de Translator Speech-API?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

De Translator Speech-API kan worden gebruikt om end-to-end-, realtime, spraakomzettingen toe te voegen aan toepassingen, hulpprogramma's of oplossingen waarvoor meertalige spraakomzetting nodig is, ongeacht het doelbesturingssysteem of de ontwikkelingstalen. De API kan worden gebruikt voor zowel spraak-naar-spraak als spraak-naar-tekst.

De Translator Text-API is een Azure-service, is onderdeel van de [Azure Cognitive Services API](https://docs.microsoft.com/azure/)-verzameling voor Machine Learning en AI-algoritmen in de cloud en is direct te gebruiken in uw ontwikkelprojecten.

Met de Translator Speech-API kunnen clienttoepassingen gesproken audio streamen naar de service en een stream met tekst- en audioresultaten ontvangen, met daarin de herkende tekst in de brontaal en de vertaling in de doeltaal. Tekstresultaten worden geproduceerd door automatische spraakherkenning (Automatic Speech Recognition, ASR) toe te passen op de binnenkomende audiostream, mogelijk gemaakt door deep neural networks. De onbewerkte ASR-uitvoer wordt verder verbeterd met behulp van een nieuwe techniek, TrueText, zodat deze nog beter overeenkomt met de intentie van de gebruiker. TrueText verwijdert bijvoorbeeld onderbrekingen (hmm's en hoesten) en herhaalde woorden en zorgt voor correcte interpunctie en hoofdlettergebruik. Ook is de mogelijkheid om schuttingtaal te maskeren of uit te sluiten in de techniek opgenomen. De herkennings- en vertalingsengines zijn speciaal getraind om gesproken taal te verwerken.

De Translator Speech-service gebruikt stiltedetectie om het einde van een taaluiting te bepalen. Na een onderbreking in de stemactiviteit, streamt de service een definitief resultaat terug voor de voltooide taaluiting. De service kan ook gedeeltelijke resultaten terugsturen, zodat tussenliggende herkenningen en vertalingen zichtbaar zijn voor een lopende taaluiting.

De service biedt voor spraak-naar-spraakomzetting de mogelijkheid om spraak na te bootsen in de doeltalen (tekst-naar-spraak) op basis van de gesproken tekst. Tekst-naar-spraak-audio wordt gemaakt in de indeling die is opgegeven door de client. De indelingen WAV en MP3 zijn beschikbaar.

De Translator Speech-API maakt gebruik van het WebSocket-protocol voor een full-duplex-communicatiekanaal tussen de client en de server.

## <a name="about-microsoft-translator"></a>Over Microsoft Translator
Microsoft Translator is een cloudservice voor machinevertaling. Aan de basis van deze service staan de [Translator Text-API](https://www.microsoft.com/en-us/translator/translatorapi.aspx) en de Translator Speech-API, die te vinden zijn in diverse Microsoft-producten en -services en door duizenden bedrijven over de hele wereld worden gebruikt in hun toepassingen en werkstromen om hun inhoud beschikbaar te maken voor een wereldwijd publiek.

Meer informatie over de [Microsoft Translator-service](https://www.microsoft.com/en-us/translator/home.aspx)

## <a name="microsoft-translator-neural-machine-translation-nmt"></a>Neurale machinevertalingen (NMT) van Microsoft Translator
De Translator Speech-API maakt gebruik van zowel de oudere statistische machinevertalingen (SMT) als de nieuwere neurale machinevertalingen (NMT) om vertalingen te leveren.

Statistische machinevertaling heeft een plafond bereikt wat betreft prestatieverbeteringen. De vertaalkwaliteit wordt niet meer significant verbeterd voor algemene systemen die SMT gebruiken. Een nieuwe vertaaltechnologie op basis van AI-technologie, op basis van neurale netwerken (NN), wordt steeds vaker gebruikt.

NMT biedt betere vertalingen, niet alleen op basis van de kwaliteitsscores van onbewerkte vertalingen, maar ook omdat deze soepeler en menselijker klinken dan SMT-vertalingen.
De belangrijkste reden hiervoor is dat NMT gebruikmaakt van de volledige context van een zin bij het vertalen van woorden. Bij SMT wordt alleen gekeken naar de directe context van een paar woorden voor en na elk woord.

NMT-modellen vormen de kern van de API en zijn niet zichtbaar voor eindgebruikers. De enige merkbare verschillen zijn:
* de verbeterde vertaalkwaliteit, met name voor talen zoals Chinees, Japans en Arabisch;
* de incompatibiliteit met de bestaande Hub-aanpassingsfuncties (voor gebruik met de Microsoft Translator Text-API).

Alle ondersteunde talen voor spraakomzetting worden vertaald met behulp van NMT. Alle spraak-naar-spraakomzetting maakt dus gebruik van NMT.

Spraak-naar-tekst kan een combinatie van NMT en SMT gebruiken, afhankelijk van de taalcombinatie. Als de doeltaal voor NMT wordt ondersteund, wordt de volledige vertaling verzorgd door NMT. Als de doeltaal wordt niet ondersteund door NMT, is de vertaling een hybride van NMT en SMT, waarbij Engels als tussenstap tussen de twee talen dient.

Bekijk de ondersteunde talen op [Microsoft.com](https://www.microsoft.com/en-us/translator/languages.aspx).

Meer informatie over [de werking van NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanmelden](translator-speech-how-to-signup.md)

> [!div class="nextstepaction"]
> [Code schrijven](quickstarts/csharp.md)

## <a name="see-also"></a>Zie ook
- [Documentatiepagina van Cognitive Services](https://docs.microsoft.com/azure/)
- [Productpagina van Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
- [Informatie over de oplossing en prijzen](https://www.microsoft.com/en-us/translator/home.aspx)
