---
title: Quick start voor project akoestische met Unreal
titlesuffix: Azure Cognitive Services
description: Gebruik voorbeeld inhoud om te experimenteren met de ontwerp besturings elementen voor project akoestische in Unreal en WWise, en implementeer het op Windows Desktop.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242924"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Unreal/WWise Quick start voor project akoestische
In deze Snelstartgids gaat u experimenteren met de ontwerp besturings elementen voor project akoestische door voorbeeld inhoud te gebruiken voor de Unreal-Engine en WWise.

Software vereisten voor het gebruik van de voorbeeld inhoud:
* [Unreal-Engine](https://www.unrealengine.com/) 4,22
* [AudioKinetic WWise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>Het voorbeeld pakket downloaden
Down load het [Unreal-en WWise-voorbeeld pakket van het project akoestische](https://www.microsoft.com/download/details.aspx?id=58090). Het voorbeeld pakket bevat:
- Unreal Engine-project
- WWise-project voor het Unreal-project
- WWise-invoeg toepassing voor project akoestische

## <a name="set-up-the-project-acoustics-sample-project"></a>Het voorbeeld project van het project akoestische instellen
Installeer eerst de invoeg toepassing voor project akoestische in WWise. Implementeer vervolgens de binaire WWise-bestanden voor het Unreal-project. Pas vervolgens de WWise Unreal-invoeg toepassing aan om project akoestische te ondersteunen.

### <a name="install-the-project-acoustics-wwise-plug-in"></a>De WWise-invoeg toepassing voor project akoestische installeren
Open het start programma voor WWise. Selecteer op het tabblad invoeg **toepassingen** onder **nieuwe invoeg toepassingen installeren**de optie **toevoegen uit map**. Kies de *AcousticsWwisePlugin\ProjectAcoustics* -map die is opgenomen in het pakket dat u hebt gedownload.

![De optie voor het installeren van de WWise-invoeg toepassing in het start programma voor WWise](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>WWise binaire bestanden toevoegen aan het project akoestische Unreal-voorbeeld project
1. Selecteer in WWise Launcher het tabblad **Unreal Engine** . 
1. Selecteer het menu ' Hamburger ' (pictogram) naast de **recente Unreal-Engine projecten**en selecteer vervolgens **Bladeren voor project**. Open het voorbeeld bestand Unreal project *. uproject* in het pakket *AcousticsSample\AcousticsGame\AcousticsGame.uproject*.

   ![Het tabblad Unreal in het start programma voor WWise](media/wwise-unreal-tab.png)

3. Naast het voorbeeld project van het project akoestische, selecteert u **WWise in Project integreren**.

   ![De WWise Launcher toont het Unreal-project van het akoestische spel met de optie integreren gemarkeerd.](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>WWise Unreal-invoeg toepassings functionaliteit uitbreiden
De Unreal-invoeg toepassing voor project akoestische vereist extra gedrag dat wordt weer gegeven vanuit de WWise Unreal-invoeg toepassings-API. Voer het batch bestand uit dat is geleverd met de Unreal-invoeg toepassing voor project akoestische om deze wijzigingen te automatiseren.
* Voer *PatchWwise. bat*in *AcousticsGame\Plugins\ProjectAcoustics\Resources*uit.

    ![Het script voor het bijwerken van het WWise-project dat is gemarkeerd in een Windows Verkenner-venster](media/patch-wwise-script.png)

* Als u de DirectX SDK niet hebt geïnstalleerd: afhankelijk van de versie van WWise die u gebruikt, moet u mogelijk een opmerking plaatsen bij de regel met `DXSDK_DIR` in *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.build.cs*:

    ![De code-editor met de opmerking ' DXSDK '](media/directx-sdk-comment.png)

* Als u compileert met behulp van Visual Studio 2019: als u een koppelings fout met WWise wilt omzeilen, wijzigt u de standaard waarde *VSVersion* in *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.build.cs* hand matig in **vc150**:

    ![De code-editor met VSVersion is gewijzigd in ' vc150 '](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Het project Unreal openen 
Wanneer u het project Unreal opent, wordt u gevraagd om de modules opnieuw te bouwen. Selecteer **Ja**.

Als het project niet kan worden geopend vanwege build-fouten, controleert u of u de WWise-invoeg toepassing voor project akoestische hebt geïnstalleerd in dezelfde versie van WWise die werd gebruikt in het voorbeeld project van het project akoestische.

Als u een versie van [AudioKinetic WWise](https://www.audiokinetic.com/products/wwise/) gebruikt die ouder is dan versie 2019,1, kunt u geen geluids banken genereren met behulp van het project akoestische voor beeld-project. U moet WWise-versie 2019,1 integreren in het voorbeeld project.

## <a name="experiment-with-project-acoustics-design-controls"></a>Experimenteer met ontwerp controles voor project akoestische
Luister naar hoe de scène klinkt door de knop afspelen te selecteren in de Unreal-editor. Gebruik de sleutels W, A, S en D en de muis om door te gaan. Selecteer F1 om sneltoetsen voor extra besturings elementen weer te geven.

In de volgende informatie worden enkele ontwerp activiteiten beschreven die u kunt proberen.

### <a name="modify-occlusion-and-transmission"></a>Bedekking en overdracht wijzigen
Er zijn het ontwerp besturings elementen per bron project op elke Unreal-geluids actor.

![Het ontwerp besturings elementen van de Unreal-editor](media/demo-scene-sound-source-design-controls.png)

Als de **bedekking** vermenigvuldiger groter is dan 1 (de standaard waarde is 1), is bedekking exaggerated. Een instelling van minder dan 1 maakt het effect van de bedekking subtieler.

Als u de verzen ding via de wand wilt inschakelen, verplaatst u de schuif regelaar voor **verzen ding (DB)** vanaf het laagste niveau.

### <a name="modify-wetness-for-a-source"></a>Wetness voor een bron wijzigen
Gebruik **Perceptueel, afstands verdraaiing**om te wijzigen hoe snel wetness verandert met afstand. Met de geluids sterkte van het project worden natte niveaus in de gehele ruimte berekend met behulp van simulatie. De niveaus kunnen soepel en op afstand verschillen en geven waarnemingen op afstand. Als u dit effect wilt exaggerate, verhoogt u de natte afstands niveaus om de afstands verdraaiing te verhogen. Het verdraaien van waarden kleiner dan 1 maken de op afstand gebaseerde reverberation subtieler. U kunt dit effect ook nauw keurig aanpassen via de instelling **Wetness (DB)** .

Als u de verval tijd in de ruimte wilt verg Roten, past u de schaal voor de **verval tijd**aan. Houd rekening met een geval waarbij het simulatie resultaat een verval tijd van 1,5 seconden is. Het instellen van de **verval tijd** op 2 resulteert in een verval tijd van 3 seconden die is toegepast op de bron.

### <a name="modify-distance-based-attenuation"></a>Afzwakking op afstand wijzigen
De WWise-invoeg toepassing voor project akoestische is een afname van de op afstand gebaseerde demping per bron die in WWise is ingebouwd. Als u deze curve wijzigt, wordt het niveau van het droog-pad gewijzigd. De invoeg toepassing voor project akoestische past het natte niveau aan om het natte/droge mix dat is opgegeven door de besturings elementen simulatie en ontwerp, te behouden.

![Het deel venster WWise-verzwakking met demping naar 0 vóór de simulatie grens](media/demo-sounds-attenuation.png)

De geluids processen van het project worden berekend in een ' simulatie regio ' die is gecentreerd rond elke gesimuleerde speler locatie. De akoestische activa in het voor beeld van het pakket zijn geïntegreerde met een simulatie gebied straal van 45 meters. Verzwakkingen zijn ontworpen om 0 vóór 45 meters te bevallen. Hoewel deze wegvalheid geen strikte vereiste is, is het het voor behoud dat alleen de geometrie binnen 45 meters van de listener occlude.

## <a name="next-steps"></a>Volgende stappen
* [Integreer de](unreal-integration.md) invoeg toepassing voor project akoestische in uw Unreal-project.
* [Maak een Azure-account](create-azure-account.md) voor uw eigen Bakes.
