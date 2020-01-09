---
title: Algoritmes kiezen
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klassieke) algoritmen kiezen voor onder Super visie en onbewaakte lessen in Clustering, classificatie of regressie experimenten.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 03/04/2019
ms.openlocfilehash: 2073123a61e919c10caaaea141f776e842f4d717
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427740"
---
# <a name="how-to-choose-algorithms-for-azure-machine-learning-studio-classic"></a>Algoritmen kiezen voor Azure Machine Learning Studio (klassiek)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Het antwoord op de vraag ' wat machine learning-algoritme moet ik gebruiken? ' is altijd afhankelijk van. Het hangt af van de grootte, de kwaliteit en de aard van de gegevens. Dit is afhankelijk van wat u met het antwoord wilt doen. Het hangt af van de manier waarop de wiskunde van het algoritme is vertaald in instructies voor de computer die u gebruikt. En dit is afhankelijk van de hoeveelheid tijd die u hebt. Zelfs de meest ervaren gegevens wetenschappers kunnen niet bepalen welk algoritme het beste presteert voordat ze worden uitgevoerd.

Machine Learning Studio (klassiek) biedt geavanceerde algoritmen, zoals schaal bare, gestimuleerde beslissings structuren, Bayesiaanse Aanbevelings systemen, diep gaande Neural netwerken en beslissings jungle die zijn ontwikkeld bij micro soft Research. Ook bevat het schaalbare open-source machine learning-pakketten, zoals Vowpal Wabbit. Machine Learning Studio (klassiek) ondersteunt machine learning-algoritmen voor multi klasse-en binaire classificatie, regressie en clustering. Bekijk de volledige lijst met [machine learning modules](/azure/machine-learning/studio-module-reference/index).
De documentatie bevat informatie over elk algoritme en het afstemmen van de para meters voor het optimaliseren van het algoritme voor uw gebruik.  


## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Het Cheat-werk blad met Machine Learning algoritme

Het **[Cheat-venster van Microsoft Azure machine learning-algoritme](../algorithm-cheat-sheet.md)** helpt u bij het kiezen van het juiste machine learning algoritme voor uw Predictive Analytics oplossingen van de Azure machine learning bibliotheek met algoritmen.
In dit artikel vindt u instructies voor het gebruik van dit Cheat-venster.

> [!NOTE]
> Als u het Cheat-blad wilt downloaden en dit artikel wilt volgen, gaat u naar het werk [blad machine learning Algorithm Cheat](../algorithm-cheat-sheet.md).
> 
> 

Deze aanbevelingen zijn gecompileerde feedback en tips van veel gegevens wetenschappers en machine learning experts. We hebben niet op alles instemmen, maar we hebben geprobeerd onze meningen in een ruwe consensus te harmoniseren. De meeste van de instructies voor het afstemmen beginnen met ' Dit is afhankelijk van... '


> [!TIP]
> Zie [basis beginselen van machine learning met algoritme voorbeelden](basics-infographic-with-algorithm-examples.md)voor een eenvoudig te begrijpen infographic-overzicht van machine learning-basis informatie over veelgebruikte algoritmen die worden gebruikt voor het beantwoorden van algemene machine learning vragen.

## <a name="flavors-of-machine-learning"></a>De smaak van machine learning

### <a name="supervised"></a>Onder supervisie

Bewaakte leer algoritmen maken voor spellingen op basis van een aantal voor beelden. Historische aandelen prijzen kunnen bijvoorbeeld worden gebruikt om schattingen te maken over toekomstige prijzen. Elk voor beeld dat wordt gebruikt voor trainingen, is voorzien van de waarde van interest, in dit geval de aandelen prijs. Een leer algoritme met Super visie zoekt naar patronen in deze waardelabels. Het kan alle informatie gebruiken die relevant kan zijn: de dag van de week, het seizoen, de financiële gegevens van het bedrijf, het type branche, de aanwezigheid van storende geopolitieke gebeurtenissen, en elk algoritme zoekt naar verschillende soorten patronen. Nadat het algoritme het beste patroon heeft gevonden dat kan worden gebruikt, wordt dit patroon in gebruik voor het maken van voor spellingen voor niet-gelabelde test gegevens, de prijzen van morgen.

Learning is een populair en nuttig type machine learning. Met één uitzonde ring worden alle modules in Azure Machine Learning Studio (klassiek) bewaakte leer algoritmen. Er zijn verschillende specifieke soorten gecontroleerde lessen die worden weer gegeven in Azure Machine Learning Studio (klassiek): classificatie, regressie en anomalie detectie.

* **Classificatie**. Wanneer de gegevens worden gebruikt om een categorie voor tes pellen, wordt gebruikgemaakt van toezicht, ook wel classificatie genoemd. Dit is het geval wanneer u een afbeelding als een afbeelding van een ' kat ' of ' hond ' toewijst. Als er slechts twee opties zijn, wordt deze **twee klassen** of **binomiale classificatie**genoemd. Wanneer er meer categorieën zijn, zoals bij het voors pellen van de winnaar van de NCAA maart Madness-toernooi, wordt dit probleem ook wel een **classificatie met meerdere klassen**genoemd.
* **Regressie**. Wanneer een waarde wordt voor speld, net als bij aandelen prijzen, heette learning ' regressie '.
* **Afwijkings detectie**. Soms is het doel om gegevens punten te identificeren die gewoon ongebruikelijk zijn. In het geval van fraude detectie zijn een zeer ongebruikelijke bestedings patroon van credit cards verdacht. De mogelijke variaties zijn zoveel en de voor beelden van de training zo weinig, dat het niet haalbaar is om te leren hoe frauduleuze activiteiten eruitzien. De aanpak die afwijkende detectie in beslag neemt, is om simpelweg te weten te komen welke normale activiteiten eruitzien (met behulp van een historisch overzicht van niet-frauduleuze trans acties) en om iets te identificeren dat aanzienlijk afwijkt.

### <a name="unsupervised"></a>Zonder super visie

Er zijn geen labels aan gegevens punten gekoppeld aan het leren zonder toezicht. In plaats daarvan kunt u de gegevens op een bepaalde manier organiseren of de structuur ervan beschrijven. Dit kan betekenen dat het wordt gegroepeerd in clusters of dat er verschillende manieren worden gevonden om complexe gegevens te bekijken, zodat deze eenvoudiger of meer georganiseerd zijn.

### <a name="reinforcement-learning"></a>Bekrachtigend leren

In het versterken van learning wordt de algoritme gekozen om een actie te kiezen als reactie op elk gegevens punt. Het leer algoritme heeft ook een korte tijd een belonings signaal ontvangen dat aangeeft hoe goed de beslissing was.
Op basis hiervan wordt de strategie van het algoritme aangepast om de hoogste beloning te kunnen halen. Er zijn momenteel geen ingrijpende leer algoritme modules in Azure Machine Learning Studio (klassiek). Educatief leren is gebruikelijk in Robotics, waarbij de set Lees bewerkingen op één punt in de tijd een gegevens punt is, en het algoritme de volgende actie van de robot moet kiezen. Het is ook natuurlijk geschikt voor Internet of Things toepassingen.

## <a name="considerations-when-choosing-an-algorithm"></a>Overwegingen bij het kiezen van een algoritme

### <a name="accuracy"></a>Nauw keurigheid

Het is niet altijd nodig om het meest nauw keurige antwoord optimaal te benutten.
Soms is een benadering voldoende, afhankelijk van wat u wilt gebruiken voor. Als dat het geval is, kunt u uw verwerkings tijd aanzienlijk verkorten door de methoden te benaderen. Een ander voor deel van een nader te benaderen methode is dat ze niet groter worden voor komen.

### <a name="training-time"></a>Trainingstijd

Het aantal minuten of uren dat nodig is voor het trainen van een model, is afhankelijk van de verschillende algoritmen. De trainings tijd is vaak nauw verbonden met nauw keurigheid, een voor de andere. Bovendien zijn sommige algoritmen gevoeliger voor het aantal gegevens punten dan andere.
Wanneer tijd is beperkt, kan de keuze van het algoritme worden opgegeven, met name wanneer de gegevensset groot is.

### <a name="linearity"></a>Lineariteit

Veel machine learning-algoritmen maken gebruik van lineariteit. Bij lineaire classificatie algoritmen wordt ervan uitgegaan dat klassen kunnen worden gescheiden door een rechte lijn (of het hogere analoge). Dit zijn onder andere logistiek-regressie en ondersteunde vector machines (zoals geïmplementeerd in Azure Machine Learning Studio (klassiek)).
Bij lineaire regressie algoritmen wordt ervan uitgegaan dat gegevens trends een rechte lijn volgen. Deze hypo Thesen zijn niet slecht voor bepaalde problemen, maar op andere plaatsen nauw keuriger.

![Grens van niet-lineaire klasse](./media/algorithm-choice/image1.png)

***Niet-lineaire boundary*** *-afhankelijkheid op een lineaire classificatie algoritme zou leiden tot een geringe nauw keurigheid*

![Gegevens met een niet-lineaire trend](./media/algorithm-choice/image2.png)

***Gegevens met een niet-lineaire trend*** *: door gebruik te maken van een lineaire regressie methode worden veel grotere fouten gegenereerd dan nodig* is

Ondanks hun gevaren zijn lineaire algoritmen zeer populair als een eerste aanvals regel. Ze zijn vaak algorithmically eenvoudig en snel te trainen.

### <a name="number-of-parameters"></a>Aantal para meters

Para meters zijn de knopen die een gegevens wetenschapper krijgt om in te scha kelen bij het instellen van een algoritme. Ze zijn getallen die van invloed zijn op het gedrag van de algoritme, zoals fout tolerantie of het aantal iteraties of opties tussen varianten van de werking van het algoritme. De trainings tijd en nauw keurigheid van het algoritme kunnen soms behoorlijk gevoelig zijn om alleen de juiste instellingen te verkrijgen. Normaal gesp roken vereisen algoritmen met een groot aantal para meters de meeste proef versie en fout om een goede combi natie te vinden.

Het is ook mogelijk dat er in Azure Machine Learning Studio (klassiek) een parameter blok voor het weglaten van [para meters](algorithm-parameters-optimize.md) wordt uitgevoerd die automatisch alle parameter combinaties van de gewenste granulariteit probeert. Dit is een goede manier om ervoor te zorgen dat u de parameter ruimte hebt gemaakt. de tijd die nodig is om een model te trainen, neemt exponentieel toe met het aantal para meters.

Aan de kant van een groot aantal para meters wordt doorgaans aangegeven dat een algoritme meer flexibiliteit heeft. Het kan vaak zeer goed nauw keurig zijn, op voor waarde dat u de juiste combi natie van parameter instellingen kunt vinden.

### <a name="number-of-features"></a>Aantal functies

Voor bepaalde typen gegevens kan het aantal functies erg groot zijn vergeleken met het aantal gegevens punten. Dit is vaak het geval met genetischer of tekstuele gegevens. Het grote aantal functies kan enkele leer algoritmen verwarrenden, waardoor de trainings tijd unfeasibly lang wordt. Support Vector machines zijn bijzonder goed geschikt voor dit geval (zie hieronder).

### <a name="special-cases"></a>Speciale gevallen

Sommige leer algoritmen maken specifieke veronderstellingen over de structuur van de gegevens of de gewenste resultaten. Als u een spel kunt vinden dat aan uw behoeften voldoet, kunt u meer nuttige resultaten, nauw keurigere voor spellingen of snellere opleidings tijden geven.

| **Algoritme** | **Nauw keurigheid** | **Trainings tijd** | **Lineariteit** | **Parameters** | **Opmerkingen** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Classificatie met twee klassen** | | | | | |
| [logistiek regressie](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) | |● |● |5 | |
| [beslissings forest](/azure/machine-learning/studio-module-reference/two-class-decision-forest) |● |○ | |6 | |
| [beslissing jungle](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) |● |○ | |6 |Weinig geheugen capaciteit |
| [versterkte beslissings structuur](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree) |● |○ | |6 |Grote geheugen capaciteit |
| [Neural-netwerk](/azure/machine-learning/studio-module-reference/two-class-neural-network) |● | | |9 |[Aanvullende aanpassing is mogelijk](azure-ml-netsharp-reference-guide.md) |
| [gemiddeld Perceptron](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) |○ |○ |● |4 | |
| [vector computer ondersteunen](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) | |○ |● |5 |Geschikt voor grote functie sets |
| [lokale diep gaande ondersteuning vector machine](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) |○ | | |8 |Geschikt voor grote functie sets |
| [Bayes ' punt machine](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine) | |○ |● |3 | |
| **Classificatie met meerdere klassen** | | | | | |
| [logistiek regressie](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) | |● |● |5 | |
| [beslissings forest](/azure/machine-learning/studio-module-reference/multiclass-decision-forest) |● |○ | |6 | |
| [beslissing jungle](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle) |● |○ | |6 |Weinig geheugen capaciteit |
| [Neural-netwerk](/azure/machine-learning/studio-module-reference/multiclass-neural-network) |● | | |9 |[Aanvullende aanpassing is mogelijk](azure-ml-netsharp-reference-guide.md) |
| [one-v-all](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass) |- |- |- |- |Zie Eigenschappen van de methode met twee klassen geselecteerd |
| **Regressie** | | | | | |
| [lineaire](/azure/machine-learning/studio-module-reference/linear-regression) | |● |● |4 | |
| [Bayesiaanse lineair](/azure/machine-learning/studio-module-reference/bayesian-linear-regression) | |○ |● |2 | |
| [beslissings forest](/azure/machine-learning/studio-module-reference/decision-forest-regression) |● |○ | |6 | |
| [versterkte beslissings structuur](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) |● |○ | |5 |Grote geheugen capaciteit |
| [snelle quantile voor forests](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) |● |○ | |9 |Distributies in plaats van punt voorspellingen |
| [Neural-netwerk](/azure/machine-learning/studio-module-reference/neural-network-regression) |● | | |9 |[Aanvullende aanpassing is mogelijk](azure-ml-netsharp-reference-guide.md) |
| [Poisson](/azure/machine-learning/studio-module-reference/poisson-regression) | | |● |5 |Technisch logboek-lineair. Voor Voorspellings tellingen |
| [rang telwoord](/azure/machine-learning/studio-module-reference/ordinal-regression) | | | |0 |Voor het voors pellen van rang orde |
| **Detectie van afwijkingen** | | | | | |
| [vector computer ondersteunen](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) |○ |○ | |2 |Vooral geschikt voor grote functie sets |
| [Anomalie detectie op basis van PCA](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) | |○ |● |3 | |
| [K-betekent](/azure/machine-learning/studio-module-reference/k-means-clustering) | |○ |● |4 |Een cluster algoritme |

**Algoritme-eigenschappen:**

**●** : toont uitstekende nauw keurigheid, snelle opleidings tijden en het gebruik van lineariteit

**○** : toont goede nauw keurigheid en gematige opleidings tijden

## <a name="algorithm-notes"></a>Algoritme notities

### <a name="linear-regression"></a>Lineaire regressie

Zoals eerder vermeld, past [lineaire regressie](/azure/machine-learning/studio-module-reference/linear-regression) een lijn (of vlak, of hyperplane) toe aan de gegevensset. Het is een paard, eenvoudig en snel, maar het is mogelijk te vereenvoudigde voor een aantal problemen.

![Gegevens met een lineaire trend](./media/algorithm-choice/image3.png)

***Gegevens met een lineaire trend***

### <a name="logistic-regression"></a>Logistieke regressie

Hoewel de naam ' regressie ' bevat, is een logistiek regressie een krachtig hulp middel voor de classificatie van [twee klassen](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) en meerdere [klassen](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) . Het is snel en eenvoudig. Het feit dat het gebruik maakt van een '-vormige curve in plaats van een rechte lijn, maakt het natuurlijk geschikt voor het delen van gegevens in groepen. Logistiek regressie biedt lineaire grenzen voor klassen, dus wanneer u deze gebruikt, moet u ervoor zorgen dat een lineaire benadering iets is wat u kunt gebruiken.

![Logistiek regressie naar gegevens met twee klassen met slechts één functie](./media/algorithm-choice/image4.png)

***Een logistiek-regressie naar gegevens met twee klassen met slechts één functie*** *: de grens van de klasse is het punt waarop de logistiek kromme net zo dicht bij beide klassen ligt*

### <a name="trees-forests-and-jungles"></a>Structuren, forests en jungle

Beslissings bossen ([regressie](/azure/machine-learning/studio-module-reference/decision-forest-regression), [twee klassen en meerdere](/azure/machine-learning/studio-module-reference/two-class-decision-forest) [klassen](/azure/machine-learning/studio-module-reference/multiclass-decision-forest)), beslissings Jungle ([twee klassen](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) en meerdere [klassen](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle)) en versterkte beslissings structuren ([regressie](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) en [twee klassen](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree)) zijn allemaal gebaseerd op beslissings structuren, een basis machine learning concept. Er zijn veel varianten van beslissings structuren, maar ze hebben allemaal hetzelfde effect. Verdeel de functie ruimte in regio's met voornamelijk hetzelfde label. Dit kunnen regio's zijn van een consistente categorie of van een constante waarde, afhankelijk van of u classificatie of regressie uitvoert.

![Beslissings structuur verdeelt een functie ruimte](./media/algorithm-choice/image5.png)

***Een beslissings structuur verdeelt een functie ruimte in regio's van ruwweg uniforme waarden***

Omdat een functie ruimte kan worden onderverdeeld in wille keurige kleine regio's, is het eenvoudig om te Voorst Ellen dat het precies genoeg is om één gegevens punt per regio te delen. Dit is een zeer voor beeld van overmontage. Om dit te voor komen, wordt een grote set bomen gebouwd met speciale wiskundige zorg om ervoor te zorgen dat de structuren niet worden gecorreleerd. Het gemiddelde van dit "besluitvormings Forest" is een structuur die de overmontage van het voor komt. Beslissings bossen kunnen veel geheugen gebruiken. Beslissings jungle zijn een variant die minder geheugen verbruikt bij de kosten van een iets langere trainings tijd.

Versterkte beslissings structuren zorgen ervoor dat de overneming wordt voor komen door te beperken hoe vaak ze kunnen onderverdelen en hoeveel gegevens punten zijn toegestaan in elke regio. Met het algoritme wordt een reeks structuren gemaakt, die elk van elkaar leren om de fout te compenseren die in de boom structuur voor komt. Het resultaat is een zeer nauw keurige kennis die een groot aantal geheugens gebruikt. Voor de volledige technische beschrijving raadpleegt u het [oorspronkelijke papier van Friedman](https://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Quantile regressie van Fast forest](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) is een variatie op de beslissings structuren voor het speciale geval waarbij u niet alleen de normale (mediaan) waarde van de gegevens binnen een regio wilt weten, maar ook de distributie in de vorm van quantiles.

### <a name="neural-networks-and-perceptrons"></a>Neural netwerken en Perceptrons

Neural-netwerken zijn toonaangevende leer algoritmen voor problemen met meerdere [klassen](/azure/machine-learning/studio-module-reference/multiclass-neural-network), [twee klassen](/azure/machine-learning/studio-module-reference/two-class-neural-network)en [regressies](/azure/machine-learning/studio-module-reference/neural-network-regression) . Ze bevinden zich in een oneindig aantal, maar de Neural netwerken in Azure Machine Learning Studio (klassiek) zijn de vorm van gerichte acyclische grafieken. Dit betekent dat de invoer functies door een reeks lagen worden door gegeven (nooit achterwaarts) voordat ze worden omgezet in uitvoer. In elke laag worden invoer gewogen in verschillende combi Naties, opgeteld en door gegeven aan de volgende laag. Deze combi natie van eenvoudige berekeningen resulteert in de mogelijkheid om geavanceerde klassen grenzen en gegevens trends te ontdekken. In veel gelaagde netwerken van deze sortering wordt gedieper gewaakt dat brand stoffen zoveel technische rapportage en scienceis kunnen uitvoeren.

Deze hoge prestaties zijn echter niet gratis. Neural-netwerken kunnen veel tijd in beslag nemen, met name voor grote gegevens sets met veel functies. Ze hebben ook meer para meters dan de meeste algoritmen, wat betekent dat het opruimen van de para meters de trainings tijd van een geweldige deal uitbreidt.
En voor gebruikers die [hun eigen netwerk structuur willen opgeven](azure-ml-netsharp-reference-guide.md), zijn de mogelijkheden van inexhaustible.

![Grenzen die zijn geleerd door Neural-netwerken](./media/algorithm-choice/image6.png)

***De grenzen die door Neural-netwerken zijn geleerd, kunnen ingewikkeld en onregelmatig zijn***

De [gemiddelde Perceptron van twee klassen](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) is Neural Networks antwoord op skyrocketing training-tijden. Er wordt gebruikgemaakt van een netwerk structuur die de grenzen van lineaire klassen geeft. Het is bijna primitieve door de standaarden van vandaag, maar het heeft een lange geschiedenis van het werken met een krachtige werk wijze en is klein genoeg om snel te leren.

### <a name="svms"></a>SVMs

Ondersteuning voor vector machines (SVMs) vindt u de grens waarmee klassen worden gescheiden door zo breed mogelijk een marge. Wanneer de twee klassen niet duidelijk kunnen worden gescheiden, vinden de algoritmen de beste grens die ze kunnen hebben. Zoals geschreven in Azure Machine Learning Studio (klassiek), voert de [SVM van twee klassen](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) dit alleen een rechte lijn (in SVM-speak wordt een lineaire kernel gebruikt).
Omdat deze lineaire benadering wordt gemaakt, kan deze redelijk snel worden uitgevoerd. Waar het inderdaad het geval is, is de functie-intense gegevens, zoals tekst-of genoom gegevens. In dergelijke gevallen kunnen SVMs sneller en met minder ruimte worden gescheiden dan de meeste andere algoritmen, naast het vereisen van slechts een kleine hoeveelheid geheugen.

![Grens van vector computer klasse ondersteunen](./media/algorithm-choice/image7.png)

***Een typische grens van de ondersteunings vector machine maximaliseert de marge die twee klassen scheidt***

Een ander product van micro soft Research, de [lokale diep gaande SVM van twee klassen](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) is een niet-lineaire variant van SVM die de meeste snelheid en geheugen efficiëntie van de lineaire versie behoudt. Het is ideaal voor gevallen waarin de lineaire benadering niet nauw keurig genoeg antwoorden geeft. De ontwikkel aars houden het snel in het onderverdelen van het probleem in een aantal kleine lineaire SVM-problemen. Lees de [volledige beschrijving](http://proceedings.mlr.press/v28/jose13.html) voor meer informatie over hoe ze deze slag hebben opgetrokken.

Met behulp van een slimme uitbrei ding van niet-lineaire SVMs tekent de [SVM van één klasse](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) een grens die de gehele gegevensset nauw keurig omlijnt. Het is nuttig voor anomalie detectie. Nieuwe gegevens punten die zich ver buiten die grens bevinden, zijn ongebruikelijk genoeg.

### <a name="bayesian-methods"></a>Bayesiaanse-methoden

Bayesiaanse-methoden hebben een zeer goede kwaliteit: ze vermijden het niet meer. Dit doet u door een aantal veronderstellingen vooraf te nemen over de waarschijnlijke verdeling van het antwoord. Een andere byproduct van deze benadering is dat ze zeer weinig para meters hebben. Azure Machine Learning Studio (klassiek) heeft Bayesiaanse-algoritmen voor zowel classificatie ([tweeklasselijke Bayes ' punt machine](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine)) als regressie ([Bayesiaanse lineaire regressie](/azure/machine-learning/studio-module-reference/bayesian-linear-regression)).
Houd er rekening mee dat deze gegevens kunnen worden gesplitst of aangepast met een rechte lijn.

Op een historisch notitie zijn Bayes-punt machines ontwikkeld bij micro soft Research. Ze hebben een uitzonderlijk mooi theoretisch werk. De belanghebbende student wordt omgeleid naar het [oorspronkelijke artikel in JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) en een ongevraagde [blog door Chris loper](https://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Gespecialiseerde algoritmen
Als u een zeer specifiek doel hebt, bent u mogelijk in de hand. Binnen de verzameling Azure Machine Learning Studio (klassiek) zijn er algoritmen die zijn gespecialiseerd in:

- positie voor spelling ([Ordinale regressie](/azure/machine-learning/studio-module-reference/ordinal-regression)),
- voor spelling tellen ([Poisson-regressie](/azure/machine-learning/studio-module-reference/poisson-regression)),
- afwijkings detectie (één gebaseerd op [analyse van belangrijkste onderdelen](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) en één op basis van de [ondersteunde vector machines](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine))
- Clustering ([K-gemiddelde](/azure/machine-learning/studio-module-reference/k-means-clustering))

![Anomalie detectie op basis van PCA](./media/algorithm-choice/image8.png)

***Anomalie detectie op basis van PCA*** *: de grote meerderheid van de gegevens valt onder een stereo type distributie; punten die aanzienlijk afwijken van die distributie, zijn verdacht*

![Gegevensset gegroepeerd met behulp van K-gemiddelden](./media/algorithm-choice/image9.png)

***Een gegevensset is gegroepeerd in vijf clusters met behulp van K-gemiddelden***

Er is ook een ensemble met [één-v-alle multi klasse-classificatie](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass), waarmee het n-class-classificatie probleem wordt verbroken in n-1 2-klasse-classificatie problemen. De eigenschappen voor nauw keurigheid, opleidings tijd en lineariteit worden bepaald door de classificaties van twee klassen die worden gebruikt.

![Classificaties met twee klassen gecombineerd om een classificatie met drie klassen te vormen](./media/algorithm-choice/image10.png)

***Een paar classificaties van twee klassen combi neren om een classificatie met drie klassen te vormen***

Azure Machine Learning Studio (klassiek) biedt ook toegang tot een krachtig machine learning Framework onder de titel van [Vowpal Wabbit](/azure/machine-learning/studio-module-reference/train-vowpal-wabbit-version-7-4-model).
VW is hier een categorisatie, omdat deze zowel classificatie-als regressie problemen kan ontdekken en zelfs van gedeeltelijk niet-gelabelde gegevens kan leren. U kunt deze configureren voor het gebruik van een van de verschillende leer algoritmen, verlies functies en optimalisatie algoritmen. Het is aan het begin ontworpen om efficiënt, parallel en zeer snel te zijn. Het verwerkt heel grote functie sets met weinig duidelijke inspanningen.
VW is gestart en geleid door de eigen John Langford van micro soft Research, is een formule één vermelding in een veld met aandelen Car-algoritmen. Niet elk probleem past bij VW, maar als dat wel het geval is, is het mogelijk uw tijd waard om de leer curve op de interface te zetten. Het is ook beschikbaar als [zelfstandige open-source code](https://github.com/JohnLangford/vowpal_wabbit) in verschillende talen.

## <a name="next-steps"></a>Volgende stappen

* Zie [basis beginselen van machine learning met algoritme voorbeelden](basics-infographic-with-algorithm-examples.md)voor een eenvoudig te begrijpen infographic-overzicht van machine learning-basis informatie over veelgebruikte algoritmen die worden gebruikt voor het beantwoorden van algemene machine learning vragen.

* Zie voor een lijst op categorie van alle machine learning-algoritmen die beschikbaar zijn in Machine Learning Studio (klassiek) [model initialiseren](/azure/machine-learning/studio-module-reference/machine-learning-initialize-model) in het algoritme machine learning Studio (klassiek) en module Help.

* Zie voor een volledig alfabetische lijst met algoritmen en modules in Machine Learning Studio (klassiek) [de modules a-Z-lijst van machine learning Studio (klassiek)](/azure/machine-learning/studio-module-reference/a-z-module-list) in machine learning Studio (klassiek)-algoritme en module Help.
