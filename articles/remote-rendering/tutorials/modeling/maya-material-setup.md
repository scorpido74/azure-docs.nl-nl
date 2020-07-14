---
title: Fysiek gebaseerde rendering instellen in Maya
description: In deze zelfstudie wordt uitgelegd hoe u Fysiek gebaseerde renderingmaterialen instelt in Maya en deze exporteert in een FBX-indeling.
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 72742ff4f6aa19fda092b44d8d2237e7d49dd816
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85373235"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-maya"></a>Zelfstudie: Fysiek gebaseerde rendering instellen in Maya

## <a name="overview"></a>Overzicht
In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Materialen met geavanceerde belichting toewijzen aan objecten in de scène.
> * Objecten en materialen ruimtelijk weergeven.
> * Een scène exporteren naar een FBX-indeling en belangrijke opties selecteren.

Het maken van [fysiek gebaseerde rendering (PBR)-materialen](../../overview/features/pbr-materials.md) in Maya is een relatief eenvoudige taak. Het is in veel opzichten vergelijkbaar met het instellen van PBR in andere apps voor het maken van inhoud, zoals 3DS Max. Deze zelfstudie is een handleiding voor de installatie van een eenvoudige PBR-shader en FBX-export voor Azure Remote Rendering-projecten. 

De voorbeeldscène in deze zelfstudie bevat een aantal polygone box-objecten. Er worden verschillende materialen aan toegewezen, zoals hout, metaal, geverfd metaal, plastic en rubber. In brede zin bevat elk materiaal (vrijwel) alle van de volgende texturen:

* **Albedo**, de kleurkaart van de materialen, ook wel **Diffuus** of **BaseColor** genoemd.
* **Metaligheid**, bepaalt of een materiaal van metaal is en welke delen van metaal zijn. 
* **Ruwheid**, bepaalt hoe ruw of glad een oppervlak is, wat van invloed is op de scherpte of wazigheid van de reflecties en glanspunten op een oppervlak.
* **Normaal**, wat details toevoegt aan een oppervlak zonder dat er noodzakelijkerwijs meer polygonen worden toegevoegd. Voorbeelden van details kunnen putjes en deukjes in een metalen oppervlak of de nerf in hout zijn.
* **Omgevingsocclusie**, wordt gebruikt om zachte schaduwen en contactschaduwen aan een model toe te voegen. Het is een kaart in grijstinten die aangeeft welke gebieden van een model volledige belichting (wit) of volledige schaduw (zwart) krijgen. 

## <a name="prerequisites"></a>Vereisten
* Autodesk Maya 2017 of nieuwer

## <a name="set-up-materials-in-the-scene"></a>Materialen in de scène instellen
U kunt als volgt een PBR-materiaal instellen in Maya.

Zoals u in de voorbeeldscène ziet, hebben we een aantal box-objecten gemaakt. Elk object stelt een ander type materiaal voor. Elk van deze objecten heeft een passende naam gekregen, zoals weergegeven in de afbeelding.

Azure Remote Rendering maakt voor het meten gebruik van meters en de opwaartse richting is de Y-as. Voordat u begint met het maken van assets, raden we u aan om uw scène-eenheden in Maya op meters in te stellen. Stel voor het exporteren de eenheden in op meters in de exportinstellingen van FBX.

> [!TIP]
> Geef de assets van het model passende namen op basis van het relevante onderdeel of type materiaal. Zinvolle namen maken het gemakkelijker om te navigeren in object-intensieve scènes.

![Objectnamen](media/object-names.jpg)

Nadat u een aantal texturen hebt gemaakt of verkregen, kunt u ook unieke texturen maken. U kunt apps gebruiken waarmee u texturen kunt maken, zoals Quixel Suite, PhotoShop of Substance Suite, maar u kunt ook generieke tegeltexturen uit andere bronnen overnemen.

U kunt als volgt texturen op uw model toepassen:

1. Selecteer in de viewport van uw scène uw model of geometrie en klik er met de rechtermuisknop op. Selecteer in het menu dat verschijnt de optie **Nieuw materiaal toewijzen**.
1. Ga in het dialoog venster **Nieuw materiaal toewijzen** naar **Maya** > **Stingray PBS**. Hiermee wordt een PBR-materiaal aan uw model toegewezen. 

In Maya 2020 zijn er diverse PBR-shaders beschikbaar. Dit zijn **Maya Standard Surface**, **Arnold Standard Surface** en **Stingray PBR**. De **Maya Standard Surface-shader** kan nog niet worden geëxporteerd via de FBX 2020-invoegtoepassing. De **Arnold Standard Surface-shader** kan met FBX-bestanden worden geëxporteerd. In de meeste andere opzichten is deze identiek aan de **Maya Standard Surface-shader**. Het is vergelijkbaar met **Physical Material** in 3D Studio Max.

**De Stingray-shader** is compatibel met veel andere toepassingen en komt het meest overeen met de vereisten van Azure Remote Rendering. Dit wordt ondersteund vanaf Maya 2017. Wanneer dit type materiaal wordt gevisualiseerd in de viewport, is dit te vergelijken met wat er later in Azure Remote Rendering wordt gevisualiseerd.

![Stingray-materiaal](media/stingray-material.jpg)

Nu uw materiaal is toegewezen aan uw asset en de juiste naam heeft gekregen, kunt u uw verschillende texturen gaan toewijzen. In de volgende afbeeldingen wordt getoond waar elk textuurtype past bij het PBR-materiaal. Met het Stingray PBR-materiaal kunt u selecteren welke kenmerken u kunt activeren. Voordat u uw structuurkaarten aansluit, moet u de relevante kenmerken activeren.

![Materiaalinstellingen](media/material-setup.jpg)

Geef uw materialen passende namen, waarbij u rekening houdt met het gebruik of type ervan. Een materiaal dat wordt gebruikt voor een uniek onderdeel, kunt u de naam van dat onderdeel geven. Een materiaal dat voor een groter aantal gebieden wordt gebruikt, kunt u de naam van de eigenschappen of het type ervan geven.

Wijs uw texturen toe zoals weergegeven in de afbeelding.

![Textuurinstellingen](media/texture-setup.jpg)

Als uw PBR-materialen zijn gemaakt en ingesteld, kunt u eventueel [objecten instantiëren](../../how-tos/conversion/configure-model-conversion.md#instancing) in uw scène. Het instantiëren van vergelijkbare objecten in uw scène, zoals moeren, bouten, schroeven en sluitringen, kan aanzienlijke besparingen opleveren wat betreft de bestandsgrootte. Instanties van een masterobject kunnen hun eigen schaal, draaiing en transformaties krijgen, zodat deze naar behoefte in uw scène kunnen worden geplaatst. 

In Maya is het instantiëringsproces eenvoudig.

1. Ga in het menu **Edit** naar **Duplicate Special** om opties te openen.
1. Selecteer in het dialoogvenster **Duplicate Special Options**, voor **Geometry type**, de optie **Instance**. 
1. Selecteer **Duplicate Special**.

   ![Instantiëren](media/instancing.jpg)

Hiermee wordt een instantie van het object gemaakt. U kunt het onafhankelijk van het bovenliggende item en van andere instanties van dat bovenliggende item verplaatsen, draaien of schalen. 

Wijzigingen die u in de onderdeelmodus aanbrengt aan een instantie, worden doorgevoerd naar alle instanties van uw object. Bijvoorbeeld als u werkt met geïnstantieerde objectonderdelen, zoals hoekpunten en polygoonvlakken. Zorg ervoor dat alle wijzigingen die u hebt aangebracht, van invloed zijn op al deze instanties. 

In de voorbeeldscène is elk afzonderlijk box-object geïnstantieerd. Deze actie wordt relevant als we de scène exporteren naar FBX-indeling.

![Scèneoverzicht](media/scene-overview.jpg)

> [!TIP]
> Maak instanties in uw scène terwijl u bezig bent. Het later vervangen van kopieën met geïnstantieerde objecten is bijzonder moeilijk. 

## <a name="fbx-export-process"></a>Het FBX-exportproces

We gaan verder met de FBX-export van uw scène of scèneassets. Wanneer u assets exporteert, is het zinvol om alleen de objecten of assets te selecteren uit de scène die u wilt exporteren. Er kunnen bijvoorbeeld wel honderd objecten in een scène zitten. Als u er slechts dertig wilt gebruiken, heeft het geen zin om de hele scène te exporteren. 

Maak als volgt uw selectie:

1. Ga naar **File** > **Export Selection** om het dialoogvenster **Export Selection** te openen.
1. Selecteer in het vak **Files of type** de optie **FBX export** om de FBX-exportinstellingen weer te geven. De belangrijkste FBX-exportinstellingen zijn rood gemarkeerd in de afbeelding.

   ![FBX-export](media/FBX-exporting.jpg)

Afhankelijk van uw vereisten wilt u bijvoorbeeld een asset naar een client verzenden. Misschien wilt u niet een groot aantal textuurbestanden met de asset verzenden. U kunt de texturen desgewenst insluiten in het geëxporteerde FBX-bestand. Deze optie houdt in dat er slechts één bestand hoeft te worden ingepakt, maar dat de grootte van de FBX-asset aanzienlijk toeneemt. U kunt de optie voor het insluiten van texturen inschakelen door de optie **Embed Media** in te schakelen, zoals weergegeven.

> [!TIP]
> In dit voorbeeld heeft het bestand de naam gekregen om deze voorwaarde te weerspiegelen. Deze wijze van naamgeving is een goede manier om uw assets makkelijker terug te vinden. 

Nadat u de configuratie voor het exporteren hebt ingesteld, selecteert u rechtsonder **Export Selection**.

![Media insluiten](media/embedding-media.jpg)

## <a name="conclusion"></a>Conclusie

Over het algemeen lijken materialen van dit type realistisch, omdat ze zijn gebaseerd op natuurlijk licht. Hierdoor ontstaat een extra onderdompelingseffect dat ervoor zorgt dat de scène werkelijk lijkt te bestaan.

## <a name="next-steps"></a>Volgende stappen

U weet nu hoe u materialen kunt instellen met geavanceerde belichting voor objecten in een scène. U weet ook hoe u de objecten kunt exporteren naar de FBX-indeling die wordt ondersteund door Azure Remote Rendering. De volgende stap bestaat uit het converteren van het FBX-bestand en visualiseren in Azure Remote Rendering.

> [!div class="nextstepaction"]
> [Snelstart: een model converteren voor rendering](../../quickstarts\convert-model.md)