---
title: 'Quickstart: Spraak herkennen vanaf een microfoon, C# (Unity) - Speech-service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.custom: devx-track-csharp
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: 8307cfee90fdf65d119bbc1e55eabb0533a4a6ad
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926408"
---
> [!NOTE]
> De Speech SDK voor Unity ondersteunt Windows Desktop (x86 en x64) of Universeel Windows-platform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) en iOS (x64-simulator, ARM32 en ARM64)

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * [Een resource voor de Azure Speech-service maken](../../../../get-started.md)
> * [Uw ontwikkelomgeving instellen en een leeg project maken](../../../../quickstarts/setup-platform.md?tabs=unity&pivots=programming-language-csharp)
> * Zorg ervoor dat u toegang tot een microfoon hebt voor het vastleggen van audio

Als u dat al hebt gedaan: geweldig. Laten we doorgaan.

## <a name="create-a-unity-project"></a>Een Unity-project maken

1. Open Unity. Als u Unity voor het eerst gebruikt, wordt het venster **Unity Hub** *<version number>* weergegeven. (U kunt Unity Hub ook rechtstreeks openen om bij dit venster te komen.)

   [![Het venster Unity Hub](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Selecteer **Nieuw**. Het venster **Een nieuw project maken met Unity** *<version number>* wordt weergegeven.

   [![Een nieuw project maken in Unity Hub](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. Voer bij **Projectnaam** de tekst **csharp-unity** in.
1. Selecteer bij **Sjablonen** het selectievakje **3D** als dat nog niet is geselecteerd.
1. Selecteer of maak bij **Locatie** een map om het project in op te slaan.
1. Selecteer **Maken**.

Na enige tijd wordt het venster Unity Editor weergegeven.



## <a name="add-ui"></a>Gebruikersinterface toevoegen

Nu voegen we een minimale gebruikersinterface toe aan onze scène. Deze gebruikersinterface bestaat uit een knop om spraakherkenning te activeren en een tekstveld om het resultaat weer te geven. In het [venster **Hierarchy**](https://docs.unity3d.com/Manual/Hierarchy.html) wordt een voorbeeldscène weergegeven die in Unity bij het nieuwe project is gemaakt.

1. Selecteer bovenaan het venster **Hierarchy** de **knop** **Create** > **UI** > .

   Met deze actie maakt u drie game-objecten die u kunt zien in het venster **Hierarchy**: het object **Button**, het object **Canvas** dat de knop bevat, en het object **EventSystem**.

   [![Omgeving van Unity Editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Navigeer in de weergave **Scene**](https://docs.unity3d.com/Manual/SceneViewNavigation.html), zodat u het canvas en de knop in de [weergave **Scene**](https://docs.unity3d.com/Manual/UsingTheSceneView.html) goed kunt zien.

1. Stel in het [venster **Inspector**](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standaard aan de rechterkant) de eigenschappen **Pos X** en **Pos Y** in op **0**, zodat de knop midden in het canvas wordt gecentreerd.

1. Selecteer in het venster **Hierarchy** de optie **Create** > **UI** > **Text** om een **Text**-object te maken.

1. Stel in het venster **Inspector** de eigenschappen **Pos X** en **Pos Y** in op **0** en **120** en stel de eigenschappen **Width** en **Height** in op **240** en **120**. Met deze waarden zorgt u ervoor dat het tekstveld en de knop elkaar niet overlappen.

Als u klaar bent, ziet de weergave **Scene** er ongeveer uit als in de volgende schermopname:

[![De weergave Scene in de Unity Editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

Voer de volgende stappen uit om de voorbeeldscriptcode voor het Unity-project toe te voegen:

1. Selecteer in het venster [Project](https://docs.unity3d.com/Manual/ProjectView.html) de optie **Create** > **C# script** om een nieuw C#-script toe te voegen.

   [![Het venster Project in de Unity Editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Naam van het script `HelloWorld`.

1. Dubbelklik op `HelloWorld` om het zojuist gemaakte script te bewerken.

   > [!NOTE]
   > Als u de code-editor wilt configureren voor bewerking via Unity, selecteert u **Edit** > **Preferences** en gaat u vervolgens naar de voorkeuren voor **External Tools**. Raadpleeg de [Unity User Manual](https://docs.unity3d.com/Manual/Preferences.html) voor meer informatie.

1. Vervang het bestaande script door de volgende code:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. Zoek de tekenreeks `YourSubscriptionKey` en vervang deze door de abonnementssleutel van Speech-service.

1. Zoek ook de tekenreeks `YourServiceRegion` en vervang deze door de **Regio-id** van de [regio](https://aka.ms/speech/sdkregion) die aan uw abonnement is gekoppeld.

1. Sla het gewijzigde script op.

Keer nu terug naar de Unity Editor en voeg het script als een onderdeel toe aan een van uw game-objecten:

1. Selecteer in het venster **Hierarchy** de optie **Canvas**.

1. Selecteer in het venster **Inspector** de knop **Add Component**.

   [![Het venster Inspector van de Unity Editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. Zoek in de vervolgkeuzelijst naar het `HelloWorld`-script dat we hierboven hebben gemaakt en voeg dit toe. De sectie **Hello World (script)** wordt weergegeven in het venster **Inspector**, met daarin twee niet-geïnitialiseerde eigenschappen, **Output Text** en **Start Reco Button**. Deze onderdeeleigenschappen van Unity komen overeen met de openbare eigenschappen van de klasse `HelloWorld`.

1. Selecteer de objectkiezer (het pictogram van een cirkeltje, rechts van de eigenschap) van de eigenschap **Start Reco Button** en kies het object **Button** dat u eerder hebt gemaakt.

1. Selecteer de objectkiezer van de eigenschap **Output Text** en kies het object **Text** dat u eerder hebt gemaakt.

   > [!NOTE]
   > De knop heeft ook een genest Text-object. Zorg ervoor dat u dit niet per ongeluk kiest als tekstuitvoer (of wijzig de naam van een van de Text-objecten met behulp van het veld **Name** in het venster **Inspector** om verwarring te voorkomen).

## <a name="run-the-application-in-the-unity-editor"></a>De toepassing uitvoeren in de Unity-editor

Nu kunt u de toepassing uitvoeren in de Unity Editor.

1. Druk op de knop **Play** (een naar rechts wijzende driehoek) op de werkbalk van de Unity Editor (onder de menubalk).

1. Ga naar de [weergave **Game**](https://docs.unity3d.com/Manual/GameView.html) en wacht tot in het object **Text** de tekst **Click button to recognize speech** wordt weergegeven. (Er wordt **New Text** weergegeven als de toepassing niet is gestart of niet gereed is om te reageren.)

1. Selecteer de knop en zegt u iets in het Engels in de microfoon van uw computer. Uw gesproken tekst wordt verzonden naar de Speech-service en getranscribeerd naar tekst, die in de weergave **Game** wordt weergegeven.

   [![De weergave Game in de Unity Editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Controleer het [venster **Console**](https://docs.unity3d.com/Manual/Console.html) op foutopsporingsberichten. Als het venster **Console** niet wordt weergegeven, gaat u naar de menubalk en selecteert u **Window** > **General** > **Console** om het weer te geven.

1. Als u klaar bent met de spraakherkenning, selecteert u de knop **Play** op de werkbalk van de Unity Editor om de toepassing te stoppen.

## <a name="additional-options-to-run-this-application"></a>Extra opties voor het uitvoeren van deze toepassing

Deze toepassing kan ook worden geïmplementeerd als een Android-app, als een zelfstandige Windows-app of als een UWP-toepassing.
Zie onze [voorbeeldopslagplaats](https://aka.ms/csspeech/samples) voor meer informatie. In de map `quickstart/csharp-unity` wordt de configuratie voor deze aanvullende doelen beschreven.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

