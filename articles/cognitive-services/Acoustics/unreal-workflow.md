---
title: Project Acoustics Unreal Design Zelfstudie
titlesuffix: Azure Cognitive Services
description: Deze zelfstudie beschrijft de ontwerpworkflow voor Project Acoustics in Unreal en Wwise.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 817a11171c5b4b4ef205e5fbb04f9b6d6d85b248
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854252"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Project Acoustics Unreal/Wwise Ontwerp Tutorial
Deze zelfstudie beschrijft de ontwerpinstelling en workflow voor Project Acoustics in Unreal en Wwise.

Vereisten voor software:
* Een Unreal project met de Project Acoustics Wwise en Unreal plugins

Om een Unreal project met Project Acoustics te krijgen, u:
* Volg de [Project Acoustics Unreal integratie-instructies](unreal-integration.md) om Project Acoustics toe te voegen aan uw Unreal project
* Of gebruik het [project Project Acoustics voorbeeldproject](unreal-quickstart.md).

## <a name="setup-project-wide-wwise-properties"></a>Projectbrede Wwise-eigenschappen instellen
Wwise heeft globale obstructie en occlusie curven die van invloed zijn op hoe de Project Acoustics plugin rijdt de Wwise audio DSP.

### <a name="design-wwise-occlusion-curves"></a>Ontwerp Wwise occlusie curven
Wanneer Project Acoustics actief is, reageert het op de occlusievolume, low-pass filter (LPF) en high-pass filter (HPF) curves die u in Wwise instelt. We raden u aan uw volumecurvetype in te stellen op lineair met een waarde van -100 dB voor een occlusiewaarde van 100.

Met deze instelling, als de Project Acoustics simulatie berekent een occlusie van -18 dB, zal het input aan de onderstaande curve op X = 18, en de bijbehorende Y-waarde is de demping toegepast. Om half occlusie te doen, stelt u het eindpunt in op -50 dB in plaats van -100 dB, of op -200 dB om occlusie te overdrijven. U elke curve die het beste werkt voor uw spel op maat maken en verfijnen.
 
![Schermafbeelding van wwise occlusioncurve-editor](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Wwise obstructiecurven uitschakelen
De Wwise obstructiecurves beïnvloeden het droge niveau geïsoleerd, maar Project Acoustics gebruikt ontwerpcontroles en simulatie om natte/droge verhoudingen af te dwingen. We raden u aan de volumecurve uit te schakelen. Om de nattigheid te ontwerpen, gebruikt u de besturingselement Wetness Adjust die later wordt beschreven.
 
Als u obstructie LPF/HPF-curven voor andere doeleinden gebruikt, moet u ervoor zorgen dat u ze op Y=0 op X=0 hebt ingesteld (dat wil zeggen dat er geen LPF of HPF is wanneer er geen obstructie is).

![Schermafbeelding van wwise obstructiecurve-editor](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Ontwerp projectakoestiek mixerparameters
U globale reverb-eigenschappen beheren door het tabblad mixerplug-in van de Project Acoustics Bus te bezoeken. Dubbelklik op "Project Acoustics Mixer (Custom)" om het instellingenpaneel van de mixerplug-in te openen.

U ook zien dat de mixer plugin heeft een Perform Spatialization optie. Als u liever de ingebouwde spatialisatie van Project Acoustic gebruikt, schakelt u het selectievakje 'Spatialisatie uitvoeren' in en kiest u uit HRTF of Panning. Zorg ervoor dat u alle Dry Aux-bussen uitschakelt die u hebt ingesteld, anders hoort u het directe pad twee keer. Gebruik de "Wetness Adjust" en "Reverb Time Scale Factor" om wereldwijde controle uit te oefenen op de reverb mix. Let op: je moet Unreal opnieuw opstarten en vervolgens geluidsbanken regenereren voordat je op play drukt om mixerplug-config-wijzigingen op te pikken, zoals het selectievakje 'Spatialization uitvoeren'.

![Schermafbeelding van de plug-insopties van Project Acoustics Wwise-mixer](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>Ontwerpbesturingselementen voor Project Acoustics instellen in de wwise-actormixerhiërarchie
Als u parameters van een afzonderlijke actormixer wilt beheren, dubbelklikt u op de Actor-Mixer en klikt u vervolgens op het tabblad Mixer-invoegtoepassing. Hier u alle parameters op het niveau per geluid wijzigen. Deze waarden worden gecombineerd met de waarden die zijn ingesteld vanaf de Onwerkelijke kant (hieronder beschreven). Als de Project Acoustics Unreal-plug-in bijvoorbeeld Aanpassing buitenheid op een object instelt op 0,5 en Wwise deze instelt op -0,25, is de resulterende aanpassing buitenheid die op dat geluid wordt toegepast 0,25.

![Schermafbeelding van de instellingen per geluidsmixer in de wwise-actormixerhiërarchie](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Zorg ervoor dat de aux-bus heeft droge verzenden, en output bus heeft nat verzenden
Vergeet niet dat de vereiste actor-mixer setup de gebruikelijke droge en natte routing in Wwise wisselt. Het produceert galmsignaal op de uitgangsbus van de actor-mixer (ingesteld op Project Acoustics Bus) en droog signaal langs de door de gebruiker gedefinieerde aux-bus. Deze routing is vereist vanwege de functies van de Wwise mixer plugin API die de Project Acoustics Wwise plugin gebruikt.

![Schermafbeelding van wwise-editor met richtlijnen voor spraakontwerp voor Project Acoustics](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Afstandsdempingscurven instellen
Zorg ervoor dat elke dempingscurve die wordt gebruikt door actormixers met Project Acoustics door de gebruiker is gedefinieerd, aux-send set is ingesteld op 'output busvolume'. Wwise doet dit standaard voor nieuw gemaakte dempingscurven. Als u een bestaand project migreert, controleert u de curve-instellingen.

Standaard heeft de Project Acoustics simulatie een straal van 45 meter rond de locatie van de speler. We raden over het algemeen aan om uw dempingscurve in te stellen op -200 dB rond die afstand. Deze afstand is geen harde beperking. Voor sommige geluiden als wapens wil je misschien een grotere straal. In dergelijke gevallen is het voorbehoud dat alleen geometrie binnen 45 m van de locatie van de speler zal deelnemen. Als de speler zich in een kamer bevindt en er een geluidsbron buiten de kamer is en 100 meter verderop, wordt deze goed afgesloten. Als de bron zich in een kamer bevindt en de speler zich buiten en op 100 m afstand bevindt, wordt deze niet goed afgesloten.

![Schermafbeelding van wwise-dempingscurven](media/atten-curve.png)

### <a name="post-mixer-equalization"></a>Egalisatie na mixer ###
 Een ander ding dat je zou willen doen is het toevoegen van een post mixer equalizer. U de Project Acoustics-bus behandelen als een typische reverb-bus (in de standaardgalmmodus) en er een filter op zetten om egalisatie uit te brengen. U een voorbeeld hiervan zien in het Project Acoustics Wwise Sample Project.

![Schermafbeelding van Wwise-eq na de mixer](media/wwise-post-mixer-eq.png)

Een high pass-filter kan bijvoorbeeld helpen om de bas te verwerken van near-field opnames die boomy, onrealistische reverb opleveren. U ook meer post-bake controle bereiken door het aanpassen van de EQ via RTPC's, zodat u de kleur van de galm te veranderen in het spel.

## <a name="set-up-scene-wide-project-acoustics-properties"></a>Scènebrede Project Acoustics-eigenschappen instellen

De actor Acoustics Space legt veel besturingselementen bloot die het gedrag van het systeem wijzigen en nuttig zijn bij het debuggen.

![Schermafbeelding van unreal acoustics-ruimtebesturingselementen](media/acoustics-space-controls.png)

* **Akoestiek Gegevens:** Aan dit veld moet een gebakken akoestiekactief van de map Inhoud/Akoestiek worden toegewezen. De Project Acoustics-plug-in voegt automatisch de content/acoustics-map toe aan de verpakte mappen van uw project.
* **Tegelgrootte:** De omvang van de regio rond de luisteraar die u wilt akoestiek gegevens geladen in RAM. Zolang de luisteraarsondes onmiddellijk rond de speler binnen worden geladen, zijn de resultaten zelfde als het laden van akoestische gegevens voor alle sondes. Grotere tegels gebruiken meer RAM, maar verminderen schijf I/O
* **Automatisch streamen:** Wanneer ingeschakeld, laadt u automatisch nieuwe tegels wanneer de listener de rand van een geladen gebied bereikt. Wanneer uitgeschakeld, moet u nieuwe tegels handmatig laden via code of blauwdrukken
* **Cacheschaal:** hiermee bepaalt u de grootte van de cache die wordt gebruikt voor akoestische query's. Een kleinere cache verbruikt minder RAM, maar kan het CPU-gebruik voor elke query verhogen.
* **Akoestiek ingeschakeld:** Een foutopsporingsbesturingselement om snel A/B-toggling van de Akoestieksimulatie mogelijk te maken. Dit besturingselement wordt genegeerd in verzendconfiguraties. De besturingselement is handig om te bepalen of een bepaalde audiobug afkomstig is van de akoestiekberekeningen of een ander probleem in het Wwise-project.
* **Afstanden bijwerken:** Gebruik deze optie als u de voorgebakken akoestiekinformatie wilt gebruiken voor afstandsquery's. Deze query's zijn vergelijkbaar met ray casts, maar ze zijn vooraf berekend dus neem veel minder CPU. Een voorbeeld gebruik is voor discrete reflecties uit het dichtst oppervlak aan de luisteraar. Om dit volledig te benutten, moet u code of blauwdrukken gebruiken om afstanden te bevragen.
* **Statistieken tekenen:** Hoewel UE's `stat Acoustics` u cpu-informatie kunnen geven, wordt in deze statusweergave het momenteel geladen ACE-bestand, RAM-gebruik en andere statusgegevens linksboven in het scherm weergegeven.
* **Teken Voxels:** Overlay voxels dicht bij de luisteraar met het voxel raster gebruikt tijdens runtime interpolatie. Als een zender zich in een runtime voxel bevindt, mislukt deze niet in akoestische query's.
* **Sondes tekenen:** Laat alle sondes voor deze scène zien. Ze zullen verschillende kleuren, afhankelijk van hun belasting staat.
* **Afstanden tekenen:** Als Updateafstanden is ingeschakeld, wordt een vak op het dichtstbijzijnde oppervlak weergegeven in gekwantificeerde richtingen rond de listener.

## <a name="actor-specific-acoustics-design-controls"></a>Actor-specifieke akoestiek ontwerpcontroles
Deze ontwerpbesturingselementen zijn beperkt tot een afzonderlijke audiocomponent in Unreal.

![Schermafbeelding van besturingselementen voor onwerkelijke audiocomponenten](media/audio-component-controls.png)

* **Occlusie multiplier:** Hiermee bepaalt u het occlusie-effect. Waarden > 1 versterken de occlusie. Waarden <1 minimaliseren deze.
* **Aanpassing nattigheid:** Extra reverb dB
* **Decay Time Multiplier:** Hiermee regelt u de RT60 op vermenigvuldigende wijze, op basis van de output van de akoestieksimulatie
* **Aanpassing buitenheid:** Bepaalt hoe buiten de nagalm is. Waarden dichter bij 0 zijn meer binnenshuis, dichter bij 1 zijn meer buiten. Deze aanpassing is additief, dus het instellen van het op -1 zal binnenshuis af te dwingen, het instellen van het op +1 zal buiten af te dwingen.
* **Transmissie Db:** Maak een extra door-de-muur geluid met deze luidheid in combinatie met line-of-sight gebaseerde afstand demping.
* **Wet Ratio Distance Warp:** Hiermee past u de nagalmkenmerken op de bron aan alsof deze dichter/verder weg is, zonder het directe pad te beïnvloeden.
* **Spelen op het startscherm:** Schakel in om op te geven of het geluid automatisch moet worden afgespeeld tijdens het starten van de scène. Standaard ingeschakeld.
* **Akoestische parameters weergeven:** Geef foutopsporingsinformatie direct boven op de component in de game weer. (alleen voor niet-verzendconfiguraties)

## <a name="blueprint-functionality"></a>Blauwdrukfunctionaliteit
De Acoustics Space-actor is toegankelijk via een blauwdruk en biedt functionaliteit zoals het laden van een kaart of het wijzigen van instellingen via level scripting. We geven hier twee voorbeelden.

### <a name="add-finer-grained-control-over-streaming-load"></a>Fijnere-grained controle over streaming belasting toevoegen
Als u de akoestische gegevensstreaming zelf wilt beheren in plaats van automatisch te streamen op basis van de positie van de speler, u de blauwdrukfunctie Force Load Tile gebruiken:

![Schermafbeelding van blueprintstreamingopties in Unreal](media/blueprint-streaming.png)

* **Doel:** De AkoestiekSpace-acteur
* **Middelste positie:** Het centrum van de regio dat gegevens moet laden
* **Probes buiten uitladen:** Indien aangevinkt, worden alle sondes die niet in de nieuwe regio zijn, gelost van HET RAM-geheugen. Als het nieuwe gebied niet is ingeschakeld, wordt het in het geheugen geladen terwijl de bestaande sondes ook in het geheugen worden geladen
* **Blok bij voltooiing:** Maakt de tegel laden een synchrone bewerking

De tegelgrootte moet al zijn ingesteld voordat u Force Load Tile aanroept. U bijvoorbeeld zoiets doen om een ACE-bestand te laden, uw tegelgrootte in te stellen en te streamen in een regio:

![Schermafbeelding van opties voor streaminginstellingen in Unreal](media/streaming-setup.png)

De blauwdrukfunctie Load Acoustics Data die in dit voorbeeld wordt gebruikt, heeft de volgende parameters:

* **Doel:** De AcousticsSpace acteur.
* **Nieuwe Bake:** De akoestiek data asset te laden. Het verlaten van deze blanco / instelling op null zal de huidige bak te lossen zonder het laden van een nieuwe.

### <a name="optionally-query-for-surface-proximity"></a>Optioneel query voor oppervlaktenabijheid
Als u wilt zien hoe dicht oppervlakken zich in een bepaalde richting rond de listener bevinden, u de functie Queryafstand gebruiken. Deze functie kan nuttig zijn voor het besturen van directionele vertraagde reflecties, of voor andere spellogica die wordt aangedreven door de nabijheid van het oppervlak. De query is minder duur dan een ray-cast, omdat de resultaten worden getrokken uit de akoestiek opzoektabel.

![Schermafbeelding van voorbeeld query Blauwdrukafstand](media/distance-query.png)

* **Doel:** De AkoestiekSpace-acteur
* **Kijk richting:** De richting waarin u moet invragen, gecentreerd op de listener
* **Afstand:** Als query slaagt, is de afstand tot het dichtstbijzijnde oppervlak
* **Retourwaarde:** Booleaan - true als query geslaagd, anders onwaar

## <a name="next-steps"></a>Volgende stappen
* Ontdek de concepten achter het [ontwerpproces](design-process.md)
* [Een Azure-account maken](create-azure-account.md) om uw eigen scène te bakken


