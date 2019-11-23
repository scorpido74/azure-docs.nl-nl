---
title: Unreal-en WWise-integratie in Project akoestische
titlesuffix: Azure Cognitive Services
description: In dit artikel wordt de integratie van het project akoestische Unreal-en WWise-invoeg toepassingen in uw project beschreven.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243048"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Unreal-en WWise-integratie in Project akoestische
In dit artikel wordt beschreven hoe u het pakket voor de geluids invoeg toepassing van het project integreert in uw bestaande Unreal-en WWise-Game project.

Software vereisten:
* [Unreal Engine](https://www.unrealengine.com/) 4.20 +
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018,1
* [WWise-invoeg toepassing voor Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Als u een directe integratie van de WWise-SDK gebruikt in plaats van de WWise-Unreal-invoeg toepassing, raadpleegt u de invoeg toepassing project akoestische Unreal en past u WWise-API-aanroepen aan.

Als u project akoestische wilt gebruiken met een andere audio-engine dan WWise, moet u een uitbrei ding aanvragen in het [discussie forum van het project akoestische](https://github.com/microsoft/ProjectAcoustics/issues). U kunt de Unreal-invoeg toepassing voor project akoestische gebruiken om een query uit te voeren op geluids gegevens en API-aanroepen naar uw engine te maken.

## <a name="download-project-acoustics"></a>Project akoestische downloaden
Down load het [Unreal-en WWise-invoeg pakket voor project akoestische](https://www.microsoft.com/download/details.aspx?id=58090) als u dat nog niet hebt gedaan.

Het pakket bevat een Unreal-invoeg toepassing en een WWise-mixer-invoeg toepassing. De Unreal-invoeg toepassing biedt integratie van de editor en runtime. Tijdens de gameplay berekent het project akoestische Unreal-invoeg toepassingen para meters zoals bedekking voor elk spel object voor elk frame. Deze para meters worden vertaald naar WWise API-aanroepen.

## <a name="integration-steps"></a>Integratie stappen

Volg deze stappen om het pakket te installeren en te implementeren in uw game.

### <a name="install-the-project-acoustics-mixer-plug-in"></a>De invoeg toepassing voor de project akoestische mixers installeren
1. Open het start programma voor WWise. Selecteer op het tabblad **invoeg toepassingen** onder **nieuwe invoeg toepassingen installeren**de optie **toevoegen uit map**.

    ![Een invoeg toepassing installeren in het start programma voor WWise](media/wwise-install-new-plugin.png)

1. Selecteer de *AcousticsWwisePlugin\ProjectAcoustics* -map in het Download pakket. Deze bevat de bundel invoeg toepassing WWise mixer.

   WWise installeert de invoeg toepassing. De geluids van het project moet worden weer gegeven in de lijst met geïnstalleerde invoeg toepassingen in WWise.  
![De lijst met geïnstalleerde WWise-invoeg toepassingen na de installatie van de project akoestische](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>WWise in uw game deimplementeren
Implementeer WWise in uw game, zelfs als u al een WWise hebt geïntegreerd. Deze stap integreert de WWise-invoeg toepassing voor project akoestische.

   > [!NOTE]
   > **Engine-invoeg toepassing:** Als WWise is geïnstalleerd als een game-invoeg toepassing in een Unreal C++ -project, slaat u deze stap over. Als de app in plaats daarvan wordt geïnstalleerd als een engine-invoeg toepassing, bijvoorbeeld omdat uw Unreal-project alleen blauw drukken is, is WWise-implementatie met onze mixer-invoeg toepassing ingewik kelder. Een leeg pop-Unreal C++ project maken. Sluit de Unreal-editor als deze wordt geopend en volg de resterende procedure voor het implementeren van WWise in het dummy-project. Kopieer vervolgens de geïmplementeerde WWise-invoeg toepassing.
 
1. Selecteer in het start programma WWise het tabblad **Unreal Engine** . Selecteer het menu ' Hamburger ' (pictogram) naast **recente Unreal-Engine projecten** en selecteer vervolgens **Bladeren voor project**. Open het project bestand van het Unreal-project *.*

    ![Het tabblad WWise Launcher Unreal](media/wwise-unreal-tab.png)

1. Selecteer **WWise integreren in Project** of **WWise wijzigen in Project**. Deze stap integreert binaire WWise-bestanden in uw project, met inbegrip van de invoeg toepassing voor de project akoestische-mixer.

   > [!NOTE]
   > **Engine-invoeg toepassing:** Als u WWise als een engine-invoeg toepassing gebruikt en u het dummy-project hebt gemaakt zoals eerder beschreven, kopieert u de map die WWise geïmplementeerd: *[DummyUProject] \Plugins\Wwise*. Plak het over *[UESource] \Engine\Plugins\Wwise*. *[DummyUProject]* is het lege Unreal C++ -projectmap en *[UESource]* is de plaats waar de Unreal-Engine bronnen zijn geïnstalleerd. Nadat u de map hebt gekopieerd, kunt u het dummy-project verwijderen.

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>Voeg de Unreal-invoeg toepassing voor project akoestische toe aan uw game
 
1. Kopieer de map *Unreal\ProjectAcoustics* in het invoeg toepassings pakket. Maak een nieuwe map *[UProjectDir] \Plugins\ProjectAcoustics*, waarbij *[UProjectDir]* de projectmap van uw game is die het *. uproject* -bestand bevat.

   > [!NOTE]
   > **Engine-invoeg toepassing**: als u WWise als een engine-invoeg toepassing gebruikt, moet u ook project akoestische als een Unreal-Engine-invoeg toepassing gebruiken. Gebruik in plaats van de doelmap die eerder is vermeld *[UESource] \Engine\Plugins\ProjectAcoustics*.

1. Controleer of u een map *WWise* naast de map *ProjectAcoustics* ziet. Het bevat de WWise-invoeg toepassing en binaire bestanden voor de invoeg toepassing die u eerder hebt geïmplementeerd.

### <a name="extend-wwise-unreal-plug-in-functionality"></a>WWise Unreal-invoeg toepassings functionaliteit uitbreiden
De Unreal-invoeg toepassing voor project akoestische vereist extra gedrag dat wordt weer gegeven vanuit de WWise Unreal-invoeg toepassing API volgens [deze richt lijnen](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). We hebben een batch-bestand opgenomen om de patch procedure te automatiseren.

* Voer *PatchWwise. bat*in *Plugins\ProjectAcoustics\Resources*uit. In het volgende voor beeld wordt het AcousticsGame-voorbeeld project gebruikt.

    ![Een Windows Verkenner-venster met het script voor de patch WWise gemarkeerd](media/patch-wwise-script.png)

* Als u de DirectX SDK niet hebt geïnstalleerd: afhankelijk van de versie van WWise die u gebruikt, moet u mogelijk een opmerking plaatsen bij de regel die `DXSDK_DIR` in *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.build.cs*bevat:

    ![De code-editor met ' DXSDK '](media/directx-sdk-comment.png)

* Als u compileert met Visual Studio 2019: als u een koppelings fout met WWise wilt omzeilen, wijzigt u de standaard `VSVersion` waarde in *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.build.cs* hand matig in **vc150**:

    ![De code-editor met ' VSVersion ' is gewijzigd in ' vc150 '](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>Het spel bouwen en controleren of python is ingeschakeld

1. Compileer uw spel en controleer of het correct is gebouwd. Als deze nog niet is gemaakt, controleert u de vorige stappen zorgvuldig voordat u doorgaat.

1. Open het project in de Unreal-editor.

    > [!NOTE]
    > **Engine-invoeg toepassing:** Als u ProjectAcoustics als een engine-invoeg toepassing gebruikt, moet u er ook voor zorgen dat deze is ingeschakeld onder ingebouwde invoeg toepassingen.

    Er wordt een nieuwe modus weer gegeven, die aangeeft dat de geluids van het project is geïntegreerd.

    ![De akoestische modus is volledig in Unreal](media/acoustics-mode-full.png)

1. Controleer of de python-invoeg toepassing voor Unreal is ingeschakeld, zodat de integratie van de editor correct werkt.

    ![De python-uitbrei dingen in de Unreal-editor zijn ingeschakeld](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>Stel uw WWise-project in om project akoestische te gebruiken

Een voor beeld van een WWise-project is opgenomen in de down load samples. We raden u aan deze samen met deze instructies te bekijken. De scherm afbeeldingen verderop in dit artikel zijn afkomstig uit dit project.

#### <a name="bus-setup"></a>Bus-installatie
De Unreal-invoeg toepassing voor project akoestische zoekt naar de gekoppelde mixer-invoeg toepassing op een bus met de exacte naam `Project Acoustics Bus`. Maak een nieuwe audio bus met dezelfde naam. De invoeg toepassing voor de mixer kan worden gebruikt in verschillende configuraties. We gaan echter ervan uit dat deze alleen wordt gebruikt voor de verwerking van galm. Deze bus heeft het gemengde galm signaal voor alle bronnen die akoestische gebruiken. De IT-upstream kan worden gecombineerd in elke wille keurige bus-structuur. Hier wordt een voor beeld weer gegeven uit het WWise-voorbeeld project dat is opgenomen in de voor beeld-down load.

![WWise-bussen met de project akoestische bus](media/acoustics-bus.png)

1. Stel de kanaal configuratie op de bus in op *1,0*, *2,0*, *4,0*, *5,1*of *7,1*. Elke andere instelling leidt tot geen uitvoer op de bus.

    ![Kanaal configuratie opties voor de project akoestische bus](media/acoustics-bus-channel-config.png)

1. Ga naar de gegevens van de geluids bus van het project en zorg ervoor dat u het tabblad **invoeg toepassing voor mixer** kunt zien.

    ![WWise met het tabblad invoeg toepassing voor de project akoestische bus ingeschakeld](media/mixer-tab-enable.png)

1. Ga naar het tabblad **invoeg toepassing voor mixer** en voeg de invoeg toepassing voor de project akoestische mixers toe aan de bus.

    ![Diagram van het toevoegen van de invoeg toepassing voor de project akoestische-mixer aan de WWise-bus](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>Setup actor-mixer-hiërarchie
Voor de beste prestaties wordt de verwerking van digitale signalen op het niveau van een geluid toegepast op alle bronnen tegelijk. Daarom moet de invoeg toepassing worden gebruikt als een mixer-invoeg toepassing. WWise vereist dat er mixer-invoeg toepassingen aanwezig zijn op de uitvoer bus, hoewel de uitvoer bus doorgaans het droge uitvoer signaal uitvoert. Voor de geluids sterkte van een project moet het droge signaal worden gerouteerd via aux-bussen, terwijl het natte signaal wordt uitgevoerd op de `Project Acoustics Bus`. Het volgende proces ondersteunt geleidelijke migratie naar deze signaal stroom.

Stel dat u een bestaand project hebt met een actor-mixer-hiërarchie die *beeld*, *wapens*en anderen op het hoogste niveau bevat. Elk heeft een bijbehorende uitvoer bus voor de droge mix. Stel dat u beeld wilt migreren om akoestische te gebruiken. Maak eerst een corresponderende aux-bus om de droge submix te maken die een onderliggend element is van de beeld-uitvoer bus. We hebben bijvoorbeeld een voor voegsel ' droge ' gebruikt in de volgende afbeelding om de bussen te ordenen, hoewel de exacte naam niet van belang is. Alle meters of effecten die u op de beeld-bus had, functioneren net als voorheen.

![Aanbevolen WWise droge mix instellen](media/wwise-dry-mix-setup.png)

Wijzig vervolgens de bus-uitvoer structuur voor de beeld actor-mixer als volgt, waarbij de *project akoestische bus* is ingesteld als **uitvoer bus**en *Dry_Footsteps* ingesteld als een door de gebruiker gedefinieerde aux-bus.

![Aanbevolen installatie van WWise actor mixer-bus](media/actor-mixer-bus-settings.png)

Alle beeld verkrijgen nu akoestische behandeling en voeren hun galm uit op de projector van het project akoestische. Het droge signaal wordt gerouteerd via Dry_Footsteps en wordt zo ruimtelijk gewend.

De geluids fragmenten van het project zijn alleen van toepassing op geluiden met een 3D-locatie in de wereld. De volgende [WWise-documentatie](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)moeten de plaatsings eigenschappen worden ingesteld zoals weer gegeven. De instelling van de **3D-spatialization** kan een *positie* of *positie en stand* zijn, waar nodig.

![Aanbevolen positie-instellingen voor WWise actor](media/wwise-positioning.png)

U kunt geen **uitvoer bus** instellen op een andere bus die upstream samenmengt in de *project akoestische bus*. WWise legt deze vereiste op mixer-invoeg toepassingen.

Als u een onderliggend element in de beeld actor-mixer-hiërarchie niet wilt gebruiken voor het gebruik van akoestische, kunt u ' bovenliggende ' overschrijven ' gebruiken om dit te doen.

Als u door het spel gedefinieerde of door de gebruiker gedefinieerde verzen dingen voor een actor-mixer in het spel gebruikt, schakelt u deze uit op die actor-mixer om te voor komen dat er twee keer meer worden toegepast.

#### <a name="spatialization"></a>Spatialization
De WWise mixer-invoeg toepassing voor project akoestische past standaard convolutie galm toe, waardoor WWise op spatialization kan worden gesleept. Wanneer u project akoestische gebruikt in deze standaard alleen-galm configuratie, kunt u elke kanaal configuratie en spatialization-methode gebruiken voor uw droge mix. U kunt dus bijna elke spatializer combi neren en vergelijken met het project akoestische klanken. Uw opties zijn onder andere [op Ambisonics gebaseerde binaural spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) en [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Project akoestische bevat een optionele spatializer die zowel op objecten gebaseerde HRTF rendering als pannen ondersteunt. Schakel het selectie vakje **spatialization uitvoeren** in de instellingen van de mixer-invoeg toepassing in en kies tussen *HRTF* of *pannen*. Schakel ook door de gebruiker gedefinieerde aux-verzen dingen uit naar alle droge bussen om spatializing twee maal te vermijden door de invoeg toepassing voor project akoestische mixers en WWise. De spatialization-modus kan in realtime niet worden gewijzigd, omdat er een geluids Bank opnieuw moet worden gegenereerd. Start Unreal opnieuw op en Genereer soundbanks voordat u afspelen selecteert voor het integreren van de configuratie wijzigingen voor de invoeg toepassing van de mixer, zoals de instelling spatialization selectie vakje **uitvoeren** .

![Spatialization-instellingen voor WWise-mixer-invoeg toepassingen](media/mixer-spatial-settings.png)

Helaas worden andere op objecten gebaseerde spatializer-invoeg toepassingen momenteel niet ondersteund. Ze worden geïmplementeerd als mixer-invoeg toepassingen en WWise staat niet toe dat meerdere mixer-invoeg toepassingen worden toegewezen aan één actor-mixer.  

### <a name="audio-setup-in-unreal"></a>Audio-instellingen in Unreal
1. Eerst moet u uw game niveau maken om een akoestische activa te maken, die in *Content\Acoustics*worden geplaatst. Raadpleeg de [zelf studie voor Unreal maken](unreal-baking.md). Een aantal pre-geïntegreerde niveaus zijn opgenomen in het voorbeeld pakket.

1. Maak een actor voor akoestische ruimte in uw scène. Maak slechts een van deze actors op een niveau, omdat deze de akoestische voor het hele niveau vertegenwoordigt.

    ![Maken van een actor voor akoestische ruimte in de Unreal-editor](media/create-acoustics-space.png)

1. Wijs de geïntegreerde akoestische-gegevensasset toe aan de geluids gegevens sleuf van de akoestische Space actor. Uw scène heeft nu akoestische beelden.

    ![De toewijzing van akoestische activa in de Unreal-editor](media/acoustics-asset-assign.png)

1. Voeg een lege actor toe. Configureer deze als volgt.

    ![De Unreal-editor toont het gebruik van akoestische onderdelen in een lege actor](media/acoustics-component-usage.png)

       
    <sup>1</sup> Voeg een akoestische audio-onderdeel aan de actor toe. Met dit onderdeel wordt de functionaliteit van het project akoestisch toegevoegd aan het audio onderdeel WWise.
        
    <sup>2</sup> het vak **afspelen op het begin** is standaard geselecteerd. Met deze instelling wordt een gekoppelde WWise-gebeurtenis geactiveerd op het niveau van opstarten.</li>
         
    <sup>3</sup> gebruik het selectie vakje **akoestische para meters weer geven** om informatie over fout opsporing op het scherm af te drukken over de bron.

    ![Het Unreal editor-geluids paneel op de geluids bron met de waarden voor fout opsporing ingeschakeld](media/debug-values.png)

    <sup>4</sup> wijs een WWise-gebeurtenis toe per gewone WWise-werk stroom.
       
    <sup>5</sup> zorg ervoor dat **ruimtelijke audio gebruiken** is uitgeschakeld. Als u de geluids fragmenten van het project gebruikt voor een bepaald audio onderdeel, kunt u de ruimtelijke audio-engine van WWise niet tegelijkertijd gebruiken voor akoestische geluids.</li>
       
U bent nu klaar. Ga door de scène en verken de akoestische effecten.

## <a name="next-steps"></a>Volgende stappen
* Probeer de [zelf studie Unreal/WWise](unreal-workflow.md)van het ontwerp van het project.
* Meer informatie [over hoe u Bakes kunt doen](unreal-baking.md) voor uw game scènes.
