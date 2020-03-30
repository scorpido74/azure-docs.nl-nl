---
title: Problemen met beperkingsfouten in Azure oplossen | Microsoft Documenten
description: Beperking van fouten, nieuwe pogingen en back-off in Azure Compute.
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
ms.openlocfilehash: f5fbd80fc9a8e519cf8f49ab16d7e747c6a8171b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045356"
---
# <a name="troubleshooting-api-throttling-errors"></a>Problemen met API-beperkingsfouten oplossen 

Azure Compute-aanvragen kunnen worden beperkt bij een abonnement en per regio om te helpen bij de algehele prestaties van de service. We zorgen ervoor dat alle oproepen naar de Azure Compute Resource Provider (CRP), die resources beheert onder Microsoft.Compute-naamruimte niet hoger zijn dan de maximaal toegestane API-aanvraagsnelheid. In dit document worden API-beperking, details over het oplossen van beperkingsproblemen en best practices beschreven om te voorkomen dat deze worden beperkt.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Beperking door Azure Resource Manager vs Resource Providers  

Als voordeur naar Azure doet Azure Resource Manager de verificatie- en eerste-ordervalidatie en -beperking van alle binnenkomende API-aanvragen. Azure Resource Manager-gespreksfrequentielimieten en gerelateerde diagnostische responsHTTP-headers worden [hier](https://docs.microsoft.com/azure/azure-resource-manager/management/request-limits-and-throttling)beschreven.
 
Wanneer een Azure API-client een beperkingsfout krijgt, is de HTTP-status 429 te veel aanvragen. Als u wilt weten of de beperking van de aanvraag wordt uitgevoerd `x-ms-ratelimit-remaining-subscription-reads` door Azure `x-ms-ratelimit-remaining-subscription-writes` Resource Manager of een onderliggende resourceprovider zoals CRP, controleert u de voor GET-aanvragen en antwoordkoppen voor niet-GET-aanvragen. Als het resterende aantal oproepen 0 nadert, is de algemene aanroeplimiet van het abonnement bereikt die is gedefinieerd door Azure Resource Manager. Activiteiten van alle abonnementsklanten worden samen geteld. Anders is de beperking afkomstig van de doelresourceprovider (de `/providers/<RP>` provider die wordt geadresseerd door het segment van de aanvraag-URL). 

## <a name="call-rate-informational-response-headers"></a>Informatiekoppen voor informatieover de gesprekssnelheid 

| Header                            | Waardenotatie                           | Voorbeeld                               | Beschrijving                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit-remaining-resource |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | Resterende API-aanroeptelling voor het beperkingsbeleid voor de resourcebucket of -bewerkingsgroep, inclusief het doel van deze aanvraag                                                                   |
| x-ms-request-charge               | ```<count>```                             | 1                                     | Het aantal aantal oproepen telt "in rekening gebracht" voor deze HTTP-aanvraag in de richting van de limiet van het toepasselijke beleid. Dit is meestal 1. Batchaanvragen, zoals voor het schalen van een virtuele machineschaalset, kunnen meerdere tellingen in rekening brengen. |


Houd er rekening mee dat een API-aanvraag kan worden onderworpen aan beleid voor meervoudige beperking. Er zal een `x-ms-ratelimit-remaining-resource` aparte header voor elk beleid. 

Hier is een voorbeeldreactie om het verzoek voor virtuele machineschaalset te verwijderen.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Details van beperkingsfouten

De HTTP-status 429 wordt vaak gebruikt om een aanvraag af te wijzen omdat een gesprekslimiet is bereikt. Een typische foutreactie voor beperking van de gegevensbeperking van Compute Resource Provider ziet eruit als het onderstaande voorbeeld (alleen relevante kopteksten worden weergegeven):

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

Het beleid met het resterende aantal oproepen van 0 is het beleid waarvoor de beperkingsfout wordt geretourneerd. In dit geval `HighCostGet30Min`is dat . De algemene indeling van de antwoordtekst is de algemene Azure Resource Manager API-foutindeling (conformant met OData). De belangrijkste `OperationNotAllowed`foutcode, , is de ene Compute Resource Provider gebruikt om throttling fouten te melden (onder andere soorten clientfouten). De `message` eigenschap van de innerlijke fout(en) bevat een geserialiseerde JSON-structuur met de details van de beperkingsschending.

Zoals hierboven beschreven, omvat elke beperkingsfout de `Retry-After` koptekst, die het minimumaantal seconden biedt dat de client moet wachten voordat de aanvraag opnieuw wordt geprobeerd. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>API-aanroepsnelheid en beperkingsfoutanalyzer
Er is een previewversie van een probleemoplossingsfunctie beschikbaar voor de API van de Compute-resourceprovider. Deze PowerShell-cmdlets bieden statistieken over de API-aanvraagsnelheid per tijdsinterval per bewerking en beperkingsschendingen per bewerkingsgroep (beleid):
-   [Export-azloganalyticrateratebyinterval](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Export-AzLogAnalyticThrottledRequest](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticthrottledrequest)

De API-oproepstatistieken kunnen veel inzicht bieden in het gedrag van de client(s) van een abonnement en eenvoudige identificatie van oproeppatronen mogelijk maken die beperking veroorzaken.

Een beperking van de analyzer is voorlopig dat het geen aanvragen voor schijf- en momentopnamebronnen telt (ter ondersteuning van beheerde schijven). Aangezien het verzamelt gegevens uit telemetrie CRP's, het kan ook niet helpen bij het identificeren van throttling fouten van ARM. Maar die kunnen gemakkelijk worden geïdentificeerd op basis van de onderscheidende ARM response headers, zoals eerder besproken.

De PowerShell-cmdlets maken gebruik van een REST-service-API, die eenvoudig rechtstreeks door clients kan worden aangeroepen (hoewel met nog geen formele ondersteuning). Als u de HTTP-aanvraagindeling wilt bekijken, voert u de cmdlets uit met -Foutopsporingsschakelaar of snuffelt u in de uitvoering ervan met Fiddler.


## <a name="best-practices"></a>Aanbevolen procedures 

- Probeer Azure-service-API-fouten niet onvoorwaardelijk en/of onmiddellijk opnieuw. Een veel voorkomende gebeurtenis is dat clientcode in een snelle lus voor nieuwe try komt wanneer u een fout tegenkomt die niet opnieuw kan worden geprobeerd. Pogingen zullen uiteindelijk de toegestane gesprekslimiet voor de groep van de doelbewerking uitputten en andere klanten van het abonnement beïnvloeden. 
- In api-automatiseringsgevallen met een hoog volume u overwegen proactieve client-side self-throttling te implementeren wanneer het beschikbare aantal oproepen voor een doelgroep onder een lage drempel zakt. 
- Respecteer bij het bijhouden van async-bewerkingen de kophints opnieuw proberen-na. 
- Als de clientcode informatie nodig heeft over een bepaalde virtuele machine, vraagt u die VM rechtstreeks op in plaats van alle VM's in de brongroep of het volledige abonnement op te sommen en vervolgens de benodigde VM aan de clientzijde te kiezen. 
- Als clientcode VM's, schijven en momentopnamen van een specifieke Azure-locatie nodig heeft, gebruikt u locatiegebaseerde vorm van `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` de query in plaats van alle abonnementsVM's op te vragen en vervolgens te filteren op locatie aan de clientzijde: query naar regionale eindpunten van Compute Resource Provider. 
-   Bij het maken of bijwerken van API-bronnen in het bijzonder, VM's en virtuele machine schaal sets, is het veel `provisioningState`efficiënter om de geretourneerde async operatie te volgen tot voltooiing dan polling op de resource URL zelf (op basis van de ).

## <a name="next-steps"></a>Volgende stappen

Zie Richtlijnen voor specifieke services opnieuw proberen voor meer informatie over het opnieuw proberen van richtlijnen [voor andere services.](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)
