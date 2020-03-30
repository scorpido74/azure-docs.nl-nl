---
title: Latente Dirichlet-toewijzing
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Latente Dirichlet-toewijzing om anders niet-geclassificeerde tekst te groeperen in een aantal categorieën.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/11/2020
ms.openlocfilehash: 1384491489c175ffc338f80a99aa8d5050f835d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109223"
---
# <a name="latent-dirichlet-allocation"></a>Latente Dirichlet-toewijzing

In dit artikel wordt beschreven hoe u de module **Latente Dirichlet-toewijzing** gebruiken in Azure Machine Learning-ontwerper (voorbeeld), om anders niet-geclassificeerde tekst te groeperen in een aantal categorieën. 

Latente Dirichlet Allocation (LDA) wordt vaak gebruikt in natural language processing (NLP) om teksten te vinden die vergelijkbaar zijn. Een andere veel voorkomende term is *topic modeling*.

Deze module neemt een kolom tekst en genereert deze uitvoer:

+ De brontekst, samen met een score voor elke categorie

+ Een functiematrix, met geëxtraheerde termen en coëfficiënten voor elke categorie

+ Een transformatie, die u opslaan en opnieuw toepassen op nieuwe tekst die wordt gebruikt als invoer

Deze module maakt gebruik van de scikit-learn bibliotheek. Zie voor meer informatie over scikit-learn de [GitHub repository, met tutorials en een uitleg van het algoritme.

### <a name="more-about-latent-dirichlet-allocation-lda"></a>Meer informatie over Latente Dirichlet Allocation (LDA)

Over het algemeen is LDA geen methode voor classificatie op zich, maar maakt gebruik van een generatieve aanpak. Wat dit betekent is dat u geen bekende klassenlabels hoeft te leveren en vervolgens de patronen af te leiden.  In plaats daarvan genereert het algoritme een probabilistisch model dat wordt gebruikt om groepen onderwerpen te identificeren. U het probabilistische model gebruiken om bestaande trainingsaanvragen of nieuwe aanvragen die u aan het model verstrekt, als invoer te classificeren.

Een generatief model kan de voorkeur hebben omdat het vermijdt het maken van sterke veronderstellingen over de relatie tussen de tekst en categorieën, en gebruikt alleen de verdeling van woorden om wiskundig model onderwerpen.

+ De theorie wordt besproken in dit artikel, beschikbaar als PDF-download: [Latente Dirichlet Allocatie: Blei, Ng en Jordanië](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf)

+ De implementatie in deze module is gebaseerd op de [scikit-learn bibliotheek](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) voor LDA.

Zie voor meer informatie de sectie [Technische notities.](#technical-notes)

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Latente Dirichlet-toewijzing configureren

Deze module vereist een gegevensset die een kolom tekst bevat, ruw of vooraf verwerkt.

1. Voeg de **module Latente Dirichlet-toewijzing** toe aan uw pijplijn.

2. Geef als invoer voor de module een gegevensset op met een of meer tekstkolommen.

3. Kies **voor Doelkolommen**een of meer kolommen met tekst die u wilt analyseren.

    U meerdere kolommen kiezen, maar deze moeten van het tekenreeksgegevenstype zijn.

    Omdat LDA een grote functiematrix uit de tekst maakt, analyseert u doorgaans één tekstkolom.

4. Typ **voor Aantal onderwerpen dat u wilt modelleren**een geheel getal tussen 1 en 1000 dat aangeeft hoeveel categorieën of onderwerpen u uit de invoertekst wilt afleiden.

    Standaard worden er 5 onderwerpen gemaakt.

5. Voor **N-grammen,** geef de maximale lengte van N-gram gegenereerd tijdens hashing.

    De standaardinstelling is 2, wat betekent dat zowel bigrammen als unigrammen worden gegenereerd.

6. Selecteer de optie **Normaliseren** om uitvoerwaarden om te zetten in waarschijnlijkheden. In plaats van de getransformeerde waarden als gehele getallen weer te geven, worden waarden in de uitvoer- en functiegegevensset als volgt getransformeerd:

    + Waarden in de gegevensset worden weergegeven `P(topic|document)`als een waarschijnlijkheid waarbij .

    + Waarden in de matrix van het functieonderwerp `P(word|topic)`worden weergegeven als een waarschijnlijkheid waarbij .

    > [!NOTE] 
    > In Azure Machine Learning designer (preview), omdat de bibliotheek die we gebaseerd, scikit-learn, niet langer ondersteuning voor ongenormaliseerde *doc_topic_distr* uitvoer van versie 0.19, dus in deze module, **Normalize** parameter kan alleen worden toegepast op **Feature Topic matrix** output, Getransformeerd **dataset** output is altijd genormaliseerd.

7. Selecteer de optie, **Alle opties weergeven**en stel deze in op TRUE als u deze wilt weergeven en stel vervolgens extra geavanceerde parameters in.

    Deze parameters zijn specifiek voor de scikit-learn implementatie van LDA. Er zijn een aantal goede tutorials over LDA in scikit-leren, evenals de officiële [scikit-learn document](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html).

    + **Rho parameter**. Geef een voorafgaande waarschijnlijkheid voor de spaarzaamheid van onderwerpdistributies. Komt overeen met `topic_word_prior` de parameter van sklearn. U zou de waarde 1 gebruiken als u verwacht dat de verdeling van woorden vlak is; d.w.z., worden alle woorden verondersteld gelijkwaarschijnlijk. Als u denkt dat de meeste woorden schaars lijken, u deze instellen op een veel lagere waarde.

    + **Alfaparameter**. Geef een eerdere waarschijnlijkheid op voor de sparsity van onderwerpgewichten per document.  Komt overeen met `doc_topic_prior` de parameter van sklearn.

    + **Geschat aantal documenten**. Typ een getal dat de beste schatting geeft van het aantal documenten (rijen) dat wordt verwerkt. Hierdoor kan de module een hashtabel van voldoende grootte toewijzen.  Komt overeen `total_samples` met de parameter in scikit-learn.

    + **Grootte van de partij**. Typ een getal dat aangeeft hoeveel rijen er in elke batch tekst naar LDA-model worden verzonden. Komt overeen `batch_size` met de parameter in scikit-learn.

    + **Initiële waarde van iteratie die wordt gebruikt in het leerupdateschema**. Geef de beginwaarde op die het leerpercentage voor vroege iteraties in online leren vermindert. Komt overeen `learning_offset` met de parameter in scikit-learn.

    + **Stroom toegepast op de iteratie tijdens updates**. Geef aan welk vermogen het aantal iteraties is toegepast om de leersnelheid tijdens online updates te bepalen. Komt overeen `learning_decay` met de parameter in scikit-learn.

    + **Aantal doorgangen over de gegevens**. Geef het maximum aantal keren op dat het algoritme over de gegevens wordt gecyclus. Komt overeen `max_iter` met de parameter in scikit-learn.

8. Selecteer de **optie, Bouw woordenboek van ngrammen** of **Build woordenboek van ngrams voorafgaand aan LDA**, als u wilt de n-gram lijst te maken in een eerste pass, voordat het classificeren van tekst.

    Als u het oorspronkelijke woordenboek vooraf maakt, u het woordenboek later gebruiken bij het bekijken van het model. Het is over het algemeen gemakkelijker om resultaten in kaart te brengen naar tekst in plaats van numerieke indexen. Het opslaan van het woordenboek duurt echter langer en gebruikt extra opslag.

9. Typ **voor Maximale grootte van ngramwoordenboek**het totale aantal rijen dat kan worden gemaakt in het n-gramwoordenboek.

    Deze optie is handig voor het regelen van de grootte van het woordenboek. Als het aantal ngrammen in de invoer echter groter is dan deze grootte, kunnen er botsingen optreden.

10. Verzend de pijplijn. De LDA-module gebruikt de stelling van Bayes om te bepalen welke onderwerpen aan afzonderlijke woorden kunnen worden gekoppeld. Woorden zijn niet uitsluitend gekoppeld aan onderwerpen of groepen; in plaats daarvan heeft elke n-gram een geleerde kans om geassocieerd te worden met een van de ontdekte klassen.

## <a name="results"></a>Resultaten

De module heeft twee uitgangen:

+ **Getransformeerde gegevensset**: bevat de invoertekst en een opgegeven aantal gedetecteerde categorieën, samen met de scores voor elk tekstvoorbeeld voor elke categorie.

+ **Functieonderwerpmatrix**: De meest linkse kolom bevat de uitgepakte tekstfunctie en er is een kolom voor elke categorie met de score voor die functie in die categorie.


### <a name="lda-transformation"></a>LDA-transformatie

Deze module wordt ook uitgevoerd met de *LDA-transformatie* die LDA toepast op de gegevensset.

U deze transformatie opslaan door de gegevensset te registreren onder het tabblad **Uitvoer+logboeken** in het rechterdeelvenster van de module en deze opnieuw te gebruiken voor andere gegevenssets. Dit kan handig zijn als u hebt getraind op een groot corpus en de coëfficiënten of categorieën wilt hergebruiken.

### <a name="refining-an-lda-model-or-results"></a>Een LDA-model of -resultaten verfijnen

Meestal u geen enkel LDA-model maken dat aan alle behoeften voldoet, en zelfs een model dat voor één taak is ontworpen, vereist mogelijk veel iteraties om de nauwkeurigheid te verbeteren. We raden u aan al deze methoden uit te proberen om uw model te verbeteren:

+ De modelparameters wijzigen
+ Visualisatie gebruiken om de resultaten te begrijpen
+ Het krijgen van de feedback van deskundigen op het gebied van het onderwerp om na te gaan of de gegenereerde onderwerpen nuttig zijn.

Kwalitatieve maatregelen kunnen ook nuttig zijn voor de beoordeling van de resultaten. Als u de resultaten van onderwerpmodellering wilt evalueren, u rekening houden met:

+ Nauwkeurigheid - Zijn soortgelijke items echt vergelijkbaar?
+ Diversiteit - Kan het model onderscheid maken tussen vergelijkbare items wanneer dat nodig is voor het zakelijke probleem?
+ Schaalbaarheid - Werkt het op een breed scala aan tekstcategorieën of alleen op een smal doeldomein?

De nauwkeurigheid van modellen op basis van LDA kan vaak worden verbeterd door natuurlijke taalverwerking te gebruiken om tekst schoon te maken, samen te vatten en te vereenvoudigen of te categoriseren. De volgende technieken, die allemaal worden ondersteund in Azure Machine Learning, kunnen bijvoorbeeld de nauwkeurigheid van de classificatie verbeteren:

+ Woordverwijdering stoppen

+ Case normalisatie

+ Lemmatization of stemming

+ Herkenning van tekeneenheden

Zie [Tekst vooraf verwerken](preprocess-text.md)voor meer informatie.

In de ontwerper u ook R- of Python-bibliotheken gebruiken voor tekstverwerking: [R Script uitvoeren,](execute-r-script.md) [Python Script uitvoeren](execute-python-script.md)



## <a name="technical-notes"></a>Technische notities

Deze sectie bevat implementatiedetails, tips en antwoorden op veelgestelde vragen.

### <a name="implementation-details"></a>Uitvoeringsdetails

Standaard worden de verdelingen van uitvoer voor getransformeerde gegevensset en feature-topic matrix genormaliseerd als waarschijnlijkheden.

+ De getransformeerde gegevensset wordt genormaliseerd als de voorwaardelijke waarschijnlijkheid van onderwerpen die een document krijgen. In dit geval is de som van elke rij gelijk aan 1.

+ De functie-onderwerp matrix wordt genormaliseerd als de voorwaardelijke waarschijnlijkheid van woorden gegeven een onderwerp. In dit geval is de som van elke kolom gelijk aan 1.

> [!TIP]
> Af en toe kan de module een leeg onderwerp retourneren, wat meestal wordt veroorzaakt door de pseudo-random initialisatie van het algoritme.  Als dit gebeurt, u proberen gerelateerde parameters te wijzigen, zoals de maximale grootte van het N-gramwoordenboek of het aantal bits dat u moet gebruiken voor het hashing van de functie.

### <a name="lda-and-topic-modeling"></a>LDA en onderwerpmodellering

Latente Dirichlet Allocation (LDA) wordt vaak gebruikt voor *content-based topic modeling*, wat in feite betekent dat categorieën leren van niet-geclassificeerde tekst. In content-based topic modeling is een onderwerp een verdeling over woorden.

Stel dat u een corpus van klantbeoordelingen hebt verstrekt dat veel, veel producten bevat. De tekst van beoordelingen die zijn ingediend door veel klanten in de tijd zou bevatten veel termen, waarvan sommige worden gebruikt in meerdere onderwerpen.

Een **onderwerp** dat wordt geïdentificeerd door het LDA-proces kan beoordelingen voor een individueel product A vertegenwoordigen of een groep productbeoordelingen vertegenwoordigen. Voor LDA is het onderwerp zelf slechts een waarschijnlijkheidsverdeling over de tijd voor een reeks woorden.

Termen zijn zelden exclusief voor een product, maar kunnen verwijzen naar andere producten, of algemene termen die van toepassing zijn op alles ("geweldig", "verschrikkelijk"). Andere termen kunnen lawaaiwoorden zijn.  Het is echter belangrijk om te begrijpen dat de LDA-methode niet beweert om alle woorden in het universum vast te leggen, of om te begrijpen hoe woorden gerelateerd zijn, afgezien van waarschijnlijkheden van co-optreden. Het kan alleen woorden groeperen die in het doeldomein zijn gebruikt.

Nadat de term-indexen zijn berekend, worden afzonderlijke rijen tekst vergeleken met behulp van een op afstand gebaseerde gelijkenismeting, om te bepalen of twee stukken tekst op elkaar lijken.  U bijvoorbeeld merken dat het product meerdere namen heeft die sterk gecorreleerd zijn. Of, je zou kunnen vinden dat sterk negatieve termen worden meestal geassocieerd met een bepaald product. U de gelijkenismaatregel zowel gebruiken om gerelateerde termen te identificeren als om aanbevelingen te maken.

###  <a name="module-parameters"></a>Moduleparameters

|Name|Type|Bereik|Optioneel|Standaard|Beschrijving|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Doelkolom(en)|Kolomselectie||Vereist|StringFunctie|Naam of index van doelkolom|  
|Aantal onderwerpen dat moet worden gemodelleerd|Geheel getal|[1;1000]|Vereist|5|Modelleer de documentverdeling op N-onderwerpen|  
|N-grammen|Geheel getal|[1;10]|Vereist|2|Bestelling van N-grammen gegenereerd tijdens hashing|  
|Normalize|Booleaans|Waar of onwaar|Vereist|waar|Normaliseer de uitvoer naar waarschijnlijkheden.  De getransformeerde gegevensset wordt P(onderwerp&#124;document) en de matrix van het functieonderwerp wordt P(word&#124;topic)|  
|Alle opties weergeven|Booleaans|Waar of onwaar|Vereist|False|Presenteert aanvullende parameters die specifiek zijn voor scikit-learn online LDA|  
|Rho, parameter|Drijvend|[0.00001;1.0]|Van toepassing wanneer het selectievakje **Alle opties weergeven** is ingeschakeld|0,01|Onderwerpwoord voorafgaande distributie|  
|Alfaparameter|Drijvend|[0.00001;1.0]|Van toepassing wanneer het selectievakje **Alle opties weergeven** is ingeschakeld|0,01|Voorafgaande distributie van documentonderwerp|  
|Geschat aantal documenten|Geheel getal|[1;int. MaxWaarde]|Van toepassing wanneer het selectievakje **Alle opties weergeven** is ingeschakeld|1000|Geschat aantal documenten (Komt overeen met total_samples parameter)|  
|Grootte van de partij|Geheel getal|[1;1024]|Van toepassing wanneer het selectievakje **Alle opties weergeven** is ingeschakeld|32|Grootte van de partij|  
|Initiële waarde van iteratie die wordt gebruikt in het updateschema voor leersnelheid|Geheel getal|[0;int. MaxWaarde]|Van toepassing wanneer het selectievakje **Alle opties weergeven** is ingeschakeld|0|Initiële waarde die leersnelheid voor vroege iteraties downweights. Komt overeen met de parameter learning_offset|  
|Stroom toegepast op de iteratie tijdens updates|Drijvend|[0.0;1.0]|Van toepassing wanneer het selectievakje **Alle opties weergeven** is ingeschakeld|0.5|Kracht toegepast op het aantal iteraties om de leersnelheid te controleren. Komt overeen met de parameter learning_decay |  
|Aantal trainingsiteraties|Geheel getal|[1;1024]|Van toepassing wanneer het selectievakje **Alle opties weergeven** is ingeschakeld|25|Aantal trainingsiteraties|  
|Het woordenboek van ngrams bouwen|Booleaans|Waar of onwaar|Van toepassing wanneer het selectievakje **Alle opties weergeven** *niet* is ingeschakeld|True|Bouwt een woordenboek van ngrammen voorafgaand aan het berekenen van LDA. Handig voor modelinspectie en -interpretatie|  
|Maximale grootte van ngramwoordenboek|Geheel getal|[1;int. MaxWaarde]|Van toepassing wanneer de optie **Het woordenboek van het bouwen van ngrammen** waar is|20.000|Maximale grootte van het ngrams woordenboek. Als het aantal tokens in de invoer deze grootte overschrijdt, kunnen er botsingen optreden|  
|Aantal bits dat moet worden gebruikt voor functiehashing|Geheel getal|[1;31]|Hiermee wordt van toepassing wanneer het selectievakje **Alle opties weergeven** *niet* is ingeschakeld en Het woordenboek van bouwen **van ngrammen** onwaar is|12|Aantal bits dat moet worden gebruikt voor functiehashing| 
|Het woordenboek van ngrams bouwen voorafgaand aan LDA|Booleaans|Waar of onwaar|Van toepassing wanneer het selectievakje **Alle opties weergeven** is ingeschakeld|True|Bouwt een woordenboek van ngrammen voorafgaand aan LDA. Handig voor modelinspectie en -interpretatie|  
|Maximum aantal ngrammen in woordenboek|Geheel getal|[1;int. MaxWaarde]|Hiermee wordt van toepassing wanneer het selectievakje **Alle opties weergeven** is ingeschakeld en de optie **Het woordenboek van Ngramten** maken waar is|20.000|Maximale grootte van het woordenboek. Als het aantal tokens in de invoer deze grootte overschrijdt, kunnen er botsingen optreden|  
|Aantal hashbits|Geheel getal|[1;31]|Hiermee wordt van toepassing wanneer het selectievakje **Alle opties weergeven** is ingeschakeld en de optie **Het woordenboek van Ngramten** maken is Onwaar|12|Aantal bits dat moet worden gebruikt tijdens functiehashing|   


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning.   
Zie [Uitzonderingen en foutcodes voor de ontwerper voor](designer-error-codes.md)een lijst met fouten die specifiek zijn voor de modules.
