---
title: Querysyntaxis voor grafieken zoeken
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de zoek syntaxis in Azure Machine Learning Designer voor het zoeken naar knoop punten in in pijplijn diagram.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 8/24/2020
ms.openlocfilehash: 762581ea5b3183d62913e9ea6935bf7e4c4ae67f
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420764"
---
# <a name="graph-search-query-syntax"></a>Querysyntaxis voor grafieken zoeken

In dit artikel vindt u informatie over de syntaxis van de zoek query in de grafiek in Azure Machine Learning. Met de functie voor het zoeken in de grafiek kunt u zoeken naar een knoop punt op basis van de naam en eigenschappen. 

 ![Scherm afbeelding met animatie waarin een voor beeld van een voorbeeld grafiek wordt weer gegeven](media/search/graph-search.gif)

De functie voor het zoeken in Zoek opdrachten in volledige tekst in de knooppunt naam en opmerkingen wordt ondersteund. U kunt ook filteren op eigenschap node, zoals runStatus, duration, computeTarget. Het tref woord zoeken is gebaseerd op de Lucene-query. Een volledige zoek opdracht ziet er als volgt uit:  

**[Lucene-query | [filter query]** 

U kunt een lucene-query of filter query gebruiken. Als u beide wilt gebruiken, gebruikt u het **|** scheidings teken. De syntaxis van de filter query is strikter dan de Lucene-query. Dus als de invoer van de klant kan worden geparseerd, wordt de filter query toegepast.

 

## <a name="lucene-query"></a>Lucene-query

De functie voor het zoeken in grafieken maakt gebruik van een eenvoudige query voor het zoeken in volledige tekst in het knoop punt ' naam ' en ' Opmerking '. De volgende lucene-Opera tors worden ondersteund:

 
- EN/OF
- Joker tekens die overeenkomen met **?** en * *\** _ Opera tors.

### <a name="examples"></a>Voorbeelden

- Eenvoudige zoek opdracht: `JSON Data`

- EN/OF: `JSON AND Validation`

- Meerdere en/of: `(JSON AND Validation) OR (TSV AND Training)`

 
- Joker tekens zoeken: 
    - `machi?e learning`
    - `mach_ing`
 
>[!NOTE]
> U kunt een lucene-query niet starten met het teken ' * '.

##  <a name="filter-query"></a>Query filteren

 
Filter query's gebruiken het volgende patroon:
 
`**[key1] [operator1] [value1]; [key2] [operator1] [value2];**`

 
U kunt de volgende knooppunt eigenschappen gebruiken als sleutels:

- runStatus
- compute
- duur
- gebruikt

En gebruik de volgende Opera tors:

- Groter dan of gelijk aan: `>=`
- Kleiner of gelijk aan: `<=`
- Groter `>`
- Jonge `<`
- Waard `==`
- Bestaan `=`
- NotEqual `!=`
- Naast `in`

 
 

### <a name="example"></a>Voorbeeld

- duration > 100;
- status in {failed, NotStarted}
- compute in {GPU-cluster}; runStatus in {voltooid}

## <a name="technical-notes"></a>Technische opmerkingen

- De relatie tussen meerdere filters is "en"
- Als `>=,  >,  <, or  <=` is gekozen, worden waarden automatisch geconverteerd naar een numeriek type. Anders worden teken reeks typen gebruikt voor vergelijking.
- Voor alle waarden van het type teken reeks is hoofdletter gevoelig in vergelijking.
- De operator in verwacht een verzameling als waarde, de syntaxis van de verzameling is `{name1, name2, name3}`
- De ruimte wordt genegeerd tussen tref woorden