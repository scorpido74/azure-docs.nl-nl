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
ms.openlocfilehash: dd9ac9654f916653af974e816485630423466ae5
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815438"
---
# <a name="swagger-documentation"></a>Documentatie voor Swagger

De speech-service biedt een Swagger-specificatie om te communiceren met een aantal REST-Api's die worden gebruikt voor het importeren van gegevens, het maken van modellen, het testen van model nauwkeurigheid, het maken van aangepaste eind punten, het in de wachtrij plaatsen van batch transcripties en het beheren van abonnementen. De meeste bewerkingen die beschikbaar zijn via de Custom Speech Portal, kunnen via een programma worden voltooid met behulp van deze Api's.

> [!NOTE]
> Bewerkingen voor spraak naar tekst en tekst naar spraak worden ondersteund als REST Api's, die op zijn beurt worden beschreven in de Swagger-specificatie.

## <a name="generating-code-from-the-swagger-specification"></a>Code genereren op basis van de Swagger-specificatie

De [Swagger-specificatie](https://cris.ai/swagger/ui/index) bevat opties waarmee u snel kunt testen op verschillende paden. Soms is het echter wenselijk om code te genereren voor alle paden, waarbij u één bibliotheek met aanroepen maakt waarmee u toekomstige oplossingen op kunt baseren. Laten we eens kijken naar het proces voor het genereren van een python-bibliotheek.

U moet Swagger instellen op dezelfde regio als uw spraak service-abonnement. U kunt uw regio bevestigen in het Azure Portal onder uw speech service-resource. Zie [regio's](regions.md)voor een volledige lijst met ondersteunde regio's.

1. Ga naar https://editor.swagger.io
2. Klik op **bestand**en vervolgens op **importeren** .
3. Voer de URL voor Swagger in, met inbegrip van de regio voor uw abonnement op spraak Services `https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Klik op **client genereren** en selecteer python
5. Sla de client bibliotheek op

U kunt de python-bibliotheek gebruiken die u hebt gegenereerd met de [voor beelden van de speech-service op github](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Referentiedocumenten

* [REST (Swagger): batch transcriptie en-aanpassing](https://westus.cris.ai/swagger/ui/index)
* [REST API: spraak naar tekst](rest-speech-to-text.md)
* [REST API: tekst-naar-spraak](rest-text-to-speech.md)

## <a name="next-steps"></a>Volgende stappen

* [Speech Service-voor beelden op github](https://aka.ms/csspeech/samples).
* [Gratis een abonnements sleutel voor een spraak service ophalen](get-started.md)
