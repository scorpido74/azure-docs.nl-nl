---
title: Query-expressie syntaxis-Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de query-expressie syntaxis in de Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 4056ecba7ac80436952228da9e1b74dc7382448c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448952"
---
# <a name="query-expression-syntax"></a>Query-expressie syntaxis

We hebben gezien dat het antwoord op een aanvraag voor het **interpreteren** een query-expressie bevat. De grammatica waarmee de query van de gebruiker wordt geïnterpreteerd, heeft voor elke interpretatie een query-expressie gemaakt. Een query-expressie kan vervolgens worden gebruikt om een **Evaluate** -aanvraag uit te geven voor het ophalen van de zoek resultaten van de entiteit.

U kunt ook uw eigen query-expressies maken en deze gebruiken in een **Evalueer** aanvraag. Dit kan handig zijn als u uw eigen gebruikers interface bouwt die een query-expressie maakt als reactie op de acties van de gebruiker. Hiervoor moet u de syntaxis voor query-expressies kennen.  

Elk kenmerk entity dat kan worden opgenomen in een query-expressie, heeft een specifiek gegevens type en een reeks mogelijke query-Opera tors. De set entiteits kenmerken en ondersteunde Opera tors voor elk kenmerk wordt opgegeven in [entiteits kenmerken](EntityAttributes.md). Voor een query met één waarde moet het kenmerk de bewerking *equals* ondersteunen. Voor een voorvoegsel query moet het kenmerk voor de *StartsWith* -bewerking worden ondersteund. Voor numerieke bereik query's moet het kenmerk voor de *IsBetween* -bewerking worden ondersteund.

Sommige gegevens van de entiteit worden opgeslagen als samengestelde kenmerken, zoals aangegeven door een punt. in de naam van het kenmerk. Bijvoorbeeld: auteur/relatie-informatie wordt weer gegeven als een samengesteld kenmerk. Het bevat vier onderdelen: AuN, AuId, AfN, AfId. Deze onderdelen zijn afzonderlijke stukjes gegevens die één entiteit kenmerk waarde vormen.

Opmerking: alle query-expressies moeten in kleine letters en zonder speciale tekens worden genoteerd.

**Teken reeks kenmerk: enkele waarde** (inclusief overeenkomsten voor synoniemen)  
TI = ' indexeren door latente semantische analyse '  
Composiet (AA. AuN = ' Suzan Dumais ')

**Teken reeks kenmerk: exacte enkelvoudige waarde** (komt alleen overeen met canonieke waarden)  
TI = = ' indexeren door latente semantische analyse '  
Composiet (AA. AuN = = ' Susan t Dumais ')
     
**Teken reeks kenmerk: voorvoegsel waarde**   
TI = ' indexeren door latente Seman '...  
Composiet (AA. AuN = "Suzan du"...)

**Numeriek kenmerk: enkele waarde**  
Y = 2010
 
**Numeriek kenmerk: bereik waarde**  
Y>2005  
Y>=2005  
Y < 2010  
Y < = 2010  
Y =\[2010, 2012\) (bevat alleen links grens waarde: 2010, 2011)  
Y =\[2010, 2012\] (inclusief grens waarden: 2010, 2011, 2012)
 
**Numeriek kenmerk: voorvoegsel waarde**  
Y = ' 19 '... (wille keurige numerieke waarde die begint met 19) 
 
**Datum kenmerk: enkele waarde**  
D='2010-02-04'

**Date-kenmerk: bereik waarde**  
D>'2010-02-03'  
D = [' 2010-02-03 ', ' 2010-02-05 ']

**En/of Query's:**  
En (Y = 1985, Ti = ' ongeordende elektronische systemen ')  
Of (TI = ' ongeordende elektronische systemen ', Ti = ' fout tolerantie principes en-prak tijken ')  
En (of (Y = 1985, Y = 2008), Ti = ' ongeordende elektronische systemen ')
 
**Samengestelde Query's:**  
Als u een query wilt uitvoeren op onderdelen van een samengesteld kenmerk, moet u het deel van de query-expressie dat verwijst naar het samengestelde kenmerk in de functie Composite () insluiten. 

Als u bijvoorbeeld wilt zoeken naar documenten op naam auteur, gebruikt u de volgende query:
```
Composite(AA.AuN='mike smith')
```
<br>Als u een query wilt uitvoeren op documenten van een bepaalde auteur terwijl de auteur bij een bepaald Instituut was, gebruikt u de volgende query:
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>De functie samengestelde () verbindt de twee delen van het samengestelde kenmerk samen. Dit betekent dat we alleen papier krijgen waarin een van de auteurs "Mike Smith" is, terwijl hij op Harvard was. 

Als u een query wilt uitvoeren voor documenten door een bepaalde auteur in connecties met (andere) auteurs van een bepaald Instituut, gebruikt u de volgende query:
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>Omdat samengestelde () in deze versie wordt toegepast op de auteur en de relatie afzonderlijk vóór en (), krijgen we alle documenten te zien waarbij een van de auteurs "Mike Smith" is en een van de Connecties van de auteurs "Harvard" is. Dit is vergelijkbaar met het vorige query voorbeeld, maar dit is niet hetzelfde.

In het algemeen moet u rekening houden met het volgende voor beeld: we hebben een samengesteld kenmerk C met twee onderdelen A en B. Een entiteit kan meerdere waarden voor C hebben. Dit zijn onze entiteiten:
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>De query 
```
Composite(And(C.A=1, C.B=2))
```

<br>komt alleen overeen met entiteiten met een waarde voor C waarbij component C. A 1 is en onderdeel C. B is 2. Alleen E1 komt overeen met deze query.

De query 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>komt overeen met entiteiten met een waarde voor C waarbij C. A 1 is en ook een waarde heeft voor C waarbij C. B 2 is. Zowel E1 als E2 komen overeen met deze query.

Opmerking:  
- U kunt niet verwijzen naar een deel van een samengesteld kenmerk buiten een samengestelde ()-functie.
- U kunt niet verwijzen naar onderdelen van twee verschillende samengestelde kenmerken binnen dezelfde samengestelde functie ().
- U kunt niet verwijzen naar een kenmerk dat geen deel uitmaakt van een samengesteld kenmerk binnen een samengestelde ()-functie.
