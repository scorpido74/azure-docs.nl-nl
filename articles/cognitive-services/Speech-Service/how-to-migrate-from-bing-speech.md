---
title: Migreren van Bing Speech naar de speech-service
titleSuffix: Azure Cognitive Services
description: Meer informatie over hoe u vanuit een bestaand Bing Speech abonnement migreert naar de spraak service vanuit Azure Cognitive Services.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: nitinme
ms.openlocfilehash: 00a20ac2d7f05c42be9e955eeb8f47c302db7885
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464567"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migreren van Bing Speech naar de speech-service

Gebruik dit artikel om uw toepassingen van de Bing Speech-API naar de speech-service te migreren.

In dit artikel vindt u een overzicht van de verschillen tussen de Bing Speech Api's en de spraak Services, en suggesties voor strategieën voor het migreren van uw toepassingen. Uw Bing Speech-API-abonnements sleutel werkt niet met de spraak service; u hebt een nieuw abonnement voor spraak services nodig.

Met één abonnement sleutel voor spraak Services krijgt u toegang tot de volgende functies. Elk wordt afzonderlijk gemeten, zodat u alleen betaalt voor de functies die u gebruikt.

* [Spraak naar tekst](speech-to-text.md)
* [Aangepaste spraak naar tekst ](https://cris.ai)
* [Tekst naar spraak](text-to-speech.md)
* [Aangepaste tekst naar spraak](how-to-customize-voice-font.md)
* [Spraakomzetting](speech-translation.md) (bevat geen [tekstomzetting](../translator/translator-info-overview.md))

De [Speech SDK](speech-sdk.md) is een functionele vervanging voor de Bing speech-client Bibliotheken, maar gebruikt een andere API.

## <a name="comparison-of-features"></a>Vergelijking van functies

De spraak Services zijn grotendeels vergelijkbaar met Bing Speech, met de volgende verschillen.

Functie | Bing Speech | Spraakservices | Details
-|-|-|-
C++SDK | :heavy_minus_sign: | :heavy_check_mark: | Speech Services biedt ondersteuning voor Windows en Linux.
Java SDK | :heavy_check_mark: | :heavy_check_mark: | Spraak Services ondersteunen Android-en spraak apparaten.
C# SDK | :heavy_check_mark: | :heavy_check_mark: | Speech Services ondersteunt Windows 10, Universeel Windows-platform (UWP) en .NET Standard 2,0.
Continue spraak herkenning | 10 minuten | Onbeperkt (met SDK) | De websockets protocollen van Bing Speech en Speech Services ondersteunen Maxi maal tien minuten per oproep. De Speech SDK maakt echter automatisch opnieuw verbinding met de time-out of de verbinding wordt verbroken.
Gedeeltelijke of tussentijdse resultaten | :heavy_check_mark: | :heavy_check_mark: | Met het websockets protocol of SDK.
Aangepaste spraak modellen | :heavy_check_mark: | :heavy_check_mark: | Voor Bing Speech is een afzonderlijk Custom Speech-abonnement vereist.
Aangepaste spraak lettertypen | :heavy_check_mark: | :heavy_check_mark: | Voor Bing Speech is een apart aangepast spraak abonnement vereist.
24-KHz stemmen | :heavy_minus_sign: | :heavy_check_mark:
Spraakintentieherkenning | Vereist een afzonderlijke LUIS-API-aanroep | Geïntegreerd (met SDK) |  U kunt een LUIS-sleutel gebruiken met de spraak service.
Eenvoudige intentie herkenning | :heavy_minus_sign: | :heavy_check_mark:
Batch-transcriptie met lange audio bestanden | :heavy_minus_sign: | :heavy_check_mark:
Herkennings-modus | Hand matig via eind punt-URI | Automatisch | Herkennings modus is niet beschikbaar in de speech-service.
Locatie van eind punt | Wereldwijd | Landspecifieke | Regionale eind punten verbeteren de latentie.
REST API's | :heavy_check_mark: | :heavy_check_mark: | De REST-Api's voor spraak Services zijn compatibel met Bing Speech (verschillende eind punten). REST-Api's bieden ondersteuning voor tekst-naar-spraak-en beperkte spraak-naar-tekst functionaliteit.
Protocollen voor websockets | :heavy_check_mark: | :heavy_check_mark: | De API voor websockets van speech Services is compatibel met Bing Speech (ander eind punt). Migreer naar de spraak-SDK, indien mogelijk, om uw code te vereenvoudigen.
Service-naar-Service-API-aanroepen | :heavy_check_mark: | :heavy_minus_sign: | Verschaft in Bing Speech via de C# service bibliotheek.
Open-Source-SDK | :heavy_check_mark: | :heavy_minus_sign: |

De spraak Services gebruiken een op tijd gebaseerd prijs model (in plaats van een model op basis van een trans actie). Zie de [prijzen voor spraak Services](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) voor meer informatie.

## <a name="migration-strategies"></a>Migratiestrategieën

Als u of uw organisatie toepassingen in ontwikkeling of productie heeft die gebruikmaken van een Bing Speech-API, moet u ze zo snel mogelijk bijwerken om de spraak services te gebruiken. Raadpleeg de [documentatie voor speech Services](index.md) voor beschik bare sdk's, code voorbeelden en zelf studies.

De rest- [api's](rest-apis.md) voor spraak Services zijn compatibel met de Bing speech-api's. Als u momenteel de Bing Speech REST-Api's gebruikt, hoeft u alleen het REST-eind punt te wijzigen en over te scha kelen naar een abonnement sleutel voor spraak Services.

De websockets voor spraak Services zijn ook compatibel met de protocollen die worden gebruikt door Bing Speech. U wordt aangeraden om voor nieuwe ontwikkeling de Speech SDK te gebruiken in plaats van websockets. Het is een goed idee om bestaande code ook naar de SDK te migreren. Net als bij de REST-Api's is voor bestaande code die gebruikmaakt van Bing Speech via websockets, alleen een wijziging in het eind punt en een bijgewerkte sleutel vereist.

Als u een Bing Speech-client bibliotheek gebruikt voor een specifieke programmeer taal, moet u voor de migratie naar de [Speech SDK](speech-sdk.md) wijzigingen aanbrengen in uw toepassing, omdat de API anders is. De Speech SDK kan uw code eenvoudiger maken, en u hebt ook toegang tot nieuwe functies.

Op dit moment ondersteunt C# de spraak-SDK ([Details hier](https://aka.ms/csspeech)), java (Android en aangepaste apparaten), objectief C (Ios C++ ), (Windows en Linux) en Java script. Api's op alle platforms zijn vergelijkbaar en versnellen de ontwikkeling van meerdere platforms.

De spraak services bieden geen globaal eind punt. Bepaal of uw toepassing efficiënt werkt wanneer er één regionaal eind punt wordt gebruikt voor al het verkeer. Als dat niet het geval is, gebruikt u geolocatie om het meest efficiënte eind punt te bepalen. U hebt een afzonderlijk abonnement voor spraak services nodig in elke regio die u gebruikt.

Als uw toepassing langdurige verbindingen gebruikt en geen beschik bare SDK kan gebruiken, kunt u een websockets-verbinding gebruiken. De time-outlimiet van 10 minuten beheren door opnieuw verbinding te maken met de juiste tijden.

Om aan de slag te gaan met de Speech SDK:

1. Down load de [spraak-SDK](speech-sdk.md).
1. Werk met de [Snelstartgids](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) en [zelf studies](how-to-recognize-intents-from-speech-csharp.md)voor spraak Services. Bekijk ook de [code voorbeelden](samples.md) om ervaring te krijgen met de nieuwe api's.
1. Werk uw toepassing bij om de spraak services te gebruiken.

## <a name="support"></a>Ondersteuning

Bing Speech klanten contact opnemen met klant ondersteuning door een [ondersteunings ticket](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)te openen. U kunt ook contact met ons opnemen als voor uw ondersteunings [abonnement een technisch ondersteunings plan](https://azure.microsoft.com/support/plans/)nodig is.

Ga naar de [ondersteunings pagina](support.md)voor spraak Services voor spraak service, SDK en API-ondersteuning.

## <a name="next-steps"></a>Volgende stappen

* [Probeer spraak services gratis uit](get-started.md)
* [Quick Start: spraak herkennen in een UWP-app met behulp van de Speech SDK](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Zie ook
* [Opmerkingen bij de release van speech Services](releasenotes.md)
* [Wat is de speech-service](overview.md)
* [Documentatie voor spraak Services en Speech SDK](speech-sdk.md#get-the-sdk)
