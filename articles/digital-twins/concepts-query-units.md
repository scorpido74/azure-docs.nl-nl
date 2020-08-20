---
title: Query-eenheden in azure Digital Apparaatdubbels
titleSuffix: Azure Digital Twins
description: Inzicht in het facturerings concept van query-eenheden in azure Digital Apparaatdubbels
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 5334a1196ac8044c56e615cf8894b44646b48fb4
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88615107"
---
# <a name="query-units-in-azure-digital-twins"></a>Query-eenheden in azure Digital Apparaatdubbels 

Een Azure Digital Apparaatdubbels **query Unit (qu)** is een eenheid op aanvraag die wordt gebruikt voor het uitvoeren van uw [Azure Digital apparaatdubbels-query's](how-to-query-graph.md) met behulp van de [query-API](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query). 

De systeem bronnen, zoals CPU, IOPS en geheugen die nodig zijn om query bewerkingen uit te voeren die worden ondersteund door Azure Digital Apparaatdubbels, worden abstract. Hiermee kunt u in plaats daarvan het gebruik bijhouden in query-eenheden.

De complexiteit van de query is van invloed op het aantal QUs dat wordt gebruikt om die query uit te voeren. 

In dit artikel wordt uitgelegd hoe u query-eenheden begrijpt en verbruik van query eenheden kunt bijhouden.

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>Het verbruik van de query-eenheid in azure Digital Apparaatdubbels zoeken 

Wanneer u een query uitvoert met behulp van de Azure Digital Apparaatdubbels- [query-API](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query), kunt u de reactie header controleren om het aantal QUs bij te houden dat de query verbruikt. Zoek naar "query-kosten" in het antwoord teruggestuurd vanuit Azure Digital Apparaatdubbels. 

Met de Azure Digital Apparaatdubbels [sdk's](how-to-use-apis-sdks.md) kunt u de header van de query lading extra heren van het wissel bare antwoord. In deze sectie wordt beschreven hoe u een query uitvoert voor Digital apparaatdubbels en hoe u het bewissel bare antwoord kunt herhalen om de koptekst van de query-toerekenbaar uit te pakken. 

Het volgende code fragment laat zien hoe u de query kosten kunt ophalen die worden gemaakt bij het aanroepen van de query-API. De antwoord pagina's worden dan eerst herhaald voor toegang tot de koptekst van de query en vervolgens wordt de digitale dubbele resultaten binnen elke pagina herhaald. 
 
```csharp
AsyncPageable<string> asyncPageableResponseWithCharge = client.QueryAsync("SELECT * FROM digitaltwins"); 
int pageNum = 0; 

// The "await" keyword here is required, as a call is made when fetching a new page. 

await foreach (Page<string> page in asyncPageableResponseWithCharge.AsPages()) 
{ 
    Console.WriteLine($"Page {++pageNum} results:"); 

    // Extract the query-charge header from the page 

    if (QueryChargeHelper.TryGetQueryCharge(page, out float queryCharge)) 
    { 
        Console.WriteLine($"Query charge was: {queryCharge}"); 
    } 

    // Iterate over the twin instances. 

    // The "await" keyword is not required here, as the paged response is local. 

    foreach (string response in page.Values) 
    { 
        BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(response); 
        Console.WriteLine($"Found digital twin '{twin.Id}'"); 
    } 
} 
```

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over het uitvoeren van query's in azure Digital Apparaatdubbels naar:
* [*Concepten: query taal*](concepts-query-language.md)
* [*Instructies: een query uitvoeren op de dubbele grafiek*](how-to-query-graph.md)
* [Naslag documentatie voor query-API](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query/querytwins)

U kunt Azure Digital Apparaatdubbels-query limieten vinden in [*Naslag informatie: Service limieten in open bare preview*](reference-service-limits.md).