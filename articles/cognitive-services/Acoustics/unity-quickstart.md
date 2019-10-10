---
title: Snelstartgids voor project akoestische
titlesuffix: Azure Cognitive Services
description: Gebruik voorbeeld inhoud om te experimenteren met de ontwerp besturings elementen voor project akoestische in eenheid en implementatie op Windows Desktop.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243023"
---
# <a name="project-acoustics-unity-quickstart"></a>Snelstartgids voor project akoestische eenheid
Gebruik de voorbeeld inhoud van de project akoestische voor eenheid om te experimenteren met ontwerp besturings elementen met simulatie-ondersteuning.

Software vereisten:
* [Unity 2018.2 +](https://unity3d.com) voor Windows
* [Pakket met voorbeeld inhoud voor project akoestische](https://www.microsoft.com/download/details.aspx?id=57346)

Wat is opgenomen in het voorbeeld pakket?
* Eenheids scène met geometrie, geluids bronnen en gameplay-besturings elementen
* Invoeg toepassing voor project akoestische
* Geïntegreerde akoestische activa voor de scène

## <a name="import-the-sample-package"></a>Het voorbeeld pakket importeren
Importeer het voorbeeld pakket in een nieuw Unity-project.
1. Ga in Unity naar **Assets** > **import pakket** > **aangepast pakket**.

    ![De eenheid opties import pakket](media/import-package.png)  

1. Kies **ProjectAcoustics. unitypackage**.

1. Selecteer de knop **importeren** om het Unity-pakket in uw project te integreren.  
  
    ![Het dialoog venster unit import package importeren](media/import-dialog.png)  

Als u het pakket wilt importeren in een bestaand project, Raadpleeg [Unit-integratie](unity-integration.md) voor aanvullende stappen en notities.

>[!NOTE]
>Er worden verschillende fout berichten weer gegeven in het console logboek nadat het importeren is voltooid. Ga door naar de volgende stap en start Unity opnieuw.

## <a name="restart-unity"></a>Eenheid opnieuw opstarten
Voor het maken-gedeelte van de akoestische Toolkit is .NET 4 vereist. *x* Scripting runtime-versie. Met de pakket import worden de instellingen van uw Unity-speler bijgewerkt. De eenheid moet opnieuw worden opgestart om deze instelling van kracht te laten worden. Als u wilt controleren of de instelling van kracht is, opent u de instellingen van de **speler** :

![Het menu eenheids project instellingen](media/player-settings.png)  

![Het deel venster instellingen voor Unity Player met .NET 4. x geselecteerd](media/net45.png)  

>[!NOTE]
>Deze scherm opname is gemaakt op eenheid 2018. *x*. De installatie kopie kan verschillen in nieuwere versies van unit.

## <a name="open-the-project-acoustics-bake-window"></a>Open het maken-venster voor project akoestische
Onder eenheid selecteert u **akoestische** in het menu **venster** .

![De eenheids-editor met de geluids optie is gemarkeerd in het menu venster](media/window-acoustics.png)

Er wordt een zwevend **akoestische** -venster geopend. In dit venster kunt u de eigenschappen van de akoestische simulatie instellen.

![De eenheids editor voor het akoestische venster is geopend](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>Experimenteer met de ontwerp besturings elementen
Open de voorbeeld scène in de map *ProjectAcousticsSample* en selecteer de afspeel knop in de Unity-editor. Gebruik de sleutels W, A, S en D en de muis om door te gaan. Als u wilt vergelijken hoe de scène klinkt met en zonder akoestische, selecteert u de R-toets totdat de overlay-tekst rood wordt weer gegeven en ' geluids fragmenten: uitgeschakeld '. Selecteer F1 om sneltoetsen te bekijken voor meer besturings elementen. U kunt ook met de rechter muisknop op een actie selecteren en vervolgens met de linkermuisknop klikken om die actie uit te voeren.

Het *AcousticsAdjust* -script wordt gekoppeld aan de geluids bronnen in de voorbeeld scène. Hiermee schakelt u de ontwerp parameters per bron in.

![Het unit AcousticsAdjust-script](media/acoustics-adjust.png)

In de volgende secties worden enkele van de effecten besproken die u kunt maken met behulp van de beschik bare besturings elementen. Voor gedetailleerde informatie over elk besturings element, raadpleegt u de zelf studie over het ontwerpen van de [project akoestische unit-ontwerp](unity-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Afzwakking op afstand wijzigen
De digitale signaal verwerking in de spatializer-invoeg toepassing voor de geluids sterkte van het **project** respecteert de verzwakking per bron op afstand die is ingebouwd in de Unity-editor. De besturings elementen voor verzwakking op afstand bevinden zich in het **audio bron** onderdeel, dat zich in het deel venster **Inspector** bevindt met geluids bronnen onder **3D-geluids instellingen**:

![Het deel venster Opties voor afstands afzwakking](media/distance-attenuation.png)

De geluids processen van het project worden berekend in een ' simulatie regio ' die is gecentreerd rond de locatie van de speler. De akoestische activa in het voor beeld van het pakket zijn geïntegreerde op een simulatie gebied van 45 meters rond de speler. De geluids afzwakking moet dus worden ontworpen om op ongeveer 45 m te vallen.

### <a name="modify-occlusion-and-transmission"></a>Bedekking en overdracht wijzigen
* Als de **bedekking** vermenigvuldiger groter is dan 1 (de standaard waarde is 1), is bedekking exaggerated. Als u het bedekking effect subtieler wilt maken, stelt u dit in op minder dan 1.

* Als u de verzen ding via de wand wilt inschakelen, verplaatst u de schuif regelaar voor **verzen ding (DB)** van de laagste instelling weg.

### <a name="modify-wetness-for-a-source"></a>Wetness voor een bron wijzigen
* Gebruik **Perceptueel, afstands verdraaiing**om te wijzigen hoe snel wetness verandert met afstand. Met behulp van simulatie worden door project akoestische natte niveaus berekend, waardoor waarnemingen op afstand worden geboden en probleemloos kan worden afgestemd op afstand. Verhoog de afstands verdraaiing exaggerates dit effect door toenemende natte niveaus te verhogen. Het verdraaien van waarden kleiner dan 1 maken de op afstand gebaseerde reverberation subtieler.

   Wijzig de **Wetness (DB-instelling)** om nauw keurige aanpassingen te maken voor dit effect.

* Als u de verval tijd in de ruimte wilt verg Roten, past u de **verval tijd**aan. Als het simulatie resultaat voor een bepaald locatie paar van de bron-listener een verval tijd van 1,5 seconden is en de schaal voor de **verval tijd** is ingesteld op 2, is de verval tijd die wordt toegepast op de bron 3 seconden.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [ontwerp besturings elementen op basis van op eenheid gebaseerde project akoestische](unity-workflow.md).
* Bekijk de concepten verder achter het [ontwerp proces](design-process.md).
* [Maak een Azure-account](create-azure-account.md) om de maken-en maken-processen te verkennen.
