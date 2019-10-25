---
title: Eenvoudige querysyntaxis
titleSuffix: Azure Cognitive Search
description: Verwijzing voor de eenvoudige query syntaxis die wordt gebruikt voor Zoek opdrachten in volledige tekst in azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
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
ms.openlocfilehash: fb98be9975de38ec9f65e723e078a1db8755b4ed
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792559"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Eenvoudige query syntaxis in azure Cognitive Search

Azure Cognitive Search implementeert twee op lucene gebaseerde query talen: [eenvoudige query-parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) en de [lucene query-parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). In azure Cognitive Search worden de opties voor fuzzy/helling uitgesloten van de eenvoudige query syntaxis.  

> [!NOTE]  
>  Azure Cognitive Search biedt een alternatieve [lucene-query syntaxis](query-lucene-syntax.md) voor complexere query's. Zie [Hoe zoeken in volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)voor meer informatie over het parseren van de architectuur en voor delen van query's voor elke syntaxis.

## <a name="how-to-invoke-simple-parsing"></a>Eenvoudige parsering aanroepen

Eenvoudige syntaxis is de standaard instelling. Aanroep is alleen nodig als u de syntaxis van volledig naar eenvoudig opnieuw instelt. Als u de syntaxis expliciet wilt instellen, gebruikt u de zoek parameter `queryType`. Geldige waarden zijn `simple|full`, met `simple` als de standaard waarde en `full` voor lucene. 

## <a name="query-behavior-anomalies"></a>Afwijkingen in het query gedrag

Alle tekst met een of meer voor waarden wordt beschouwd als een geldig start punt voor het uitvoeren van query's. Azure Cognitive Search komt overeen met documenten die een of meer van de voor waarden bevatten, inclusief eventuele variaties die tijdens de analyse van de tekst zijn gevonden. 

Net zoals dit klinkt, is er één aspect van de uitvoering van query's in azure Cognitive Search die *mogelijk* onverwachte resultaten oplevert, in plaats van de zoek resultaten te verminderen, omdat meer voor waarden en Opera tors worden toegevoegd aan de invoer teken reeks. Of deze uitbrei ding daad werkelijk plaatsvindt, is afhankelijk van de opname van een NOT-operator, gecombineerd met een `searchMode` parameter instelling die bepaalt hoe niet wordt geïnterpreteerd in termen van en of of gedrag. Gezien de standaard, `searchMode=Any`en een NOT-operator, wordt de bewerking berekend als een OR-actie, zodat `"New York" NOT Seattle` alle steden retourneert die niet Seattle zijn.  

Normaal gesp roken ziet u dit gedrag waarschijnlijk in gebruikers interactie patronen voor toepassingen die over inhoud zoeken, waarbij gebruikers waarschijnlijk een operator in een query bevatten, in tegens telling tot e-commerce sites met meer ingebouwde navigatie structuren. Zie [niet-operator](#not-operator)voor meer informatie. 

## <a name="boolean-operators-and-or-not"></a>Booleaanse Opera tors (en, of, niet) 

U kunt Opera tors insluiten in een query reeks om een uitgebreide set criteria te bouwen waarmee overeenkomende documenten worden gevonden. 

### <a name="and-operator-"></a>EN operator `+`

De operator AND is een plus teken. `wifi+luxury` zoekt bijvoorbeeld naar documenten die zowel `wifi` als `luxury`bevatten.

### <a name="or-operator-"></a>OF operator `|`

De operator OR is een verticale streep of een sluis teken. Zo zoekt `wifi | luxury` naar documenten met zowel `wifi` als `luxury` of beide.

<a name="not-operator"></a>

### <a name="not-operator--"></a>GEEN operator `-`

De operator NOT is een minteken. Zo zoekt `wifi –luxury` naar documenten die de `wifi`-term hebben en/of geen `luxury` (en/of worden beheerd door `searchMode`).

> [!NOTE]  
>  Met de optie `searchMode` bepaalt u of een term met de operator NOT ANDed of ORed met de andere voor waarden in de query in het afwezigheid van een `+` of `|` operator. Intrekken dat `searchMode` kan worden ingesteld op `any` (standaard) of `all`. Als u `any`gebruikt, wordt het intrekken van query's verg root door meer resultaten te gebruiken en wordt standaard `-` als ' of niet ' geïnterpreteerd. `wifi -luxury` komt bijvoorbeeld overeen met documenten die de term `wifi` bevatten of die die niet de term `luxury`bevatten. Als u `all`gebruikt, wordt de nauw keurigheid van query's verg root door minder resultaten op te nemen en standaard-wordt geïnterpreteerd als "en niet". `wifi -luxury` komt bijvoorbeeld overeen met documenten die de term `wifi` bevatten en niet de term ' luxe ' bevatten. Dit is weliswaar een meer intuïtief gedrag voor de operator `-`. Daarom kunt u overwegen `searchMode=all` te gebruiken in plaats van `searchMode=any` als u zoek acties wilt optimaliseren voor een nauw keurigheid in plaats van intrekken *en* uw gebruikers vaak de operator `-` gebruiken in Zoek opdrachten.

## <a name="suffix-operator"></a>Achtervoegsel operator

De operator suffix is een asterisk `*`. `lux*` zoekt bijvoorbeeld naar documenten die een term hebben die begint met `lux`, waarbij het hoofdletter gebruik wordt genegeerd.  

## <a name="phrase-search-operator"></a>Zoek operator voor woordgroepen

De operator phrase omsluit een woord groep tussen aanhalings tekens `" "`. Als `Roach Motel` bijvoorbeeld (zonder aanhalings tekens) zoekt naar documenten met `Roach` en/of `Motel` ergens in een wille keurige volg orde, worden `"Roach Motel"` (met aanhalings tekens) alleen gezocht in documenten die die gehele woord groep samen bevatten en in die volg orde (tekst analyse nog steeds van toepassing).

## <a name="precedence-operator"></a>Prioriteits operator

De operator voor rang omsluit de teken reeks tussen haakjes `( )`. Zo zoekt `motel+(wifi | luxury)` naar documenten met de Motel-term en ofwel `wifi` of `luxury` (of beide).  

## <a name="escaping-search-operators"></a>Zoek operatoren voor Escapes  

 Als u de bovenstaande symbolen wilt gebruiken als feitelijk deel van de Zoek tekst, moeten ze worden voorafgegaan door een back slash te maken. `luxury\+hotel` resulteert bijvoorbeeld in de term `luxury+hotel`. Er zijn twee uitzonde ringen op deze regel, waar Escapes niet nodig is om dingen eenvoudig te maken voor de meest voorkomende gevallen:  

- De operator NOT `-` hoeft alleen te worden ontsnapn als deze het eerste teken na witruimte is, niet als het midden van een term is. `wi-fi` is bijvoorbeeld een enkele term. Overwegende dat GUID'S (zoals `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) als één token worden beschouwd.
- De operator voor het achtervoegsel `*` moet alleen worden ontsnapt als het laatste teken vóór witruimte is, niet als het zich in het midden van een term bedoet. `wi*fi` wordt bijvoorbeeld beschouwd als één token.

> [!NOTE]  
>  Hoewel het samen voegen van tokens in beslag houdt, kan de tekst analyse deze splitsen, afhankelijk van de analyse modus. Zie [taal ondersteuning &#40;Azure Cognitive Search rest API&#41; ](index-add-language-analyzers.md) voor meer informatie.  

## <a name="see-also"></a>Zie ook  

+ [Zoeken in &#40;documenten Azure Cognitive Search rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene-querysyntaxis](query-lucene-syntax.md)
+ [Syntaxis voor OData-expressie](query-odata-filter-orderby-syntax.md) 
