---
title: 'Quickstart: Vertaal spraak, java (Windows, Linux)-Speech Service'
titleSuffix: Azure Cognitive Services
description: In deze quickstart maakt u een eenvoudige Java-toepassing om spraak van gebruikers vast te leggen, deze om te zetten in een andere taal en de tekst uit te voeren naar de opdrachtregel. Deze handleiding is bedoeld voor Windows- en Linux-gebruikers.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 402bf198981641db74a86bbc420fab83124f0b47
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802016"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>Quickstart: Omzetten van spraak met de spraak-SDK voor Java

Quick starts zijn ook beschikbaar voor de virtuele assistent van [spraak naar tekst](quickstart-java-jre.md), [tekst naar spraak](quickstart-text-to-speech-java-jre.md)en spraak [-eerste](quickstart-virtual-assistant-java-jre.md).

In deze quickstart maakt u een eenvoudige Java-toepassing waarmee spraak van gebruikers via de microfoon van de computer wordt vastgelegd, waarna de spraak wordt omgezet en de omgezette tekst in realtime wordt getranscribeerd naar de opdrachtregel. Deze toepassing is ontworpen om te worden uitgevoerd op 64-bits Windows-of 64-bits Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9) of op macOS 10,13 of hoger. Het is gebouwd met het Speech SDK maven-pakket en de eclips Java IDE.

Zie [Taalondersteuning](language-support.md) voor een volledige lijst met talen die voor spraakomzetting beschikbaar zijn.

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

> [!NOTE]
> Zie [Speech Devices SDK](speech-devices-sdk.md) voor de Speech Devices-SDK en het Roobo-apparaat.

## <a name="create-and-configure-project"></a>Project maken en configureren

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Om een nieuwe lege klasse aan uw Java-project toe te voegen, selecteert u **Bestand** > **Nieuw** > **Klasse**.

1. Voer in het venster **Nieuwe Java-klasse**, in het veld **Pakket**, **speechsdk.quickstart** in en voer in het veld **Naam** **Main** in.

   ![Schermopname van het venster Nieuwe Java-klasse](media/sdk/qs-java-jre-06-create-main-java.png)

1. Vervang alle code in `Main.java` door het volgende codefragment:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Vervang de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang de tekenreeks `YourServiceRegion` door de [regio](regions.md) die aan uw abonnement is gekoppeld (bijvoorbeeld `westus` voor het gratis proefabonnement).

1. Sla de wijzigingen in het project op.

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

Druk op F11 of selecteer **Uitvoeren** > **Fouten opsporen**.

De spraakinvoer van de microfoon wordt herkend, getranscribeerd naar het Duits en weergegeven in het consolevenster. Druk op Enter om te stoppen met het vastleggen van spraak.

![Schermafbeelding van console-uitvoer na geslaagde herkenning](media/sdk/qs-translate-java-jre-output.png)

## <a name="next-steps"></a>Volgende stappen

Op GitHub vindt u aanvullende voorbeelden, zoals hoe u spraak kunt lezen vanuit een audiobestand en omgezette tekst als kunstmatige spraak kunt uitvoeren.

> [!div class="nextstepaction"]
> [Bekijk Java-voorbeelden op GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zie ook

- [Snelstart: Spraak herkennen, Java (Windows, Linux)](quickstart-java-jre.md)
- [Akoestische modellen aanpassen](how-to-customize-acoustic-models.md)
- [Taalmodellen aanpassen](how-to-customize-language-model.md)
