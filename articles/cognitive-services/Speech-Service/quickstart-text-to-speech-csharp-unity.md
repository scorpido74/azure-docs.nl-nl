---
title: 'Snelstartgids: de herkenning van spraak, unit-Speech Service'
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
ms.openlocfilehash: b4d329d9d3c2a259fb90b0278c54ba8590590995
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675489"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-unity"></a>Snelstartgids: de herkenning van spraak met de Speech SDK voor Unity

Quick starts zijn ook beschikbaar voor [spraak herkenning](quickstart-csharp-unity.md).

Gebruik deze hand leiding voor het maken van een tekst-naar-spraak-toepassing met behulp van [Unity](https://unity3d.com/) en de Azure COGNITIVE Services Speech SDK voor Unity.
Wanneer u klaar bent, kunt u spraak van tekst in realtime op de spreker van uw apparaat bewerken.
Als u niet bekend bent met Unity, moet u de [hand leiding voor de Unity-gebruiker](https://docs.unity3d.com/Manual/UnityManual.html) bestuderen voordat u de ontwikkeling van uw toepassing start.

> [!NOTE]
> Unit ondersteunt Windows Desktop (x86 en x64) of de Universeel Windows-platform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) en iOS (x64 Simulator, ARM32 en ARM64).

## <a name="prerequisites"></a>Vereisten

Als u dit project wilt voltooien, hebt u het volgende nodig:

* [Unit 2018,3 of hoger](https://store.unity.com/) met [Unity 2019,1 voegt ondersteuning toe voor UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Versie 15,9 of hoger van Visual Studio 2017 is ook acceptabel.
* Voor ondersteuning voor Windows ARM64 installeert u de [optionele build tools voor ARM64 en de Windows 10 SDK voor ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).
* Een abonnementssleutel voor de Speech Service. [Gratis downloaden](get-started.md).

## <a name="create-a-unity-project"></a>Een Unity-project maken

* Begin eenheid en selecteer op het tabblad **projecten** de optie **Nieuw**.
* Geef **project naam** op als **csharp eenheid** en **sjabloon** als **3D**en kies een locatie.
  Selecteer vervolgens **Project maken**.
* Na enige tijd wordt het editorvenster van Unity weergegeven.

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* De Speech-SDK voor Unity (bètaversie) wordt geleverd als een Unity-activumpakket (.unitypackage). Down load deze van [deze website](https://aka.ms/csspeech/unitypackage).
* Selecteer **Activa** > **Pakket importeren** > **Aangepast pakket** om de Speech-SDK te importeren. Raadpleeg de [documentatie van unititeit](https://docs.unity3d.com/Manual/AssetPackages.html)voor meer informatie.
* Selecteer in de bestands kiezer het Speech SDK. unitypackage-bestand dat u hebt gedownload.
* Zorg ervoor dat alle bestanden zijn geselecteerd en selecteer **importeren**.

  ![Schermopname van de Unity-editor bij het importeren van het activumpakket voor de Speech-SDK voor Unity](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-a-ui"></a>Een gebruikers interface toevoegen

We voegen een minimale gebruikers interface toe aan onze scène die bestaat uit een invoer veld voor het invoeren van de tekst voor synthese, een knop voor het activeren van de spraak synthese en een tekst veld om het resultaat weer te geven.

* In het [hiërarchie venster](https://docs.unity3d.com/Manual/Hierarchy.html) (standaard aan de linkerkant) wordt een voor beeld van een scène weer gegeven dat de eenheid die is gemaakt met het nieuwe project.
* Selecteer de knop **maken** boven aan het **hiërarchie** venster en selecteer  > **invoer veld**voor de **gebruikers interface** .
* Met deze optie maakt u drie spel objecten die u kunt zien in het **hiërarchie** venster: een **invoer veld** object dat is genest binnen een object **canvas** en een **Event System** -object.
* [Navigeer door de scène weergave](https://docs.unity3d.com/Manual/SceneViewNavigation.html) zodat u een goede weer gave hebt van het canvas en het invoer veld in de [scène weergave](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Selecteer in het venster **hiërarchie** het object **invoer veld** om de instellingen weer te geven in het [Inspector-venster](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standaard aan de rechter kant).
* Stel de eigenschappen **POS X** en **POS Y** in op **0** zodat het invoer veld midden in het canvas wordt gecentreerd.
* Selecteer de knop **maken** boven aan het **hiërarchie** venster. Selecteer de**knop** **UI**  >  om een knop te maken.
* Selecteer het **knop** object in het **hiërarchie** venster om de instellingen weer te geven in het [Inspector-venster](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standaard aan de rechter kant).
* Stel de eigenschappen **POS X** en **POS Y** in op **0** en **-48**. Stel de eigenschappen **width** en **Height** in op **160** en **30** om ervoor te zorgen dat de knop en het invoer veld elkaar niet overlappen.
* Selecteer de knop **maken** boven aan het **hiërarchie** venster. Selecteer de  > **tekst** van de **gebruikers interface** om een tekst veld te maken.
* Selecteer het **tekst** object in het **hiërarchie** venster om de instellingen weer te geven in het [Inspector-venster](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standaard aan de rechter kant).
* Stel de eigenschappen **POS X** en **POS Y** in op **0** en **80**. Stel de eigenschappen **width** en **Height** in op **320** en **80** om ervoor te zorgen dat het tekst veld en het invoer veld elkaar niet overlappen.
* Selecteer de knop **maken** boven aan het **hiërarchie** venster. Selecteer **audio**  > **audio bron** om een audio bron te maken.

Wanneer u klaar bent, moet de gebruikers interface er ongeveer als volgt uitzien:

[![Schermopname van de quickstartgebruikersinterface in de Unity-editor](media/sdk/qs-tts-csharp-unity-ui-inline.png)](media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Selecteer in het [venster Project](https://docs.unity3d.com/Manual/ProjectView.html) (standaard aan de linkerkant) de knop **maken** en selecteer  **C# vervolgens script**. Naam van het script `HelloWorld`.

1. Open het script voor bewerking door erop te dubbelklikken.

   > [!NOTE]
   > U kunt configureren welke code-editor wordt gestart door  > **voor keuren** **bewerken** te selecteren. Zie voor meer informatie de [hand leiding voor Unity User](https://docs.unity3d.com/Manual/Preferences.html).

1. Vervang alle code door deze code:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Zoek en vervang de teken reeks `YourSubscriptionKey` door uw abonnements sleutel voor spraak Services.

1. Vervang de tekenreeks `YourServiceRegion` door de [regio](regions.md) die aan uw abonnement is gekoppeld. De regio wordt bijvoorbeeld `westus` als u de gratis proef versie gebruikt.

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

  [![Screenshot van de Snelstartgids die wordt uitgevoerd in het eenheids spel venster](media/sdk/qs-tts-csharp-unity-output-inline.png)](media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Controleer het [console venster](https://docs.unity3d.com/Manual/Console.html) voor fout opsporing van berichten.
* Wanneer u klaar bent met het bekunsten van de spraak, selecteert u de knop **afspelen** in de werk balk eenheids editor om de app te stoppen.

## <a name="additional-options-to-run-this-application"></a>Extra opties voor het uitvoeren van deze toepassing

Deze toepassing kan ook worden geïmplementeerd in Android als een zelfstandige Windows-app of een UWP-toepassing.
Zie de voor [beeld-opslag plaats](https://aka.ms/csspeech/samples) in de map Quick Start/csharp-unit die de configuratie voor deze aanvullende doelen beschrijft.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zie ook

- [Spraak lettertypen aanpassen](how-to-customize-voice-font.md)
- [Spraak voorbeelden vastleggen](record-custom-voice-samples.md)
