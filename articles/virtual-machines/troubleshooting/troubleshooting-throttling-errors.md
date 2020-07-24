---
title: Problemen met beperkings fouten in azure oplossen | Microsoft Docs
description: Beperkings fouten, nieuwe pogingen en uitstel in azure compute.
services: virtual-machines
documentationcenter: ''
author: changov
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: changov
ms.reviewer: vashan, rajraj
ms.openlocfilehash: b1cc8a43423ecd33218948aaa001fc34877eac60
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074291"
---
# <a name="troubleshooting-api-throttling-errors"></a>Problemen met API-beperkingsfouten oplossen 

Azure Compute-aanvragen kunnen op basis van een abonnement en per regio worden beperkt om te helpen bij de algehele prestaties van de service. We zorgen ervoor dat alle aanroepen naar de Azure Compute resource provider (CRP), die resources onder micro soft beheert, worden beheerd. de berekenings naam ruimte overschrijdt de Maxi maal toegestane API-aanvraag snelheid. Dit document beschrijft de API-beperking, informatie over het oplossen van problemen met beperking en aanbevolen procedures om te voor komen dat deze wordt beperkt.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Beperking door Azure Resource Manager VS resource providers  

Als front-deur naar Azure Azure Resource Manager de verificatie en de eerste validatie en beperking van alle binnenkomende API-aanvragen. Azure Resource Manager aanroep frequentie limieten en gerelateerde diagnostische antwoord HTTP-headers worden [hier](../../azure-resource-manager/management/request-limits-and-throttling.md)beschreven.
 
Wanneer een Azure API-client een beperkings fout krijgt, is de HTTP-status 429 te veel aanvragen. Als u wilt weten of de aanvraag beperking wordt uitgevoerd door Azure Resource Manager of een onderliggende resource provider zoals CRP, raadpleegt u de `x-ms-ratelimit-remaining-subscription-reads` voor Get-aanvragen en `x-ms-ratelimit-remaining-subscription-writes` reactie headers voor niet-Get-aanvragen. Als het resterende aantal aanroepen bijna 0 is, is de algemene oproep limiet van het abonnement bereikt door Azure Resource Manager. Activiteiten door alle abonnements-clients worden samen geteld. Als dat niet het geval is, is de beperking afkomstig van de resource provider van de doel groep (de oplossing die wordt bepaald door het `/providers/<RP>` segment van de aanvraag-URL). 

## <a name="call-rate-informational-response-headers"></a>Informatie over oproep frequentie gegevens 

| Header                            | Waarde-indeling                           | Voorbeeld                               | Beschrijving                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-MS-ratelimit-rest-resource |```<source RP>/<policy or bucket>;<count>```| Micro soft. Compute/HighCostGet3Min; 159 | Resterend aantal API-aanroepen voor het beperkings beleid met betrekking tot de resource Bucket of bewerkings groep, inclusief het doel van deze aanvraag                                                                   |
| x-ms-request-charge               | ```<count>```                             | 1                                     | Het aantal aanroepen voor deze HTTP-aanvraag wordt in rekening gebracht voor de limiet van het betreffende beleid. Dit is meestal 1. Batch-aanvragen, zoals voor het schalen van een schaalset voor virtuele machines, kunnen meerdere tellingen in rekening brengen. |


Houd er rekening mee dat een API-aanvraag kan worden onderhevig aan meerdere beperkings beleidsregels. Er is een afzonderlijke `x-ms-ratelimit-remaining-resource` koptekst voor elk beleid. 

Hier volgt een voor beeld van een antwoord voor het verwijderen van de aanvraag voor een schaalset voor virtuele machines.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Details van beperkings fout

De 429 HTTP-status wordt meestal gebruikt voor het afwijzen van een aanvraag omdat een limiet voor de aanroep frequentie is bereikt. Een typisch beperkings fout antwoord van de compute-resource provider ziet er ongeveer uit zoals in het onderstaande voor beeld (alleen relevante headers worden weer gegeven):

```
HTTP/1.1 429 Too Many Requests
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet3Min;46
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet30Min;0
Retry-After: 1200
Content-Type: application/json; charset=utf-8
{
  "code": "OperationNotAllowed",
  "message": "The server rejected the request because too many requests have been received for this subscription.",
  "details": [
    {
      "code": "TooManyRequests",
      "target": "HighCostGet30Min",
      "message": "{\"operationGroup\":\"HighCostGet30Min\",\"startTime\":\"2018-06-29T19:54:21.0914017+00:00\",\"endTime\":\"2018-06-29T20:14:21.0914017+00:00\",\"allowedRequestCount\":800,\"measuredRequestCount\":1238}"
    }
  ]
}

```

Het beleid met het resterende aantal aanroepen van 0 is het wacht tijd dat de beperkings fout wordt geretourneerd. In dit geval is `HighCostGet30Min` . De algemene indeling van de antwoord tekst is de algemene Azure Resource Manager API-fout indeling (conform OData). De hoofd fout code, `OperationNotAllowed` , is de resource provider van één Compute gebruikt voor het rapporteren van beperkings fouten (onder andere typen client fouten). De `message` eigenschap van de interne fout (en) bevat een GESERIALISEERDE JSON-structuur met de details van de beperkings schending.

Zoals hierboven is beschreven, omvat elke beperkings fout de `Retry-After` header, waarmee het minimum aantal seconden wordt aangegeven dat de client moet wachten voordat het opnieuw wordt geprobeerd om de aanvraag uit te voeren. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>API-aanroep snelheid en beperking van fout analyse
Er is een preview-versie van de functie voor probleem oplossing beschikbaar voor de API van de compute-resource provider. Deze Power shell-cmdlets bieden statistieken over de API-aanvraag snelheid per tijds interval per bewerking en schendingen van beperking per bewerkings groep (beleid):
-   [Exporteren-AzLogAnalyticRequestRateByInterval](/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Exporteren-AzLogAnalyticThrottledRequest](/powershell/module/az.compute/export-azloganalyticthrottledrequest)

Met de API-aanroep statistieken kunt u het gedrag van de client (s) van een abonnement goed begrijpen en zo een eenvoudige identificatie van oproep patronen mogelijk maken die een beperking veroorzaken.

Een beperking van het analyse programma voor de tijd is dat er geen aanvragen worden geteld voor bron typen van schijven en moment opnamen (ter ondersteuning van beheerde schijven). Omdat de gegevens worden verzameld van de telemetrie van CRP, kan deze ook niet helpen bij het identificeren van beperkings fouten van ARM. Deze kunnen echter gemakkelijk worden geïdentificeerd op basis van de reactie headers van de onderscheidende ARM, zoals eerder is besproken.

De Power shell-cmdlets maken gebruik van een REST Service-API, die gemakkelijk rechtstreeks kan worden aangeroepen door clients (hoewel er nog geen formele ondersteuning is). Als u de indeling van de HTTP-aanvraag wilt weer geven, voert u de switch cmdlets with debug of checke with Fiddler uit.


## <a name="best-practices"></a>Aanbevolen procedures 

- Voer de API-fouten van Azure service niet onvoorwaardelijk en/of onmiddellijk opnieuw uit. Een veelvoorkomend exemplaar is voor client code om een herhaalde lus te krijgen wanneer er een fout optreedt die niet opnieuw kan worden uitgevoerd. De toegestane oproep limiet voor de groep van de doel bewerking wordt uiteindelijk uitgeput en de andere clients van het abonnement zijn van invloed op de nieuwe pogingen. 
- In het geval van API-automatiserings toepassingen met veel volumes kunt u de proactieve zelf beperking aan de client zijde implementeren wanneer het aantal beschik bare aanroepen voor een doel bewerkings groep onder een lage drempel waarde daalt. 
- Houd bij het volgen van asynchrone bewerkingen rekening met de hints voor opnieuw proberen na. 
- Als de client code informatie nodig heeft over een bepaalde virtuele machine, voert u een query uit op de VM in plaats van alle virtuele machines in de betreffende resource groep of het hele abonnement te vermelden en vervolgens de benodigde VM aan de client zijde te selecteren. 
- Als voor de client code Vm's, schijven en moment opnamen van een specifieke Azure-locatie nodig zijn, gebruikt u op locatie gebaseerde formulier van de query in plaats van alle Vm's van het abonnement te doorzoeken en vervolgens te filteren op locatie aan de client zijde: `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` query om regionale eind punten van de resource provider te berekenen. 
-   Bij het maken of bijwerken van API-resources met name Vm's en virtuele-machine schaal sets is het veel efficiënter om de geretourneerde async-bewerking bij te houden dan polling op de bron-URL zelf (op basis van de `provisioningState` ).

## <a name="next-steps"></a>Volgende stappen

Zie [richt lijnen voor opnieuw proberen voor specifieke services](/azure/architecture/best-practices/retry-service-specific) voor meer informatie over nieuwe richt lijnen voor andere services in Azure.
