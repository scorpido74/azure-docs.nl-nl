---
title: De voorbeeldgegevenssets gebruiken
titleSuffix: ML Studio (classic) - Azure
description: Beschrijvingen van de gegevenssets die worden gebruikt in voorbeeldmodellen die zijn opgenomen in Machine Learning Studio (klassiek). U deze voorbeeldgegevenssets gebruiken voor uw experimenten.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: b7b8606f7f15f8d6fdd66681a1c7ade60ff506f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217783"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio-classic"></a>De voorbeeldgegevenssets gebruiken in Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

[top]: #machine-learning-sample-datasets

Wanneer u een nieuwe werkruimte maakt in Azure Machine Learning Studio (klassiek), worden standaard een aantal voorbeeldgegevenssets en experimenten opgenomen. Veel van deze voorbeeldgegevenssets worden gebruikt door de voorbeeldmodellen in de [Azure AI-galerie.](https://gallery.azure.ai/) Andere zijn opgenomen als voorbeelden van verschillende soorten gegevens die doorgaans worden gebruikt in machine learning.

Sommige van deze gegevenssets zijn beschikbaar in Azure Blob-opslag. Voor deze gegevenssets bevat de volgende tabel een directe koppeling. U deze gegevenssets in uw experimenten gebruiken met behulp van de module [Gegevens importeren.][import-data]

De rest van deze voorbeeldgegevenssets zijn beschikbaar in uw werkruimte onder **Opgeslagen gegevenssets**. U vindt dit in het modulepalet links van het experimentcanvas in Machine Learning Studio (klassiek).
U een van deze gegevenssets gebruiken in uw eigen experiment door deze naar uw experimentcanvas te slepen.

## <a name="datasets"></a>Gegevenssets

<table>

<tr>
  <th>Naam van de gegevensset</th>
  <th>Beschrijving van gegevensset</th>
</tr>

<tr>
  <td>Gegevensgegevensgegevens van binaire classificatie van inkomsten voor volwassenen</td>
  <td>
Een subset van de volkstellingsdatabase van 1994, met werkende volwassenen ouder dan 16 jaar met een aangepaste inkomensindex van > 100.
<p></p>
<b>Gebruik:</b> Classificeren mensen met behulp van demografische gegevens om te voorspellen of een persoon verdient meer dan 50K per jaar.
<p></p>
<b>Gerelateerd onderzoek:</b> Kohavi, R., Becker, B., (1996). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Repository . Irvine, CA: Universiteit van Californië, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Gegevensset luchthavencodes</td>
  <td>
Amerikaanse luchthavencodes.
<p></p>
Deze gegevensset bevat één rij voor elke luchthaven in de VS, met het luchthaven-id-nummer en de naam samen met de locatiestad en -status.
  </td>
</tr>

<tr>
  <td>Auto prijsgegevens (Raw)</td>
  <td>
Informatie over auto's per merk en model, inclusief de prijs, functies zoals het aantal cilinders en MPG, evenals een verzekeringsrisicoscore.
<p></p>
De risicoscore wordt in eerste instantie geassocieerd met de automatische prijs. Het wordt vervolgens aangepast voor het werkelijke risico in een proces dat actuarissen als symbool kent. Een waarde van +3 geeft aan dat de auto riskant is en een waarde van -3 dat deze waarschijnlijk veilig is.
<p></p>
<b>Gebruik:</b> Voorspel de risicoscore op basis van functies, met behulp van regressie of multivariate classificatie. 
<p></p>
<b>Gerelateerd onderzoek:</b> Schlimmer, J.C. (1987). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Repository . Irvine, CA: Universiteit van Californië, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Fietsverhuur UCI dataset</td>
  <td>
UCI Bike Rental dataset die is gebaseerd op echte gegevens van Capital Bikeshare bedrijf dat een fiets verhuur netwerk onderhoudt in Washington DC.
<p></p>
De gegevensset heeft één rij voor elk uur van elke dag in 2011 en 2012, voor een totaal van 17.379 rijen. Het bereik van de per uur fiets verhuur is van 1 tot 977.

  </td>
</tr>

<tr>
  <td>Afbeelding van Bill Gates RGB</td>
  <td>
Openbaar beschikbaar afbeeldingsbestand dat is geconverteerd naar CSV-gegevens.
<p></p>
De code voor het converteren van de afbeelding wordt weergegeven in de <strong>pagina Kleurkwanting met behulp van k-means clustering</strong> model detail pagina.
  </td>
</tr>

<tr>
  <td>Bloeddonatiegegevens</td>
  <td>
Een subset van gegevens uit de bloeddonordatabase van het Blood Transfusion Service Center van Hsin-Chu City, Taiwan.
<p></p>
Donorgegevens omvatten de maanden sinds de laatste donatie), en frequentie, of het totale aantal donaties, tijd sinds de laatste donatie en hoeveelheid bloed gedoneerd.
<p></p>
<b>Gebruik:</b> Het doel is om via classificatie te voorspellen of de donor bloed heeft gedoneerd in maart 2007, waarbij 1 een donor aangeeft tijdens de doelperiode, en 0 een niet-donor. 
<p></p>
<b>Gerelateerd onderzoek:</b> Yeh, I.C., (2008). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Repository . Irvine, CA: Universiteit van Californië, School of Information and Computer Science
<p></p>
Yeh, I-Cheng, Yang, King-Jang, en Ting, Tao-Ming, "Kennis ontdekking op RFM model met behulp van Bernoulli sequentie, "Expert Systems with Applications, 2008,<a href="https://dx.doi.org/10.1016/j.eswa.2008.07.018">https://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Gegevens over borstkanker</td>
  <td>
Een van de drie kankergerelateerde datasets van het Oncologisch Instituut die vaak voorkomen in machine learning literatuur. Combineert diagnostische informatie met kenmerken uit laboratoriumanalyse van ongeveer 300 weefselmonsters.
<p></p>
<b>Gebruik:</b> Classificeren van de aard van kanker, op basis van 9 attributen, waarvan sommige zijn lineair en sommige zijn categorisch. 
<p></p>
<b>Gerelateerd onderzoek:</b> Wohlberg, W.H., Straat, W.N., & Mangasarian, O.L. (1995). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Repository . Irvine, CA: Universiteit van Californië, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Borstkanker kenmerken <td>
De gegevensset bevat informatie voor 102K verdachte regio's (kandidaten) van röntgenbeelden, elk beschreven door 117 functies. De functies zijn eigendom en hun betekenis wordt niet onthuld door de makers van de dataset (Siemens Healthcare). 
  </td>
</tr>

<tr>
  <td>Borstkanker Info</td>
  <td>
De gegevensset bevat aanvullende informatie voor elk verdacht gebied van röntgenafbeelding. Elk voorbeeld bevat informatie (bijvoorbeeld label, patiënt-ID, coördinaten van patch ten opzichte van de hele afbeelding) over het bijbehorende rijnummer in de gegevensset Borstkankerfuncties. Elke patiënt heeft een aantal voorbeelden. Voor patiënten die een kanker hebben, sommige voorbeelden zijn positief en sommige zijn negatief. Voor patiënten die geen kanker hebben, zijn alle voorbeelden negatief. De dataset heeft 102K voorbeelden. De dataset is bevooroordeeld, 0,6% van de punten is positief, de rest is negatief. De dataset is beschikbaar gesteld door Siemens Healthcare.
  </td>
</tr>

<tr>
  <td>CRM-appetencylabels gedeeld</td>
  <td>
Labels van de KDD Cup 2009 customer relationship prediction challenge<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">(orange_small_train_appetency.labels).</a>
  </td>
</tr>

<tr>
  <td>CRM-churn-labels gedeeld</td>
  <td>
Labels van de KDD Cup 2009 customer relationship prediction challenge<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">(orange_small_train_churn.labels).</a>
  </td>
</tr>

<tr>
  <td>CRM-gegevensset gedeeld</td>
  <td>
Deze gegevens zijn afkomstig van de KDD Cup 2009 customer relationship prediction challenge<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">(orange_small_train.data.zip).</a>
<p></p>
De dataset bevat 50K klanten van het Franse telecombedrijf Orange. Elke klant heeft 230 geanonimiseerde functies, waarvan 190 numeriek en 40 categorisch. De functies zijn zeer schaars.
  </td>
</tr>

<tr>
  <td>CRM Upselling Labels gedeeld</td>
  <td>
Labels van de KDD Cup 2009 customer relationship prediction challenge<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">(orange_large_train_upselling.labels).</a>
  </td>
</tr>

<tr>
  <td>Energie-efficiëntie Regressie gegevens</td>
  <td>
Een verzameling gesimuleerde energieprofielen, gebaseerd op 12 verschillende bouwvormen. De gebouwen worden onderscheiden door acht eigenschappen. Dit omvat beglazing gebied, de beglazing gebied verdeling, en oriëntatie.
<p></p>
<b>Gebruik:</b> Gebruik regressie of classificatie om de energie-efficiëntierating te voorspellen op basis van een van de twee echt gewaardeerde antwoorden. Voor classificatie met meerdere klassen wordt de responsvariabele rondgerond tot het dichtstbijzijnde gehele getal. 
<p></p>
<b>Gerelateerd onderzoek:</b> Xifara, A. & Tsanas, A. (2012). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Repository . Irvine, CA: Universiteit van Californië, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Vluchtvertragingen Gegevens</td>
  <td>
Passagiersvlucht op tijd prestatiegegevens afkomstig van de TranStats data verzameling van het Amerikaanse Ministerie van Transport (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">On-Time</a>).
<p></p>
De gegevensset bestrijkt de periode april-oktober 2013. Voordat u uploadt naar Azure Machine Learning Studio (klassiek), werd de gegevensset als volgt verwerkt:
<ul>
  <li>De dataset werd gefilterd om alleen de 70 drukste luchthavens in het vasteland van de VS te dekken</li>
  <li>Geannuleerde vluchten werden als vertraagd met meer dan 15 minuten bestempeld</li>
  <li>Omgeleide vluchten werden uitgefilterd</li>
  <li>De volgende kolommen zijn geselecteerd: Jaar, Maand, DagofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Geannuleerd</li>
</ul>
</td>
</tr>

<tr>
  <td>Vlucht-on-time prestaties (Raw)</td>
  <td>
Records van aankomsten en vertrek van vliegtuigvluchten in de Verenigde Staten vanaf oktober 2011.
<p></p>
<b>Gebruik:</b> Voorspel vertragingen. 
<p></p>
<b>Gerelateerd onderzoek:</b> Van US Dept. <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>of Transportation .
  </td>
</tr>

<tr>
  <td>Bosbranden gegevens</td>
  <td>
Bevat weergegevens, zoals temperatuur- en vochtigheidsindexen en windsnelheid. De gegevens zijn afkomstig uit een gebied in het noordoosten van Portugal, gecombineerd met records van bosbranden.
<p></p>
<b>Gebruik:</b> Dit is een moeilijke regressietaak, waarbij het doel is om het verbrande gebied van bosbranden te voorspellen. 
<p></p>
<b>Gerelateerd onderzoek:</b> Cortez, P., & Morais, A. (2008). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Repository . Irvine, CA: Universiteit van Californië, School of Information and Computer Science
<p></p>
[Cortez en Morais, 2007] P. Cortez en A. Morais. Een data mining-benadering om bosbranden te voorspellen met behulp van meteorologische gegevens. In J. Neves, M.F. Santos en J. Machado Eds., New Trends in Artificial Intelligence, Proceedings of the 13th EPIA 2007 - Portuguese Conference on Artificial Intelligence, December, Guimarães, Portugal, pp. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Beschikbaar op: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr>
  <td>Duitse credit card UCI-gegevensset</td>
  <td>
De UCI Statlog (German Credit Card) dataset (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog+German+Credit+Data),</a>met behulp van het duitse.data bestand.
<p></p>
De gegevensset classificeert mensen, beschreven door een reeks kenmerken, als lage of hoge kredietrisico's. Elk voorbeeld vertegenwoordigt een persoon. Er zijn 20 functies, zowel numeriek als categorisch, en een binair label (de kredietrisicowaarde). De inschrijvingen met een hoog kredietrisico hebben label = 2, lage kredietrisicoposten hebben label = 1. De kosten van het verkeerd classificeren van een voorbeeld met een laag risico als hoog is 1, terwijl de kosten van het verkeerd classificeren van een hoog risico voorbeeld als laag is 5.
  </td>
</tr>

<tr>
  <td>IMDB-filmtitels</td>
  <td>
De gegevensset bevat informatie over films die zijn beoordeeld in Twitter-tweets: IMDB-film-ID, filmnaam, genre en productiejaar. Er zijn 17K films in de dataset. De dataset werd geïntroduceerd in de paper "S. Dooms, T. De Pessemier en L. Martens. MovieTweetings: een Movie Rating Dataset verzameld van Twitter. Workshop over Crowdsourcing en Human Computation for Recommender Systems, CrowdRec op RecSys 2013."
  </td>
</tr>

<tr>
  <td>Iris twee klassen gegevens</td>
  <td>
Dit is misschien wel de bekendste database te vinden in de patroonherkenning literatuur. De dataset is relatief klein, met 50 voorbeelden van elk bloemblaadje metingen van drie irisvariëteiten.
<p></p>
<b>Gebruik:</b> Voorspel het iristype van de metingen.  
<p></p>
<b>Gerelateerd onderzoek:</b> Fisher, R.A. (1988). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Repository . Irvine, CA: Universiteit van Californië, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Film Tweets</td>
  <td>
De gegevensset is een uitgebreide versie van de gegevensset FilmTweetings. De dataset heeft 170K ratings voor films, gewonnen uit goed gestructureerde tweets op Twitter. Elke instantie vertegenwoordigt een tweet en is een tuple: user ID, IMDB movie ID, rating, timestamp, aantal favorieten voor deze tweet, en het aantal retweets van deze tweet. De dataset werd beschikbaar gesteld door A. Said, S. Dooms, B. Loni en D. Tikk voor Recommender Systems Challenge 2014.
  </td>
</tr>

<tr>
  <td>MPG data voor diverse auto's</td>
  <td>
Deze dataset is een licht gewijzigde versie van de dataset van de StatLib bibliotheek van Carnegie Mellon University. De dataset werd gebruikt in de 1983 American Statistical Association Exposition.
<p></p>
De gegevens lijsten brandstofverbruik voor verschillende auto's in mijlen per gallon. Het bevat ook informatie zoals het aantal cilinders, motorverplaatsing, pk, totaal gewicht en acceleratie.
<p></p>
<b>Gebruik:</b> Voorspel het brandstofverbruik op basis van drie multivalueerde discrete attributen en vijf doorlopende kenmerken. 
<p></p>
<b>Gerelateerd onderzoek:</b> StatLib, Carnegie Mellon University, (1993). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Repository . Irvine, CA: Universiteit van Californië, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Pima Indians Diabetes Binaire Classificatie dataset</td>
  <td>
Een subset van gegevens van het National Institute of Diabetes and Digestive and Kidney Diseases database. De dataset werd gefilterd om zich te concentreren op vrouwelijke patiënten van Pima Indiase afkomst. De gegevens omvatten medische gegevens zoals glucose en insulineniveaus, evenals levensstijlfactoren.
<p></p>
<b>Gebruik:</b> Voorspellen of het onderwerp diabetes heeft (binaire classificatie). 
<p></p>
<b>Gerelateerd onderzoek:</b> Sigillito, V. (1990). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml</a>Repository " . Irvine, CA: Universiteit van Californië, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Restaurant klantgegevens</td>
  <td>
Een reeks metagegevens over klanten, inclusief demografische gegevens en voorkeuren.
<p></p>
<b>Gebruik:</b> Gebruik deze dataset, in combinatie met de andere twee restaurantdatasets, om een recommender systeem te trainen en te testen. 
<p></p>
<b>Gerelateerd onderzoek:</b> Bache, K. en Lichman, M. (2013). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Repository . Irvine, CA: Universiteit van Californië, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>Gegevens over de restaurantfunctie</td>
  <td>
Een reeks metagegevens over restaurants en hun functies, zoals voedseltype, eetstijl en locatie.
<p></p>
<b>Gebruik:</b> Gebruik deze dataset, in combinatie met de andere twee restaurantdatasets, om een recommender systeem te trainen en te testen. 
<p></p>
<b>Gerelateerd onderzoek:</b> Bache, K. en Lichman, M. (2013). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Repository . Irvine, CA: Universiteit van Californië, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>Restaurantbeoordelingen</td>
  <td>
Bevat beoordelingen die door gebruikers worden gegeven aan restaurants op een schaal van 0 tot 2.
<p></p>
<b>Gebruik:</b> Gebruik deze dataset, in combinatie met de andere twee restaurantdatasets, om een recommender systeem te trainen en te testen. 
<p></p>
<b>Gerelateerd onderzoek:</b> Bache, K. en Lichman, M. (2013). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Repository . Irvine, CA: Universiteit van Californië, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>Staal Annealing multi-class dataset</td>
  <td>
Deze dataset bevat een reeks records van staal annealing proeven. Het bevat de fysieke kenmerken (breedte, dikte, type (spoel, plaat, enz.) van de resulterende staalsoorten.
<p></p>
<b>Gebruik:</b> Voorspel een van de twee numerieke klassekenmerken; hardheid of kracht. U ook correlaties tussen kenmerken analyseren.
<p></p>
Staalkwaliteiten volgen een vaste standaard, gedefinieerd door SAE en andere organisaties. U bent op zoek naar een specifiek 'cijfer' (de klassevariabele) en wilt inzicht krijgen in de benodigde waarden. 
<p></p>
<b>Gerelateerd onderzoek:</b> Sterling, D. & Buntine, W. (NA). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Repository . Irvine, CA: Universiteit van Californië, School of Information and Computer Science
<p></p>
Een handige gids voor staalkwaliteiten is hier te vinden:<a href="https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf">https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Telescoopgegevens</td>
  <td>
Record van hoge energie gamma deeltjes uitbarstingen samen met achtergrondgeluid, beide gesimuleerd met behulp van een Monte Carlo proces.
<p></p>
De bedoeling van de simulatie was om de nauwkeurigheid van de grondgebaseerde atmosferische Cherenkov gammatelescopen te verbeteren. Dit wordt gedaan door behulp van statistische methoden om onderscheid te maken tussen het gewenste signaal (Cherenkov stralingsdouches) en achtergrondgeluid (hadronische douches geïnitieerd door kosmische stralen in de bovenste atmosfeer).
<p></p>
De gegevens zijn vooraf verwerkt om een langwerpig cluster te maken met de lange as is gericht op het cameracentrum. De kenmerken van deze ellips (vaak Hillas parameters genoemd) behoren tot de beeldparameters die kunnen worden gebruikt voor discriminatie.
<p></p>
<b>Gebruik:</b> Voorspel of het beeld van een douche signaal of achtergrondgeluid vertegenwoordigt.
<p></p>
<b>Opmerkingen:</b> Eenvoudige classificatienauwkeurigheid is niet zinvol voor deze gegevens, omdat het classificeren van een achtergrondgebeurtenis als signaal erger is dan het classificeren van een signaalgebeurtenis als achtergrond. Voor de vergelijking van verschillende classifiers moet de ROC-grafiek worden gebruikt. De kans dat een achtergrondgebeurtenis als signaal wordt geaccepteerd, moet lager zijn dan een van de volgende drempelwaarden: 0,01, 0,02, 0,05, 0,1 of 0,2.
<p></p>
Merk ook op dat het aantal achtergrondgebeurtenissen (h, voor hadronische douches) wordt onderschat. In reële metingen vertegenwoordigt de h- of ruisklasse de meeste gebeurtenissen. 
<p></p>
<b>Gerelateerd onderzoek:</b> Bock, R.K. (1995). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Repository . Irvine, CA: Universiteit van Californië, School of Information </td>
</tr>

<tr>
  <td>Weergegevensset</td>
  <td>
Uurgebonden weerwaarnemingen van NOAA<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">(samengevoegde gegevens van 201304 tot 201310).</a>
<p></p>
De weersgegevens hebben betrekking op waarnemingen van weerstations op luchthavens, die betrekking hebben op de periode april-oktober 2013. Voordat u uploadt naar Azure Machine Learning Studio (klassiek), werd de gegevensset als volgt verwerkt:
<ul>
  <li>Weerstation-iD's werden in kaart gebracht aan overeenkomstige luchthaven-geïdentificeerde</li>
  <li>Weerstations die niet zijn gekoppeld aan de 70 drukste luchthavens werden uitgefilterd</li>
  <li>De kolom Datum is opgesplitst in afzonderlijke kolommen Voor jaar, maand en dag</li>
  <li>De volgende kolommen werden geselecteerd: AirportID, Jaar, Maand, Dag, Tijd, Tijdzone, SkyCondition, Zichtbaarheid, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, Windsnelheid, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500-gegevensset</td>
  <td>
Gegevens zijn afgeleid<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>van Wikipedia ( ) op basis van artikelen van elk S&P 500 bedrijf, opgeslagen als XML-gegevens.
<p></p>
Voordat u uploadt naar Azure Machine Learning Studio (klassiek), werd de gegevensset als volgt verwerkt:
<ul>
  <li>Tekstinhoud voor elk specifiek bedrijf extraheren</li>
  <li>Wikiopmaak verwijderen</li>
  <li>Niet-alfanumerieke tekens verwijderen</li>
  <li>Alle tekst converteren naar kleine letters</li>
  <li>Bekende bedrijfscategorieën zijn toegevoegd</li>
</ul>
<p></p>
Houd er rekening mee dat voor sommige bedrijven een artikel niet kon worden gevonden, dus het aantal records is minder dan 500.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
De dataset bevat klantgegevens en aanwijzingen over hun reactie op een direct mailing campagne. Elke rij vertegenwoordigt een klant. De gegevensset bevat negen functies over demografische gegevens van gebruikers en gedrag uit het verleden en drie labelkolommen (bezoek, conversie en uitgaven).  Bezoek is een binaire kolom die aangeeft dat een klant na de marketingcampagne heeft bezocht. Conversie geeft aan dat een klant iets heeft gekocht. Uitgaven is het bedrag dat is uitgegeven.  De dataset werd beschikbaar gesteld door Kevin Hillstrom voor MineThatData E-Mail Analytics en Data Mining Challenge.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Kenmerken van testvoorbeelden in de RCV1-V2 Reuters nieuwsdataset. De dataset heeft 781K nieuwsartikelen samen met hun iDs (eerste kolom van de dataset). Elk artikel is tokenized, stopworded, en stemde. De dataset is beschikbaar gesteld door David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Kenmerken van trainingsvoorbeelden in de RCV1-V2 Reuters nieuwsdataset. De dataset heeft 23K nieuwsartikelen samen met hun iDs (eerste kolom van de dataset). Elk artikel is tokenized, stopworded, en stemde. De dataset is beschikbaar gesteld door David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Dataset van de KDD Cup 1999 Knowledge Discovery and Data Mining Tools Competition<a href="https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">(kddcup99.html).</a>
<p></p>
De gegevensset is gedownload en opgeslagen in Azure Blob-opslag<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">(network_intrusion_detection.csv)</a>en bevat zowel trainings- als testgegevenssets. De trainingsgegevensset heeft ongeveer 126K-rijen en 43 kolommen, inclusief de labels. Drie kolommen maken deel uit van de labelinformatie en 40 kolommen, bestaande uit numerieke en tekenreeks/categorische kenmerken, zijn beschikbaar voor het trainen van het model. De testgegevens hebben ongeveer 22,5K testvoorbeelden met dezelfde 43 kolommen als in de trainingsgegevens.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Onderwerp opdrachten voor nieuwsartikelen in de RCV1-V2 Reuters nieuws dataset. Een nieuwsartikel kan aan verschillende onderwerpen worden toegewezen. De indeling van elke&lt;rij&gt; &lt;is&gt; "topic name document id 1". De gegevensset bevat 2,6 Miljoen onderwerptoewijzingen. De dataset is beschikbaar gesteld door David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Deze gegevens komen uit de KDD Cup 2010 Student performance evaluatie uitdaging<a href="https://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">(student performance evaluatie).</a> De gebruikte gegevens zijn de Algebra_2008_2009 trainingsset (Stamper, J., Niculescu-Mizil, A., Ritter, S., Gordon, G.J., & Koedinger, K.R. (2010). Algebra I 2008-2009. Challenge dataset van KDD Cup 2010 Educational Data Mining Challenge. Vind het op <a href="https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
De gegevensset is gedownload en opgeslagen in Azure Blob-opslag<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">(student_performance.txt)</a>en bevat logboekbestanden van een tutoringsysteem voor studenten. De meegeleverde functies omvatten probleem-ID en de korte beschrijving, student-ID, tijdstempel, en hoeveel pogingen de student heeft gedaan voordat het probleem op de juiste manier wordt opgelost. De oorspronkelijke gegevensset heeft 8,9 M-records; deze gegevensset is down-sampled naar de eerste 100K rijen. De gegevensset bevat 23 kolommen met tabbladen van verschillende typen: numeriek, categorisch en tijdstempel.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Kickstart je experimenten met voorbeelden](sample-experiments.md)

<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
