---
title: Azure Maps-woordenlijst | Microsoft Documenten
description: Een woordenlijst met veelgebruikte termen die zijn gekoppeld aan Azure Maps, Locatiegebaseerde Services en GIS.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: c9d5c2efc14dec5beee0eaf81a4f628421ef3d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657041"
---
# <a name="glossary"></a>Woordenlijst

In de volgende lijst worden veelvoorkomende woorden beschreven die worden gebruikt met de Azure Maps-services.

## <a name="a"></a>A

<a name="address-validation"></a>**Adresvalidatie:** het proces om het bestaan van een adres te verifiëren.

<a name="advanced-routing"></a>**Geavanceerde routering:** een verzameling services die vooraf bewerkingen uitvoeren met behulp van routeringsgegevens; zoals het berekenen van bereikbare bereiken (isochronen), afstandsmatrices en batchrouteaanvragen.

<a name="aerial-imagery"></a>**Luchtfoto's :** Zie [satellietbeelden](#satellite-imagery). 

<a name="along-a-route-search"></a>**Langs een routezoekopdracht**: een ruimtelijke query die zoekt naar gegevens binnen een opgegeven omwegtijd of afstand van een routepad.

<a name="altitude"></a>**Hoogte:** De hoogte of verticale hoogte van een punt boven een referentieoppervlak. Hoogtemetingen zijn gebaseerd op een bepaald referentieniveau, zoals gemiddelde zeespiegel. Zie ook verhoging.

<a name="ambiguous"></a>**Dubbelzinnig**: een staat van onzekerheid in gegevensclassificatie die bestaat wanneer een object op de juiste wijze twee of meer waarden voor een bepaald kenmerk kan worden toegewezen. Wanneer bijvoorbeeld geocodering "CA" wordt geretourneerd, worden twee dubbelzinnige resultaten geretourneerd: "Canada" en "Californië". "CA" is een land en een staatscode, voor respectievelijk "Canada" en "Californië". 

<a name="annotation"></a>**Annotatie**: Tekst of afbeeldingen die op de kaart worden weergegeven om informatie aan de gebruiker te verstrekken. Annotatie kan een specifieke kaartentiteit identificeren of beschrijven, algemene informatie geven over een gebied op de kaart of informatie geven over de kaart zelf.

<a name="antimeridian"></a>**Antimeridian**: Ook bekend als<sup>th</sup> de 180e Meridiaan. Dit is het punt waar -180 graden en 180 graden van de longitude elkaar ontmoeten. Dat is het tegenovergestelde van de eerste meridiaan op de aardbol.

<a name="application-programming-interface-api"></a>**Application Programming Interface (API)**: Een specificatie waarmee ontwikkelaars toepassingen kunnen maken.

<a name="api-key"></a>**API-toets:** Zie [Verificatie van gedeelde sleutels](#shared-key-authentication).

<a name="area-of-interest-aoi"></a>**Interessegebied (AOI):** De mate die wordt gebruikt om een focusgebied voor een kaart of een databaseproductie te definiëren.

<a name="asset-tracking"></a>**Asset tracking:** het proces van het bijhouden van de locatie van een actief, zoals een persoon, voertuig of een ander object.

<a name="asynchronous-request"></a>**Asynchrone aanvraag**: een HTTP-verzoek dat een verbinding opent en een verzoek indient bij de server die een id retourneert voor de asynchrone aanvraag, sluit vervolgens de verbinding. De server blijft de aanvraag verwerken en de gebruiker kan de status controleren met behulp van de id. Wanneer de aanvraag klaar is met de verwerking, kan de gebruiker het antwoord downloaden. Dit type aanvraag wordt gebruikt voor langlopende processen.

<a name="autocomplete"></a>**Automatisch aanvullen:** een functie in een toepassing die de rest van een woord voorspelt dat een gebruiker typt. 

<a name="autosuggest"></a>**Autosuggest**: Een functie in een toepassing die logische mogelijkheden voorspelt voor wat de gebruiker typt.

<a name="azure-location-based-services-lbs"></a>**Azure Location Based Services (LBS)**: de voormalige naam van Azure Maps toen deze in preview stond.

<a name="azure-active-directory"></a>**Azure Active Directory (Azure AD):** Azure AD is de cloudgebaseerde service voor identiteits- en toegangsbeheer van Microsoft. Azure Maps Azure AD-integratie is momenteel beschikbaar in preview voor alle Azure Maps API's. Azure AD ondersteunt rbac (role-based access control) om fijnmazige toegang tot Azure Maps-bronnen mogelijk te maken. Zie [Azure Maps en Azure AD en](azure-maps-authentication.md) Verificatie beheren in Azure [Maps](how-to-manage-authentication.md)voor meer informatie over Azure Maps Azure AD-integratie.

<a name="azure-maps-key"></a>**Azure Maps-sleutel:** Zie [Verificatie van gedeelde sleutels](#shared-key-authentication).

## <a name="b"></a>B

<a name="base-map"></a>**Basiskaart:** het deel van een kaarttoepassing met achtergrondreferentiegegevens zoals wegen, oriëntatiepunten en politieke grenzen.

<a name="batch-request"></a>**Batchaanvraag:** het proces waarbij meerdere aanvragen worden gecombineerd tot één aanvraag.

<a name="bearing"></a>**Lager**: De horizontale richting van een punt ten opzichte van een ander punt. Dit wordt uitgedrukt als een hoek ten opzichte van het noorden, van 0-graden tot 360 graden in een richting met de klok mee. 

<a name="boundary"></a>**Grens:** een lijn of veelhoek die aangrenzende politieke entiteiten scheidt, zoals landen/regio's, districten en eigenschappen. Een grens is een lijn die al dan niet fysieke kenmerken, zoals rivieren, bergen of muren, kan volgen.

<a name="bounds"></a>**Grenzen**: Zie [Selectiekader .](#bounding-box)

<a name="bounding-box"></a>**Selectiekader**: Een reeks coördinaten die worden gebruikt om een rechthoekig gebied op de kaart weer te geven. 

## <a name="c"></a>C

<a name="cadastre"></a>**Kadaster**: Een record van geregistreerde grond en eigendommen. Zie ook [Pakket](#parcel).

<a name="camera"></a>**Camera:** In de context van een interactief kaartbesturingselement definieert een camera het gezichtsveld van de kaarten. De viewport van de camera wordt bepaald op basis van verschillende kaartparameters: midden, zoomniveau, toonhoogte, lager. 

<a name="centroid"></a>**Centroid**: Het geometrische centrum van een functie. De centroid van een lijn zou het middelpunt zijn, terwijl de centroid van een veelhoek het middelpunt van het gebied zou zijn.

<a name="choropleth-map"></a>**Choropleth kaart**: Een thematische kaart waarin gebieden zijn gearceerd in verhouding tot een meting van een statistische variabele. Deze statistische variabele wordt weergegeven op de kaart. Bijvoorbeeld, het kleuren van de grens van elke Amerikaanse staat op basis van de relatieve bevolking van alle andere staten.

<a name="concave-hull"></a>**Concave hull**: Een vorm die een mogelijke holle geometrie vertegenwoordigt die alle vormen in de opgegeven gegevensset omsluit. De gegenereerde vorm is vergelijkbaar met het verpakken van de gegevens met plastic folie en vervolgens verwarmen, waardoor grote overspanningen tussen de punten in te grot naar andere gegevens punten.

<a name="consumption-model"></a>**Verbruiksmodel**: Informatie die bepaalt hoe snelheid een voertuig brandstof of elektriciteit verbruikt. Zie ook de documentatie van het [verbruiksmodel.](consumption-model.md)

<a name="control"></a>**Controle:** een op zichzelf staande of herbruikbare component die bestaat uit een grafische gebruikersinterface die een set gedragingen voor de interface definieert. Bijvoorbeeld, een kaart besturingselement, is over het algemeen het gedeelte van de gebruikersinterface die een interactieve kaart laadt.

<a name="convex-hull"></a>**Convexe romp**: Een bolle romp is een vorm die de minimale bolle geometrie vertegenwoordigt die alle vormen in de opgegeven gegevensset omsluit. De gegenereerde vorm is vergelijkbaar met het omwikkelen van een elastische band rond de gegevensset.

<a name="coordinate"></a>**Coördinaat**: Bestaat uit de lengte- en breedtewaarden die worden gebruikt om een locatie op een kaart weer te geven.

<a name="coordinate-system"></a>**Coördinatensysteem**: Een referentiekader dat wordt gebruikt om de posities van punten in de ruimte in twee of drie dimensies te definiëren.

<a name="country-code"></a>**Landcode**: een unieke id voor een land/regio op basis van de ISO-standaard. ISO2 is een code met twee tekens voor een land (bijvoorbeeld VS), die ISO3 vertegenwoordigt een code met drie tekens (bijvoorbeeld de VS).

<a name="country-subdivision"></a>**De onderverdeling**van het land : Een eerste-niveau onderverdeling van een land/gebied, algemeen gekend als een staat of provincie.

<a name="country-secondary-subdivision"></a>**Land secundaire onderverdeling**: Een tweede niveau onderverdeling van een land / regio, algemeen bekend als een provincie.

<a name="country-tertiary-subdivision"></a>**Tertiaire onderverdeling van het land**: Een onderverdeling op het derde niveau van een land/regio, meestal een genoemd gebied zoals een wijk.

<a name="cross-street"></a>**Dwarsstraat**: Een punt waar twee of meer straten elkaar kruisen.

<a name="cylindrical-projection"></a>**Cilindrische projectie**: Een projectie die punten van een sferoïde of bol transformeert op een raaklijn of secant cilinder. De cilinder wordt vervolgens van boven naar beneden gesneden en afgevlakt in een vlak.

## <a name="d"></a>D

<a name="datum"></a>**Datum**: De referentiespecificaties van een meetsysteem, een systeem van coördinatenposities op een oppervlak (een horizontaal datum) of hoogten boven of onder een oppervlak (een verticale datum).

<a name="dbf-file"></a>**DBF-bestand**: een databasebestandsindeling die wordt gebruikt in combinatie met Shapefiles (SHP).

<a name="degree-minutes-seconds-dms"></a>**Graad minuten seconden (DMS)**: De maateenheid voor het beschrijven van breedte- en lengtegraad. Een graad is 1/360<sup>ste</sup> van een cirkel. Een graad is verder verdeeld in 60 minuten, en een minuut is verdeeld in 60 seconden.

<a name="delaunay-triangulation"></a>**Delaunay triangulatie**: Een techniek voor het creëren van een gaas van aaneengesloten, niet-overlappende driehoeken uit een dataset van punten. De omlijstcirkel van elke driehoek bevat geen punten uit de gegevensset in het interieur.

<a name="demographics"></a>**Demografie**: De statistische kenmerken (zoals leeftijd, geboortecijfer en inkomen) van een menselijke bevolking.

<a name="destination"></a>**Bestemming**: Een eindpunt of locatie waar naar iemand reist.

<a name="digital-elevation-model-dem"></a>**Digital Elevation Model (DEM)**: Een gegevensset van hoogtewaarden met betrekking tot een oppervlak, vastgelegd over een gebied met regelmatige tussenpozen met behulp van een gemeenschappelijke datum. DEM's worden meestal gebruikt om terreinreliëf weer te geven.

<a name="dijkstra's-algorithm"></a>**Dijkstra's algoritme**: Een algoritme dat de connectiviteit van een netwerk onderzoekt om het kortste pad tussen twee punten te vinden.

<a name="distance-matrix"></a>**Afstandsmatrix**: Een matrix die reistijd en afstandsinformatie tussen een reeks oorsprongen en bestemmingen bevat. 

## <a name="e"></a>E

<a name="elevation"></a>**Hoogte:** de verticale afstand van een punt of een object boven of onder een referentieoppervlak of -datum. Over het algemeen is het referentieoppervlak de gemiddelde zeespiegel. Hoogte verwijst over het algemeen naar de verticale hoogte van het land.

<a name="envelope"></a>**Envelop**: Zie [Selectiekader](#bounding-box).

<a name="extended-postal-code"></a>**Uitgebreide postcode**: Een postcode die aanvullende informatie kan bevatten. In de VS hebben postcodes bijvoorbeeld vijf cijfers. Maar, een uitgebreide postcode, bekend als zip +4, bevat vier extra cijfers. Deze extra cijfers worden gebruikt om een geografisch segment te identificeren binnen het vijfcijferige leveringsgebied, zoals een stadsblok, een groep appartementen of een postbus. Het kennen van het geografische segment helpt bij het efficiënt sorteren en bezorgen van post.

<a name="extent"></a>**Omvang**: Zie [Selectiekader](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a>**Federated authentication**: Een verificatiemethode waarmee één aanmeldings-/verificatiemechanisme kan worden gebruikt in meerdere web- en mobiele apps. 

<a name="feature"></a>**Functie:** een object dat een geometrie combineert met een extra metagegevens. 

<a name="feature-collection"></a>**Functieverzameling**: Een verzameling objecten met een functie.

<a name="find-along-route"></a>**Zoek langs route**: een ruimtelijke query die zoekt naar gegevens die zich binnen een opgegeven omwegtijd of afstand van een routepad bevinden.

<a name="find-nearby"></a>**Zoek in de buurt**: Een ruimtelijke query die een vaste rechte lijn afstand (als de kraai vliegt) zoekt vanaf een punt.

<a name="fleet-management"></a>**Fleet management**: Het beheer van bedrijfsvoertuigen zoals auto's, vrachtwagens, schepen en vliegtuigen. Fleet management kan een scala aan functies omvatten, zoals voertuigfinanciering, onderhoud, telematica (tracking en diagnostiek) en bestuurder, snelheid, brandstof en gezondheids- en veiligheidsbeheer. Fleet Management is een proces dat wordt gebruikt door bedrijven die vertrouwen op transport in hun bedrijf. De bedrijven willen de risico's minimaliseren en hun totale transport- en personeelskosten verlagen, terwijl ze tegelijkertijd de naleving van de overheidswetgeving waarborgen.

<a name="free-flow-speed"></a>**Vrije doorstromingssnelheid**: De vrije doorstromingssnelheid verwacht onder ideale omstandigheden. Meestal de maximumsnelheid.

<a name="free-form-address"></a>**Gratis formulieradres**: Een volledig adres dat wordt weergegeven als één regel tekst.

<a name="fuzzy-search"></a>**Fuzzy search**: Een zoekopdracht die een gratis tekstreeks inneemt die een adres of interessepunt kan zijn. 

## <a name="g"></a>G

<a name="geocode"></a>**Geocode:** een adres of locatie die is omgezet in een coördinaat die kan worden gebruikt om die locatie op een kaart weer te geven. 

<a name="geocoding"></a>**Geocoding**: Ook bekend als forward geocoding, is het proces van het omzetten van adres van locatiegegevens in coördinaten. 

<a name="geodesic-path"></a>**Geodetisch pad**: Het kortste pad tussen twee punten op een gebogen oppervlak. Wanneer dit pad wordt weergegeven op Azure Maps, wordt dit pad weergegeven als een gebogen lijn als gevolg van de Mercator-projectie.

<a name="geofence"></a>**Geofence:** een gedefinieerd geografisch gebied dat kan worden gebruikt om gebeurtenissen te activeren wanneer een apparaat de regio binnenkomt of bestaat.

<a name="geojson"></a>**GeoJSON**: Is een veelgebruikte JSON-bestandsindeling die wordt gebruikt voor het opslaan van geografische vectorgegevens zoals punten, lijnen en veelhoeken. **Opmerking**: Azure Maps gebruikt een uitgebreide versie van GeoJSON zoals [hier gedocumenteerd.](extend-geojson.md)

<a name="geometry"></a>**Geometrie:** vertegenwoordigt een ruimtelijk object zoals een punt, lijn of veelhoek.

<a name="geometrycollection"></a>**GeometrieCollectie:** een verzameling geometrieobjecten.

<a name="geopol"></a>**GeoPol**: Verwijst naar geopolitiek gevoelige gegevens, zoals betwiste grenzen en plaatsnamen.

<a name="georeference"></a>**Georeferentie**: Het proces van het afstemmen van geografische gegevens of afbeeldingen op een bekend coördinatensysteem. Dit proces kan bestaan uit het verschuiven, roteren, schalen of scheeftrekken van de gegevens.

<a name="georss"></a>**GeoRSS**: een XML-extensie voor het toevoegen van ruimtelijke gegevens aan RSS-feeds.

<a name="gis"></a>**GIS**: Een acroniem voor "Geografisch InformatieSysteem". Een veelgebruikte term die wordt gebruikt om de kaartindustrie te beschrijven.

<a name="gml"></a>**GML**: Ook bekend als Aardrijkskunde Markup Taal. Een XML-bestandsextensie voor het opslaan van ruimtelijke gegevens.

<a name="gps"></a>**GPS**: Ook bekend als Global Positioning System, is een systeem van satellieten die worden gebruikt voor het bepalen van een positie van apparaten op de aarde. De satellieten zenden signalen uit waarmee een GPS-ontvanger overal op aarde zijn eigen locatie kan berekenen door middel van trilateratie.

<a name="gpx"></a>**GPX**: Ook bekend als GPS eXchange formaat, is een XML-bestandsindeling die vaak is gemaakt van GPS-apparaten.  

<a name="great-circle-distance"></a>**Grote cirkel afstand**: De kortste afstand tussen twee punten op het oppervlak van een bol.

<a name="greenwich-mean-time-gmt"></a>**Greenwich Mean Time (GMT)**: De tijd op de eerste meridiaan, die loopt door het Royal Observatory in Greenwich, Engeland.

<a name="guid"></a>**GUID**: Een wereldwijd unieke id. Een tekenreeks die wordt gebruikt om een interface, klasse, tekstbibliotheek, componentcategorie of record op unieke wijze te identificeren.

## <a name="h"></a>H

<a name="haversine-formula"></a>**Haversine formule**: Een veel voorkomende vergelijking die wordt gebruikt voor het berekenen van de grote-cirkel afstand tussen twee punten op een bol.

<a name="hd-maps"></a>**HD-kaarten**: Ook bekend als High Definition Maps, bestaat uit high fidelity road network informatie zoals rijstrookmarkeringen, bewegwijzering, en richtinglichten die nodig zijn voor autonoom rijden.

<a name="heading"></a>**Kop**: De richting iets wijst of naar voren. Zie ook [Lager](#heading).

<a name="heatmap"></a>**Heatmap:** een gegevensvisualisatie waarin een reeks kleuren de dichtheid van punten in een bepaald gebied weergeeft. Zie ook Thematische kaart.

<a name="hybrid-imagery"></a>**Hybride beelden**: Satelliet- of luchtfoto's met weggegevens en labels erbovenop.

## <a name="i"></a>I

<a name="iana"></a>**IANA**: Een acroniem voor de Internet Assigned Numbers Authority. Een non-profitgroep die toezicht houdt op de wereldwijde toewijzing van IP-adres.

<a name="isochrone"></a>**Isochrone**: Een isochrone definieert het gebied waarin iemand binnen een bepaalde tijd kan reizen voor een vervoerswijze in elke richting vanaf een bepaalde locatie. Zie ook [Bereikbaar bereik](#reachable-range).

<a name="isodistance"></a>**Isodistance**: Gezien een locatie, een isochrone definieert het gebied waarin iemand kan reizen binnen een bepaalde afstand voor een wijze van vervoer in elke richting. Zie ook [Bereikbaar bereik](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a>**KML**: Ook wel keyhole markuptaal genoemd, is een veelgebruikte XML-bestandsindeling voor het opslaan van geografische vectorgegevens zoals punten, lijnen en veelhoeken. 

## <a name="l"></a>L

<a name="landsat"></a>**Landsat**: Multispectrale, aarde-baan satellieten ontwikkeld door NASA die beelden van het land te verzamelen. Deze beelden worden gebruikt in vele industrieën zoals landbouw, bosbouw en cartografie.

<a name="latitude"></a>**Breedtegraad**: De hoekafstand gemeten in graden vanaf de evenaar in een noord- of zuidrichting.

<a name="level-of-detail"></a>**Detailniveau:** Zie Zoomniveau.

<a name="lidar"></a>**Lidar**: Acroniem voor lichtdetectie en variërend. Een remote-sensing techniek die lasers gebruikt om afstanden tot reflecterende oppervlakken te meten.

<a name="linear-interpolation"></a>**Lineaire interpolatie**: De schatting van een onbekende waarde met behulp van de lineaire afstand tussen bekende waarden.

<a name="linestring"></a>**LineString:** een geometrie die wordt gebruikt om een lijn weer te geven. Ook wel bekend als een polyline. 

<a name="localization"></a>**Lokalisatie**: Ondersteuning voor verschillende talen en culturen.

<a name="logistics"></a>**Logistiek**: Het proces van het verplaatsen van mensen, voertuigen, leveringen of activa op een gecoördineerde manier.

<a name="longitude"></a>**Lengte :** De hoekafstand gemeten in graden van de eerste meridiaan in een oost- of westrichting.

## <a name="m"></a>M

<a name="map-tile"></a>**Kaarttegel:** een rechthoekige afbeelding die een partitie van een kaartcanvas vertegenwoordigt. Zie [Zoomniveaus en tegelrasterdocumentatie voor](zoom-levels-and-tile-grid.md)meer informatie.

<a name="marker"></a>**Markering**: Ook wel bekend als een pin of punaise, is een pictogram dat een puntlocatie op een kaart vertegenwoordigt.

<a name="mercator-projection"></a>**Mercator projectie**: Een cilindrische kaart projectie die werd de standaard kaart projectie voor nautische doeleinden vanwege zijn vermogen om lijnen van constante koers, bekend als rhumb lijnen, als rechte segmenten die de hoeken met de meridianen te behouden. Alle vlakke kaartprojecties vervormen de vormen of maten van de kaart in vergelijking met de werkelijke lay-out van het aardoppervlak. De Mercator-projectie overdrijft gebieden ver van de evenaar, zodat kleinere gebieden groter op de kaart verschijnen als u de polen nadert. 

<a name="multilinestring"></a>**MultiLineString:** een geometrie die een verzameling LineString-objecten vertegenwoordigt. 

<a name="multipoint"></a>**MultiPoint:** een geometrie die een verzameling puntobjecten vertegenwoordigt.

<a name="multipolygon"></a>**MultiPolygon:** een geometrie die een verzameling polygoonobjecten vertegenwoordigt. Bijvoorbeeld, om de grens van Hawaï te tonen, zou elk eiland worden geschetst met een veelhoek. Zo zou de grens van Hawaï dus een MultiPolygon zijn.

<a name="municipality"></a>**Gemeente**: Een stad of stad. 

<a name="municipality-subdivision"></a>**Gemeente onderverdeling**: Een onderverdeling van een gemeente, zoals een buurt of lokale naam zoals "downtown".

## <a name="n"></a>N

<a name="navigation-bar"></a>**Navigatiebalk:** de set besturingselementen op een kaart die wordt gebruikt voor het aanpassen van het zoomniveau, de toonhoogte, de rotatie en het schakelen van de basiskaartlaag.

<a name="nearby-search"></a>**Zoeken in de buurt**: een ruimtelijke query die een vaste rechte afstand (als de kraai vliegt) vanaf een punt doorzoekt.

<a name="neutral-ground-truth"></a>**Neutrale grondwaarheid:** een kaart die labels weergeeft in de officiële taal van de regio die het vertegenwoordigt en in lokale scripts indien beschikbaar.

## <a name="o"></a>O

<a name="origin"></a>**Oorsprong**: Een startpunt of locatie waar een gebruiker zich bevindt.

## <a name="p"></a>P

<a name="panning"></a>**Pannen:** Het proces van het verplaatsen van de kaart in elke richting met behoud van een constant zoomniveau.

<a name="parcel"></a>**Perceel**: Een stuk grond of eigendomsgrens.

<a name="pitch"></a>**Pitch**: De hoeveelheid kantelen van de kaart ten opzichte van de verticale waar 0 recht naar beneden kijkt naar de kaart.

<a name="point"></a>**Punt:** een geometrie die één positie op de kaart vertegenwoordigt. 

<a name="points-of-interest-poi"></a>**Points of interest (POI):** Een bedrijf, oriëntatiepunt of gemeenschappelijke plaats van belang.

<a name="polygon"></a>**Veelhoek**: een vaste geometrie die een gebied op een kaart vertegenwoordigt. 

<a name="polyline"></a>**Polyline**: Een geometrie die wordt gebruikt om een lijn weer te geven. Ook wel bekend als een LineString. 

<a name="position"></a>**Positie**: de lengtegraad, breedtegraad en hoogte (x,y,z-coördinaten) van een punt.

<a name="post-code"></a>**Postcode**: Zie [Postcode](#postal-code).

<a name="postal-code"></a>**Postcode**: Een reeks letters of cijfers, of beide, in een specifieke indeling. De postcode wordt door de postdienst van een land/regio gebruikt om geografische gebieden in zones te verdelen om de bezorging van post te vereenvoudigen.

<a name="primary-key"></a>**Primaire sleutel:** de eerste van twee abonnementensleutels voor gedeelde sleutelverificatie in Azure Maps. Zie [Verificatie van gedeelde sleutels](#shared-key-authentication).

<a name="prime-meridian"></a>**Eerste meridiaan**: Een lijn van lengte die 0-graden lengte vertegenwoordigt. Over het algemeen, longitude waarden dalen bij het reizen in een westelijke richting tot 180 graden en toenemen bij het reizen in oostelijke richtingen tot -180-graden. 

<a name="prj"></a>**PRJ**: Een tekstbestand dat vaak vergezeld gaat van een Shapefile-bestand dat informatie bevat over het geprojecteerde coördinatensysteem waarin de gegevensset zich bevindt.

<a name="projection"></a>**Projectie**: Een geprojecteerd coördinatenstelsel op basis van een kaartprojectie zoals dwarsmercator, Albers gelijk gebied en Robinson. Deze bieden de mogelijkheid om kaarten van het bolvormige oppervlak van de aarde te projecteren op een tweedimensionaal Cartesiaans coördinatenvlak. Geprojecteerde coördinatensystemen worden soms kaartprojecties genoemd.

## <a name="q"></a>Q

<a name="quadkey"></a>**Quadkey**: Een base-4 adresindex voor een tegel binnen een quadtree-betegelingssysteem. Zie [Zoomniveaus en tegelrasterdocumentatie](zoom-levels-and-tile-grid.md) voor meer informatie voor meer informatie.

<a name="quadtree"></a>**Quadtree**: Een datastructuur waarin elk knooppunt precies vier kinderen heeft. Het tegelsysteem dat in Azure Maps wordt gebruikt, maakt gebruik van een quadstructuur, zodat als gebruiker in één niveau zoomt, elke kaarttegel opsplitst in vier subtiles.  Zie [Zoomniveaus en tegelrasterdocumentatie](zoom-levels-and-tile-grid.md) voor meer informatie voor meer informatie.

<a name="queries-per-second-qps"></a>**Query's per seconde (QPS)**: het aantal query's of aanvragen dat binnen een seconde naar een service of platform kan worden gedaan. 

## <a name="r"></a>R

<a name="radial-search"></a>**Radiaal zoeken:** een ruimtelijke query die een vaste rechte lijn afstand zoekt (als de kraai vliegt) vanaf een punt. 

<a name="raster-data"></a>**Rastergegevens:** een matrix van cellen (of pixels) die is ingedeeld in rijen en kolommen (of een raster) waarbij elke cel een waarde bevat die informatie weergeeft, zoals temperatuur. Raster's omvatten digitale luchtfoto's, beelden van satellieten, digitale foto's en gescande kaarten.

<a name="raster-layer"></a>**Rasterlaag:** een tegellaag die bestaat uit rasterafbeeldingen.

<a name="reachable-range"></a>**Bereikbaar bereik**: Een bereik bepaalt het gebied waarin iemand binnen een bepaalde tijd of afstand kan reizen, voor een vervoerswijze om te reizen, in elke richting vanaf een locatie. Zie ook [Isochrone](#isochrone) en [Isodistance.](#isodistance)

<a name="remote-sensing"></a>**Teledetectie**: Het proces van het verzamelen en interpreteren van sensorgegevens op afstand.

<a name="rest-service"></a>**REST service**: Het acroniem REST staat voor Representational State Transfer. Een REST-service is een op URL gebaseerde webservice die is gebaseerd op basisvan webtechnologie om te communiceren, waarbij de meest voorkomende methoden HTTP GET- en POST-verzoeken zijn. Dit soort diensten hebben de neiging om mij veel sneller en kleiner dan de traditionele SOAP-gebaseerde diensten.

<a name="reverse-geocode"></a>**Omgekeerde geocode**: Het proces van het nemen van een coördinaat en het bepalen van het adres waarin wordt weergegeven op een kaart.

<a name="reproject"></a>**Herproject:** Zie [Transformatie](#transformation).

<a name="rest-service"></a>**REST-dienst**: Acroniem voor representatieve staatsoverdracht. Een architectuur voor het uitwisselen van informatie tussen peers in een gedecentraliseerde, gedistribueerde omgeving. Met REST kunnen programma's op verschillende computers onafhankelijk van een besturingssysteem of platform communiceren. Een service kan een HTTP-aanvraag (Hypertext Transfer Protocol) verzenden naar een uniforme url (resource locator) en gegevens terugkrijgen.

<a name="route"></a>**Route**: Een pad tussen twee of meer locaties, die ook aanvullende informatie kunnen bevatten, zoals instructies voor waypoints langs de route.

<a name="requests-per-second-rps"></a>**Aanvragen per seconde (RPS)**: Zie [query's per seconde (QPS).](#queries-per-second-qps) 

<a name="rss"></a>**RSS:** Acroniem voor echt eenvoudige syndicatie, Resource Description Framework (RDF) Site Samenvatting, of Rich Site Summary, afhankelijk van de bron. Een eenvoudige, gestructureerde XML-indeling voor het delen van inhoud tussen verschillende websites. RSS-documenten bevatten belangrijke metagegevenselementen zoals auteur, datum, titel, een korte beschrijving en een hypertekstkoppeling. Deze informatie helpt een gebruiker (of een RSS-uitgeverservice) te beslissen welke materialen verder onderzoek waard zijn.

## <a name="s"></a>S

<a name="satellite-imagery"></a>**Satellietbeelden**: Beelden die zijn vastgelegd door vliegtuigen en satellieten die recht naar beneden wijzen.

<a name="secondary-key"></a>**Secundaire sleutel:** de tweede van twee abonnementensleutels voor gedeelde sleutelverificatie van Azure Maps. Zie [Verificatie van gedeelde sleutels](#shared-key-authentication).

<a name="shapefile-shp"></a>**Shapefile (SHP)**: Ook wel esri shapefile genoemd, is een vectorgegevensopslagindeling voor het opslaan van de locatie, vorm en kenmerken van geografische kenmerken. Een shapefile wordt opgeslagen in een set gerelateerde bestanden.

<a name="shared-key-authentication"></a>**Verificatie van gedeelde sleutels:** verificatie met gedeelde sleutel is afhankelijk van het doorgeven van azure maps-accountgegenereerde sleutels bij elk verzoek aan Azure Maps. Deze sleutels worden vaak abonnementssleutels genoemd. Het wordt aanbevolen dat sleutels regelmatig worden geregenereerd voor beveiliging. Er zijn twee sleutels aanwezig, zodat u verbindingen onderhouden met de ene toets terwijl u de andere regenereert. Als u de sleutels opnieuw genereert, dient u elke toepassing bij te werken die toegang heeft tot dit account om de nieuwe sleutels te kunnen gebruiken. Zie [Azure Maps en Azure AD en](azure-maps-authentication.md) Verificatie beheren in Azure [Maps](how-to-manage-authentication.md)voor meer informatie over Azure Maps-verificatie.

<a name="software-development-kit-sdk"></a>**Software development kit (SDK)**: Een verzameling documentatie, voorbeeldcode en voorbeeld-apps om een ontwikkelaar te helpen een API te gebruiken om apps te bouwen.

<a name="spherical-mercator-projection"></a>**Bolvormige Mercator projectie**: Zie [Web Mercator](#web-mercator). 

<a name="spatial-query"></a>**Ruimtelijke query**: een aanvraag voor een service die een ruimtelijke bewerking uitvoert. Zoals een radiale zoekopdracht of langs een routezoeken.

<a name="spatial-reference"></a>**Ruimtelijke referentie**: een op coördinaten gebaseerd lokaal, regionaal of globaal systeem dat wordt gebruikt om geografische entiteiten nauwkeurig te lokaliseren. Het definieert het coördinatensysteem dat wordt gebruikt om kaartcoördinaten te relateren aan locaties in de echte wereld. Ruimtelijke verwijzingen zorgen ervoor dat ruimtelijke gegevens uit verschillende lagen of bronnen kunnen worden geïntegreerd voor nauwkeurige weergave of analyse. Azure Maps gebruikt het [EPSG:3857-coördinatenreferentiesysteem](https://epsg.io/3857) en WGS 84 voor invoergeometriegegevens.

<a name="sql-spatial"></a>**SQL-ruimtelijk:** verwijst naar de ruimtelijke functionaliteit die is ingebouwd in SQL Azure en SQL Server 2008 en hoger. Deze ruimtelijke functionaliteit is ook beschikbaar als een .NET-bibliotheek die onafhankelijk van SQL Server kan worden gebruikt. Zie voor meer informatie de [documentatie ruimtelijke gegevens (SQL Server)](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) voor meer informatie.

<a name="subscription-key"></a>**Abonnementssleutel:** Zie [Verificatie van gedeelde sleutels](#shared-key-authentication).

<a name="synchronous-request"></a>**Synchrone aanvraag:** een HTTP-aanvraag opent een verbinding en wacht op een antwoord. Browsers beperken het aantal gelijktijdige HTTP-aanvragen dat vanaf een pagina kan worden uitgevoerd. Als meerdere langlopende synchrone aanvragen tegelijkertijd worden gedaan, kan deze limiet worden bereikt. Aanvragen worden vertraagd totdat een van de andere aanvragen is voltooid.

## <a name="t"></a>T

<a name="telematics"></a>**Telematica**: Het verzenden, ontvangen en opslaan van informatie via telecommunicatieapparaten in combinatie met het uitvoeren van de bediening op externe objecten. 

<a name="temporal-data"></a>**Tijdelijke gegevens**: Gegevens die specifiek verwijzen naar tijden of datums. Tijdelijke gegevens kunnen betrekking hebben op afzonderlijke gebeurtenissen, zoals blikseminslagen; bewegende objecten, zoals treinen; of herhaalde waarnemingen, zoals tellingen van verkeerssensoren.

<a name="terrain"></a>**Terrein**: Een gebied met een bijzonder kenmerk, zoals zanderig terrein of bergachtig terrein.

<a name="thematic-maps"></a>**Thematische kaarten**: Een thematische kaart is een eenvoudige kaart gemaakt om een thema over een geografisch gebied weer te geven. Een veelvoorkomend scenario voor dit type kaart is het kleuren van de administratieve regio's, zoals landen/regio's op basis van een bepaalde metrische gegevens.

<a name="tile-layer"></a>**Tegellaag:** een laag die wordt weergegeven door kaarttegels (rechthoekige secties) in een doorlopende laag te monteren. De tegels zijn rasterafbeeldingstegels of vectortegels. Rastertegellagen worden meestal van tevoren weergegeven en worden opgeslagen als afbeeldingen op een server. Rastertegellagen kunnen een grote opslagruimte gebruiken. Vectortegellagen worden vrijwel realtime weergegeven in de clienttoepassing. De opslagvereisten aan de serverzijde zijn dus kleiner voor vectortegellagen.

<a name="time-zone"></a>**Tijdzone**: Een regio van de wereld die een uniforme standaardtijd voor juridische, commerciële en sociale doeleinden in acht neemt. Tijdzones hebben de neiging om de grenzen van landen/regio's en hun onderverdelingen te volgen.

<a name="transaction"></a>**Transactie:** Azure Maps gebruikt een transactioneel licentiemodel waar;

- Voor elke 15 gevraagde kaart- of verkeerstegels wordt één transactie gemaakt.
- Voor elke API-aanroep wordt één transactie gemaakt naar een van de services in Azure Maps. Zoeken en routeren zijn voorbeelden van Azure Maps-service.

<a name="transformation"></a>**Transformatie**: Het proces van het converteren van gegevens tussen verschillende geografische coördinatensystemen. U bijvoorbeeld bepaalde gegevens hebben die in het Verenigd Koninkrijk zijn vastgelegd en op basis van het geografische coördinatensysteem OSGB 1936. Azure Maps gebruikt de [epsg:3857-coördinatenreferentiesysteemvariant](https://epsg.io/3857) van WGS84. Als zodanig om de gegevens correct weer te geven, moet de coördinaten van het ene systeem naar het andere worden getransformeerd.

<a name="traveling-salesmen-problem-tsp"></a>**Traveling Salesmen Problem (TSP)**: Een Hamiltoniaanse circuit probleem waarbij een verkoper moet de meest efficiënte manier om een reeks stops te bezoeken te vinden, dan terug te keren naar de startlocatie.  

<a name="trilateration"></a>**Trilateratie**: Het proces van het bepalen van de positie van een punt op het aardoppervlak, ten opzichte van twee andere punten, door het meten van de afstanden tussen alle drie de punten.

<a name="turn-by-turn-navigation"></a>**Turn-by-turn navigatie:** een toepassing die route-instructies biedt voor elke stap van een route als de gebruikers de volgende manoeuvre nadert.

## <a name="v"></a>V

<a name="vector-data"></a>**Vectorgegevens:** coördineren van gegevens die worden weergegeven als punten, lijnen of veelhoeken.

<a name="vector-tile"></a>**Vectortegel**: een open gegevensspecificatie voor het opslaan van georuimtelijke vectorgegevens met hetzelfde tegelsysteem als het kaartbesturingselement. Zie ook [Tegellaag](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a>**Voertuig routing Problem (VRP)**: Een klasse van problemen, waarbij een reeks bestelde routes voor een wagenpark wordt berekend, rekening houdend met een reeks beperkingen. Deze beperkingen kunnen bestaan uit levertijdvensters, meerdere routecapaciteiten en beperkingen voor de reisduur.

<a name="voronoi-diagram"></a>**Voronoi diagram**: Een verdeling van de ruimte in gebieden, of cellen, die een set van geometrische objecten omringen, meestal punt functies. Deze cellen, of polygonen, moeten voldoen aan de criteria voor Delaunay driehoeken. Alle locaties binnen een gebied zijn dichter bij het object dat het omringt dan bij enig ander object in de set. Voronoi diagrammen worden vaak gebruikt om gebieden van invloed rond geografische kenmerken af te zetten. 

## <a name="w"></a>W

<a name="waypoint"></a>**Waypoint**: Een waypoint is een opgegeven geografische locatie gedefinieerd door lengte en breedte die wordt gebruikt voor navigatiedoeleinden. Vaak gebruikt om een punt te vertegenwoordigen waar iemand een route door navigeert.

<a name="waypoint-optimization"></a>**Waypoint-optimalisatie**: Het proces van het opnieuw ordenen van een set waypoints om de reistijd of afstand die nodig is om door alle opgegeven waypoints te komen, te minimaliseren. Afhankelijk van de complexiteit van de optimalisatie wordt deze optimalisatie vaak aangeduid als het [probleem van de reizende verkopers](#traveling-salesmen-problem-tsp) of [voertuigroutering.](#vehicle-routing-problem-vrp)

<a name="web-map-service-wms"></a>**Web Map Service (WMS)**: WMS is een Open Geographic Consortium (OGC) standaard die op afbeeldingen gebaseerde kaartservices definieert. WMS-services bieden kaartafbeeldingen voor specifieke gebieden binnen een kaart op aanvraag. Afbeeldingen bevatten vooraf gerenderde symboliek en kunnen worden weergegeven in een van de verschillende benoemde stijlen, indien gedefinieerd door de service.

<a name="web-mercator"></a>**Web Mercator**: Ook bekend als Sferische Mercator projectie. Het is een lichte variant van de Mercator projectie, een voornamelijk gebruikt in web-based mapping programma's. Het maakt gebruik van dezelfde formules als de standaard Mercator projectie als gebruikt voor kleinschalige kaarten. Echter, het web Mercator maakt gebruik van de bolvormige formules op alle schalen, maar grootschalige Mercator kaarten normaal gebruik maken van de ellipsvormige vorm van de projectie. De discrepantie is onmerkbaar op de globale schaal, maar het zorgt ervoor dat kaarten van lokale gebieden iets afwijken van echte ellipsvormige Mercator-kaarten, op dezelfde schaal.

<a name="wgs84"></a>**WGS84**: Een reeks constanten die worden gebruikt om ruimtelijke coördinaten te relateren aan locaties op het oppervlak van de kaart. De WGS84 datum is de standaard die wordt gebruikt door de meeste online mapping providers en GPS-apparaten. Azure Maps gebruikt de [epsg:3857-coördinatenreferentiesysteemvariant](https://epsg.io/3857) van WGS84.

## <a name="z"></a>Z

<a name="z-coordinate"></a>**Z-coördinaat**: Zie [Hoogte](#altitude). 

<a name="zip-code"></a>**Postcode**: Zie [Postcode](#postal-code).

<a name="Zoom level"></a>**Zoomniveau**: hiermee geeft u het detailniveau op en hoeveel van de kaart zichtbaar is. Wanneer ingezoomd helemaal naar niveau 0, zal de volledige wereldkaart vaak zichtbaar zijn. Maar de kaart toont beperkte details, zoals namen van het land/regio, grenzen en oceaannamen. Wanneer ingezoomd dichter bij niveau 17, zal de kaart een gebied van een paar stadsblokken met gedetailleerde weginformatie tonen. In azure maps is het hoogste zoomniveau 22. Zie [Zoomniveaus en tegelrasterdocumentatie voor](zoom-levels-and-tile-grid.md) meer informatie.

