---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 2b2c043e70aac14c7fc6f0b58aae257624b05d13
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673253"
---
In een Java-project worden de bindingen gedefinieerd als bindings aantekeningen voor de functie methode. Het bestand *Function. json* wordt vervolgens automatisch gegenereerd op basis van deze aantekeningen.

Blader naar de locatie van de functie code onder _src/main/Java_, open het *functie. java* -project bestand en voeg de volgende para meter toe `run` aan de methode definitie:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

De `msg` para meter is [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) een type dat een verzameling teken reeksen vertegenwoordigt die als berichten naar een uitvoer binding worden geschreven wanneer de functie is voltooid. In dit geval is de uitvoer een opslag wachtrij met de `outqueue`naam. De connection string voor het opslag account is ingesteld met de `connection` methode. In plaats van de connection string zelf, geeft u de toepassings instelling door die het opslag account bevat connection string.

De `run` definitie van de methode moet er nu uitzien als in het volgende voor beeld:  

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