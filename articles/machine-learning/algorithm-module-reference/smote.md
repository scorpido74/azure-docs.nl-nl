---
title: SMOTE
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de SMOTE-module in Azure Machine Learning-service om het aantal voor beelden met minder incidenten in een gegevensset te verhogen door gebruik te maken van oversteek proeven.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 072268af52ebf7d3bede564d8c949eec9fc9f625
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516186"
---
# <a name="smote"></a>SMOTE

In dit artikel wordt beschreven hoe u de **SMOTE** -module in azure machine learning Designer (preview) gebruikt om het aantal ondergemaakte cases in een gegevensset die wordt gebruikt voor machine learning te verg Roten. SMOTE is een betere manier om het aantal zeldzame gevallen te verhogen dan alleen bestaande aanvragen te dupliceren.  

 U verbindt de SMOTE-module met een gegevensset die niet in *balans*is. Er zijn verschillende redenen waarom een gegevensset niet in balans kan worden gegeven: de categorie die u wilt richten, is mogelijk zeldzaam in de populatie of het kan lastig zijn om de gegevens te verzamelen. Normaal gesp roken gebruikt u SMOTE als de *klasse* die u wilt analyseren, wordt weer gegeven. 
  
 De module retourneert een gegevensset die de oorspronkelijke voor beelden bevat, plus een extra aantal synthetische minderheids voorbeelden, afhankelijk van het percentage dat u opgeeft.  
  
### <a name="more-about-smote"></a>Meer informatie over SMOTE

**SMOTE** staat voor *synthetische minderheids bemonsterings techniek*. Dit is een statistische techniek voor het verhogen van het aantal cases in uw gegevensset op een evenwichtige manier.  De module werkt door nieuwe instanties te genereren op basis van bestaande minderheids gevallen die u als invoer opgeeft. Deze implementatie van SMOTE heeft **geen** invloed op het aantal hoofd gevallen.

De nieuwe instanties zijn niet alleen kopieën van bestaande minderheids gevallen; in plaats daarvan worden voor beelden van de *functie ruimte* voor elke doel klasse en de dichtstbijzijnde neighbors gebruikt en worden nieuwe voor beelden gegenereerd waarin de functies van de doel case worden gecombineerd met functies van de neighbors. Met deze methode worden de beschik bare functies voor elke klasse verhoogd en worden de voor beelden algemeen uitgebreid.
  
SMOTE neemt de hele gegevensset als invoer, maar het percentage van de minderheids deelnemingen wordt verhoogd. Stel bijvoorbeeld dat u een niet-sluitende gegevensset hebt waarbij slechts 1% van de gevallen de doel waarde A (de klasse minderheid) heeft en 99% van de gevallen de waarde B heeft. Als u het percentage minderheids deelnemingen wilt verhogen naar twee maal het vorige percentage, voert u 200 in voor **SMOTE percentage** in de eigenschappen van de module.  
  
## <a name="examples"></a>Voorbeelden  

We raden u aan om **SMOTE** te gebruiken met een kleine gegevensset om te zien hoe het werkt. In het volgende voor beeld wordt de bloeddonatie-gegevensset gebruikt die beschikbaar is in Azure Machine Learning Designer.
  
Als u de gegevensset aan een pijp lijn toevoegt en op **visualiseren** klikt op de uitvoer van de gegevensset, ziet u dat, van de 748-rijen of-cases in de gegevensset, er 570 cases zijn (76%) van klasse 0 en 178 cases (24%) van klasse 1. Hoewel dit niet terribly niet is gebalanceerd, staat klasse 1 voor de mensen die bloed hebben gedoneerd en daarom bevatten deze rijen de *functie ruimte* die u wilt model leren.
 
Als u het aantal cases wilt verhogen, kunt u als volgt de waarde van het **SMOTE percentage**instellen met behulp van veelvouden van 100:

||Klasse 0|Klasse 1|eind|  
|-|-------------|-------------|-----------|  
|Oorspronkelijke gegevensset<br /><br /> (gelijk aan **SMOTE percentage** = **0**)|570<br /><br /> 76%|178<br /><br /> uur|748|  
|**SMOTE percentage** = **100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|**SMOTE percentage** = **200**|570<br /><br /> 52%|534<br /><br /> 48%|1104|  
|**SMOTE percentage** = **300**|570<br /><br /> 44%|712<br /><br /> 56%|1282|  
  
> [!WARNING]
>  Het verhogen van het aantal cases met behulp van SMOTE is niet gegarandeerd om nauw keurigere modellen te maken. U moet pipelineing met verschillende percentages, verschillende functie sets en verschillende aantallen dichtstbijzijnde neighbors proberen om te zien hoe het toevoegen van aanvragen van invloed is op uw model.  
  
## <a name="how-to-configure-smote"></a>SMOTE configureren
  
1.  Voeg de module SMOTE toe aan de pijp lijn. De module onder de modules voor gegevens transformatie vindt u in de categorie manipulatie.

2. Verbind de gegevensset die u wilt verhogen. Als u de functie ruimte wilt opgeven voor het maken van de nieuwe cases, hetzij door alleen specifieke kolommen te gebruiken, of door een deel ervan uit te sluiten, gebruikt u de module [select columns in dataset](select-columns-in-dataset.md) om de kolommen te isoleren die u wilt gebruiken voordat u **SMOTE**gebruikt.
  
    Anders is het maken van nieuwe cases met **SMOTE** gebaseerd op **alle** kolommen die u opgeeft als invoer. Ten minste één kolom van de functie kolommen is numeriek.
  
3.  Zorg ervoor dat de kolom met het label of de doel klasse is geselecteerd.  **SMOTE** accepteert alleen binaire labels.
  
4.  De **SMOTE** -module identificeert automatisch de klasse minderheid in de kolom Label en vervolgens worden alle voor beelden voor de klasse minderheid opgehaald. Alle kolommen mogen geen NaN hebben.
  
5.  Typ in de optie **percentage SMOTE** een geheel getal dat het doel percentage van minderheids deelnemingen in de uitvoer gegevensset aangeeft. Bijvoorbeeld:  
  
    - U typt **0** (%). De SMOTE-module retourneert precies dezelfde gegevensset die u hebt opgegeven als invoer, waardoor er geen nieuwe minderheids aanvragen worden toegevoegd. In deze gegevensset is de klasse-verhouding niet gewijzigd.  
  
    -   U typt **100** (%). In de SMOTE-module worden nieuwe minderheids gevallen gegenereerd en worden hetzelfde aantal minderheids aanvragen toegevoegd als de oorspronkelijke gegevensset. Omdat SMOTE het aantal hoofd gevallen niet verhoogt, is het gedeelte van de cases van elke klasse nu gewijzigd.  
  
    -   U typt **200** (%). De module verdubbelt het percentage minderheids gevallen vergeleken met de oorspronkelijke gegevensset. Dit leidt **niet** tot twee keer zoveel minderheids gevallen als voorheen.  In plaats daarvan wordt de grootte van de gegevensset zo verhoogd dat het aantal meerderheids gevallen hetzelfde blijft, en het aantal minderheids gevallen wordt verhoogd totdat het overeenkomt met de gewenste percentage waarde.  
  
    > [!NOTE]
    > Gebruik alleen veelvouden van 100 voor het SMOTE-percentage.

6.  Gebruik het **aantal dichtstbijzijnde neighbors** om de grootte te bepalen van de functie ruimte die het SMOTE-algoritme gebruikt bij het bouwen van nieuwe cases. Een *dichtstbijzijnde neighbor* is een rij met gegevens (een aanvraag) die vergelijkbaar is met een doel aanvraag. De afstand tussen twee gevallen wordt gemeten door de gewogen vectoren van alle functies te combi neren.  
  
     + Door het aantal dichtstbijzijnde buren te verhogen, profiteert u van meer zaken over functies.
     + Door het aantal dichtstbijzijnde neighbors te bewaren, kunt u gebruikmaken van functies die meer lijken op die in de oorspronkelijke steek proef.  
  
7. Typ een waarde in het tekstvak **wille keurig Seed** als u dezelfde resultaten wilt hebben als voor uitvoeringen van dezelfde pijp lijn, met dezelfde gegevens. Anders genereert de module een wille keurige Seed op basis van de klok waarden van de processor wanneer de pijp lijn wordt geïmplementeerd. Dit kan ertoe leiden dat er enigszins verschillende resultaten worden uitgevoerd.

8. Voer de pijplijn uit.  
  
     De uitvoer van de module is een gegevensset met de oorspronkelijke rijen plus een aantal toegevoegde rijen met minderheids gevallen.  

## <a name="technical-notes"></a>Technische opmerkingen

+ Wanneer u een model publiceert dat gebruikmaakt van de **SMOTE** -module, verwijdert u **SMOTE** uit de voorspellende pijp lijn voordat deze wordt gepubliceerd als een webservice.  De reden hiervoor is dat SMOTE is bedoeld voor het verbeteren van een model tijdens de training en niet is bedoeld voor scores.  Mogelijk wordt er een fout melding weer geven als een gepubliceerde voorspellende pijp lijn de SMOTE-module bevat.

+ U kunt vaak betere resultaten krijgen als u ontbrekende waarde opschonen of andere trans formaties toepast om gegevens te corrigeren voordat u SMOTE toepast. 

+ Sommige onderzoekers hebben onderzocht of SMOTE effectief is op zeer dimensionale of sparse gegevens, zoals die worden gebruikt in tekst classificaties of Genomics. Dit document bevat een goed overzicht van de effecten en de theoretische geldigheid van het Toep assen van SMOTE in de volgende gevallen: [Blagus en LUSA: SMOTE voor grootschalige, gebalanceerde gegevens](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106)

    Als SMOTE niet effectief is in uw gegevensset, kunt u overwegen om diverse methoden te gebruiken voor het overmeten van de minderheids gevallen of het ondersampling van de hoofd gevallen, evenals ensemble-technieken die de kenniser rechtstreeks helpen, door gebruik te maken van clustering, bebagging of adaptief Boosting.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 

