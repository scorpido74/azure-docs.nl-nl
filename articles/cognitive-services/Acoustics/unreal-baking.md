---
title: Project Acoustics Unreal Bake Tutorial
titlesuffix: Azure Cognitive Services
description: Dit document beschrijft het proces van het indienen van een akoestiek bakken met behulp van de Unreal editor extensie.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 7a868a5f9b06499e23710399733b0659d97f900d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854889"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>Project Acoustics Unreal Bake Tutorial
Dit document beschrijft het proces van het indienen van een akoestiek bakken met behulp van de Unreal editor extensie.

Er zijn vijf stappen om een bak te doen:

1. Uw navigatienetwerk voor spelers maken of taggen
2. Akoestiekgeometrie taggen
3. Akoestische materiaaleigenschappen toewijzen aan geometrie
4. Voorbeeld van plaatsing van sonde
5. Bak

## <a name="open-the-project-acoustics-editor-mode"></a>De editormodus Project Acoustics openen

Importeer het Project Acoustics-plug-inpakket in uw project. Zie het onderwerp [Onwerkelijke integratie](unreal-integration.md) voor hulp hierbij. Zodra de plug-in is geïntegreerd, opent u de gebruikersinterface akoestiek door op het nieuwe pictogram akoestiekmodus te klikken.

![Schermafbeelding van de optie Unreal Editor Acoustics Mode](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Tag acteurs voor akoestiek

Het tabblad Objecten is het eerste tabblad dat wordt weergegeven wanneer u de akoestiekmodus opent. Gebruik dit tabblad om acteurs in je level te taggen, waardoor de **tags AcousticsGeometry** of **AcousticsNavigation** aan de acteurs worden toegevoegd.

Selecteer een of meer objecten in de World Outliner of gebruik de sectie **Bulkselectie** om alle objecten van een specifieke categorie te selecteren. Zodra objecten zijn geselecteerd, gebruikt u de sectie **Labelen** om de gewenste tag toe te passen op de geselecteerde objecten.

Als iets noch **AcousticsGeometry** noch **AcousticsNavigation** tag heeft, zal het in de simulatie worden genegeerd. Alleen statische mazen, navigatiemazen en landschappen worden ondersteund. Als je iets anders tagt, wordt het genegeerd.

### <a name="for-reference-the-objects-tab-parts"></a>Voor referentie: de delen van het tabblad Objecten

![Schermafbeelding van het tabblad Akoestiekobjecten in Unreal](media/unreal-objects-tab-details.png)

1. De knoppen voor tabselectie (tabblad**Objecten** geselecteerd). Gebruik deze knoppen om door de verschillende stappen van het doen van een akoestiek bakken, van boven naar beneden.
2. Een korte beschrijving van wat u moet doen met behulp van deze pagina.
3. Beschikbare selectors voor acteurs in het level.
4. Als u op **Selecteren** klikt, worden alle objecten geselecteerd in het niveau dat overeenkomt met ten minste één van de geselecteerde actortypen.
5. Als u **alles op Deselectie deselecteren klikt,** wordt de huidige selectie gewist. Dit is hetzelfde als het raken van de ontsnappingssleutel.
6. Gebruik deze keuzerondjes om te kiezen of u de tag Geometrie of Navigatie wilt toepassen op de geselecteerde actoren.
7. Als u op **Tag** klikt, wordt de geselecteerde tag toegevoegd aan alle geselecteerde actoren.
8. Als u op **Markering ongedaan maakt,** wordt de geselecteerde tag verwijderd uit alle geselecteerde actoren.
9. Als u **op Tagged selecteren klikt,** wordt de huidige selectie gewist en worden alle actoren met de geselecteerde tag geselecteerd.
10. Deze statistieken laten zien hoeveel acteurs zijn getagd met elk tagtype.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Tag akoestiek occlusie en reflectie geometrie

Open het tabblad Objecten van het venster Akoestiek. Markeer objecten als Akoestiekgeometrie als ze geluid moeten occlude, reflecteren of absorberen. Akoestiek geometrie kan omvatten dingen zoals grond, muren, daken, ramen & raamglas, tapijten, en grote meubels. U elke willekeurige complexiteit voor deze objecten gebruiken. Omdat de scène is voxelized vóór simulatie, zeer gedetailleerde mazen, zoals bomen met veel kleine bladeren, zijn niet duurder om te bakken dan vereenvoudigde objecten.

Neem geen dingen op die geen invloed mogen hebben op de akoestiek, zoals onzichtbare botsingsmazen.

De transformatie van een object op het moment van de sondeberekening (via het tabblad Probes, hieronder) wordt in de bakresultaten vastgesteld. Als u een van de gemarkeerde objecten in de scène verplaatst, moet de berekening van de sonde opnieuw worden berekend en de scène opnieuw worden bakken.

### <a name="create-or-tag-a-navigation-mesh"></a>Een navigatienet maken of taggen

Een navigatienet wordt gebruikt om sondepunten te plaatsen voor simulatie. U Unreal's [Nav Mesh Bounds Volume](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html)gebruiken of u uw eigen navigatienet opgeven. U moet ten minste één object taggen als **Akoestieknavigatie**. Als u unreal's navigatiemesh gebruikt, moet u ervoor zorgen dat u het eerst laat bouwen.

### <a name="acoustics-volumes"></a>Akoestiek Volumes ###

Er is nog meer, geavanceerde aanpassingen die u maken op uw navigatiegebieden met **Akoestiekvolumes.** **AkoestiekVolumes** zijn actoren die u aan uw scène toevoegen waarmee u gebieden selecteren die u opnemen en negeren vanuit het navigatienet. De actor onthult een eigenschap die kan worden geschakeld tussen 'Opnemen' en 'Uitsluiten'. "Opnemen" volumes zorgen ervoor dat alleen gebieden van het navigatienet erin worden beschouwd en "Uitsluiten" volumes markeren die gebieden die moeten worden genegeerd. "Uitsluiten" volumes worden altijd toegepast na "Opnemen" volumes. Zorg ervoor dat **u akoestiekvolumes tagt** als **akoestieknavigatie** via het gebruikelijke proces op het tabblad Objecten. Deze acteurs worden ***niet*** automatisch getagd.

![Schermafbeelding van de eigenschappen van akoestiekvolume in Unreal](media/unreal-acoustics-volume-properties.png)

"Uitsluiten" volumes zijn voornamelijk bedoeld om fijnkorrelige controle te geven over waar geen sondes te plaatsen voor het aanscherpen van het gebruik van hulpbronnen.

![Schermafbeelding van Akoestiekvolume uitsluiten in Onwerkelijk](media/unreal-acoustics-volume-exclude.png)

'Opnemen' volumes zijn handig voor het maken van handmatige secties van een scène, bijvoorbeeld als u uw scène wilt opsplitsen in meerdere akoestische zones. Bijvoorbeeld, als je een grote scène, vele kilometers in het kwadraat, en je hebt twee gebieden van belang die u wilt akoestiek bakken op. U twee grote Include volumes in de scène tekenen en ACE-bestanden voor elk van hen één voor één produceren. Vervolgens u in het spel triggervolumes gebruiken in combinatie met blauwdrukoproepen om het juiste ACE-bestand te laden wanneer de speler elke tegel nadert.

**Akoestiek Volumes** beperken alleen de navigatie en ***niet*** de geometrie. Elke sonde in een "Include" **Acoustics Volume** zal nog steeds trekken in alle benodigde geometrie buiten het volume bij het uitvoeren van golfsimulaties. Daarom mogen er geen discontinuïteiten zijn in occlusie of andere akoestiek als gevolg van de speler die van de ene sectie naar de andere gaat.

## <a name="select-acoustic-materials"></a>Akoestische materialen selecteren

Zodra uw objecten zijn getagd, klikt u op de knop **Materialen** om naar het tabblad Materialen te gaan. Dit tabblad wordt gebruikt om materiaaleigenschappen op te geven voor elk materiaal in het niveau. Voordat acteurs worden getagd, is deze leeg.

De akoestische materialen regelen de hoeveelheid geluidsenergie die van elk oppervlak wordt teruggekaatst. Het standaard akoestische materiaal heeft absorptie vergelijkbaar met beton. Project Acoustics stelt materialen voor op basis van de scènemateriaalnaam.

De nagalmtijd van een bepaald materiaal in een ruimte is omgekeerd gerelateerd aan de absorptiecoëfficiënt, waarbij de meeste materialen absorptiewaarden hebben in het bereik van 0,01 tot 0,20. Materialen met absorptiecoëfficiënten boven dit bereik zijn zeer absorberend. Als een ruimte bijvoorbeeld te weergalmend klinkt, verandert u het akoestische materiaal van de muren, vloer of plafond in iets van hogere onthouding. De akoestische materiaalopdracht is van toepassing op alle actoren die dat scènemateriaal gebruiken.

![Grafiek die negatieve correlatie van nagalmtijd met absorptiecoëfficiënt toont](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Voor referentie: delen van het tabblad Materialen

![Schermafbeelding van het tabblad Akoestiekobjecten in Unreal](media/unreal-materials-tab-details.png)

1. De knop **Materialen,** gebruikt om deze pagina naar boven te brengen.
2. Een korte beschrijving van wat u moet doen met behulp van deze pagina.
3. De lijst van materialen die worden gebruikt in het niveau, afkomstig van de acteurs gelabeld als **AcousticsGeometry**. Als u hier op een materiaal klikt, worden alle objecten in de scène geselecteerd die dat materiaal gebruiken.
4. Toont het akoestische materiaal waaraan het scènemateriaal is toegewezen. Klik op een vervolgkeuzelijst om een scènemateriaal opnieuw toe te wijzen aan een ander akoestisch materiaal.
5. Toont de akoestische absorptiecoëfficiënt van het materiaal dat in de vorige kolom is geselecteerd. Een waarde van nul betekent perfect reflecterend (geen absorptie), terwijl een waarde van 1 betekent perfect absorptief (geen reflectie). Als u deze waarde wijzigt, wordt het akoestiekmateriaal (stap #4) bijgewerkt naar **Custom.**

Als u wijzigingen aanbrengt in de materialen in uw scène, moet u van tabbladen wisselen in de plug-in Project Acoustics om deze wijzigingen weer te geven op het tabblad **Materialen.**

## <a name="calculate-and-review-listener-probe-locations"></a>Locaties voor listenersonde berekenen en controleren

Nadat u de materialen hebt toebedeeld, schakelt u over naar het tabblad **Probes.**

### <a name="for-reference-parts-of-the-probes-tab"></a>Voor referentie: delen van het tabblad Probes

![Schermafbeelding van het tabblad Akoestieksprobes in Unreal](media/unreal-probes-tab-details.png)

1. De tabknop **Probes** die wordt gebruikt om deze pagina weer te geven
2. Een korte beschrijving van wat u moet doen met behulp van deze pagina
3. Gebruik dit om een grove of fijne simulatieresolutie te kiezen. Grof is sneller, maar heeft bepaalde afwegingen. Zie [Bakresolutie](bake-resolution.md) hieronder voor meer informatie.
4. Kies de locatie waar de akoestiek gegevensbestanden moeten worden geplaatst met behulp van dit veld. Klik op de knop met "..." om een mapkiezer te gebruiken. Zie [Gegevensbestanden](#Data-Files) hieronder voor meer informatie over gegevensbestanden.
5. De gegevensbestanden voor deze scène worden benoemd met behulp van het hier meegeleverde voorvoegsel. De standaardinstelling is "[Level Name]_AcousticsData".
6. Klik **op** de knop Berekenen om de scène te voxelize en bereken de locaties van het sondepunt. Dit gebeurt lokaal op uw machine, en moet worden gedaan voorafgaand aan het doen van een bak. Nadat de sondes zijn berekend, worden de bovenstaande besturingselementen uitgeschakeld en wordt deze knop gewijzigd om **Duidelijk**te zeggen. Klik **op de** knop Wissen om de berekeningen te wissen en schakel de besturingselementen in, zodat u opnieuw berekenen met behulp van nieuwe instellingen.

Sondes moeten worden geplaatst via het geautomatiseerde proces op het tabblad **Probes.**


### <a name="what-the-calculate-button-calculates"></a>Wat de knop 'Berekenen' berekent

Met de knop **Berekenen** worden alle gegevens die u tot nu toe hebt verstrekt (geometrie, navigatie, materialen en de grove/fijne instelling) en gaat u door verschillende stappen:

1. Het neemt de geometrie uit de scène mazen en berekent een voxel volume. Het voxel volume is een 3-dimensionaal volume dat je hele scène omsluit, en bestaat uit kleine kubieke "voxels". De grootte van de voxels wordt bepaald door de simulatiefrequentie, die wordt ingesteld door de instelling **Simulatieresolutie.** Elke voxel is gemarkeerd als "open air" of met scènegeometrie. Als een voxel geometrie bevat, wordt de voxel getagd met de absorptiecoëfficiënt van het materiaal dat aan die geometrie is toegewezen.
2. Vervolgens gebruikt het de navigatiegegevens om akoestisch interessante locaties te berekenen waar de speler naartoe zou kunnen gaan. Het probeert te vinden van een redelijk kleine set van deze locaties die kleinere gebieden zoals deuropeningen en gangen omvat, en vervolgens naar kamers, om ruimtes te openen. Voor kleine scènes is dit meestal minder dan 100 locaties, terwijl grote scènes kunnen hebben tot duizend.
3. Voor elk van de uiteindelijke listenerlocaties die het berekent, bepaalt het een aantal parameters, zoals hoe "open" de ruimte is, de grootte van de ruimte waarin het zich bevindt, enz.
4. De resultaten van deze berekeningen worden opgeslagen in bestanden op de door u opgegeven locatie (Zie [gegevensbestanden](#Data-Files) hieronder)

Afhankelijk van de grootte van uw scène en de snelheid van uw machine, kunnen deze berekeningen enkele minuten duren.

Zodra deze berekeningen zijn voltooid, u een voorbeeld van zowel de voxel gegevens en de sonde punt locaties om ervoor te zorgen dat de bak geeft je goede resultaten. Dingen zoals een slecht navigatiegaas of ontbrekende/extra geometrie zijn meestal snel zichtbaar in de preview, zodat u deze corrigeren.


## <a name="debug-display"></a>Foutopsporingsscherm

Nadat de sondeberekening is voltooid, verschijnt er een nieuwe actor in de World Outliner genaamd **AcousticsDebugRenderer.** Als u de **selectievakjes Render Probes** en **Render Voxels** inschakelt, wordt het foutopsporingsscherm in de weergaveport van de editor ingeschakeld.

![Schermafbeelding van acoustics Debug Renderer-actor in Unreal Editor](media/acoustics-debug-renderer.png)

Als u geen voxels of sondes op uw niveau ziet, moet u ervoor zorgen dat real-time rendering is ingeschakeld in de viewport.

![Schermafbeelding van de optie realtime rendering in Unreal](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxels Voxels

Voxels worden in het scènevenster weergegeven als groene kubussen rond de deelnemende geometrie. Voxels die alleen lucht bevatten worden niet getoond. Er is een grote groene doos rond uw hele scène die het volledige voxel volume aangeeft dat in de simulatie zal worden gebruikt.
Beweeg je scène en controleer of de akoestisch-occluding geometrie voxels heeft. Controleer ook of niet-akoestiekobjecten zoals botsmazen niet zijn gevoxeliseerd. De scène camera moet binnen ongeveer 5 meter van het object voor de voxels te laten zien.

Als je de voxels vergelijkt met grove resolutie versus fijne resolutie, zul je zien dat de grove voxels twee keer zo groot zijn.

![Screenshot van Acoustics voxels preview in Unreal editor](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Sondepunten

Probe punten zijn synoniem met mogelijke speler (luisteraar) locaties. Bij het bakken berekent de simulatie de akoestiek die alle mogelijke bronlocaties met elk sondepunt verbindt. Bij runtime wordt de listenerlocatie geïnterpoleerd tussen de nabijgelegen sondepunten.

Het is belangrijk om te controleren of sonde punten bestaan overal waar de speler wordt verwacht om te reizen in de scène. Sondepunten worden door de Project Acoustics-engine op het navigatienet geplaatst en kunnen niet worden verplaatst of bewerkt, dus zorg ervoor dat het navigatiegaas alle mogelijke spelerslocaties bedekt door de sondepunten te inspecteren.

![Schermafbeelding van de preview van Acoustics-sondes in Unreal](media/unreal-probes-preview.png)

Zie [Resolutie bakken](bake-resolution.md) voor meer informatie over grof versus fijn resolutie.

## <a name="bake-your-level-using-azure-batch"></a>Uw niveau bakken met Azure Batch

U uw scène bakken met een compute cluster in de cloud met de Azure Batch-service. De Project Acoustics Unreal-plug-in maakt rechtstreeks verbinding met Azure Batch om een Azure Batch-cluster voor elke bak te instantiëren, beheren en afbreken. Voer op het tabblad Bakken uw Azure-referenties in, selecteer een clustermachinetype en -grootte en klik op Bakken.

### <a name="for-reference-parts-of-the-bake-tab"></a>Ter referentie: Delen van het baktabblad

![Schermafbeelding van het tabblad Akoestiek bakken in Unreal](media/unreal-bake-tab-details.png)

1. De knop Tabblad Bakken wordt gebruikt om deze pagina naar boven te brengen.
2. Een korte beschrijving van wat te doen op deze pagina.
3. Velden om uw Azure-referenties in te voeren zodra uw Azure-account is gemaakt. Zie [Een Azure Batch-account maken voor](create-azure-account.md)meer informatie .
4. Start Azure portal om uw abonnementen te beheren, het gebruik te bewaken en factureringsgegevens te bekijken, enz. 
5. Azure batch compute node type te gebruiken voor de berekening. Het knooppunttype moet worden ondersteund door de locatie van uw Azure-datacenter. Als niet zeker, laat dan bij **Standard_F8s_v2**.
6. Aantal knooppunten dat u voor deze berekening moet gebruiken. Het aantal dat u hier invoert, is van invloed op de tijd om de bak te voltooien en wordt beperkt door uw Azure Batch-kerntoewijzing. De standaardtoewijzing maakt slechts twee 8 kernknooppunten of één 16-kernknooppunt mogelijk, maar kan worden uitgebreid. Zie [Een Azure Batch-account maken](create-azure-account.md)voor meer informatie over kerntoewijzingsbeperkingen.
7. Schakel dit selectievakje in om de compute pool te configureren om [knooppunten met lage prioriteit](https://docs.microsoft.com/azure/batch/batch-low-pri-vms)te gebruiken. Compute nodes met lage prioriteit hebben veel lagere kosten, maar ze zijn mogelijk niet altijd beschikbaar of kunnen op elk moment worden voorgelaten.
8. De hoeveelheid verstreken tijd die naar verwachting nodig is voordat uw taak in de cloud wordt uitgevoerd. Dit omvat geen knooppuntopstarttijd. Zodra de taak begint te lopen, dit gaat over hoe lang het moet duren voordat je terug de resultaten. Houd er rekening mee dat dit slechts een schatting is.
9. De totale hoeveelheid rekentijd die nodig is om de simulaties uit te voeren. Dit is de totale hoeveelheid gegevensstroom van het knooppunt die in Azure wordt gebruikt. Zie [Het schatten van bakkosten](#Estimating-bake-cost) hieronder voor meer informatie over het gebruik van deze waarde.
10. Klik op de knop Bakken om de bak naar de cloud te sturen. Terwijl een taak wordt uitgevoerd, wordt in deze functie **annuleren** weergegeven. Als er fouten zijn op dit tabblad of als de werkstroom op het tabblad **Sondes** niet is voltooid, wordt deze knop uitgeschakeld.
11. De sonde telt voor uw scène zoals berekend op het tabblad **Sondes.** Het aantal sondes bepaalt het aantal simulaties dat in de cloud moet worden uitgevoerd. U niet meer knooppunten opgeven dan er sondes zijn.
12. In dit bericht ziet u de huidige status van de taak of als er fouten in dit tabblad staan, wat die fouten zijn.

U altijd volledige informatie krijgen over actieve taken, compute pools en opslag op de [Azure-portal.](https://portal.azure.com)

Terwijl een taak wordt uitgevoerd, wordt de knop **Bakken** gewijzigd in **Taak annuleren**. Gebruik deze knop om de lopende taak te annuleren. Het annuleren van een taak kan niet ongedaan worden gemaakt, er zijn geen resultaten beschikbaar en er worden nog steeds kosten in rekening gebracht voor elke Azure-rekentijd die vóór annulering wordt gebruikt.

Als je eenmaal bent begonnen met een bak, kun je Unreal sluiten. Afhankelijk van het project, knooppunttype en aantal knooppunten kan een cloudbak enkele uren duren. De status van de baktaak wordt bijgewerkt wanneer u het project herlaadt en het venster Akoestiek opent. Als de taak is voltooid, wordt het uitvoerbestand gedownload.

De Azure-referenties worden veilig opgeslagen op uw lokale machine en gekoppeld aan uw Unreal-project. Ze worden uitsluitend gebruikt om een beveiligde verbinding met Azure tot stand te brengen.

### <a name="estimating-azure-bake-cost"></a><a name="Estimating-bake-cost"></a>Azure-bakkosten schatten

Als u wilt inschatten wat een bepaalde bak kost, neemt u de waarde die wordt weergegeven voor **Geschatte rekenkosten**, wat een duur is, en vermenigvuldigt u dat met de uurkosten in uw lokale valuta van het **vm-knooppunttype** dat u hebt geselecteerd. Het resultaat bevat niet de nodetijd die nodig is om de knooppunten operationeel te krijgen. Als u bijvoorbeeld **Standard_F8s_v2** selecteert voor uw knooppunttype, dat een kostprijs heeft van $ 0,40/uur en de geschatte rekenkosten 3 uur en 57 minuten bedragen, zijn de geschatte kosten voor het uitvoeren van de taak $ 0,40 * ~ 4 uur = ~ $ 1,60. De werkelijke kosten zullen waarschijnlijk een beetje hoger zijn vanwege de extra tijd om de knooppunten aan de slag te krijgen. U de kosten per uur knooppunt vinden op de pagina [Azure Batch Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (selecteer 'Compute optimized' of 'High performance compute' voor de categorie).

### <a name="reviewing-the-bake-results"></a>Het bekijken van de bakresultaten

Controleer na het bakken of de voxels en sondepunten zich op hun verwachte locaties bevinden door de runtime-plug-in uit te voeren.

## <a name="data-files"></a><a name="Data-Files"></a>Gegevensbestanden

Er zijn vier gegevensbestanden gemaakt door deze plugin op verschillende punten. Slechts één daarvan is nodig bij runtime en wordt geplaatst in de content/akoestiek-map van uw project, die automatisch wordt toegevoegd aan het verpakkingspad van uw project. De andere drie bevinden zich in de map Acoustics Data en zijn niet verpakt.

* **[Project]/Config/ProjectAcoustics.cfg**: Dit bestand slaat de gegevens op die u in voert in velden in de gebruikersinterface van de akoestiekmodus. De locatie en de naam van dit bestand kunnen niet worden gewijzigd. Er zijn andere waarden die in dit bestand zijn opgeslagen die van invloed zijn op de bak, maar ze zijn voor gevorderde gebruikers en mogen niet worden gewijzigd.
* **[Project]/Content/Acoustics/[LevelName]\_AcousticsData.ace**: Dit bestand is wat er wordt gemaakt tijdens de baksimulatie en bevat de opzoekgegevens die door de runtime worden gebruikt om de akoestiek van uw scène weer te geven. De locatie en de naam van dit bestand kunnen worden gewijzigd met de velden op het **tabblad Sondes.** Als u de naam van dit bestand wilt wijzigen nadat het is gemaakt, verwijdert u de UAsset uit uw Unreal-project, wijzigt u de naam van het bestand buiten Unreal in Verkenner en importeert u dit bestand opnieuw in Unreal om een nieuwe UAsset te produceren. Het hernoemen van de UAsset op zichzelf zal niet werken.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData.vox**: Dit bestand slaat de voxelized akoestiekgeometrie en de materiaaleigenschappen op. Berekend met de knop **Berekenen** op het tabblad **Sondes.** De locatie en de naam van dit bestand kunnen worden gewijzigd met de velden op het **tabblad Sondes.**
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData\_config.xml**: Dit bestand slaat parameters op die worden berekend met de knop **Berekenen** op het tabblad **Probes.** De locatie en de naam van dit bestand kunnen worden gewijzigd met de velden op het **tabblad Sondes.**

Zorg ervoor dat u het *.ace-bestand dat is gedownload van Azure niet verwijdert. Dit bestand is niet te herstellen, behalve door het opnieuw bakken van de scène.

## <a name="next-steps"></a>Volgende stappen
* Ontdek de [ontwerpbesturingselementen voor Unreal](unreal-workflow.md)
* Ontdek de [ontwerpconcepten van Project Acoustics](design-process.md)

