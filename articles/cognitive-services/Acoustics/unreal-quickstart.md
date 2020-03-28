---
title: Project Acoustics snel aan de slag met Unreal
titlesuffix: Azure Cognitive Services
description: Gebruik voorbeeldinhoud om te experimenteren met ontwerpbesturingselementen voor Project Acoustics in Unreal en Wwise en implementeer naar Windows-bureaublad.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d3afcded894f72626a4f24bcbe85c34ac1329c29
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72242924"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Project Acoustics Unreal/Wwise snel aan de slag
In deze quickstart experimenteer je met ontwerpbesturingselementen voor Project Acoustics met behulp van voorbeeldinhoud voor de Unreal Engine en Wwise.

Softwarevereisten voor het gebruik van de voorbeeldinhoud:
* [Unreal Engine](https://www.unrealengine.com/) 4.22
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>Het voorbeeldpakket downloaden
Download het [voorbeeldpakket project acoustics Unreal en Wwise.](https://www.microsoft.com/download/details.aspx?id=58090) De voorbeeldverpakking bevat:
- Unreal Engine project
- Wwise project voor het Unreal project
- Project Acoustics Wwise plug-in

## <a name="set-up-the-project-acoustics-sample-project"></a>Het voorbeeldproject Project Acoustics instellen
Installeer eerst de Project Acoustics plug-in in Wwise. Implementeer vervolgens de Wwise-binaries in het Unreal-project. Pas vervolgens de Wwise Unreal-plug-in aan om Project Acoustics te ondersteunen.

### <a name="install-the-project-acoustics-wwise-plug-in"></a>De Project Acoustics Wwise-plug-in installeren
Open de Wwise Launcher. Selecteer op het tabblad **Plug-ins** onder **Nieuwe plug-ins installeren**de optie **Toevoegen uit map**. Kies de *map AcousticsWwisePlugin\ProjectAcoustics* die is opgenomen in het pakket dat u hebt gedownload.

![De optie om de Wwise-plug-in te installeren in de Wwise Launcher](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Wwise-binaries toevoegen aan het project Project Acoustics Unreal
1. Selecteer in Wwise Launcher het tabblad **Unreal Engine.** 
1. Selecteer het menu 'hamburger' (pictogram) naast **Recente Unreal Engine-projecten**en selecteer **Bladeren naar project**. Open het voorbeeld Unreal project *.uproject* bestand in het pakket *AcousticsSample\AcousticsGame\AcousticsGame.uproject*.

   ![Het tabblad Onwerkelijk in de Wwise Launcher](media/wwise-unreal-tab.png)

3. Selecteer naast het voorbeeldproject Project Acoustics de optie **Wwise integreren in Project**.

   ![De Wwise Launcher toont het Acoustics Game Unreal-project met de optie Integreren gemarkeerd.](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Wwise Unreal-plug-infunctionaliteit uitbreiden
De Project Acoustics Unreal plug-in moet extra gedrag blootgesteld van de Wwise Unreal plug-in API. Voer het batchbestand uit dat bij de Project Acoustics Unreal-plug-in is opgenomen om deze wijzigingen te automatiseren.
* Binnen *AcousticsGame\Plugins\ProjectAcoustics\Resources,* voer *PatchWwise.bat*.

    ![Het script om het Wwise-project te patchen dat is gemarkeerd in een Windows Explorer-venster](media/patch-wwise-script.png)

* Als u de DirectX SDK niet hebt geïnstalleerd: afhankelijk van de versie van Wwise die u `DXSDK_DIR` gebruikt, moet u mogelijk commentaar geven op de regel die bevat in *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs:*

    ![De code editor met "DXSDK" commentaar uit](media/directx-sdk-comment.png)

* Als u compileert met Visual Studio 2019: Als u een koppelingsfout met Wwise wilt omzeilen, wijzigt u handmatig de standaardwaarde *VSVersion* in *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* naar **vc150:**

    ![De codeeditor met VSVersion is gewijzigd in "vc150"](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Het Unreal Project openen 
Wanneer u het Unreal-project opent, wordt u gevraagd om modules opnieuw op te bouwen. Selecteer **Ja**.

Als het openen van het project mislukt vanwege bouwfouten, controleert u of u de Project Acoustics Wwise-plug-in hebt geïnstalleerd op dezelfde versie van Wwise die is gebruikt in het project Acoustics-voorbeeldproject.

Als u een versie van [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) eerder gebruikt dan versie 2019.1, u geen geluidsbanken genereren met behulp van het project Acoustics-voorbeeldproject. U moet Wwise versie 2019.1 integreren in het voorbeeldproject.

## <a name="experiment-with-project-acoustics-design-controls"></a>Experimenteren met ontwerpregelaars van Project Acoustics
Luister naar hoe de scène klinkt door de afspeelknop in de Unreal-editor te selecteren. Gebruik de toetsen W, A, S en D en de muis om te bewegen. Als u sneltoetsen voor extra besturingselementen wilt bekijken, selecteert u F1.

De volgende informatie beschrijft een aantal ontwerpactiviteiten om te proberen.

### <a name="modify-occlusion-and-transmission"></a>Occlusie en transmissie wijzigen
Er zijn ontwerpbesturingselementen per bron voor Project Acoustics op elke Unreal-geluidsactor.

![De Unreal Editor akoestiek ontwerp controles](media/demo-scene-sound-source-design-controls.png)

Als de **occlusiemultiplier** groter is dan 1 (de standaardwaarde is 1), wordt occlusie overdreven. Een instelling van minder dan 1 maakt het occlusie-effect subtieler.

Als u de transmissie via de muur wilt inschakelen, verplaatst u de schuifregelaar **Transmission (dB)** van het laagste niveau.

### <a name="modify-wetness-for-a-source"></a>Nattigheid voor een bron wijzigen
Als u wilt veranderen hoe snel de nattigheid verandert met afstand, gebruikt u **Perceptuele Afstandwarp.** Project Acoustics berekent natte niveaus in de ruimte door middel van simulatie. De niveaus variëren soepel met afstand en bieden perceptuele afstand signalen. Om dit effect te overdrijven, verhoogt u de afstandgerelateerde natte niveaus om de afstandsvervorming te verhogen. Kromtrekkenwaarden van minder dan 1 maken de op afstand gebaseerde nagalm subtieler. U dit effect ook fijner aanpassen via de **instelling Wetness (dB).**

Als u de vervaltijd in de ruimte wilt verhogen, past u **de vervaltijdschaal**aan. Overweeg een geval waarin het simulatieresultaat een vervaltijd van 1,5 seconden is. Als **u de vervaltijdschaal** instelt op 2, wordt een vervaltijd van 3 seconden ingesteld op de bron.

### <a name="modify-distance-based-attenuation"></a>Demping op afstand wijzigen
De Project Acoustics Wwise mixer plug-in respecteert de per-source afstand-gebaseerde demping die is ingebouwd in Wwise. Als u deze curve wijzigt, verandert het droogpadniveau. De Project Acoustics plug-in past het natte niveau aan om de natte/droge mix te behouden die door de simulatie- en ontwerpregelaars wordt gespecificeerd.

![Het Wwise-dempingscurvepaneel met demping die naar 0 gaat vóór de simulatiegrens](media/demo-sounds-attenuation.png)

Project Acoustics berekent in een "simulatiegebied" vak dat is gecentreerd rond elke gesimuleerde speler locatie. De akoestiek activa in het monster pakket werden gebakken met een simulatie regio straal van 45 meter. Dempingen werden ontworpen om te vallen tot 0 voor 45 meter. Hoewel deze falloff is niet een strikte eis, het draagt het voorbehoud dat alleen geometrie binnen 45 meter van de luisteraar zal occlude geluiden.

## <a name="next-steps"></a>Volgende stappen
* [Integreer de Project Acoustics](unreal-integration.md) plug-in in uw Unreal project.
* [Maak een Azure-account](create-azure-account.md) voor uw eigen bakken.
