---
title: Project Acoustics Quickstart met Unity
titlesuffix: Azure Cognitive Services
description: Gebruik voorbeeldinhoud om te experimenteren met ontwerpbesturingselementen voor Project Acoustics in Unity en implementeren op Windows Desktop.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fabdd221ef99414eae0156babbd76dedb1f0745d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72243023"
---
# <a name="project-acoustics-unity-quickstart"></a>Project Akoestiek Eenheid Quickstart
Gebruik projectacoustics-voorbeeldinhoud voor Unity om te experimenteren met ontwerpbesturingselementen met simulatiebasis.

Softwarevereisten:
* [Unity 2018.2+](https://unity3d.com) voor Windows
* [Voorbeeldinhoudpakket project Akoestiek](https://www.microsoft.com/download/details.aspx?id=57346)

Wat zit er in het monsterpakket?
* Unity-scène met geometrie, geluidsbronnen en gameplay-besturingselementen
* Project Acoustics plug-in
* Gebakken akoestiek activa voor de scène

## <a name="import-the-sample-package"></a>Het voorbeeldpakket importeren
Importeer het voorbeeldpakket in een nieuw Unity-project.
1. Ga in Unity naar het aangepaste pakket **voor het** > **invoerpakket** > **voor**assets .

    ![De opties voor het eenheidsimportpakket](media/import-package.png)  

1. Kies **ProjectAcoustics.unitypackage**.

1. Selecteer de knop **Importeren** om het Unity-pakket in uw project te integreren.  
  
    ![Het dialoogvenster Pakket voor eenheid importeren](media/import-dialog.png)  

Zie [Unity-integratie](unity-integration.md) voor extra stappen en notities als u het pakket wilt importeren in een bestaand project.

>[!NOTE]
>Er worden verschillende foutberichten weergegeven in het consolelogboek nadat de import is voltooid. Ga verder naar de volgende stap en start Unity opnieuw.

## <a name="restart-unity"></a>Eenheid opnieuw starten
Het bakgedeelte van de akoestiektoolkit vereist de .NET 4. *x* runtime-versie scripting. Het importeren van pakketten werkt uw Unity-spelerinstellingen bij. Start Unity opnieuw om deze instelling van kracht te laten worden. Open de instellingen **voor Player** om te controleren of de instelling van kracht is geworden:

![Het menu Eenheidsprojectinstellingen](media/player-settings.png)  

![Het deelvenster Instellingen voor eenheidsspelers met .NET 4.x geselecteerd](media/net45.png)  

>[!NOTE]
>Deze screenshot is afkomstig uit Unity 2018. *x*. Het beeld kan verschillen in nieuwere versies van Unity.

## <a name="open-the-project-acoustics-bake-window"></a>Open het bakvenster van Project Acoustics
Selecteer in Unity De optie **Akoestiek** in het **menu Venster.**

![De Unity-editor met de optie Akoestiek gemarkeerd in het menu Venster](media/window-acoustics.png)

Er gaat een zwevend **Acoustics-venster** open. In dit venster stelt u de eigenschappen van de akoestische simulatie in.

![De Unity-editor met het acousticsvenster open](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>Experimenteren met de ontwerpbesturingselementen
Open de voorbeeldscène in de map *ProjectAcousticsSample* en selecteer de afspeelknop in de Unity-editor. Gebruik de toetsen W, A, S en D en de muis om te bewegen. Als u wilt vergelijken hoe de scène klinkt met en zonder akoestiek, selecteert u de R-toets totdat de overlaytekst rood wordt en 'Akoestiek: uitgeschakeld' wordt weergegeven. Als u sneltoetsen wilt bekijken voor meer besturingselementen, selecteert u F1. U ook met de rechtermuisknop klikken om een actie te selecteren en vervolgens met links klikken om die actie uit te voeren.

Het *script AcousticsAdjust* is gekoppeld aan de geluidsbronnen in de voorbeeldscène. Het maakt de ontwerpparameters per bron mogelijk.

![Het script Unity AcousticsAdjust](media/acoustics-adjust.png)

In de volgende secties worden enkele effecten onderzocht die u maken met behulp van de beschikbare besturingselementen. Zie de [Project Acoustics Unity Design Tutorial](unity-workflow.md)voor gedetailleerde informatie over elk besturingselement.

### <a name="modify-distance-based-attenuation"></a>Demping op afstand wijzigen
De audio digitale signaalverwerking in de **Project Acoustics** Unity spatializer plug-in respecteert de op afstand gebaseerde demping per bron die is ingebouwd in de Unity-editor. De besturingselementen voor demping op afstand bevinden zich in de component **Audiobron,** die zich bevindt in het **deelvenster Inspector** van geluidsbronnen onder **3D-geluidsinstellingen:**

![Het deelvenster Opties voor de demping van de afstand unity](media/distance-attenuation.png)

Project Acoustics berekent in een "simulatieregio" vak dat gecentreerd is rond de locatie van de speler. De akoestiek activa in het monster pakket werden gebakken op een simulatie regio grootte van 45 meter rond de speler. Dus, het geluid demping moet worden ontworpen om te vallen tot 0 op ongeveer 45 m.

### <a name="modify-occlusion-and-transmission"></a>Occlusie en transmissie wijzigen
* Als de **occlusiemultiplier** groter is dan 1 (de standaardwaarde is 1), wordt occlusie overdreven. Om het occlusie-effect subtieler te maken, stelt u het in op minder dan 1.

* Als u de transmissie via de muur wilt inschakelen, verplaatst u de schuifregelaar **Transmissie (dB)** van de laagste instelling.

### <a name="modify-wetness-for-a-source"></a>Nattigheid voor een bron wijzigen
* Als u wilt veranderen hoe snel de nattigheid verandert met afstand, gebruikt u **Perceptuele Afstandwarp.** Door middel van simulatie berekent Project Acoustics natte niveaus, die perceptuele afstandssignalen bieden en soepel variëren met afstand. Het verhogen van de afstand warp overdrijft dit effect door het verhogen van afstand-gerelateerde natte niveaus. Kromtrekkenwaarden van minder dan 1 maken de op afstand gebaseerde nagalm subtieler.

   Als u dit effect beter wilt aanpassen, wijzigt u de instelling **Wetness (dB).**

* Als u de vervaltijd in de ruimte wilt verhogen, past u de **vervaltijdschaal**aan. Als het simulatieresultaat voor een bepaald locatiepaar bronlistener een vervaltijd van 1,5 seconden is en de **vervaltijdschaal** is ingesteld op 2, is de vervaltijd die op de bron wordt toegepast 3 seconden.

## <a name="next-steps"></a>Volgende stappen
* Lees meer over de [Unity-gebaseerde Project Acoustics ontwerpbesturingselementen.](unity-workflow.md)
* Verder verkennen van de concepten achter het [ontwerpproces](design-process.md).
* [Maak een Azure-account](create-azure-account.md) aan om de pre-bake- en bakprocessen te verkennen.
