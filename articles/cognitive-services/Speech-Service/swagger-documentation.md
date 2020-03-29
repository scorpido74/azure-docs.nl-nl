---
title: Swagger documentatie - Spraakservice
titleSuffix: Azure Cognitive Services
description: De Swagger-documentatie kan worden gebruikt om SDK's automatisch te genereren voor een aantal programmeertalen. Alle activiteiten in onze dienst worden ondersteund door Swagger
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: fcc43caf895dadfaf832a47c3254f9b828bcb71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77430802"
---
# <a name="swagger-documentation"></a>Documentatie voor Swagger

De Spraakservice biedt een Swagger-specificatie om te communiceren met een handvol REST-API's die worden gebruikt om gegevens te importeren, modellen te maken, de nauwkeurigheid van het model te testen, aangepaste eindpunten te maken, batchtranscripties in de wachtrij te zetten en abonnementen te beheren. De meeste bewerkingen die beschikbaar zijn via de aangepaste spraakportal kunnen programmatisch worden voltooid met behulp van deze API's.

> [!NOTE]
> Zowel spraak-naar-tekst en tekst-naar-spraak operaties worden ondersteund beschikbaar als REST API's, die op hun beurt zijn gedocumenteerd in de Swagger specificatie.

## <a name="generating-code-from-the-swagger-specification"></a>Code genereren van de Swagger-specificatie

De [Swagger specificatie](https://cris.ai/swagger/ui/index) heeft opties waarmee u snel testen voor verschillende paden. Soms is het echter wenselijk om code voor alle paden te genereren, waardoor er één bibliotheek met aanroepen wordt gemaakt waarop toekomstige oplossingen kunnen worden gebaseerd. Laten we eens kijken naar het proces om een Python-bibliotheek te genereren.

U moet Swagger instellen op dezelfde regio als uw spraakserviceabonnement. U uw regio bevestigen in de Azure-portal onder uw spraakservicebron. Zie regio's voor een volledige lijst met ondersteunde [regio's.](regions.md)

1. Ga naar https://editor.swagger.io
2. Klik **op Bestand**en klik vervolgens op **Importeren**
3. Voer de swagger-URL in, inclusief de regio voor uw spraakserviceabonnement`https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Klik **op Client genereren** en selecteer Python
5. De clientbibliotheek opslaan

U de Python-bibliotheek gebruiken die u hebt gegenereerd met de [voorbeelden van spraakserviceop GitHub.](https://aka.ms/csspeech/samples)

## <a name="reference-docs"></a>Referentiedocumenten

* [REST (Swagger): Batch transcriptie en aanpassing](https://westus.cris.ai/swagger/ui/index)
* [REST-API: spraak-naar-tekst](rest-speech-to-text.md)
* [REST API: Tekst-naar-spraak](rest-text-to-speech.md)

## <a name="next-steps"></a>Volgende stappen

* [Spraakservicevoorbeelden op GitHub](https://aka.ms/csspeech/samples).
* [Ontvang gratis een abonnementssleutel voor spraakservice](get-started.md)
