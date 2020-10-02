---
title: Swagger-documentatie-Speech-Service
titleSuffix: Azure Cognitive Services
description: De Swagger-documentatie kan worden gebruikt om automatisch Sdk's te genereren voor een aantal programmeer talen. Alle bewerkingen in onze service worden ondersteund door Swagger
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 30e8224b6cb757f044a5eac598d834cee838391e
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629978"
---
# <a name="swagger-documentation"></a>Documentatie voor Swagger

De speech-service biedt een Swagger-specificatie om te communiceren met een aantal REST-Api's die worden gebruikt voor het importeren van gegevens, het maken van modellen, het testen van model nauwkeurigheid, het maken van aangepaste eind punten, het in de wachtrij plaatsen van batch transcripties en het beheren van abonnementen. De meeste bewerkingen die beschikbaar zijn via de Custom Speech Portal, kunnen via een programma worden voltooid met behulp van deze Api's.

> [!NOTE]
> Bewerkingen voor spraak naar tekst en tekst naar spraak worden ondersteund als REST Api's, die op zijn beurt worden beschreven in de Swagger-specificatie.

## <a name="generating-code-from-the-swagger-specification"></a>Code genereren op basis van de Swagger-specificatie

De [Swagger-specificatie](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) bevat opties waarmee u snel kunt testen op verschillende paden. Soms is het echter wenselijk om code te genereren voor alle paden, waarbij u één bibliotheek met aanroepen maakt waarmee u toekomstige oplossingen op kunt baseren. Laten we eens kijken naar het proces voor het genereren van een python-bibliotheek.

U moet Swagger instellen op dezelfde regio als uw spraak service-abonnement. U kunt uw regio bevestigen in het Azure Portal onder uw speech service-resource. Zie [regio's](regions.md)voor een volledige lijst met ondersteunde regio's.

1. Ga in een browser naar de Swagger-specificatie voor uw regio `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0` . Klik op die pagina op **API-definitie**en klik op **Swagger**. Kopieer de URL van de pagina die wordt weer gegeven.
1. Ga in een nieuwe browser naar https://editor.swagger.io
1. Klik op **bestand**, klik op **URL importeren**, plak de URL en klik op **OK**.
1. Klik op **client genereren** en selecteer **python**. De client bibliotheek wordt gedownload naar uw computer.

U kunt de python-bibliotheek gebruiken die u hebt gegenereerd met de [voor beelden van de speech-service op github](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Naslagdocumentatie

* [REST (Swagger): batch transcriptie en-aanpassing](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [REST API: Spraak-naar-tekst](rest-speech-to-text.md)
* [REST API: Tekst-naar-spraak](rest-text-to-speech.md)

## <a name="next-steps"></a>Volgende stappen

* [Speech Service-voor beelden op github](https://aka.ms/csspeech/samples).
* [Verkrijg gratis een spraakserviceabonnementssleutel](overview.md#try-the-speech-service-for-free)
