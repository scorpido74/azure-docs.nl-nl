---
title: 'Quickstart: Bewaak spraak, unit-Speech-Service'
titleSuffix: Azure Cognitive Services
description: Gebruik deze hand leiding voor het maken van een tekst-naar-spraak-toepassing met Unity en de Speech SDK voor Unity. Wanneer u klaar bent, kunt u spraak van tekst in realtime op de spreker van uw apparaat bekunsten.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 9/19/2019
ms.author: yinhew
ms.openlocfilehash: be5f07b8ea58d0d62c70e0e9dc8ab187ce4a0f63
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803185"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-unity"></a>Quickstart: Stem de spraak met de Speech SDK voor Unity

Quick starts zijn ook beschikbaar voor [spraak herkenning](quickstart-csharp-unity.md).

Gebruik deze hand leiding voor het maken van een tekst-naar-spraak-toepassing met behulp van [Unity](https://unity3d.com/) en de Speech SDK voor Unity.
Wanneer u klaar bent, kunt u spraak van tekst in realtime op de spreker van uw apparaat bekunsten.
Als u niet bekend met Unity bent, is het raadzaam eerst de [gebruikershandleiding van Unity](https://docs.unity3d.com/Manual/UnityManual.html) te bestuderen voordat u begint met de ontwikkeling van uw toepassing.

> [!NOTE]
> Het ondersteunt Windows Desktop (x86 en x64) of Universeel Windows-platform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) en iOS (x64 Simulator, ARM32 en ARM64).

## <a name="prerequisites"></a>Vereisten

Als u dit project wilt voltooien, hebt u het volgende nodig:

* [Eenheid 2018,3 of hoger](https://store.unity.com/) met [Unit 2019,1 toevoegen van ondersteuning voor UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Versie 15,9 of hoger van Visual Studio 2017 is ook acceptabel.
* Voor ondersteuning voor Windows ARM64 installeert u de [optionele build tools voor ARM64 en de Windows 10 SDK voor ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/) 
* Een abonnementssleutel voor de Speech Service. [Gratis downloaden](get-started.md).

## <a name="create-a-unity-project"></a>Een Unity-project maken

* Start Unity, selecteer het tabblad **Projecten** en vervolgens **Nieuw**.
* Geef bij **Projectnaam** **csharp-unity** op, geef bij **Sjabloon** **3D** op en kies een locatie.
  Selecteer vervolgens **Project maken**.
* Na enige tijd wordt het editorvenster van Unity weergegeven.

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* De Speech-SDK voor Unity (bètaversie) wordt geleverd als een Unity-activumpakket (.unitypackage).
  U kunt het [hier](https://aka.ms/csspeech/unitypackage) downloaden.
* Selecteer **Activa** > **Pakket importeren** > **Aangepast pakket** om de Speech-SDK te importeren.
  Raadpleeg de [documentatie voor Unity](https://docs.unity3d.com/Manual/AssetPackages.html) voor meer informatie.
* Selecteer in de bestandenkiezer het Speech-SDK-bestand (.unitypackage) dat u hierboven hebt gedownload.
* Zorg ervoor dat alle bestanden zijn geselecteerd en klik op **Importeren**:

  ![Schermopname van de Unity-editor bij het importeren van het activumpakket voor de Speech-SDK voor Unity](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-ui"></a>Gebruikersinterface toevoegen

We voegen een minimale gebruikers interface toe aan onze scène, bestaande uit een invoer veld voor het invoeren van de tekst voor synthese, een knop voor het activeren van een spraak synthese en een tekst veld om het resultaat weer te geven.

* In [Hierarchy Window](https://docs.unity3d.com/Manual/Hierarchy.html) (standaard aan de linkerkant), wordt een voorbeeldscène weergegeven die in Unity bij het nieuwe project is gemaakt.
* Klik op de knop **maken** boven aan het hiërarchie venster en selecteer **gebruikers interface**- > **invoer veld**.
* Hiermee maakt u drie spel objecten die u kunt zien in het hiërarchie venster: een **invoer veld** object dat is genest binnen een object **canvas** en een **Event System** -object.
* [Navigeer door de scène weergave](https://docs.unity3d.com/Manual/SceneViewNavigation.html) zodat u een goede weer gave hebt van het canvas en het invoer veld in de [scène weergave](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Klik op het object **invoer veld** in het hiërarchie venster om de instellingen weer te geven in het [venster Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standaard aan de rechter kant).
* Stel de eigenschappen **POS X** en **POS Y** in op **0**, zodat het invoer veld midden in het canvas wordt gecentreerd.
* Klik opnieuw op de knop **maken** boven aan het hiërarchie venster en selecteer de knop **UI**@no__t **-2 om** een knop te maken.
* Klik op het object **Button** in het venster Hierarchy om de bijbehorende instellingen weer te geven in het inspectievenster [Inspector Window](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standaard aan de rechterkant).
* Stel de **Eigenschappen POS X** en **POS Y** in op **0** en **-48**en stel de eigenschappen **width** en **Height** in op **160** en **30** om ervoor te zorgen dat de knop en het invoer veld elkaar niet overlappen.
* Klik opnieuw op de knop **maken** boven aan het hiërarchie venster en selecteer de**tekst** van de **gebruikers interface** > om een tekst veld te maken.
* Klik op het object **Text** in het venster Hierarchy om de bijbehorende instellingen weer te geven in het inspectievenster [Inspector Window](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standaard aan de rechterkant).
* Stel de **Eigenschappen POS X** en **POS Y** in op **0** en **80**en stel de eigenschappen **width** en **Height** in op **320** en **80** om ervoor te zorgen dat het tekst veld en het invoer veld elkaar niet overlappen.
* Klik opnieuw op de knop **maken** boven aan het hiërarchie venster en selecteer **Audio** > -**audio bron** om een audio bron te maken.

Als u hiermee klaar bent, ziet de gebruikersinterface er ongeveer als volgt uit:

[![Schermopname van de quickstartgebruikersinterface in de Unity-editor](media/sdk/qs-tts-csharp-unity-ui-inline.png)](media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Klik in het projectvenster [Project Window](https://docs.unity3d.com/Manual/ProjectView.html) (standaard linksonder) op de knop **Create** en selecteer vervolgens  **C# script**. Naam van het script `HelloWorld`.

1. Open het script voor bewerking door erop te dubbelklikken.

   > [!NOTE]
   > U kunt configureren welke code-editor wordt gestart onder **Edit** > **Preferences**. Zie de [gebruikershandleiding van Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Vervang alle code door deze code:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Zoek de teken reeks `YourSubscriptionKey` en vervang deze door uw abonnement sleutel voor spraak Services.

1. Vervang de tekenreeks `YourServiceRegion` door de [regio](regions.md) die aan uw abonnement is gekoppeld. Als u bijvoorbeeld gebruikmaakt van de gratis proefversie, dan is `westus` de regio.

1. Sla het gewijzigde script op.

1. In de Unity-editor moet het script als onderdeel worden toegevoegd aan een van uw game-objecten.

   * Klik op het object **Canvas** in het venster Hierarchy. Hiermee opent u de instelling in het [Inspector-venster](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standaard aan de rechter kant).
   * Klik op de knop **Add Component** in het venster Inspector Window, zoek het HelloWorld-script dat we hierboven hebben gemaakt en voeg het toe.
   * Houd er rekening mee dat het onderdeel Hallo wereld vier niet-geïnitialiseerde eigenschappen, **uitvoer tekst**, **invoer veld**, **knop voor spreken** en **audio bron**heeft die overeenkomen met open bare eigenschappen van de `HelloWorld`-klasse.
     Om deze te koppelen, klikt u op Object Picker (het pictogram van een cirkeltje, rechts van de eigenschap) en kiest u de objecten die u eerder hebt gemaakt: Text en Button.

     > [!NOTE]
     > Het invoer veld en de knop hebben ook een genest object tekst. Zorg ervoor dat u deze niet per ongeluk voor tekst uitvoer hebt gekozen (of wijzig de naam van de tekst objecten in het veld naam in het Inspector-venster om die Verwar ring te voor komen).

## <a name="run-the-application-in-the-unity-editor"></a>De toepassing uitvoeren in de Unity-editor

* Druk op de knop **Play** op de werkbalk van de Unity-editor (onder de menubalk).

* Nadat de app is gestart, voert u tekst in het invoer veld in en klikt u op de knop. Uw tekst wordt verzonden naar de spraak Services en gesynthesizerd op spraak, die op uw spreker wordt afgespeeld.

  [![Schermopname van de actieve quickstart in het Unity Game-venster](media/sdk/qs-tts-csharp-unity-output-inline.png)](media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Controleer het consolevenster [Console Window](https://docs.unity3d.com/Manual/Console.html) op foutopsporingsberichten.

* Wanneer u klaar bent met het maken van de spraak, klikt u op de knop **afspelen** in de werk balk van de eenheids editor om de app te stoppen.

## <a name="additional-options-to-run-this-application"></a>Extra opties voor het uitvoeren van deze toepassing

Deze toepassing kan ook worden geïmplementeerd onder Android, als een zelfstandige Windows-app of UWP-toepassing.
Raadpleeg onze [voorbeeldopslagplaats](https://aka.ms/csspeech/samples) in de map quickstart/csharp-unity, waarin de configuratie voor deze aanvullende doelen wordt beschreven.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zie ook

- [Spraak lettertypen aanpassen](how-to-customize-voice-font.md)
- [Spraak voorbeelden vastleggen](record-custom-voice-samples.md)
