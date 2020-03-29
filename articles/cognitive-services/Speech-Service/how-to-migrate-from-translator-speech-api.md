---
title: Migreren van de Api voor vertalersspraak naar de spraakservice
titleSuffix: Azure Cognitive Services
description: Meer informatie over het migreren van uw toepassingen van de Translator Speech API naar de spraakservice.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: aahi
ms.openlocfilehash: 75a456c4a297b0465c34b8e0af2e87056ad565b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77560895"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migreren van de Api voor vertalersspraak naar de spraakservice

Gebruik dit artikel om uw toepassingen te migreren van de Microsoft Translator Speech API naar de [spraakservice](index.yml). Deze handleiding beschrijft de verschillen tussen de Translator Speech API en Speech-service en stelt strategieën voor voor het migreren van uw toepassingen.

> [!NOTE]
> Uw abonnementssleutel voor de Log-api voor vertalers wordt niet geaccepteerd door de spraakservice. U moet een nieuw abonnement op de Spraakservice maken.

## <a name="comparison-of-features"></a>Vergelijking van functies

| Functie                                           | Translator Speech-API                                  | Speech Service | Details                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Vertaling naar tekst                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Vertaling naar spraak                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Globaal eindpunt                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | De Spraakservice biedt geen globaal eindpunt. Een globaal eindpunt kan verkeer automatisch naar het dichtstbijzijnde regionale eindpunt leiden, waardoor de latentie in uw toepassing afneemt.                                                    |
| Regionale eindpunten                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Verbindingstijdslimiet                             | 90 minuten                                               | Onbeperkt met de SDK. 10 minuten met een WebSockets-verbinding.                                                                                                                                                                                                                                                                                   |
| Auth-toets in koptekst                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Meerdere talen vertaald in één aanvraag | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDKs beschikbaar                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Zie de documentatie van de [spraakservice](index.yml) voor beschikbare SDK's.                                                                                                                                                    |
| WebSockets-verbindingen                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| API voor talen                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | De spraakservice ondersteunt hetzelfde talenpakket dat wordt beschreven in het [referentieartikel voor de vertaaltalen van de Vertaler.](../translator-speech/languages-reference.md) |
| Godslastering Filter en Marker                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM als invoer                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Andere bestandstypen als invoer                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Gedeeltelijke resultaten                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Tijdsinformatie                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| Correlatie-id                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Aangepaste spraakmodellen                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | De spraakservice biedt aangepaste spraakmodellen waarmee u spraakherkenning aanpassen aan de unieke woordenschat van uw organisatie.                                                                                                                                           |
| Aangepaste vertaalmodellen                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Als u zich abonneert op de Microsoft Text Translation API, u [Custom Translator](https://www.microsoft.com/translator/business/customization/) gebruiken om uw eigen gegevens te gebruiken voor nauwkeurigere vertalingen.                                                 |

## <a name="migration-strategies"></a>Migratiestrategieën

Als u of uw organisatie toepassingen in ontwikkeling of productie hebben die gebruikmaken van de Translator Speech API, moet u deze bijwerken om de spraakservice te gebruiken. Zie de documentatie van de [spraakservice](index.yml) voor beschikbare SDK's, codevoorbeelden en zelfstudies. Houd rekening met het volgende wanneer u migreert:

* De Spraakservice biedt geen globaal eindpunt. Bepaal of uw toepassing efficiënt functioneert wanneer deze één regionaal eindpunt gebruikt voor al het verkeer. Zo niet, gebruik geolocatie om het meest efficiënte eindpunt te bepalen.

* Als uw toepassing langdurige verbindingen gebruikt en de beschikbare SDK's niet kan gebruiken, u een WebSockets-verbinding gebruiken. Beheer de time-outlimiet van 10 minuten door op de juiste tijdstippen opnieuw verbinding te maken.

* Als uw toepassing de Translator Text API en Translator Speech API gebruikt om aangepaste vertaalmodellen in te schakelen, u categorie-id's rechtstreeks toevoegen met behulp van de spraakservice.

* In tegenstelling tot de Translator Speech API kan de Spraakservice vertalingen in meerdere talen in één aanvraag voltooien.

## <a name="next-steps"></a>Volgende stappen

* [Probeer speechservice gratis uit](get-started.md)
* [Snelstart: spraak herkennen in een UWP-app met de Spraak-SDK](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Zie ook

* [Wat is de Spraakservice](overview.md)
* [Spraakservice en SpraakSDK-documentatie](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
