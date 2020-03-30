---
title: Net# aangepaste neurale netwerken
titleSuffix: ML Studio (classic) - Azure
description: Syntaxisgids voor de specificatietaal net# neurale netwerken. Meer informatie over het maken van aangepaste neurale netwerkmodellen in Azure Machine Learning Studio (klassiek).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2018
ms.openlocfilehash: c1912e670a9cf1c178b58cefbd33171f15be2483
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218252"
---
# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning-studio-classic"></a>Gids voor net# neurale netwerkspecificatietaal voor Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Net# is een taal ontwikkeld door Microsoft die wordt gebruikt om complexe neurale netwerkarchitecturen te definiëren, zoals diepe neurale netwerken of convolutions van willekeurige dimensies. U complexe structuren gebruiken om het leren over gegevens zoals beeld, video of audio te verbeteren.

U in deze contexten een Net#-architectuurspecificatie gebruiken:

+ Alle neurale netwerkmodules in Microsoft Azure Machine Learning Studio (klassiek): [Multiclass Neural Network,](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/multiclass-neural-network) [Two-Class Neural Network](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/two-class-neural-network)en [Neural Network Regression](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Neurale netwerkfuncties in Microsoft ML Server: [NeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/neuralnet) en [rxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet)voor de R-taal en [rx_neural_network](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network) voor Python.


In dit artikel worden de basisconcepten en syntaxis beschreven die nodig zijn om een aangepast neuraal netwerk te ontwikkelen met Net#:

+ Neurale netwerkvereisten en hoe de primaire componenten te definiëren
+ De syntaxis en trefwoorden van de taal net#specificatie
+ Voorbeelden van aangepaste neurale netwerken gemaakt met behulp van Net #



## <a name="neural-network-basics"></a>Basisprincipes van neurale netwerken

Een neurale netwerkstructuur bestaat uit knooppunten die zijn georganiseerd in lagen en gewogen verbindingen (of randen) tussen de knooppunten. De verbindingen zijn directioneel en elke verbinding heeft een bronknooppunt en een doelknooppunt.

Elke trainbare laag (een verborgen of een uitvoerlaag) heeft een of meer **verbindingsbundels.** Een verbindingsbundel bestaat uit een bronlaag en een specificatie van de verbindingen van die bronlaag. Alle verbindingen in een bepaalde bundel delen bron- en doellagen. In Net#wordt een verbindingsbundel beschouwd als behorend tot de doellaag van de bundel.

Net# ondersteunt verschillende soorten verbindingsbundels, waarmee u de manier waarop ingangen worden toegewezen aan verborgen lagen en toegewezen aan de uitgangen aanpassen.

De standaard- of standaardbundel is een **volledige bundel,** waarin elk knooppunt in de bronlaag is verbonden met elk knooppunt in de doellaag.

Daarnaast ondersteunt Net# de volgende vier soorten geavanceerde verbindingsbundels:

+ **Gefilterde bundels**. U een predicaat definiëren met behulp van de locaties van het bronlaagknooppunt en het knooppunt van de doellaag. Knooppunten worden verbonden wanneer het predicaat Waar is.

+ **Convolutional bundels**. U kleine buurten van knooppunten in de bronlaag definiëren. Elk knooppunt in de doellaag is verbonden met één buurt met knooppunten in de bronlaag.

+ **Bundelingsbundels** en **Response-normalisatiebundels**. Deze zijn vergelijkbaar met convolutionalbundels in die tijd dat de gebruiker kleine buurten van knooppunten in de bronlaag definieert. Het verschil is dat de gewichten van de randen in deze bundels niet trainbaar zijn. In plaats daarvan wordt een vooraf gedefinieerde functie toegepast op de waarden van het bronknooppunt om de waarde van het doelknooppunt te bepalen.


## <a name="supported-customizations"></a>Ondersteunde aanpassingen

De architectuur van neurale netwerkmodellen die u in Azure Machine Learning Studio (klassiek) maakt, kan uitgebreid worden aangepast met Net#. U kunt:

+ Maak verborgen lagen en bepaal het aantal knooppunten in elke laag.
+ Geef op hoe lagen met elkaar moeten worden verbonden.
+ Definieer speciale connectiviteitsstructuren, zoals convolutions en gewichtsverdelingsbundels.
+ Geef verschillende activeringsfuncties op.

Zie [Structuurspecificatie](#structure-specifications)voor meer informatie over de syntaxis van de specificatietaal .

Zie [Voorbeelden](#examples-of-net-usage)voor het definiëren van neurale netwerken voor een aantal veelvoorkomende machine learning-taken, van simplex tot complex.

## <a name="general-requirements"></a>Algemene vereisten

+ Er moet precies één uitvoerlaag, ten minste één invoerlaag en nul of meer verborgen lagen zijn.
+ Elke laag heeft een vast aantal knooppunten, conceptueel gerangschikt in een rechthoekige array van willekeurige afmetingen.
+ Invoerlagen hebben geen gekoppelde getrainde parameters en vertegenwoordigen het punt waar instantiegegevens het netwerk binnenkomen.
+ Trainbare lagen (de verborgen en uitvoerlagen) hebben getrainde parameters, bekend als gewichten en vooroordelen, gekoppeld.
+ De bron- en doelknooppunten moeten zich in afzonderlijke lagen bevinden.
+ Verbindingen moeten acyclisch zijn; Met andere woorden, er kan geen keten van verbindingen zijn die terugleidt naar het oorspronkelijke bronknooppunt.
+ De uitvoerlaag kan geen bronlaag van een verbindingsbundel zijn.

## <a name="structure-specifications"></a>Structuurspecificaties

Een neurale netwerkstructuurspecificatie bestaat uit drie secties: de **constante declaratie**, de **laagdeclaratie**, de **verbindingsdeclaratie**. Er is ook een optionele **sectie van de declaratie** van het aandeel. De secties kunnen in elke volgorde worden opgegeven.

## <a name="constant-declaration"></a>Constante aangifte

Een constante verklaring is optioneel. Het biedt een middel om waarden te definiëren die elders in de neurale netwerkdefinitie worden gebruikt. De verklaring bestaat uit een identificatienummer gevolgd door een gelijk teken en een waardeexpressie.

In de volgende instructie wordt `x`bijvoorbeeld een constante gedefinieerd:

`Const X = 28;`

Als u twee of meer constanten tegelijk wilt definiëren, sluit u de id-namen en -waarden in accolades in en scheidt u deze met puntkomma's. Bijvoorbeeld:

`Const { X = 28; Y = 4; }`

De rechterkant van elke toewijzingsexpressie kan een geheel getal, een echt getal, een Booleaanse waarde (Waar of Onwaar) of een wiskundige expressie zijn. Bijvoorbeeld:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Laagdeclaratie

De laagdeclaratie is vereist. Het definieert de grootte en bron van de laag, inclusief de verbindingsbundels en -kenmerken. De declaratie-instructie begint met de naam van de laag (invoer, verborgen of uitvoer), gevolgd door de afmetingen van de laag (een tuple van positieve gehele getallen). Bijvoorbeeld:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;
```

+ Het product van de afmetingen is het aantal knooppunten in de laag. In dit voorbeeld zijn er twee dimensies [5,20], wat betekent dat er 100 knooppunten in de laag zijn.
+ De lagen kunnen in elke volgorde worden gedeclareerd, met één uitzondering: Als er meer dan één invoerlaag is gedefinieerd, moet de volgorde waarin ze worden gedeclareerd overeenkomen met de volgorde van de functies in de invoergegevens.

Als u wilt opgeven dat het aantal knooppunten in `auto` een laag automatisch wordt bepaald, gebruikt u het trefwoord. Het `auto` zoekwoord heeft verschillende effecten, afhankelijk van de laag:

+ In een invoerlaagdeclaratie is het aantal knooppunten het aantal functies in de invoergegevens.
+ In een verborgen laagdeclaratie is het aantal knooppunten het getal dat wordt opgegeven door de parameterwaarde voor **Aantal verborgen knooppunten**.
+ In een uitvoerlaagdeclaratie is het aantal knooppunten 2 voor classificatie met twee klassen, 1 voor regressie en gelijk aan het aantal uitvoerknooppunten voor classificatie met meerdere klassen.

Met de volgende netwerkdefinitie kan bijvoorbeeld de grootte van alle lagen automatisch worden bepaald:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;
```

Een laagdeclaratie voor een trainbare laag (de verborgen of uitvoerlagen) kan optioneel de uitvoerfunctie (ook wel een activeringsfunctie genoemd) bevatten, die standaard **sigmoïde** voor classificatiemodellen en **lineair** voor regressiemodellen bevat. Zelfs als u de standaardinstelling gebruikt, u de activeringsfunctie expliciet vermelden, indien gewenst voor de duidelijkheid.

De volgende uitvoerfuncties worden ondersteund:

+ Sigmoid
+ Lineaire
+ softmax softmax
+ rlineair
+ Square
+ Sqrt
+ srlinear
+ Abs
+ Tanh
+ brlinear

In de volgende verklaring wordt bijvoorbeeld de **softmax-functie** gebruikt:

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Verbindingsverklaring

Onmiddellijk na het definiëren van de trainbare laag moet u verbindingen declareren tussen de lagen die u hebt gedefinieerd. De aangifte van de `from`verbindingsbundel begint met het trefwoord, gevolgd door de naam van de bronlaag van de bundel en het soort verbindingsbundel dat moet worden gemaakt.

Momenteel worden vijf soorten verbindingsbundels ondersteund:

+ **Volledige** bundels, aangegeven door het trefwoord`all`
+ **Gefilterde** bundels, aangegeven `where`door het trefwoord, gevolgd door een predicaatexpressie
+ **Convolutional** bundels, aangegeven `convolve`door het trefwoord, gevolgd door de convolutie attributen
+ **Bundelingsbundels,** aangegeven door de trefwoorden **max pool** of **gemiddelde pool**
+ **Response normalisatie** bundels, aangegeven door de trefwoord **respons norm**

## <a name="full-bundles"></a>Volledige bundels

Een volledige verbindingsbundel bevat een verbinding van elk knooppunt in de bronlaag met elk knooppunt in de doellaag. Dit is het standaardtype netwerkverbinding.

## <a name="filtered-bundles"></a>Gefilterde bundels

Een gefilterde verbindingsbundelspecificatie bevat een predicaat, syntactisch uitgedrukt, net als een C# lambda-expressie. In het volgende voorbeeld worden twee gefilterde bundels gedefinieerd:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
```

+ In het `ByRow`predicaat `s` voor , is een parameter die een index `Pixels`in `d` de rechthoekige array van knooppunten van de invoerlaag, en is een parameter die een index in de array van knooppunten van de verborgen laag, `ByRow`. Het type `s` van `d` beide en is een tuple van gehele getallen van lengte twee. Conceptueel, `s` bereiken over alle paren `0 <= s[0] < 10` `0 <= s[1] < 20`van `d` gehele getallen met en , `0 <= d[0] < 10` `0 <= d[1] < 12`en bereiken over alle paren van gehele getallen, met en .

+ Aan de rechterkant van de predicaatuitdrukking is er een voorwaarde. In dit voorbeeld is `s` er `d` voor elke waarde van en zodanig dat de voorwaarde True is, een rand van het bronlaagknooppunt naar het knooppunt van de doellaag. Deze filterexpressie geeft dus aan dat de bundel een `s` verbinding bevat van het `d` knooppunt dat wordt gedefinieerd door de node die wordt gedefinieerd door in alle gevallen waarin s[0] gelijk is aan d[0].

Optioneel u een set gewichten opgeven voor een gefilterde bundel. De waarde voor het kenmerk **Gewichten** moet een tuple zwevende puntwaarden zijn met een lengte die overeenkomt met het aantal verbindingen dat door de bundel wordt gedefinieerd. Standaard worden gewichten willekeurig gegenereerd.

Gewichtswaarden worden gegroepeerd op de doelknooppuntindex. Dat wil zeggen, als het eerste doelknooppunt is `K` verbonden met K-bronknooppunten, zijn de eerste elementen van de **tuple Gewichten** de gewichten voor het eerste doelknooppunt, in volgorde van de bronindex. Hetzelfde geldt voor de resterende doelknooppunten.

Het is mogelijk om gewichten direct als constante waarden op te geven. Als u bijvoorbeeld de gewichten eerder hebt geleerd, u deze als constanten opgeven met behulp van deze syntaxis:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Convolutional bundels

Wanneer de trainingsgegevens een homogene structuur hebben, worden convolutionalverbindingen vaak gebruikt om functies op hoog niveau van de gegevens te leren. In beeld-, audio- of videogegevens kan ruimtelijke of temporele dimensionaliteit bijvoorbeeld redelijk uniform zijn.

Convolutional bundels maken gebruik van rechthoekige **kernels** die door de afmetingen worden geschoven. In wezen definieert elke kernel een set gewichten die worden toegepast in lokale buurten, aangeduid als **kerneltoepassingen.** Elke kerneltoepassing komt overeen met een knooppunt in de bronlaag, dat het **centrale knooppunt**wordt genoemd. De gewichten van een kernel worden gedeeld tussen vele verbindingen. In een convolutional bundel, elke kernel is rechthoekig en alle kernel toepassingen zijn dezelfde grootte.

Convolutional bundels ondersteunen de volgende kenmerken:

**InputShape** definieert de dimensionaliteit van de bronlaag ten behoeve van deze convolutional bundel. De waarde moet een tuple van positieve gehele getallen zijn. Het product van de gehele getallen moet gelijk zijn aan het aantal knooppunten in de bronlaag, maar anders hoeft het niet overeen te komen met de dimensionaliteit die is opgegeven voor de bronlaag. De lengte van deze tuple wordt de **arity** waarde voor de convolutional bundel. Meestal verwijst arity naar het aantal argumenten of operands dat een functie kan nemen.

Als u de vorm en locaties van de kernels wilt definiëren, gebruikt u de kenmerken **KernelShape,** **Stride**, **Padding,** **LowerPad**en **UpperPad:**

+ **KernelShape**: (vereist) Definieert de dimensionaliteit van elke kernel voor de convolutional bundel. De waarde moet een tuple van positieve gehele getallen zijn met een lengte die gelijk is aan de arity van de bundel. Elk onderdeel van deze tuple mag niet groter zijn dan de overeenkomstige component **van InputShape**.

+ **Pas**: (optioneel) Definieert de schuifstapgroottes van de convolutie (één stapgrootte voor elke dimensie), dat wil zeggen de afstand tussen de centrale knooppunten. De waarde moet een tuple van positieve gehele getallen met een lengte die de arity van de bundel. Elk onderdeel van deze tuple mag niet groter zijn dan de overeenkomstige component van **KernelShape**. De standaardwaarde is een tuple met alle componenten gelijk aan één.

+ **Delen**: (optioneel) Definieert de gewichtsverdeling voor elke dimensie van de convolutie. De waarde kan een enkele Booleaanse waarde zijn of een tuple boole van Boolewaarden met een lengte die de arity van de bundel is. Een enkele Booleaanse waarde wordt uitgebreid tot een tuple van de juiste lengte met alle componenten gelijk aan de opgegeven waarde. De standaardwaarde is een tuple die bestaat uit alle True-waarden.

+ **MapCount**: (optioneel) Hiermee definieert u het aantal functiekaarten voor de convolutionalbundel. De waarde kan een enkel positief geheel getal zijn of een tuple van positieve gehele getallen met een lengte die de arity van de bundel is. Een enkele gehele waarde wordt uitgebreid tot een tuple van de juiste lengte met de eerste componenten gelijk aan de opgegeven waarde en alle resterende componenten gelijk aan een. De standaardwaarde is één. Het totale aantal functiekaarten is het product van de onderdelen van de tuple. De factoring van dit totale aantal over de componenten bepaalt hoe de waarden van de functiekaart worden gegroepeerd in de doelknooppunten.

+ **Gewichten**: (optioneel) Definieert de initiële gewichten voor de bundel. De waarde moet een tuple van drijvende puntwaarden zijn met een lengte die het aantal kernels is dat het aantal gewichten per kernel maal geeft, zoals later in dit artikel wordt gedefinieerd. De standaardgewichten worden willekeurig gegenereerd.

Er zijn twee sets van eigenschappen die opvulling controle, de eigenschappen zijn elkaar exclusief:

+ **Opvulling**: (optioneel) Bepaalt of de invoer moet worden opgevuld met behulp van een **standaard opvullingsschema**. De waarde kan een enkele Booleaanse waarde zijn, of het kan een tuple van Booleische waarden zijn met een lengte die de arity van de bundel is.

    Een enkele Booleaanse waarde wordt uitgebreid tot een tuple van de juiste lengte met alle componenten gelijk aan de opgegeven waarde.

    Als de waarde voor een dimensie True is, wordt de bron logischerwijs in die dimensie opgevuld met cellen met nulwaarden ter ondersteuning van extra kerneltoepassingen, zodat de centrale knooppunten van de eerste en laatste kernels in die dimensie de eerste en laatste knooppunten zijn in die dimensie in de bronlaag. Zo wordt het aantal "dummy" knooppunten in elke dimensie `(InputShape[d] - 1) / Stride[d] + 1` automatisch bepaald, om precies kernels in de gewatteerde bronlaag te passen.

    Als de waarde voor een dimensie Onwaar is, worden de kernels zo gedefinieerd dat het aantal knooppunten aan elke kant dat buitengesloten is hetzelfde is (tot een verschil van 1). De standaardwaarde van dit kenmerk is een tuple met alle componenten gelijk aan False.

+ **UpperPad** en **LowerPad**: (optioneel) Zorg voor meer controle over de hoeveelheid opvulling die u wilt gebruiken. **Belangrijk:** Deze kenmerken kunnen worden gedefinieerd als en alleen als de eigenschap **Padding** hierboven ***niet*** is gedefinieerd. De waarden moeten integer-gewaardeerde tuples met lengtes die de arity van de bundel. Wanneer deze kenmerken zijn opgegeven, worden 'dummy'-knooppunten toegevoegd aan de onder- en bovenuiteinden van elke dimensie van de invoerlaag. Het aantal knooppunten dat aan de onderste en bovenste uiteinden in elke dimensie wordt toegevoegd, wordt bepaald door respectievelijk **LowerPad**[i] en **UpperPad**[i].

    Om ervoor te zorgen dat kernels alleen overeenkomen met "echte" knooppunten en niet met "dummy" knooppunten, moet aan de volgende voorwaarden worden voldaan:
  - Elk onderdeel van **LowerPad** moet `KernelShape[d]/2`strikt minder zijn dan .
  - Elk onderdeel van **UpperPad** mag `KernelShape[d]/2`niet groter zijn dan .
  - De standaardwaarde van deze kenmerken is een tuple met alle componenten gelijk aan 0.

    De instelling **Padding** = true maakt zoveel opvulling mogelijk als nodig is om het "midden" van de kernel in de "echte" ingang te houden. Dit verandert de wiskunde een beetje voor het berekenen van de output grootte. Over het algemeen wordt de uitvoergrootte `I` *D* berekend als `D = (I - K) / S + 1`, waar `S` is de invoergrootte, `K` de kernelgrootte is, de pas is en `/` integer-verdeling is (rond naar nul). Als u UpperPad = [1, 1] instelt, is de invoergrootte `I` effectief 29, en dus `D = (29 - 5) / 2 + 1 = 13`. Echter, wanneer **Padding** = `I` waar, in `K - 1`wezen wordt gestoten door ; vandaar `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14`. Door waarden voor **UpperPad** en **LowerPad op te** geven krijg je veel meer controle over de vulling dan wanneer je alleen **Padding** = true instelt.

Zie deze artikelen voor meer informatie over convolutional networks en hun toepassingen:

+ [http://deeplearning.net/tutorial/lenet.html](http://deeplearning.net/tutorial/lenet.html)
+ [https://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf)

## <a name="pooling-bundles"></a>Bundelingsbundels

Een **poolingbundel** past geometrie toe die vergelijkbaar is met convolutionalconnectivity, maar gebruikt vooraf gedefinieerde functies om knooppuntwaarden te bronom de waarde van het doelknooppunt af te leiden. Vandaar, pooling bundels hebben geen trainbare staat (gewichten of vooroordelen). Bundelingsbundels ondersteunen alle convolutionale kenmerken, behalve **Delen,** **MapCount**en **Gewichten**.

Doorgaans overlappen de kernels die worden samengevat door aangrenzende pooling-eenheden elkaar niet. Als Stride[d] in elke dimensie gelijk is aan KernelShape[d], is de verkregen laag de traditionele lokale poollaag, die vaak wordt gebruikt in convolutional neurale netwerken. Elk doelknooppunt berekent het maximum of het gemiddelde van de activiteiten van de kernel in de bronlaag.

In het volgende voorbeeld wordt een bundeling geïllustreerd:

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }
```

+ De arity van de bundel is 3: dat wil `InputShape` `KernelShape`zeggen, `Stride`de lengte van de tuples , en .
+ Het aantal knooppunten in de `5 * 24 * 24 = 2880`bronlaag is .
+ Dit is een traditionele lokale poollaag omdat **KernelShape** en **Stride** gelijk zijn.
+ Het aantal knooppunten in de `5 * 12 * 12 = 1440`doellaag is .

Zie de volgende artikelen voor meer informatie over het bundelen van lagen:

+ [https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf)(Punt 3.4)
+ [https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf)
+ [https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Response normalisatiebundels

**Response normalisatie** is een lokale normalisatie regeling die voor het eerst werd geïntroduceerd door Geoffrey Hinton, et al., in het papier [ImageNet Classificatie met Deep Convolutional Neural Networks](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf).

Reactienormalisatie wordt gebruikt om generalisatie in neurale netten te helpen. Wanneer een neuron is vuren op een zeer hoog activeringsniveau, een lokale reactie normalisatie laag onderdrukt het activeringsniveau van de omliggende neuronen. Dit wordt gedaan met`α`behulp `β`van `k`drie parameters ( , en ) en een ingewikkelde structuur (of buurtvorm). Elk neuron in de doellaag **y** komt overeen met een neuron **x** in de bronlaag. Het activeringsniveau van **y** wordt gegeven `f` door de volgende formule, `Nx` waarbij het activeringsniveau van een neuron is, en is de kernel (of de set die de neuronen in de buurt van **x**bevat), zoals gedefinieerd door de volgende convolutionale structuur:

![formule voor convolutionalstructuur](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Response normalisatie bundels ondersteunen alle convolutional attributen behalve **Delen,** **MapCount**en **Gewichten**.

+ Als de kernel neuronen bevat op dezelfde kaart als ***x,*** wordt het normalisatieschema aangeduid als **dezelfde kaartnormalisatie**. Als u dezelfde kaartnormalisatie wilt definiëren, moet de eerste coördinaat in **InputShape** de waarde 1 hebben.

+ Als de kernel neuronen bevat in dezelfde ruimtelijke positie als ***x,*** maar de neuronen zijn in andere kaarten, wordt het normalisatieschema genoemd **over kaarten normalisatie**. Dit type reactie normalisatie implementeert een vorm van laterale remming geïnspireerd door het type gevonden in echte neuronen, het creëren van concurrentie voor grote activeringsniveaus tussen neuron uitgangen berekend op verschillende kaarten. Als u de normalisatie van kaarten wilt definiëren, moet de eerste coördinaat een geheel getal zijn dat groter is dan één en niet groter is dan het aantal kaarten, en de rest van de coördinaten moet de waarde 1 hebben.

Omdat responsnormalisatiebundels een vooraf gedefinieerde functie toepassen op bronknooppuntwaarden om de waarde van het doelknooppunt te bepalen, hebben ze geen trainbare status (gewichten of vooroordelen).

> [!NOTE]
> De knooppunten in de doellaag komen overeen met neuronen die de centrale knooppunten van de kernels zijn. Als dit `KernelShape[d]` bijvoorbeeld vreemd `KernelShape[d]/2` is, komt dit overeen met het centrale kernelknooppunt. Als `KernelShape[d]` gelijk is, is het `KernelShape[d]/2 - 1`centrale knooppunt op . Daarom, `Padding[d]` als false is, hebben `KernelShape[d]/2` de eerste en de laatste knooppunten geen overeenkomstige knooppunten in de doellaag. Om deze situatie te voorkomen, definieer **je Padding** als [waar, waar, ..., waar].

Naast de vier eerder beschreven kenmerken ondersteunen responsnormalisatiebundels ook de volgende kenmerken:

+ **Alfa**: (vereist) Hiermee geeft u een `α` drijvende-kommawaarde op die overeenkomt met in de vorige formule.
+ **Bèta**: (vereist) Hiermee geeft u een `β` drijvende-kommawaarde op die overeenkomt met in de vorige formule.
+ **Verschuiving**: (optioneel) Hiermee geeft u een `k` drijvende-kommawaarde op die overeenkomt met in de vorige formule. Het standaard op 1.

In het volgende voorbeeld wordt een antwoordnormalisatiebundel gedefinieerd met behulp van de volgende kenmerken:

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }
```

+ De bronlaag bevat vijf kaarten, elk met een aof dimensie van 12x12, in totaal in 1440 knooppunten.
+ De waarde van **KernelShape** geeft aan dat dit dezelfde kaartnormalisatielaag is, waarbij de buurt een 3x3-rechthoek is.
+ De standaardwaarde van **Opvulling** is False, dus de doellaag heeft slechts 10 knooppunten in elke dimensie. Als u één knooppunt wilt opnemen in de doellaag die overeenkomt met elk knooppunt in de bronlaag, voegt u Vulling = [waar, waar, waar] toe; en verander de grootte van RN1 in [5, 12, 12].

## <a name="share-declaration"></a>Declaratie van delen

Net# ondersteunt optioneel het definiëren van meerdere bundels met gedeelde gewichten. De gewichten van twee bundels kunnen worden gedeeld als hun structuren hetzelfde zijn. In de volgende syntaxis worden bundels met gedeelde gewichten gedefinieerd:

```Net#
share-declaration:
  share    {    layer-list    }
  share    {    bundle-list    }
  share    {    bias-list    }

  layer-list:
    layer-name    ,    layer-name
    layer-list    ,    layer-name

  bundle-list:
    bundle-spec    ,    bundle-spec
    bundle-list    ,    bundle-spec

  bundle-spec:
    layer-name    =>     layer-name

  bias-list:
    bias-spec    ,    bias-spec
    bias-list    ,    bias-spec

  bias-spec:
    1    =>    layer-name

  layer-name:
    identifier
```

In de volgende share-declaration worden bijvoorbeeld de laagnamen opgegeven, die aangeeft dat zowel gewichten als vooroordelen moeten worden gedeeld:

```Net#
Const {
  InputSize = 37;
  HiddenSize = 50;
  }
input {
  Data1 [InputSize];
  Data2 [InputSize];
  }
hidden {
  H1 [HiddenSize] from Data1 all;
  H2 [HiddenSize] from Data2 all;
  }
output Result [2] {
  from H1 all;
  from H2 all;
  }
share { H1, H2 } // share both weights and biases
```

+ De invoerfuncties worden verdeeld in twee invoerlagen van gelijke grootte.
+ De verborgen lagen berekenen vervolgens eigenschappen op een hoger niveau op de twee invoerlagen.
+ De aandelenverklaring bepaalt dat *H1* en *H2* op dezelfde manier moeten worden berekend op basis van hun respectieve inputs.

Dit kan ook worden gespecificeerd met twee afzonderlijke aandelendeclaratie als volgt:

```Net#
share { Data1 => H1, Data2 => H2 } // share weights
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases
```

U het korte formulier alleen gebruiken als de lagen één bundel bevatten. In het algemeen is delen alleen mogelijk wanneer de relevante structuur identiek is, wat betekent dat ze dezelfde grootte, dezelfde convolutionale geometrie hebben, enzovoort.

## <a name="examples-of-net-usage"></a>Voorbeelden van net# gebruik

In deze sectie vindt u enkele voorbeelden van hoe u Net# gebruiken om verborgen lagen toe te voegen, de manier te definiëren waarop verborgen lagen met andere lagen communiceren en convolutionale netwerken te bouwen.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Definieer een eenvoudig aangepast neuraal netwerk: "Hello World" voorbeeld

Dit eenvoudige voorbeeld laat zien hoe je een neuraal netwerkmodel maken dat één verborgen laag heeft.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;
```

Het voorbeeld illustreert enkele basisopdrachten als volgt:

+ De eerste regel definieert `Data`de invoerlaag (met de naam ). Wanneer u `auto` het zoekwoord gebruikt, bevat het neurale netwerk automatisch alle functiekolommen in de invoervoorbeelden.
+ De tweede regel maakt de verborgen laag. De `H` naam wordt toegewezen aan de verborgen laag, die 200 knooppunten heeft. Deze laag is volledig verbonden met de invoerlaag.
+ De derde regel definieert `Out`de uitvoerlaag (met de naam), die 10 uitvoerknooppunten bevat. Als het neurale netwerk wordt gebruikt voor classificatie, is er één uitvoerknooppunt per klasse. Het trefwoord **sigmoid** geeft aan dat de uitvoerfunctie wordt toegepast op de uitvoerlaag.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Meerdere verborgen lagen definiëren: voorbeeld van computervisie

In het volgende voorbeeld wordt uitgelegd hoe u een iets complexer neuraal netwerk definiëren, met meerdere aangepaste verborgen lagen.

```Net#
// Define the input layers
input Pixels [10, 20];
input MetaData [7];

// Define the first two hidden layers, using data only from the Pixels input
hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;

// Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
hidden Gather [100]
{
from ByRow all;
from ByCol all;
}

// Define the output layer and its sources
output Result [10]
{
from Gather all;
from MetaData all;
}
```

Dit voorbeeld illustreert verschillende kenmerken van de neurale netwerken specificatie taal:

+ De structuur heeft twee `Pixels` `MetaData`invoerlagen en .
+ De `Pixels` laag is een bronlaag voor twee `ByRow` verbindingsbundels, met doellagen en `ByCol`.
+ De `Gather` lagen `Result` en zijn doellagen in meerdere verbindingsbundels.
+ De uitvoerlaag `Result`is een doellaag in twee verbindingsbundels; een met de verborgen `Gather` laag op het tweede niveau als `MetaData` doellaag en de andere met de invoerlaag als doellaag.
+ De verborgen `ByRow` lagen `ByCol`en geven gefilterde connectiviteit op met behulp van predicaatexpressies. Om precies te zijn `ByRow` is het knooppunt in [x, `Pixels` y] verbonden met de knooppunten in die de eerste index coördinaat gelijk aan de eerste coördinaat van het knooppunt, x. Op dezelfde manier is `ByCol` het knooppunt in [x, y] verbonden met de knooppunten in `Pixels` die de tweede indexcoördinaat binnen een van de tweede coördinaat van het knooppunt hebben, y.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Een convolutional netwerk definiëren voor classificatie van meerdere klassen: voorbeeld van cijferherkenning

De definitie van het volgende netwerk is ontworpen om getallen te herkennen, en het illustreert een aantal geavanceerde technieken voor het aanpassen van een neuraal netwerk.

```Net#
input Image [29, 29];
hidden Conv1 [5, 13, 13] from Image convolve
  {
  InputShape  = [29, 29];
  KernelShape = [ 5,  5];
  Stride      = [ 2,  2];
  MapCount    = 5;
  }
hidden Conv2 [50, 5, 5]
from Conv1 convolve
  {
  InputShape  = [ 5, 13, 13];
  KernelShape = [ 1,  5,  5];
  Stride      = [ 1,  2,  2];
  Sharing     = [false, true, true];
  MapCount    = 10;
  }
hidden Hid3 [100] from Conv2 all;
output Digit [10] from Hid3 all;
```

+ De structuur heeft één `Image`invoerlaag, .
+ Het `convolve` trefwoord geeft aan `Conv1` `Conv2` dat de lagen met de naam en convolutional lagen. Elk van deze laagdeclaraties wordt gevolgd door een lijst van de convolutiekenmerken.
+ Het net heeft een `Hid3`derde verborgen laag, die volledig `Conv2`is aangesloten op de tweede verborgen laag, .
+ De uitvoerlaag `Digit`is alleen verbonden met de `Hid3`derde verborgen laag. Het `all` trefwoord geeft aan dat de `Hid3`uitvoerlaag volledig is verbonden met .
+ De arity van de convolution is drie: `InputShape`de `KernelShape` `Stride`lengte `Sharing`van de tuples , , en .
+ Het aantal gewichten per `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26`kernel is . Of. `26 * 50 = 1300`
+ U de knooppunten in elke verborgen laag als volgt berekenen:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ Het totale aantal knooppunten kan worden berekend met behulp van de aangegeven dimensionaliteit van de laag, [50, 5, 5], als volgt:`MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Want `Sharing[d]` is Alleen `d == 0`onwaar voor , `MapCount * NodeCount\[0] = 10 * 5 = 50`het aantal kernels is .

## <a name="acknowledgements"></a>Erkenningen

De Net# taal voor het aanpassen van de architectuur van neurale netwerken werd ontwikkeld bij Microsoft door Shon Katzenberger (Architect, Machine Learning) en Alexey Kamenev (Software Engineer, Microsoft Research). Het wordt intern gebruikt voor machine learning-projecten en toepassingen, variërend van beelddetectie tot tekstanalyse. Zie [Neurale netten in Azure Machine Learning-studio - Inleiding tot Net#](https://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx) voor meer informatie
