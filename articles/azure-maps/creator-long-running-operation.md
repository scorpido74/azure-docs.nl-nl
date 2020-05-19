---
title: API voor langlopende bewerkingen Azure Maps
description: Meer informatie over langlopende asynchrone achtergrond verwerking in Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 360fc4af688e393bb8639ee773f0bf0de603a425
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598386"
---
# <a name="creator-long-running-operation-api"></a>API voor langlopende bewerkingen van de maker

Voor sommige API'S in Azure Maps wordt een [asynchroon patroon van een aanvraag/antwoord](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply)gebruikt. Met dit patroon kan Azure Maps Maxi maal beschik bare en responsieve services bieden. In dit artikel wordt uitgelegd hoe u de specifieke implementatie van langlopende asynchrone achtergrond verwerking van Azure-kaarten uitvoert.

## <a name="submitting-a-request"></a>Een aanvraag verzenden

Een client toepassing start een langlopende bewerking via een synchrone aanroep naar een HTTP-API. Deze aanroep bevindt zich doorgaans in de vorm van een HTTP POST-aanvraag. Wanneer een asynchrone werk belasting is gemaakt, retourneert de API een HTTP- `202` status code, waarmee wordt aangegeven dat de aanvraag is geaccepteerd. Dit antwoord bevat een `Location` header die verwijst naar een eind punt dat door de client kan worden gecontroleerd om de status van de langlopende bewerking te controleren.

### <a name="example-of-a-success-response"></a>Voor beeld van een reactie op succes

```HTTP
Status: 202 Accepted
Location: https://atlas.microsoft.com/service/operations/{operationId}

```

Als de aanroep niet wordt door gegeven, retourneert de API in plaats daarvan een HTTP- `400` antwoord voor een ongeldige aanvraag. De antwoord tekst geeft de client meer informatie over waarom de aanvraag ongeldig is.

### <a name="monitoring-the-operation-status"></a>De bewerkings status bewaken

Het locatie-eind punt dat in de geaccepteerde antwoord koppen is opgenomen, kan worden gecontroleerd om de status van de langlopende bewerking te controleren. De antwoord tekst van de bewerkings status aanvraag bevat altijd de `status` en de `createdDateTime` Eigenschappen. De `status` eigenschap toont de huidige status van de langlopende bewerking. Mogelijke statussen zijn `"NotStarted"` , `"Running"` , en `"Succeeded"` `"Failed"` . De `createdDateTime` eigenschap toont het tijdstip waarop de eerste aanvraag is gedaan om de langlopende bewerking te starten. Als de status ofwel `"NotStarted"` of is `"Running"` , `Retry-After` wordt er ook een header met het antwoord gegeven. De `Retry-After` header, gemeten in seconden, kan worden gebruikt om te bepalen wanneer de volgende polling-aanroep naar de bewerkings status-API moet worden gemaakt.

### <a name="example-of-running-a-status-response"></a>Voor beeld van het uitvoeren van een status reactie

```HTTP
Status: 200 OK
Retry-After: 30
{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Running"
}
```

## <a name="handling-operation-completion"></a>Voltooiing van bewerkingen verwerken

Bij het volt ooien van de langlopende bewerking is de status van de reactie ofwel `"Succeeded"` of `"Failed"` . Wanneer een nieuwe resource is gemaakt op basis van een langlopende bewerking, retourneert het succes antwoord een HTTP- `201 Created` status code. Het antwoord bevat ook een `Location` kop die verwijst naar meta gegevens over de resource. Als er geen nieuwe resource is gemaakt, wordt in het succes bericht een HTTP- `200 OK` status code geretourneerd. Bij een fout is de antwoord status code ook de `200 OK` code. Het antwoord heeft echter een `error` eigenschap in de hoofd tekst. De fout gegevens voldoen aan de OData-fout specificatie.

### <a name="example-of-success-response"></a>Voor beeld van een geslaagd antwoord

```HTTP
Status: 201 Created
Location: "https://atlas.microsoft.com/tileset/{tileset-id}"
 {
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/tileset/{tileset-id}"
}
```

### <a name="example-of-failure-response"></a>Voor beeld van een reactie op fouten

```HTTP
Status: 200 OK

{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Failed",
    "error": {
        "code": "InvalidFeature",
        "message": "The provided feature is invalid.",
        "details": {
            "code": "NoGeometry",
            "message": "No geometry was provided with the feature."
        }
    }
}
```
