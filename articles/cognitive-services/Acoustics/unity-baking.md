---
title: Zelf studie voor het maken van de project-unit-eenheid
titlesuffix: Azure Cognitive Services
description: In deze zelf studie worden akoestische Baking met project akoestische in eenheid beschreven.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243130"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Zelf studie voor het maken van de project-unit-eenheid
In deze zelf studie worden akoestische Baking met behulp van project akoestische in eenheid.

Software vereisten:
* [Unity 2018.2 +](https://unity3d.com) voor Windows of MacOS
* [Invoeg toepassing voor project akoestische, geïntegreerd in uw Unity-project](unity-integration.md) of de [voorbeeld inhoud van de project akoestische unit](unity-quickstart.md)
* *Optioneel:* Een [Azure batch account](create-azure-account.md) om Bakes te versnellen door gebruik te maken van Cloud Computing

## <a name="open-the-project-acoustics-bake-window"></a>Open het maken-venster voor project akoestische
In eenheid selecteert u **akoestische** in het menu **venster** .

![De eenheids-editor met de geluids optie is gemarkeerd in het menu venster](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Een navigatie-net maken
Project akoestische maakt gebruik van een navigatie net om listener-test punten te plaatsen voor simulatie. U kunt de [werk stroom unit navigatie net](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)gebruiken. U kunt ook een ander 3D-model pakket gebruiken om uw eigen net te ontwerpen.

## <a name="mark-acoustic-scene-objects"></a>Akoestische scène objecten markeren
De geluids elementen van het project zijn afhankelijk van twee soorten scène objecten voor simulatie:
- De objecten die in de simulatie overeenkomen met en occlude-geluid
- Het NetPlayer-navigatie net waarmee listener-test punten in simulatie worden beperkt

Beide object typen worden gemarkeerd met behulp van het tabblad **objecten** .

Omdat het markeren van objecten de onderdelen *AcousticsGeometry* of *AcousticsNavigation* aan het object toevoegt, kunt u ook de [werk stroom van het standaard eenheids onderdeel](https://docs.unity3d.com/Manual/UsingComponents.html) gebruiken om objecten te markeren of te markeren. U kunt alleen netrenderers en terreinsen markeren. Alle andere object typen worden genegeerd. Schakel de selectie vakjes alle betrokken objecten in of uit.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Akoestische bedekking-en reflectie geometrie markeren
Open het tabblad **objecten** van het **akoestische** venster. Markeer objecten als *akoestische geometrie* als ze occlude, reflectie of absorberende klank moeten hebben. De geometrie van de akoestische kan zaken bevatten zoals grond, wanden, daken, Windows en venster glas, vloeren en grote meubelen. U kunt elk wille keurig niveau van complexiteit voor deze objecten gebruiken. Omdat de scène voxelized is vóór simulatie, worden zeer gedetailleerde netten, zoals structuren met veel bladeren, niet meer kostbaar voor maken dan vereenvoudigde objecten.

Neem geen dingen op die niet van invloed zijn op de akoestische, zoals onzichtbare botsingen.

De trans formatie van een object tijdens de test (via het tabblad **tests** ) wordt opgelost in de maken-resultaten. Als alle gemarkeerde objecten in de scène later worden verplaatst, moeten de test berekening en Baking opnieuw worden uitgevoerd.

### <a name="mark-the-navigation-mesh"></a>Het navigatie net markeren
Navigatie netten die zijn gemaakt via de eenheids werk stroom worden door het akoestische systeem opgehaald. Als u uw eigen netten wilt gebruiken, markeert u deze op het tabblad **objecten** .

### <a name="for-reference-the-objects-tab-parts"></a>Ter referentie: de onderdelen op het tabblad objecten
De onderdelen van het tabblad (in afbeelding van de beschrijvingen) zijn:

1. De knoppen voor het selectie-tabblad (met het tabblad **objecten** geselecteerd). Gebruik deze knoppen om door de verschillende stappen van een akoestische maken te scha kelen, van links naar rechts.
1. Een korte beschrijving van wat u kunt doen met behulp van dit tabblad.
1. Beschik bare filters voor het hiërarchie venster. Gebruik deze opties om het hiërarchie venster te filteren op objecten van het opgegeven type, zodat u ze eenvoudig kunt markeren. Als u nog geen geluid hebt gemarkeerd, dan worden de laatste twee opties weer gegeven. Met deze opties kunt u echter objecten vinden nadat ze zijn gemarkeerd.
1. Als er geen objecten zijn geselecteerd, wordt in deze sectie de status van alle objecten in de scène weer gegeven.
    * Totaal: het totale aantal actieve, niet-verborgen objecten.
    * Genegeerd: het aantal objecten dat geen mesh-renderers of terrein is.
    * Net: het aantal mesh-renderer-objecten.
    * Terrein: het aantal terrein objecten.
    * Geometrie: het aantal net-of terrein objecten dat is gemarkeerd als **akoestische geometrie**.
    * Navigatie: het aantal net-of terrein objecten dat is gemarkeerd als **akoestische navigatie**. Dit nummer omvat niet de eenheids NavMesh unit.
1. Het totale aantal ' Markeringen ' objecten in de scène, die alleen netrenderers en terrein zijn. Gebruik de selectie vakjes om deze objecten als geometrie of navigatie voor akoestische te markeren (Voeg het juiste onderdeel toe aan).
1. Als er niets is geselecteerd, wordt in deze notitie aan u herinnerd dat u zo nodig objecten kunt selecteren voor markeren. U kunt ook een of beide selectie vakjes inschakelen om alle objecten in de scène te markeren.
1. Als objecten zijn geselecteerd, wordt in deze sectie de status van alleen de geselecteerde objecten weer gegeven.
1. Het totaal aantal geselecteerde objecten dat kan worden gemarkeerd. Als u de selectie vakjes inschakelt of uitschakelt, worden alleen de geselecteerde objecten gemarkeerd.

Als er niets in uw scène is geselecteerd, ziet het tabblad **objecten** eruit als in de volgende afbeelding.

![Het tabblad akoestische objecten waarbij niets is geselecteerd](media/objects-tab-no-selection-detail.png)

Als er iets in uw scène-of hiërarchie venster is geselecteerd, ziet het tabblad eruit als in de volgende afbeelding.

![Het tabblad akoestische objecten met selecties](media/objects-tab-selection-detail.png)

Als sommige objecten zijn gemarkeerd en andere niet, wordt in de juiste selectie vakjes een ' gemengde ' waarde weer gegeven, zoals in de volgende afbeelding.

![Het tabblad akoestische objecten met een gemengde selectie van gemarkeerde pictogrammen](media/mixed-object-selection-detail.png)

Schakel het selectie vakje in om alle items te markeren. Schakel het selectie vakje uit om alle objecten op te heffen.

Objecten kunnen worden gemarkeerd voor geometrie en navigatie.

## <a name="select-acoustic-materials"></a>Akoestische materialen selecteren
Nadat uw objecten zijn gemarkeerd, selecteert u de knop **materialen** . Wijs vervolgens geluids materiaal toe. Het systeem voor de geluids elementen van het project is gekoppeld aan het fysieke materiaal systeem van unit. Als twee objecten afzonderlijke akoestische materialen hebben, moeten ze beschikken over afzonderlijke Visual-materialen.

De selectie van het akoestische materiaal bepaalt de hoeveelheid geluids energie die van elk Opper vlak wordt weer gegeven. Het standaard geluids materiaal heeft een absorptie die vergelijkbaar is met staal. In Project akoestische worden materialen voorgesteld op basis van de naam van het visuele element. U kunt ook het akoestische materiaal **aangepast** aan een materiaal toewijzen om een aanpas bare schuif regelaar voor absorptie coëfficiënt te activeren.

De reverberation tijd van een bepaald materiaal in een kamer is inkerend gerelateerd aan de absorptie coëfficiënt. De meeste materialen hebben absorptie waarden in een bereik van 0,01 tot 0,20. Materialen met absorptie coëfficiënten buiten dit bereik zijn zeer absorberend.

![In een grafiek ziet u de negatieve correlatie van de reverberation tijd en de absorptie coëfficiënt.](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Ter referentie: onderdelen van het tabblad materialen
![Het tabblad akoestische materialen in eenheid](media/materials-tab-detail.png)
1. Op de knop **materialen** wordt dit tabblad weer gegeven.
2. Een korte beschrijving van wat u kunt doen met behulp van dit tabblad.
3. Als dit selectie vakje is ingeschakeld, worden alleen materialen weer gegeven die worden gebruikt door objecten die zijn gemarkeerd als **akoestische geometrie** . Anders worden alle materialen vermeld die in de scène worden gebruikt.
4. Met deze opties kunt u de volg orde van de opties in een menu in de **akoestische** kolom (#6) wijzigen. U kunt akoestische materialen sorteren op **naam** of op **absorptivity**, van laag naar hoog.
5. Een alfabetisch gesorteerde lijst met materialen die in de scène worden gebruikt. Als het selectie vakje **alleen gemarkeerde items weer geven** is geselecteerd (#3), worden alleen materialen weer gegeven die worden gebruikt door objecten die zijn gemarkeerd als **akoestische geometrie** . Selecteer hier een materiaal om alle objecten in de scène te selecteren die dat materiaal gebruiken.
6. Het akoestische materiaal waaraan het scène materiaal is toegewezen. Selecteer een item om het akoestische materiaal te wijzigen dat aan dit scène materiaal is toegewezen. Als u de sorteer volgorde van deze menu's wilt wijzigen, gebruikt u de opties voor het **sorteren van akoestische** (#4).
7. De akoestische absorptie coëfficiënt van het materiaal dat is geselecteerd in de kolom links (#6). De waarde 0 betekent perfect reflectie (geen absorptie), terwijl 1 betekent perfect absorptive (geen reflectie). De absorptie coëfficiënt kan alleen worden gewijzigd als het geselecteerde materiaal **aangepast is.**
8. De schuif regelaar wordt geactiveerd voor een materiaal dat is gemarkeerd als **aangepast**. U kunt de schuif regelaar verplaatsen of een waarde typen om een absorptie coëfficiënt toe te wijzen.

## <a name="calculate-and-review-listener-probe-locations"></a>Test locaties voor listeners berekenen en controleren
Nadat u de materialen hebt toegewezen, gaat u naar het tabblad **probe** . Selecteer **berekenen** om listener-test punten te plaatsen voor simulatie.

### <a name="what-the-calculate-button-does"></a>Wat is de knop berekenen?
De knop **berekenen** maakt gebruik van de geselecteerde akoestische scène geometrie om uw scène voor te bereiden voor simulatie:

- Het neemt de geometrie van de scène netten en berekent een *Voxel-volume*. Het Voxel-volume is een volume van uw hele scène, bestaande uit een klein kubieke ' voxels '. De grootte van de Voxel wordt bepaald door de simulatie frequentie, die wordt bepaald door de instelling voor de **simulatie oplossing** . Elke Voxel is gemarkeerd als ' open lucht ' of met scène geometrie. Als een Voxel geometrie bevat, wordt de Voxel gelabeld met de absorptie coëfficiënt van het materiaal dat is toegewezen aan die geometrie.
- De navigatie netten worden gebruikt om listener-test punten te plaatsen. Het algoritme vergelijkt de concurrerende bezorgdheid van ruimtelijke dekking en simulatie tijd en bestands grootte. Het is erop gericht om ervoor te zorgen dat smalle gangen en kleine ruimten altijd een zekere dekking hebben. Typische test punt aantallen variëren van 100 voor kleine scènes tot een paar duizend voor grote scènes.

Afhankelijk van de grootte van uw scène en de snelheid van uw computer, kan het enkele minuten duren voordat deze berekeningen worden uitgevoerd.

### <a name="review-voxel-and-probe-placement"></a>Voxel en test plaatsing controleren
Bekijk een voor beeld van de Voxel-gegevens en-test punt locaties om ervoor te zorgen dat u klaar bent om uw scène te maken. Een onvolledig navigatie-net of ontbrekende of extra akoestische geometrie is doorgaans eenvoudig zichtbaar in de preview. Schakel Voxel en test plaatsing in of uit met behulp van het menu **Gizmos** .

![Het Gizmos-menu in eenheid](media/gizmos-menu.png)

Voxels die akoestische geometrie bevatten, worden weer gegeven als groene kubussen. Verken uw scène en controleer of alles dat geometrie moet zijn, voxels heeft. De scène camera moet binnen ongeveer 5 meters van het object liggen om de voxels weer te geven.

Als u voxels vergelijkt die zijn gemaakt met ruwe resolutie en een nauw keurige resolutie, ziet u dat de ruwe voxels twee maal zo groot zijn.

![Het ruwe voxels-voor beeld in de Unity-editor](media/voxel-cubes-preview.png)

Simulatie resultaten worden in runtime geinterpolatie tussen de locaties van de listener-test punten. Controleer of er test punten aanwezig zijn in de buurt van locaties waar de speler naar verwachting in de scène gaat.

![De proef versie van de eenheids editor](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Wees voorzichtig met het wijzigen van de namen van scènes
De scène naam wordt gebruikt om de scène te verbinden met bestanden die de plaatsing van het test punt en de voxelization opslaan. Als u de naam van de scène wijzigt nadat de test punten zijn berekend, gaan de toewijzings-en plaatsings gegevens van de materialen verloren en moeten deze opnieuw worden uitgevoerd.

### <a name="for-reference-parts-of-the-probes-tab"></a>Ter referentie: delen van het tabblad tests
![Het tabblad akoestische tests in eenheid](media/probes-tab-detail.png)

1. Met de knop **probe** wordt dit tabblad geopend.
2. Een korte beschrijving van wat u op dit tabblad kunt doen.
3. Gebruik deze opties voor het instellen van grove of nauw keurige simulatie. Grof is sneller, maar er zijn afwegingen. Zie [maken-oplossing](bake-resolution.md)voor meer informatie.
4. Hiermee geeft u op waar geluids gegevensbestand moet worden geplaatst. Selecteer de knop ' **...** ' om toegang te krijgen tot een map kiezen. De standaard locatie is *assets/AcousticsData*. Er wordt ook een *Editor* -submap gemaakt op deze locatie. Zie [gegevens bestanden die door het maken-proces zijn toegevoegd](#Data-Files)verderop in dit artikel voor meer informatie.
5. Het voor voegsel dat hier wordt opgegeven, wordt gebruikt om de gegevens bestanden voor deze scène een naam te geven. De standaard waarde is Acoustics_ *[scène naam]* .
6. Nadat de tests zijn berekend, worden de besturings elementen die we zojuist hebben beschreven, uitgeschakeld. Selecteer de knop **wissen** om de berekeningen te wissen en de besturings elementen in te scha kelen, zodat u opnieuw kunt berekenen met nieuwe instellingen.
7. Selecteer **berekenen** om de scène te voxelize en de locaties van het test punt te berekenen. De berekening wordt lokaal op uw computer uitgevoerd. U moet dit doen voordat u een maken uitvoert.

In deze versie van project akoestische kunnen tests niet hand matig worden geplaatst. Gebruik het geautomatiseerde proces op het tabblad **tests** .

Zie [maken Resolution](bake-resolution.md)(Engelstalig) voor meer informatie over grove en kleine oplossingen.

## <a name="bake-your-scene-by-using-azure-batch"></a>Maken uw scène met behulp van Azure Batch
U kunt uw scène in een berekenings cluster in de Cloud maken door de Azure Batch-service te gebruiken. De invoeg toepassing voor de project akoestische unit-eenheid maakt rechtstreeks verbinding met Azure Batch om een Azure Batch cluster te instantiëren, te beheren en te verbreken voor elke maken. Voer uw Azure-referenties in op het tabblad **maken** , selecteer een type en grootte voor de cluster machine en selecteer vervolgens **maken**.

### <a name="for-reference-parts-of-the-bake-tab"></a>Ter referentie: delen van het tabblad maken
![Het tabblad akoestische maken in eenheid](media/bake-tab-details.png)

1. Op de knop **maken** wordt dit tabblad weer gegeven.
2. Een korte beschrijving van wat u op deze pagina kunt doen.
3. Voer uw Azure-referenties in deze velden in, nadat u uw Azure-account hebt gemaakt. Zie [een Azure batch-account maken](create-azure-account.md)voor meer informatie.
4. Het veld docker-code voor de akoestische hulp programmaset.
5. Hiermee opent u de Azure Portal voor het beheren van uw abonnementen, het controleren van het gebruik en het weer geven van facturerings gegevens.
6. Hiermee geeft u het type van het berekenings knooppunt Azure Batch moet worden gebruikt voor de berekening. Het knooppunt type moet worden ondersteund door de locatie van uw Azure-Data Center. Als u het niet zeker weet, laat u dan **Standard_F8s_v2**.
7. Het aantal knoop punten dat moet worden gebruikt voor de berekening. Dit getal is van invloed op de maken-tijd. Het is beperkt door uw Azure Batch core-toewijzing. De standaard toewijzing is alleen toegestaan voor twee 8-kern knooppunten of een 1 16-kern knoop punt, maar kan worden uitgebreid. Zie [een Azure batch-account maken](create-azure-account.md)voor meer informatie over kern toewijzings beperkingen.
8. Schakel dit selectie vakje in om uw reken groep te configureren voor het gebruik van [knoop punten met een lage prioriteit](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Reken knooppunten met lage prioriteit hebben veel lagere kosten. Maar ze zijn mogelijk niet altijd beschikbaar of kunnen op elk gewenst moment worden voorranglen.
9. Het aantal tests voor uw scène zoals berekend op het tabblad **tests** . Het aantal tests bepaalt het aantal simulaties dat moet worden uitgevoerd in de Cloud. U kunt niet meer knoop punten opgeven dan er tests zijn.
10. Een schatting van de tijd die uw taak nodig heeft om uit te voeren in de Cloud, met uitzonde ring van de opstart tijd van het knoop punt. Nadat de taak is uitgevoerd, wordt in dit veld een schatting weer gegeven van hoe lang tot u de resultaten terugkeert.
11. De totale hoeveelheid computer tijd die nodig is om de simulaties uit te voeren. Deze waarde is het totale aantal reken tijden van het knoop punt dat wordt gebruikt in Azure. Zie voor meer informatie de [schatting van Azure maken-kosten](#Estimating-bake-cost) verderop in dit artikel.
12. Dit bericht geeft aan waar de resultaten van de maken worden opgeslagen wanneer de taak is voltooid.
13. *(Alleen Geavanceerd gebruiken:)* met deze knop wordt de eenheid gedwongen om een maken die u hebt ingediend, te verg eten. Als u bijvoorbeeld de resultaten hebt gedownload met behulp van een andere machine, selecteert u de knop **status wissen** om de taak te verg eten. Het resultaat bestand, indien gereed, wordt *niet* gedownload. *Dit is niet hetzelfde als het annuleren van de taak. Als de taak wordt uitgevoerd, wordt deze nog steeds uitgevoerd in de Cloud.*
14. Selecteer deze knop om de maken naar de cloud te verzenden. Terwijl een taak wordt uitgevoerd, wordt deze knop **taak annuleren**.
15. Selecteer deze knop om de verwerking van [akoestische geluids simulatie op uw PC](#Local-bake)voor te bereiden.
16. In dit gebied wordt de status van de maken weer gegeven. Wanneer de maken is voltooid, wordt ' gedownload ' weer gegeven.

U kunt altijd volledige informatie krijgen over actieve taken, reken Pools en opslag in de [Azure Portal](https://portal.azure.com).

Terwijl een taak wordt uitgevoerd, verandert de **maken** -knop Label om de **taak te annuleren**. Gebruik deze knop om de taak die wordt uitgevoerd te annuleren. U wordt gevraagd om te bevestigen. Het annuleren van een taak kan niet ongedaan worden gemaakt. Wanneer u annuleert, zullen er geen resultaten meer beschikbaar zijn, maar worden er nog steeds kosten in rekening gebracht voor alle gebruikte Azure-reken tijd.

Nadat u een maken hebt gestart, kunt u de eenheid sluiten. Afhankelijk van het project, het knooppunt type en het aantal knoop punten kan een Cloud-maken enkele uren duren. De taak status maken wordt bijgewerkt wanneer u het project opnieuw laadt en het akoestische venster opent. Als de taak is voltooid, wordt het uitvoer bestand gedownload.

Voor de beveiliging worden Azure-referenties op uw lokale machine opgeslagen en gekoppeld aan uw Unity-editor. Ze worden alleen gebruikt om een beveiligde verbinding met Azure tot stand te brengen.

## <a name="find-the-status-of-a-running-job-on-the-azure-portal"></a>De status van een actieve taak op het Azure Portal vinden

1. Zoek de maken-taak-ID op het tabblad **maken** .

    ![De ID van de eenheid maken-taak is gemarkeerd op het tabblad maken](media/unity-job-id.png)  

2. Open de [Azure Portal](https://portal.azure.com), ga naar het batch-account dat is gebruikt voor de maken en selecteer **taken**.

    ![De koppeling taken in de Azure Portal](media/azure-batch-jobs.png)  

3. Zoek naar de taak-ID in de lijst met taken.

   ![De status van de maken-taak is gemarkeerd in de Azure Portal](media/azure-bake-job-status.png)  

4. Selecteer de taak-ID om de status van de gerelateerde taken en de algehele taak status weer te geven.

   ![De taak status maken](media/azure-batch-task-state.png)  


### <a name="Estimating-bake-cost"></a>Kosten voor Azure maken schatten

Als u wilt schatten wat een maken kost, begint u met de geschatte waarde voor **reken kosten** , die een duur is. Vermenigvuldig die waarde met de kosten per uur in uw lokale valuta voor het **VM-knooppunt type** dat u selecteert. Houd er rekening mee dat het resultaat niet de tijd van het knoop punt bevat die nodig is om de knoop punten actief en werkend te krijgen.

Stel bijvoorbeeld dat u **Standard_F8s_v2** selecteert voor het knooppunt type, met de kosten $0.40/HR. Als de **geschatte reken kosten** 3 uur en 57 minuten zijn, is de geschatte kosten voor het uitvoeren van de taak $0,40 * ~ 4 uur = ~ $1,60. De werkelijke kosten zijn waarschijnlijk iets hoger vanwege de extra tijd voor het starten van de knoop punten.

Zoek kosten per uur op [Azure batch prijzen](https://azure.microsoft.com/pricing/details/virtual-machines/linux). (Selecteer verwerkte **Compute** of **High Performance Compute** als categorie.)

## <a name="Local-bake"></a>Maken uw scène op uw PC
U kunt uw scène ook maken op uw eigen PC. Deze methode kan handig zijn om te experimenteren met akoestische voor kleine scènes voordat u een Azure Batch-account maakt. Houd er rekening mee dat de lokale akoestische simulatie veel tijd kan duren, afhankelijk van de grootte van de scène.

### <a name="minimum-hardware-requirements"></a>Minimale hardwarevereisten
* Een x86-64-processor met ten minste 8 kernen en 32 GB aan RAM-geheugen
* [Hyper-V ingeschakeld](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) om docker uit te voeren

Een voor beeld: in onze tests op een 8-kern computer, Intel Xeon E5-1660 @ 3 GHz en 32 GB aan RAM-geheugen.
* Een kleine scène met 100 tests duurde ongeveer 2 uur voor een ruwe maken of 32 uur voor een fijne maken.
* Een middel grote scène met 1.000 tests duurde ongeveer 20 uur voor een ruwe maken of 21 dagen voor een fijne maken.

### <a name="set-up-docker"></a>Docker instellen
Installeer en configureer docker op de PC waarop de simulatie wordt uitgevoerd:
1. Installeer [docker Desktop](https://www.docker.com/products/docker-desktop).
2. Open docker-instellingen, ga naar **Geavanceerd**en configureer de bronnen voor ten minste 8 GB RAM-geheugen. Hoe meer Cpu's u aan docker kunt toewijzen, hoe sneller de maken wordt voltooid.  
![voorbeeld instellingen voor docker](media/docker-settings.png)
1. Ga naar **gedeelde stations**en schakel delen in voor het station dat wordt gebruikt voor de verwerking.  
opties ![docker-gedeelde stations](media/docker-shared-drives.png)

### <a name="run-the-local-bake"></a>De lokale maken uitvoeren
1. Selecteer de knop **lokale maken voorbereiden** op het tabblad **maken** . Selecteer vervolgens de locatie van de map waarin de invoer bestanden en uitvoerings scripts moeten worden opgeslagen. U kunt de maken vervolgens op elke computer uitvoeren, zolang deze voldoet aan de minimale hardwarevereisten en u docker installeert door de map naar die computer te kopiëren.
2. Als u de simulatie wilt starten, voert u het script *runlocalbake. bat* uit in Windows of het script *runlocalbake.sh* op MacOS. Met dit script wordt de project akoestische docker-installatie kopie opgehaald met de hulpprogram ma's die nodig zijn voor simulatie verwerking en wordt de simulatie gestart.
3. Nadat de simulatie is voltooid, kopieert u het resulterende *. Ace* -bestand terug naar uw Unity-project. Als u er zeker van wilt zijn dat unit wordt herkend als een binair bestand, voegt u '. bytes ' toe aan de bestands extensie (bijvoorbeeld ' Scene1. ace. bytes '). De gedetailleerde logboeken voor de simulatie worden opgeslagen in *AcousticsLog. txt.* Als u problemen ondervindt, inspecteert u dit bestand om te helpen bij het vaststellen van het probleem.

## <a name="Data-Files"></a>Gegevens bestanden die worden toegevoegd door het maken-proces

De volgende vier gegevens bestanden worden gemaakt tijdens het maken-proces. De ene bevat de simulatie resultaten en wordt geleverd met uw titel. De overige opslag eenheid editor-gerelateerde gegevens.

Simulatie resultaat:
* *Activa/AcousticsData/akoestische\_[scène naam]. ace. bytes*: dit bestand is de opzoek tabel voor runtime. Het bevat de simulatie resultaten en voxelized akoestische scène-elementen. U kunt de naam en locatie van dit bestand wijzigen op het tabblad **tests** .

   *Zorg ervoor dat u het simulatie resultaten bestand niet verwijdert. Deze kan niet worden hersteld, behalve door de scène te rebakingen.*

Editor-gegevens bestanden:
* *Assets/redacteur/[scènenaam]\_AcousticsParameters. Asset*: in dit bestand worden de gegevens opgeslagen die u invoert in velden in de gebruikers interface van akoestische elementen. U kunt de naam en locatie van dit bestand niet wijzigen.
* *Assets/AcousticsData/redacteur/Acoustics_ [scènenaam]. Vox*: in dit bestand worden de voxelized-akoestische geometrie en de materiaal eigenschappen opgeslagen die worden berekend wanneer u de knop **berekenen** selecteert op het tabblad **tests** . U kunt de naam en locatie van dit bestand wijzigen op het tabblad **tests** .
* *Assets/AcousticsData/redacteur/akoestische\_[scènenaam]\_config. XML*: in dit bestand worden simulatie parameters opgeslagen die worden berekend wanneer u **berekenen**selecteert. U kunt de naam en locatie van dit bestand wijzigen op het tabblad **tests** .

## <a name="set-up-the-acoustics-lookup-table"></a>De zoek tabel voor akoestische instellingen instellen
Sleep het **project akoestische** prefab van het deel venster Project naar uw scène:

![De akoestische prefab in eenheid](media/acoustics-prefab.png)

Selecteer het spel object **ProjectAcoustics** en ga naar het deel venster Inspector. Geef de locatie op van uw maken-resultaat (het ACE-bestand in *assets/AcousticsData*): Sleep het naar het akoestische-beheer script of selecteer de knop Circle naast het tekstvak.

![De akoestische prefab-manager in eenheid](media/acoustics-manager.png)

## <a name="next-steps"></a>Volgende stappen
* Verken de [besturings elementen voor unit-ontwerp](unity-workflow.md).
* Verken [ontwerp concepten voor project akoestische](design-process.md).
