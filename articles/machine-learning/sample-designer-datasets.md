---
title: Voorbeeld gegevens sets gebruiken in Azure Machine Learning Designer
titleSuffix: Azure Machine Learning
description: Meer informatie over de voorbeeld gegevens sets die zijn opgenomen in Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 03/03/2020
ms.openlocfilehash: 7c5087a15210dff7c2a51aa5af38b8f7c26e28cc
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673472"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>De voorbeeld gegevens sets gebruiken in Azure Machine Learning Designer (preview)

Wanneer u een nieuwe pijp lijn maakt in Azure Machine Learning Designer (preview), zijn standaard een aantal voorbeeld gegevens sets opgenomen. Deze voorbeeld gegevens sets worden gebruikt door de voorbeeld pijplijnen in de start pagina van de ontwerp functie. 

De voorbeeld gegevens sets zijn beschikbaar onder de categorie **gegevens sets**-**samples** . U kunt dit vinden in het module palet links van het canvas in de ontwerp functie. U kunt elk van deze gegevens sets in uw eigen pijp lijn gebruiken door deze naar het canvas te slepen.

## <a name="datasets"></a>Gegevenssets


| Gegevensset&nbsp;naam&nbsp;&nbsp;&nbsp;&nbsp;| Beschrijving van gegevensset |
|-------------|:--------------------|
| Gegevensset opbrengst binaire classificatie volwassene | Een subset van de 1994-optelling data base, met behulp van werk volwassenen in de leeftijd van 16 met een gecorrigeerde inkomsten index van > 100.<br/>**Gebruik**: classificeren mensen met behulp van demografische gegevens om te voors pellen of een persoon meer dan 50.000 per jaar verdient.<br/> **Verwant onderzoek**: Kohavi, R., Becker, B., (1996). [Icb machine learning-opslag plaats](https://archive.ics.uci.edu/ml). Irvine, CA: Universiteit van Californië, school van informatie en computer wetenschappen|
|Prijs gegevens auto Mobile (onbewerkt)|Informatie over auto's per merk en model, met inbegrip van de prijs, functies zoals het aantal flessen en MPG, evenals een verzekerings risico Score.<br/> De risico Score is in eerste instantie gekoppeld aan automatische prijs. De waarde wordt vervolgens aangepast voor het werkelijke risico in een proces dat wordt aangeduid met de vorm van een symbool. De waarde + 3 geeft aan dat het automatische risico is en dat de waarde-3 waarschijnlijk veilig is.<br/>**Gebruik**:</b> de risico Score per functie te voors pellen met behulp van regressie of multidimensionale classificatie.<br/>**Verwant onderzoek**:</b> Schlimmer, J.C. (1987). [Icb machine learning-opslag plaats](https://archive.ics.uci.edu/ml). Irvine, CA: Universiteit van Californië, school van informatie en computer wetenschappen. |
| Gedeelde CRM verlangen-labels |Labels van de KDDe Cup 2009 Customer Relationship prediction Challenge ([orange_small_train_appetency. labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)).|
|CRM-verloop labels gedeeld|Labels van de KDDe Cup 2009 Customer Relationship prediction Challenge ([orange_small_train_churn. labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)).|
|Gedeelde CRM-gegevensset | Deze gegevens zijn afkomstig van de KDDe Cup 2009 Customer Relationship predictation Challenge ([orange_small_train. data. zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)). <br/>De gegevensset bevat 50.000-klanten van het Franse Telecom bedrijf oranje. Elke klant heeft 230 geanonimiseerd-functies, 190 met een numerieke en 40 categorische. De functies zijn zeer verspreid. |
|Gedeelde verkoop etiketten voor CRM|Labels van de KDDe Cup 2009 Customer Relationship prediction Challenge ([orange_large_train_upselling. labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)|
|Gegevens van vlieg vertraging|On-time prestatie gegevens van reizigers vlucht die zijn gemaakt op basis van de TranStats-gegevens verzameling van het Amerikaanse ministerie van Trans Port ([op tijd](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)).<br/>De gegevensset heeft betrekking op de periode van april tot en met 2013. Voordat u naar de ontwerp functie uploadt, is de gegevensset als volgt verwerkt: <br/>-De gegevensset is gefilterd om alleen de 70 drukste lucht havens in het continentale VS te bedekken <br/>-Geannuleerde vluchten zijn gelabeld als een vertraging van meer dan 15 minuten <br/>-Doorgestuurde vluchten zijn gefilterd <br/>-De volgende kolommen zijn geselecteerd: jaar, maand, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, geannuleerd|
|Duitse gegevensset voor credit card UCI|De Statlog-gegevensset ([Statlog + Duitsland + credit](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))Card) van de Icb's (Duitsland), met behulp van het bestand Duits. gegevens.<br/>De gegevensset classificeert mensen, zoals wordt beschreven door een set kenmerken, als laag of hoog risico. Elk voor beeld vertegenwoordigt een persoon. Er zijn 20 functies, zowel numeriek als categorische, en een binair label (de waarde voor het credit risico). Vermeldingen met een hoog credit bedrag hebben Label = 2, items met een laag krediet risico hebben label = 1. De kosten voor een slechtere classificatie van een voor beeld met een laag risico zijn 1, terwijl de kosten voor een slechtere voor beeld van een hoog risico worden geclassificeerd als laag 5.|
|IMDB-film titels|De gegevensset bevat informatie over films die zijn geclassificeerd in Twitter tweets: IMDB-film-ID, film naam, genre en productie jaar. De gegevensset bevat 17K-films. De gegevensset is geïntroduceerd in het papier ' S. Dooms, T. de PESSEMIER en L. Martens. MovieTweetings: een gegevensset voor film classificatie die is verzameld van Twitter. Workshop voor crowdsourcing en menselijke berekeningen voor aanbevolen systemen, CrowdRec op RecSys 2013. "|
|Film classificaties|De gegevensset is een uitgebreide versie van de Tweetings-gegevensset van de film. De gegevensset bevat 170K-classificaties voor films, geëxtraheerd uit goed gestructureerde tweets op Twitter. Elk exemplaar vertegenwoordigt een Tweet en is een tupel: gebruikers-ID, IMDB-film-ID, classificatie, tijds tempel, aantal favorieten voor deze Tweet en het aantal retweeten van deze Tweet. De gegevensset is beschikbaar gesteld door een. zei, S. DOOMS, B. Loni en D. Tikk voor aanbevolen systemen Challenge 2014.|
|Weer gegevensset|Op land gebaseerde weers waarnemingen van NOAA ([samengevoegde gegevens van 201304 tot 201310](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)).<br/>De weer gegevens zijn van toepassing op waarnemingen die zijn gemaakt van de weers stations van de lucht haven, voor de periode van april tot en met 2013. Voordat u naar de ontwerp functie uploadt, is de gegevensset als volgt verwerkt:    <br/> -Weer station-Id's zijn toegewezen aan de bijbehorende luchthaven-Id's    <br/> -Weer berichten die niet zijn gekoppeld aan de 70 drukste lucht havens zijn uitgefilterd    <br/> -De kolom date is gesplitst in de kolommen voor een jaar, maand en dag    <br/> -De volgende kolommen zijn geselecteerd: AirportID, jaar, maand, dag, tijd, time zone, SkyCondition, visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, record type, HourlyPrecip, altimeter|
|Wikipedia SP 500-gegevensset|Gegevens worden afgeleid van Wikipedia (https://www.wikipedia.org/) op basis van artikelen van elke S & P 500-bedrijf, opgeslagen als XML-gegevens.    <br/>Voordat u naar de ontwerp functie uploadt, is de gegevensset als volgt verwerkt:    <br/> -Tekst inhoud voor elk specifiek bedrijf extra heren    <br/> -Wiki-opmaak verwijderen    <br/> -Niet-alfanumerieke tekens verwijderen    <br/> -Alle tekst naar kleine letters converteren    <br/> -Bekende bedrijfs categorieën zijn toegevoegd    <br/>Houd er rekening mee dat voor sommige bedrijven een artikel niet kan worden gevonden. het aantal records is dus kleiner dan 500.|

## <a name="next-steps"></a>Volgende stappen

* Leer de basis beginselen van predictive analytics en machine learning met [de zelf studie: prijs van auto Mobile met de ontwerper](tutorial-designer-automobile-price-train-score.md)

* [Import data](./algorithm-module-reference/import-data.md) -module gebruiken om voorbeeld gegevens sets te importeren
