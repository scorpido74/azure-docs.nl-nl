---
title: Micro soft Bing Speech-Service | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Gebruik micro soft Speech API om op spraak gebaseerde acties toe te voegen aan uw apps, waaronder realtime-interactie met gebruikers.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff9de2557583eecd5ddb2acd97b445a93abc5fb6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966654"
---
# <a name="what-is-bing-speech"></a>Wat is Bing Speech?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

De op de cloud gebaseerde micro soft-Bing Speech-API biedt ontwikkel aars een eenvoudige manier om krachtige spraak functies in hun toepassingen te maken, zoals het besturings element spraak opdracht, het dialoog venster voor gebruikers met natuurlijke spraak conversatie en spraak transcriptie en-dictering. De Speech-API van micro soft ondersteunt zowel *spraak op tekst* als *Text to speech* conversie.

- **Spraak naar tekst** API converteert menselijke spraak naar tekst die kan worden gebruikt als invoer of opdrachten voor het beheren van uw toepassing.
- **Text to speech** Met API wordt tekst geconverteerd naar audiostreams die kunnen worden weer gegeven aan de gebruiker van uw toepassing.

## <a name="speech-to-text-speech-recognition"></a>Spraak naar tekst (spraak herkenning)

De *API voor* spraak herkenning van micro soft verzorgt audio stromen naar tekst die door uw toepassing kan worden weer gegeven aan de gebruiker of waarmee kan worden gereageerd als opdracht invoer. Het biedt ontwikkel aars twee manieren om spraak toe te voegen aan hun apps: REST-Api's **of** op websockets gebaseerde client bibliotheken.

- [Rest-api's](GetStarted/GetStartedREST.md): Ontwikkel aars kunnen HTTP-aanroepen van hun apps naar de service gebruiken voor spraak herkenning.
- [Client bibliotheken](GetStarted/GetStartedClientLibraries.md): Ontwikkel aars kunnen voor geavanceerde functies micro soft speech client-bibliotheken downloaden en een koppeling maken naar hun apps.  De-client bibliotheken zijn beschikbaar op verschillende platformen (Windows, Android, iOS) met behulp van verschillende talen (C#Java, java script, ObjectiveC). In tegens telling tot de REST-Api's maakt de client bibliotheken gebruik van WebSocket-protocol.

| Gebruiksvoorbeelden | [REST API's](GetStarted/GetStartedREST.md) | [Client bibliotheken](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Een korte gesp roken audio converteren, bijvoorbeeld opdrachten (audio lengte < 15 s) zonder tussenliggende resultaten | Ja | Ja |
| Een lange audio converteren (> 15 s) | Nee | Ja |
| Stream audio met de gewenste tussentijdse resultaten | Nee | Ja |
| Inzicht in de tekst die is geconverteerd van audio met behulp van LUIS | Nee | Ja |

Welke benaderingen van ontwikkel aars kiezen (REST Api's of client bibliotheken), micro soft Speech Service ondersteunt het volgende:

- Geavanceerde technologieën voor spraak herkenning van micro soft die worden gebruikt door Cortana, Office dicteren, Office Translator en andere micro soft-producten.
- Realtime continue herkenning. Met de API voor spraak herkenning kunnen gebruikers audio naar tekst in realtime transcriberen en wordt ondersteuning geboden voor het ontvangen van de tussenliggende resultaten van de woorden die tot nu toe zijn herkend. De spraak service biedt ook ondersteuning voor het detecteren van het eind van spraak. Daarnaast kunnen gebruikers extra opmaak mogelijkheden kiezen, zoals het hoofdletter gebruik en interpunctie, het maskeren van scheld woorden en tekst normalisatie.
- Ondersteunt geoptimaliseerde spraak herkennings resultaten voor *interactieve*, *conversatie*-en *dicteer* scenario's. Voor gebruikers scenario's waarvoor aangepaste taal modellen en akoestische modellen zijn vereist, kunt u met [Custom Speech Service](../custom-speech-service/cognitive-services-custom-speech-home.md) spraak modellen maken die zijn afgestemd op uw toepassing en uw gebruikers.
- Ondersteuning voor veel gesp roken talen in meerdere dialecten. Zie [herkennings talen](api-reference-rest/supportedlanguages.md)voor een volledige lijst met ondersteunde talen in elke herkennings modus.
- Integratie met language standing. Naast het converteren van de audio-invoer naar tekst, biedt de *spraak naar tekst* toepassingen een extra mogelijkheid om te begrijpen wat de tekst inhoudt. De [Language Understanding intelligent service (Luis)](../LUIS/what-is-luis.md) wordt gebruikt voor het uitpakken van intenties en entiteiten uit de herkende tekst.

### <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met het gebruik van micro soft speech recognition service met [rest api's](GetStarted/GetStartedREST.md) of [client bibliotheken](GetStarted/GetStartedClientLibraries.md).
- Bekijk [voorbeeld toepassingen](samples.md) in de programmeer taal van uw voor keur.
- Ga naar het gedeelte met Naslag informatie om de details van het [micro soft speech-protocol](API-Reference-REST/websocketprotocol.md) en API-verwijzingen te vinden.

## <a name="text-to-speech-speech-synthesis"></a>Tekst-naar-spraak (spraak-synthese)

*Text to speech* Api's gebruiken REST om gestructureerde tekst te converteren naar een audio stroom. De Api's bieden snelle tekst-naar-spraak conversie in verschillende stemmen en talen. Daarnaast hebben gebruikers ook de mogelijkheid om audio-eigenschappen te wijzigen, zoals de uitspraak, het volume, de hoogte, enzovoort. SSML-Tags gebruiken.

### <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met het gebruik van micro soft text to Speech Service: [Text to Speech-API verwijzing](api-reference-rest/bingvoiceoutput.md). Zie [ondersteunde land instellingen en spraak lettertypen](api-reference-rest/bingvoiceoutput.md#SupLocales)voor een volledige lijst met talen en stemmen die door Text to speech worden ondersteund.
