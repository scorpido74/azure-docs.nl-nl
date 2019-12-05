---
title: 'Quick Start: spraak herkennen vanuit een microfoon C# , (Unit)-spraak service'
titleSuffix: Azure Cognitive Services
description: Nader te bepalen
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: b0a9eddf147eedfa972e0574e62c9e7c2eace84f
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818719"
---
> [!NOTE]
> De Speech SDK voor unit ondersteunt Windows Desktop (x86 en x64) of Universeel Windows-platform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) en iOS (x64 Simulator, ARM32 en ARM64)

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Uw ontwikkel omgeving instellen](../../../../quickstarts/setup-platform.md?tabs=unity)
> * [Een leeg voorbeeld project maken](../../../../quickstarts/create-project.md?tabs=unity)
> * Zorg ervoor dat u toegang tot een microfoon hebt voor het vastleggen van audio

Als u dit al hebt gedaan, is dit geweldig. Laten we verder gaan.

## <a name="create-a-unity-project"></a>Een Unity-project maken

1. Eenheid openen. Als u unit voor de eerste keer gebruikt, wordt het venster **eenheids naaf** *<version number>* weer gegeven. (U kunt unit hub ook rechtstreeks openen om dit venster weer te geven.)

   [venster van de hub ![eenheid](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Selecteer **Nieuw**. Het venster **een nieuw project maken met unity** *<version number>* wordt weer gegeven.

   [![een nieuw project maken in Unity-hub](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. Voer in **project naam** **csharp eenheid**in.
1. In **sjablonen**, als **3D** niet al is geselecteerd, selecteert u deze.
1. In **locatie**selecteert of maakt u een map waarin u het project wilt opslaan.
1. Selecteer **Maken**.

Na een beetje tijd wordt het venster Unity editor weer gegeven.



## <a name="add-ui"></a>Gebruikersinterface toevoegen

We gaan nu een minimale gebruikers interface toevoegen aan onze scène. Deze gebruikers interface bestaat uit een knop voor het activeren van spraak herkenning en een tekst veld om het resultaat weer te geven. In het [ **hiërarchie** venster](https://docs.unity3d.com/Manual/Hierarchy.html)wordt een voor beeld weer gegeven van een scène die is gemaakt met het nieuwe project.

1. Selecteer boven in het **hiërarchie** venster de **knop** > **gebruikers interface** **maken** > .

   Met deze actie maakt u drie spel objecten die u kunt zien in het **hiërarchie** venster: een **knop** object, een **canvas** object dat de knop bevat en een **Event System** -object.

   [omgeving voor eenheids editor ![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Navigeer door de **scène** weergave](https://docs.unity3d.com/Manual/SceneViewNavigation.html) zodat u een goede weer gave hebt van het canvas en de knop in de [ **scène** weergave](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. Stel in [het venster **Inspector** ](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standaard aan de rechter kant) de eigenschappen **POS X** en **POS Y** in op **0**, zodat de knop in het midden van het canvas wordt gecentreerd.

1. Selecteer in het venster **hiërarchie** de optie > **gebruikers interface** **maken** > **tekst** om een **tekst** object te maken.

1. Stel in het venster **Inspector** de eigenschappen **POS X** en **POS Y** in op **0** en **120**en stel de eigenschappen **width** en **Height** in op **240** en **120**. Deze waarden zorgen ervoor dat het tekst veld en de knop elkaar niet overlappen.

Wanneer u klaar bent, moet de **scène** weergave er ongeveer als volgt uitzien:

[de scène weergave ![in de eenheids editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

Voer de volgende stappen uit om de voorbeeld script code voor het Unity-project toe te voegen:

1. Selecteer in het [venster Project](https://docs.unity3d.com/Manual/ProjectView.html)de optie >  **C# script** **maken** om een nieuw C# script toe te voegen.

   [![Project venster in de eenheids editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Naam van het script `HelloWorld`.

1. Dubbel klik op `HelloWorld` om het zojuist gemaakte script te bewerken.

   > [!NOTE]
   > Als u de code-editor wilt configureren voor gebruik door eenheid voor bewerken, selecteert u > **voor keuren** **bewerken** en gaat u naar de voor keuren voor **externe hulpprogram ma's** . Zie voor meer informatie de [hand leiding voor Unity User](https://docs.unity3d.com/Manual/Preferences.html).

1. Vervang het bestaande script door de volgende code:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. Zoek en vervang de teken reeks `YourSubscriptionKey` met de abonnements sleutel van uw speech-service.

1. Zoek en vervang de teken reeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld. Als u bijvoorbeeld gebruikmaakt van de gratis proefversie, dan is `westus` de regio.

1. Sla het gewijzigde script op.

Ga nu terug naar de eenheids editor en voeg het script als een onderdeel toe aan een van de spel objecten:

1. Selecteer in het venster **hiërarchie** het object **canvas** .

1. Selecteer de knop **onderdeel toevoegen** in het **item** venster.

   [![Inspector-venster in de eenheids editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. Zoek in de vervolg keuzelijst het `HelloWorld` script dat hierboven is gemaakt en voeg dit toe. Een **Hallo wereld sectie (script)** wordt weer gegeven in het **Inspector** -venster, met daarin twee niet-geïnitialiseerde eigenschappen, **uitvoer tekst** en de **knop & herstelpuntstatus starten**. De eigenschappen van de Unity-component komen overeen met de open bare eigenschappen van de klasse `HelloWorld`.

1. Selecteer de object kiezer van de eigenschap **Start & herstelpuntstatus** (het kleine cirkel pictogram rechts van de eigenschap) en kies het **knop** object dat u eerder hebt gemaakt.

1. Selecteer de object kiezer van de eigenschap **uitvoer tekst** en kies het **tekst** object dat u eerder hebt gemaakt.

   > [!NOTE]
   > De knop heeft ook een genest Text-object. Zorg ervoor dat u deze niet per ongeluk voor tekst uitvoer kiest (of wijzig de naam van een van de tekst objecten met behulp van het veld **naam** in het venster **controle** om Verwar ring te voor komen).

## <a name="run-the-application-in-the-unity-editor"></a>De toepassing uitvoeren in de Unity-editor

U bent nu klaar om de toepassing uit te voeren in de Unity-editor.

1. Selecteer in de werk balk Unity editor (onder de menu balk) de knop **afspelen** (een naar rechts wijzende drie hoek).

1. Ga naar de [ **Game** weergave](https://docs.unity3d.com/Manual/GameView.html)en wacht tot het **tekst** object wordt weer gegeven **Klik op de knop om spraak herkenning te herkennen**. (Er wordt **nieuwe tekst** weer gegeven wanneer de toepassing niet is gestart of niet gereed is om te reageren.)

1. Selecteer de knop en spreek een Engelse zin of zin in op de microfoon van uw computer. Uw spraak wordt verzonden naar de speech-service en getranscribeerd naar tekst, die wordt weer gegeven in de **Game** weergave.

   [de weer gave van ![game in de eenheids editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Controleer het [ **console** venster](https://docs.unity3d.com/Manual/Console.html) voor fout opsporing van berichten. Als het **console** venster niet wordt weer gegeven, gaat u naar de menu balk en selecteert u **window** > **General** > **console** om deze weer te geven.

1. Wanneer u klaar bent met het herkennen van spraak, selecteert u de knop **afspelen** in de werk balk eenheids editor om de toepassing te stoppen.

## <a name="additional-options-to-run-this-application"></a>Extra opties voor het uitvoeren van deze toepassing

Deze toepassing kan ook worden geïmplementeerd als een Android-app, een zelfstandige Windows-app of een UWP-toepassing.
Zie voor meer informatie onze [voorbeeld opslagplaats](https://aka.ms/csspeech/samples). In de map `quickstart/csharp-unity` wordt de configuratie voor deze aanvullende doelen beschreven.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
