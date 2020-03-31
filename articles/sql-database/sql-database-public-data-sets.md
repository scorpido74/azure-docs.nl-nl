---
title: Openbare gegevenssets voor Azure-analyses
description: Meer informatie over openbare gegevenssets die u gebruiken om Azure-analyseservices en -oplossingen te prototypen en te testen.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2018
ms.openlocfilehash: 4b1c1a963b065411f1a0ab84141bdf1835930ebb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973540"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>Openbare gegevenssets voor testen en prototypen

Blader door deze lijst met openbare gegevenssets voor gegevens die u gebruiken om opslag- en analyseservices en -oplossingen te prototypen en te testen.

## <a name="us-government-and-agency-data"></a>Gegevens van de Amerikaanse overheid en agentschap

| Gegevensbron | Over de gegevens | Over de bestanden |
|---|---|---|
| [Gegevens van de Amerikaanse regering](https://catalog.data.gov/dataset) | Meer dan 250.000 datasets voor landbouw, klimaat, consument, ecosystemen, onderwijs, energie, financiën, gezondheid, lokale overheid, productie, maritiem, oceaan, openbare veiligheid en wetenschap en onderzoek in de VS. | Bestanden van verschillende groottes in verschillende indelingen, waaronder HTML, XML, CSV, JSON, Excel en vele anderen. U beschikbare gegevenssets filteren op bestandsindeling. |
| [Amerikaanse volkstellingsgegevens](https://www.census.gov/data.html) | Statistische gegevens over de bevolking van de VS | Gegevenssets zijn in verschillende formaten. |
| [Aardwetenschappelijke gegevens van NASA](https://earthdata.nasa.gov/) | Meer dan 32.000 gegevensverzamelingen over landbouw, atmosfeer, biosfeer, klimaat, cryosfeer, menselijke dimensies, hydrosfeer, landoppervlak, oceanen, interacties tussen zon en aarde en meer. | Gegevenssets zijn in verschillende formaten. |
| [Vertragingen van vluchten van luchtvaartmaatschappijen en andere vervoersgegevens](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "Het U.S. Department of Transportation's (DOT) Bureau of Transportation Statistics (BTS) volgt de prestaties op tijd van binnenlandse vluchten uitgevoerd door grote luchtvaartmaatschappijen. Summiere informatie over het aantal on-time, vertraagde, geannuleerde en omgeleide vluchten verschijnt ... samengevatte tabellen geplaatst op deze website." | Bestanden zijn in CSV-indeling. |
| [Verkeersdoden - US Fatality Analysis Reporting System (FARS)](https://www.nhtsa.gov/FARS) | "FARS is een landelijke volkstelling die NHTSA, het Congres, en de Amerikaanse openbare jaarlijkse gegevens over dodelijke verwondingen geleden in auto-verkeersongevallen." | "Maak uw eigen fatality gegevens online uitgevoerd met behulp van de FARS Query System. Of download alle FARS-gegevens uit 1975 om te presenteren vanaf de FTP-site." |
| [Toxische chemische gegevens - EPA Toxiciteit ForeCaster (ToxCast™) gegevens](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "EPA's meest bijgewerkte, openbaar beschikbare toxiciteitsgegevens met hoge doorvoer over duizenden chemische stoffen. Deze gegevens worden gegenereerd door de ToxCast-onderzoeksinspanning van de EPA." | Gegevenssets zijn beschikbaar in verschillende indelingen, waaronder spreadsheets, R-pakketten en MySQL-databasebestanden. |
| [Toxische chemische gegevens - NIH Tox21 Data Challenge 2014](https://tripod.nih.gov/tox21/challenge/) | "De 2014 Tox21 data uitdaging is ontworpen om wetenschappers te helpen begrijpen het potentieel van de chemische stoffen en verbindingen worden getest door middel van de toxicologie in de 21e eeuw initiatief om biologische paden te verstoren op een manier die kan leiden tot toxische effecten." | Datasets zijn beschikbaar in SMILES- en SDF-formaten. De gegevens bieden "testactiviteitsgegevens en chemische structuren op de Tox21-verzameling van ~ 10.000 verbindingen (Tox21 10K)." |
| [Biotechnologie en genoomgegevens van de NCBI](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Meerdere datasets voor genen, genomen en eiwitten. | Gegevenssets zijn in tekst, XML, BLAST en andere indelingen. Er is een BLAST-app beschikbaar. |

## <a name="other-statistical-and-scientific-data"></a>Andere statistische en wetenschappelijke gegevens

| Gegevensbron | Over de gegevens | Over de bestanden |
|---|---|---|
| [New York City taxi gegevens](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | "Taxiritrecords omvatten velden die ophaal- en afhaaldatums/-tijden vastleggen, ophaal- en afleverlocaties, reisafstanden, gespecificeerde tarieven, tarieftypen, betalingstypen en passagiersaantallen die door de chauffeur zijn gerapporteerd." | Gegevenssets bevinden zich per maand in CSV-bestanden. |
| [Microsoft Research-gegevenssets - "Data Science for Research"](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | Meerdere datasets voor mens-computer interactie, audio/video, data mining/information retrieval, geospatial/locatie, natural language processing en robotica/computer vision. | Datasets zijn in verschillende formaten, ritsen om te downloaden. |
| [Openbare genoomgegevens](https://www.completegenomics.com/public-data/) | "Een diverse gegevensreeks van gehele menselijke genomen is vrij beschikbaar voor openbaar gebruik om om het even welke genomische studie te verbeteren..." De aanbieder, Complete Genomics, is een private for-profit onderneming. | Gegevenssets zijn na extractie in UNIX-tekstindeling. Analysetools zijn ook beschikbaar. |
| [Open Science Data Cloud-gegevens](https://www.opensciencedatacloud.org/projects/) | "De Open Science Data Cloud biedt de wetenschappelijke gemeenschap middelen voor het opslaan, delen en analyseren van wetenschappelijke datasets op terabyte- en petabyteschaal."| Gegevenssets zijn in verschillende formaten. |
| [Wereldwijde klimaatgegevens - WorldClim](https://worldclim.org/) | "WorldClim is een set van wereldwijde klimaatlagen (gerasterd klimaatgegevens) met een ruimtelijke resolutie van ongeveer 1 km2. Deze gegevens kunnen worden gebruikt voor het in kaart brengen en ruimtelijke modellering." | Deze bestanden bevatten georuimtelijke gegevens. Zie [Gegevensindeling](https://worldclim.org/formats1)voor meer informatie . |
| [Gegevens over de menselijke samenleving - The GDELT Project](https://www.gdeltproject.org/data.html) | "Het GDELT Project is de grootste, meest uitgebreide en hoogste resolutie open database van de menselijke samenleving ooit gemaakt." | De raw-gegevensbestanden zijn in CSV-indeling. |
| [Advertentie klik voorspelling gegevens voor machine learning van Criteo](https://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "De grootste ooit openbaar uitgebrachte ML dataset." Zie [criteo's 1 TB Klikvoorspellingsset](https://blogs.technet.microsoft.com/machinelearning/20../../now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/)voor meer informatie. | |
| [ClueWeb09 text mining dataset van The Lemur Project](https://www.lemurproject.org/clueweb09.php/) | "De ClueWeb09 dataset is gemaakt ter ondersteuning van onderzoek naar het ophalen van informatie en aanverwante menselijke taaltechnologieën. Het bestaat uit ongeveer 1 miljard webpagina's in 10 talen die werden verzameld in januari en februari 2009." | Zie [Gegevenssetgegevens](https://www.lemurproject.org/clueweb09/datasetInformation.php).|

## <a name="online-service-data"></a>Online servicegegevens

| Gegevensbron | Over de gegevens | Over de bestanden |
|---|---|---|
| [GitHub-archief](https://www.githubarchive.org/) | "GitHub Archive is een project om de openbare GitHub-tijdlijn [van gebeurtenissen] op te nemen en het gemakkelijk toegankelijk te maken voor verdere analyse." | Download JSON-gecodeerde gebeurtenisarchieven in .gz (Gzip)-indeling van een webclient. |
| [GitHub-activiteitsgegevens van het GHTorrent-project](http://ghtorrent.org/) | "Het GHTorrent-project [is] een poging om een schaalbare, opvraagbare, offline spiegel van gegevens te maken die worden aangeboden via de GitHub REST API. GHTorrent bewaakt de GitHub public event tijdlijn. Voor elke gebeurtenis haalt het de inhoud en hun afhankelijkheden volledig terug." | MySQL-databasedumps zijn in CSV-indeling. |
| [Stack Overflow-gegevensdump](https://archive.org/details/stackexchange) | "Dit is een geanonimiseerde dump van alle door de gebruiker bijgedragen inhoud op het Stack Exchange-netwerk [inclusief Stack Overflow]." | "Elke site [zoals Stack Overflow] is opgemaakt als een apart archief bestaande uit XML-bestanden geritst via 7-zip met behulp van bzip2 compressie. Elk sitearchief bevat berichten, gebruikers, stemmen, opmerkingen, postgeschiedenis en PostLinks." |
