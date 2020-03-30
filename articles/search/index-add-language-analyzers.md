---
title: Taalanalysers toevoegen aan tekenreeksvelden
titleSuffix: Azure Cognitive Search
description: Meertalige lexicale tekstanalyse voor niet-Engelse query's en indexen in Azure Cognitive Search.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
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
ms.openlocfilehash: a97bee27b74aa211b4d4d56547726555edefa87a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283145"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Taalanalysers toevoegen aan tekenreeksvelden in een Azure Cognitive Search-index

Een *taalanalyzer* is een specifiek type [tekstanalyzer](search-analyzers.md) dat lexicale analyses uitvoert met behulp van de taalregels van de doeltaal. Elk doorzoekbaar veld heeft een **analyzer** eigenschap. Als uw index vertaalde tekenreeksen bevat, zoals afzonderlijke velden voor Engelse en Chinese tekst, u taalanalysers op elk veld opgeven om toegang te krijgen tot de rijke taalkundige mogelijkheden van deze analysers.  

Azure Cognitive Search ondersteunt 35 analyzers ondersteund door Lucene en 50 analyzers ondersteund door gepatenteerde Microsoft natural language processing technologie die wordt gebruikt in Office en Bing.

## <a name="comparing-analyzers"></a>Analysers vergelijken

Sommige ontwikkelaars geven misschien de voorkeur aan de meer bekende, eenvoudige, open-source oplossing van Lucene. Lucene taalanalysers zijn sneller, maar de Microsoft-analysators hebben geavanceerde mogelijkheden, zoals lemmatisatie, woorddecompounding (in talen als Duits, Deens, Nederlands, Zweeds, Noors, Ests, Afwerking, Hongaars, Slowaaks) en entiteit herkenning (URL's, e-mails, data, getallen). Indien mogelijk, moet u uit vergelijkingen van zowel de Microsoft en Lucene analyzers om te beslissen welke is een betere pasvorm. 

Indexeren met Microsoft-analysers is gemiddeld twee tot drie keer langzamer dan hun Lucene-equivalenten, afhankelijk van de taal. Zoekprestaties mogen niet significant worden beïnvloed voor query's met een gemiddelde grootte. 

### <a name="english-analyzers"></a>Engelse analysers

De standaard analyzer is Standard Lucene, die goed werkt voor het Engels, maar misschien niet zo goed als Lucene's Engels analyzer of Microsoft's Engels analyzer. 
 
+ Lucene's Engelse analyzer breidt de standaard analyzer uit. Het verwijdert bezitters (trailing 's) van woorden, past stemming volgens Porter Stemming algoritme, en verwijdert Engels stop woorden.  

+ Microsoft's Engels analyzer voert lemmatization in plaats van stemming. Dit betekent dat het kan omgaan met verbogen en onregelmatige woordvormen veel beter wat resulteert in meer relevante zoekresultaten 

## <a name="configuring-analyzers"></a>Analysers configureren

Taalanalysers worden gebruikt zoals het is. Voor elk veld in de indexdefinitie u de eigenschap **analyzer** instellen op een analyzernaam die de taal- en taalstapel (Microsoft of Lucene) opgeeft. Dezelfde analyzer wordt toegepast bij het indexeren en zoeken naar dat veld. U bijvoorbeeld afzonderlijke velden hebben voor Engelse, Franse en Spaanse hotelbeschrijvingen die naast elkaar in dezelfde index bestaan.

> [!NOTE]
> Het is niet mogelijk om een andere taalanalyzer te gebruiken op indexeringstijd dan op querytijd voor een veld. Die mogelijkheid is gereserveerd voor [aangepaste analysers.](index-add-custom-analyzers.md) Als u daarom de eigenschappen **searchAnalyzer** of **indexAnalyzer** probeert in te stellen op de naam van een taalanalyzer, geeft de REST-API een foutreactie. U moet in plaats daarvan de eigenschap **analyzer** gebruiken.

Gebruik de queryparameter **searchFields** om op te geven tegen welk taalspecifiek veld u wilt zoeken in uw query's. U queryvoorbeelden bekijken die de eigenschap analyzer bevatten in [Zoekdocumenten](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Zie Index &#40;Azure [Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)voor meer informatie over indexeigenschappen. Zie [Analysein Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-analyzers)voor meer informatie over analyse in Azure Cognitive Search.

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Lijst met taalanalyse 
 Hieronder vindt u de lijst met ondersteunde talen samen met namen van Lucene en Microsoft analyzer.  

|Taal|Naam Microsoft Analyzer|Lucene Analyzer Naam|  
|--------------|-----------------------------|--------------------------|  
|Arabisch|ar.microsoft|ar.lucene|  
|Armeens||hy.lucene hy.lucene|  
|Bangla|bn.microsoft||  
|Baskisch||eu.lucene|  
|Bulgaars|bg.microsoft|bg.lucene|  
|Catalaans|ca.microsoft|ca.lucene|  
|Vereenvoudigd Chinees|zh-Hans.microsoft|zh-Hans.lucene|  
|Traditioneel Chinees|zh-Hant.microsoft|zh-Hant.lucene|  
|Kroatisch|hr.microsoft||  
|Tsjechisch|cs.microsoft|cs.lucene|  
|Deens|da.microsoft|da.lucene|  
|Nederlands|nl.microsoft|nl.lucene|  
|Engels|nl.microsoft|nl.lucene|  
|Ests|et.microsoft||  
|Fins|fi.microsoft fi.microsoft|fi.lucene|  
|Frans|fr.microsoft fr.microsoft|fr.lucene|  
|Galicisch||gl.lucene gl.lucene|  
|Duits|de.microsoft|de.lucene|  
|Grieks|el.microsoft|el.lucene el.lucene|  
|Gujarati|gu.microsoft gu.microsoft||  
|Hebreeuws|he.microsoft he.microsoft he.microsoft he.||  
|Hindi|hi.microsoft|hi.lucene|  
|Hongaars|hu.microsoft hu.microsoft|hu.lucene hu.lucene|  
|IJslands|is.microsoft||  
|Indonesisch (Bahasa)|id.microsoft|id.lucene|  
|Iers||ga.lucene ga.lucene|  
|Italiaans|it.microsoft|it.lucene|  
|Japans|ja.microsoft|ja.lucene|  
|Kannada|kn.microsoft||  
|Koreaans|ko.microsoft ko.microsoft|ko.lucene ko.lucene|  
|Lets|lv.microsoft|lv.lucene|  
|Litouws|lt.microsoft lt.microsoft||  
|Malajalam|ml.microsoft||  
|Maleis (Latijn)|ms.microsoft ms.microsoft||  
|Marathi|mr.microsoft||  
|Norwegian|nb.microsoft nb.microsoft|no.lucene|  
|Perzisch||fa.lucene|  
|Pools|pl.microsoft pl.microsoft|pl.lucene|  
|Portugees (Brazilië)|pt-Br.microsoft|pt-Br.lucene|  
|Portugees (Portugal)|pt-Pt.microsoft|pt-Pt.lucene|  
|Punjabi|pa.microsoft||  
|Roemeens|ro.microsoft|ro.lucene|  
|Russisch|ru.microsoft|ru.lucene|  
|Servisch (Cyrillisch)|sr-cyrillic.microsoft||  
|Servisch (Latijns)|sr-latin.microsoft||  
|Slowaaks|sk.microsoft||  
|Sloveens|sl.microsoft sl.microsoft sl.microsoft||  
|Spaans|es.microsoft|es.lucene|  
|Zweeds|sv.microsoft (sv.microsoft)|sv.lucene (sv.lucene)|  
|Tamil|ta.microsoft||  
|Telugu|te.microsoft||  
|Thai|th.microsoft th.microsoft|th.lucene|  
|Turks|tr.microsoft|tr.lucene|  
|Oekraïens|uk.microsoft||  
|Urdu|your.microsoft||  
|Vietnamees|vi.microsoft vi.microsoft||  

 Alle analysers met namen geannoteerd met **Lucene** worden aangedreven door [Apache Lucene's taalanalysers.](https://lucene.apache.org/core/6_6_1/core/overview-summary.html )

## <a name="see-also"></a>Zie ook  

+ [Index &#40;Azure Cognitive Search REST&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [AnalyzerName, klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

