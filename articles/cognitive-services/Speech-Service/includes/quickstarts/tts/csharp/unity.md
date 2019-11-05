---
title: 'Snelstartgids: een synthese van de C# spraak, (Unit)-spraak service'
titleSuffix: Azure Cognitive Services
description: Gebruik deze hand leiding voor het maken van een tekst-naar-spraak-toepassing met Unity en de Speech SDK voor Unity. Wanneer u klaar bent, kunt u spraak van tekst in realtime op de spreker van uw apparaat bekunsten.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 9/19/2019
ms.author: yinhew
ms.openlocfilehash: 281fbcf3f42160b18adcad3f06f6ef4a7b8de243
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502924"
---
> [!NOTE]
> Unit ondersteunt Windows Desktop (x86 en x64) of de Universeel Windows-platform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) en iOS (x64 Simulator, ARM32 en ARM64).

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Uw ontwikkel omgeving instellen](../../../../quickstarts/setup-platform.md?tabs=unity)
> * [Een leeg voorbeeld project maken](../../../../quickstarts/create-project.md?tabs=unity)

## <a name="add-a-ui"></a>Een gebruikers interface toevoegen

We voegen een minimale gebruikers interface toe aan onze scène die bestaat uit een invoer veld voor het invoeren van de tekst voor synthese, een knop voor het activeren van de spraak synthese en een tekst veld om het resultaat weer te geven.

* In het [hiërarchie venster](https://docs.unity3d.com/Manual/Hierarchy.html) (standaard aan de linkerkant) wordt een voor beeld van een scène weer gegeven dat de eenheid die is gemaakt met het nieuwe project.
* Selecteer de knop **maken** boven aan het **hiërarchie** venster en selecteer > **invoer veld**voor de **gebruikers interface** .
* Met deze optie maakt u drie spel objecten die u kunt zien in het **hiërarchie** venster: een **invoer veld** object dat is genest binnen een object **canvas** en een **Event System** -object.
* [Navigeer door de scène weergave](https://docs.unity3d.com/Manual/SceneViewNavigation.html) zodat u een goede weer gave hebt van het canvas en het invoer veld in de [scène weergave](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Selecteer in het venster **hiërarchie** het object **invoer veld** om de instellingen weer te geven in het [Inspector-venster](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standaard aan de rechter kant).
* Stel de eigenschappen **POS X** en **POS Y** in op **0** zodat het invoer veld midden in het canvas wordt gecentreerd.
* Selecteer de knop **maken** boven aan het **hiërarchie** venster. Selecteer de **knop** **UI** > om een knop te maken.
* Selecteer het **knop** object in het **hiërarchie** venster om de instellingen weer te geven in het [Inspector-venster](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standaard aan de rechter kant).
* Stel de eigenschappen **POS X** en **POS Y** in op **0** en **-48**. Stel de eigenschappen **width** en **Height** in op **160** en **30** om ervoor te zorgen dat de knop en het invoer veld elkaar niet overlappen.
* Selecteer de knop **maken** boven aan het **hiërarchie** venster. Selecteer de > **tekst** van de **gebruikers interface** om een tekst veld te maken.
* Selecteer het **tekst** object in het **hiërarchie** venster om de instellingen weer te geven in het [Inspector-venster](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standaard aan de rechter kant).
* Stel de eigenschappen **POS X** en **POS Y** in op **0** en **80**. Stel de eigenschappen **width** en **Height** in op **320** en **80** om ervoor te zorgen dat het tekst veld en het invoer veld elkaar niet overlappen.
* Selecteer de knop **maken** boven aan het **hiërarchie** venster. Selecteer **audio** > **audio bron** om een audio bron te maken.

Wanneer u klaar bent, moet de gebruikers interface er ongeveer als volgt uitzien:

[![Schermopname van de quickstartgebruikersinterface in de Unity-editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Selecteer in het [venster Project](https://docs.unity3d.com/Manual/ProjectView.html) (standaard aan de linkerkant) de knop **maken** en selecteer  **C# vervolgens script**. Naam van het script `HelloWorld`.

1. Open het script voor bewerking door erop te dubbelklikken.

   > [!NOTE]
   > U kunt configureren welke code-editor wordt gestart door > **voor keuren** **bewerken** te selecteren. Zie voor meer informatie de [hand leiding voor Unity User](https://docs.unity3d.com/Manual/Preferences.html).

1. Vervang alle code door deze code:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/text-to-speech/Assets/Scripts/HelloWorld.cs#code)]

1. Zoek en vervang de teken reeks `YourSubscriptionKey` door uw abonnements sleutel voor spraak Services.

1. Vervang de tekenreeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld. De regio wordt bijvoorbeeld `westus` als u de gratis proef versie gebruikt.

1. Sla het gewijzigde script op.

1. Voeg het script als een onderdeel toe aan een van uw spel objecten in de Unity-editor.

   * Selecteer in het venster **hiërarchie** het object **canvas** om de instelling in het [venster controle](https://docs.unity3d.com/Manual/UsingTheInspector.html) te openen (standaard aan de rechter kant).
   * Selecteer de knop **onderdeel toevoegen** in het **Inspector** -venster. Zoek vervolgens het `HelloWorld` script dat we eerder hebben gemaakt en voeg het toe.
   * Het onderdeel HelloWorld heeft vier niet-geïnitialiseerde eigenschappen, **uitvoer tekst**, **invoer veld**, **spraak knop** en **audio bron**die overeenkomen met open bare eigenschappen van de klasse `HelloWorld`.
     Als u deze wilt verkabelen, selecteert u de object kiezer (het pictogram van de kleine cirkel rechts van de eigenschap). Selecteer de tekst-en knop objecten die u eerder hebt gemaakt.

     > [!NOTE]
     > Het invoer veld en de knop hebben ook een genest object tekst. Zorg ervoor dat u deze niet per ongeluk hebt gekozen voor tekst uitvoer. Of u kunt de naam wijzigen van de tekst objecten die gebruikmaken van het veld **naam** in het venster **Inspector** om die Verwar ring te voor komen.

## <a name="run-the-application-in-the-unity-editor"></a>De toepassing uitvoeren in de Unity-editor

* Selecteer de knop **afspelen** in de werk balk eenheids editor onder de menu balk.
* Nadat de app is gestart, voert u tekst in het invoer veld in en selecteert u de knop. Uw tekst wordt verzonden naar de speech-service en gesynthesizerd op spraak, die op uw spreker wordt afgespeeld.

  [Scherm afbeelding van de actieve Quick Start in het eenheids spel venster ![](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-inline.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Controleer het [console venster](https://docs.unity3d.com/Manual/Console.html) voor fout opsporing van berichten.

## <a name="additional-options-to-run-this-application"></a>Extra opties voor het uitvoeren van deze toepassing

Deze toepassing kan ook worden geïmplementeerd in Android als een zelfstandige Windows-app of een UWP-toepassing.
Zie de voor [beeld-opslag plaats](https://aka.ms/csspeech/samples) in de map Quick Start/csharp-unit die de configuratie voor deze aanvullende doelen beschrijft.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zie ook

- [Een aangepaste stem maken](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Aangepaste spraak voorbeelden vastleggen](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
