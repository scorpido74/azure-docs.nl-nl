---
title: Door de gebruiker gedefinieerde functies in Azure Stream Analytics
description: Dit artikel bevat een overzicht van door de gebruiker gedefinieerde functies in Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 45e766c624ee96f7faa06fb07d00349e620a4c0a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133488"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Door de gebruiker gedefinieerde functies in Azure Stream Analytics

Met de SQL-achtige query taal in Azure Stream Analytics kunt u eenvoudig real-time analyse logica implementeren op streaminggegevens. Stream Analytics biedt extra flexibiliteit via aangepaste functies die worden aangeroepen in uw query. Het volgende code voorbeeld is een UDF- `sampleFunction` naam die één para meter accepteert, elke invoer record die de taak ontvangt, en het resultaat wordt naar de `sampleResult`uitvoer geschreven als.

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>Typen functies

Azure Stream Analytics ondersteunt de volgende vier functie typen: 

* Door de gebruiker gedefinieerde JavaScript-functies 
* Door de gebruiker gedefinieerde Java script-samen stellingen 
* Door de gebruiker gedefinieerde C#-functies (met Visual Studio) 
* Azure Machine Learning 

U kunt deze functies gebruiken voor scenario's zoals realtime scores met behulp van machine learning modellen, teken reeks bewerkingen, complexe wiskundige berekeningen, gegevens coderen en decoderen. 

## <a name="limitations"></a>Beperkingen

Door de gebruiker gedefinieerde functies zijn stateless en de geretourneerde waarde kan alleen een scalaire waarde zijn. U kunt geen externe REST-eind punten aanroepen vanuit deze door de gebruiker gedefinieerde functies, omdat dit waarschijnlijk invloed heeft op de prestaties van uw taak. 

Azure Stream Analytics houdt geen registratie bij van alle functies en heeft resultaat opgeleverd. Ter waarborging van de Herhaal baarheid: als u uw taak opnieuw uitvoert vanuit oudere tijds tempel, worden dezelfde resultaten opnieuw weer gegeven. u kunt geen `Date.GetData()` functies `Math.random()`zoals of gebruiken, omdat deze functies niet hetzelfde resultaat voor elke aanroep retour neren.  

## <a name="resource-logs"></a>Resourcelogboeken

Runtime fouten worden als onherstelbaar beschouwd en worden opgehaald via activiteiten-en bron Logboeken. U wordt aangeraden alle uitzonde ringen en fouten te verwerken en een geldig resultaat te retour neren naar uw query. Hiermee wordt voor komen dat uw taak naar een [mislukte status](job-states.md)gaat.  


## <a name="next-steps"></a>Volgende stappen

* [Door de gebruiker gedefinieerde Java script-functies in Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Door de gebruiker gedefinieerde Java script-functies Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md)
* [Door de gebruiker gedefinieerde .NET Standard-functies ontwikkelen voor Azure Stream Analytics-taken](stream-analytics-edge-csharp-udf-methods.md)
* [Azure Stream Analytics integreren met Azure Machine Learning](machine-learning-udf.md)

