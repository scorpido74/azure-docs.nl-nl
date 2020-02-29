---
title: De voorbeeld gegevens sets gebruiken
titleSuffix: Azure Machine Learning
description: Beschrijvingen van de gegevens sets die worden gebruikt in voorbeeld modellen die zijn opgenomen in Machine Learning Designer. U kunt deze voorbeeld gegevens sets gebruiken voor uw pijp lijnen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 14e514f0025d474b3afb45524753583b7c2e8a7d
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165060"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>De voorbeeld gegevens sets gebruiken in Azure Machine Learning Designer (preview)

Wanneer u een nieuwe pijp lijn maakt in Azure Machine Learning Designer (preview), zijn standaard een aantal voorbeeld gegevens sets opgenomen. Veel van deze voorbeeld gegevens sets worden gebruikt door de voorbeeld modellen in de start pagina van de ontwerp functie. Anderen zijn opgenomen als voor beelden van verschillende typen gegevens die doorgaans worden gebruikt in machine learning.

Sommige van deze gegevens sets zijn beschikbaar in Azure Blob-opslag. De volgende tabel bevat een directe koppeling voor deze gegevens sets. U kunt deze gegevens sets in uw pijp lijnen gebruiken met behulp van de module [import data](./algorithm-module-reference/import-data.md) .

De rest van deze voorbeeld gegevens sets zijn beschikbaar onder de categorie **gegevens sets**-**samples** . U kunt dit vinden in het module palet links van het canvas in de ontwerp functie. U kunt elk van deze gegevens sets in uw eigen pijp lijn gebruiken door deze naar het canvas te slepen.

## <a name="datasets"></a>Gegevenssets

<table>

<tr>
  <th>Naam van de gegevensset</th>
  <th>Beschrijving van gegevensset</th>
</tr>

<tr>
  <td>Gegevensset opbrengst binaire classificatie volwassene</td>
  <td>
Een subset van de 1994-optelling data base, met behulp van werk volwassenen in de leeftijd van 16 met een gecorrigeerde inkomsten index van > 100.
<p></p>
<b>Gebruik:</b> Classificeer gebruikers met demografische gegevens om te voors pellen of een persoon meer dan 50.000 een jaar verdient.
<p></p>
<b>Verwant onderzoek:</b> Kohavi, R., Becker, B., (1996). <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>van ICB machine learning-opslag. Irvine, CA: Universiteit van Californië, school van informatie en computer wetenschappen </td>
</tr>

<tr>
  <td>Prijs gegevens auto Mobile (onbewerkt)</td>
  <td>
Informatie over auto's per merk en model, met inbegrip van de prijs, functies zoals het aantal flessen en MPG, evenals een verzekerings risico Score.
<p></p>
De risico Score is in eerste instantie gekoppeld aan automatische prijs. De waarde wordt vervolgens aangepast voor het werkelijke risico in een proces dat wordt aangeduid met de vorm van een symbool. De waarde + 3 geeft aan dat het automatische risico is en dat de waarde-3 waarschijnlijk veilig is.
<p></p>
<b>Gebruik:</b> De risico Score door functies te voors pellen met behulp van regressie of multidimensionale classificatie. 
<p></p>
<b>Verwant onderzoek:</b> Schlimmer, J.C. (1987). <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>van ICB machine learning-opslag. Irvine, CA: Universiteit van Californië, school van informatie en computer wetenschappen </td>
</tr>


<tr>
  <td>Gedeelde CRM verlangen-labels</td>
  <td>
Labels van de KDDe Cup 2009 Customer Relationship prediction Challenge (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency. labels</a>).
  </td>
</tr>

<tr>
  <td>CRM-verloop labels gedeeld</td>
  <td>
Labels van de KDDe Cup 2009 Customer Relationship prediction Challenge (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn. labels</a>).
  </td>
</tr>

<tr>
  <td>Gedeelde CRM-gegevensset</td>
  <td>
Deze gegevens zijn afkomstig van de KDDe Cup 2009 Customer Relationship predictation Challenge (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train. data. zip</a>).
<p></p>
De gegevensset bevat 50.000-klanten van het Franse Telecom bedrijf oranje. Elke klant heeft 230 geanonimiseerd-functies, 190 met een numerieke en 40 categorische. De functies zijn zeer verspreid.
  </td>
</tr>

<tr>
  <td>Gedeelde verkoop etiketten voor CRM</td>
  <td>
Labels van de KDDe Cup 2009 Customer Relationship prediction Challenge (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling. labels</a>).
  </td>
</tr>

<tr>
  <td>Gegevens van vlieg vertraging</td>
  <td>
On-time prestatie gegevens van reizigers vlucht die zijn gemaakt op basis van de TranStats-gegevens verzameling van het Amerikaanse ministerie van Trans Port (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">op tijd</a>).
<p></p>
De gegevensset heeft betrekking op de periode van april tot en met 2013. Voordat u naar de ontwerp functie uploadt, is de gegevensset als volgt verwerkt:
<ul>
  <li>De gegevensset is gefilterd op alleen de 70 drukste lucht havens in het continentale VS</li>
  <li>Geannuleerde vluchten zijn gelabeld met meer dan 15 minuten.</li>
  <li>Doorgevoerde vluchten zijn gefilterd</li>
  <li>De volgende kolommen zijn geselecteerd: jaar, maand, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, geannuleerd</li>
</ul>
</td>
</tr>

<tr>
  <td>Duitse gegevensset voor credit card UCI</td>
  <td>
De Statlog-gegevensset (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + Duitsland + credit</a>Card) van de Icb's (Duitsland), met behulp van het bestand Duits. gegevens.
<p></p>
De gegevensset classificeert mensen, zoals wordt beschreven door een set kenmerken, als laag of hoog risico. Elk voor beeld vertegenwoordigt een persoon. Er zijn 20 functies, zowel numeriek als categorische, en een binair label (de waarde voor het credit risico). Vermeldingen met een hoog credit bedrag hebben Label = 2, items met een laag krediet risico hebben label = 1. De kosten voor een slechtere classificatie van een voor beeld met een laag risico zijn 1, terwijl de kosten voor een slechtere voor beeld van een hoog risico worden geclassificeerd als laag 5.
  </td>
</tr>

<tr>
  <td>IMDB-film titels</td>
  <td>
De gegevensset bevat informatie over films die zijn geclassificeerd in Twitter tweets: IMDB-film-ID, film naam, genre en productie jaar. De gegevensset bevat 17K-films. De gegevensset is geïntroduceerd in het papier ' S. Dooms, T. de PESSEMIER en L. Martens. MovieTweetings: een gegevensset voor film classificatie die is verzameld van Twitter. Workshop voor crowdsourcing en menselijke berekeningen voor aanbevolen systemen, CrowdRec op RecSys 2013. "
  </td>
</tr>

<tr>
  <td>Film classificaties</td>
  <td>
De gegevensset is een uitgebreide versie van de Tweetings-gegevensset van de film. De gegevensset bevat 170K-classificaties voor films, geëxtraheerd uit goed gestructureerde tweets op Twitter. Elk exemplaar vertegenwoordigt een Tweet en is een tupel: gebruikers-ID, IMDB-film-ID, classificatie, tijds tempel, aantal favorieten voor deze Tweet en het aantal retweeten van deze Tweet. De gegevensset is beschikbaar gesteld door een. zei, S. DOOMS, B. Loni en D. Tikk voor aanbevolen systemen Challenge 2014.
  </td>
</tr>


<tr>
  <td>Weer gegevensset</td>
  <td>
Op land gebaseerde weers waarnemingen van NOAA (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">samengevoegde gegevens van 201304 tot 201310</a>).
<p></p>
De weer gegevens zijn van toepassing op waarnemingen die zijn gemaakt van de weers stations van de lucht haven, voor de periode van april tot en met 2013. Voordat u naar de ontwerp functie uploadt, is de gegevensset als volgt verwerkt:
<ul>
  <li>Er zijn weer station-Id's toegewezen aan de bijbehorende luchthaven-Id's</li>
  <li>Weer berichten die niet zijn gekoppeld aan de 70 drukste lucht havens zijn uitgefilterd</li>
  <li>De datum kolom is gesplitst in de kolommen voor één jaar, maand en dag</li>
  <li>De volgende kolommen zijn geselecteerd: AirportID, year, month, Day, time, time zone, SkyCondition, visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, record type, HourlyPrecip, altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500-gegevensset</td>
  <td>
Gegevens worden afgeleid van Wikipedia (<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>) op basis van artikelen van elke S & P 500-bedrijf, opgeslagen als XML-gegevens.
<p></p>
Voordat u naar de ontwerp functie uploadt, is de gegevensset als volgt verwerkt:
<ul>
  <li>Tekst inhoud voor elk specifiek bedrijf extra heren</li>
  <li>Wiki-opmaak verwijderen</li>
  <li>Niet-alfanumerieke tekens verwijderen</li>
  <li>Alle tekst naar kleine letters converteren</li>
  <li>Er zijn bekende bedrijfs categorieën toegevoegd</li>
</ul>
<p></p>
Houd er rekening mee dat voor sommige bedrijven een artikel niet kan worden gevonden. het aantal records is dus kleiner dan 500.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Volgende stappen

* Leer de basis beginselen van predictive analytics en machine learning met [de zelf studie: prijs van auto Mobile met de ontwerper](tutorial-designer-automobile-price-train-score.md)

* [Import data](./algorithm-module-reference/import-data.md) -module gebruiken om voorbeeld gegevens sets te importeren
