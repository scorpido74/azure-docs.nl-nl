---
title: Door de gebruiker gedefinieerde functies in Azure Stream Analytics
description: Dit artikel is een overzicht van door de gebruiker gedefinieerde functies in Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b29d66e8bb213fbbb162c3249f022e0783f9f62f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115584"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Door de gebruiker gedefinieerde functies in Azure Stream Analytics

De SQL-achtige querytaal in Azure Stream Analytics maakt het eenvoudig om realtime analyselogica te implementeren op streaminggegevens. Stream Analytics biedt extra flexibiliteit via aangepaste functies die in uw query worden aangeroepen. Het volgende codevoorbeeld is een `sampleFunction` UDF-aangeroepen die één parameter accepteert, elke invoerrecord `sampleResult`die de taak ontvangt en het resultaat wordt naar de uitvoer geschreven als .

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>Typen functies

Azure Stream Analytics ondersteunt de volgende vier functietypen: 

* Door de gebruiker gedefinieerde JavaScript-functies 
* Door JavaScript door de gebruiker gedefinieerde aggregaten 
* C#door de gebruiker gedefinieerde functies (met behulp van Visual Studio) 
* Azure Machine Learning 

U deze functies gebruiken voor scenario's zoals realtime scoren met behulp van machine learning-modellen, snaarmanipulaties, complexe wiskundige berekeningen, codering en decoderingvan gegevens. 

## <a name="limitations"></a>Beperkingen

Door de gebruiker gedefinieerde functies zijn stateloos en de retourwaarde kan slechts een scalaire waarde zijn. U niet bellen naar externe REST-eindpunten van deze door de gebruiker gedefinieerde functies, omdat dit waarschijnlijk van invloed is op de prestaties van uw taak. 

Azure Stream Analytics houdt niet alle aanroepen van functies bij en geeft resultaten weer. Om herhaalbaarheid te garanderen - bijvoorbeeld het opnieuw uitvoeren van uw taak vanaf oudere tijdstempel `Date.GetData()` `Math.random()`produceert dezelfde resultaten opnieuw - geen functies zoals of , omdat deze functies niet hetzelfde resultaat voor elke aanroep retourneren.  

## <a name="diagnostic-logs"></a>Diagnostische logboeken

Eventuele runtime fouten worden beschouwd als fataal en worden opgedoken door middel van activiteit en diagnostische logs. Het wordt aanbevolen dat uw functie alle uitzonderingen en fouten verwerkt en een geldig resultaat terugvoert naar uw query. Dit voorkomt dat uw taak naar een [mislukte status gaat.](job-states.md)  


## <a name="next-steps"></a>Volgende stappen

* [JavaScript-door de gebruiker gedefinieerde functies in Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Azure Stream Analytics JavaScript-door de gebruiker gedefinieerde aggregaten](stream-analytics-javascript-user-defined-aggregates.md)
* [Ontwikkel .NET Standard door de gebruiker gedefinieerde functies voor Azure Stream Analytics-taken](stream-analytics-edge-csharp-udf-methods.md)
* [Azure Stream Analytics integreren met Azure Machine Learning](machine-learning-udf.md)

