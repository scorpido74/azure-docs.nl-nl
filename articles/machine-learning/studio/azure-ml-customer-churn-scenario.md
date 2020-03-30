---
title: Klantverloop analyseren
titleSuffix: ML Studio (classic) - Azure
description: Case study van het ontwikkelen van een geïntegreerd model voor het analyseren en scoren van klantverloop met Azure Machine Learning Studio (klassiek).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 4cf918abae51ca330054ef86e57095d29a21a37a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204525"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Klantverloop analyseren met Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

## <a name="overview"></a>Overzicht
In dit artikel wordt een referentie-implementatie gepresenteerd van een klantchurn-analyseproject dat is gebouwd met Azure Machine Learning Studio (klassiek). In dit artikel bespreken we bijbehorende generieke modellen voor het holistisch oplossen van het probleem van industriële klantverloop. We meten ook de nauwkeurigheid van modellen die zijn gebouwd met behulp van Machine Learning en beoordelen de richtingen voor verdere ontwikkeling.  

### <a name="acknowledgements"></a>Erkenningen
Dit experiment is ontwikkeld en getest door Serge Berger, Principal Data Scientist bij Microsoft, en Roger Barga, voorheen Product Manager voor Microsoft Azure Machine Learning Studio (klassiek). Het Azure-documentatieteam erkent dankbaar hun expertise en bedankt hen voor het delen van deze whitepaper.

> [!NOTE]
> De gegevens die voor dit experiment worden gebruikt, zijn niet openbaar beschikbaar. Zie: Sjabloon voor het model model voor het [verloop](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) van de detailhandel in [Azure AI Gallery](https://gallery.azure.ai/) voor een voorbeeld van het bouwen van een machine learning-model voor churn-analyse
> 
> 



## <a name="the-problem-of-customer-churn"></a>Het probleem van klantverloop
Bedrijven in de consumentenmarkt en in alle bedrijfssectoren hebben te maken met churn. Soms is churn buitensporig en beïnvloedt het beleid beslissingen. De traditionele oplossing is om churners met hoge neiging te voorspellen en hun behoeften aan te pakken via een conciërgeservice, marketingcampagnes of door speciale dispensaties toe te passen. Deze benaderingen kunnen variëren van industrie tot industrie. Ze kunnen zelfs variëren van een bepaald consumentencluster tot de andere binnen de ene bedrijfstak (bijvoorbeeld telecommunicatie).

De gemeenschappelijke factor is dat bedrijven deze speciale inspanningen voor klantenbinding moeten minimaliseren. Dus, een natuurlijke methodologie zou zijn om elke klant te scoren met de kans op churn en het adres van de top N ones. De top klanten misschien wel de meest winstgevende. In meer geavanceerde scenario's wordt bijvoorbeeld een winstfunctie ingezet bij de selectie van kandidaten voor speciale dispensatie. Deze overwegingen maken echter slechts deel uit van de volledige strategie voor het omgaan met churn. Bedrijven moeten ook rekening houden met risico's (en bijbehorende risicotolerantie), het niveau en de kosten van de interventie en plausibele segmentatie van klanten.  

## <a name="industry-outlook-and-approaches"></a>Vooruitzichten en benaderingen van de industrie
Verfijnde behandeling van churn is een teken van een volwassen industrie. Het klassieke voorbeeld is de telecommunicatie-industrie waar abonnees vaak van de ene provider naar de andere overstappen. Deze vrijwillige churn is een eerste zorg. Bovendien hebben providers veel kennis opgebouwd over *churn drivers*, wat de factoren zijn die klanten ertoe aanzetten om over te stappen.

Bijvoorbeeld, handset of apparaat keuze is een bekende driver van churn in de mobiele telefoon bedrijf. Dientengevolge, is een populair beleid om de prijs van een zaktelefoon voor nieuwe abonnees te subsidiëren en een volledige prijs aan bestaande klanten voor een verbetering in rekening te brengen. Historisch gezien heeft dit beleid ertoe geleid dat klanten van de ene provider naar de andere hoppen om een nieuwe korting te krijgen. Dit, op zijn beurt, heeft gevraagd aanbieders om hun strategieën te verfijnen.

Hoge volatiliteit in handset aanbod is een factor die snel ongeldig modellen van churn die zijn gebaseerd op de huidige handset modellen. Bovendien, mobiele telefoons zijn niet alleen telecommunicatie-apparaten, ze zijn ook mode-verklaringen (overweeg de iPhone). Deze sociale voorspellers vallen buiten het bereik van reguliere telecommunicatiegegevenssets.

Het netto resultaat voor modellering is dat je niet bedenken een goed beleid gewoon door het elimineren van bekende redenen voor churn. In feite is een continue modelleringsstrategie, inclusief klassieke modellen die categorische variabelen (zoals beslissingsbomen) kwantificeren, **verplicht**.

Met behulp van big data sets op hun klanten, organisaties zijn het uitvoeren van big data analytics (met name churn detectie op basis van big data) als een effectieve aanpak van het probleem. Meer informatie over de big data-aanpak van het probleem van churn vindt u in de sectie Aanbevelingen over ETL.  

## <a name="methodology-to-model-customer-churn"></a>Methodologie om klantverloop te modelleren
Een veelvoorkomend probleemoplossend proces om klantverloop op te lossen wordt weergegeven in cijfers 1-3:  

1. Met een risicomodel u overwegen hoe acties waarschijnlijkheid en risico beïnvloeden.
2. Met een interventiemodel u nagaan hoe het interventieniveau de kans op verloop van churn en de hoeveelheid customer lifetime value (CLV) kan beïnvloeden.
3. Deze analyse leent zich voor een kwalitatieve analyse die wordt geëscaleerd naar een proactieve marketingcampagne die zich richt op klantsegmenten om het optimale aanbod te leveren.  

![Diagram met de manier waarop risicotolerantie plus beslissingsmodellen bruikbare inzichten opleveren](./media/azure-ml-customer-churn-scenario/churn-1.png)

Deze toekomstgerichte aanpak is de beste manier om churn te behandelen, maar het komt met complexiteit: we moeten een multi-model archetype ontwikkelen en afhankelijkheden tussen de modellen traceren. De interactie tussen modellen kan worden ingekapseld zoals weergegeven in het volgende diagram:  

![Interactiediagram churn-model](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Figuur 4: Verenigd archetype met meerdere modellen*  

Interactie tussen de modellen is essentieel als we een holistische benadering van klantenbinding willen leveren. Elk model degradeert noodzakelijkerwijs in de loop van de tijd; daarom is de architectuur een impliciete lus (vergelijkbaar met het archetype dat is ingesteld door de CRISP-DM data mining-standaard, [***3***]).  

De algemene cyclus van risico-beslissing-marketing segmentatie /ontbinding is nog steeds een algemene structuur, die van toepassing is op veel zakelijke problemen. Churn analyse is gewoon een sterke vertegenwoordiger van deze groep van problemen, omdat het vertoont alle eigenschappen van een complex zakelijk probleem dat niet mogelijk is een vereenvoudigde voorspellende oplossing. De sociale aspecten van de moderne benadering van churn worden niet bijzonder benadrukt in de benadering, maar de sociale aspecten zijn ingekapseld in het modellerenarchetype, aangezien zij in om het even welk model zouden zijn.  

Een interessante toevoeging hier is big data analytics. De telecommunicatie- en retailbedrijven van vandaag verzamelen uitgebreide gegevens over hun klanten, en we kunnen gemakkelijk voorzien dat de behoefte aan connectiviteit met meerdere modellen een gemeenschappelijke trend zal worden, gezien opkomende trends zoals het Internet of Things en alomtegenwoordige apparaten, waarmee bedrijven slimme oplossingen in meerdere lagen kunnen gebruiken.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>Het modelleren van archetype implementeren in Machine Learning Studio (klassiek)
Gezien het beschreven probleem, wat is de beste manier om een geïntegreerde modellering en scoring aanpak te implementeren? In deze sectie laten we zien hoe we dit hebben bereikt met Azure Machine Learning Studio (klassiek).  

De multi-model benadering is een must bij het ontwerpen van een wereldwijd archetype voor churn. Zelfs het scoren (voorspellende) deel van de aanpak moet multi-model zijn.  

Het volgende diagram toont het prototype dat we hebben gemaakt, dat vier scorealgoritmen in Machine Learning Studio (klassiek) gebruikt om churn te voorspellen. De reden voor het gebruik van een multi-model benadering is niet alleen het creëren van een ensemble classifier om de nauwkeurigheid te verhogen, maar ook om te beschermen tegen over-fitting en prescriptieve functie selectie te verbeteren.  

![Schermafbeelding van een complexe Studio (klassieke) werkruimte met veel onderling verbonden modules](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Figuur 5: Prototype van een churn modellering aanpak*  

De volgende secties geven meer details over het prototype scoremodel dat we geïmplementeerd met behulp van Machine Learning Studio (klassiek).  

### <a name="data-selection-and-preparation"></a>Gegevensselectie en -voorbereiding
De gegevens die worden gebruikt om de modellen en scoreklanten te bouwen, werden verkregen uit een verticale CRM-oplossing, waarbij de gegevens werden versluierd om de privacy van klanten te beschermen. De gegevens bevatten informatie over 8.000 abonnementen in de VS en het combineert drie bronnen: het inrichten van gegevens (metagegevens van abonnementen), activiteitsgegevens (gebruik van het systeem) en klantgegevens. De gegevens bevatten geen bedrijfsgerelateerde informatie over de klanten; het bevat bijvoorbeeld geen loyaliteitsmetadata of creditscores.  

Voor de eenvoud zijn ETL- en datareinigingsprocessen buiten bereik omdat we ervan uitgaan dat de gegevensvoorbereiding al elders is gedaan.

Functieselectie voor modellering is gebaseerd op voorlopige significantiescore van de set voorspellers, opgenomen in het proces dat de willekeurige forestmodule gebruikt. Voor de implementatie in Machine Learning Studio (klassiek) hebben we het gemiddelde, de mediaan en de bereiken voor representatieve functies berekend. We hebben bijvoorbeeld aggregaten toegevoegd voor de kwalitatieve gegevens, zoals minimum- en maximumwaarden voor gebruikersactiviteit.

We hebben ook tijdelijke informatie vastgelegd voor de laatste zes maanden. We analyseerden gegevens voor een jaar en we vastgesteld dat zelfs als er statistisch significante trends, het effect op churn is sterk verminderd na zes maanden.  

Het belangrijkste punt is dat het hele proces, inclusief ETL, functieselectie en modellering is geïmplementeerd in Machine Learning Studio (klassiek), met behulp van gegevensbronnen in Microsoft Azure.   

De volgende diagrammen illustreren de gegevens die zijn gebruikt.  

![Schermafbeelding van een voorbeeld van de gegevens die worden gebruikt met ruwe waarden](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Figuur 6: Fragment van gegevensbron (versluierd)*  

![Schermafbeelding van statistische functies die zijn geëxtraheerd uit gegevensbron](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Figuur 7: Functies uit gegevensbron*
 

> Houd er rekening mee dat deze gegevens privé zijn en daarom kunnen het model en de gegevens niet worden gedeeld.
> Zie dit voorbeeldexperiment voor een vergelijkbaar model met openbaar beschikbare gegevens in de [Azure AI Gallery:](https://gallery.azure.ai/) [Telco Customer Churn](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Voor meer informatie over hoe u een churn-analysemodel implementeren met Cortana Intelligence Suite, raden we [deze video](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) ook aan van Senior Program Manager Wee Hyong Tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmen die in het prototype worden gebruikt
We gebruikten de volgende vier machine learning-algoritmen om het prototype te bouwen (geen aanpassing):  

1. Logistieke regressie (LR)
2. Verhoogde beslisboom (BT)
3. Gemiddelde perceptron (AP)
4. Ondersteuningsvectormachine (SVM)  

Het volgende diagram illustreert een gedeelte van het ontwerpoppervlak van het experiment, dat de volgorde aangeeft waarin de modellen zijn gemaakt:  

![Schermafbeelding van een klein gedeelte van het canvas voor studio-experimenten](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Figuur 8: Modellen maken in Machine Learning Studio (klassiek)*  

### <a name="scoring-methods"></a>Scoremethoden
We scoorden de vier modellen met behulp van een gelabelde trainingsdataset.  

We hebben ook de scoredataset voorgelegd aan een vergelijkbaar model dat is gebouwd met behulp van de desktopeditie van SAS Enterprise Miner 12. We hebben de nauwkeurigheid van het SAS-model en alle vier de Machine Learning Studio (klassieke) modellen gemeten.  

## <a name="results"></a>Resultaten
In deze sectie presenteren we onze bevindingen over de nauwkeurigheid van de modellen, gebaseerd op de scoredataset.  

### <a name="accuracy-and-precision-of-scoring"></a>Nauwkeurigheid en nauwkeurigheid van het scoren
Over het algemeen is de implementatie in Azure Machine Learning Studio (klassiek) achter SAS in nauwkeurigheid met ongeveer 10-15% (Area Under Curve of AUC).  

Echter, de belangrijkste metrische in churn is de verkeerde classificatie tarief: dat wil zeggen, van de top N churners zoals voorspeld door de classificatie, die van hen eigenlijk **niet** churn, en toch kreeg speciale behandeling? In het volgende diagram wordt deze misclassificatiesnelheid voor alle modellen vergeleken:  

![Gebied onder de curvegrafiek waarin de prestaties van 4 algoritmen worden vergeleken](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Figuur 9: Passau prototype gebied onder curve*

### <a name="using-auc-to-compare-results"></a>AUC gebruiken om resultaten te vergelijken
Area Under Curve (AUC) is een statistiek die een globale maatstaf voor *separabiliteit* vertegenwoordigt tussen de verdelingvan scores voor positieve en negatieve populaties. Het is vergelijkbaar met de traditionele Receiver Operator Characteristic (ROC) grafiek, maar een belangrijk verschil is dat de AUC-statistiek niet vereist dat u een drempelwaarde kiest. In plaats daarvan, het vat de resultaten over **alle** mogelijke keuzes. De traditionele ROC-grafiek toont daarentegen de positieve snelheid op de verticale as en het fout-positieve percentage op de horizontale as en de classificatiedrempel varieert.   

AUC wordt gebruikt als een maat voor de waarde voor verschillende algoritmen (of verschillende systemen) omdat het het mogelijk maakt modellen te vergelijken door middel van hun AUC waarden. Dit is een populaire aanpak in industrieën zoals meteorologie en biowetenschappen. AUC is dus een populair instrument voor het beoordelen van stijlvollere prestaties.  

### <a name="comparing-misclassification-rates"></a>Verkeerd classificatiepercentages vergelijken
We vergeleken de verkeerde classificatiepercentages op de betreffende dataset met behulp van de CRM-gegevens van ongeveer 8.000 abonnementen.  

* De SAS misclassificatie tarief was 10-15%.
* De Machine Learning Studio (klassieke) misclassificatie tarief was 15-20% voor de top 200-300 churners.  

In de telecommunicatie-industrie is het belangrijk om alleen die klanten aan te spreken die het hoogste risico lopen om te churn door hen een conciërgeservice of een andere speciale behandeling aan te bieden. In dat opzicht behaalt de (klassieke) implementatie van Machine Learning Studio resultaten op gelijke voet met het SAS-model.  

Tegelijkertijd is nauwkeurigheid belangrijker dan precisie, omdat we vooral geïnteresseerd zijn in het correct classificeren van potentiële churners.  

Het volgende diagram van Wikipedia toont de relatie in een levendige, gemakkelijk te begrijpen afbeelding:  

![Twee doelwitten. Een doel toont hit merken losjes gegroepeerd, maar in de buurt van de bulls-eye gemarkeerd "Lage nauwkeurigheid: goede waarheid, slechte precisie. Een ander doel strak gegroepeerd, maar ver van de bulls-eye gemarkeerd "Lage nauwkeurigheid: slechte waarheid, goede precisie"](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Figuur 10: Afweging tussen nauwkeurigheid en precisie*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Nauwkeurigheid en precisieresultaten voor een boost-beslissingsboommodel
In de volgende grafiek worden de ruwe resultaten van het scoren weergegeven met het Machine Learning-prototype voor het gepromote beslissingsstructuurmodel, dat toevallig de meest nauwkeurige van de vier modellen is:  

![Tabelfragment met nauwkeurigheid, precisie, terugroepen, F-Score, AUC, Gemiddeld logboekverlies en trainingslogboekverlies voor vier algoritmen](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Figuur 11: Gepromote kenmerken van het model van de beslissingsboom*

## <a name="performance-comparison"></a>Prestatievergelijking
We vergeleken de snelheid waarmee gegevens werden gescoord met behulp van de Machine Learning Studio (klassieke) modellen en een vergelijkbaar model gemaakt met behulp van de desktop editie van SAS Enterprise Miner 12.1.  

In de volgende tabel worden de prestaties van de algoritmen samengevat:  

*Tabel 1. Algemene prestaties (nauwkeurigheid) van de algoritmen*

| LR | BT | AP | Svm |
| --- | --- | --- | --- |
| Gemiddeld model |Het beste model |Slecht presterend |Gemiddeld model |

De modellen gehost in Machine Learning Studio (klassieke) beter dan SAS met 15-25% voor de snelheid van uitvoering, maar de nauwkeurigheid was grotendeels op gelijke voet.  

## <a name="discussion-and-recommendations"></a>Discussie en aanbevelingen
In de telecommunicatie-industrie zijn verschillende praktijken ontstaan om churn te analyseren, waaronder:  

* Statistieken afleiden voor vier fundamentele categorieën:
  * **Entiteit (bijvoorbeeld een abonnement)**. Basisinformatie verstrekken over het abonnement en/of de klant die het onderwerp is van churn.
  * **Activiteit**. Verkrijg alle mogelijke gebruiksinformatie die gerelateerd is aan de entiteit, bijvoorbeeld het aantal aanmeldingen.
  * **Klantenservice**. Oogst informatie uit logboeken voor klantenondersteuning om aan te geven of het abonnement problemen of interacties met de klantenservice had.
  * **Concurrerende en zakelijke gegevens**. Verkrijg alle informatie mogelijk over de klant (bijvoorbeeld, kan niet beschikbaar zijn of moeilijk te volgen).
* Gebruik het belang om de selectie van functies te stimuleren. Dit impliceert dat het gebooste beslisboommodel altijd een veelbelovende aanpak is.  

Het gebruik van deze vier categorieën creëert de illusie dat een eenvoudige *deterministische* benadering, gebaseerd op indexen gevormd op redelijke factoren per categorie, voldoende moet zijn om klanten te identificeren die risico lopen op churn. Helaas, hoewel dit begrip lijkt plausibel, het is een vals begrip. De reden is dat churn is een tijdelijk effect en de factoren die bijdragen aan churn zijn meestal in voorbijgaande toestanden. Wat leidt een klant te overwegen verlaten vandaag misschien anders morgen, en het zal zeker anders zes maanden vanaf nu. Daarom is een *probabilistisch* model een noodzaak.  

Deze belangrijke observatie wordt vaak over het hoofd gezien in het bedrijfsleven, die over het algemeen de voorkeur geeft aan een business intelligence-georiënteerde benadering van analytics, vooral omdat het een gemakkelijker te verkopen en geeft eenvoudige automatisering.  

Echter, de belofte van self-service analytics met behulp van Machine Learning Studio (klassiek) is dat de vier categorieën van informatie, beoordeeld per divisie of afdeling, uitgegroeid tot een waardevolle bron voor machine learning over churn.  

Een andere spannende mogelijkheid die in Azure Machine Learning Studio (klassiek) komt, is de mogelijkheid om een aangepaste module toe te voegen aan de opslagplaats van vooraf gedefinieerde modules die al beschikbaar zijn. Deze mogelijkheid biedt in wezen de mogelijkheid om bibliotheken te selecteren en sjablonen voor verticale markten te maken. Het is een belangrijke differentiator van Azure Machine Learning Studio (klassiek) in de markt.  

We hopen dit onderwerp in de toekomst voort te zetten, vooral met betrekking tot big data analytics.
  

## <a name="conclusion"></a>Conclusie
Dit document beschrijft een verstandige aanpak van het gemeenschappelijke probleem van klantverloop met behulp van een generiek kader. We hebben een prototype voor het scoren van modellen overwogen en geïmplementeerd met Azure Machine Learning Studio (klassiek). Tot slot hebben we de nauwkeurigheid en prestaties van de prototypeoplossing beoordeeld met betrekking tot vergelijkbare algoritmen in SAS.  

 

## <a name="references"></a>Verwijzingen
[1] Predictive Analytics: Beyond the Predictions, W. McKnight, Information Management, July/August 2011, p.18-20.  

[2] Wikipedia artikel: [Nauwkeurigheid en precisie](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Stapsgewijze datamininggids](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Big Data Marketing: Betrek uw klanten effectiever en stimuleer waarde](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Modelsjabloon voor telcochurn](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) in [Azure AI-galerie](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Bijlage
![Momentopname van een presentatie over churn prototype](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Figuur 12: Momentopname van een presentatie over churn prototype*
