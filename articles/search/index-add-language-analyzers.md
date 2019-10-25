---
title: Taal analyse functies toevoegen aan teken reeks velden in een index
titleSuffix: Azure Cognitive Search
description: Meertalige tekst analyse in meerdere talen voor niet-Engelse query's en indexen in azure Cognitive Search.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: f5833da5b15c893499b0d786972eff61c7391137
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790141"
---
# <a name="add-language-analyzers-to-an-azure-cognitive-search-index"></a>Taal analyse functies toevoegen aan een Azure Cognitive Search-index

Een *taal analyse* is een specifiek type [tekst analyse](search-analyzers.md) dat lexicale analyses uitvoert met behulp van de taal kundige regels van de doel taal. Elk doorzoekbaar veld heeft een eigenschap **Analyzer** . Als uw index vertaalde teken reeksen bevat, zoals afzonderlijke velden voor Engelse en Chinese tekst, kunt u taal analysen opgeven voor elk veld om toegang te krijgen tot de uitgebreide taal mogelijkheden van die analyse functies.  

Azure Cognitive Search ondersteunt 35-analyse functies die worden ondersteund door Lucene, en 50-analyse functies die worden ondersteund door een eigen micro soft-technologie voor natuurlijke taal verwerking die wordt gebruikt in Office en Bing.

## <a name="comparing-analyzers"></a>Analyse functies vergelijken

Sommige ontwikkel aars hebben mogelijk de voor keur aan een bekendere, eenvoudige, open-source oplossing van Lucene. De taal analysen van Lucene zijn sneller, maar de micro soft-analyse functies hebben geavanceerde mogelijkheden, zoals lemmatisering, het afmaken van woorden (in talen als Duits, Deens, Nederlands, Zweeds, Noors, Estland, finish, Hong aars, Slowaaks) en entiteit herkenning (Url's, e-mails, datums, cijfers). Voer, indien mogelijk, vergelijkingen uit van de micro soft-en lucene-analyse functies om te bepalen welke een beter aansluit. 

Indexeren met micro soft-analyse functies is gemiddeld twee tot drie keer langzamer dan hun bijbehorende Lucene, afhankelijk van de taal. Zoek prestaties moeten niet significant worden beïnvloed voor de gemiddelde grootte van query's. 

### <a name="english-analyzers"></a>Engelse analyse functies

De standaard-Analyzer is standaard-Lucene, die goed werkt voor het Engels, maar mogelijk niet ook en de Engelse analyse functie van Lucene of het Engelse analyse beleid van micro soft. 
 
+ De Engelse Analyzer van Lucene breidt de Standard Analyzer uit. Hiermee verwijdert u de possessives (van het begin) van woorden, wordt de functie voor het verbreken van de splitsing van het schema en de Engelse stop woorden verwijderd.  

+ De Engelse Analyzer van micro soft voert lemmatisering uit in plaats van de ontleding. Dit betekent dat het verbogen en onregelmatige woord vormen veel beter kan verwerken wat resulteert in meer relevante zoek resultaten 

## <a name="configuring-analyzers"></a>Analyse functies configureren

Taal analysen worden gebruikt als-is. Voor elk veld in de index definitie kunt u de eigenschap **Analyzer** instellen op een analyse naam die de taal en talen stack (micro soft of lucene) aangeeft. Dezelfde analyse wordt toegepast bij het indexeren en zoeken naar dat veld. U kunt bijvoorbeeld afzonderlijke velden voor Engelse, Franse en Spaanse Hotel beschrijvingen hebben die naast elkaar in dezelfde index bestaan. U kunt in plaats van **Analyzer**ook **indexAnalyzer** en **searchAnalyzer** gebruiken om verschillende analyse regels te hebben tijdens het indexeren en de query tijd. 

Gebruik de para meter **searchFields** om op te geven in welke taal specifiek veld moet worden gezocht in uw query's. U kunt query voorbeelden bekijken die de eigenschap Analyzer in [Zoek documenten](https://docs.microsoft.com/rest/api/searchservice/search-documents)bevatten. 

Zie [Create index &#40;Azure Cognitive Search rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)voor meer informatie over eigenschappen van de index. Zie voor meer informatie over het analyseren van Azure Cognitive Search analyse functies [in azure Cognitive Search](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Language Analyzer-lijst 
 Hieronder ziet u de lijst met ondersteunde talen in combi natie met de namen van Lucene en micro soft Analyzer.  

|Taal|Naam van micro soft Analyzer|Naam van Lucene Analyzer|  
|--------------|-----------------------------|--------------------------|  
|Arabisch|AR. micro soft|AR. lucene|  
|Armeens||HY. lucene|  
|Bengalese|bn. micro soft||  
|Baskisch||EU. lucene|  
|Bulgaars|bg. micro soft|bg. lucene|  
|Catalaans|ca. micro soft|ca. lucene|  
|Vereenvoudigd Chinees|zh-Hans. micro soft|zh-Hans. lucene|  
|Traditioneel Chinees|zh-hant. micro soft|zh-hant. lucene|  
|Kroatisch|HR. micro soft||  
|Tsjechisch|CS. micro soft|CS. lucene|  
|Deens|da. micro soft|da. lucene|  
|Nederlands|nl. micro soft|nl. lucene|  
|Nederlands|en. micro soft|en. lucene|  
|Estisch|et. micro soft||  
|Fins|fi. micro soft|fi. lucene|  
|Frans|fr. micro soft|fr. lucene|  
|Galicisch||gl. lucene|  
|Duits|de. micro soft|de. lucene|  
|Grieks|El. micro soft|El. lucene|  
|Gujarati|Gu. micro soft||  
|Hebreeuws|he. micro soft||  
|Hindi|Hallo. micro soft|Hallo. lucene|  
|Hongaars|hu. micro soft|hu. lucene|  
|IJslands|is. micro soft||  
|Indonesisch (Bahasa)|id. micro soft|id. lucene|  
|Iers||Ga. lucene|  
|Italiaans|it. micro soft|it. lucene|  
|Japans|Ja. micro soft|Ja. lucene|  
|Kannada|kn. micro soft||  
|Koreaans|ko. micro soft|ko. lucene|  
|Lets|LV. micro soft|LV. lucene|  
|Litouws|lt. micro soft||  
|Malajalam|ml. micro soft||  
|Maleis (Latijns)|MS. micro soft||  
|Marathi|Mr. micro soft||  
|Noors|NB. micro soft|No. lucene|  
|Perzisch||FA. lucene|  
|Pools|pl. micro soft|pl. lucene|  
|Portugees (Brazilië)|pt-br. micro soft|pt-br. lucene|  
|Portugees (Portugal)|pt-pt. micro soft|pt-pt. lucene|  
|Punjabi|pa. micro soft||  
|Roemeens|ro. micro soft|ro. lucene|  
|Russisch|ru. micro soft|ru. lucene|  
|Servisch (Cyrillisch)|SR-Cyrillisch. micro soft||  
|Servisch (Latijns)|SR-Latijn. micro soft||  
|Slowaaks|SK. micro soft||  
|Sloveens|SL. micro soft||  
|Spaans|es. micro soft|es. lucene|  
|Zweeds|SV. micro soft|SV. lucene|  
|Tamil|ta. micro soft||  
|Telugu|te. micro soft||  
|Thais|th. micro soft|th. lucene|  
|Turks|tr. micro soft|tr. lucene|  
|Oekraïens|UK. micro soft||  
|Urdu|uw. micro soft||  
|Vietnamees|VI. micro soft||  

 Alle analyse functies met namen die zijn gekoppeld aan **lucene** , worden aangedreven door [de taal Analyseers van Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Zie ook  

+ [Index &#40;van Azure Cognitive Search rest API maken&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [Klasse Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

