---
title: 'Quickstart: Synthetiseren spraak, Java (Windows, Linux, macOS) - Spraakservice'
titleSuffix: Azure Cognitive Services
description: In deze quickstart leert u een eenvoudige Java-toepassing te maken die spraak uit tekst vastlegt en synthetiseert en afspeelt met de standaardluidspreker.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: a49fadee598d1fa87c6dddbbf6ad388243ea6fb8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925406"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u:

> [!div class="checklist"]
> * [Een Azure-spraakbron maken](../../../../get-started.md)
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=jre)

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Als u een nieuwe lege klasse wilt toevoegen aan uw Java-project, selecteert u **Bestand** > **Nieuwe** > **klasse**.

1. Voer in het venster **Nieuwe Java-klasse**, in het veld **Pakket**, **speechsdk.quickstart** in en voer in het veld **Naam****Main** in.

   ![Schermopname van het venster Nieuwe Java-klasse](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Vervang alle code in `Main.java` door het volgende codefragment:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/text-to-speech/src/speechsdk/quickstart/Main.java#code)]

1. Vervang de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang de tekenreeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld (bijvoorbeeld `westus` voor het gratis proefabonnement).

1. Sla de wijzigingen in het project op.

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

Druk op F11 of selecteer **Foutopsporing** > **uitvoeren**.
Voer een tekst in wanneer u daarom wordt gevraagd en u hoort de gesynthetiseerde audio die wordt afgespeeld vanaf de standaardluidspreker.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zie ook

- [Een aangepaste stem maken](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Aangepaste spraakvoorbeelden opnemen](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
