---
title: Fysiek gebaseerde renderingmaterialen instellen in Maya
description: In deze zelfstudie wordt uitgelegd hoe u Fysiek gebaseerde renderingmaterialen instelt in Maya en deze exporteert naar FBX-indeling
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 5579994b0746a2de4b0f2ca927027ac709940024
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84977378"
---
# <a name="tutorial-setting-up-physically-based-rendering-materials-in-maya"></a>Zelfstudie: Fysiek gebaseerde renderingmaterialen instellen in Maya

## <a name="overview"></a>Overzicht
In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Materialen met een geavanceerd belichtingsmodel toewijzen aan objecten in de scène.
> * Het instantiëren van objecten en materialen afhandelen.
> * Een scène exporteren naar FBX-indeling en belangrijke opties om te selecteren.

Het maken van [PBR-materialen (Physically Based Rendering)](../../overview/features/pbr-materials.md) in `Maya` is een relatief eenvoudige taak die vergelijkbaar is met de vele manieren om PBR in te stellen in andere apps voor het maken van inhoud, zoals `3DS Max`. Deze zelfstudie is een handleiding tot het instellen van basis-BPR-shader en FBX-export voor ARR-projecten. 

De voorbeeldscène in deze zelfstudie bevat een aantal `Polygon Box`-objecten waaraan diverse materialen zijn toegewezen: hout, metaal, gelakt metaal, plastic en rubber. In brede zin bevat elk materiaal alle of de meeste van de volgende texturen 

* `Albedo` is de kleurkaart van de materialen, die ook wel `Diffuse` of `BaseColor` wordt genoemd
* `Metalness` bepaalt of een materiaal van metaal is en welke delen van metaal zijn. 
* `Roughness` bepaalt hoe ruw of vloeiend een oppervlak is, wat invloed heeft op de scherpte of wazigheid van de reflecties en hooglichten op een oppervlak.
* `Normals` voegt details toe aan een oppervlak, bijvoorbeeld putjes en deuken op een metalen oppervlak of nerven in hout, zonder dat er meer veelhoeken hoeven te worden toegevoegd.
* `Ambient Occlusion` wordt gebruikt om zachte schaduwen en contactschaduwen toe te voegen aan een model. Het is een kaart in grijstinten die aangeeft welke gebieden van een model volledige belichting (wit) of volledige schaduw (zwart) krijgen. 

## <a name="prerequisites"></a>Vereisten
* `Autodesk Maya 2017` of nieuwer

## <a name="setting-up-materials-in-the-scene"></a>Materialen instellen in de scène
In Maya gaat het instellen van een PBR-materiaal als volgt:

Zoals te zien is in de voorbeeldscène, hebben we een aantal kubusobjecten gemaakt die elk een ander type materiaal vertegenwoordigen. Elk van deze objecten heeft een passende naam gekregen, zoals weergegeven in de onderstaande afbeelding 

> Voordat we beginnen met het maken van assets voor Azure Remote Rendering (ARR), is het de moeite waard te vermelden dat er wordt gemeten in meters en dat de opwaartse richting de Y-as is. Daarom is het raadzaam om uw scène-eenheden in Maya in te stellen op meters. Ook is het bij het exporteren raadzaam om eenheden in te stellen op meters in de FBX-exportinstellingen. 

> [!TIP]
Het is een goed gebruik om uw modelassets passende namen te geven, met daarin vaak het relevante onderdeel of materiaaltype, omdat het hierdoor eenvoudiger wordt om te navigeren in scènes met veel objecten.

![Objectnamen](media/object-names.jpg)

Afhankelijk van uw behoeften kunt u unieke texturen maken voor een model in textuur-apps, zoals `Quixel Suite`, `Photoshop` of `Substance Suite` of gebruikmaken van algemene tegeltexturen van andere bronnen. Na het maken of verwerven van uw texturen kunt u deze als volgt op uw model toepassen:

* Selecteer in de viewport van uw scène uw model/geometrie en klik er met de rechtermuisknop op. Klik in het menu dat wordt weergegeven op `Assign New Material`
* Ga in de `Assign New Material`-opties naar `Maya`>`Stingray PBS`. Hiermee wordt een PBR-materiaal aan uw model toegewezen. 

In `Maya 2020`zijn er diverse PBR-shaders beschikbaar: `Maya Standard Surface`, `Arnold Standard Surface`en `Stingray PBR`. De `Maya Standard Surface Shader` is nog niet exporteerbaar via de `FBX plugin 2020`, terwijl de `Arnold Standard Surface Shader` kan worden geëxporteerd met FBX-bestanden. In de meeste andere opzichten is deze gelijk aan de `Maya Standard Surface Shader` en aan het `Physical Material` in `3D Studio Max`.

**`The Stingray PBR Shader`** is compatibel met veel andere toepassingen, komt het meest overeen met de vereisten van `ARR` en wordt ondersteund vanaf `Maya 2017`. Het is ook handig dat dit type materialen bij visualisatie in viewport lijkt op wat later in ARR wordt gevisualiseerd.

![Het materiaal Stingray](media/stingray-material.jpg)

Nu uw materiaal is toegewezen aan uw asset en de juiste naam heeft gekregen, kunt u doorgaan met het toewijzen van uw verschillende texturen. In de volgende afbeeldingen wordt aangegeven waar elk textuurtype past bij het PBR-materiaal. Met het materiaal `Stingray PBR` kunt u selecteren welke kenmerken u kunt activeren. Vóór de `plug in` van uw textuurkaarten moet u dus de relevante kenmerken activeren: 

![Materiaalinstellingen](media/material-setup.jpg)

> [!TIP]
Het is raadzaam om uw materialen passende namen te geven, waarbij u rekening houdt met het gebruik en/of type ervan. Een materiaal dat voor een uniek onderdeel moet worden gebruikt, kan worden vernoemd naar dat onderdeel, terwijl een materiaal dat kan worden gebruikt in een breder aantal gebieden, kan worden vernoemd naar de eigenschappen of het type ervan.

Wijs uw texturen als volgt toe:

![Textuurinstellingen](media/texture-setup.jpg)

Als uw PBR-materialen zijn gemaakt en ingesteld, is het de moeite waard om na te denken over het [instantiëren van objecten](../../how-tos/conversion/configure-model-conversion.md#instancing) in uw scène. Het instantiëren van vergelijkbare objecten in uw scène, zoals moeren, bouten en schroeven, sluitringen en alle objecten die hetzelfde zijn, kan aanzienlijke besparingen opleveren wat betreft de bestandsgrootte. Instanties van een masterobject kunnen hun eigen schaal, draaiing en transformaties krijgen, zodat deze naar behoefte in uw scène kunnen worden geplaatst. In Maya is het instantiëringsproces eenvoudig.

* Ga in het menu `Edit` naar `Duplicate Special` en open de `Options`. 
* In de `Duplicate Special`-opties wijzigt u `Geometry Type` van `Copy` in `Instance`. 
* Klik op `Duplicate Special`

![Instantiëren](media/instancing.jpg)

Met deze actie wordt een instantie van uw object gemaakt dat onafhankelijk van het bovenliggende item en andere instanties van dat bovenliggende item kan worden gedraaid of geschaald. 
>Wijzigingen die u in de onderdeelmodus aanbrengt in een instantie worden echter verzonden naar alle instanties van uw object. Als u dus werkt met geïnstantieerde objectonderdelen, zoals hoekpunten en polygoonvlakken, moet u zich ervan bewust zijn dat de wijzigingen die u aanbrengt, van invloed zijn op al deze instanties.

In de voorbeeldscène is elk afzonderlijk kubusobject geïnstantieerd. Deze actie wordt relevant als we de scène exporteren naar FBX-indeling.

![Scèneoverzicht](media/scene-overview.jpg)

>De best practice voor instantiëren in uw scène is om deze instanties gaandeweg te maken, omdat het vervangen van kopieën door geïnstantieerde objecten later bijzonder moeilijk is. 

## <a name="fbx-export-process"></a>Het FBX-exportproces

Nu kunnen we overgaan tot de FBX-export van uw scène of scèneassets. Over het algemeen is het bij het exporteren van assets handig om alleen de gewenste objecten/assets in de scène te selecteren voor export. Als u 100 objecten in een scène hebt, maar u er slechts 30 van wilt gebruiken, heeft het geen zin om de hele scène te exporteren. Maak dus uw selectie, tenzij u de hele scène wilt exporteren, en ga naar:

* `File` > `Export Selection`. Ga in het dialoogvenster Exporteren vervolgens naar beneden en stel `Files of Type` in op `FBX Export`. In dit venster worden de FBX-exportinstellingen weergegeven. De belangrijkste FBX-exportinstellingen zijn rood gemarkeerd in de onderstaande afbeelding.

![FBX-export](media/FBX-exporting.jpg)

Afhankelijk van uw vereisten (u wilt bijvoorbeeld een asset naar een client verzenden, maar zonder grote textuurbestanden), kunt u ervoor kiezen om de texturen in te sluiten in het geëxporteerde FBX-bestand. Deze optie houdt in dat er slechts één bestand hoeft te worden ingepakt, maar dat de grootte van de FBX-asset aanzienlijk toeneemt. U kunt de optie voor het insluiten van texturen inschakelen door de optie `Embed Media` in te schakelen, zoals hieronder wordt weergegeven.

> [!TIP]
> U ziet dat het bestand in dit geval is vernoemd naar deze voorwaarde. Dat is een goede manier om uw assets bij te houden. 

Wanneer u klaar bent met het instellen van de exportconfiguratie, klikt u op de knop Selectie exporteren in de rechterbenedenhoek.

![Media insluiten](media/embedding-media.jpg)

## <a name="conclusion"></a>Conclusie

Over het algemeen lijken materialen van dit type realistisch, omdat ze zijn gebaseerd op natuurlijk-lichtregels. Hierdoor ontstaat het extra insluitende effect dat de scène werkelijk bestaat.

## <a name="next-steps"></a>Volgende stappen

U weet nu wat de belangrijkste functionaliteit is voor het instellen van materialen met geavanceerde belichting van objecten in een scène en hoe u deze kunt exporteren naar FBX-indeling, die wordt ondersteund door ARR. De volgende stap is om het FBX-bestand te converteren en te visualiseren in ARR.

> [!div class="nextstepaction"]
> [Snelstart: een model converteren voor rendering](../../quickstarts\convert-model.md)