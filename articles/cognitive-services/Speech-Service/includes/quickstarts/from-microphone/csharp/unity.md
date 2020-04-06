---
title: 'Snelstart: spraak herkennen van een microfoon, C# (Unity)- Spraakservice'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: 6b3c8c62382d548d8a1bc7d5f611cdfbe2fd72fb
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671776"
---
> [!NOTE]
> De SpraakSDK voor Unity ondersteunt Windows Desktop (x86 en x64) of Universal Windows Platform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) en iOS (x64 simulator, ARM32 en ARM64)

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * [Een Azure-spraakbron maken](../../../../get-started.md)
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=unity&pivots=programming-language-csharp)
> * Zorg ervoor dat u toegang hebt tot een microfoon voor audio-opname

Als je dit al gedaan hebt, geweldig. Laten we doorgaan.

## <a name="create-a-unity-project"></a>Een Unity-project maken

1. Open Eenheid. Als u Unity voor de eerste keer gebruikt, wordt het venster **Unity Hub** *<version number>* weergegeven. (U Unity Hub ook rechtstreeks openen om bij dit venster te komen.)

   [![Unity Hub-venster](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Selecteer **Nieuw**. Het venster **Een nieuw project maken met het** *<version number>* venster Eenheid wordt weergegeven.

   [![Een nieuw project maken in Unity Hub](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. Voer **in Projectnaam** **csharp-unity**in .
1. Als 3D nog niet is geselecteerd, selecteert u in **Sjablonen,** als **3D** nog niet is geselecteerd.
1. Selecteer of maak in **Locatie**een map om het project in op te slaan.
1. Selecteer **Maken**.

Na een tijdje wordt het venster Unity Editor weergegeven.



## <a name="add-ui"></a>Gebruikersinterface toevoegen

Laten we nu een minimale gebruikersinterface toevoegen aan onze scène. Deze gebruikersinterface bestaat uit een knop om spraakherkenning te activeren en een tekstveld om het resultaat weer te geven. In het venster [ **Hiërarchie** ](https://docs.unity3d.com/Manual/Hierarchy.html)wordt een voorbeeldscène weergegeven die Unity met het nieuwe project heeft gemaakt.

1. Selecteer us-knop**Gebruikersinterface** >  **maken** > boven aan het **venster Hiërarchie** **.**

   Met deze actie worden drie gameobjecten gemaakt die u zien in het **venster Hiërarchie:** een **object Button,** een **object Canvas** met de knop en een **object EventSystem.**

   [![Unity Editor-omgeving](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Navigeer door de **scèneweergave,** ](https://docs.unity3d.com/Manual/SceneViewNavigation.html) zodat u het canvas en de knop in de [ **scèneweergave** ](https://docs.unity3d.com/Manual/UsingTheSceneView.html)goed bekijken.

1. Stel in het venster [ **Controle** ](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standaard rechts) de eigenschappen **Pos X** en **Pos Y** in op **0,** zodat de knop in het midden van het canvas is gecentreerd.

1. Selecteer **in** het venster Hiërarchie de optie > **UI-tekst** > **Text** **maken**om een **object Text** te maken.

1. Stel in het venster **Controle** de eigenschappen **Pos X** en **Pos Y** in op **0** en **120**en stel de eigenschappen **Breedte** en **Hoogte** in op **240** en **120**. Deze waarden zorgen ervoor dat het tekstveld en de knop elkaar niet overlappen.

Wanneer u klaar bent, moet de **scèneweergave** er hetzelfde uitzien als deze schermafbeelding:

[![Scèneweergave in de Unity Editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

Voer de volgende stappen uit om de voorbeeldscriptcode voor het Unity-project toe te voegen:

1. Selecteer [in](https://docs.unity3d.com/Manual/ProjectView.html)het venster Project de optie**C#-script** **maken** > om een nieuw C#-script toe te voegen.

   [![Projectvenster in de Unity Editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Naam van het script `HelloWorld`.

1. Dubbelklik `HelloWorld` om het nieuw gemaakte script te bewerken.

   > [!NOTE]
   > Als u de codeeditor wilt configureren die door Unity voor bewerking moet worden gebruikt, selecteert u**Voorkeuren** **bewerken** > en gaat u naar de voorkeuren **voor Extern hulpprogramma's.** Zie de [gebruikershandleiding unity](https://docs.unity3d.com/Manual/Preferences.html)voor meer informatie.

1. Vervang het bestaande script door de volgende code:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. Zoek en vervang `YourSubscriptionKey` de tekenreeks door uw abonnementssleutel voor spraakservice.

1. Zoek en vervang `YourServiceRegion` de tekenreeks door de **regio-id** uit [regio](https://aka.ms/speech/sdkregion) die is gekoppeld aan uw abonnement. Als u bijvoorbeeld gebruikmaakt van de gratis proefversie, dan is `westus` de regio.

1. Sla het gewijzigde script op.

Ga nu terug naar de Unity Editor en voeg het script als onderdeel toe aan een van je spelobjecten:

1. Selecteer **in** het venster Hiërarchie het object **Canvas.**

1. Selecteer **in** het venster Controle de knop **Component toevoegen.**

   [![Venster Controle in de Eenheidseditor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. Zoek in de vervolgkeuzelijst `HelloWorld` naar het script dat we hierboven hebben gemaakt en voeg het toe. Er wordt een sectie **Hello World (Script)** weergegeven in het venster **Inspector,** met twee niet-geïninitialiseerde eigenschappen, **Uitvoertekst** en **Knop Reco starten**. Deze eigenschappen van de component `HelloWorld` Unity komen overeen met de openbare eigenschappen van de klasse.

1. Selecteer de objectkiezer van de eigenschap **Reco-knop starten** (het pictogram van de kleine cirkel rechts van de eigenschap) en kies het object **Button** dat u eerder hebt gemaakt.

1. Selecteer de objectkiezer van de eigenschap **Uitvoertekst** en kies het object **Text** dat u eerder hebt gemaakt.

   > [!NOTE]
   > De knop heeft ook een genest Text-object. Zorg ervoor dat u deze niet per ongeluk kiest voor tekstuitvoer (of wijzig de naam van een van de tekstobjecten met het veld **Naam** in het venster **Controle** om verwarring te voorkomen).

## <a name="run-the-application-in-the-unity-editor"></a>De toepassing uitvoeren in de Unity-editor

Nu bent u klaar om de toepassing uit te voeren binnen de Unity Editor.

1. Selecteer op de werkbalk Unity Editor (onder de menubalk) de knop **Afspelen** (een driehoek met rechts aanwijzen).

1. Ga naar [ **de gameweergave** ](https://docs.unity3d.com/Manual/GameView.html)en wacht tot het object **Tekst** de knop Klik weergeeft om spraak **te herkennen.** (Er wordt **nieuwe tekst** weergegeven wanneer de toepassing nog niet is gestart of nog niet klaar is om te reageren.)

1. Selecteer de knop en spreek een Engelse zin of zin in de microfoon van uw computer. Uw spraak wordt verzonden naar de spraakservice en getranscribeerd naar tekst, die wordt weergegeven in de **gameweergave.**

   [![Game view in de Unity Editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Controleer het [ **consolevenster** ](https://docs.unity3d.com/Manual/Console.html) op foutopsporingsberichten. Als het **consolevenster** niet wordt weergegeven, gaat u naar de menubalk en selecteert **u Windows** > **General** > **Console** om het weer te geven.

1. Wanneer u klaar bent met het herkennen van spraak, selecteert u de knop **Afspelen** op de werkbalk Unity Editor om de toepassing te stoppen.

## <a name="additional-options-to-run-this-application"></a>Extra opties voor het uitvoeren van deze toepassing

Deze toepassing kan ook worden geïmplementeerd als een Android-app, een Windows-stand-alone app of een UWP-toepassing.
Zie voor meer informatie onze [voorbeeldopslagplaats.](https://aka.ms/csspeech/samples) De `quickstart/csharp-unity` map beschrijft de configuratie voor deze extra doelen.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](../footer.md)]
