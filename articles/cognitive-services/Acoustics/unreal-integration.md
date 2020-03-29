---
title: Project Acoustics Unreal en Wwise integratie
titlesuffix: Azure Cognitive Services
description: In dit artikel wordt beschreven dat de Plug-ins Project Acoustics Unreal en Wwise in uw project zijn opgenomen.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: e57212a3002390754aaebc5f2aa9ffb10af230a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243048"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Project Acoustics Unreal en Wwise integratie
In dit artikel wordt beschreven hoe u het Project Acoustics-insteekpakket integreren in uw bestaande Unreal en Wwise-gameproject.

Softwarevereisten:
* [Unreal Engine](https://www.unrealengine.com/) 4.20+
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1
* [Wwise plug-in voor Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Als u een directe integratie van de Wwise SDK gebruikt in plaats van de Wwise Unreal-plug-in, raadpleegt u de Project Acoustics Unreal-plug-in en past u Wwise API-aanroepen aan.

Als u Project Acoustics wilt gebruiken met een andere audio-engine dan Wwise, dient u een verbeteringsverzoek in het [discussieforum projectakoestiek.](https://github.com/microsoft/ProjectAcoustics/issues) U de Project Acoustics Unreal-plug-in gebruiken om akoestiekgegevens op te vragen en API-aanroepen naar uw engine.

## <a name="download-project-acoustics"></a>Project Acoustics downloaden
Download het [Project Acoustics Unreal en Wwise plug-in pakket](https://www.microsoft.com/download/details.aspx?id=58090) als je dat nog niet hebt gedaan.

Een Unreal Engine plug-in en een Wwise mixer plug-in zijn inbegrepen in het pakket. De Unreal-plug-in biedt editor- en runtime-integratie. Tijdens het spelen berekent de Project Acoustics Unreal plug-in parameters zoals occlusie voor elk gameobject voor elk frame. Deze parameters worden vertaald in Wwise API-aanroepen.

## <a name="integration-steps"></a>Integratiestappen

Volg deze stappen om het pakket te installeren en te implementeren in je game.

### <a name="install-the-project-acoustics-mixer-plug-in"></a>De invoegstop van de Project Acoustics-mixer installeren
1. Open de Wwise launcher. Selecteer op het tabblad **Plug-ins** onder **Nieuwe plug-ins installeren**de optie **Toevoegen in map**.

    ![Installeer een plug-in in de Wwise launcher](media/wwise-install-new-plugin.png)

1. Selecteer de *map AcousticsWwisePlugin\ProjectAcoustics* die zich in het downloadpakket bevindt. Het bevat de Wwise mixer plug-in bundel.

   Wwise installeert de plug-in. Project Acoustics moet verschijnen op de geïnstalleerde plug-ins lijst in Wwise.  
![De Wwise geïnstalleerde plug-ins lijst na Project Acoustics installatie](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>Implementeer Wwise in je spel
Zet Wwise opnieuw in je spel, zelfs als je Wwise al hebt geïntegreerd. Deze stap integreert de Project Acoustics Wwise plug-in.

   > [!NOTE]
   > **Motorplug-in:** Als je Wwise hebt geïnstalleerd als een game plug-in in een Unreal C++ project, sla je deze stap over. Als het in plaats daarvan is geïnstalleerd als een engine plug-in, bijvoorbeeld omdat uw Unreal project is Blueprint alleen, Wwise implementatie met onze mixer plug-in is complexer. Maak een dummy leeg Unreal C ++ project. Sluit de Unreal-editor als deze wordt geopend en volg de resterende procedure om Wwise in het dummy-project te implementeren. Kopieer vervolgens de geïmplementeerde Wwise-plug-in.
 
1. Selecteer in het Startprogramma Wwise het tabblad **Unreal Engine.** Selecteer het menu 'hamburger' (pictogram) naast **Recente Unreal Engine-projecten** en selecteer **Bladeren naar project.** Open het Unreal-project *.projectbestand van* je game.

    ![Het tabblad Wwise launcher Unreal](media/wwise-unreal-tab.png)

1. Selecteer **Wwise integreren in Project** of **Wwise wijzigen in Project**. Deze stap integreert Wwise binaries in uw project, inclusief de Project Acoustics mixer plug-in.

   > [!NOTE]
   > **Motorplug-in:** Als u Wwise gebruikt als engine plug-in en u het dummy-project hebt gemaakt zoals eerder beschreven, kopieert u de map die Wwise heeft geïmplementeerd: *[DummyUProject]\Plugins\Wwise*. Plak het over *[UESource]\Engine\Plugins\Wwise*. *[DummyUProject]* is het lege Unreal C++-projectpad en *[UESource]* is de plaats waar de Unreal Engine-bronnen zijn geïnstalleerd. Nadat u de map hebt gekopieerd, u het dummy-project verwijderen.

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>Voeg de Project Acoustics Unreal-plug-in toe aan je game
 
1. Kopieer de map *Unreal\ProjectAcoustics* in het insteekpakket. Maak een nieuwe map *[UProjectDir]\Plugins\ProjectAcoustics*, waarbij *[UProjectDir]* de projectmap van uw game is die het *.uproject-bestand* bevat.

   > [!NOTE]
   > **Engine plug-in**: Als je Wwise gebruikt als een motor plug-in, moet je Project Acoustics ook gebruiken als een Unreal engine plug-in. Gebruik *[UESource]\Engine\Plugins\ProjectAcoustics*in plaats van de eerder aangehaalde bestemmingsmap.

1. Controleer of u een *Wwise-map* ziet naast de map *ProjectAcoustics.* Het bevat de Wwise plug-in samen met binaries voor de mixer plug-in die u eerder geïmplementeerd.

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Wwise Unreal-plug-infunctionaliteit uitbreiden
De Project Acoustics Unreal plug-in vereist extra gedrag blootgesteld van de Wwise Unreal plug-in API volgens [deze richtlijnen](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). We hebben een batchbestand opgenomen om de patchprocedure te automatiseren.

* Binnen *in Plugins\ProjectAcoustics\Resources,* voer *PatchWwise.bat*uit. In het volgende voorbeeld wordt gebruik gemaakt van ons AcousticsGame voorbeeldproject.

    ![Een Windows Explorer-venster met het script om Wwise te patchen gemarkeerd](media/patch-wwise-script.png)

* Als u de DirectX SDK niet hebt geïnstalleerd: afhankelijk van de versie van Wwise die u `DXSDK_DIR` gebruikt, moet u mogelijk commentaar geven op de regel die bevat in *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs:*

    ![De code editor met 'DXSDK' commentaar uit](media/directx-sdk-comment.png)

* Als u compileert met Visual Studio 2019: Als u een koppelingsfout met Wwise wilt omzeilen, wijzigt u handmatig de standaardwaarde `VSVersion` in *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* naar **vc150:**

    ![De codeeditor met "VSVersion" is gewijzigd in "vc150"](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>Bouw het spel en controleer of Python is ingeschakeld

1. Compileer je spel en zorg ervoor dat het correct wordt opgebouwd. Als het niet bouwt, controleert u de vorige stappen zorgvuldig voordat u verdergaat.

1. Open uw project in Unreal Editor.

    > [!NOTE]
    > **Motorplug-in:** Als u ProjectAcoustics gebruikt als een motorplug-in, moet u er ook voor zorgen dat het is ingeschakeld onder "ingebouwde" plug-ins.

    U ziet een nieuwe modus, die aangeeft dat Project Acoustics is geïntegreerd.

    ![Akoestiek modus vol in Unreal](media/acoustics-mode-full.png)

1. Controleer of de Python-plug-in voor Unreal is ingeschakeld, zodat de editor-integratie correct functioneert.

    ![De Python-extensies in de Unreal-editor zijn ingeschakeld](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>Uw Wwise-project instellen om Project Acoustics te gebruiken

Een voorbeeld Wwise project is opgenomen in de monsters te downloaden. We raden u aan het samen met deze instructies te bekijken. De screenshots later in dit artikel zijn van dit project.

#### <a name="bus-setup"></a>Bussetup
De Project Acoustics Unreal plug-in zoekt naar de bijbehorende mixer plug-in op een bus met de exacte naam. `Project Acoustics Bus` Maak een nieuwe audiobus met dezelfde naam. De mixer plug-in kan werken in verschillende configuraties. Maar voor nu, gaan we ervan uit dat het alleen zal worden gebruikt voor reverb verwerking. Deze bus zal het gemengde galmsignaal dragen voor alle bronnen die Acoustics gebruiken. Het kan stroomopwaarts mengen in elke bus-mengen structuur. Een voorbeeld wordt hier weergegeven uit het Wwise-voorbeeldproject dat is opgenomen in de voorbeelddownload.

![Wwise bussen die De Bus van de Akoestiek van Het wat van het project](media/acoustics-bus.png)

1. Stel de kanaalconfiguratie op de bus in op *1.0*, *2.0*, *4.0*, *5.1*of *7.1*. Elke andere instelling zal resulteren in geen output op de bus.

    ![Kanaalconfig opties voor Project Acoustics Bus](media/acoustics-bus-channel-config.png)

1. Ga naar de details van de Project Acoustics Bus en zorg ervoor dat u het **tabblad Mixer-plug-in** zien.

    ![Wwise met het tabblad Mixer Plug-in voor de Project Acoustics Bus ingeschakeld](media/mixer-tab-enable.png)

1. Ga naar het tabblad **Mixer Plug-in** en voeg de plug-in voor de akoestiek van het project toe aan de bus.

    ![Diagram voor het toevoegen van de Project Acoustics Mixer Plug-in aan de Wwise-bus](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>Instelling voor de hiërarchie van actormixer
Voor de beste prestaties past Project Acoustics audiodigitale signaalverwerking toe op alle bronnen tegelijk. De plug-in moet dus werken als een mixer plug-in. Wwise vereist mixer plug-ins te worden op de output bus, hoewel de output bus draagt meestal de droge output signaal. Project Acoustics vereist dat het droge signaal wordt gerouteerd door `Project Acoustics Bus`aux bussen, terwijl het natte signaal wordt uitgevoerd op de . Het volgende proces ondersteunt geleidelijke migratie naar deze signaalstroom.

Stel dat u een bestaand project hebt met een hiërarchie van actor-mixers die *voetstappen,* *wapens*en andere op het hoogste niveau bevat. Elk heeft een bijbehorende output bus voor zijn droge mix. Stel dat u voetstappen wilt migreren om akoestiek te gebruiken. Maak eerst een bijbehorende aux-bus om de droge submix te dragen die een kind is van de voetstappen uitvoerbus. We gebruikten bijvoorbeeld een "Droog" voorvoegsel in de volgende afbeelding om de bussen te organiseren, hoewel de exacte naam niet belangrijk is. Alle meters of effecten die je had op de voetstappen bus zal nog steeds functioneren als voorheen.

![Aanbevolen Wwise droge mix setup](media/wwise-dry-mix-setup.png)

Wijzig vervolgens de busuitvoerstructuur voor de actormixer Voetstappen als volgt, met *Project Acoustics Bus* als **uitvoerbus**en *Dry_Footsteps* ingesteld als een door de gebruiker gedefinieerde aux-bus.

![Aanbevolen Wwise actor mixer bus setup](media/actor-mixer-bus-settings.png)

Nu krijgen alle voetstappen een akoestiekbehandeling en output van hun galm op de Project Acoustics Bus. Het droge signaal wordt door Dry_Footsteps gerouteerd en zoals gebruikelijk ruimtelijk gemaakt.

Project Acoustics is alleen van toepassing op geluiden met een 3D-locatie in de wereld. Na [Wwise-documentatie](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)moeten de positioneringseigenschappen worden ingesteld zoals weergegeven. De **instelling 3D Spatialization** kan indien nodig *positie* of positie *+ oriëntatie* zijn.

![Aanbevolen positioneringsinstellingen van Wwise Actor](media/wwise-positioning.png)

U **de uitvoerbus** niet instellen op een andere bus die stroomopwaarts in *Project Acoustics Bus*wordt gemengd. Wwise legt deze eis op aan mixerplug-ins.

Als u wilt dat een kind in de voetstappen van de actor-mixerhiërarchie geen akoestiek gebruikt, u 'ouder overschrijven' gebruiken om het af te melden.

Als je game-defined of door de gebruiker gedefinieerde stuurt voor galm op een actor-mixer in het spel, schakel je ze uit op die actor-mixer om te voorkomen dat je twee keer galm toepast.

#### <a name="spatialization"></a>Ruimtelijkheid
De Project Acoustics Wwise mixer plug-in past standaard convolutie reverb toe, waardoor Wwise panning spatialization hoeft te doen. Wanneer u Project Acoustics gebruikt in deze standaard reverb-only configuratie, u elke kanaalconfiguratie en ruimtelijke methode gebruiken op uw droge mix. Zo kun je bijna elke spatializer mixen en matchen met de Project Acoustics reverb. Uw opties omvatten [Ambisonics-gebaseerde binaurale spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) en [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Project Acoustics bevat een optionele spatializer die zowel objectgebaseerde HRTF-rendering en panning met hoge resolutie ondersteunt. Schakel het selectievakje **Ruimtemaken uitvoeren** in de invoeginstellingen voor mixers in en kies tussen *HRTF* of *Pannen.* Schakel ook door de gebruiker gedefinieerde aux-sends naar alle droge bussen uit om te voorkomen dat de Plug-in project acoustics en Wwise twee keer ruimtelijk worden gemaakt. De spatialisatiemodus kan niet in realtime worden gewijzigd omdat er een herstel van de geluidsbank nodig is. Start Unreal opnieuw op en regenereerde vervolgens geluidsbanken voordat u play selecteert om mixerplug-in config-wijzigingen te integreren, zoals de instelling voor het selectievakje **Ruimte uitvoeren.**

![Instellingen voor plug-in-ruimte wwise Mixer](media/mixer-spatial-settings.png)

Helaas worden andere objectgebaseerde spatializer-plug-ins momenteel niet ondersteund. Ze worden geïmplementeerd als mixerplug-ins en Wwise staat niet toe dat meerdere mixerplug-ins worden toegewezen aan één actor-mixer.  

### <a name="audio-setup-in-unreal"></a>Audio-installatie in Unreal
1. Eerst moet je je spelniveau bakken om een akoestiek-asset te produceren, die in *Content\Acoustics*wordt geplaatst. Raadpleeg de [Unreal Bake Tutorial](unreal-baking.md). Sommige voorgebakken niveaus zijn opgenomen in het monsterpakket.

1. Maak een akoestiekruimteacteur in je scène. Maak slechts een van deze actoren in een niveau, omdat het vertegenwoordigt de akoestiek voor het hele niveau.

    ![Creatie van een Akoestiek ruimte acteur in de Unreal editor](media/create-acoustics-space.png)

1. Wijs de gebakken akoestische dataasset toe aan de akoestiek-gegevenssleuf op de ruimteactor Akoestiek. Uw scène heeft nu akoestiek!

    ![Akoestiek asset assignment in de Unreal editor](media/acoustics-asset-assign.png)

1. Voeg een lege actor toe. Configureer het als volgt.

    ![De Unreal-editor toont het gebruik van de akoestiekcomponent in een lege actor](media/acoustics-component-usage.png)

       
    <sup>1</sup> Voeg een audiocomponent Akoestiek toe aan de actor. Dit onderdeel voegt Project Acoustics-functionaliteit toe aan de Wwise-audiocomponent.
        
    <sup>2</sup> Het vak **Afspelen op start** is standaard geselecteerd. Met deze instelling wordt een bijbehorende Wwise-gebeurtenis geactiveerd bij het opstarten op niveau.</li>
         
    <sup>3</sup> Gebruik het selectievakje **Akoestiekparameters weergeven** om foutopsporingsgegevens op het scherm over de bron af te drukken.

    ![Het deelvenster Onwerkelijke editor Akoestiek over geluidsbron met foutopsporingswaarden ingeschakeld](media/debug-values.png)

    <sup>4</sup> Wijs een Wwise-gebeurtenis toe per de gebruikelijke Wwise-werkstroom.
       
    <sup>5</sup> Zorg ervoor dat **Gebruik Ruimtelijke Audio** is uitgeschakeld. Als u Project Acoustics voor een bepaalde audiocomponent gebruikt, u de Spatial Audio-engine van Wwise niet tegelijkertijd gebruiken voor akoestiek.</li>
       
U bent nu klaar. Beweeg rond de scène en verken de akoestische effecten!

## <a name="next-steps"></a>Volgende stappen
* Probeer de [Project Acoustics Unreal / Wwise Design Tutorial](unreal-workflow.md).
* Meer [informatie over hoe je bakt](unreal-baking.md) voor je spelscènes.
