---
title: Voorbeeld pijplijnen & gegevens sets
titleSuffix: Azure Machine Learning
description: Gebruik voor beelden in Azure Machine Learning Designer om uw machine learning pijp lijnen te starten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.openlocfilehash: 60f8f2593ef9c05fa905e9d3d6f6ba0901a14243
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983343"
---
# <a name="example-pipelines--datasets-for-azure-machine-learning-designer"></a>Voorbeeld pijplijnen & gegevens sets voor Azure Machine Learning Designer

Gebruik de ingebouwde voor beelden in Azure Machine Learning Designer om snel aan de slag te gaan met het bouwen van uw machine learning pijp lijnen. De GitHub- [opslag plaats](https://github.com/Azure/MachineLearningDesigner) van Azure machine learning Designer bevat gedetailleerde documentatie waarmee u een aantal algemene machine learning scenario's kunt begrijpen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://aka.ms/AMLFree).
* Een Azure Machine Learning-werk ruimte met de Enter prise-SKU.

## <a name="use-sample-pipelines"></a>Voorbeeld pijplijnen gebruiken

De ontwerp functie slaat een kopie van de voorbeeld pijplijnen op in uw studio-werk ruimte. U kunt de pijp lijn bewerken om deze aan uw behoeften aan te passen en deze als uw eigen toepassing op te slaan. Gebruik deze als uitgangs punt om uw projecten op de slag te brengen.

U kunt als volgt een voor beeld van een ontwerp gebruiken:

1. Meld u aan bij <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>en selecteer de werk ruimte waarmee u wilt werken.

1. Selecteer **ontwerp functie**.

1. Selecteer een voorbeeld pijplijn in het gedeelte **nieuwe pijp lijn** .

    Selecteer **meer voor beelden weer geven** voor een volledige lijst met voor beelden.

1. Als u een pijp lijn wilt uitvoeren, moet u eerst het standaard Compute-doel instellen om de pijp lijn uit te voeren.

   1. Selecteer in het deel venster **instellingen** rechts van het canvas de optie **reken doel selecteren**.

   1. Selecteer een bestaand Compute target in het dialoog venster dat wordt weer gegeven of maak een nieuw doel. Selecteer **Opslaan**.

   1. Selecteer **verzenden** boven aan het canvas om een pijplijn uitvoering te verzenden.

   Afhankelijk van de voorbeeld pijplijn en de berekenings instellingen kan het enige tijd duren voordat de uitvoering is voltooid. De standaard instellingen voor de berekening hebben een minimale knooppunt grootte van 0, wat betekent dat de ontwerp functie resources moet toewijzen na inactiviteit. Herhaalde pijplijn uitvoeringen nemen minder tijd in beslag, omdat de reken resources al zijn toegewezen. Daarnaast gebruikt de ontwerp functie in cache geplaatste resultaten voor elke module om de efficiëntie te verbeteren.


1. Nadat de pijp lijn is uitgevoerd, kunt u de pijp lijn bekijken en de uitvoer voor elke module bekijken voor meer informatie. Gebruik de volgende stappen om module-uitvoer weer te geven:

   1. Selecteer een module in het canvas.

   1. Selecteer **uitvoer en logboeken**in het detail venster van de module rechts van het canvas. Selecteer het pictogram](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) grafiek ![pictogram visualiseren om de resultaten van elke module te bekijken. 

   Gebruik de voor beelden als begin punten voor een aantal van de meest voorkomende machine learning scenario's.

## <a name="regression"></a>Regressie

Verken deze ingebouwde regressie voorbeelden.

| Voorbeeldtitel | Beschrijving | 
| --- | --- |
| [Voor beeld 1: regressie-auto-prijs voorspelling (basis)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Prijs van de auto voors pellen met behulp van lineaire regressie. |
| [Voor beeld 2: regressie-prijs voorspellingen voor auto (Geavanceerd)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | De prijzen van auto's voors pellen met behulp van het besluitvormings forest en de versterkte beslissings structuur regressors gevonden Vergelijk modellen om het beste algoritme te vinden.

## <a name="classification"></a>Classificatie

Verken deze ingebouwde classificatie voorbeelden. U vindt meer informatie over de voor beelden zonder documentatie koppelingen door de voor beelden te openen en in plaats daarvan de module opmerkingen weer te geven.

| Voorbeeldtitel | Beschrijving | 
| --- | --- |
| [Voor beeld 3: binaire classificatie met functie selectie-inkomen voor spelling](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Baten als hoog of laag voors pellen met behulp van een versterkte beslissings structuur met twee klassen. Gebruik Pearson-correlatie om onderdelen te selecteren.
| [Voor beeld 4: binaire classificatie met aangepast python-script-credit risico voor spelling](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Het classificeren van tegoed aanvragen als hoog of laag risico. Gebruik de script module python uitvoeren om uw gegevens te verlichten.
| [Voor beeld 5: binaire classificatie-relatie tussen klant en voor spelling](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Het klant verloop voors pellen met behulp van geboostte beslissings structuren van twee klasse. Gebruik SMOTE om een voor beeld van zuivere gegevens te berekenen.
| [Voor beeld 7: tekst classificatie-Wikipedia SP 500-gegevensset](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Bedrijfs typen classificeren vanuit Wikipedia-artikelen met een logistiek regressie van meer klasse. |
| Voor beeld 12: classificatie met meervoudige klassen-letter herkenning | Maak een ensemble van binaire classificaties om geschreven letters te classificeren. |

## <a name="recommender"></a>Aanbevelingsfunctie

Bekijk deze ingebouwde aanbevolen voor beelden. U vindt meer informatie over de voor beelden zonder documentatie koppelingen door de voor beelden te openen en in plaats daarvan de module opmerkingen weer te geven.

| Voorbeeldtitel | Beschrijving | 
| --- | --- |
| Voor beeld 10: aanbeveling-film classificatie tweets | Bouw een film aanbevolen engine van film titels en classificatie. |

## <a name="utility"></a>Utility

Meer informatie over de voor beelden van machine learning-hulpprogram ma's en-functies. U vindt meer informatie over de voor beelden zonder documentatie koppelingen door de voor beelden te openen en in plaats daarvan de module opmerkingen weer te geven.

| Voorbeeldtitel | Beschrijving | 
| --- | --- |
| [Voor beeld 6: aangepaste R-script gebruiken voor spelling vertraging](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| Voor beeld 8: Kruis validatie voor binaire classificatie-volwassenen inkomen voor spelling | Gebruik Kruis validatie om een binaire classificatie te maken voor volwassenen inkomen.
| Voor beeld 9: permutatie functie urgentie | Gebruik het belang van de permutatie functie om urgentie scores voor de test gegevensset te berekenen. 
| Voor beeld 11: para meters voor binaire classificatie afstemmen-volwassene baten voor spelling | Gebruik Tune model Hyper parameters om optimale Hyper parameters te vinden om een binaire classifier te bouwen. |

## <a name="datasets"></a>Gegevenssets

Wanneer u een nieuwe pijp lijn maakt in Azure Machine Learning Designer, zijn standaard een aantal voorbeeld gegevens sets opgenomen. Deze voorbeeld gegevens sets worden gebruikt door de voorbeeld pijplijnen in de start pagina van de ontwerp functie. 

De voorbeeld gegevens sets zijn beschikbaar onder de categorie voor**beelden** van **gegevens sets**-. U kunt dit vinden in het module palet links van het canvas in de ontwerp functie. U kunt elk van deze gegevens sets in uw eigen pijp lijn gebruiken door deze naar het canvas te slepen.

| Naam&nbsp;van gegevensset&nbsp;&nbsp;&nbsp;&nbsp;| Beschrijving van gegevensset |
|-------------|:--------------------|
| Gegevensset opbrengst binaire classificatie volwassene | Een subset van de 1994-optelling data base, met behulp van werk volwassenen in de leeftijd van 16 met een gecorrigeerde inkomsten index van > 100.<br/>**Gebruik**: classificeren mensen met behulp van demografische gegevens om te voors pellen of een persoon meer dan 50.000 per jaar verdient.<br/> **Verwant onderzoek**: Kohavi, R., Becker, B., (1996). [Icb machine learning-opslag plaats](https://archive.ics.uci.edu/ml). Irvine, CA: Universiteit van Californië, school van informatie en computer wetenschappen|
|Prijs gegevens auto Mobile (onbewerkt)|Informatie over auto's per merk en model, met inbegrip van de prijs, functies zoals het aantal flessen en MPG, evenals een verzekerings risico Score.<br/> De risico Score is in eerste instantie gekoppeld aan automatische prijs. De waarde wordt vervolgens aangepast voor het werkelijke risico in een proces dat wordt aangeduid met de vorm van een symbool. De waarde + 3 geeft aan dat het automatische risico is en dat de waarde-3 waarschijnlijk veilig is.<br/>**Gebruik**:</b> de risico Score door functies voors pellen met behulp van regressie of multidimensionale classificatie.<br/>**Verwant onderzoek**:</b> Schlimmer, J.C. (1987). [Icb machine learning-opslag plaats](https://archive.ics.uci.edu/ml). Irvine, CA: Universiteit van Californië, school van informatie en computer wetenschappen. |
| Gedeelde CRM verlangen-labels |Labels van de KDDe Cup 2009 Customer Relationship prediction Challenge ([orange_small_train_appetency. labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)).|
|CRM-verloop labels gedeeld|Labels van de KDDe Cup 2009 Customer Relationship prediction Challenge ([orange_small_train_churn. labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)).|
|Gedeelde CRM-gegevensset | Deze gegevens zijn afkomstig van de KDDe Cup 2009 Customer Relationship predictation Challenge ([orange_small_train. data. zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)). <br/>De gegevensset bevat 50.000-klanten van het Franse Telecom bedrijf oranje. Elke klant heeft 230 geanonimiseerd-functies, 190 met een numerieke en 40 categorische. De functies zijn zeer verspreid. |
|Gedeelde verkoop etiketten voor CRM|Labels van de KDDe Cup 2009 Customer Relationship prediction Challenge ([orange_large_train_upselling. labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)|
|Gegevens van vlieg vertraging|On-time prestatie gegevens van reizigers vlucht die zijn gemaakt op basis van de TranStats-gegevens verzameling van het Amerikaanse ministerie van Trans Port ([op tijd](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)).<br/>De gegevensset heeft betrekking op de periode van april tot en met 2013. Voordat u naar de ontwerp functie uploadt, is de gegevensset als volgt verwerkt: <br/>-De gegevensset is gefilterd om alleen de 70 drukste lucht havens in het continentale VS te bedekken <br/>-Geannuleerde vluchten zijn gelabeld als een vertraging van meer dan 15 minuten <br/>-Doorgestuurde vluchten zijn gefilterd <br/>-De volgende kolommen zijn geselecteerd: jaar, maand, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, geannuleerd|
|Duitse gegevensset voor credit card UCI|De Statlog-gegevensset ([Statlog + Duitsland + credit](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))Card) van de Icb's (Duitsland), met behulp van het bestand Duits. gegevens.<br/>De gegevensset classificeert mensen, zoals wordt beschreven door een set kenmerken, als laag of hoog risico. Elk voor beeld vertegenwoordigt een persoon. Er zijn 20 functies, zowel numeriek als categorische, en een binair label (de waarde voor het credit risico). Vermeldingen met een hoog credit bedrag hebben Label = 2, items met een laag krediet risico hebben label = 1. De kosten voor een slechtere classificatie van een voor beeld met een laag risico zijn 1, terwijl de kosten voor een slechtere voor beeld van een hoog risico worden geclassificeerd als laag 5.|
|IMDB-film titels|De gegevensset bevat informatie over films die zijn geclassificeerd in Twitter tweets: IMDB-film-ID, film naam, genre en productie jaar. De gegevensset bevat 17K-films. De gegevensset is geïntroduceerd in het papier ' S. Dooms, T. de PESSEMIER en L. Martens. MovieTweetings: een gegevensset voor film classificatie die is verzameld van Twitter. Workshop voor crowdsourcing en menselijke berekeningen voor aanbevolen systemen, CrowdRec op RecSys 2013. "|
|Film classificaties|De gegevensset is een uitgebreide versie van de Tweetings-gegevensset van de film. De gegevensset bevat 170K-classificaties voor films, geëxtraheerd uit goed gestructureerde tweets op Twitter. Elk exemplaar vertegenwoordigt een Tweet en is een tupel: gebruikers-ID, IMDB-film-ID, classificatie, tijds tempel, aantal favorieten voor deze Tweet en het aantal retweeten van deze Tweet. De gegevensset is beschikbaar gesteld door een. zei, S. DOOMS, B. Loni en D. Tikk voor aanbevolen systemen Challenge 2014.|
|Weer gegevensset|Op land gebaseerde weers waarnemingen van NOAA ([samengevoegde gegevens van 201304 tot 201310](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)).<br/>De weer gegevens zijn van toepassing op waarnemingen die zijn gemaakt van de weers stations van de lucht haven, voor de periode van april tot en met 2013. Voordat u naar de ontwerp functie uploadt, is de gegevensset als volgt verwerkt:    <br/> -Weer station-Id's zijn toegewezen aan de bijbehorende luchthaven-Id's    <br/> -Weer berichten die niet zijn gekoppeld aan de 70 drukste lucht havens zijn uitgefilterd    <br/> -De kolom date is gesplitst in de kolommen voor een jaar, maand en dag    <br/> -De volgende kolommen zijn geselecteerd: AirportID, jaar, maand, dag, tijd, tijd zone, SkyCondition, zicht baarheid, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, record type, HourlyPrecip, altimeter|
|Wikipedia SP 500-gegevensset|Gegevens worden afgeleid van Wikipedia (https://www.wikipedia.org/) op basis van artikelen van elke S&P 500-bedrijf, opgeslagen als XML-gegevens.    <br/>Voordat u naar de ontwerp functie uploadt, is de gegevensset als volgt verwerkt:    <br/> -Tekst inhoud voor elk specifiek bedrijf extra heren    <br/> -Wiki-opmaak verwijderen    <br/> -Niet-alfanumerieke tekens verwijderen    <br/> -Alle tekst naar kleine letters converteren    <br/> -Bekende bedrijfs categorieën zijn toegevoegd    <br/>Houd er rekening mee dat voor sommige bedrijven een artikel niet kan worden gevonden. het aantal records is dus kleiner dan 500.|


## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

Leer de basis beginselen van predictive analytics en machine learning met [de zelf studie: prijs van auto Mobile met de ontwerper](tutorial-designer-automobile-price-train-score.md)

