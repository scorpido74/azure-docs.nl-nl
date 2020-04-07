---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 2b2c043e70aac14c7fc6f0b58aae257624b05d13
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673253"
---
In een Java-project worden de bindingen gedefinieerd als bindende annotaties op de functiemethode. Het *bestand function.json* wordt vervolgens automatisch gegenereerd op basis van deze annotaties.

Blader naar de locatie van uw functiecode onder _src/main/java,_ open het *projectbestand Function.java* en voeg de volgende parameter toe aan de `run` methodedefinitie:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

De `msg` parameter [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) is een type dat een verzameling tekenreeksen vertegenwoordigt die als berichten naar een uitvoerbinding worden geschreven wanneer de functie is voltooid. In dit geval is de uitvoer `outqueue`een opslagwachtrij met de naam . De verbindingstekenreeks voor het opslagaccount `connection` wordt ingesteld door de methode. In plaats van de verbindingstekenreeks zelf, passeert u de toepassingsinstelling die de tekenreeks Opslagaccountverbinding bevat.

De `run` methodedefinitie moet er nu uitzien als het volgende voorbeeld:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```