---
title: Eenvoudige query syntaxis-Azure Search
description: Verwijzing voor de eenvoudige query syntaxis die wordt gebruikt voor Zoek opdrachten in volledige tekst in Azure Search.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/08/2019
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: e6c5ea86534001e0e5de2b02c4151af70631e4ef
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650013"
---
# <a name="simple-query-syntax-in-azure-search"></a>Eenvoudige query syntaxis in Azure Search
Azure Search implementeert twee op lucene gebaseerde query talen: [Eenvoudige query-parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) en de [lucene query-parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). In Azure Search sluit de eenvoudige query syntaxis de opties voor fuzzy/helling uit.  

> [!NOTE]  
>  Azure Search biedt een alternatieve [lucene-query syntaxis](query-lucene-syntax.md) voor complexere query's. Zie [Hoe zoeken in volledige tekst werkt in azure Search](search-lucene-query-architecture.md)voor meer informatie over het parseren van de architectuur en voor delen van query's voor elke syntaxis.

## <a name="how-to-invoke-simple-parsing"></a>Eenvoudige parsering aanroepen

Eenvoudige syntaxis is de standaard instelling. Aanroep is alleen nodig als u de syntaxis van volledig naar eenvoudig opnieuw instelt. Als u de syntaxis expliciet wilt instellen, `queryType` gebruikt u de zoek parameter. Geldige waarden zijn `simple|full`onder andere `simple` de standaard waarde en `full` voor lucene. 

## <a name="query-behavior-anomalies"></a>Afwijkingen in het query gedrag

Alle tekst met een of meer voor waarden wordt beschouwd als een geldig start punt voor het uitvoeren van query's. Azure Search komen overeen met documenten die een of meer van de voor waarden bevatten, inclusief eventuele variaties die tijdens de analyse van de tekst zijn gevonden. 

Net als bij deze geluiden is er één aspect van de uitvoering van query's in Azure Search die *mogelijk* onverwachte resultaten opleveren, in plaats van de zoek resultaten te verminderen, omdat meer voor waarden en Opera tors worden toegevoegd aan de invoer teken reeks. Of deze uitbrei ding daad werkelijk plaatsvindt, is afhankelijk van de opname van een not- `searchMode` operator, gecombineerd met een parameter instelling die bepaalt hoe niet wordt geïnterpreteerd in termen van en of gedrag. Gezien de standaard, `searchMode=Any`, en een not-operator, wordt de bewerking berekend als een or-actie, zodat `"New York" NOT Seattle` alle steden worden geretourneerd die niet Seattle zijn.  

Normaal gesp roken ziet u dit gedrag waarschijnlijk in gebruikers interactie patronen voor toepassingen die over inhoud zoeken, waarbij gebruikers waarschijnlijk een operator in een query bevatten, in tegens telling tot e-commerce sites met meer ingebouwde navigatie structuren. Zie [niet-operator](#not-operator)voor meer informatie. 

## <a name="boolean-operators-and-or-not"></a>Booleaanse Opera tors (en, of, niet) 

U kunt Opera tors insluiten in een query reeks om een uitgebreide set criteria te bouwen waarmee overeenkomende documenten worden gevonden. 

### <a name="and-operator-"></a>Operator en`+`

De operator AND is een plus teken. Zoekt bijvoorbeeld `wifi+luxury` naar documenten met zowel `wifi` als `luxury`.

### <a name="or-operator-"></a>OR-operator`|`

De operator OR is een verticale streep of een sluis teken. Zoekt bijvoorbeeld `wifi | luxury` naar documenten met ofwel `wifi` of `luxury` beide.

<a name="not-operator"></a>

### <a name="not-operator--"></a>GEEN operator`-`

De operator NOT is een minteken. Zoekt bijvoorbeeld `wifi –luxury` naar documenten die de `wifi` term en/of niet hebben `luxury` (en/of worden beheerd door `searchMode`).

> [!NOTE]  
>  Met `searchMode` deze optie bepaalt u of een term met de operator Not ANDed of ORed met de andere voor waarden in de query in het afwezigheid `+` van `|` een or-operator. Intrekken `searchMode` dat kan worden ingesteld op `any` ofwel (standaard) `all`of. Als u gebruikt `any`, wordt het intrekken van query's verhoogd door meer resultaten te gebruiken en wordt standaard `-` geïnterpreteerd als "of niet". Komt bijvoorbeeld `wifi -luxury` overeen met documenten die `wifi` de voor waarden bevatten of die de term `luxury`niet bevatten. Als u gebruikt `all`, wordt de nauw keurigheid van query's verg root door minder resultaten op te nemen en standaard-wordt geïnterpreteerd als "en niet". Komt bijvoorbeeld `wifi -luxury` overeen met documenten die de term `wifi` bevatten en niet de term ' luxe ' bevatten. Dit is weliswaar een meer intuïtief gedrag voor `-` de operator. Daarom kunt u overwegen in plaats `searchMode=all` van `searchMode=any` te gebruiken als u zoek acties wilt optimaliseren in plaats van intrekken, *en* uw gebruikers vaak de `-` operator gebruiken in Zoek opdrachten.

## <a name="suffix-operator"></a>Achtervoegsel operator

De operator suffix is een sterretje `*`. Zoekt bijvoorbeeld `lux*` naar documenten met een term die begint met `lux`, en negeert de aanvraag.  

## <a name="phrase-search-operator"></a>Zoek operator voor woordgroepen

De operator phrase omsluit een woord groep tussen aanhalings `" "`tekens. Bijvoorbeeld, terwijl `Roach Motel` (zonder aanhalings tekens) zoekt naar `Roach` documenten die en/of `Motel` ergens in een wille `"Roach Motel"` keurige volg orde staan (met aanhalings tekens) komen alleen overeen met documenten die die hele woord groep samen bevatten en in die Volg orde (tekst analyse is nog steeds van toepassing).

## <a name="precedence-operator"></a>Prioriteits operator

De operator voor rang omsluit de teken reeks tussen haakjes `( )`. Zoekt bijvoorbeeld `motel+(wifi | luxury)` naar documenten met de Motel-term en ofwel `wifi` of `luxury` (of beide).  

## <a name="escaping-search-operators"></a>Zoek operatoren voor Escapes  

 Als u de bovenstaande symbolen wilt gebruiken als feitelijk deel van de Zoek tekst, moeten ze worden voorafgegaan door een back slash te maken. Resulteert bijvoorbeeld `luxury\+hotel` in de term `luxury+hotel`. Er zijn twee uitzonde ringen op deze regel, waar Escapes niet nodig is om dingen eenvoudig te maken voor de meest voorkomende gevallen:  

- De operator `-` not moet alleen worden ontsnapd als het eerste teken na witruimte is, niet als het zich in het midden van een term bedoet. Een voor beeld `wi-fi` : een enkele term; overwegende dat guid's ( `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`zoals) als één token worden beschouwd.
- De operator `*` achtervoegsel moet alleen worden ontsnapt als dit het laatste teken vóór spatie is, niet als het midden van een term is. `wi*fi` Wordt bijvoorbeeld beschouwd als één token.

> [!NOTE]  
>  Hoewel het samen voegen van tokens in beslag houdt, kan de tekst analyse deze splitsen, afhankelijk van de analyse modus. Zie [taal ondersteuning &#40;Azure Search service rest API&#41; ](index-add-language-analyzers.md) voor meer informatie.  

## <a name="see-also"></a>Zie ook  

+ [Zoeken naar &#40;documenten Azure Search service rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene-querysyntaxis](query-lucene-syntax.md)
+ [Syntaxis voor OData-expressie](query-odata-filter-orderby-syntax.md) 
