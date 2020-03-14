---
title: Klant verloop analyseren
titleSuffix: ML Studio (classic) - Azure
description: Casestudy voor het ontwikkelen van een geïntegreerd model voor het analyseren en evalueren van klant verloop met behulp van Azure Machine Learning Studio (klassiek).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 4cf918abae51ca330054ef86e57095d29a21a37a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204525"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Klant verloop analyseren met behulp van Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

## <a name="overview"></a>Overzicht
In dit artikel vindt u een referentie-implementatie van een project met klant verloop analyse dat is gebouwd met behulp van Azure Machine Learning Studio (klassiek). In dit artikel wordt besproken hoe gekoppelde algemene modellen voor het oplossen van het probleem van het verloop van industriële klanten zuinigste. We ook meet de nauwkeurigheid van modellen die zijn gebouwd met behulp van Machine Learning en richtlijnen voor de verdere ontwikkeling beoordelen.  

### <a name="acknowledgements"></a>Dankbetuigingen
Dit experiment werd ontwikkeld en getest door Serge Berger, Principal data wetenschapper bij micro soft en Roger Barga, voorheen product manager voor Microsoft Azure Machine Learning Studio (klassiek). De Azure-documentatieteam dank erkent hun ervaring en ze Bedankt voor het delen van deze whitepaper.

> [!NOTE]
> De gegevens die worden gebruikt voor dit experiment is niet openbaar beschikbaar. Voor een voor beeld van het bouwen van een machine learning model voor de verloop analyse raadpleegt u: [sjabloon detail verloop model](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) in [Azure AI Gallery](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>Het probleem van klantverloop
Bedrijven in de markt consumenten en in alle enterprise-sectoren hebben om op te lossen verloop. Soms verloop uitzonderlijk veel en is van invloed op beleidsbeslissingen. De traditionele oplossing is te hoog-neiging zullen zijn churners voorspellen en hun behoeften via een service concierge marketingcampagnes, of door het toepassen van speciale dispensaties. Deze methoden kunnen variëren branche branche. Ze kunnen zelfs binnen één bedrijfstak (bijvoorbeeld telecommunicatie) uit een cluster van bepaalde consumenten verschillen.

De algemene factor is dat ondernemingen nodig om deze bewaarduur van een speciale klant. Zo zou een natuurlijke methodologie score van elke klant met de kans van verloop en de bovenste N die verhelpen. De belangrijkste klanten mogelijk het meest winstgevend zijn. Bijvoorbeeld, meer geavanceerde scenario's een functie winst in dienst is bij de selectie van kandidaten voor speciale dispensatie. Deze overwegingen zijn echter slechts een deel van de complete strategie voor het omgaan met verloop. Bedrijven hebben ook rekening account risico's (en bijbehorende risicotolerantie), wordt het niveau en de kosten van de tussenkomst van de en aannemelijke klantsegmentatie.  

## <a name="industry-outlook-and-approaches"></a>De bedrijfstak van outlook en benaderingen
Geavanceerde verwerking van het verloop is een teken van een goed ontwikkelde bedrijfstak. Het klassieke voorbeeld is de branche telecommunicatie waar abonnees bekend is dat ze vaak overschakelen van één provider naar de andere. Dit verloop niet verplicht is een primair probleem. Daarnaast hebben providers belang rijke kennis verzameld over *verloop Stuur Programma's*. Dit zijn de factoren die klanten stimuleren om over te scha kelen.

Telefoon of het apparaat keuze is bijvoorbeeld een bekende stuurprogramma van het verloop in het bedrijf mobiele telefoon. Als gevolg hiervan is een populaire beleid voor de prijs van een toestel subsidize voor nieuwe abonnees en een volledige prijs voor bestaande klanten voor een upgrade. In het verleden heeft dit beleid geleid tot klanten hopping van één provider naar een andere nieuwe korting te krijgen. Deze heeft providers om hun strategieën te verfijnen op zijn beurt gevraagd.

Hoge volatiliteit in aanbiedingen die telefoon is een factor die snel worden ongeldig modellen van het verloop die zijn gebaseerd op de huidige telefoon modellen. Bovendien mobiele telefoons zijn niet alleen apparaten die telecommunicatie, ze zijn ook mode-instructies (Houd rekening met de iPhone). Deze sociale voorspellingsfactoren vallen buiten het bereik van reguliere telecommunicatie-gegevenssets.

Het resultaat voor het maken van modellering is dat u een geluid beleid kan niet devise gewoon door het elimineren van bekende redenen voor het verloop. Een continue model strategie, met inbegrip van klassieke modellen waarmee Categorische variabelen (zoals beslissings structuren) worden gekwantificeerd, is in feite **verplicht**.

Met behulp van big data-sets op hun klanten, uitvoert organisaties big data-analyses (met name, verloop detectie op basis van big data) als een effectieve benadering voor het probleem. U vindt meer informatie over de big data-benadering tot het probleem van het verloop in de aanbevelingen van ETL-sectie.  

## <a name="methodology-to-model-customer-churn"></a>Methodologie aan klantverloop model
Een algemene proces oplossen van problemen op te lossen klantverloop wordt weergegeven in afbeelding 1-3:  

1. Een model risico's kunt u rekening houden met de invloed van acties op waarschijnlijkheid en risico's.
2. Een model tussenkomst van de kunt u overwegen hoe het niveau van de tussenkomst van invloed kan zijn op de kans van verloop en het bedrag van de klant levensduurwaarde (CLV).
3. Deze analyse gepaard met een kwalitatieve analyse die een proactieve marketingcampagne die gericht is op klantsegmenten voor het leveren van de optimale aanbieding is geëscaleerd.  

![Diagram waarin wordt getoond hoe een risico tolerantie en besluit modellen resulteren in inzichten die actie kan ondernemen](./media/azure-ml-customer-churn-scenario/churn-1.png)

Deze vooruit uitziende aanpak is de beste manier om het verloop behandelen, maar wordt geleverd met complexiteit: we hebben een multi-modeldatabase archetype en tracering afhankelijkheden tussen de modellen te ontwikkelen. De interactie tussen modellen kan worden ingekapseld, zoals wordt weergegeven in het volgende diagram:  

![Interactie diagram van het verloop model](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Afbeelding 4: Unified multi-model archetype*  

Interactie tussen de modellen is essentieel als we een holistische benadering om aan te leveren klantretentie. Elk model hoeft niet meer in de loop van de tijd te worden gedegradeerd. Daarom is de architectuur een impliciete lus (vergelijkbaar met de archetype die is ingesteld door de scherp-DM-gegevens analyse standaard, [***3***]).  

De algehele cyclus van risico-besluit-marketing segmentering/ontleding is nog steeds een gegeneraliseerde structuur, van toepassing op tal van zakelijke problemen is. Verloop analyse is gewoon een sterke vertegenwoordiger van deze groep van problemen, omdat deze de kenmerken van een complexe zakelijke probleem dat is niet toegestaan een vereenvoudigd voorspellende oplossing vertoont. De sociale aspecten van de moderne aanpak van verloop zijn met name niet gemarkeerd in de benadering, maar de sociale aspecten worden ingekapseld in de archetype modelleren als ze op een model zou zijn.  

Hier een interessante toevoeging is analyse van big data. Vandaag telecommunicatie en detailhandelaren uitgebreide gegevens verzamelen over hun klanten en we kunnen eenvoudig voorzien de noodzaak voor meerdere modellen connectiviteit wordt een algemene trend, bepaalde opkomende trends, zoals het Internet of Things en alomtegenwoordige apparaten, waardoor business-to-intelligente oplossingen op meerdere lagen gebruiken.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>De Modeling archetype implementeren in Machine Learning Studio (klassiek)
Wat is het probleem dat wordt beschreven: wat is de beste manier om een geïntegreerde model-en Score benadering te implementeren? In deze sectie wordt gedemonstreerd hoe dit wordt gedaan met behulp van Azure Machine Learning Studio (klassiek).  

De multi-modeldatabase aanpak is een moet bij het ontwerpen van een globale archetype voor verloop. Ook de scoring (voorspellende) deel van de aanpak moet meerdere modellen.  

In het volgende diagram ziet u het prototype dat is gemaakt. Dit maakt gebruik van vier Score algoritmen in Machine Learning Studio (klassiek) om verloop te voors pellen. De reden voor het gebruik van een multi-modeldatabase benadering is niet alleen voor het maken van een classificatie ensembles voor betere nauwkeurigheid, maar ook om te beveiligen tegen sprake van redundante aanpassing van labels en prescriptieve Functieselectie verbeteren.  

![Scherm afbeelding waarin een complexe studio-werk ruimte (klassieke) wordt weer gegeven met een groot aantal onderling verbonden modules](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Afbeelding 5: prototype van een verloop model benadering*  

In de volgende secties vindt u meer informatie over het prototype-beoordelings model dat we hebben geïmplementeerd met behulp van Machine Learning Studio (klassiek).  

### <a name="data-selection-and-preparation"></a>Gegevens selecteren en voorbereiden
De gegevens die worden gebruikt om de modellen te bouwen en klanten van de score is verkregen van een verticale CRM-oplossing met de gegevens die zijn verborgen voor het beveiligen van de privacy van klanten. De gegevens bevat informatie over de 8000-abonnementen in de Verenigde Staten en drie bronnen worden gecombineerd: inrichten van gegevens (metagegevens van het abonnement), gegevens over gebruikersactiviteiten (gebruik van het systeem) en gegevens van de klant ondersteuning. De gegevens bevatten geen bedrijfsgerelateerde informatie over de klanten; het bevat bijvoorbeeld geen loyale meta gegevens of credit scores.  

Voor het gemak bent ETL en processen voor opschonen van gegevens buiten het bereik omdat we ervan uitgaan dat het voorbereiden van gegevens is al is klaar ergens anders.

Functies selecteren voor het maken van modellering is gebaseerd op het voorlopige significante scoren van de set voorspellingsfactoren, opgenomen in het proces dat gebruikmaakt van de module willekeurige forest. Voor de implementatie in Machine Learning Studio (klassiek) hebben we het gemiddelde, de mediaan en de bereiken voor representatieve functies berekend. We hebben toegevoegd bijvoorbeeld statistische functies voor de kwalitatieve gegevens, zoals de minimale en maximale waarden voor gebruikersactiviteit.

We hebben ook vastgelegd tijdelijke gegevens voor de meest recente zes maanden. Gegevens geanalyseerd voor één jaar en wij tot stand gebracht, zelfs als er statistisch significant trends waren, wordt de gevolgen zijn voor verloop aanzienlijk verminderd na zes maanden.  

Het belangrijkste punt is dat het hele proces, inclusief ETL, het selecteren van functies en het model leren, is geïmplementeerd in Machine Learning Studio (klassiek) met behulp van gegevens bronnen in Microsoft Azure.   

De volgende diagrammen ziet u de gegevens die is gebruikt.  

![Scherm afbeelding met een voor beeld van de gegevens die worden gebruikt met ruwe waarden](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Afbeelding 6: fragment van de gegevens bron (verborgen)*  

![Scherm afbeelding met statistische functies die zijn geëxtraheerd uit de gegevens bron](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Afbeelding 7: functies die zijn geëxtraheerd uit de gegevens bron*
 

> Houd er rekening mee dat deze gegevens privé is; daarom het model en de gegevens kunnen niet worden gedeeld.
> Voor een vergelijkbaar model met openbaar beschik bare gegevens raadpleegt u dit voorbeeld experiment in het [Azure AI Gallery](https://gallery.azure.ai/): [telecommunicatie klant verloop](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Voor meer informatie over hoe u een verloop analyse model kunt implementeren met behulp van Cortana Intelligence Suite, wordt [deze video](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) ook aanbevolen door Senior Program Manager wee Hyong Tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Die worden gebruikt in het model
We de volgende vier machine learning-algoritmen gebruikt voor het bouwen van het model (geen aanpassing):  

1. Logistieke regressie (LR)
2. Boosted-beslisboom (BT)
3. Gemiddelde perceptron (AP)
4. Voor ondersteuningsvectormachines (SVM)  

Het volgende diagram illustreert een deel van het ontwerpvenster experiment, waarmee wordt aangegeven van de volgorde waarin de modellen zijn gemaakt:  

![Scherm afbeelding van een kleine sectie van het canvas van Studio experimenten](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Afbeelding 8: modellen maken in Machine Learning Studio (klassiek)*  

### <a name="scoring-methods"></a>Scoring-methoden
We vier modellen hebt beoordeeld met behulp van een gegevensset met gelabelde training.  

We ook verzonden de scoring gegevensset met een vergelijkbare model gebouwd met behulp van de desktop-editie van SAS Enterprise hierop 12. We hebben de nauw keurigheid van het SAS-model en alle vier Machine Learning Studio (klassieke) modellen gemeten.  

## <a name="results"></a>Resultaten
In deze sectie geven we onze bevindingen over de nauwkeurigheid van de modellen, op basis van de scoring-gegevensset.  

### <a name="accuracy-and-precision-of-scoring"></a>Nauwkeurigheid en precisie van score
Over het algemeen ligt de implementatie in Azure Machine Learning Studio (klassiek) achter SAS nauw keurig met ongeveer 10-15% (gebied onder curve of AUC).  

De belangrijkste metrische gegevens in het verloop zijn echter het onjuist classificatie tempo: dat wil zeggen, van de bovenste N-bussen als voor speld door de classificatie, waarvan het eigenlijk **geen** verloop heeft ondervonden, en die nog een speciale behandeling hebben ontvangen? Het volgende diagram worden deze snelheid misclassification voor alle modellen vergeleken:  

![Gebied onder het curve diagram waarmee de prestaties van 4 algoritmen worden vergeleken](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Afbeelding 9: Passau-prototype gebied onder bocht*

### <a name="using-auc-to-compare-results"></a>Met behulp van AUC om resultaten te vergelijken
Opper vlak onder curve (AUC) is een metriek die een globale *separability* vertegenwoordigt tussen de distributies van scores voor positieve en negatieve populaties. Het is vergelijkbaar met de traditionele ontvanger Operator kenmerk (ROC)-grafiek, maar één belangrijk verschil is dat de metriek AUC vereist niet dat u een waarde voor drempel kiezen. In plaats daarvan wordt een overzicht gegeven van de resultaten voor **alle** mogelijke keuzes. Daarentegen de traditionele ROC-grafiek toont de-positief-ratio op de verticale as en de fout-positief-ratio op de horizontale as en is afhankelijk van de drempelwaarde voor classificatie.   

AUC wordt gebruikt als een meet waarde voor verschillende algoritmen (of verschillende systemen), omdat het mogelijk is dat modellen worden vergeleken met hun AUC-waarden. Dit is een populaire benadering in branches zoals meteorologie en biosciences. Dus vertegenwoordigt AUC een populair hulpprogramma voor het beoordelen van de prestaties van de classificatie.  

### <a name="comparing-misclassification-rates"></a>Vergelijking van de tarieven voor misclassification
We vergeleken de misclassification tarieven op de gegevensset in kwestie met behulp van de CRM-gegevens van ongeveer 8.000 abonnementen.  

* De snelheid van de misclassification SAS is 10-15%.
* De Machine Learning Studio (klassiek) heeft een niet-geclassificeerd classificatie percentage van 15-20% voor de top 200-300-bussen.  

In de branche telecommunicatie is het belangrijk om alleen die klanten met het hoogste risico voor het verloop door aan te bieden ze een concierge-service of andere speciale behandeling. In dit opzicht behaalt de Machine Learning Studio (klassieke) implementatie resultaten op pari met het SAS-model.  

Evenzo, is nauwkeurigheid belangrijker dan de precisie omdat we voornamelijk geïnteresseerd bent in goed mogelijke churners classificeren.  

Het volgende diagram van Wikipedia ziet u de relatie in een afbeelding levendige, eenvoudig te begrijpen:  

![Twee doelen. Eén doel toont de zoek markeringen die los van elkaar zijn gegroepeerd, maar in de buurt van de stieren-ogen als "lage nauw keurigheid: goede betrouw baarheid, slechte precisie. Een ander doel is nauw keurig gegroepeerd, maar ver van de stieren-ogen gemarkeerd met lage nauw keurigheid, goede nauw keurigheid.](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Afbeelding 10: verhouding tussen nauw keurigheid en precisie*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Resultaten nauwkeurigheid en precisie voor boosted decision tree model
Het volgende diagram wordt de onbewerkte resultaten van het scoring-met behulp van de Machine Learning-model voor de boosted decision tree-model, wat gebeurt er met de meest nauwkeurige tussen de vier modellen worden weergegeven:  

![Tabel fragment met nauw keurigheid, precisie, intrekken, F-Score, AUC, gemiddeld logboek verlies en verlies van het trainings logboek voor vier algoritmen](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Afbeelding 11: kenmerken van het versterkte model van de beslissings structuur*

## <a name="performance-comparison"></a>Vergelijking van de prestaties
We hebben de snelheid vergeleken waarmee gegevens zijn gescoord met behulp van de Machine Learning Studio klassieke modellen en een vergelijkbaar model dat is gemaakt met behulp van de Desktop Edition van SAS Enter prise Miner 12,1.  

De volgende tabel geeft een overzicht van de prestaties van de algoritmen:  

*Tabel 1. Algemene prestaties (nauw keurigheid) van de algoritmen*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Gemiddelde Model |Het beste Model |Attenderen |Gemiddelde Model |

De modellen die worden gehost in Machine Learning Studio (klassiek) uitvoerde SAS met 15-25% voor de snelheid van de uitvoering, maar de nauw keurigheid is grotendeels op pari.  

## <a name="discussion-and-recommendations"></a>Bespreking en aanbevelingen
In de branche telecommunicatie verschillende procedures voor het analyseren van verloop, is ontstaan met inbegrip van:  

* Metrische gegevens voor vier fundamentele categorieën worden afgeleid:
  * **Entiteit (bijvoorbeeld een abonnement)** . Algemene informatie over het abonnement en/of de klant die het onderwerp van het verloop inrichten.
  * **Activiteit**. Alle informatie over het gebruik mogelijk die is gerelateerd aan de entiteit, bijvoorbeeld het aantal aanmeldingen verkrijgen.
  * **Klanten ondersteuning**. Verzamelt gegevens uit logboeken van de klant ondersteuning om aan te geven of het abonnement problemen of interactie met de klantondersteuning heeft.
  * **Concurrerende en zakelijke gegevens**. Verkrijgen van mogelijk informatie over de klant (bijvoorbeeld kunnen zijn niet beschikbaar of moeilijk zijn om bij te houden).
* Gebruik van belang voor het station functies selecteren. Dit betekent dat het boosted decision tree model altijd een veelbelovende benadering is.  

Het gebruik van deze vier categorieën maakt de illusie die een eenvoudige *deterministische* benadering heeft, op basis van de indices die op redelijke factoren per categorie zijn gevormd, moet voldoende zijn om klanten te identificeren die risico lopen op het verloop. Helaas, hoewel dit begrip plausibele lijkt, is een false begrip. De reden is dat verloop een tijdelijke effect is en de factoren die bijdragen aan het verloop meestal in tijdelijke Staten zijn. Wat kunt u vandaag nog klanten potentiële klanten kan afwijken morgen en het zeker worden verschillende zes maanden vanaf nu. Daarom is een *Probabilistic* -model nood zakelijk.  

Observatie van dit belangrijk is vaak over het hoofd gezien in het bedrijfsleven, die in het algemeen de voorkeur geeft aan een business intelligence-georiënteerde benadering met analytics, meestal omdat het een eenvoudiger verkopen en admits eenvoudig automatiseren.  

De belofte van self-service analyses met behulp van Machine Learning Studio (klassiek) is echter dat de vier categorieën informatie, gesorteerd op divisie of afdeling, een waardevolle bron worden voor machine learning over het verloop.  

Een andere fantastische mogelijkheid in Azure Machine Learning Studio (klassiek) is de mogelijkheid om een aangepaste module toe te voegen aan de opslag plaats van vooraf gedefinieerde modules die al beschikbaar zijn. Deze mogelijkheid maakt in feite een mogelijkheid voor het selecteren van bibliotheken en sjablonen maken voor verticale markten. Het is een belang rijke onderscheid van Azure Machine Learning Studio (klassiek) op de markt plaats.  

We hopen dat om door te gaan in dit onderwerp in de toekomst, met name met betrekking tot de analyse van big data.
  

## <a name="conclusion"></a>Conclusie
Dit document beschrijft een functionele aanpak voor het aanpakken van het algemene probleem van klantverloop met behulp van een algemene framework. We beschouwen als een prototype voor het scoren van modellen en zijn geïmplementeerd met behulp van Azure Machine Learning Studio (klassiek). Ten slotte wordt de nauwkeurigheid en de prestaties van de oplossing prototype met betrekking tot vergelijkbare algoritmen in SAS beoordeeld.  

 

## <a name="references"></a>Verwijzingen
[1] voorspellende analyses: buiten de voor spelling, W. McKnight, Information Management, juli/augustus 2011, p. 18-20.  

[2] Wikipedia-artikel: [nauw keurigheid en precisie](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [helder-DM 1,0: stapsgewijze hand leiding voor gegevens analyse](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Big data marketing: uw klanten effectiever benaderen en de waarde ervan verlagen](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [telecommunicatie verloop model sjabloon](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) in [Azure AI Gallery](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Bijlage
![Moment opname van een presentatie in het verloop prototype](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Afbeelding 12: moment opname van een presentatie in het verloop prototype*
