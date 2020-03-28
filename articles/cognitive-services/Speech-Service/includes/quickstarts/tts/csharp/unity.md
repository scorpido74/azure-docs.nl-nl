---
title: 'Snelstart: Spraak synthetiseren, C# (Unity) - Spraakservice'
titleSuffix: Azure Cognitive Services
description: Gebruik deze handleiding om een tekst-naar-spraak-toepassing te maken met Unity en de Speech SDK for Unity. Wanneer u klaar bent, u spraak uit tekst in realtime synthetiseren naar de luidspreker van uw apparaat.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 9/19/2019
ms.author: yinhew
ms.openlocfilehash: 73a4477547c562a9960fe72b352e906676eadae5
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925194"
---
> [!NOTE]
> Unity ondersteunt Windows Desktop (x86 en x64) of het Universal Windows Platform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) en iOS (x64 simulator, ARM32 en ARM64).

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u:

> [!div class="checklist"]
> * [Een Azure-spraakbron maken](../../../../get-started.md)
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=unity)

## <a name="add-a-ui"></a>Een gebruikersinterface toevoegen

We voegen een minimale gebruikersinterface toe aan onze scène die bestaat uit een invoerveld om de tekst in te voeren voor synthese, een knop om spraaksynthese te activeren en een tekstveld om het resultaat weer te geven.

* In het [venster Hiërarchie](https://docs.unity3d.com/Manual/Hierarchy.html) (standaard aan de linkerkant) wordt een voorbeeldscène weergegeven die Unity met het nieuwe project heeft gemaakt.
* Selecteer de knop **Maken** boven aan het **venster Hiërarchie** en selecteer**UI-invoerveld** **UI** > .
* Met deze optie worden drie gameobjecten gemaakt die u zien in het **venster Hiërarchie:** een **object Invoerveld** dat is genest in een **object Canvas** en een **object EventSystem.**
* [Navigeer door de scèneweergave,](https://docs.unity3d.com/Manual/SceneViewNavigation.html) zodat u een goed beeld hebt van het canvas en het invoerveld in de [scèneweergave](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Selecteer het object **Invoerveld** in het **venster Hiërarchie** om de instellingen weer te geven in het [venster Controle](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standaard aan de rechterkant).
* Stel de eigenschappen **Pos X** en **Pos Y** in op **0,** zodat het invoerveld in het midden van het canvas is gecentreerd.
* Selecteer opnieuw de knop **Maken** boven aan het **venster Hiërarchie.** Selecteer **Gebruikersinterfaceknop** > **Button** om een knop te maken.
* Selecteer het object **Button** in het **venster Hiërarchie** om de instellingen weer te geven in het [venster Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standaard aan de rechterkant).
* Stel de eigenschappen **Pos X** en **Pos Y** in op **0** en **-48**. Stel de eigenschappen **Breedte** en **Hoogte** in op **160** en **30** om ervoor te zorgen dat de knop en het invoerveld elkaar niet overlappen.
* Selecteer opnieuw de knop **Maken** boven aan het **venster Hiërarchie.** Selecteer **UI-tekst** > **Text** om een tekstveld te maken.
* Selecteer het object **Tekst** in het **venster Hiërarchie** om de instellingen weer te geven in het [venster Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standaard aan de rechterkant).
* Stel de eigenschappen **Pos X** en **Pos Y** in op **0** en **80**. Stel de eigenschappen **Breedte** en **Hoogte** in op **320** en **80** om ervoor te zorgen dat het tekstveld en het invoerveld elkaar niet overlappen.
* Selecteer opnieuw de knop **Maken** boven aan het **venster Hiërarchie.** Selecteer > **Audioaudiobron** om een audiobron te maken. **Audio**

Wanneer u klaar bent, moet de gebruikersinterface er hetzelfde uitzien als deze schermafbeelding:

[![Schermafbeelding van de gebruikersinterface voor snelstart in de Unity Editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Selecteer in het [venster Project](https://docs.unity3d.com/Manual/ProjectView.html) (standaard links onder) de knop **Maken** en selecteer **Vervolgens C#-script**. Naam van het script `HelloWorld`.

1. Open het script voor bewerking door erop te dubbelklikken.

   > [!NOTE]
   > U configureren welke codeeditor wordt gestart door Voorkeuren **bewerken** > **te**selecteren. Zie de [gebruikershandleiding unity](https://docs.unity3d.com/Manual/Preferences.html)voor meer informatie.

1. Vervang alle code door deze code:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/text-to-speech/Assets/Scripts/HelloWorld.cs#code)]

1. Lokaliseer `YourSubscriptionKey` en vervang de tekenreeks door uw abonnementssleutel voor spraakservice.

1. Vervang de tekenreeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld. Bijvoorbeeld, de regio `westus` is als u de gratis proefperiode.

1. Sla het gewijzigde script op.

1. Terug in de Unity Editor voegt u het script als onderdeel toe aan een van uw spelobjecten.

   * Selecteer het object **Canvas** in het **venster Hiërarchie** om de instelling in het [venster Controle](https://docs.unity3d.com/Manual/UsingTheInspector.html) te openen (standaard aan de rechterkant).
   * Selecteer de knop **Component toevoegen** in het venster **Controle.** Zoek vervolgens `HelloWorld` naar het script dat we eerder hebben gemaakt en voeg het toe.
   * De component HelloWorld heeft vier niet-geïninitialiseerde **eigenschappen, Uitvoertekst,** **Invoerveld,** **Spreekknop** en **Audiobron,** die overeenkomen met de openbare eigenschappen van de `HelloWorld` klasse.
     Als u ze wilt vastmaken, selecteert u de objectkiezer (het pictogram met de kleine cirkel rechts van de eigenschap). Selecteer de tekst- en knopobjecten die u eerder hebt gemaakt.

     > [!NOTE]
     > Het invoerveld en de knop hebben ook een genest tekstobject. Zorg ervoor dat u het niet per ongeluk kiest voor tekstuitvoer. U ook de naam van de tekstobjecten wijzigen die het veld **Naam** in het venster **Controle** gebruiken om die verwarring te voorkomen.

## <a name="run-the-application-in-the-unity-editor"></a>De toepassing uitvoeren in de Unity-editor

* Selecteer de knop **Afspelen** op de werkbalk Unity Editor onder de menubalk.
* Nadat de app is gestart, voert u tekst in het invoerveld in en selecteert u de knop. Uw tekst wordt verzonden naar de spraakservice en gesynthetiseerd naar spraak, die wordt afgespeeld op uw luidspreker.

  [![Schermafbeelding van het snel uitvoeren van de start in het venster Eenheidsspel](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-inline.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Controleer het [consolevenster](https://docs.unity3d.com/Manual/Console.html) op foutopsporingsberichten.

## <a name="additional-options-to-run-this-application"></a>Extra opties voor het uitvoeren van deze toepassing

Deze toepassing kan ook worden geïmplementeerd op Android als een Windows-stand-alone app of een UWP-toepassing.
Bekijk de [voorbeeldopslagplaats](https://aka.ms/csspeech/samples) in de map quickstart/csharp-unity waarin de configuratie voor deze extra doelen wordt beschreven.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zie ook

- [Een aangepaste stem maken](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Aangepaste spraakvoorbeelden opnemen](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
