---
title: SMOTE
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de SMOTE-module in Azure Machine Learning om het aantal voorbeelden met een lage incidentie in een gegevensset te verhogen met behulp van oversampling.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: ed6d9e86143c3a5d6c97c4bd92a07c258bbd1bbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477456"
---
# <a name="smote"></a>SMOTE

In dit artikel wordt beschreven hoe u de SMOTE-module gebruiken in Azure Machine Learning-ontwerper (preview) om het aantal ondervertegenwoordigde aanvragen in een gegevensset te verhogen die wordt gebruikt voor machine learning. SMOTE is een betere manier om het aantal zeldzame gevallen te verhogen dan alleen bestaande gevallen te dupliceren.  

U verbindt de SMOTE-module met een *niet-gehandicapte gegevensset.* Er zijn veel redenen waarom een gegevensset onevenwichtig kan zijn. De categorie die u target, is bijvoorbeeld zeldzaam in de populatie of de gegevens kunnen moeilijk te verzamelen zijn. Normaal gesproken gebruikt u SMOTE wanneer de *klasse* die u wilt analyseren ondervertegenwoordigd is. 
  
De module retourneert een gegevensset die de oorspronkelijke voorbeelden bevat. Het retourneert ook een aantal synthetische minderheidsmonsters, afhankelijk van het percentage dat u opgeeft.  
  
## <a name="more-about-smote"></a>Meer over SMOTE

Synthetic Minority Oversampling Technique (SMOTE) is een statistische techniek om het aantal cases in uw dataset op een evenwichtige manier te verhogen. De module werkt door het genereren van nieuwe exemplaren uit bestaande minderheidsgevallen die u levert als input. Deze implementatie van SMOTE verandert *niets* aan het aantal gevallen in meerderheid.

De nieuwe exemplaren zijn niet alleen kopieën van bestaande minderheidszaken. In plaats daarvan neemt het algoritme voorbeelden van de *functieruimte* voor elke doelklasse en de dichtstbijzijnde buren. Het algoritme genereert vervolgens nieuwe voorbeelden die functies van de doelcase combineren met functies van zijn buren. Deze aanpak verhoogt de functies die beschikbaar zijn voor elke klasse en maakt de monsters meer algemeen.
  
SMOTE neemt de volledige gegevensset als invoer, maar verhoogt het percentage van alleen de minderheidsgevallen. Stel dat u een onevenwichtige gegevensset hebt waarbij slechts 1 procent van de aanvragen de doelwaarde A (de minderheidsklasse) heeft en 99 procent van de aanvragen de waarde B heeft. Als u het percentage minderheidsgevallen wilt verhogen tot twee keer het vorige percentage, voert u **200** in voor **het SMOTE-percentage** in de eigenschappen van de module.  
  
## <a name="examples"></a>Voorbeelden  

We raden u aan SMOTE te gebruiken met een kleine gegevensset om te zien hoe het werkt. In het volgende voorbeeld wordt de gegevensset Bloeddonatie gebruikt die beschikbaar is in Azure Machine Learning-ontwerper.
  
Als u de gegevensset toevoegt aan een pijplijn en **Visual selecteert** op de uitvoer van de gegevensset, u zien dat van de 748 rijen of aanvragen in de gegevensset 570 gevallen (76 procent) van klasse 0 zijn en 178 gevallen (24 procent) van klasse 1. Hoewel dit resultaat niet erg onevenwichtig is, vertegenwoordigt klasse 1 de mensen die bloed hebben gedoneerd, zodat deze rijen de *functieruimte* bevatten die u wilt modelleren.
 
Als u het aantal aanvragen wilt verhogen, u de waarde van **het SMOTE-percentage**als volgt instellen door veelvouden van 100 te gebruiken:

||Klasse 0|Klasse 1|totaal|  
|-|-------------|-------------|-----------|  
|Oorspronkelijke gegevensset<br /><br /> (gelijk aan **SMOTE-percentage** = **0**)|570<br /><br /> 76%|178<br /><br /> 24%|748|  
|**SMOTE-percentage** = **100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|**SMOTE-percentage** = **200**|570<br /><br /> 52%|534<br /><br /> 48%|1,104|  
|**SMOTE-percentage** = **300**|570<br /><br /> 44%|712<br /><br /> 56%|1,282|  
  
> [!WARNING]
> Het verhogen van het aantal gevallen met behulp van SMOTE is niet gegarandeerd om nauwkeurigere modellen te produceren. Probeer pipelining met verschillende percentages, verschillende functiesets en verschillende aantallen naaste buren om te zien hoe het toevoegen van cases uw model beïnvloedt.  
  
## <a name="how-to-configure-smote"></a>SMOTE configureren
  
1.  Voeg de SMOTE-module toe aan uw pijplijn. U de module vinden onder **datatransformatiemodules**in de categorie **Manipulatie.**

2. Verbind de gegevensset die u wilt promoten. Als u de functieruimte voor het bouwen van de nieuwe aanvragen wilt opgeven, door alleen specifieke kolommen te gebruiken of door sommige uit te sluiten, gebruikt u de module [Kolommen selecteren in gegevensset.](select-columns-in-dataset.md) U vervolgens de kolommen isoleren die u wilt gebruiken voordat u SMOTE gebruikt.
  
    Anders is het maken van nieuwe aanvragen via SMOTE gebaseerd op *alle* kolommen die u als invoer verstrekt. Ten minste één kolom van de functiekolommen is numeriek.
  
3.  Controleer of de kolom die het label of de doelklasse bevat, is geselecteerd. SMOTE accepteert alleen binaire labels.
  
4.  De SMOTE-module identificeert automatisch de minderheidsklasse in de labelkolom en krijgt vervolgens alle voorbeelden voor de minderheidsklasse. Alle kolommen kunnen geen NaN-waarden hebben.
  
5.  Voer in de optie **SMOTE-percentage** een heel getal in dat het streefpercentage van minderheidsgevallen in de uitvoergegevensset aangeeft. Bijvoorbeeld:  
  
    - U voert **0**in. De SMOTE-module retourneert exact dezelfde gegevensset die u als invoer hebt opgegeven. Het voegt geen nieuwe minderheidszaken toe. In deze gegevensset is het klassenaandeel niet gewijzigd.  
  
    - U voert **100**in. De SMOTE-module genereert nieuwe minderheidscases. Het voegt hetzelfde aantal minderheidsgevallen toe die zich in de oorspronkelijke gegevensset bevonden. Omdat SMOTE het aantal gevallen in meerderheid niet verhoogt, is het aantal gevallen van elke klasse gewijzigd.  
  
    - U voert **200**in. De module verdubbelt het percentage minderheidsgevallen ten opzichte van de oorspronkelijke gegevensset. Dit *leidt niet* tot twee keer zoveel minderheidszaken als voorheen. Integendeel, de grootte van de gegevensset wordt zo vergroot dat het aantal gevallen in de meerderheid gelijk blijft. Het aantal minderheidszaken wordt verhoogd totdat het overeenkomt met de gewenste procentuele waarde.  
  
    > [!NOTE]
    > Gebruik slechts veelvouden van 100 voor het SMOTE-percentage.

6.  Gebruik de optie **Aantal buren** om de grootte te bepalen van de functieruimte die het SMOTE-algoritme gebruikt bij het bouwen van nieuwe aanvragen. Een dichtstbijzijnde buurman is een rij gegevens (een aanvraag) die vergelijkbaar is met een doelaanvraag. De afstand tussen twee gevallen wordt gemeten door de gewogen vectoren van alle functies te combineren.  
  
    + Door het verhogen van het aantal naaste buren, krijg je functies uit meer gevallen.
    + Door het aantal dichtstbijzijnde buren laag te houden, gebruikt u functies die meer lijken op die in het oorspronkelijke voorbeeld.  
  
7. Voer een waarde in het vak **Random seed** in als u dezelfde resultaten wilt garanderen ten opzichte van runs van dezelfde pijplijn, met dezelfde gegevens. Anders genereert de module een willekeurig zaad op basis van de klokwaarden van de processor wanneer de pijplijn wordt geïmplementeerd. De generatie van een willekeurig zaad kan leiden tot iets andere resultaten over loopt.

8. Verzend de pijplijn.  
  
   De uitvoer van de module is een gegevensset die de oorspronkelijke rijen bevat plus een aantal toegevoegde rijen met minderheidsaanvragen.  

## <a name="technical-notes"></a>Technische notities

+ Wanneer u een model publiceert dat de **SMOTE-module** gebruikt, verwijdert u **SMOTE** uit de voorspellende pijplijn voordat het wordt gepubliceerd als een webservice. De reden hiervoor is dat SMOTE is bedoeld voor het verbeteren van een model tijdens de training, niet voor het scoren. Er kan een fout worden gemaakt als een gepubliceerde voorspellende pijplijn de SMOTE-module bevat.

+ U vaak betere resultaten krijgen als u ontbrekende waarden opschonen of andere transformaties toepast om gegevens op te lossen voordat u SMOTE toepast. 

+ Sommige onderzoekers hebben onderzocht of SMOTE effectief is op hoogdimensionale of schaarse gegevens, zoals gegevens die worden gebruikt in tekstclassificatie of genomicsdatasets. Dit document heeft een goede samenvatting van de effecten en van de theoretische validiteit van de toepassing van SMOTE in dergelijke gevallen: [Blagus en Lusa: SMOTE voor hoogdimensionale klasse-onevenwichtige gegevens](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106).

+ Als SMOTE niet effectief is in uw gegevensset, zijn andere benaderingen die u mogelijk overweegt:
  + Methoden voor oversampling van de minderheidszaken of ondersteekproeven van de meeste gevallen.
  + Ensembletechnieken die de leerling rechtstreeks helpen door clustering, zakken of adaptieve boosting te gebruiken.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 

