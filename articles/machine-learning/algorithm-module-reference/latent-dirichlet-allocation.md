---
title: Latente Dirichlet-toewijzing
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u de Dirichlet-toewijzings module kunt gebruiken om niet-geclassificeerde tekst in een aantal categorieën te groeperen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/05/2020
ms.openlocfilehash: a75bf458a1c6735de42349de5d5cb6845e9ae464
ms.sourcegitcommit: 20e246e86e25d63bcd521a4b4d5864fbc7bad1b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/08/2020
ms.locfileid: "84487964"
---
# <a name="latent-dirichlet-allocation"></a>Latente Dirichlet-toewijzing

In dit artikel wordt beschreven hoe u de **Dirichlet-toewijzings** module in azure machine learning Designer (preview) gebruikt om niet-geclassificeerde tekst in een aantal categorieën te groeperen. 

Latente Dirichlet toewijzing (LDA) wordt vaak gebruikt in natuurlijke taal verwerking (NLP) om teksten te vinden die vergelijkbaar zijn. Een andere algemene term is een *onderwerp model lering*.

Deze module haalt een tekst kolom op en genereert de volgende uitvoer:

+ De bron tekst, samen met een score voor elke categorie

+ Een functie matrix met geëxtraheerde voor waarden en coëfficiënten voor elke categorie

+ Een trans formatie, die u kunt opslaan en opnieuw Toep assen op nieuwe tekst die als invoer wordt gebruikt

Deze module maakt gebruik van de scikit-Learn-bibliotheek. Zie voor meer informatie over scikit-informatie de [GitHub-opslag plaats, die zelf studies bevat en een uitleg van de algoritme.

### <a name="more-about-latent-dirichlet-allocation-lda"></a>Meer informatie over de Dirichlet-toewijzing (LDA)

LDA is over het algemeen geen methode voor classificatie per se, maar maakt gebruik van een pregeneratieve benadering. Dit betekent dat u geen bekende klassen labels hoeft op te geven en vervolgens de patronen af te leiden.  In plaats daarvan wordt een Probabilistic-model gegenereerd dat wordt gebruikt om groepen met onderwerpen te identificeren. U kunt het Probabilistic-model gebruiken voor het classificeren van bestaande trainings cases of nieuwe cases die u aan het model levert als invoer.

Een kernvast model kan worden voor keuren, omdat het voor komt dat er sterke hypo Thesen worden gemaakt over de relatie tussen de tekst en categorieën, en wordt alleen de distributie van woorden gebruikt voor wiskundige model onderwerpen.

+ De theorie wordt in dit document besproken, beschikbaar als PDF-down load: latente [Dirichlet toewijzing: Blei, ng en Jordanië](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf)

+ De implementatie in deze module is gebaseerd op de [scikit-Learn-bibliotheek](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) voor Lda.

Zie de sectie met [technische opmerkingen](#technical-notes) voor meer informatie.

## <a name="how-to-configure-latent-dirichlet-allocation"></a>De toewijzing van latente Dirichlet configureren

Deze module vereist een gegevensset die een kolom met tekst bevat, hetzij RAW ofwel preprocesd.

1. Voeg de **Dirichlet-toewijzings** module aan uw pijp lijn toe.

2. Geef als invoer voor de module een gegevensset op die een of meer tekst kolommen bevat.

3. Kies voor **doel kolommen**een of meer kolommen met tekst die u wilt analyseren.

    U kunt meerdere kolommen kiezen, maar deze moeten van het teken reeks gegevens type zijn.

    In het algemeen, omdat LDA een grote functie matrix maakt op basis van de tekst, analyseert u doorgaans een enkele tekst kolom.

4. Typ een geheel getal tussen 1 en 1000 dat aangeeft hoeveel categorieën of onderwerpen u wilt afleiden van de invoer tekst voor het **aantal onderwerpen dat moet worden gemodelleerd**.

    Standaard worden er vijf onderwerpen gemaakt.

5. Voor **n-gram**geeft u de maximale lengte van n-gram op die tijdens hashing wordt gegenereerd.

    De standaard waarde is 2, wat betekent dat zowel bigrams als unigrams worden gegenereerd.

6. Selecteer de optie **normaliseren** om uitvoer waarden te converteren naar kansen. In plaats van de getransformeerde waarden als gehele getallen weer te geven, worden waarden in de uitvoer-en functie gegevensset als volgt getransformeerd:

    + De waarden in de gegevensset worden weer gegeven als een kans waarbij `P(topic|document)` .

    + Waarden in de matrix van het functie-onderwerp worden weer gegeven als een kans waar `P(word|topic)` .

    > [!NOTE] 
    > In Azure Machine Learning Designer (preview), omdat de bibliotheek die we hebben gebaseerd, scikit-Learn niet langer ondersteuning bieden voor niet-genormaliseerde *doc_topic_distr* uitvoer van versie 0,19, kan in deze module **normaliseren** para meter alleen worden toegepast op de matrix uitvoer van de **functie** , **getransformeerde gegevensset** -uitvoer wordt altijd genormaliseerd.

7. Selecteer de optie, **alle opties weer geven**en stel deze in op waar als u aanvullende geavanceerde para meters wilt weer geven en instellen.

    Deze para meters zijn specifiek voor de scikit-leer implementatie van LDA. Er zijn een aantal goede zelf studies over LDA in scikit-informatie, evenals het officiële [scikit-document](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html).

    + **Rho-para meter**. Geef een eerdere kans op voor de sparsity van onderwerp-distributies. Komt overeen met de `topic_word_prior` para meter sklearn. U gebruikt de waarde 1 als u verwacht dat de distributie van woorden plat is; dat wil zeggen dat alle woorden worden verondersteld equiprobable. Als u denkt dat de meeste woorden verspreid worden weer gegeven, kunt u deze op een veel lagere waarde instellen.

    + **Alfa-para meter**. Geef een eerdere kans op voor de sparsity van het onderwerp gewicht per document.  Komt overeen met de `doc_topic_prior` para meter sklearn.

    + Het **geschatte aantal documenten**. Typ een getal dat de beste schatting aangeeft van het aantal documenten (rijen) dat wordt verwerkt. Hiermee kan de module een hash-tabel met voldoende grootte toewijzen.  Komt overeen met de `total_samples` para meter in scikit-learn.

    + **De grootte van de batch**. Typ een getal dat aangeeft hoeveel rijen moeten worden ingevoegd in elke batch tekst die wordt verzonden naar het LDA-model. Komt overeen met de `batch_size` para meter in scikit-learn.

    + De **begin waarde van de iteratie die wordt gebruikt voor het plannen van de trainings update**. Geef de begin waarde op die de downweights-Learning frequentie voor vroege iteraties in online training aangeeft. Komt overeen met de `learning_offset` para meter in scikit-learn.

    + **Kracht die tijdens de updates op de herhaling wordt toegepast**. Geef het energie niveau op dat wordt toegepast op het aantal iteraties voor het beheren van het leer tempo tijdens online-updates. Komt overeen met de `learning_decay` para meter in scikit-learn.

    + **Aantal Passes over de gegevens**. Geef het maximum aantal keren op dat het algoritme voor de gegevens wordt gerecycled. Komt overeen met de `max_iter` para meter in scikit-learn.

8. Selecteer de optie, maak een **woorden lijst van ngrams** of maak een **woorden lijst van ngrams vóór LDA**. Als u de n-gram lijst in een eerste keer wilt maken, moet u voordat u tekst classificeert.

    Als u de eerste woorden lijst vooraf maakt, kunt u later de woorden lijst gebruiken bij het controleren van het model. Het is over het algemeen eenvoudiger om resultaten toe te wijzen aan tekst in plaats van numerieke indexen. Het opslaan van de woorden lijst duurt echter langer en het gebruik van extra opslag ruimte.

9. Voor de **maximale grootte van de ngram woorden lijst**, typt u het totale aantal rijen dat kan worden gemaakt in de n-gram-woorden lijst.

    Deze optie is handig voor het beheren van de grootte van de woorden lijst. Als het aantal ngrams in de invoer echter deze grootte overschrijdt, kunnen er conflicten optreden.

10. Verzend de pijp lijn. De LDA-module maakt gebruik van Bayes theorema om te bepalen welke onderwerpen kunnen worden gekoppeld aan afzonderlijke woorden. Woorden die niet exclusief zijn gekoppeld aan onderwerpen of groepen; in plaats daarvan heeft elke n-gram een geleerde kans om te koppelen aan een van de gedetecteerde klassen.

## <a name="results"></a>Resultaten

De module heeft twee uitvoer:

+ **Getransformeerde gegevensset**: bevat de invoer tekst en een opgegeven aantal gedetecteerde categorieën, samen met de scores voor elk tekst voorbeeld voor elke categorie.

+ **Functie onderwerp matrix**: de meest linkse kolom bevat de geëxtraheerde tekst functie en er is een kolom voor elke categorie met daarin de score voor die functie in die categorie.


### <a name="lda-transformation"></a>LDA-trans formatie

Deze module voert ook de *LDA-trans formatie* uit waarmee Lda op de gegevensset wordt toegepast.

U kunt deze trans formatie opslaan door deze opnieuw te gebruiken voor andere gegevens sets. Dit kan handig zijn als u hebt getraind op een grote verzameling en u de coëfficiënten of categorieën opnieuw wilt gebruiken.
Als u deze trans formatie opnieuw wilt gebruiken, klikt u op het pictogram gegevensset registreren in het rechter paneel van de module LDA om deze te laten deel **uitmaken** van een module onder **gegevens sets** in de lijst module. Vervolgens kunt u deze module verbinden om de transformatie module toe te [passen](apply-transformation.md) om deze trans formatie opnieuw te gebruiken.

### <a name="refining-an-lda-model-or-results"></a>Een LDA-model of-resultaten verfijnen

Doorgaans kunt u slechts één LDA-model maken dat aan alle behoeften voldoet, en zelfs een model dat is ontworpen voor de ene taak kan veel iteraties vereisen om de nauw keurigheid te verbeteren. We raden u aan al deze methoden te proberen om uw model te verbeteren:

+ De para meters van het model wijzigen
+ Visualisatie gebruiken om de resultaten te begrijpen
+ U krijgt de feedback over experts om te bepalen of de gegenereerde onderwerpen nuttig zijn.

Kwalitatieve maat regelen kunnen ook handig zijn voor het beoordelen van de resultaten. Als u de resultaten van een onderwerpen model wilt evalueren, kunt u overwegen:

+ Nauw keurigheid: vergelijk bare items zijn heel vergelijkbaar?
+ Diversiteit: kan het model onderscheid tussen vergelijk bare items als dat nodig is voor het bedrijfs probleem?
+ Schaal baarheid: werkt alleen in een breed scala aan tekst categorieën of alleen op een beperkt doel domein?

De nauw keurigheid van modellen gebaseerd op LDA kan vaak worden verbeterd door gebruik te maken van natuurlijke taal verwerking om tekst te reinigen, samen te vatten en te vereenvoudigen of te categoriseren. De volgende technieken, die allemaal worden ondersteund in Azure Machine Learning, kunnen de classificatie nauw keurigheid verbeteren:

+ Verwijderen van woorden stoppen

+ Case-normalisatie

+ Lemmatisering of stam gebruik

+ Herkenning van tekeneenheden

Zie [preproces-tekst](preprocess-text.md)voor meer informatie.

In de ontwerp functie kunt u ook R-of python-bibliotheken gebruiken voor tekst verwerking: [r-script uitvoeren](execute-r-script.md), [python-script uitvoeren](execute-python-script.md)



## <a name="technical-notes"></a>Technische opmerkingen

Deze sectie bevat implementatie details, tips en antwoorden op veelgestelde vragen.

### <a name="implementation-details"></a>Implementatie Details

Standaard worden de distributies van uitvoer voor getransformeerde gegevensset en functie-topic genormaliseerd als kansen.

+ De getransformeerde gegevensset wordt genormaliseerd als de voorwaardelijke kans van onderwerpen op basis van een document. In dit geval is de som van elke rij gelijk aan 1.

+ De functie-onderwerp matrix wordt genormaliseerd als de voorwaardelijke waarschijnlijkheid van woorden op basis van een onderwerp. In dit geval is de som van elke kolom gelijk aan 1.

> [!TIP]
> Af en toe kan de module een leeg onderwerp retour neren. dit wordt meestal veroorzaakt door de pseudo-wille keurige initialisatie van de algoritme.  Als dit het geval is, kunt u de bijbehorende para meters wijzigen, zoals de maximale grootte van de N-gram-woorden lijst of het aantal bits dat moet worden gebruikt voor functie-hashing.

### <a name="lda-and-topic-modeling"></a>LDA en topics model lering

Latente Dirichlet-toewijzing (LDA) wordt vaak gebruikt voor *model lering op basis van inhoud*. Dit houdt in dat categorieën worden leren van niet-geclassificeerde tekst. In een onderwerp dat is gebaseerd op inhouds modellen, is een onderwerp een distributie over woorden.

Stel dat u een verzameling hebt geleverd met beoordelingen van klanten die veel, veel producten bevatten. De tekst van beoordelingen die zijn ingediend door veel klanten gedurende een bepaalde periode, bevat veel voor waarden, waarvan sommige worden gebruikt in meerdere onderwerpen.

Een **onderwerp** dat wordt geïdentificeerd door het LDA-proces, kan beoordelingen vertegenwoordigen voor een afzonderlijk product A, of het kan een groep product beoordelingen vertegenwoordigen. Voor LDA is het onderwerp zelf een waarschijnlijke verdeling van de duur van een aantal woorden.

De termen zijn zelden exclusief voor een product, maar kunnen verwijzen naar andere producten, of algemene voor waarden die van toepassing zijn op alles (' geweldig ', ' Awful '). Andere voor waarden kunnen ruis woorden zijn.  Het is echter belang rijk om te begrijpen dat de LDA-methode niet van toepassing is op het vastleggen van alle woorden in het universum, of om te begrijpen hoe woorden zijn gerelateerd, afgezien van de waarschijnlijkheid van co-instantie. Hiermee kunnen alleen woorden worden gegroepeerd die in het doel domein zijn gebruikt.

Nadat de termen van de term zijn berekend, worden afzonderlijke tekst rijen vergeleken met behulp van een op afstand gebaseerde soort gelijke maat eenheid om te bepalen of twee stukken tekst op elkaar lijken.  U kunt bijvoorbeeld zien dat het product meerdere namen heeft die sterk worden gecorreleerd. Het is ook mogelijk dat zeer negatieve voor waarden doorgaans zijn gekoppeld aan een bepaald product. U kunt de maat eenheid van de overeenkomst gebruiken om gerelateerde voor waarden te identificeren en aanbevelingen te maken.

###  <a name="module-parameters"></a>Module parameters

|Naam|Type|Bereik|Optioneel|Standaard|Beschrijving|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Doel kolom (men)|Kolom selectie||Vereist|StringFeature|Naam of index van doel kolom|  
|Aantal te model lerene onderwerpen|Geheel getal|[1; 1000]|Vereist|5|De document distributie met N onderwerpen model leren|  
|N-gram|Geheel getal|[1; 10]|Vereist|2|Volg orde van N-gram gegenereerd tijdens hashing|  
|Normaliseren|Booleaans|Waar of onwaar|Vereist|true|Normaliseer uitvoer naar kansen.  De getransformeerde gegevensset is P (onderwerp&#124;document) en de matrix van het functie onderwerp is P (woord&#124;onderwerp)|  
|Alle opties weer geven|Booleaans|Waar of onwaar|Vereist|False|Geeft aanvullende para meters die specifiek zijn voor scikit-Learn Online LDA|  
|Rho-para meter|Float|[0.00001; 1.0]|Van toepassing wanneer het selectie vakje **alle opties weer geven** is geselecteerd|0,01|Voorafgaande distributie over onderwerps woord|  
|Alpha-para meter|Float|[0.00001; 1.0]|Van toepassing wanneer het selectie vakje **alle opties weer geven** is geselecteerd|0,01|Document onderwerp voorafgaande distributie|  
|Geschat aantal documenten|Geheel getal|[1; int. MaxValue|Van toepassing wanneer het selectie vakje **alle opties weer geven** is geselecteerd|1000|Het geschatte aantal documenten (komt overeen met total_samples para meter)|  
|Grootte van de batch|Geheel getal|[1; 1024]|Van toepassing wanneer het selectie vakje **alle opties weer geven** is geselecteerd|32|Grootte van de batch|  
|Aanvankelijke waarde van herhaling die wordt gebruikt voor de update planning voor het trainings tempo|Geheel getal|[0; int. MaxValue|Van toepassing wanneer het selectie vakje **alle opties weer geven** is geselecteerd|0|Aanvankelijke waarde voor downweights Learning rate voor vroege iteraties. Komt overeen met de para meter learning_offset|  
|Kracht toegepast op de herhaling tijdens updates|Float|[0,0; 1.0]|Van toepassing wanneer het selectie vakje **alle opties weer geven** is geselecteerd|0,5|Kracht toegepast op het aantal iteraties om het leer tempo te beheren. Komt overeen met de para meter learning_decay |  
|Aantal opleidings herhalingen|Geheel getal|[1; 1024]|Van toepassing wanneer het selectie vakje **alle opties weer geven** is geselecteerd|25|Aantal opleidings herhalingen|  
|Woorden lijst van ngrams bouwen|Booleaans|Waar of onwaar|Van toepassing wanneer het selectie vakje **alle opties weer geven** *is uitgeschakeld*|True|Bouwt een woorden lijst van ngrams vóór de Computing LDA. Nuttig voor model inspectie en-interpretatie|  
|Maximale grootte van ngram-woorden lijst|Geheel getal|[1; int. MaxValue|Van toepassing wanneer de keuze **lijst Dictionary van Ngrams** waar is|20.000|Maximale grootte van de ngrams-woorden lijst. Als het aantal tokens in de invoer groter is dan deze grootte, kunnen er conflicten optreden|  
|Aantal bits dat moet worden gebruikt voor functie-hashing|Geheel getal|[1; 31]|Van toepassing wanneer het selectie vakje **alle opties weer geven** is *niet* geselecteerd en het **bouwen van de woorden lijst ngrams** is ingesteld op False|12|Aantal bits dat moet worden gebruikt voor functie-hashing| 
|Build Dictionary van ngrams vóór LDA|Booleaans|Waar of onwaar|Van toepassing wanneer het selectie vakje **alle opties weer geven** is geselecteerd|True|Hiermee maakt u een woorden lijst met ngrams vóór LDA. Nuttig voor model inspectie en-interpretatie|  
|Maximum aantal ngrams in woorden lijst|Geheel getal|[1; int. MaxValue|Van toepassing wanneer het selectie vakje **alle opties weer geven** is geselecteerd en de optie **woorden lijst van ngrams** is ingesteld op True|20.000|Maximum grootte van de woorden lijst. Als het aantal tokens in de invoer groter is dan deze grootte, kunnen er conflicten optreden|  
|Aantal hash-bits|Geheel getal|[1; 31]|Van toepassing wanneer het selectie vakje **alle opties weer geven** is geselecteerd en de optie **woorden lijst van ngrams** is ingesteld op False|12|Aantal bits dat moet worden gebruikt tijdens hashing van functies|   


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning.   
Zie [uitzonde ringen en fout codes voor de ontwerp functie](designer-error-codes.md)voor een lijst met fouten die specifiek zijn voor de modules.
