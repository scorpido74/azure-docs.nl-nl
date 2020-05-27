---
title: Woord naar vector converteren
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van drie beschik bare Word2Vec-modellen voor het extra heren van een vocabulaire en de bijbehorende woord insluitingen van een verzameling tekst.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: e0e796b75690bcacc6be8ef29b8b490c7faa40af
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853769"
---
# <a name="convert-word-to-vector"></a>Woord naar vector converteren

In dit artikel wordt beschreven hoe u de module **woord converteren naar vector** in azure machine learning Designer (preview) kunt gebruiken om verschillende Word2Vec modellen (Word2Vec, FastText, ondersteunt vooraf getrainde model) toe te passen op de verzameling van de tekst die u als invoer hebt opgegeven en een vocabulaire met woorden insluitingen te genereren.

Deze module maakt gebruik van de Gensim-bibliotheek. Ga voor meer informatie over Gensim naar de [officiële website](https://radimrehurek.com/gensim/apiref.html) die zelf studies en uitleg over algoritmen bevat.

### <a name="more-about-convert-word-to-vector"></a>Meer informatie over het converteren van woord naar vector

Over het algemeen is het converteren van Word naar vector, of Word vectorization, een proces voor de verwerking van natuurlijke taal, dat gebruikmaakt van taal modellen of technieken voor het toewijzen van woorden in een vector ruimte, dat wil zeggen, om elk woord te vertegenwoordigen door een vector van reële getallen en ondertussen, kunnen woorden met vergelijk bare betekeniss een vergelijk bare weer gave hebben.

Word-insluitingen kunnen worden gebruikt als eerste invoer voor NLP downstream-taken, zoals tekst classificatie, analyse van sentiment, enzovoort.

In deze module hebben we met verschillende technologieën voor het insluiten van woorden drie veelgebruikte methoden geïmplementeerd, waaronder twee online trainings modellen, Word2Vec en FastText, en één vooraf getraind model, ondersteunt-wiki-gigaword-100. Online trainings modellen worden getraind op uw invoer gegevens, terwijl vooraf getrainde modellen on-line worden getraind op een groter tekst verzameling, (bijvoorbeeld Wikipedia, Google News) bevat meestal ongeveer 100.000.000.000 woorden, en woord insluiting blijft constant tijdens het woord vectorization. Vooraf getrainde woord modellen bieden voor delen, zoals gereduceerde trainings tijd, betere woord vectoren die zijn gecodeerd en verbeterde algehele prestaties.

+ Word2Vec is een van de populairste technieken voor het leren van woord insluitingen met een beschik bare Neural-netwerk, theorie wordt in dit document besproken, dat beschikbaar is als PDF-down load: [efficiënte schatting van woord represen in vector ruimte, Mikolov, Tomas, et al](https://arxiv.org/pdf/1301.3781.pdf). De implementatie in deze module is gebaseerd op de [gensim-bibliotheek voor Word2Vec](https://radimrehurek.com/gensim/models/word2vec.html).

+ FastText theorie wordt beschreven in dit artikel, dat beschikbaar is als PDF-down load: [verrijkt het downloaden van woord vectoren met subwoordgegevens, Bojanowski, Piotr, et al](https://arxiv.org/pdf/1607.04606.pdf). De implementatie in deze module is gebaseerd op de [gensim-bibliotheek voor FastText](https://radimrehurek.com/gensim/models/fasttext.html).

+ Ondersteunt vooraf getraind model: ondersteunt-wiki-gigaword-100, is een verzameling van vooraf getrainde vectoren op basis van een Wikipedia-tekst verzameling, met daarin 5.6 B-tokens en 400K niet-hoofdletter woorden, PDF is beschikbaar: [ondersteunt: globale vectoren voor de weer gave van woorden](https://nlp.stanford.edu/pubs/glove.pdf).

## <a name="how-to-configure-convert-word-to-vector"></a>Conversie van woord naar vector configureren

Voor deze module is een gegevensset vereist die een kolom met tekst bevat. voorbewerkte tekst is beter.

1. Voeg de module **woord naar vector converteren** toe aan de pijp lijn.

2. Geef als invoer voor de module een gegevensset op die een of meer tekst kolommen bevat.

3. Kies bij **doel kolom**slechts één kolom die de tekst bevat die moet worden verwerkt.

    In het algemeen, omdat deze module een woorden lijst maakt op basis van tekst, verschilt de inhoud van verschillende kolommen, wat leidt tot een andere woordenlijst inhoud, daarom accepteert module slechts één doel kolom.

4. Voor **Word2Vec-strategie**kiest u uit `GloVe pretrained English Model` , `Gensim Word2Vec` en `Gensim FastText` .

5. Als de **Word2Vec-strategie** is `Gensim Word2Vec` of `Gensim FastText` :

    + **Word2Vec-trainings algoritme**. Kies uit `Skip_gram` en `CBOW` . Het verschil wordt in het oorspronkelijke [document](https://arxiv.org/pdf/1301.3781.pdf)geïntroduceerd.

        Standaard methode is `Skip_gram` .

    + **Lengte van het insluiten van woorden**. De dimensionaliteit van de woord vectoren opgeven. Komt overeen met de `size` para meter in gensim.

        De standaard embedding_size is 100.

    + **Grootte van het context venster**. De maximale afstand opgeven tussen het woord dat wordt voorspeld en het huidige woord. Komt overeen met de `window` para meter in gensim.

        De standaard venster grootte is 5.

    + **Aantal epoches**. Geef het aantal epoches (iteraties) op voor de verzameling. Komt overeen met de `iter` para meter in gensim.

        Standaard aantal epoches is 5.

6. Voor **maximale woordenlijst grootte**geeft u het maximum aantal woorden op dat is gegenereerd op basis van een vocabulaire.

    Als er meer unieke woorden zijn dan dit, haalt u de voor vallen vervolgens op.

    De standaard woordenlijst grootte is 10000.

7. Voor het **minimum aantal**woorden moet u een minimum aantal woorden opgeven, waardoor de module alle woorden negeert die een frequentie hebben die lager is dan deze waarde.

    De standaard waarde is 5.

8. Verzend de pijp lijn.

## <a name="examples"></a>Voorbeelden

De module heeft één uitvoer:

+ **Vocabulaire with Embedding**: bevat de gegenereerde vocabulaire, samen met de insluiting van elk woord, één dimensie neemt één kolom in beslag.

### <a name="result-examples"></a>Voor beelden van resultaten

Om te laten zien hoe de module **woord converteren naar vector** werkt, wordt in het volgende voor beeld deze module toegepast met de standaard instellingen voor de voor verwerking van de gegevensset van de voorverwerkte Wikipedia-SP 500 in azure machine learning (preview).

#### <a name="source-dataset"></a>Brongegevensset

De gegevensset bevat een kolom categorie, evenals de volledige tekst die is opgehaald uit Wikipedia. In deze tabel worden slechts enkele representatieve voor beelden weer gegeven.

|tekst|
|----------|
|Nasdaq 100 component s p 500 component Foundation oprichter locatie City Apple Campus 1 oneindige lus straat Cupertino Californië Cupertino Californië locatie land Verenigde Staten...|
|BR Nasdaq 100 Nasdaq 100 component BR s p 500 s p 500 component Industry computer Software Foundation BR oprichter Charles Geschke BR John Warnock locatie Adobe Systems...|
|s p 500 s p 500 component Industry automobiel automobiel van de 1908 2009 onderneming voorafgaande aan de opvolgende taak...|
|s p 500 s p 500 component Industry conglomeraat Company conglomeraat Foundation oprichter locatie City Fairfield Connecticut Fairfield Connecticut locatie land USA gebied...|
|BR s p 500 s p 500 component Foundation 1903 oprichter William s Harley BR Arthur Davidson Harley Davidson oprichter Arthur Davidson BR Walter Davidson BR William a Davidson location...|

#### <a name="output-vocabulary-with-embeddings"></a>Uitvoer woordenlijst met insluitingen

De volgende tabel bevat de uitvoer van deze module voor het maken van een Wikipedia SP 500-gegevensset als invoer. De meest linkse kolom geeft de woorden lijst aan, de insluit vector wordt vertegenwoordigd door de waarden van de resterende kolommen in dezelfde rij.

|Vocabulaire|Dimmen 0 insluiten|DIMM 1 insluiten|DIMM 2 insluiten|Dimensie 3 insluiten|DIMM 4 insluiten|DIMM 5 insluiten|...|Dim-99 insluiten|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|nasdaq|-0,375865|0,609234|0,812797|-0,002236|0,319071|-0,591986|...|0,364276
|component|0,081302|0,40001|0,121803|0,108181|0,043651|-0,091452|...|0,636587
|s|-0,34355|-0,037092|-0,012167|0,151542|0,601019|0,084501|...|0,149419
|p|-0,133407|0,073244|0,170396|0,326706|0,213463|-0,700355|...|0,530901
onderkant|-0,166819|0,10883|-0,07933|-0,073753|0,262137|0,045725|...|0,27487
oprichter|-0,297408|0,493067|0,316709|-0,031651|0,455416|-0,284208|...|0,22798
location|-0,375213|0,461229|0,310698|0,213465|0,200092|0,314288|...|0,14228
city|-0,460828|0,505516|-0,074294|-0,00639|0,116545|0,494368|...|-0,2403
Apple|0,05779|0,672657|0,597267|-0,898889|0,099901|0,11833|...|0,4636
Campus|-0,281835|0,29312|0,106966|-0,031385|0,100777|-0,061452|...|0,05978
onbeperkt|-0,263074|0,245753|0,07058|-0,164666|0,162857|-0,027345|...|-0,0525
lus|-0,391421|0,52366|0,141503|-0,105423|0,084503|-0,018424|...|-0,0521

In dit voor beeld hebben we de standaard waarde gebruikt `Gensim Word2Vec` als de **Word2Vec-strategie**, het **trainings algoritme** `Skip-gram` , de **lengte van het insluiten van woorden** is 100, daarom hebben we 100 het insluiten van kolommen.

## <a name="technical-notes"></a>Technische opmerkingen

Deze sectie bevat tips en antwoorden op veelgestelde vragen.

+ Verschil tussen online Train en vooraf getraind model

    In dit **voor woord omzetten in een vector module**hebben we drie verschillende strategieën gegeven, twee online trainings modellen en één vooraf getraind model. Het online trainings model gebruikt uw invoer-gegevensset als trainings gegevens, genereert vocabulaire-en woord vectoren tijdens de training, terwijl vooraf getraind model al is getraind door veel grotere tekst verzameling zoals Wikipedia of Twitter-tekst. Daarom is het vooraf getrainde model eigenlijk een verzameling (woord, Embedding).  

    Als ondersteunt vooraf getraind model is gekozen als een vectorization-strategie, wordt een woorden lijst van de invoer-gegevensset samen vatting en wordt er een insluitings vector gegenereerd voor elk woord van het vooraf getrainde model, zonder online training, het gebruik van vooraf getrainde modellen zou kunnen besparen en een betere prestaties hebben, met name wanneer de grootte van de gegevensset relatief klein is.

+ Grootte insluiten

    Over het algemeen is de lengte van het insluiten van woorden ingesteld op een paar honderd (bijvoorbeeld 100, 200, 300) om goede prestaties te verzorgen, omdat kleine, insluitings grootte kleine vector ruimte kan veroorzaken, waardoor conflicten kunnen ontstaan bij het insluiten van woorden.  

    Voor vooraf getrainde modellen is de lengte van woord insluitingen vast, in deze implementatie is het insluiten van de grootte van ondersteunt-wiki-gigaword-100 100.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

Zie [machine learning fout codes](designer-error-codes.md)voor een lijst met fouten die specifiek zijn voor de Designer-modules (preview-versie).
