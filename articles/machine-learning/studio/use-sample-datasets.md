---
title: De voorbeeld gegevens sets gebruiken
titleSuffix: ML Studio (classic) - Azure
description: Beschrijvingen van de gegevens sets die worden gebruikt in voorbeeld modellen die zijn opgenomen in Machine Learning Studio (klassiek). U kunt deze voorbeeld gegevens sets gebruiken voor uw experimenten.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: 211a2a83d2668c3240daca11e9063b31d42655ae
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152751"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio-classic"></a>De voorbeeld gegevens sets gebruiken in Azure Machine Learning Studio (klassiek)
[top]: #machine-learning-sample-datasets

Wanneer u een nieuwe werk ruimte maakt in Azure Machine Learning Studio (klassiek), zijn standaard een aantal voorbeeld gegevens sets en experimenten opgenomen. Veel van deze voorbeeld gegevens sets worden gebruikt door de voorbeeld modellen in het [Azure AI Gallery](https://gallery.azure.ai/). Anderen zijn opgenomen als voor beelden van verschillende typen gegevens die doorgaans worden gebruikt in machine learning.

Sommige van deze gegevens sets zijn beschikbaar in Azure Blob-opslag. De volgende tabel bevat een directe koppeling voor deze gegevens sets. U kunt deze gegevens sets in uw experimenten gebruiken met behulp van de module [import data][import-data] .

De rest van deze voorbeeld gegevens sets zijn beschikbaar in uw werk ruimte onder **opgeslagen gegevens sets**. U kunt dit vinden in het module palet links van het canvas op het experiment in Machine Learning Studio (klassiek).
U kunt elk van deze gegevens sets in uw eigen experiment gebruiken door deze naar uw experiment doek te slepen.

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
  <td>Gegevensset voor luchthaven codes</td>
  <td>
Amerikaanse luchthaven codes.
<p></p>
Deze gegevensset bevat één rij voor elke Amerikaanse lucht haven, met het nummer en de naam van de lucht haven en de plaats van de locatie en de status.
  </td>
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
  <td>ICB-gegevensset van fietsen</td>
  <td>
Een huur gegevensset van de ICB-fiets die is gebaseerd op echte gegevens van het kapitaal Bike share-bedrijf dat een netwerk van de fiets huur onderhoudt in Washington DC.
<p></p>
De gegevensset heeft één rij voor elk uur van elke dag in 2011 en 2012, voor een totaal van 17.379 rijen. Het bereik van de huur van een fiets per uur is 1 tot en met 977.

  </td>
</tr>

<tr>
  <td>RGB-afbeelding van Bill Gates</td>
  <td>
Openbaar beschikbaar afbeeldings bestand geconverteerd naar CSV-gegevens.
<p></p>
De code voor het converteren van de afbeelding vindt u in de <strong>kleur kwantisatiefouten met behulp</strong> van de detail pagina voor cluster model gegevens.
  </td>
</tr>

<tr>
  <td>Bloed donatie gegevens</td>
  <td>
Een subset van gegevens uit de bloeddonor-data base van het bloedtrans fusie Service Center van Hsin-Chu City, Taiwan.
<p></p>
Donor gegevens omvatten de maanden sinds de laatste donatie), de frequentie of het totale aantal donaties, de tijd sinds de laatste donatie en de hoeveelheid bloed gedoneerd.
<p></p>
<b>Gebruik:</b> Het doel is om te voors pellen via de classificatie of het donor gedoneerde bloed in maart 2007, waarbij 1 staat voor een donor tijdens de doel periode en 0 een niet-donor. 
<p></p>
<b>Verwant onderzoek:</b> Yeh, I.C., (2008). <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>van ICB machine learning-opslag. Irvine, CA: Universiteit van Californië, school van informatie en computer wetenschappen
<p></p>
Yeh, I-Cheng, Yang, King-Jang en afschrijven, label-PN, ' kennis detectie op RFM model met behulp van Bernoulli sequence ', ' experts Systems with Applications, 2008, <a href="https://dx.doi.org/10.1016/j.eswa.2008.07.018">https://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Borst kanker-gegevens</td>
  <td>
Een van de drie aan kanker gerelateerde gegevens sets die worden verschaft door het Oncology Institute dat regel matig wordt weer gegeven in machine learning documentatie. Combineert diagnostische gegevens met functies van laboratorium analyse van ongeveer 300-weefsel voorbeelden.
<p></p>
<b>Gebruik:</b> Het type kanker classificeren, gebaseerd op 9 kenmerken, sommige lineair en sommige zijn categorische. 
<p></p>
<b>Verwant onderzoek:</b> Wohlberg, W.H., Street, W.N., & Mangasarian, O.L. (1995). <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>van ICB machine learning-opslag. Irvine, CA: Universiteit van Californië, school van informatie en computer wetenschappen </td>
</tr>

<tr>
  <td>Borst kanker-functies <td>
De gegevensset bevat informatie voor 102K verdachte regio's (kandidaten) van Röntgen installatie kopieën, elk beschreven door 117-functies. De functies zijn eigendom en hun betekenis wordt niet getoond door de makers van de gegevensset (Siemens gezondheids zorg). 
  </td>
</tr>

<tr>
  <td>Borst kanker-informatie</td>
  <td>
De gegevensset bevat aanvullende informatie voor elke verdachte regio van de X-Ray-installatie kopie. Elk voor beeld bevat informatie (bijvoorbeeld label, patiënt-ID, coördinaten van patch ten opzichte van de hele afbeelding) over het overeenkomstige rijnummer in de gegevensset van de borst-functie van kanker. Elke patiënt heeft een aantal voor beelden. Voor patiënten die een kanker hebben, zijn enkele voor beelden positief en sommige zijn negatief. Voor patiënten die geen kanker hebben, zijn alle voor beelden negatief. De gegevensset bevat 102K-voor beelden. De gegevensset is bias, 0,6% van de punten is positief, de rest negatief. De gegevensset is beschikbaar gesteld door de Siemens-gezondheids zorg.
  </td>
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
  <td>Regressie gegevens voor energie-efficiëntie</td>
  <td>
Een verzameling gesimuleerde energie profielen, op basis van 12 verschillende vormen voor het bouwen. De gebouwen worden onderscheiden door acht functies. Hiertoe behoren de beglazings gebieden, de geglansde oppervlakte distributie en de afdruk stand.
<p></p>
<b>Gebruik:</b> Gebruik regressie of classificatie om de classificatie van energie-efficiëntie te voors pellen op basis van twee echte reacties. Voor classificatie met meerdere klassen wordt de reactie variabele afgerond op het dichtstbijzijnde gehele getal. 
<p></p>
<b>Verwant onderzoek:</b> Xifara, A. & Tsanas, A. (2012). <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>van ICB machine learning-opslag. Irvine, CA: Universiteit van Californië, school van informatie en computer wetenschappen </td>
</tr>

<tr>
  <td>Gegevens van vlieg vertraging</td>
  <td>
On-time prestatie gegevens van reizigers vlucht die zijn gemaakt op basis van de TranStats-gegevens verzameling van het Amerikaanse ministerie van Trans Port (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">op tijd</a>).
<p></p>
De gegevensset heeft betrekking op de periode van april tot en met 2013. Voordat u uploadt naar Azure Machine Learning Studio (klassiek), is de gegevensset als volgt verwerkt:
<ul>
  <li>De gegevensset is gefilterd op alleen de 70 drukste lucht havens in het continentale VS</li>
  <li>Geannuleerde vluchten zijn gelabeld met meer dan 15 minuten.</li>
  <li>Doorgevoerde vluchten zijn gefilterd</li>
  <li>De volgende kolommen zijn geselecteerd: jaar, maand, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, geannuleerd</li>
</ul>
</td>
</tr>

<tr>
  <td>Vlucht tijd in werking (onbewerkt)</td>
  <td>
Registreert aankomst van vlieg tuigen en vertrek binnen Verenigde Staten van oktober 2011.
<p></p>
<b>Gebruik:</b> Vertragingen in de vlucht voors pellen. 
<p></p>
<b>Verwant onderzoek:</b> Van de Amerikaanse afdeling. Trans Port <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>.
  </td>
</tr>

<tr>
  <td>Gegevens van het forest wordt geactiveerd</td>
  <td>
Bevat weer gegevens, zoals de indices van Tempe ratuur en vochtigheid en wind snelheid. De gegevens zijn afkomstig uit een gebied van Noordoost-Portugal, gecombineerd met records van bosbranden.
<p></p>
<b>Gebruik:</b> Dit is een moeilijke regressie taak, waarbij het doel is om het gebrande gebied van de bosbranden te voors pellen. 
<p></p>
<b>Verwant onderzoek:</b> Cortez, P., & Morais, A. (2008). <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>van ICB machine learning-opslag. Irvine, CA: Universiteit van Californië, school van informatie en computer wetenschappen
<p></p>
[Cortez en Morais, 2007] P. Cortez en een. Morais. Een aanpak van gegevens analyse voor het voors pellen van forests met behulp van meteorologische gegevens. In J. Neves, M. F. Santos en J. Machado eds., nieuwe trends in kunst matige intelligentie, procedures voor de 13 EPIA 2007-Portugese conferentie over kunst matige intelligentie, december, Guimarães, Portugal, pp. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Beschikbaar op: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
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
  <td>Twee klassen gegevens Iris</td>
  <td>
Dit is mogelijk de best bekende data base die wordt gevonden in de documentatie voor patroon herkenning. De gegevensset is relatief klein, met 50 voor beelden van de bloem maten van drie Iris varianten.
<p></p>
<b>Gebruik:</b> Het Iris type voors pellen op basis van de metingen.  
<p></p>
<b>Verwant onderzoek:</b> Fisher, R.A. (1988). <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>van ICB machine learning-opslag. Irvine, CA: Universiteit van Californië, school van informatie en computer wetenschappen </td>
</tr>

<tr>
  <td>Film tweets</td>
  <td>
De gegevensset is een uitgebreide versie van de Tweetings-gegevensset van de film. De gegevensset bevat 170K-classificaties voor films, geëxtraheerd uit goed gestructureerde tweets op Twitter. Elk exemplaar vertegenwoordigt een Tweet en is een tupel: gebruikers-ID, IMDB-film-ID, classificatie, tijds tempel, aantal favorieten voor deze Tweet en het aantal retweeten van deze Tweet. De gegevensset is beschikbaar gesteld door een. zei, S. DOOMS, B. Loni en D. Tikk voor aanbevolen systemen Challenge 2014.
  </td>
</tr>

<tr>
  <td>MPG-gegevens voor diverse auto's</td>
  <td>
Deze gegevensset is een enigszins gewijzigde versie van de gegevensset die is opgenomen in de StatLib-bibliotheek van de Carnegie Mellon University. De gegevensset is gebruikt in de 1983-Amerikaanse statistische Association Exposition.
<p></p>
Het brandstof verbruik voor verschillende auto's in mijlen per gallon wordt weer gegeven. Het bevat ook informatie zoals het aantal cilinders, de verplaatsing van de motor, de paarden kracht, het totale gewicht en de versnelling.
<p></p>
<b>Gebruik:</b> Brandstof voordelen voors pellen op basis van drie discrete kenmerken met meerdere waarden en vijf doorlopende kenmerken. 
<p></p>
<b>Verwant onderzoek:</b> StatLib, Carnegie Mellon University, (1993). <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>van ICB machine learning-opslag. Irvine, CA: Universiteit van Californië, school van informatie en computer wetenschappen </td>
</tr>

<tr>
  <td>Gegevensset voor Pima Indians diabetes binary-classificatie</td>
  <td>
Een subset van gegevens uit de data base van het National Institute of diabetes en de Digestive-en de nier-ziekten. De gegevensset is gefilterd om zich te concentreren op vrouwelijke patiënten van Pima Indiase erfgoed. De gegevens omvatten medische gegevens, zoals glucose-en inuline niveaus, en levens factoren.
<p></p>
<b>Gebruik:</b> Voor spelt u of het onderwerp diabetes heeft (binaire classificatie). 
<p></p>
<b>Verwant onderzoek:</b> Sigillito, V. (1990). Machine Learning opslagplaats <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>voor UCI. Irvine, CA: Universiteit van Californië, school van informatie en computer wetenschappen </td>
</tr>

<tr>
  <td>Klant gegevens van restaurant</td>
  <td>
Een set meta gegevens over klanten, met inbegrip van demografische informatie en voor keuren.
<p></p>
<b>Gebruik:</b> Gebruik deze gegevensset, in combi natie met de andere twee gegevens sets van het restaurant, om een aanbevolen systeem te trainen en te testen. 
<p></p>
<b>Verwant onderzoek:</b> Bache, K. en Lichman, M. (2013). <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>van ICB machine learning-opslag. Irvine, CA: Universiteit van Californië, school van informatie en computer wetenschappen.
  </td>
</tr>

<tr>
  <td>Gegevens van het restaurant onderdeel</td>
  <td>
Een set meta gegevens over restaurants en hun functies, zoals voedsel type, restauratie stijl en locatie.
<p></p>
<b>Gebruik:</b> Gebruik deze gegevensset, in combi natie met de andere twee gegevens sets van het restaurant, om een aanbevolen systeem te trainen en te testen. 
<p></p>
<b>Verwant onderzoek:</b> Bache, K. en Lichman, M. (2013). <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>van ICB machine learning-opslag. Irvine, CA: Universiteit van Californië, school van informatie en computer wetenschappen.
  </td>
</tr>

<tr>
  <td>Restaurant beoordelingen</td>
  <td>
Bevat beoordelingen van gebruikers aan restaurants op een schaal van 0 tot 2.
<p></p>
<b>Gebruik:</b> Gebruik deze gegevensset, in combi natie met de andere twee gegevens sets van het restaurant, om een aanbevolen systeem te trainen en te testen. 
<p></p>
<b>Verwant onderzoek:</b> Bache, K. en Lichman, M. (2013). <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>van ICB machine learning-opslag. Irvine, CA: Universiteit van Californië, school van informatie en computer wetenschappen.
  </td>
</tr>

<tr>
  <td>Annealing-gegevensset met meerdere klassen stalen</td>
  <td>
Deze gegevensset bevat een reeks records uit stalen annealing-experimenten. Het bevat de fysieke kenmerken (breedte, dikte, type (spiraal, blad, enzovoort) van de resulterende stalen typen.
<p></p>
<b>Gebruik:</b> Voors pellen van twee numerieke klassen kenmerken; hardheid of sterkte. U kunt ook correlaties tussen kenmerken analyseren.
<p></p>
Staal kwaliteiten volgen een set-standaard, gedefinieerd door SAE en andere organisaties. U zoekt naar een specifieke ' grade ' (de variabele Class) en u wilt meer inzicht krijgen in de waarden die nodig zijn. 
<p></p>
<b>Verwant onderzoek:</b> Sterling, D. & Buntine, W. (NA). <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>van ICB machine learning-opslag. Irvine, CA: Universiteit van Californië, school van informatie en computer wetenschappen
<p></p>
Een nuttige hand leiding voor staal kwaliteiten vindt u hier: <a href="https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf">https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Gegevens van telescoop</td>
  <td>
Record van hoogwaardige gamma partikel deeltjes samen met achtergrond geluid, beide gesimuleerd met behulp van een Monte Carlo-proces.
<p></p>
Het doel van de simulatie is om de nauw keurigheid van de Cherenkov gamma-telebereiken op grond van atmosferische lucht te verbeteren. Dit doet u door statistische methoden te gebruiken om onderscheid te maken tussen het gewenste signaal (Cherenkov stralings Showers) en achtergrond geluid (hadronic Showers die door cosmice stralen in de bovenste atmosfeer worden gestart).
<p></p>
De gegevens zijn vooraf verwerkt voor het maken van een rek-cluster met de lange as, gericht op het camera centrum. De kenmerken van deze ellips (vaak Hillas para meters genoemd) zijn onder de para meters van de installatie kopie die kan worden gebruikt voor discriminatie.
<p></p>
<b>Gebruik:</b> Voor spelt of een afbeelding van een douche een signaal of achtergrond geluid weergeeft.
<p></p>
<b>Opmerkingen:</b> Eenvoudige classificatie nauw keurigheid is niet zinvol voor deze gegevens, omdat het classificeren van een achtergrond gebeurtenis is erger dan het classificeren van een signaal gebeurtenis als achtergrond. Voor de vergelijking van verschillende classificaties moet de ROC grafiek worden gebruikt. De kans dat een achtergrond gebeurtenis als signaal wordt geaccepteerd, moet lager zijn dan een van de volgende drempel waarden: 0,01, 0,02, 0,05, 0,1 of 0,2.
<p></p>
Houd er ook rekening mee dat het aantal achtergrond gebeurtenissen (h, voor hadronic Showers) wordt onderschat. In echte metingen vertegenwoordigt de klasse h of Noise het meren deel van de gebeurtenissen. 
<p></p>
<b>Verwant onderzoek:</b> Bock, R.K. (1995). <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>van ICB machine learning-opslag. Irvine, CA: Universiteit van Californië, school van informatie </td>
</tr>

<tr>
  <td>Weer gegevensset</td>
  <td>
Op land gebaseerde weers waarnemingen van NOAA (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">samengevoegde gegevens van 201304 tot 201310</a>).
<p></p>
De weer gegevens zijn van toepassing op waarnemingen die zijn gemaakt van de weers stations van de lucht haven, voor de periode van april tot en met 2013. Voordat u uploadt naar Azure Machine Learning Studio (klassiek), is de gegevensset als volgt verwerkt:
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
Voordat u uploadt naar Azure Machine Learning Studio (klassiek), is de gegevensset als volgt verwerkt:
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

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing. CSV</a></td>
  <td>
De gegevensset bevat klant gegevens en indicaties over hun reactie op een directe mailing campagne. Elke rij vertegenwoordigt een klant. De gegevensset bevat negen functies voor demografische gegevens van gebruikers en eerdere gedragingen, en drie label kolommen (bezoeken, conversie en uitgaven).  Bezoek is een binaire kolom die aangeeft dat een klant na de marketing campagne is bezocht. Conversie geeft aan dat een klant iets heeft gekocht. Best Eden aan het bedrag dat is uitgegeven.  De gegevensset is beschikbaar gesteld door Kevin Hillstrom voor MineThatData-mail analyse en gegevens analyse.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test. CSV</a></td>
  <td>
Functies van test voorbeelden in de RCV1-v2-nieuws gegevensset van Reuters. De gegevensset heeft 781K nieuws artikelen samen met hun Id's (eerste kolom van de gegevensset). Elk artikel wordt getokend, stopworded en versleuteld. De gegevensset is beschikbaar gesteld door David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train. CSV</a></td>
  <td>
Functies van training-voor beelden in de RCV1-v2-nieuws gegevensset van Reuters. De gegevensset heeft 23K nieuws artikelen samen met hun Id's (eerste kolom van de gegevensset). Elk artikel wordt getokend, stopworded en versleuteld. De gegevensset is beschikbaar gesteld door David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection. CSV</a><br></td>
  <td>
Gegevensset van de KDDe Cup 1999 kennis detectie en Hulpprogram Ma's voor gegevens analyse van<a href="https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.</a>
<p></p>
De gegevensset is gedownload en opgeslagen in Azure Blob Storage (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection. CSV</a>) en bevat zowel trainingen als gegevens sets testen. De trainings gegevensset bevat ongeveer 126K rijen en 43 kolommen, met inbegrip van de labels. Er zijn drie kolommen die deel uitmaken van de label informatie en 40 kolommen die bestaan uit numerieke en teken reeks-en categorische-functies, zijn beschikbaar voor het trainen van het model. De test gegevens hebben ongeveer 22.5 K-test voorbeelden met dezelfde 43 kolommen als in de trainings gegevens.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2. topics. qrels. CSV</a></td>
  <td>
Onderwerps toewijzingen voor nieuws artikelen in de RCV1-v2-nieuws gegevensset van Reuters. Een nieuws artikel kan worden toegewezen aan verschillende onderwerpen. De indeling van elke rij is '&lt;topic name&gt; &lt;document-id&gt; 1 '. De gegevensset bevat 2.6 M-onderwerps toewijzingen. De gegevensset is beschikbaar gesteld door David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance. txt</a></td>
  <td>
Deze gegevens zijn afkomstig van de KDDe Cup 2010 Student prestatie-evaluatie test (<a href="https://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">Student Performance Evaluation</a>). De gebruikte gegevens zijn de Algebra_2008_2009-Trainingsset (stempel, J., Niculescu-Mizil, A., Ritter, S., Gordon, G.J., & Koedinger, K.R. (2010). Algebra I 2008-2009. Uitdaging gegevensset van de KDDe Cup 2010 onderwijs gegevens analyse uitdaging. Zoek het op <a href="https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">down loads. jsp</a>.
<p></p>
De gegevensset is gedownload en opgeslagen in Azure Blob-opslag (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance. txt</a>) en bevat logboek bestanden van een student-studie systeem. De opgegeven functies omvatten de probleem-ID en de korte beschrijving, student-ID, tijds tempel en het aantal pogingen dat de student heeft gedaan voordat het probleem op de juiste manier wordt opgelost. De oorspronkelijke gegevensset heeft 8,9 M Records; Deze gegevensset is niet beschikbaar-voor beeld van de eerste 100.000 rijen. De gegevensset heeft 23 door tabs gescheiden kolommen van verschillende typen: numeriek, categorische en tijds tempel.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Kickstart uw experimenten met voor beelden](sample-experiments.md)

<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
