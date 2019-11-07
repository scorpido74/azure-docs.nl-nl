---
title: Klant verloop analyseren
titleSuffix: ML Studio (classic) Azure
description: Casestudy voor het ontwikkelen van een geïntegreerd model voor het analyseren en evalueren van klant verloop met behulp van Azure Machine Learning Studio (klassiek).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 063b745bbf1c5e8453e0f6abe3cefdc76a60b5f9
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73619740"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Klant verloop analyseren met behulp van Azure Machine Learning Studio (klassiek)
## <a name="overview"></a>Overzicht
In dit artikel vindt u een referentie-implementatie van een project met klant verloop analyse dat is gebouwd met behulp van Azure Machine Learning Studio (klassiek). In dit artikel bespreken we gekoppelde algemene modellen voor het op holistische wijze oplossen van het probleem van het klanten verloop van de industrie. We meten ook de nauw keurigheid van modellen die zijn gebouwd met behulp van Machine Learning en beoordeelt de instructies voor verdere ontwikkeling.  

### <a name="acknowledgements"></a>Bevestigingen
Dit experiment werd ontwikkeld en getest door Serge Berger, Principal data wetenschapper bij micro soft en Roger Barga, voorheen product manager voor Microsoft Azure Machine Learning Studio (klassiek). De gratefully van het Azure-documentatie team erkent hun expertise en bedankt voor het delen van dit technisch document.

> [!NOTE]
> De gegevens die worden gebruikt voor dit experiment zijn niet openbaar beschikbaar. Voor een voor beeld van het bouwen van een machine learning model voor de verloop analyse raadpleegt u: [sjabloon detail verloop model](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) in [Azure AI Gallery](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>Het probleem van klant verloop
Bedrijven op de consumenten markt en in alle bedrijfs sectoren moeten het verloop kunnen behandelen. Soms is het verloop buitensporig en kunnen de beleids beslissingen worden beïnvloed. De traditionele oplossing is het voors pellen van neiging-bussen en hun behoeften te beantwoorden aan de hand van een concierge service, marketing campagnes of door speciale uitmaten toe te passen. Deze benaderingen kunnen variëren van branche tot branche. Ze kunnen zelfs variëren van een bepaald consumenten cluster in een branche (bijvoorbeeld telecom municatie).

De gemeen schappelijke factor is dat bedrijven deze speciale klant retentie taken moeten minimaliseren. Een natuurlijke methodologie zou dus elke klant kunnen beoordelen met de waarschijnlijkheid van het verloop en het adres van de bovenste N. De belangrijkste klanten zijn mogelijk de meeste winstgevende. In meer geavanceerde scenario's wordt bijvoorbeeld een winst functie gehanteerd tijdens de selectie van kandidaten voor speciale afzieningen. Deze overwegingen vormen echter slechts een deel van de volledige strategie voor het verwerken van het verloop. Bedrijven moeten ook rekening houden met het risico (en de bijbehorende risico tolerantie), het niveau en de kosten van de interventie en plausible-klant segmentatie.  

## <a name="industry-outlook-and-approaches"></a>Outlook en benaderingen van de branche
Geavanceerde verwerking van verloop is een aantekening van een volwassene industrie. Het klassieke voor beeld is de telecommunicatie sector waar abonnees vaak van de ene provider naar de andere bekend zijn. Dit vrijwillige verloop is een prime-bezorgdheid. Daarnaast hebben providers belang rijke kennis verzameld over *verloop Stuur Programma's*. Dit zijn de factoren die klanten stimuleren om over te scha kelen.

Bijvoorbeeld: hoorn of apparaat is een goed bekend stuur programma van verloop in de mobiele telefoon. Als gevolg hiervan is een populair beleid de prijs van een hoorn voor nieuwe abonnees te subsidiëren en een volledige prijs voor bestaande klanten in rekening te brengen voor een upgrade. In het verleden heeft dit beleid geleid tot klanten verspringen van de ene provider naar de andere om een nieuwe korting te krijgen. Op zijn beurt hebben wij desgevraagd hun strategieën verfijnd.

Hoge volatiliteit in Hoorn-aanbod is een factor waarmee snel modellen van verloop op basis van huidige hoorn modellen ongeldig worden gemaakt. Mobiele telefoons zijn bovendien niet alleen telecommunicatie apparaten, ze zijn ook een manier om de iPhone te bekijken. Deze sociale voor spellingen vallen buiten het bereik van normale telecommunicatie gegevens sets.

Het netto resultaat voor het maken van een model is dat u een geluids beleid niet eenvoudig kunt maken door bekende redenen voor verloop te elimineren. Een continue model strategie, met inbegrip van klassieke modellen waarmee Categorische variabelen (zoals beslissings structuren) worden gekwantificeerd, is in feite **verplicht**.

Organisaties die gebruikmaken van big data sets voor hun klanten, voeren big data analyses uit (met name de verloop detectie op basis van big data) als een doel matige benadering van het probleem. Meer informatie over de big data benadering van het probleem van verloop vindt u in de sectie aanbevelingen voor ETL.  

## <a name="methodology-to-model-customer-churn"></a>Methodologie voor het model leren van klant verloop
Een veelvoorkomend proces voor het oplossen van problemen met het oplossen van klant verloop wordt weer gegeven in figuren 1-3:  

1. Met een risico model kunt u overwegen hoe acties van invloed zijn op de kans en het risico.
2. Met een interventie model kunt u overwegen hoe het interventie niveau kan invloed hebben op de kans op verloop en de waarde voor de levens duur van de klant (CLV).
3. Deze analyse is van pas voor een kwalitatieve analyse die wordt geëscaleerd naar een proactieve marketing campagne die de klant segmenten bedoelt om de optimale aanbieding te leveren.  

![Diagram waarin wordt getoond hoe een risico tolerantie en besluit modellen resulteren in inzichten die actie kan ondernemen](./media/azure-ml-customer-churn-scenario/churn-1.png)

Deze voorwaartse benadering is de beste manier om het verloop te behandelen, maar het is wel het meest complex: we moeten een multi-model archetype en tracerings afhankelijkheden tussen de modellen ontwikkelen. De interactie tussen modellen kan worden ingekapseld, zoals wordt weer gegeven in het volgende diagram:  

![Interactie diagram van het verloop model](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Afbeelding 4: Unified multi-model archetype*  

De interactie tussen de modellen is essentieel als we een holistische benadering voor klant retentie moeten bieden. Elk model hoeft niet meer in de loop van de tijd te worden gedegradeerd. Daarom is de architectuur een impliciete lus (vergelijkbaar met de archetype die is ingesteld door de scherp-DM-gegevens analyse standaard, [***3***]).  

De totale cyclus van risico besluit-Marketing segmentatie/ontdubbeling is nog steeds een algemene structuur, die van toepassing is op veel zakelijke problemen. Verloop analyse is een sterke vertegenwoordiger van deze groep problemen omdat deze alle kenmerken van een complex zakelijk probleem vertoont die geen vereenvoudigde voorspellende oplossing toestaan. De sociaal-aspecten van de moderne benadering van het verloop zijn niet bijzonder gemarkeerd in de benadering, maar de sociale aspecten worden ingekapseld in het model archetype, aangezien ze in elk model zouden worden opgenomen.  

Hier volgt een interessante toevoeging big data Analytics. De huidige telecommunicatie-en retail bedrijven verzamelen uitgebreide gegevens over hun klanten, en we kunnen eenvoudig op de hoogte worden gesteld dat de nood zaak van connectiviteit met meerdere modellen een gemeen schappelijke trend is, gezien nieuwe trends zoals de Internet of Things en alomtegenwoordige apparaten, waarmee bedrijven slimme oplossingen op meerdere lagen kunnen gebruiken.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>De Modeling archetype implementeren in Machine Learning Studio (klassiek)
Wat is het probleem dat wordt beschreven: wat is de beste manier om een geïntegreerde model-en Score benadering te implementeren? In deze sectie wordt gedemonstreerd hoe we dit hebben gedaan met behulp van de klassieke versie van Azure Machine Learning Studio.  

De aanpak met meerdere modellen is een nodig bij het ontwerpen van een globale archetype voor verloop. Zelfs het Score (voorspellende) deel van de benadering moet meerdere modellen zijn.  

In het volgende diagram ziet u het prototype dat is gemaakt. Dit maakt gebruik van vier Score algoritmen in Machine Learning Studio (klassiek) om verloop te voors pellen. De reden voor het gebruik van een aanpak met meerdere modellen is niet alleen voor het maken van een ensemble-classificatie om de nauw keurigheid te verbeteren, maar ook om te beschermen tegen meerwaarde en voor het verbeteren van de functie selectie.  

![Scherm afbeelding waarin een complexe studio-werk ruimte (klassieke) wordt weer gegeven met een groot aantal onderling verbonden modules](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Afbeelding 5: prototype van een verloop model benadering*  

In de volgende secties vindt u meer informatie over het prototype-beoordelings model dat we hebben geïmplementeerd met behulp van Machine Learning Studio (klassiek).  

### <a name="data-selection-and-preparation"></a>Gegevens selectie en-voor bereiding
De gegevens die worden gebruikt voor het bouwen van de modellen en de Score klanten zijn verkregen van een verticale oplossing van een CRM-toepassing, met de gegevens die zijn afgeschermd voor het beschermen van de privacy van klanten. De gegevens bevatten informatie over 8.000-abonnementen in de Verenigde Staten en combi neren drie bronnen: inrichtings gegevens (meta gegevens van abonnement), activiteit gegevens (gebruik van het systeem) en klant ondersteunings gegevens. De gegevens bevatten geen bedrijfsgerelateerde informatie over de klanten; het bevat bijvoorbeeld geen loyale meta gegevens of credit scores.  

Voor het gemak vallen ETL en het opschonen van gegevens niet binnen het bereik omdat we ervan uitgaan dat de gegevens voorbereiding elders al is uitgevoerd.

Het selecteren van onderdelen voor model lering is gebaseerd op het voorafgaande belang rijk scoren van de set voor spellingen, opgenomen in het proces dat gebruikmaakt van de module wille keurige forests. Voor de implementatie in Machine Learning Studio (klassiek) hebben we het gemiddelde, de mediaan en de bereiken voor representatieve functies berekend. We hebben bijvoorbeeld aggregaties voor de kwalitatieve gegevens toegevoegd, zoals minimale en maximale waarden voor gebruikers activiteit.

We hebben ook tijdelijke informatie vastgelegd voor de meest recente zes maanden. We hebben gegevens gedurende één jaar geanalyseerd en we hebben vastgesteld dat zelfs als er statistisch significante trends waren, het effect op het verloop sterk afneemt na zes maanden.  

Het belangrijkste punt is dat het hele proces, inclusief ETL, het selecteren van functies en het model leren, is geïmplementeerd in Machine Learning Studio (klassiek) met behulp van gegevens bronnen in Microsoft Azure.   

In de volgende diagrammen ziet u de gegevens die zijn gebruikt.  

![Scherm afbeelding met een voor beeld van de gegevens die worden gebruikt met ruwe waarden](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Afbeelding 6: fragment van de gegevens bron (verborgen)*  

![Scherm afbeelding met statistische functies die zijn geëxtraheerd uit de gegevens bron](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Afbeelding 7: functies die zijn geëxtraheerd uit de gegevens bron*
 

> Houd er rekening mee dat deze gegevens privé zijn, waardoor het model en de gegevens niet kunnen worden gedeeld.
> Voor een vergelijkbaar model met openbaar beschik bare gegevens raadpleegt u dit voorbeeld experiment in het [Azure AI Gallery](https://gallery.azure.ai/): [telecommunicatie klant verloop](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Voor meer informatie over hoe u een verloop analyse model kunt implementeren met behulp van Cortana Intelligence Suite, wordt [deze video](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) ook aanbevolen door Senior Program Manager wee Hyong Tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmen die in het prototype worden gebruikt
We hebben de volgende vier machine learning algoritmen gebruikt om het prototype te bouwen (geen aanpassing):  

1. Logistiek regressie (LR)
2. Versterkte beslissings structuur (BT)
3. Gemiddeld Perceptron (AP)
4. Support Vector machine (SVM)  

Het volgende diagram illustreert een deel van het ontwerp oppervlak van het experiment, waarmee de volg orde wordt aangegeven waarin de modellen zijn gemaakt:  

![Scherm afbeelding van een kleine sectie van het canvas van Studio experimenten](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Afbeelding 8: modellen maken in Machine Learning Studio (klassiek)*  

### <a name="scoring-methods"></a>Score methoden
We hebben de vier modellen gescoord met behulp van een gelabelde trainings gegevensset.  

We hebben ook de Score gegevensset verzonden naar een vergelijkbaar model dat is gemaakt met behulp van de Desktop Edition van SAS Enter prise Miner 12. We hebben de nauw keurigheid van het SAS-model en alle vier Machine Learning Studio (klassieke) modellen gemeten.  

## <a name="results"></a>Resultaten
In deze sectie presen teren we onze bevindingen over de nauw keurigheid van de modellen, op basis van de Score gegevensset.  

### <a name="accuracy-and-precision-of-scoring"></a>Nauw keurigheid en nauw keurigheid van scores
Over het algemeen ligt de implementatie in de klassieke versie van Azure Machine Learning Studio achter SAS nauw keurig met ongeveer 10-15% (gebied onder curve of AUC).  

De belangrijkste metrische gegevens in het verloop zijn echter het onjuist classificatie tempo: dat wil zeggen, van de bovenste N-bussen als voor speld door de classificatie, waarvan het eigenlijk **geen** verloop heeft ondervonden, en die nog een speciale behandeling hebben ontvangen? In het volgende diagram wordt dit niet-geclassificeerde aantal modellen vergeleken:  

![Gebied onder het curve diagram waarmee de prestaties van 4 algoritmen worden vergeleken](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Afbeelding 9: Passau-prototype gebied onder bocht*

### <a name="using-auc-to-compare-results"></a>Resultaten vergelijken met AUC
Opper vlak onder curve (AUC) is een metriek die een globale *separability* vertegenwoordigt tussen de distributies van scores voor positieve en negatieve populaties. Het is vergelijkbaar met de grafiek van de traditionele ontvanger van ontvangers (ROC), maar een belang rijk verschil is dat u voor de AUC-metriek geen drempel waarde moet kiezen. In plaats daarvan wordt een overzicht gegeven van de resultaten voor **alle** mogelijke keuzes. Daarentegen toont de traditionele ROC grafiek het positieve getal op de verticale as en het onjuiste positieve rendement op de horizontale as, en de classificatie drempel varieert.   

AUC wordt gebruikt als een meet waarde voor verschillende algoritmen (of verschillende systemen), omdat het mogelijk is dat modellen worden vergeleken met hun AUC-waarden. Dit is een populaire benadering in branches, zoals meteorologische en biowetenschappen. AUC vertegenwoordigt daarom een populair hulp programma voor het beoordelen van classificatie prestaties.  

### <a name="comparing-misclassification-rates"></a>Het vergelijken van de niet-geclassificeerde tarieven
We hebben de ongelijke classificatie tarieven van de betreffende gegevensset vergeleken met de CRM-gegevens van ongeveer 8.000 abonnementen.  

* Het geclassificeerde SAS-aantal is 10-15%.
* De Machine Learning Studio (klassiek) heeft een niet-geclassificeerd classificatie percentage van 15-20% voor de top 200-300-bussen.  

In de telecommunicatie sector is het belang rijk om alleen klanten te adresseren die het grootste risico op verloop hebben door hen een concierge service of een andere speciale behandeling te bieden. In dit opzicht behaalt de Machine Learning Studio (klassieke) implementatie resultaten op pari met het SAS-model.  

Met hetzelfde token is nauw keuriger dan de nauw keurigheid, omdat we vooral geïnteresseerd zijn in het goed classificeren van potentiële bussen.  

In het volgende diagram van Wikipedia ziet u de relatie in een levendige, eenvoudig te begrijpen afbeelding:  

![Twee doelen. Eén doel toont de zoek markeringen die los van elkaar zijn gegroepeerd, maar in de buurt van de stieren-ogen als "lage nauw keurigheid: goede betrouw baarheid, slechte precisie. Een ander doel is nauw keurig gegroepeerd, maar ver van de stieren-ogen gemarkeerd met lage nauw keurigheid, goede nauw keurigheid.](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Afbeelding 10: verhouding tussen nauw keurigheid en precisie*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Nauw keurigheid en precisie resultaten voor het versterkte model van de beslissings structuur
In het volgende diagram worden de onbewerkte resultaten van scores weer gegeven met behulp van het Machine Learning prototype voor het versterkte beslissings structuur model. Dit is de meest nauw keurige tussen de vier modellen:  

![Tabel fragment met nauw keurigheid, precisie, intrekken, F-Score, AUC, gemiddeld logboek verlies en verlies van het trainings logboek voor vier algoritmen](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Afbeelding 11: kenmerken van het versterkte model van de beslissings structuur*

## <a name="performance-comparison"></a>Vergelijking van prestaties
We hebben de snelheid vergeleken waarmee gegevens zijn gescoord met behulp van de Machine Learning Studio klassieke modellen en een vergelijkbaar model dat is gemaakt met behulp van de Desktop Edition van SAS Enter prise Miner 12,1.  

De volgende tabel geeft een overzicht van de prestaties van de algoritmen:  

*Tabel 1. Algemene prestaties (nauw keurigheid) van de algoritmen*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Gemiddeld model |Het beste model |Wordt uitgevoerd |Gemiddeld model |

De modellen die worden gehost in Machine Learning Studio (klassiek) uitvoerde SAS met 15-25% voor de snelheid van de uitvoering, maar de nauw keurigheid is grotendeels op pari.  

## <a name="discussion-and-recommendations"></a>Discussies en aanbevelingen
In de telecommunicatie sector zijn er verschillende procedures opgegaan om het verloop te analyseren, waaronder:  

* Metrische gegevens afleiden voor vier fundamentele categorieën:
  * **Entiteit (bijvoorbeeld een abonnement)** . Basis informatie over het abonnement en/of de klant inrichten die het onderwerp van verloop vormt.
  * **Activiteit**. Verkrijg alle mogelijke gebruiks gegevens die betrekking hebben op de entiteit, bijvoorbeeld het aantal aanmeldingen.
  * **Klanten ondersteuning**. Informatie verzamelen van logboeken van klant ondersteuning om aan te geven of het abonnement problemen heeft of interacties met klant ondersteuning.
  * **Concurrerende en zakelijke gegevens**. Verkrijg eventuele informatie over de klant (bijvoorbeeld niet beschikbaar of moeilijk te traceren).
* Gebruik belang rijk om de functie te selecteren. Dit betekent dat het versterkte model van de beslissings structuur altijd een toezeggings benadering is.  

Het gebruik van deze vier categorieën maakt de illusie die een eenvoudige *deterministische* benadering heeft, op basis van de indices die op redelijke factoren per categorie zijn gevormd, moet voldoende zijn om klanten te identificeren die risico lopen op het verloop. Helaas, hoewel dit niet het geval is, is het een onplausiblee overeenkomst. De reden hiervoor is dat het verloop een tijdelijk effect is en dat de factoren die bijdragen aan het verloop doorgaans in de tijdelijke status zijn. Wat een klant leidt om te overwegen dat deze vandaag moet worden uitgeschakeld, kan worden gefactureerd, en het is zeker dat deze zes maanden vanaf nu afwijkt. Daarom is een *Probabilistic* -model nood zakelijk.  

Deze belang rijke waarneming is vaak te zien in bedrijven, wat een business intelligence georiënteerde aanpak voor analyse in het algemeen is, meestal omdat het een eenvoudigere verkoop is en een eenvoudige automatisering toestuurt.  

De belofte van self-service analyses met behulp van Machine Learning Studio (klassiek) is echter dat de vier categorieën informatie, gesorteerd op divisie of afdeling, een waardevolle bron worden voor machine learning over het verloop.  

Een andere fantastische mogelijkheid in de klassieke versie van Azure Machine Learning Studio is de mogelijkheid om een aangepaste module toe te voegen aan de opslag plaats van vooraf gedefinieerde modules die al beschikbaar zijn. Met deze mogelijkheid wordt in wezen een kans gemaakt om bibliotheken te selecteren en sjablonen voor verticale markten te maken. Het is een belang rijke onderscheid van de klassieke versie van Azure Machine Learning Studio op de markt plaats.  

We hopen dat u in de toekomst kunt door gaan met dit onderwerp, met name in verband met big data Analytics.
  

## <a name="conclusion"></a>Conclusie
In dit artikel wordt een praktische benadering beschreven voor het aanpakken van het veelvoorkomende probleem van klant verloop door gebruik te maken van een algemeen framework. We beschouwen als een prototype voor het scoren van modellen en zijn geïmplementeerd met behulp van de klassieke versie van Azure Machine Learning Studio. Ten slotte hebben we de nauw keurigheid en prestaties beoordeeld van de prototype oplossing met betrekking tot vergelijk bare algoritmen in SAS.  

 

## <a name="references"></a>Verwijzingen
[1] voorspellende analyses: buiten de voor spelling, W. McKnight, Information Management, juli/augustus 2011, p. 18-20.  

[2] Wikipedia-artikel: [nauw keurigheid en precisie](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [helder-DM 1,0: stapsgewijze hand leiding voor gegevens analyse](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Big data marketing: uw klanten effectiever benaderen en de waarde ervan verlagen](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [telecommunicatie verloop model sjabloon](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) in [Azure AI Gallery](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Bijlage
![Moment opname van een presentatie in het verloop prototype](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Afbeelding 12: moment opname van een presentatie in het verloop prototype*
