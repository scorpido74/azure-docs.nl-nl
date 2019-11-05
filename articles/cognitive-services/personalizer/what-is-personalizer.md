---
title: Wat is Personalizer?
titleSuffix: Azure Cognitive Services
description: Personaler is een API-service in de Cloud waarmee u de beste ervaring kunt kiezen die aan uw gebruikers wordt weer gegeven, en leer vanuit hun realtime gedrag.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 6b55ce851bb12e37aed37039889aa8e69223a286
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467186"
---
# <a name="what-is-personalizer"></a>Wat is Personalizer?

Azure Personaler is een API-service in de Cloud waarmee u de beste ervaring kunt kiezen die wordt weer gegeven aan uw gebruikers.

* Geef informatie op over uw gebruikers en inhoud en ontvang de meest voorkomende actie om uw gebruikers weer te geven. 
* U hoeft geen gegevens op te schonen en labelen voordat u Personaler gebruikt.
* Geef feedback over persoonlijker wanneer het handig is voor u. 
* Real-time analyse weer geven. 

Een demonstratie bekijken [over hoe personaler werkt](https://personalizercontentdemo.azurewebsites.net/)

## <a name="how-does-personalizer-work"></a>Hoe werkt Personaler?

Personaler gebruikt machine learning modellen om te ontdekken welke actie het hoogst in een context kan worden gerangschikt. Uw client toepassing bevat een lijst met mogelijke acties, met informatie over de activiteiten; en informatie over de context, die informatie kan bevatten over de gebruiker, het apparaat, enzovoort. Personaler bepaalt welke actie moet worden ondernomen. Zodra uw client toepassing de gekozen actie gebruikt, geeft deze aan de persoonlijke voor keuren de vorm van een belonings Score. Nadat de feedback is ontvangen, wordt door Personaler automatisch een eigen model bijgewerkt dat wordt gebruikt voor toekomstige classificaties. In de loop van de tijd zal Personaler een model trainen waarmee de beste actie kan worden voorgesteld in elke context op basis van hun functies.

## <a name="how-do-i-use-the-personalizer"></a>De Personaler Hoe kan ik gebruiken?

![Personaler gebruiken om te kiezen welke video wordt weer gegeven aan een gebruiker](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Kies een ervaring in uw app om deze aan te passen.
1. Maak en configureer een exemplaar van de personalisatie service in de Azure Portal. Elk exemplaar is een aangepaste lus.
1. Gebruik de [rangorde API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) om persoonlijker met informatie (_functies_) over uw gebruikers en de inhoud (_acties_) aan te roepen. U hoeft geen schone, gelabelde gegevens op te geven voordat u personaliseren gebruikt. Api's kunnen rechtstreeks worden aangeroepen of met behulp van Sdk's die beschikbaar zijn voor verschillende programmeer talen.
1. Geef in de client toepassing de gebruiker de actie weer die door Personaler is geselecteerd.
1. Gebruik de [belonings-API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) om aan persoonlijke voor keuren feedback te geven die aangeeft of de gebruiker de actie heeft gekozen voor persoonlijke instellingen. Dit is een _[belonings Score](concept-rewards.md)_ .
1. Bekijk de analyse in de Azure Portal om te evalueren hoe het systeem werkt en hoe uw gegevens personalisatie helpen.

## <a name="where-can-i-use-personalizer"></a>Waar kan ik Personaler gebruiken?

Uw client toepassing kan bijvoorbeeld persoonlijker toevoegen aan:

* Personaliseer welk artikel op een nieuws website is gemarkeerd.    
* Optimaliseer de plaatsing van advertenties op een website.
* Een aangepast ' Aanbevolen item ' op een winkel website weer geven.
* Stel gebruikers interface-elementen, zoals filters, voor op een specifieke foto.
* Kies een reactie op een chat-bot om de gebruikers intentie te verduidelijken of om een actie te suggereren.
* Volg prioriteiten voor suggesties van wat een gebruiker moet doen als de volgende stap in een bedrijfs proces.

Personaler is geen service voor het persistent maken en beheren van gebruikers profiel gegevens, of voor het vastleggen van de voor keuren of geschiedenis van afzonderlijke gebruikers. Personaler leert van de functies van elke interactie in de actie van een context in één model dat maximale beloningen kan verkrijgen wanneer vergelijk bare functies optreden. 

## <a name="personalization-for-developers"></a>Personalisatie voor ontwikkel aars

Personaler service heeft twee Api's:

* Verzend informatie (_functies_) over uw gebruikers en de inhoud (_acties_) die u wilt personaliseren. Personaler reageert met de bovenste actie.
* Stuur feedback naar persoonlijkere informatie over hoe goed de classificatie werkt als een [belonings Score](concept-rewards.md). 

![Eenvoudige reeks gebeurtenissen voor personalisatie](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Volgende stappen

* [Wat is er nieuw in personaliseren?](whats-new.md)
* [Hoe Personaler werkt?](how-personalizer-works.md)
* [Wat is versterking van het onderwijs?](concepts-reinforcement-learning.md)
* [Meer informatie over de functies en acties voor de rang aanvraag](concepts-features.md)
* [Meer informatie over het bepalen van de score voor de belonings aanvraag](concept-rewards.md)
* [De interactieve demo gebruiken](https://personalizationdemo.azurewebsites.net/)
