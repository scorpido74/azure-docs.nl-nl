---
title: Spraakassistenten stellen veelgestelde vragen
titleSuffix: Azure Cognitive Services
description: Krijg antwoorden op de meest populaire vragen over spraakassistenten met behulp van Aangepaste opdrachten (Preview) of het direct line-spraakkanaal.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 8480299c2c889a243150028ac9651f4b62656aec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110341"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Spraakassistenten stellen veelgestelde vragen

Als u geen antwoorden op uw vragen in dit document vinden, raadpleegt u [andere ondersteuningsopties.](support.md)

## <a name="general"></a>Algemeen

**V: Wat is een spraakassistent?**

**A:** Net als Cortana is een spraakassistent een oplossing die luistert naar de gesproken uitingen van een gebruiker, de inhoud van die uitingen analyseert voor betekenis, een of meer acties uitvoert als reactie op de intentie van de utterance en vervolgens een antwoord geeft aan de gebruiker die vaak een gesproken component bevat. Het is een "voice-in, voice-out" ervaring voor interactie met een systeem. auteurs van spraakassistenten maken een `DialogServiceConnector` toepassing op het apparaat met behulp van de spraak-SDK om te communiceren met een assistent die is gemaakt met behulp van [Aangepaste opdrachten (Preview)](custom-commands.md) of het [direct line-spraakkanaal](direct-line-speech.md) van het Bot Framework. Deze assistenten kunnen aangepaste zoekwoorden, aangepaste spraak en aangepaste spraak gebruiken om een ervaring te bieden die is afgestemd op uw merk of product.

**V: Moet ik aangepaste opdrachten (preview) of directe lijnspraak gebruiken? Wat is het verschil?**

**A:** [Aangepaste opdrachten (Preview)](custom-commands.md) is een set tools met een lagere complexiteit om eenvoudig een assistent te maken en te hosten die goed geschikt is voor taakvoltooiingsscenario's. [Direct Line Speech](direct-line-speech.md) biedt rijkere, meer geavanceerde mogelijkheden die robuuste gespreksscenario's mogelijk kunnen maken. Zie de [vergelijking van assistent-oplossingen](voice-assistants.md#choosing-an-assistant-solution) voor meer informatie.

**V: Hoe ga ik aan de slag?**

**A:** De beste manier om te beginnen met het maken van een aangepaste opdrachten (Preview) applicatie of fundamentele Bot Framework bot.

- [Een toepassing Aangepaste opdrachten (Voorbeeld) maken](quickstart-custom-speech-commands-create-new.md)
- [Een basisbot van Bot Framework maken](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
- [Een bot aansluiten op het kanaal Direct Line Spraak](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Foutopsporing

**V: Waar is mijn kanaalgeheim?**

**A:** Als u de voorbeeldversie van Direct Line Speech hebt gebruikt of als u gerelateerde documentatie leest, u een geheime sleutel verwachten op de registratiepagina van direct line-spraakkanalen. De v1.7-fabrieksmethode `DialogServiceConfig` `FromBotSecret` in de Speech SDK verwacht ook deze waarde.

De nieuwste versie van Direct Line Speech vereenvoudigt het contact met uw bot vanaf een apparaat. Op de pagina kanaalregistratie koppelt de vervolgkeuzelijst bovenaan uw direct line speechkanaalregistratie aan een spraakbron. Eenmaal gekoppeld bevat de v1.8 `BotFrameworkConfig::FromSubscription` Speech SDK een `DialogServiceConnector` fabrieksmethode waarmee u contact opneemt met de bot die u aan uw abonnement hebt gekoppeld.

Als u uw clienttoepassing nog steeds migreert van v1.7 naar v1.8, `DialogServiceConfig::FromBotSecret` u blijven werken met een niet-lege, niet-null-waarde voor de kanaalgeheime parameter, bijvoorbeeld het vorige geheim dat u hebt gebruikt. Het zal gewoon worden genegeerd bij het gebruik van een spraakabonnement in verband met een nieuwere kanaal registratie. Houd er rekening mee dat de waarde niet-null en niet-leeg _moet_ zijn, omdat deze worden gecontroleerd op het apparaat voordat de service-side association relevant is.

Voor een meer gedetailleerde gids, zie de [tutorial sectie](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel) die wandelingen door kanaal registratie.

**V: Ik krijg een 401 fout bij het aansluiten en niets werkt. Ik weet dat mijn spraakabonnementssleutel geldig is. Wat gebeurt er?**

**A:** Wanneer u uw abonnement beheert op de Azure-portal, moet u ervoor zorgen dat u de **spraakbron** (Microsoft.CognitiveServicesSpeechServices, 'Spraak') gebruikt en _niet_ de **bron Cognitive Services** (Microsoft.CognitiveServicesAllInOne, 'Alle cognitieve services'). Controleer ook [de ondersteuning van spraakserviceregio's voor spraakassistenten.](regions.md#voice-assistants)

![correct abonnement voor directe lijnspraak](media/voice-assistants/faq-supported-subscription.png "voorbeeld van een compatibel spraakabonnement")

**V: Ik krijg herkenning `DialogServiceConnector`tekst terug van mijn , maar ik zie een '1011' fout en niets van mijn bot. Waarom?**

**A:** Deze fout duidt op een communicatieprobleem tussen uw assistent en de spraakassistent.

- Controleer bij Aangepaste opdrachten (Voorbeeld) of de toepassing Aangepaste opdrachten (Preview) wordt gepubliceerd
- Controleer bij Direct Line Speech of u uw bot hebt [aangesloten op het Direct Line Speech-kanaal,](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)ondersteuning voor [streamingprotocol](https://aka.ms/botframework/addstreamingprotocolsupport) hebt toegevoegd aan uw bot (met de bijbehorende websocketondersteuning) en controleer of uw bot reageert op binnenkomende verzoeken van het kanaal.

**V: Deze code werkt nog steeds niet en/of ik krijg `DialogServiceConnector`een andere fout bij het gebruik van een . Wat moet ik doen?**

**A:** Logboekregistratie op basis van bestanden biedt aanzienlijk meer details en kan helpen bij het versnellen van ondersteuningsaanvragen. Zie hoe u [bestandslogboekregistratie wilt gebruiken](how-to-use-logging.md)om deze functionaliteit in te schakelen.

## <a name="next-steps"></a>Volgende stappen

- [Probleemoplossing](troubleshooting.md)
- [Opmerkingen vrijgeven](releasenotes.md)
