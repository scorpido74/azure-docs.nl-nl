---
title: Project Acoustics Unity Bake Tutorial
titlesuffix: Azure Cognitive Services
description: Deze tutorial beschrijft akoestiek bakken met Project Acoustics in Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 310decf8053ea16ba46250ba3aabe81c9c254e5e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72243130"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Project Acoustics Unity Bake Tutorial
Deze tutorial beschrijft akoestiek bakken met behulp van Project Acoustics in Unity.

Softwarevereisten:
* [Unity 2018.2+](https://unity3d.com) voor Windows of MacOS
* [Project Acoustics plug-in geïntegreerd in uw Unity project](unity-integration.md) of de Project Acoustics Unity sample [content](unity-quickstart.md)
* *Optioneel:* Een [Azure Batch-account](create-azure-account.md) om bakken te versnellen met behulp van cloud computing

## <a name="open-the-project-acoustics-bake-window"></a>Open het bakvenster van Project Acoustics
Selecteer in Unity De optie **Akoestiek** in het **menu Venster.**

![De Unity-editor met de optie Akoestiek gemarkeerd in het menu Venster](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Een navigatienet maken
Project Acoustics gebruikt een navigatiegaas om listenersondepunten te plaatsen voor simulatie. U de [unity navigation mesh workflow](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)gebruiken. Of u een ander 3D-modelleringspakket gebruiken om uw eigen mesh te ontwerpen.

## <a name="mark-acoustic-scene-objects"></a>Akoestische scèneobjecten markeren
Project Acoustics vertrouwt op twee soorten scèneobjecten voor simulatie:
- De objecten die reflecteren en occlude geluid in de simulatie
- De navigatiemesh voor spelers die de punten van listenersonde in simulatie beperkt

Beide objecttypen worden gemarkeerd met het tabblad **Objecten.**

Omdat het markeren van objecten eenvoudig de componenten *AcousticsGeometry* of *AcousticsNavigation* aan het object toevoegt, u ook de [standaard componentwerkstroom unity](https://docs.unity3d.com/Manual/UsingComponents.html) gebruiken om objecten te markeren of te ontmerken. U alleen mesh renderers en terreinen markeren. Alle andere objecttypen worden genegeerd. De selectievakjes markeren of markeren alle betrokken objecten.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Akoestische occlusie en reflectiegeometrie markeren
Open het tabblad **Objecten** van het venster **Akoestiek.** Markeer objecten als *Akoestiekgeometrie* als ze geluid moeten occlude, reflecteren of absorberen. Akoestiek geometrie kan omvatten dingen zoals grond, muren, daken, ramen en raamglas, tapijten, en grote meubels. U elke willekeurige complexiteit voor deze objecten gebruiken. Omdat de scène is voxelized vóór simulatie, zeer gedetailleerde mazen, zoals bomen die veel bladeren hebben, zijn niet duurder om te bakken dan vereenvoudigde objecten.

Neem geen dingen op die geen invloed mogen hebben op de akoestiek, zoals onzichtbare botsingsmazen.

De transformatie van een object tijdens de berekening van de sonde (via het tabblad **Probes)** wordt in de bakresultaten bevestigd. Als gemarkeerde objecten in de scène later worden verplaatst, moeten sondeberekening en bakken worden vernieuwd.

### <a name="mark-the-navigation-mesh"></a>Het navigatienet markeren
Navigatiemazen die zijn gemaakt via de Unity-workflow worden opgepikt door het Acoustics-systeem. Als u uw eigen mazen wilt gebruiken, markeert u deze op het tabblad **Objecten.**

### <a name="for-reference-the-objects-tab-parts"></a>Voor referentie: de delen van het tabblad Objecten
De delen van de tabbladpagina (afgebeeld na de beschrijvingen) zijn:

1. De knoppen voor tabselectie (met het tabblad **Objecten** geselecteerd). Gebruik deze knoppen om door de verschillende stappen van een akoestiek bak, van links naar rechts.
1. Een korte beschrijving van wat u doen met behulp van dit tabblad.
1. Beschikbare filters voor het hiërarchievenster. Gebruik deze opties om het hiërarchievenster te filteren op objecten van het opgegeven type, zodat u ze eenvoudig markeren. Als u nog niets hebt gemarkeerd voor de akoestiek, zal het selecteren van de laatste twee opties u niets laten zien. Met deze opties u echter objecten vinden nadat ze zijn gemarkeerd.
1. Wanneer er geen objecten zijn geselecteerd, wordt in deze sectie de status van alle objecten in de scène weergegeven.
    * Totaal: het totale aantal actieve, niet-verborgen objecten.
    * Genegeerd: het aantal objecten dat geen mesh-renderers of terreinen zijn.
    * Mesh: het aantal mesh rendererobjecten.
    * Terrein: het aantal terreinobjecten.
    * Geometrie: het aantal net- of terreinobjecten dat is gemarkeerd als **akoestiekgeometrie**.
    * Navigatie: het aantal net- of terreinobjecten dat is gemarkeerd als **Navigatie akoestiek**. Dit nummer bevat niet de Unity NavMesh.
1. Het totale aantal "markable" objecten in de scène, die alleen mesh renderers en terreinen. Gebruik de selectievakjes om deze objecten te markeren (de juiste component toe te voegen) als geometrie of navigatie voor akoestiek.
1. Als er niets is geselecteerd, herinnert deze notitie u eraan om objecten te selecteren om te markeren, indien nodig. U ook een of beide selectievakjes selecteren om alle objecten in de scène te markeren.
1. Wanneer objecten zijn geselecteerd, wordt in deze sectie alleen de status van de geselecteerde objecten weergegeven.
1. Het totale aantal 'markable' geselecteerde objecten. Het selecteren of wissen van de selectievakjes markeert of markeert alleen de geselecteerde objecten.

Als er niets is geselecteerd in uw scène, ziet het tabblad **Objecten** eruit als de volgende afbeelding.

![Het tabblad Akoestiekobjecten met niets geselecteerd](media/objects-tab-no-selection-detail.png)

Als u iets hebt geselecteerd in uw scène of hiërarchievenster, lijkt het tabblad op de volgende afbeelding.

![Het tabblad Akoestiekobjecten met selecties](media/objects-tab-selection-detail.png)

Als sommige objecten zijn gemarkeerd en andere niet, wordt in de juiste selectievakjes een 'gemengde' waarde weergegeven, zoals de volgende afbeelding.

![Het tabblad Akoestiekobjecten met een gemengde selectie pictogrammen gemarkeerd](media/mixed-object-selection-detail.png)

Schakel het selectievakje in om alle items te markeren. Schakel de optie ongedaan om alle objecten de naam te ontzeggen.

Objecten kunnen worden gemarkeerd voor zowel geometrie als navigatie.

## <a name="select-acoustic-materials"></a>Akoestische materialen selecteren
Nadat uw objecten zijn gemarkeerd, selecteert u de knop **Materialen.** Wijs vervolgens akoestische materialen toe. Het materiaalsysteem van Project Acoustics is gekoppeld aan het Unity visuele materialensysteem. Om twee objecten te hebben gescheiden akoestische materialen, moeten ze afzonderlijke visuele materialen hebben.

De akoestische materiaalselectie bepaalt de hoeveelheid geluidsenergie die van elk oppervlak wordt teruggekaatst. Het standaard akoestische materiaal heeft een absorptie die vergelijkbaar is met staal. Project Acoustics stelt materialen voor op basis van de naam van het beeldmateriaal. U het akoestische materiaal **Custom** ook toewijzen aan een materiaal om een instelbare absorptiecoëfficiëntschuifregelaar te activeren.

De nagalmtijd van een bepaald materiaal in een ruimte is omgekeerd gerelateerd aan de absorptiecoëfficiënt. De meeste materialen hebben absorptiewaarden in een bereik van 0,01 tot 0,20. Materialen met absorptiecoëfficiënten buiten dit bereik zijn zeer absorberend.

![Een grafiek toont de negatieve correlatie van de nagalmtijd en de absorptiecoëfficiënt.](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Voor referentie: delen van het tabblad Materialen
![Het tabblad Akoestiekmaterialen in Unity](media/materials-tab-detail.png)
1. Op de knop **Materialen** wordt dit tabblad weergegeven.
2. Een korte beschrijving van wat u doen met behulp van dit tabblad.
3. Wanneer dit selectievakje is ingeschakeld, worden alleen materialen weergegeven die worden gebruikt door objecten die zijn gemarkeerd als **Akoestiekgeometrie.** Anders worden alle materialen die in de scène worden gebruikt, weergegeven.
4. Gebruik deze opties om de volgorde van de opties in een menu in de kolom **Akoestiek** (#6) te wijzigen. Sorteer akoestische materialen op **naam** of op **Absorptiviteit**, van laag naar hoog.
5. Een alfabetisch gesorteerde lijst met materialen die in de scène worden gebruikt. Als het selectievakje **Alleen gemarkeerd weergeven** is ingeschakeld (#3) worden alleen materialen weergegeven die worden gebruikt door objecten die zijn gemarkeerd als **Akoestiekgeometrie.** Selecteer hier een materiaal om alle objecten in de scène te selecteren die dat materiaal gebruiken.
6. Het akoestische materiaal waaraan het scènemateriaal is toegewezen. Selecteer een item om het akoestischmateriaal te wijzigen dat aan dat scènemateriaal is toegewezen. Als u de sorteervolgorde van deze menu's wilt wijzigen, gebruikt u de opties **Akoestiek sorteren op** (#4).
7. De akoestische absorptiecoëfficiënt van het materiaal dat in de kolom links is geselecteerd (#6). Een waarde van 0 betekent perfect reflecterend (geen absorptie), terwijl 1 betekent perfect absorptieve (geen reflectie). De absorptiecoëfficiënt kan niet worden gewijzigd, tenzij het geselecteerde materiaal **aangepast is.**
8. Voor een materiaal dat als **Aangepast is**gemarkeerd, wordt de schuifregelaar geactiveerd. U de schuifregelaar verplaatsen of een waarde typen om een absorptiecoëfficiënt toe te wijzen.

## <a name="calculate-and-review-listener-probe-locations"></a>Locaties voor listenersonde berekenen en controleren
Nadat u de materialen hebt toegewezen, schakelt u over naar het tabblad **Sondes.** Selecteer **Berekenen** om listenersondepunten voor simulatie te plaatsen.

### <a name="what-the-calculate-button-does"></a>Wat de knop 'Berekenen' doet
De knop **Berekenen** gebruikt de geselecteerde akoestische scènegeometrie om uw scène voor te bereiden op simulatie:

- Het neemt de geometrie van de scène mazen en berekent een *voxel volume*. De voxel volume is een volume van uw hele scène bestaat uit kleine kubieke "voxels." Voxel grootte wordt bepaald door de simulatie frequentie, die wordt gecontroleerd door de **simulatie resolutie** instelling. Elke voxel is gemarkeerd als "open air" of met scènegeometrie. Als een voxel geometrie bevat, wordt de voxel getagd met de absorptiecoëfficiënt van het materiaal dat aan die geometrie is toegewezen.
- Het gebruikt de navigatiemazen om luisteraarsondepunten te plaatsen. Het algoritme balanceert de concurrerende zorgen van ruimtelijke dekking en simulatietijd en bestandsgrootte. Het doel is ervoor te zorgen dat smalle gangen en kleine ruimtes altijd enige dekking hebben. De typische tellingen van het sondepunt variëren van 100 voor kleine scènes aan een paar duizend voor grote scènes.

Afhankelijk van de grootte van uw scène en de snelheid van uw machine, kunnen deze berekeningen enkele minuten duren.

### <a name="review-voxel-and-probe-placement"></a>Voxel- en probe-plaatsing bekijken
Bekijk een voorbeeld van de voxel-gegevens en de locaties van probe-punten om er zeker van te zijn dat u klaar bent om uw scène te bakken. Een onvolledig navigatiegaas of ontbrekende of extra akoestische geometrie is meestal gemakkelijk zichtbaar in de preview. Plaatsen van voxel en sonde inschakelen of uitschakelen met behulp van het **menu Gizmos.**

![Het Gizmos menu in Unity](media/gizmos-menu.png)

Voxels die akoestische geometrie bevatten worden weergegeven als groene kubussen. Verken je scène en controleer of alles wat geometrie moet zijn voxels heeft. De scène camera moet binnen ongeveer 5 meter van het object voor de voxels te laten zien.

Als je voxels vergelijkt met grove resolutie versus fijne resolutie, zul je zien dat de grove voxels twee keer zo groot zijn.

![De grove voxels preview in de Unity editor](media/voxel-cubes-preview.png)

Simulatieresultaten worden geïnterpoleerd tussen locatielocaties van listenersondepunten tijdens runtime. Controleer of er sondepunten in de buurt van alle plaatsen waar de speler wordt verwacht gaan in de scène.

![De sondes preview in de Unity editor](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Zorg met scène hernamen
De scènenaam wordt gebruikt om de scène te verbinden met bestanden die de plaatsing van het sondepunt en voxelisatie opslaan. Als u de naam van de scène wijzigt nadat sondepunten zijn berekend, gaan de materiaaltoewijzings- en plaatsingsgegevens verloren en moeten ze opnieuw worden uitgevoerd.

### <a name="for-reference-parts-of-the-probes-tab"></a>Voor referentie: delen van het tabblad Probes
![Het tabblad Akoestieksondes in Unity](media/probes-tab-detail.png)

1. De **knop Probes** brengt dit tabblad naar boven.
2. Een korte beschrijving van wat u doen op dit tabblad.
3. Gebruik deze opties om een grove of fijne simulatieresolutie in te stellen. Grof is sneller, maar er zijn afwegingen. Zie Resolutie [bakken](bake-resolution.md)voor meer informatie.
4. Hiermee geeft u aan waar u gegevensbestanden voor akoestiek moet plaatsen. Selecteer de knop "**...**" om toegang te krijgen tot een mapkiezer. De standaardlocatie is *Assets/AcousticsData*. Op deze locatie wordt ook een submap *editor* gemaakt. Zie [Gegevensbestanden die door het bakproces](#Data-Files)zijn toegevoegd, later in dit artikel voor meer informatie.
5. Het hier opgegeven voorvoegsel wordt gebruikt om de gegevensbestanden voor deze scène een naam te geven. De standaardinstelling is "Acoustics_*[Scènenaam]*".
6. Nadat de sondes zijn berekend, zijn de besturingselementen die we zojuist hebben beschreven uitgeschakeld. Selecteer de knop **Wissen** om de berekeningen te wissen en schakel de besturingselementen in, zodat u opnieuw berekenen met nieuwe instellingen.
7. Selecteer **Berekenen** om de scène te voxelize en bereken sondepuntlocaties. De berekening gebeurt lokaal op uw machine. Het moet worden gedaan voor het doen van een bak.

In deze versie van Project Acoustics kunnen sondes niet handmatig worden geplaatst. Gebruik het geautomatiseerde proces op het tabblad **Probes.**

Zie [Resolutie bakken voor](bake-resolution.md)meer informatie over grof versus fijn resolutie.

## <a name="bake-your-scene-by-using-azure-batch"></a>Uw scène bakken met Azure Batch
U uw scène op een compute cluster in de cloud bakken met behulp van de Azure Batch-service. De Plug-in Project Acoustics Unity maakt rechtstreeks verbinding met Azure Batch om een Azure Batch-cluster voor elke bak te instantiëren, beheren en afbreken. Voer op het tabblad **Bakken** uw Azure-referenties in, selecteer een clustermachinetype en -grootte en selecteer **Bakken.**

### <a name="for-reference-parts-of-the-bake-tab"></a>Voor referentie: Delen van het tabblad Bakken
![Het tabblad Akoestiek bakken in Unity](media/bake-tab-details.png)

1. Met de knop **Bakken** wordt dit tabblad weergegeven.
2. Een korte beschrijving van wat u doen op deze pagina.
3. Voer uw Azure-referenties in deze velden in nadat uw Azure-account is gemaakt. Zie [Een Azure Batch-account maken voor](create-azure-account.md)meer informatie .
4. Het veld Docker-afbeeldingstag voor de gereedschapsset Akoestiek.
5. Hiermee opent u de Azure-portal om uw abonnementen te beheren, het gebruik te controleren en factureringsgegevens te bekijken.
6. Specifies the Azure Batch compute node type to use for calculation. Het knooppunttype moet worden ondersteund door uw Azure-datacenterlocatie. Als je het niet zeker weet, ga dan weg als **Standard_F8s_v2.**
7. Het aantal knooppunten dat u voor de berekening moet gebruiken. Dit getal beïnvloedt de baktijd. Het wordt beperkt door uw Azure Batch-kerntoewijzing. De standaardtoewijzing maakt alleen twee 8-core nodes of één 16-core knooppunt mogelijk, maar het kan worden uitgebreid. Zie [Een Azure Batch-account maken](create-azure-account.md)voor meer informatie over kerntoewijzingsbeperkingen.
8. Schakel dit selectievakje in om de compute pool te configureren om [knooppunten met een lage prioriteit](https://docs.microsoft.com/azure/batch/batch-low-pri-vms)te gebruiken. Compute nodes met lage prioriteit hebben veel lagere kosten. Maar ze kunnen niet altijd beschikbaar zijn of kunnen worden vooruitgelopen op elk gewenst moment.
9. De sonde telt voor uw scène zoals berekend op het tabblad **Sondes.** Het aantal sondes bepaalt het aantal simulaties dat in de cloud moet worden uitgevoerd. U niet meer knooppunten opgeven dan er sondes zijn.
10. Een schatting van de tijd die uw taak in beslag neemt om in de cloud uit te voeren, met uitzondering van de opstarttijd van het knooppunt. Nadat de taak is gestart, toont dit veld een schatting van hoe lang het duurt voordat u de resultaten terugkrijgt.
11. De totale hoeveelheid rekentijd die nodig is om de simulaties uit te voeren. Deze waarde is de totale hoeveelheid gegevensverzameling die in Azure wordt gebruikt. Zie [Azure bakkosten schatten](#Estimating-bake-cost) later in dit artikel voor meer informatie.
12. Dit bericht vertelt u waar de resultaten van de bak zal worden opgeslagen wanneer de taak is voltooid.
13. *(Geavanceerd gebruik alleen:)* Deze knop dwingt Unity om te vergeten over een bak die u ingediend. Als u de resultaten bijvoorbeeld hebt gedownload met een andere machine, selecteert u de knop **Status wissen** om die taak te vergeten. Het resultaatbestand, wanneer gereed, wordt *niet* gedownload. *Dit is niet hetzelfde als het annuleren van de taak. De taak, indien uitgevoerd, blijft worden uitgevoerd in de cloud.*
14. Selecteer deze knop om de bak naar de cloud te verzenden. Terwijl een taak wordt uitgevoerd, wordt deze knop **Taak annuleren**.
15. Selecteer deze knop om u voor te bereiden op het verwerken van [akoestieksimulaties op uw pc.](#Local-bake)
16. Dit gebied toont de status van de bak. Wanneer de bak is voltooid, toont het "Gedownload."

U altijd volledige informatie krijgen over actieve taken, compute pools en opslag in de [Azure-portal.](https://portal.azure.com)

Terwijl een taak wordt uitgevoerd, wordt het label Van de knop **Bakken** gewijzigd in **Taak annuleren**. Gebruik deze knop om de lopende taak te annuleren. U wordt gevraagd dit te bevestigen. Het annuleren van een taak kan niet ongedaan worden gemaakt. Wanneer u annuleert, zijn er geen resultaten beschikbaar, maar er worden nog steeds kosten in rekening gebracht voor elke gebruikte Azure-rekentijd.

Nadat je bent begonnen met bakken, kun je Unity sluiten. Afhankelijk van het project, knooppunttype en aantal knooppunten kan een cloudbak enkele uren duren. De status van de baktaak wordt bijgewerkt wanneer u het project herlaadt en het venster Akoestiek opent. Als de taak is voltooid, wordt het uitvoerbestand gedownload.

Voor beveiliging worden Azure-referenties opgeslagen op uw lokale machine en gekoppeld aan uw Unity-editor. Ze worden alleen gebruikt om een beveiligde verbinding met Azure tot stand te brengen.

## <a name="find-the-status-of-a-running-job-on-the-azure-portal"></a>De status van een lopende taak zoeken op de Azure-portal

1. Zoek de bakbaan-id op het tabblad **Bakken.**

    ![De Unity bake job ID gemarkeerd op het tabblad Bakken](media/unity-job-id.png)  

2. Open de [Azure-portal,](https://portal.azure.com)ga naar het Batch-account dat is gebruikt voor het bakken en selecteer **Taken**.

    ![De koppeling Taken in de Azure-portal](media/azure-batch-jobs.png)  

3. Zoek naar de taak-ID in de lijst met vacatures.

   ![De status van baktaak gemarkeerd in de Azure-portal](media/azure-bake-job-status.png)  

4. Selecteer de taak-id om de status van de gerelateerde taken en de algehele taakstatus te bekijken.

   ![De status van de baktaak](media/azure-batch-task-state.png)  


### <a name="estimate-azure-bake-cost"></a><a name="Estimating-bake-cost"></a>Schatting Azure bakkosten

Als u wilt inschatten wat een bak kost, begint u met de **geschatte compute cost-waarde,** die een duur is. Vermenigvuldig die waarde met de uurkosten in uw lokale valuta voor het **vm-knooppunttype** dat u selecteert. Houd er rekening mee dat het resultaat niet de nodetijd bevat die nodig is om de knooppunten operationeel te krijgen.

Stel dat u **Standard_F8s_v2** selecteert voor uw knooppunttype, dat een kostprijs heeft van $ 0,40 per uur. Als de **geschatte rekenkosten** 3 uur en 57 minuten bedragen, zijn de geschatte kosten voor het uitvoeren van de taak $ 0,40 * ~ 4 uur = ~ $ 1,60. De werkelijke kosten zullen waarschijnlijk een beetje hoger zijn vanwege de extra tijd om de knooppunten aan de slag te krijgen.

Zoek de kosten voor knooppunten per uur tegen [Azure Batch-prijzen.](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (Selecteer **Compute geoptimaliseerd** of Hoge prestaties **berekenen** als de categorie.)

## <a name="bake-your-scene-on-your-pc"></a><a name="Local-bake"></a>Bak je scène op je pc
U uw scène ook bakken op uw eigen pc. Deze methode kan handig zijn om te experimenteren met akoestiek voor kleine scènes voordat u een Azure Batch-account maakt. Maar merk op dat de lokale akoestiek simulatie kan een lange tijd duren, afhankelijk van de grootte van de scène.

### <a name="minimum-hardware-requirements"></a>Minimale hardwarevereisten
* Een x86-64 processor met minimaal 8 cores en 32 GB RAM
* [Hyper-V ingeschakeld](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) om Docker uit te voeren

Als voorbeeld, in onze testen op een 8-core machine, Intel Xeon E5-1660 @ 3 GHz en 32 GB RAM:
* Een kleine scène met 100 sondes duurde ongeveer 2 uur voor een grove bakken of 32 uur voor een fijne bak.
* Een middelgrote scène met 1.000 sondes duurde ongeveer 20 uur voor een grove bak of 21 dagen voor een fijne bak.

### <a name="set-up-docker"></a>Docker instellen
Installeer en configureer Docker op de pc die de simulatie zal verwerken:
1. [Docker-bureaublad installeren](https://www.docker.com/products/docker-desktop).
2. Open Docker-instellingen, ga naar **Geavanceerd**en configureer de bronnen voor ten minste 8 GB RAM. Hoe meer CPU's u toewijzen aan Docker, hoe sneller de bak zal worden voltooid.  
![Voorbeelddocker-instellingen](media/docker-settings.png)
1. Ga naar **Gedeelde stations**en schakel delen in voor het station dat wordt gebruikt voor verwerking.  
![Opties voor gedeelde stations docker](media/docker-shared-drives.png)

### <a name="run-the-local-bake"></a>Voer de lokale bak
1. Selecteer de knop **Lokaal bakken voorbereiden** op het tabblad **Bakken.** Selecteer vervolgens een maplocatie om de invoerbestanden en uitvoeringsscripts op te slaan. U de bak vervolgens op elke machine uitvoeren zolang deze voldoet aan de minimale hardwarevereisten en docker installeert door de map naar die machine te kopiëren.
2. Voer het *script runlocalbake.bat* op Windows of het *runlocalbake.sh* script op MacOS uit om de simulatie te starten. Dit script haalt de Project Acoustics Docker afbeelding op met de toolset die nodig is voor simulatieverwerking en start de simulatie.
3. Nadat de simulatie is voltooid, kopieert u het resulterende *.ace-bestand* terug naar uw Unity-project. Als u ervoor wilt zorgen dat Unity het als een binair bestand herkent, sluit u ".bytes" toe aan de bestandsextensie (bijvoorbeeld 'Scene1.ace.bytes'). De gedetailleerde logboeken voor de simulatie worden opgeslagen in *AcousticsLog.txt.* Als u problemen ondervindt, inspecteert u dit bestand om het probleem te diagnosticeren.

## <a name="data-files-added-by-the-bake-process"></a><a name="Data-Files"></a>Gegevensbestanden die door het bakproces worden toegevoegd

De volgende vier gegevensbestanden worden gemaakt tijdens het bakproces. Een bevat de simulatie resultaten en schepen met uw titel. De anderen slaan Unity-editorgerelateerde gegevens op.

Simulatieresultaat:
* *Assets/AcousticsData/Acoustics\_[SceneName].ace.bytes*: Dit bestand is de opzoektabel runtime. Het bevat de simulatieresultaten en voxelized akoestische scèneelementen. U de naam en locatie van dit bestand wijzigen op het tabblad **Sondes.**

   *Zorg ervoor dat u het simulatieresultaatbestand niet verwijdert. Het is niet terug te winnen, behalve door het opnieuw bakken van de scène.*

Editor-gegevensbestanden:
* *Assets/Editor/[SceneName]\_AcousticsParameters.asset:* In dit bestand worden de gegevens opgeslagen die u in voert in velden in de gebruikersinterface akoestiek. U de naam en locatie van dit bestand niet wijzigen.
* *Assets/AcousticsData/Editor/Acoustics_[SceneName].vox:* Dit bestand slaat de voxelized akoestiekgeometrie en de materiaaleigenschappen op die worden berekend wanneer u de knop **Berekenen** selecteert op het tabblad **Probes.** U de naam en locatie van dit bestand wijzigen op het tabblad **Sondes.**
* *Assets/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml*: Dit bestand slaat simulatieparameters op die worden berekend wanneer u **Berekenen**selecteert. U de naam en locatie van dit bestand wijzigen op het tabblad **Sondes.**

## <a name="set-up-the-acoustics-lookup-table"></a>De opzoektafel voor akoestiek instellen
Sleep de **Prefab Project Acoustics** vanuit het projectpaneel naar uw scène:

![De Akoestiek prefab in Unity](media/acoustics-prefab.png)

Selecteer het spelobject **ProjectAcoustics** en ga naar het controlepaneel. Geef de locatie van het afbakresultaat op (het .ace-bestand, in *Assets/AcousticsData):* Sleep het in het script Akoestiekbeheer of selecteer de cirkelknop naast het tekstvak.

![De Acoustics Manager prefab in Unity](media/acoustics-manager.png)

## <a name="next-steps"></a>Volgende stappen
* Ontdek de [Unity-ontwerpbesturingselementen.](unity-workflow.md)
* Ontdek [ontwerpconcepten van Project Acoustics.](design-process.md)
