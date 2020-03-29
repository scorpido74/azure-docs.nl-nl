---
title: Migreren van Bing Speech naar Speech-service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het migreren van een bestaand Bing Speech-abonnement naar de spraakservice van Azure Cognitive Services.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: nitinme
ms.openlocfilehash: b95e16f2d8257bfffcaf2524fe7f8ce6be565689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80366585"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migreren van Bing Speech naar de spraakservice

Gebruik dit artikel om uw toepassingen te migreren van de Bing Speech API naar de spraakservice.

In dit artikel worden de verschillen tussen de Bing Speech API's en de Spraakservice beschreven en worden strategieën voorgesteld voor het migreren van uw toepassingen. Uw Bing Speech API-abonnementssleutel werkt niet met de spraakservice. je hebt een nieuw Speech-serviceabonnement nodig.

Eén spraakserviceabonnementssleutel geeft toegang tot de volgende functies. Elk wordt afzonderlijk gemeten, zodat u alleen betaalt voor de functies die u gebruikt.

* [Spraak naar tekst](speech-to-text.md)
* [Aangepaste spraak naar tekst ](https://cris.ai)
* [Tekst naar spraak](text-to-speech.md)
* [Aangepaste tekst naar spraak](how-to-customize-voice-font.md)
* [Spraakomzetting](speech-translation.md) (bevat geen [tekstomzetting](../translator/translator-info-overview.md))

De [Speech SDK](speech-sdk.md) is een functionele vervanging voor de Bing Speech-clientbibliotheken, maar maakt gebruik van een andere API.

## <a name="comparison-of-features"></a>Vergelijking van functies

De Spraakservice is grotendeels vergelijkbaar met Bing Speech, met de volgende verschillen.

| Functie | Bing Speech | Speech Service | Details |
|--|--|--|--|
| C# SDK | :heavy_check_mark: | :heavy_check_mark: | Spraakservice ondersteunt Windows 10, Universal Windows Platform (UWP) en .NET Standard 2.0. |
| C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | Spraakservice ondersteunt Windows en Linux. |
| Java SDK | :heavy_check_mark: | :heavy_check_mark: | Spraakservice ondersteunt Android- en spraakapparaten. |
| Continue spraakherkenning | 10 minuten | Onbeperkt (met SDK) | WebSockets-protocollen voor Bing-spraak en spraakservice ondersteunen maximaal 10 minuten per gesprek. De SpraakSDK maakt echter automatisch opnieuw verbinding bij een time-out of koppelt de verbinding. |
| Gedeeltelijke of tussentijdse resultaten | :heavy_check_mark: | :heavy_check_mark: | Met WebSockets-protocol of SDK. |
| Aangepaste spraakmodellen | :heavy_check_mark: | :heavy_check_mark: | Bing Speech vereist een apart aangepaste spraakabonnement. |
| Aangepaste spraaklettertypen | :heavy_check_mark: | :heavy_check_mark: | Bing Speech vereist een apart Aangepast Spraakabonnement. |
| 24 kHz stemmen | :heavy_minus_sign: | :heavy_check_mark: |
| Spraakherkenning | Vereist aparte LUIS API-aanroep | Geïntegreerd (met SDK) | U een LUIS-sleutel gebruiken met de spraakservice. |
| Eenvoudige intentieherkenning | :heavy_minus_sign: | :heavy_check_mark: |
| Batch transcriptie van lange audiobestanden | :heavy_minus_sign: | :heavy_check_mark: |
| Herkennings-modus | Handleiding via eindpunt URI | Automatisch | De herkenningsmodus is niet beschikbaar in de spraakservice. |
| Eindpuntplaats | Wereldwijd | Regionaal | Regionale eindpunten verbeteren de latentie. |
| REST-API’s | :heavy_check_mark: | :heavy_check_mark: | De REST-API's van spraakservice zijn compatibel met Bing Speech (ander eindpunt). REST API's ondersteunen tekst-naar-spraak en beperkte spraak-naar-tekstfunctionaliteit. |
| WebSockets-protocollen | :heavy_check_mark: | :heavy_check_mark: | De WebSockets API van spraakservice is compatibel met Bing Speech (ander eindpunt). Migreer indien mogelijk naar de Spraak-SDK om uw code te vereenvoudigen. |
| Service-to-service API-aanroepen | :heavy_check_mark: | :heavy_minus_sign: | Aangeboden in Bing Speech via de C# Service Library. |
| Open-source SDK | :heavy_check_mark: | :heavy_minus_sign: |

De spraakservice maakt gebruik van een op tijd gebaseerd prijsmodel (in plaats van een op transacties gebaseerd model). Zie Serviceprijzen voor [spraakservice](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) voor meer informatie.

## <a name="migration-strategies"></a>Migratiestrategieën

Als u of uw organisatie toepassingen in ontwikkeling of productie hebben die een Bing Speech API gebruiken, moet u deze zo snel mogelijk bijwerken om de spraakservice te gebruiken. Zie de documentatie van de [spraakservice](index.yml) voor beschikbare SDK's, codevoorbeelden en zelfstudies.

De REST [API's](rest-apis.md) van spraakservice zijn compatibel met de Bing Spraak-API's. Als u momenteel de Bing Speech REST API's gebruikt, hoeft u alleen het REST-eindpunt te wijzigen en over te schakelen naar een abonnementssleutel voor spraakservice.

De WebSockets-protocollen voor spraakservice zijn ook compatibel met de protocollen die worden gebruikt door Bing Speech. We raden u aan voor nieuwe ontwikkeling de Spraak-SDK te gebruiken in plaats van WebSockets. Het is een goed idee om bestaande code te migreren naar de SDK ook. Net als bij de REST API's vereist bestaande code die Bing Speech via WebSockets gebruikt echter alleen een wijziging in eindpunt en een bijgewerkte sleutel.

Als u een Bing Speech-clientbibliotheek gebruikt voor een specifieke programmeertaal, vereist het migreren naar de [Spraak-SDK](speech-sdk.md) wijzigingen in uw toepassing, omdat de API anders is. De Speech SDK kan uw code eenvoudiger maken, terwijl u ook toegang krijgt tot nieuwe functies. De Speech SDK is beschikbaar in een breed scala aan programmeertalen. API's op alle platforms zijn vergelijkbaar, wat de ontwikkeling van meerdere platforms versoepelt.

De Spraakservice biedt geen globaal eindpunt. Bepaal of uw toepassing efficiënt functioneert wanneer deze één regionaal eindpunt gebruikt voor al het verkeer. Zo niet, gebruik geolocatie om het meest efficiënte eindpunt te bepalen. U hebt een apart spraakserviceabonnement nodig in elke regio die u gebruikt.

Als uw toepassing langdurige verbindingen gebruikt en geen beschikbare SDK kan gebruiken, u een WebSockets-verbinding gebruiken. Beheer de time-outlimiet van 10 minuten door op de juiste tijdstippen opnieuw verbinding te maken.

Ga om te beginnen met de Speech SDK:

1. Download de [Speech SDK](speech-sdk.md).
1. Werk door de spraakservice [snelstartgidsen](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) en [-zelfstudies.](how-to-recognize-intents-from-speech-csharp.md) Kijk ook naar de [code monsters](samples.md) om ervaring op te doen met de nieuwe API's.
1. Werk uw toepassing bij om de spraakservice te gebruiken.

## <a name="support"></a>Ondersteuning

Bing Speech-klanten moeten contact opnemen met de klantenservice door een [ondersteuningsticket te openen.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) U ook contact met ons opnemen als uw ondersteuning een [technisch ondersteuningsplan](https://azure.microsoft.com/support/plans/)vereist.

Ga voor ondersteuning voor spraakservice, SDK en API naar de [ondersteuningspagina](support.md)voor spraakservice .

## <a name="next-steps"></a>Volgende stappen

* [Probeer speechservice gratis uit](get-started.md)
* [Snelstart: spraak herkennen in een UWP-app met de Spraak-SDK](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Zie ook
* [Notities voor het vrijgeven van spraakservice](releasenotes.md)
* [Wat is de Spraakservice](overview.md)
* [Spraakservice en SpraakSDK-documentatie](speech-sdk.md#get-the-sdk)
