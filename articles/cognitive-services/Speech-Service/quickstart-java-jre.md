---
title: 'Quickstart: Speech herkennen, java (Windows, Linux, macOS)-Speech Service'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start leert u hoe u een eenvoudige Java-toepassing maakt waarmee gebruikers spraak van de microfoon van uw computer worden vastgelegd en getranscribeerd.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 9438cd1345d324b068db8837216afb39241cb2a2
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802463"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>Quickstart: Spraak herkennen met de Speech-SDK voor Java

Quick starts zijn ook beschikbaar voor [spraak-naar-spraak-vertaling](quickstart-translate-speech-java-jre.md), de [eerste virtuele assistent van Voice](quickstart-virtual-assistant-java-jre.md)en [spraak synthese](quickstart-text-to-speech-java-jre.md).

Kies indien gewenst een andere programmeer taal en/of-omgeving:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In dit artikel maakt u een Java-consoletoepassing met behulp van de [Speech-SDK](speech-sdk.md). Doel is om realtime spraak naar tekst om te zetten vanuit de microfoon van uw pc. De toepassing is gebouwd met het Speech SDK maven-pakket en de eclips Java IDE (v 4.8) op 64-bits Windows, 64-bits Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9) of op macOS 10,13 of hoger. De toepassing wordt uitgevoerd op een 64-bit Java 8 runtime-omgeving (JRE).

> [!NOTE]
> Zie [Speech Devices SDK](speech-devices-sdk.md) voor de Speech Devices-SDK en het Roobo-apparaat.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* Besturingssysteem: 64-bits Windows, 64-bits Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9) of macOS 10,13 of hoger
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) of [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Een Azure-abonnementssleutel voor de Spraakservice. [Gratis downloaden](get-started.md).

Als u Linux uitvoert, moet u ervoor zorgen dat deze afhankelijkheden zijn geïnstalleerd voordat u een eclips start.

* Op Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* Op Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Als u Windows (64-bits) gebruikt, moet u ervoor zorgen dat u micro C++ Soft Visual Redistributable hebt geïnstalleerd voor uw platform.
* [Down load micro C++ Soft Visual Redistributable voor Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="create-and-configure-project"></a>Project maken en configureren

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Om een nieuwe lege klasse aan uw Java-project toe te voegen, selecteert u **Bestand** > **Nieuw** > **Klasse**.

1. Voer in het venster **Nieuwe Java-klasse**, in het veld **Pakket**, **speechsdk.quickstart** in en voer in het veld **Naam** **Main** in.

   ![Schermopname van het venster Nieuwe Java-klasse](media/sdk/qs-java-jre-06-create-main-java.png)

1. Vervang alle code in `Main.java` door het volgende codefragment:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Vervang de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang de tekenreeks `YourServiceRegion` door de [regio](regions.md) die aan uw abonnement is gekoppeld (bijvoorbeeld `westus` voor het gratis proefabonnement).

1. Sla de wijzigingen in het project op.

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

Druk op F11 of selecteer **Uitvoeren** > **Fouten opsporen**.
De volgende 15 seconden aan spraakinvoer vanuit uw microfoon worden herkend en geregistreerd in het consolevenster.

![Schermafbeelding van console-uitvoer na geslaagde herkenning](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Volgende stappen

Op GitHub vindt u aanvullende voorbeelden, zoals hoe u spraak kunt lezen vanuit een audiobestand.

> [!div class="nextstepaction"]
> [Bekijk Java-voorbeelden op GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zie ook

- [Snelstart: Vertaal spraak, java (Windows, Linux, macOS) ](quickstart-translate-speech-java-jre.md)
- [Snelstart: Bewaak spraak, java (Windows, Linux, macOS) ](quickstart-text-to-speech-java-jre.md)
- [Akoestische modellen aanpassen](how-to-customize-acoustic-models.md)
- [Taalmodellen aanpassen](how-to-customize-language-model.md)
