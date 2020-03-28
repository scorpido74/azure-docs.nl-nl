---
title: Project Acoustics Unity Design Zelfstudie
titlesuffix: Azure Cognitive Services
description: Deze zelfstudie beschrijft de ontwerpworkflow voor Project Acoustics in Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fd00e4105ce4edae9d014df2a83c5ae3aaf778da
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854265"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Project Acoustics Unity Design Zelfstudie
Deze zelfstudie beschrijft de ontwerptools en workflow voor Project Acoustics in Unity.

Vereisten:
* Unity 2018.2+ voor Windows
* Een scène van de Eenheid met een gebakken akoestiekbezit

Voor deze tutorial kun je op twee manieren een Unity-scène met een gebakken akoestiekactief krijgen:
* [Projectakoestiek toevoegen aan uw Unity-project,](unity-integration.md) [en vervolgens een Azure Batch-account krijgen](create-azure-account.md)en vervolgens uw [Unity-scène bakken](unity-baking.md)
* Of gebruik de [samplecontent van Project Acoustics Unity.](unity-quickstart.md)

## <a name="review-design-process-concepts"></a>Concepten voor ontwerpprocessen herzien
Project Acoustics maakt gebruik van gemeenschappelijke audio digitale signaal verwerking (DSP) methoden om uw bronnen te verwerken, en geeft u controle over bekende akoestiek eigenschappen, waaronder occlusie, nat / droog mix, en nagalm staart lengte (RT60). Maar de kern [Project Acoustics ontwerp proces concept](design-process.md) is dat in plaats van het instellen van deze eigenschappen direct, u bepalen hoe simulatie resultaten worden gebruikt om deze eigenschappen rijden. De standaardinstellingen voor elk besturingselement vertegenwoordigen fysiek nauwkeurige akoestiek.

## <a name="design-acoustics-for-each-source"></a>Ontwerp akoestiek voor elke bron
Project Acoustics biedt een aantal bronspecifieke akoestiekontwerpregelaars. Hiermee u de mix in een scène controleren door bepaalde bronnen te benadrukken en anderen te de-benadrukken.

### <a name="adjust-distance-based-attenuation"></a>Demping op afstand aanpassen
De audio DSP van de **Project Acoustics** Unity spatializer plugin respecteert de op afstand gebaseerde demping per bron die is ingebouwd in de Unity Editor. Besturingselementen voor demping op afstand bevinden zich in de component **Audiobron** in het **deelvenster Inspector** van geluidsbronnen onder **3D-geluidsinstellingen:**

![Schermafbeelding van het deelvenster Opties voor afstandsdemping van Unity](media/distance-attenuation.png)

Akoestiek voert berekeningen uit in een vak "simulatiegebied" gecentreerd rond de locatie van de speler. Als een geluidsbron ver van de speler verwijderd is, zich buiten dit simulatiegebied bevindt, heeft alleen de geometrie in de doos invloed op de geluidsvoortplanting (zoals het veroorzaken van occlusie) die redelijk goed werkt wanneer occluders zich in de buurt van de speler bevinden. Echter, in gevallen waarin de speler zich in de open ruimte bevindt, maar de occluders zich in de buurt van de verre geluidsbron bevinden, kan het geluid onrealistisch worden. Onze voorgestelde oplossing is om ervoor te zorgen in dergelijke gevallen dat het geluid demping valt af tot 0 op ongeveer 45 m, de standaard horizontale afstand van de speler tot de rand van de doos.

![Schermafbeelding van het optiepaneel Unity SpeakerMode](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Occlusie en transmissie aanpassen
Als u het **script AcousticsAdjust** aan een bron koppelt, kunnen afstemmingsparameters voor die bron worden aangepast. Als u het script wilt koppelen, klikt u op **Component toevoegen** onder in het deelvenster **Inspector** en navigeert u naar Scripts > **akoestiek aanpassen**. Het script heeft zes besturingselementen:

![Schermafbeelding van het script Unity AcousticsAdjust](media/acoustics-adjust.png)

* **Akoestiek inschakelen** - Bepaalt of de akoestiek op deze bron wordt toegepast. Wanneer niet aangevinkt, zal de bron worden gespatialiseerd met HRTFs of panning, maar er zal geen akoestiek. Dit betekent geen obstructie, occlusie, of dynamische nagalm parameters zoals niveau en verval tijd. Nagalm wordt nog steeds toegepast met een vast niveau en vervaltijd.
* **Occlusie** - Breng een multiplier aan op het occlusie dB-niveau berekend door het akoestieksysteem. Als deze multiplier groter is dan 1, zal occlusie worden overdreven, terwijl waarden minder dan 1 het occlusie-effect subtieler maken en een waarde van 0 occlusie uitschakelt.
* **Transmissie (dB)** - Stel de demping (in dB) in die wordt veroorzaakt door transmissie door geometrie. Stel deze schuifregelaar in op het laagste niveau om de transmissie uit te schakelen. Akoestiek ruimtelijk de eerste droge audio als aankomst rond scène geometrie (portaling). Transmissie zorgt voor een extra droge aankomst die ruimtelijk is in de lijn-van-zicht richting. Houd er rekening mee dat de afstandsdempingscurve voor de bron ook wordt toegepast.

### <a name="adjust-reverberation"></a>Nagalm aanpassen
* **Nattigheid (dB)** - Past de nagalmkracht, in dB, aan op afstand van de bron. Positieve waarden maken een geluid meer galmen, terwijl negatieve waarden maken een geluid meer droog. Klik op het curvebesturingselement (groene lijn) om de curveeditor naar boven te halen. Wijzig de curve door links te klikken om punten toe te voegen en deze punten te slepen om de gewenste functie te vormen. De x-as is afstand tot de bron en de y-as is reverb aanpassing in dB. Zie deze [Eenheidshandleiding](https://docs.unity3d.com/Manual/EditingCurves.html)voor meer informatie over het bewerken van curven. Als u de curve terug wilt zetten naar standaard, klikt u met de rechtermuisknop op **Nattigheid** en selecteert u **Opnieuw instellen**.
* **Decay Time Scale** - Past een multiplier voor het verval tijd. Als het bakresultaat bijvoorbeeld een vervaltijd van 750 milliseconden opgeeft, maar deze waarde is ingesteld op 1,5, is de vervaltijd die op de bron wordt toegepast 1.125 milliseconden.
* **Outdoorness** - Een additieve aanpassing op de schatting van het akoestieksysteem van hoe "buiten" de nagalm op een bron moet klinken. Als u deze waarde op 1 instelt, klinkt een bron altijd volledig buiten, terwijl deze volledig binnen -1 wordt ingesteld.

Als u het **script AcousticsAdjustExperimental** aan een bron koppelt, kunnen extra experimentele afstemmingsparameters voor die bron worden toegevoegd. Als u het script wilt koppelen, klikt u op **Component toevoegen** onder in het deelvenster **Inspector** en navigeert u naar Scripts > Akoestiek **Aanpassen experimenteel**. Er is momenteel één experimentele controle:

![Schermafbeelding van het script van Unity AcousticsAdjustExperimental](media/acoustics-adjust-experimental.png)

* **Perceptuele Distance Warp** - Breng een exponentiële kromtrekken aan op de afstand die wordt gebruikt om de droge-natte verhouding te berekenen. Het akoestieksysteem berekent natte niveaus in de ruimte, die soepel variëren met afstand en bieden perceptuele afstand signalen. Kromtrekkenwaarden groter dan 1 overdrijven dit effect door afstandsgerelateerde nagalmniveaus te verhogen, waardoor het geluid "afstandelijk" wordt. Kromtrekkenwaarden van minder dan 1 maken de op afstand gebaseerde nagalm subtieler, waardoor het geluid meer "aanwezig" wordt.

## <a name="design-acoustics-for-all-sources"></a>Ontwerp akoestiek voor alle bronnen
Als u parameters voor alle bronnen wilt aanpassen, klikt u op de kanaalstrip in Unity's **Audio Mixer**en past u de parameters aan op het effect Project **Acoustics Mixer.**

![Schermafbeelding van het aanpassingspaneel van Project Acoustics Unity Mixer](media/mixer-parameters.png)

* **Wetheid aanpassen** - Past de nagalmkracht, in dB, aan op alle bronnen in de scène op basis van de afstand bron-luisteraar. Positieve waarden maken een geluid meer galmen, terwijl negatieve waarden maken een geluid meer droog.
* **RT60 Schaal** - Multiplicative scalar voor reverb tijd.
* **Panning gebruiken** - Hiermee bepaalt u of audio wordt uitgevoerd als binaurale (0) of multikanaals pannen (1). Elke waarde naast 1 geeft binauraal aan. Binaurale output is ruimtelijk met HRTF's voor gebruik met hoofdtelefoons en multichannel output is ruimtelijk met VBAP voor gebruik met multichannel surround luidsprekersystemen. Als u de multichannel-panner gebruikt, moet u de luidsprekermodus selecteren die overeenkomt met de instellingen van uw apparaat, gevonden onder **Project Instellingen** > **Audio**.

## <a name="check-proper-sound-source-placement"></a>De juiste plaatsing van de geluidsbron controleren
Geluidsbronnen die in bezette voxels worden geplaatst, krijgen geen akoestische behandeling. Omdat voxels zich uitstrekken langs de zichtbare scènegeometrie, is het mogelijk om een bron in een voxel te plaatsen terwijl deze niet wordt afgesloten door visuele geometrie. U Project Acoustics voxels bekijken door het voxel-rasterselectievakje te omdraaien in het **gizmo's-menu,** rechtsboven in de **scèneweergave.**

![Schermafbeelding van het menu Unity Gizmos](media/gizmos-menu.png)  

De voxel display kan ook helpen bepalen of visuele componenten in het spel hebben een transformatie toegepast op hen. Als dat het zo is, past u dezelfde transformatie toe op de GameObject die de **Akoestiekmanager**host.

### <a name="bake-time-vs-run-time-voxels"></a>Bak tijd vs. run time voxels
Het is mogelijk om voxels te bekijken in het editor venster op game design tijd en in het spel venster op runtime. De grootte van de voxels is verschillend in deze opvattingen. Dit komt omdat de akoestiek runtime interpolatie maakt gebruik van een fijnere voxel raster voor soepeler interpolatie resultaten. Geluidsbronplaatsing moet worden geverifieerd met behulp van de runtime voxels.

Ontwerp tijd voxels:

![Schermafbeelding van Project Acoustics voxels tijdens ontwerptijd](media/voxels-design-time.png)

Runtime voxels:

![Schermafbeelding van Project Acoustics-voxels tijdens de run](media/voxels-runtime.png)

## <a name="next-steps"></a>Volgende stappen
* Ontdek casestudies waarin de concepten achter het [ontwerpproces](design-process.md) worden belicht

