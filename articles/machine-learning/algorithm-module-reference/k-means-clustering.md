---
title: 'K-Means Clustering: Module-referentie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de clustermodule K-Means in de Azure Machine Learning om clusteringmodellen te trainen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 9606768288cc74afc24491149eb471944f45e2dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921161"
---
# <a name="module-k-means-clustering"></a>Module: K-Means Clustering

In dit artikel wordt beschreven hoe u de *clustermodule K-Means* in Azure Machine Learning-ontwerper (preview) gebruiken om een ongetraind k-means-clusteringmodel te maken. 
 
K-means is een van de eenvoudigste en *bekendste ongecontroleerde* leeralgoritmen. U het algoritme gebruiken voor verschillende machine learning-taken, zoals: 

* [Het detecteren van abnormale gegevens](https://msdn.microsoft.com/magazine/jj891054.aspx).
* Tekstdocumenten clusteren.
* Gegevenssets analyseren voordat u andere classificatie- of regressiemethoden gebruikt. 

Als u een clustermodel wilt maken, gaat u als:

* Voeg deze module toe aan uw pijplijn.
* Een gegevensset verbinden.
* Stel parameters in, zoals het aantal clusters dat u verwacht, de afstandsstatistiek die u moet gebruiken bij het maken van de clusters, enzovoort. 
  
Nadat u de hyperparameters van de module hebt geconfigureerd, sluit u het ongetrainde model aan op het [treinclustermodel.](train-clustering-model.md) Omdat het K-means-algoritme een leermethode zonder toezicht is, is een labelkolom optioneel. 

+ Als uw gegevens een label bevatten, u de labelwaarden gebruiken om de selectie van de clusters te begeleiden en het model te optimaliseren. 

+ Als uw gegevens geen label hebben, maakt het algoritme clusters die mogelijke categorieën vertegenwoordigen, uitsluitend op basis van de gegevens.  

##  <a name="understand-k-means-clustering"></a>K-means clustering begrijpen
 
In het algemeen gebruikt clustering iteratieve technieken om gevallen in een gegevensset te groeperen in clusters die vergelijkbare kenmerken bezitten. Deze groeperingen zijn nuttig voor het verkennen van gegevens, het identificeren van afwijkingen in de gegevens en uiteindelijk voor het maken van voorspellingen. Clusteringmodellen kunnen u ook helpen relaties in een gegevensset te identificeren die u mogelijk niet logisch afleiden door te bladeren of eenvoudige observatie. Om deze redenen wordt clustering vaak gebruikt in de vroege fasen van machine learning-taken, om de gegevens te verkennen en onverwachte correlaties te ontdekken.  
  
 Wanneer u een clustermodel configureert met behulp van de methode K-means, moet u een doelnummer *k* opgeven dat het gewenste aantal *centroids* in het model aangeeft. De centroid is een punt dat representatief is voor elk cluster. Het K-means-algoritme wijst elk binnenkomend gegevenspunt toe aan een van de clusters door de som van vierkanten binnen het cluster te minimaliseren. 
 
Wanneer het de trainingsgegevens verwerkt, begint het K-means algoritme met een eerste set willekeurig gekozen centroids. Centroids dienen als uitgangspuntvoor de clusters, en ze passen Lloyd's algoritme toe om hun locaties iteratief te verfijnen. Het K-means algoritme stopt met het bouwen en verfijnen van clusters wanneer het voldoet aan een of meer van deze voorwaarden:  
  
-   De centroids stabiliseren, wat betekent dat de clustertoewijzingen voor afzonderlijke punten niet meer veranderen en het algoritme is geconvergeerd op een oplossing.  
  
-   Het algoritme heeft het opgegeven aantal iteraties voltooid.  
  
 Nadat u de trainingsfase hebt voltooid, gebruikt u de module [Gegevens toewijzen aan clusters](assign-data-to-clusters.md) om nieuwe aanvragen toe te wijzen aan een van de clusters die u hebt gevonden met behulp van het K-means-algoritme. U voert clustertoewijzing uit door de afstand tussen de nieuwe aanvraag en de centroid van elk cluster te berekenen. Elke nieuwe aanvraag wordt toegewezen aan het cluster met de dichtstbijzijnde centroid.  

## <a name="configure-the-k-means-clustering-module"></a>De clustermodule K-Means configureren
  
1.  Voeg de **clustermodule K-Means clustering** toe aan uw pijplijn.  
  
2.  Als u wilt opgeven hoe u het model wilt trainen, selecteert u de optie **Trainer-modus maken.**  
  
    -   **Eén parameter:** als u de exacte parameters weet die u in het clustermodel wilt gebruiken, u een specifieke set waarden als argumenten opgeven.  
  
3.  Typ **bij Aantal centroids**het aantal clusters waarmee het algoritme moet beginnen.  
  
     Het model is niet gegarandeerd om precies dit aantal clusters te produceren. Het algoritme begint met dit aantal datapunten en iterates om de optimale configuratie te vinden.  
  
4.  De eigenschappen **Initialisatie** wordt gebruikt om het algoritme op te geven dat wordt gebruikt om de oorspronkelijke clusterconfiguratie te definiëren.  
  
    -   **Eerste N**: Sommige eerste aantal gegevenspunten worden gekozen uit de gegevensset en worden gebruikt als eerste middel. 
    
         Deze methode wordt ook wel de *Forgy-methode*genoemd.  
  
    -   **Willekeurig**: Het algoritme plaatst willekeurig een gegevenspunt in een cluster en berekent vervolgens het oorspronkelijke gemiddelde als de centroid van de willekeurig toegewezen punten van het cluster. 

         Deze methode wordt ook wel de *willekeurige partitiemethode* genoemd.  
  
    -   **K-Means++**: Dit is de standaardmethode voor het initialiseren van clusters.  
  
         De **K-means ++** algoritme werd voorgesteld in 2007 door David Arthur en Sergei Vassilvitskii om slechte clustering te voorkomen door de standaard K-means algoritme. **K-means++** verbetert standaard K-middelen door een andere methode te gebruiken voor het kiezen van de eerste clustercentra.  
  
    
5.  Typ **bij Willekeurig getalzaad**eventueel een waarde die u wilt gebruiken als zaad voor de clusterinitialisatie. Deze waarde kan een significant effect hebben op de clusterselectie.  
  
6.  Kies bij **Metric**de functie die u wilt gebruiken voor het meten van de afstand tussen clustervectoren of tussen nieuwe gegevenspunten en de willekeurig gekozen centroid. Azure Machine Learning ondersteunt de volgende statistieken over clusterafstand:  
  
    -   **Euclicaan**: De Euclidische afstand wordt vaak gebruikt als een maat voor clusterspreiding voor K-middelen clustering. Deze statistiek heeft de voorkeur omdat het de gemiddelde afstand tussen punten en de centroids minimaliseert.
  
7.  Voor **iteraties**typt u het aantal keren dat het algoritme moet herhalen over de trainingsgegevens voordat de selectie van centroids wordt afgerond.  
  
     U deze parameter aanpassen om de nauwkeurigheid af te balanceren tegen de trainingstijd.  
  
8.  Kies bij **Labelmodus toewijzen**een optie die aangeeft hoe een labelkolom, als deze aanwezig is in de gegevensset, moet worden behandeld.  
  
     Omdat K-means clustering een machine learning-methode zonder toezicht is, zijn labels optioneel. Als uw gegevensset echter al een labelkolom heeft, u deze waarden gebruiken om de selectie van de clusters te begeleiden of u opgeven dat de waarden worden genegeerd.  
  
    -   **Labelkolom negeren**: De waarden in de labelkolom worden genegeerd en worden niet gebruikt bij het bouwen van het model.
  
    -   **Ontbrekende waarden invullen**: de waarden van de labelkolom worden gebruikt als functies om de clusters te bouwen. Als er rijen ontbreken, wordt de waarde toegerekend met behulp van andere functies.  
  
    -   **Overschrijven van het dichtst bij het midden**: De waarden van de labelkolom worden vervangen door voorspelde labelwaarden met behulp van het label van het punt dat het dichtst bij de huidige centroid ligt.  

8.  Selecteer de optie **Functies normaliseren** als u functies wilt normaliseren voordat u gaat trainen.
  
     Als u normalisatie toepast, worden de gegevenspunten `[0,1]` vóór de training genormaliseerd door MinMaxNormalizer.

10. Train het model.  
  
    -   Als u **De trainermodus maken** instelt op Enkele **parameter,** voegt u een gelabelde gegevensset toe en traint u het model met de module [Treinclusteringmodel.](train-clustering-model.md)  
  
## <a name="results"></a>Resultaten

Nadat u klaar bent met het configureren en trainen van het model, hebt u een model dat u gebruiken om scores te genereren. Er zijn echter meerdere manieren om het model te trainen en meerdere manieren om de resultaten te bekijken en te gebruiken: 

### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Een momentopname van het model vastleggen in uw werkruimte

Als u de module [Treinclusteringmodel](train-clustering-model.md) hebt gebruikt:

1. Selecteer de module **Treinclusteringmodel** en open het rechterdeelvenster.

2. Selecteer het tabblad **Uitvoer.** Selecteer het pictogram **Gegevensset registreren** om een kopie van het getrainde model op te slaan.

Het opgeslagen model vertegenwoordigt de trainingsgegevens op het moment dat u het model hebt opgeslagen. Als u later de trainingsgegevens bijwerkt die in de pijplijn worden gebruikt, wordt het opgeslagen model niet bijgewerkt. 

### <a name="see-the-clustering-result-dataset"></a>Bekijk de gegevensset voor het clusteren van resultaten 

Als u de module [Treinclusteringmodel](train-clustering-model.md) hebt gebruikt:

1. Klik met de rechtermuisknop op de module **Treinclusteringmodel.**

2. Selecteer **Visualiseren**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Tips voor het genereren van het beste clustermodel  

Het is bekend dat *het zaaiproces* dat wordt gebruikt tijdens het clusteren het model aanzienlijk kan beïnvloeden. Zaaien betekent de eerste plaatsing van punten in potentiële centroids.
 
Als de gegevensset bijvoorbeeld veel uitschieters bevat en een uitschieter wordt gekozen om de clusters te zaaien, passen er geen andere gegevenspunten goed bij dat cluster en kan het cluster een singleton zijn. Dat wil zeggen, het zou kunnen hebben slechts een punt.  
  
U dit probleem op een paar manieren vermijden:  
  
-   Wijzig het aantal centroids en probeer meerdere zaadwaarden uit.  
  
-   Maak meerdere modellen, waarbij de statistiek wordt gevarieerd of meer wordt aangepast.  
  
In het algemeen is het bij clusteringmodellen mogelijk dat een bepaalde configuratie resulteert in een lokaal geoptimaliseerde set clusters. Met andere woorden, de set clusters die door het model wordt geretourneerd, past alleen bij de huidige gegevenspunten en is niet generaliseerbaar voor andere gegevens. Als u een andere initiële configuratie gebruikt, kan de K-means-methode een andere, superieure configuratie vinden. 

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 
