---
title: Migreren van Custom Speech Service naar de speech-service
titleSuffix: Azure Cognitive Services
description: De Custom Speech Service maakt nu deel uit van de spraak service. Schakel over naar de speech-service om te profiteren van de nieuwste updates voor kwaliteit en onderdelen.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 9c93286329316d081f8fd99ebd360195931b7b09
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805923"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migreren van de Custom Speech Service naar de speech-service

Gebruik dit artikel om uw toepassingen van de Custom Speech Service naar de speech-service te migreren.

De Custom Speech Service maakt nu deel uit van de spraak service. Schakel over naar de speech-service om te profiteren van de nieuwste updates voor kwaliteit en onderdelen.

## <a name="migration-for-new-customers"></a>Migratie voor nieuwe klanten

Het prijs model is eenvoudiger, met behulp van een op uren gebaseerd prijs model voor de spraak service.  

1. Maak een Azure-resource in elke regio waar uw toepassing beschikbaar is. De naam van de Azure-resource is **spraak**. U kunt een enkel Azure-resource gebruiken voor de volgende services in dezelfde regio bevinden, in plaats van afzonderlijke resources maken:

    * Spraak naar tekst
    * Aangepaste spraak-naar-tekst
    * Tekst naar spraak
    * Spraakomzetting

2. Download de [spraak SDK](speech-sdk.md).

3. Volg de snelstartgidsen en de SDK-voorbeelden voor het gebruik van de juiste API's. Als u de REST API's gebruikt, moet u ook de juiste eindpunten en resource-sleutels gebruiken.

4. Werk de client toepassing bij om de spraak service en Api's te gebruiken.

## <a name="migration-for-existing-customers"></a>Migratie voor bestaande klanten

Migreer uw bestaande bron sleutels naar de speech-service in de Speech Service-Portal. Voer de volgende stappen uit:

> [!NOTE]
> Resource-sleutels kunnen alleen worden gemigreerd binnen dezelfde regio.

1. Aanmelden bij de [cris.ai](https://cris.ai/Home/CustomSpeech) portal en selecteer het abonnement in het menu rechtsboven.

2. Selecteer **geselecteerde abonnement migreren**.

3. De abonnementssleutel invoeren in het tekstvak in en selecteer **migreren**.

## <a name="next-steps"></a>Volgende stappen

* [Probeer de speech-service gratis uit](get-started.md).
* Informatie over [spraak naar tekst](./speech-to-text.md) concepten.

## <a name="see-also"></a>Zie ook

* [Wat is de speech-service](overview.md)
* [Documentatie voor speech-service en spraak SDK](speech-sdk.md#get-the-sdk)
