---
title: Voorbeeldgegevenssets gebruiken in Azure Machine Learning-ontwerper
titleSuffix: Azure Machine Learning
description: Meer informatie over de voorbeeldgegevenssets die zijn opgenomen in Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 03/03/2020
ms.openlocfilehash: 943e23aa9d83df33bbe636ce7aee62e6e5a2950e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79037310"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>De voorbeeldgegevenssets gebruiken in Azure Machine Learning designer (voorbeeld)

Wanneer u een nieuwe pijplijn maakt in Azure Machine Learning-ontwerper (voorbeeld), worden standaard een aantal voorbeeldgegevenssets opgenomen. Deze voorbeeldgegevenssets worden gebruikt door de voorbeeldpijplijnen in de startpagina van de ontwerper. 

De voorbeeldgegevenssets zijn beschikbaar onder de categorie **Gegevenssets**-**Voorbeelden.** U vindt dit in het modulepalet links van het canvas in de ontwerper. U een van deze gegevenssets in uw eigen pijplijn gebruiken door deze naar het canvas te slepen.

## <a name="datasets"></a>Gegevenssets


| Naam&nbsp;van gegevensset&nbsp;&nbsp;&nbsp;&nbsp;| Beschrijving van gegevensset |
|-------------|:--------------------|
| Gegevensgegevensgegevens van binaire classificatie van inkomsten voor volwassenen | Een subset van de volkstellingsdatabase van 1994, met werkende volwassenen ouder dan 16 jaar met een aangepaste inkomensindex van > 100.<br/>**Gebruik**: Classificeer mensen met behulp van demografische gegevens om te voorspellen of een persoon meer dan 50.000 per jaar verdient.<br/> **Verwant Onderzoek**: Kohavi, R., Becker, B., (1996). [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml). Irvine, CA: Universiteit van Californië, School of Information and Computer Science|
|Auto prijsgegevens (Raw)|Informatie over auto's per merk en model, inclusief de prijs, functies zoals het aantal cilinders en MPG, evenals een verzekeringsrisicoscore.<br/> De risicoscore wordt in eerste instantie geassocieerd met de automatische prijs. Het wordt vervolgens aangepast voor het werkelijke risico in een proces dat actuarissen als symbool kent. Een waarde van +3 geeft aan dat de auto riskant is en een waarde van -3 dat deze waarschijnlijk veilig is.<br/>**Gebruik**</b> : Voorspel de risicoscore op basis van functies, met regressie of multivariate classificatie.<br/>**Gerelateerd**Onderzoek</b> : Schlimmer, J.C. (1987). [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml). Irvine, CA: Universiteit van Californië, School of Information and Computer Science. |
| CRM-appetencylabels gedeeld |Labels van de KDD Cup 2009 customer relationship prediction challenge[(orange_small_train_appetency.labels).](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)|
|CRM-churn-labels gedeeld|Labels van de KDD Cup 2009 customer relationship prediction challenge[(orange_small_train_churn.labels).](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)|
|CRM-gegevensset gedeeld | Deze gegevens zijn afkomstig van de KDD Cup 2009 customer relationship prediction challenge[(orange_small_train.data.zip).](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip) <br/>De dataset bevat 50K klanten van het Franse telecombedrijf Orange. Elke klant heeft 230 geanonimiseerde functies, waarvan 190 numeriek en 40 categorisch. De functies zijn zeer schaars. |
|CRM Upselling Labels gedeeld|Labels van de KDD Cup 2009 customer relationship prediction challenge[(orange_large_train_upselling.labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)|
|Vluchtvertragingen Gegevens|Passagiersvlucht op tijd prestatiegegevens afkomstig van de TranStats data verzameling van het Amerikaanse Ministerie van Transport ([On-Time](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)).<br/>De gegevensset bestrijkt de periode april-oktober 2013. Voordat u naar de ontwerper wordt geüpload, werd de gegevensset als volgt verwerkt: <br/>- De dataset werd gefilterd om alleen de 70 drukste luchthavens in het vasteland van de VS te dekken <br/>- Geannuleerde vluchten werden gelabeld als vertraagd door meer dan 15 minuten <br/>- Omgeleide vluchten werden uitgefilterd <br/>- De volgende kolommen zijn geselecteerd: Jaar, Maand, DagofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Geannuleerd|
|Duitse credit card UCI-gegevensset|De UCI Statlog (German Credit Card) dataset ([Statlog+German+Credit+Data),](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))met behulp van het duitse.data bestand.<br/>De gegevensset classificeert mensen, beschreven door een reeks kenmerken, als lage of hoge kredietrisico's. Elk voorbeeld vertegenwoordigt een persoon. Er zijn 20 functies, zowel numeriek als categorisch, en een binair label (de kredietrisicowaarde). De inschrijvingen met een hoog kredietrisico hebben label = 2, lage kredietrisicoposten hebben label = 1. De kosten van het verkeerd classificeren van een voorbeeld met een laag risico als hoog is 1, terwijl de kosten van het verkeerd classificeren van een hoog risico voorbeeld als laag is 5.|
|IMDB-filmtitels|De gegevensset bevat informatie over films die zijn beoordeeld in Twitter-tweets: IMDB-film-ID, filmnaam, genre en productiejaar. Er zijn 17K films in de dataset. De dataset werd geïntroduceerd in de paper "S. Dooms, T. De Pessemier en L. Martens. MovieTweetings: een Movie Rating Dataset verzameld van Twitter. Workshop over Crowdsourcing en Human Computation for Recommender Systems, CrowdRec op RecSys 2013."|
|Filmbeoordelingen|De gegevensset is een uitgebreide versie van de gegevensset FilmTweetings. De dataset heeft 170K ratings voor films, gewonnen uit goed gestructureerde tweets op Twitter. Elke instantie vertegenwoordigt een tweet en is een tuple: user ID, IMDB movie ID, rating, timestamp, aantal favorieten voor deze tweet, en het aantal retweets van deze tweet. De dataset werd beschikbaar gesteld door A. Said, S. Dooms, B. Loni en D. Tikk voor Recommender Systems Challenge 2014.|
|Weergegevensset|Uurgebonden weerwaarnemingen van NOAA[(samengevoegde gegevens van 201304 tot 201310).](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)<br/>De weersgegevens hebben betrekking op waarnemingen van weerstations op luchthavens, die betrekking hebben op de periode april-oktober 2013. Voordat u naar de ontwerper wordt geüpload, werd de gegevensset als volgt verwerkt:    <br/> - Weerstation-iD's werden in kaart gebracht aan overeenkomstige luchthaven-geïdentificeerden    <br/> - Weerstations die niet zijn gekoppeld aan de 70 drukste luchthavens werden uitgefilterd    <br/> - De kolom Datum is opgesplitst in afzonderlijke kolommen voor jaar, maand en dag    <br/> - De volgende kolommen werden geselecteerd: AirportID, Jaar, Maand, Dag, Tijd, Tijdzone, SkyCondition, Zichtbaarheid, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DauwpuntCelsius, RelativeHumidity, Windsnelheid, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter|
|Wikipedia SP 500-gegevensset|Gegevens zijn afgeleidhttps://www.wikipedia.org/) van Wikipedia ( gebaseerd op artikelen van elk S&P 500 bedrijf, opgeslagen als XML-gegevens.    <br/>Voordat u naar de ontwerper wordt geüpload, werd de gegevensset als volgt verwerkt:    <br/> - Tekstinhoud extraheren voor elk specifiek bedrijf    <br/> - Wikiopmaak verwijderen    <br/> - Niet-alfanumerieke tekens verwijderen    <br/> - Alle tekst converteren naar kleine letters    <br/> - Bekende bedrijfscategorieën zijn toegevoegd    <br/>Houd er rekening mee dat voor sommige bedrijven een artikel niet kon worden gevonden, dus het aantal records is minder dan 500.|

## <a name="next-steps"></a>Volgende stappen

* Leer de basisprincipes van predictive analytics en machine learning met [Tutorial: Predict auto prijs with the designer](tutorial-designer-automobile-price-train-score.md)

* Meer informatie over het aanpassen van bestaande [designervoorbeelden](samples-designer.md) om ze aan te passen aan uw behoeften.
