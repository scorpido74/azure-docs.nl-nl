---
title: Gebeurtenisschema azure-activiteitslogboek
description: Beschrijft het gebeurtenisschema voor elke categorie in het Azure-activiteitenlogboek.
author: bwren
services: azure-monitor
ms.topic: reference
ms.date: 12/04/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: c2f171c79423e0cfe8b57c05b8248679f9ada9f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472738"
---
# <a name="azure-activity-log-event-schema"></a>Gebeurtenisschema azure-activiteitslogboek
Het [azure-activiteitenlogboek](platform-logs-overview.md) biedt inzicht in gebeurtenissen op abonnementsniveau die zich in Azure hebben voorgedaan. In dit artikel wordt het gebeurtenisschema voor elke categorie beschreven. 

In de onderstaande voorbeelden wordt het schema weergegeven wanneer u het activiteitslogboek opent vanuit de portal, PowerShell, CLI en REST API. Het schema is anders wanneer u [het activiteitenlogboek streamt naar opslag- of gebeurtenishubs.](resource-logs-stream-event-hubs.md) Aan het einde van het artikel wordt een toewijzing van de eigenschappen aan het [resourcelogboekenschema](diagnostic-logs-schema.md) verstrekt.

## <a name="administrative"></a>Administratief
Deze categorie bevat de record van alle bewerkingen voor maken, bijwerken, verwijderen en actie die via Resource Manager worden uitgevoerd. Voorbeelden van de typen gebeurtenissen die u in deze categorie zou zien, zijn 'virtuele machine maken' en 'netwerkbeveiligingsgroep verwijderen' Elke actie die wordt uitgevoerd door een gebruiker of toepassing met Resource Manager, wordt gemodelleerd als een bewerking op een bepaald resourcetype. Als het bewerkingstype Schrijven, Verwijderen of Actie is, worden de records van zowel het begin als het succes of het mislukken van die bewerking geregistreerd in de categorie Beheer. De categorie Beheer bevat ook wijzigingen in op rollen gebaseerd toegangsbeheer in een abonnement.

### <a name="sample-event"></a>Voorbeeldgebeurtenis
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.microsoft.com/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Beschrijvingen van onroerend goed
| Naam van element | Beschrijving |
| --- | --- |
| autorisatie |Blob van RBAC-eigenschappen van de gebeurtenis. Bevat meestal de eigenschappen 'actie', 'rol' en 'scope'. |
| Beller |E-mailadres van de gebruiker die de bewerking heeft uitgevoerd, UPN-claim of SPN-claim op basis van beschikbaarheid. |
| Kanalen |Een van de volgende waarden: "Beheerder", "Bewerking" |
| claims |Het JWT-token dat door Active Directory wordt gebruikt om de gebruiker of toepassing te verifiëren om deze bewerking uit te voeren in Resourcebeheer. |
| correlationId |Meestal een GUID in de tekenreeksindeling. Gebeurtenissen die een correlationId delen, behoren tot dezelfde uber-actie. |
| description |Statische tekstbeschrijving van een gebeurtenis. |
| eventDataId |Unieke id van een gebeurtenis. |
| gebeurtenisNaam | Vriendelijke naam van het administratieve evenement. |
| category | Altijd "Administratief" |
| httpRequest httpRequest httpRequest httpRequest |Blob die de Http-aanvraag beschrijft. Omvat meestal de "clientRequestId", "clientIpAddress" en "methode" (HTTP-methode. BIJVOORBEELD PUT). |
| niveau |Niveau van het evenement. Een van de volgende waarden: "Kritiek", "Fout", "Waarschuwing" en "Informatief" |
| resourceGroupName |Naam van de resourcegroep voor de getroffen resource. |
| resourceProviderName |Naam van de resourceprovider voor de getroffen resource |
| resourceType | Het type resource dat is beïnvloed door een administratieve gebeurtenis. |
| resourceId |Resource-id van de getroffen resource. |
| operationId |Een GUID die wordt gedeeld tussen de gebeurtenissen die overeenkomen met één bewerking. |
| operationName |Naam van de bewerking. |
| properties |Set `<Key, Value>` paren (dat wil zeggen een woordenboek) waarin de details van de gebeurtenis worden beschreven. |
| status |Tekenreeks die de status van de bewerking beschrijft. Enkele veelvoorkomende waarden zijn: Gestart, In uitvoering, Geslaagd, Mislukt, Actief, Opgelost. |
| subStatus |Meestal is de HTTP-statuscode van de bijbehorende REST-aanroep, maar kan ook andere tekenreeksen bevatten die een substatus beschrijven, zoals deze gemeenschappelijke waarden: OK (HTTP-statuscode: 200), Gemaakt (HTTP-statuscode: 201), Geaccepteerd (HTTP-statuscode: 202), Geen inhoud (HTTP-status Code: 204), Bad Request (HTTP Status Code: 400), Not Found (HTTP Status Code: 404), Conflict (HTTP Status Code: 409), Internal Server Error (HTTP Status Code: 500), Service Unavailable (HTTP Status Code: 503), Gateway Timeout (HTTP Status Code: 504). |
| eventTimestamp |Tijdstempel wanneer de gebeurtenis is gegenereerd door de Azure-service die de aanvraag verwerkt die overeenkomt met de gebeurtenis. |
| indieningTijdstempel |Tijdstempel toen de gebeurtenis beschikbaar kwam voor query's. |
| subscriptionId |Azure-abonnements-ID. |

## <a name="service-health"></a>Status van service
Deze categorie bevat de registratie van eventuele servicestatusincidenten die zich in Azure hebben voorgedaan. Een voorbeeld van het type gebeurtenis dat u in deze categorie zou zien, is 'SQL Azure in Oost-VS ondervindt downtime'. Servicestatusgebeurtenissen zijn er in vijf varianten: Actie vereist, Assisted Recovery, Incident, Maintenance, Information of Security en worden alleen weergegeven als u een bron in het abonnement hebt die door de gebeurtenis wordt beïnvloed.

### <a name="sample-event"></a>Voorbeeldgebeurtenis
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
Raadpleeg het artikel [servicestatusmeldingen](./../../azure-monitor/platform/service-notifications.md) voor documentatie over de waarden in de eigenschappen.

## <a name="resource-health"></a>Status van resources
Deze categorie bevat de registratie van alle gebeurtenissen in de resourcestatus die zijn opgetreden in uw Azure-bronnen. Een voorbeeld van het type gebeurtenis dat u in deze categorie zou zien, is 'Status virtuele machine gewijzigd in niet beschikbaar'. Gebeurtenissen in resourcestatus kunnen een van de vier statussen vertegenwoordigen: Beschikbaar, Niet beschikbaar, gedegradeerd en onbekend. Bovendien kunnen gebeurtenissen in resourcestatus worden gecategoriseerd als platform geïnitieerd of door de gebruiker geïnitieerd.

### <a name="sample-event"></a>Voorbeeldgebeurtenis

```json
{
    "channels": "Admin, Operation",
    "correlationId": "28f1bfae-56d3-7urb-bff4-194d261248e9",
    "description": "",
    "eventDataId": "a80024e1-883d-37ur-8b01-7591a1befccb",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "ResourceHealth",
        "localizedValue": "Resource Health"
    },
    "eventTimestamp": "2018-09-04T15:33:43.65Z",
    "id": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
    "level": "Critical",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Resourcehealth/healthevent/Activated/action",
        "localizedValue": "Health Event Activated"
    },
    "resourceGroupName": "<resource group>",
    "resourceProviderName": {
        "value": "Microsoft.Resourcehealth/healthevent/action",
        "localizedValue": "Microsoft.Resourcehealth/healthevent/action"
    },
    "resourceType": {
        "value": "Microsoft.Compute/virtualMachines",
        "localizedValue": "Microsoft.Compute/virtualMachines"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "stage": "Active",
        "title": "Virtual Machine health status changed to unavailable",
        "details": "Virtual machine has experienced an unexpected event",
        "healthStatus": "Unavailable",
        "healthEventType": "Downtime",
        "healthEventCause": "PlatformInitiated",
        "healthEventCategory": "Unplanned"
    },
    "relatedEvents": []
}
```

### <a name="property-descriptions"></a>Beschrijvingen van onroerend goed
| Naam van element | Beschrijving |
| --- | --- |
| Kanalen | Altijd "Admin, Operation" |
| correlationId | Een GUID in de tekenreeksindeling. |
| description |Statische tekstbeschrijving van de waarschuwingsgebeurtenis. |
| eventDataId |Unieke id van de waarschuwingsgebeurtenis. |
| category | Altijd "ResourceHealth" |
| eventTimestamp |Tijdstempel wanneer de gebeurtenis is gegenereerd door de Azure-service die de aanvraag verwerkt die overeenkomt met de gebeurtenis. |
| niveau |Niveau van het evenement. Een van de volgende waarden: "Kritiek", "Fout", "Waarschuwing", "Informatief" en "Verbose" |
| operationId |Een GUID die wordt gedeeld tussen de gebeurtenissen die overeenkomen met één bewerking. |
| operationName |Naam van de bewerking. |
| resourceGroupName |Naam van de resourcegroep die de resource bevat. |
| resourceProviderName |Altijd "Microsoft.Resourcehealth/healthevent/action". |
| resourceType | Het type resource dat is beïnvloed door een gebeurtenis Resourcestatus. |
| resourceId | Naam van de resource-id voor de getroffen resource. |
| status |Tekenreeks die de status van de status van de statusgebeurtenis beschrijft. Waarden kunnen zijn: Actief, Opgelost, InProgress, Bijgewerkt. |
| subStatus | Meestal null voor waarschuwingen. |
| indieningTijdstempel |Tijdstempel toen de gebeurtenis beschikbaar kwam voor query's. |
| subscriptionId |Azure-abonnements-ID. |
| properties |Set `<Key, Value>` paren (dat wil zeggen een woordenboek) waarin de details van de gebeurtenis worden beschreven.|
| eigenschappen.titel | Een gebruiksvriendelijke tekenreeks die de status van de resource beschrijft. |
| eigenschappen.details | Een gebruiksvriendelijke tekenreeks die verdere details over de gebeurtenis beschrijft. |
| properties.currentHealthStatus | De huidige status van de resource. Een van de volgende waarden: "Beschikbaar", "Niet beschikbaar", "Gedegradeerd" en "Onbekend". |
| properties.previousHealthStatus | De vorige status van de resource. Een van de volgende waarden: "Beschikbaar", "Niet beschikbaar", "Gedegradeerd" en "Onbekend". |
| eigenschappen.type | Een beschrijving van het type gebeurtenis resourcestatus. |
| eigenschappen.oorzaak | Een beschrijving van de oorzaak van de gebeurtenis resourcestatus. Ofwel "UserInitiated" en "PlatformInitiated". |


## <a name="alert"></a>Waarschuwing
Deze categorie bevat de registratie van alle activeringen van Azure-waarschuwingen. Een voorbeeld van het type gebeurtenis dat u in deze categorie zou zien, is 'CPU% op myVM is de afgelopen 5 minuten meer dan 80 geweest'. Verschillende Azure-systemen hebben een waarschuwingsconcept: u een regel van een bepaalde soort definiëren en een melding ontvangen wanneer de voorwaarden overeenkomen met die regel. Elke keer dat aan een ondersteund Azure-waarschuwingstype 'wordt geactiveerd' of aan de voorwaarden wordt voldaan om een melding te genereren, wordt ook een record van de activering naar deze categorie van het activiteitenlogboek gepusht.

### <a name="sample-event"></a>Voorbeeldgebeurtenis

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>Beschrijvingen van onroerend goed
| Naam van element | Beschrijving |
| --- | --- |
| Beller | Altijd Microsoft.Insights/alertRegels |
| Kanalen | Altijd "Admin, Operation" |
| claims | JSON-blob met de SPN -naam (servicehoofdnaam) of resourcetype van de waarschuwingsengine. |
| correlationId | Een GUID in de tekenreeksindeling. |
| description |Statische tekstbeschrijving van de waarschuwingsgebeurtenis. |
| eventDataId |Unieke id van de waarschuwingsgebeurtenis. |
| category | Altijd "Alert" |
| niveau |Niveau van het evenement. Een van de volgende waarden: "Kritiek", "Fout", "Waarschuwing" en "Informatief" |
| resourceGroupName |Naam van de resourcegroep voor de impactgevende resource als het een metrische waarschuwing is. Voor andere waarschuwingstypen is dit de naam van de resourcegroep die de waarschuwing zelf bevat. |
| resourceProviderName |Naam van de resourceprovider voor de impactgevende resource als het een metrische waarschuwing is. Voor andere waarschuwingstypen is dit de naam van de resourceprovider voor de waarschuwing zelf. |
| resourceId | Naam van de resource-id voor de impactgevende resource als het een metrische waarschuwing is. Voor andere waarschuwingstypen is dit de resource-id van de waarschuwingsbron zelf. |
| operationId |Een GUID die wordt gedeeld tussen de gebeurtenissen die overeenkomen met één bewerking. |
| operationName |Naam van de bewerking. |
| properties |Set `<Key, Value>` paren (dat wil zeggen een woordenboek) waarin de details van de gebeurtenis worden beschreven. |
| status |Tekenreeks die de status van de bewerking beschrijft. Enkele veelvoorkomende waarden zijn: Gestart, In uitvoering, Geslaagd, Mislukt, Actief, Opgelost. |
| subStatus | Meestal null voor waarschuwingen. |
| eventTimestamp |Tijdstempel wanneer de gebeurtenis is gegenereerd door de Azure-service die de aanvraag verwerkt die overeenkomt met de gebeurtenis. |
| indieningTijdstempel |Tijdstempel toen de gebeurtenis beschikbaar kwam voor query's. |
| subscriptionId |Azure-abonnements-ID. |

### <a name="properties-field-per-alert-type"></a>Eigenschappenveld per waarschuwingstype
Het veld Eigenschappen bevat verschillende waarden, afhankelijk van de bron van de waarschuwingsgebeurtenis. Twee veelvoorkomende aanbieders van waarschuwingsgebeurtenissen zijn waarschuwingen voor activiteitenlogboeken en metrische waarschuwingen.

#### <a name="properties-for-activity-log-alerts"></a>Eigenschappen voor waarschuwingen voor activiteitenlogboeken
| Naam van element | Beschrijving |
| --- | --- |
| properties.subscriptionId | De abonnements-ID van de gebeurtenis activiteitslogboek waardoor deze waarschuwingsregel voor activiteitenlogboeken is geactiveerd. |
| properties.eventDataId | De gebeurtenisgegevens-id van de gebeurtenis activiteitslogboek waardoor deze waarschuwingsregel voor activiteitenlogboeken is geactiveerd. |
| properties.resourceGroep | De resourcegroep van de gebeurtenis activiteitslogboek waardoor deze waarschuwingsregel voor activiteitenlogboeken is geactiveerd. |
| properties.resourceId | De resource-id van de gebeurtenis activiteitslogboek waardoor deze waarschuwingsregel voor activiteitenlogboeken is geactiveerd. |
| properties.eventTimestamp | De gebeurtenistijdstempel van de gebeurtenis activiteitslogboek waardoor deze waarschuwingsregel voor activiteitenlogboeken is geactiveerd. |
| properties.operationName | De bewerkingsnaam van de gebeurtenis activiteitslogboek waardoor deze waarschuwingsregel voor activiteitenlogboeken is geactiveerd. |
| eigenschappen.status | De status van de gebeurtenis activiteitslogboek waardoor deze waarschuwingsregel voor activiteitenlogboeken is geactiveerd.|

#### <a name="properties-for-metric-alerts"></a>Eigenschappen voor metrische waarschuwingen
| Naam van element | Beschrijving |
| --- | --- |
| Eigenschappen. RuleUri (RuleUri) | Resource-ID van de metrische waarschuwingsregel zelf. |
| Eigenschappen. RegelNaam | De naam van de metrische waarschuwingsregel. |
| Eigenschappen. RegelBeschrijving | De beschrijving van de metrische waarschuwingsregel (zoals gedefinieerd in de waarschuwingsregel). |
| Eigenschappen. Drempel | De drempelwaarde die wordt gebruikt bij de evaluatie van de metrische waarschuwingsregel. |
| Eigenschappen. WindowsizeinMinutes | De venstergrootte die wordt gebruikt bij de evaluatie van de metrische waarschuwingsregel. |
| Eigenschappen. Aggregatie | Het aggregatietype dat is gedefinieerd in de metrische waarschuwingsregel. |
| Eigenschappen. Operator | De voorwaardelijke operator die wordt gebruikt bij de evaluatie van de metrische waarschuwingsregel. |
| Eigenschappen. MetricName | De metrische naam van de statistiek die wordt gebruikt bij de evaluatie van de metrische waarschuwingsregel. |
| Eigenschappen. MetricUnit | De metrische eenheid voor de statistiek die wordt gebruikt bij de evaluatie van de metrische waarschuwingsregel. |

## <a name="autoscale"></a>Automatisch schalen
Deze categorie bevat de registratie van alle gebeurtenissen die verband houden met de werking van de autoscale-engine op basis van de instellingen voor automatische schaal die u in uw abonnement hebt gedefinieerd. Een voorbeeld van het type gebeurtenis dat u in deze categorie zou zien, is 'Actie automatisch schalen opschalen is mislukt'. Met behulp van automatisch schalen u automatisch het aantal exemplaren in een ondersteund resourcetype uitschalen of schalen op basis van tijd van de dag en/of (metrische) gegevens laden met behulp van een instelling voor automatisch schalen. Wanneer aan de voorwaarden is voldaan om op of omlaag te worden geschaald, worden de gebeurtenissen voor het starten en het starten of mislukte of mislukte in deze categorie geregistreerd.

### <a name="sample-event"></a>Voorbeeldgebeurtenis
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>Beschrijvingen van onroerend goed
| Naam van element | Beschrijving |
| --- | --- |
| Beller | Altijd Microsoft.Insights/autoscaleInstellingen |
| Kanalen | Altijd "Admin, Operation" |
| claims | JSON-blob met de SPN -hoofdnaam of resourcetype van de autoscale-engine. |
| correlationId | Een GUID in de tekenreeksindeling. |
| description |Statische tekstbeschrijving van de gebeurtenis automatisch schalen. |
| eventDataId |Unieke id van de gebeurtenis automatisch schalen. |
| niveau |Niveau van het evenement. Een van de volgende waarden: "Kritiek", "Fout", "Waarschuwing" en "Informatief" |
| resourceGroupName |Naam van de resourcegroep voor de instelling automatisch schalen. |
| resourceProviderName |Naam van de resourceprovider voor de instelling automatisch schalen. |
| resourceId |Resource-id van de instelling voor automatisch schalen. |
| operationId |Een GUID die wordt gedeeld tussen de gebeurtenissen die overeenkomen met één bewerking. |
| operationName |Naam van de bewerking. |
| properties |Set `<Key, Value>` paren (dat wil zeggen een woordenboek) waarin de details van de gebeurtenis worden beschreven. |
| Eigenschappen. Beschrijving | Gedetailleerde beschrijving van wat de autoscale motor aan het doen was. |
| Eigenschappen. Resourcename | Resource-id van de impactdie resource heeft gehad (de resource waarop de schaalactie werd uitgevoerd) |
| Eigenschappen. OldInstancesCount | Het aantal exemplaren voordat de actie automatisch schalen van kracht werd. |
| Eigenschappen. NewInstancesCount | Het aantal exemplaren nadat de actie automatisch schalen van kracht is geworden. |
| Eigenschappen. Laatste schaalactietijd | De tijdstempel van wanneer de actie automatisch schalen heeft plaatsgevonden. |
| status |Tekenreeks die de status van de bewerking beschrijft. Enkele veelvoorkomende waarden zijn: Gestart, In uitvoering, Geslaagd, Mislukt, Actief, Opgelost. |
| subStatus | Meestal null voor autoscale. |
| eventTimestamp |Tijdstempel wanneer de gebeurtenis is gegenereerd door de Azure-service die de aanvraag verwerkt die overeenkomt met de gebeurtenis. |
| indieningTijdstempel |Tijdstempel toen de gebeurtenis beschikbaar kwam voor query's. |
| subscriptionId |Azure-abonnements-ID. |

## <a name="security"></a>Beveiliging
Deze categorie bevat de record eventuele waarschuwingen die worden gegenereerd door Azure Security Center. Een voorbeeld van het type gebeurtenis dat u in deze categorie zou zien, is 'Verdacht bestand met dubbele extensie uitgevoerd'.

### <a name="sample-event"></a>Voorbeeldgebeurtenis
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Beschrijvingen van onroerend goed
| Naam van element | Beschrijving |
| --- | --- |
| Kanalen | Altijd "Operatie" |
| correlationId | Een GUID in de tekenreeksindeling. |
| description |Statische tekstbeschrijving van de beveiligingsgebeurtenis. |
| eventDataId |Unieke id van de beveiligingsgebeurtenis. |
| gebeurtenisNaam |Vriendelijke naam van het beveiligingsevenement. |
| category | Altijd "Veiligheid" |
| Id |Unieke resource-id van de beveiligingsgebeurtenis. |
| niveau |Niveau van het evenement. Een van de volgende waarden: 'Kritiek', 'Fout', 'Waarschuwing' of 'Informatief' |
| resourceGroupName |Naam van de resourcegroep voor de resource. |
| resourceProviderName |Naam van de resourceprovider voor Azure Security Center. Altijd "Microsoft.Security". |
| resourceType |Het type resource dat de beveiligingsgebeurtenis heeft gegenereerd, zoals 'Microsoft.Security/locaties/waarschuwingen'. |
| resourceId |Resource-id van de beveiligingswaarschuwing. |
| operationId |Een GUID die wordt gedeeld tussen de gebeurtenissen die overeenkomen met één bewerking. |
| operationName |Naam van de bewerking. |
| properties |Set `<Key, Value>` paren (dat wil zeggen een woordenboek) waarin de details van de gebeurtenis worden beschreven. Deze eigenschappen variëren afhankelijk van het type beveiligingswaarschuwing. Zie [deze pagina](../../security-center/security-center-alerts-overview.md) voor een beschrijving van de typen waarschuwingen die afkomstig zijn van Security Center. |
| Eigenschappen. Ernst |Het ernstniveau. Mogelijke waarden zijn 'Hoog', 'Gemiddeld' of 'Laag'. |
| status |Tekenreeks die de status van de bewerking beschrijft. Enkele veelvoorkomende waarden zijn: Gestart, In uitvoering, Geslaagd, Mislukt, Actief, Opgelost. |
| subStatus | Meestal null voor beveiligingsgebeurtenissen. |
| eventTimestamp |Tijdstempel wanneer de gebeurtenis is gegenereerd door de Azure-service die de aanvraag verwerkt die overeenkomt met de gebeurtenis. |
| indieningTijdstempel |Tijdstempel toen de gebeurtenis beschikbaar kwam voor query's. |
| subscriptionId |Azure-abonnements-ID. |

## <a name="recommendation"></a>Aanbeveling
Deze categorie bevat de registratie van eventuele nieuwe aanbevelingen die voor uw services worden gegenereerd. Een voorbeeld van een aanbeveling is 'Beschikbaarheidssets gebruiken voor verbeterde fouttolerantie'. Er zijn vier soorten aanbevelingsgebeurtenissen die kunnen worden gegenereerd: hoge beschikbaarheid, prestaties, beveiliging en kostenoptimalisatie. 

### <a name="sample-event"></a>Voorbeeldgebeurtenis
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>Beschrijvingen van onroerend goed
| Naam van element | Beschrijving |
| --- | --- |
| Kanalen | Altijd "Operatie" |
| correlationId | Een GUID in de tekenreeksindeling. |
| description |Statische tekstbeschrijving van de aanbevelingsgebeurtenis |
| eventDataId | Unieke id van de aanbevelingsgebeurtenis. |
| category | Altijd "Aanbeveling" |
| Id |Unieke resource-id van de aanbevelingsgebeurtenis. |
| niveau |Niveau van het evenement. Een van de volgende waarden: 'Kritiek', 'Fout', 'Waarschuwing' of 'Informatief' |
| operationName |Naam van de bewerking.  Altijd "Microsoft.Advisor/genererenAanbevelingen/actie"|
| resourceGroupName |Naam van de resourcegroep voor de resource. |
| resourceProviderName |Naam van de resourceprovider voor de resource waarop deze aanbeveling van toepassing is, zoals 'MICROSOFT.COMPUTE'. |
| resourceType |Naam van het resourcetype voor de resource waarop deze aanbeveling van toepassing is, zoals 'MICROSOFT.COMPUTE/virtualmachines'. |
| resourceId |Resource-id van de resource waarop de aanbeveling van toepassing is |
| status | Altijd "actief" |
| indieningTijdstempel |Tijdstempel toen de gebeurtenis beschikbaar kwam voor query's. |
| subscriptionId |Azure-abonnements-ID. |
| properties |Set `<Key, Value>` paren (dat wil zeggen een woordenboek) waarin de details van de aanbeveling worden beschreven.|
| properties.recommendationSchemaVersion| Schemaversie van de aanbevelingseigenschappen die zijn gepubliceerd in de vermelding Activiteitslogboek |
| eigenschappen.aanbevelingCategorie | Categorie van de aanbeveling. Mogelijke waarden zijn 'Hoge beschikbaarheid', 'Prestaties', 'Beveiliging' en 'Kosten' |
| properties.recommendationImpact| Impact van de aanbeveling. Mogelijke waarden zijn "Hoog", "Medium", "Laag" |
| eigenschappen.recommendationRisk| Risico van de aanbeveling. Mogelijke waarden zijn 'Fout', 'Waarschuwing', 'Geen' |

## <a name="policy"></a>Beleid

Deze categorie bevat records van alle effectactiebewerkingen die worden uitgevoerd door [Azure Policy](../../governance/policy/overview.md). Voorbeelden van de typen gebeurtenissen die u in deze categorie zou zien, zijn _Audit_ en _Weigeren._ Elke actie die door Beleid wordt ondernomen, is gemodelleerd als een bewerking op een resource.

### <a name="sample-policy-event"></a>Voorbeeldvan beleidsgebeurtenis

```json
{
    "authorization": {
        "action": "Microsoft.Resources/checkPolicyCompliance/read",
        "scope": "/subscriptions/<subscriptionID>"
    },
    "caller": "33a68b9d-63ce-484c-a97e-94aef4c89648",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.azure.com/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "appid": "1d78a85d-813d-46f0-b496-dd72f50a3ec0",
        "appidacr": "2",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "description": "",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Policy",
        "localizedValue": "Policy"
    },
    "eventTimestamp": "2019-01-15T13:19:56.1227642Z",
    "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy/events/13bbf75f-36d5-4e66-b693-725267ff21ce/ticks/636831551961227642",
    "level": "Warning",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Authorization/policies/audit/action",
        "localizedValue": "Microsoft.Authorization/policies/audit/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Sql",
        "localizedValue": "Microsoft SQL"
    },
    "resourceType": {
        "value": "Microsoft.Resources/checkPolicyCompliance",
        "localizedValue": "Microsoft.Resources/checkPolicyCompliance"
    },
    "resourceId": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-01-15T13:20:17.1077672Z",
    "subscriptionId": "<subscriptionID>",
    "properties": {
        "isComplianceCheck": "True",
        "resourceLocation": "westus2",
        "ancestors": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "policies": "[{\"policyDefinitionId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.
            Authorization/policyDefinitions/5775cdd5-d3d3-47bf-bc55-bb8b61746506/\",\"policyDefiniti
            onName\":\"5775cdd5-d3d3-47bf-bc55-bb8b61746506\",\"policyDefinitionEffect\":\"Deny\",\"
            policyAssignmentId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.Authorization
            /policyAssignments/991a69402a6c484cb0f9b673/\",\"policyAssignmentName\":\"991a69402a6c48
            4cb0f9b673\",\"policyAssignmentScope\":\"/subscriptions/<subscriptionID>\",\"policyAssig
            nmentParameters\":{}}]"
    },
    "relatedEvents": []
}
```

### <a name="policy-event-property-descriptions"></a>Beschrijvingen van eigenschappen van beleidsgebeurtenis

| Naam van element | Beschrijving |
| --- | --- |
| autorisatie | Array van RBAC-eigenschappen van de gebeurtenis. Voor nieuwe bronnen is dit de actie en de reikwijdte van het verzoek dat een evaluatie heeft geactiveerd. Voor bestaande bronnen is de actie "Microsoft.Resources/checkPolicyCompliance/read". |
| Beller | Voor nieuwe bronnen, de identiteit die een implementatie heeft gestart. Voor bestaande bronnen is de GUID van de Microsoft Azure Policy Insights RP. |
| Kanalen | Beleidsgebeurtenissen gebruiken alleen het kanaal 'Bewerking'. |
| claims | Het JWT-token dat door Active Directory wordt gebruikt om de gebruiker of toepassing te verifiëren om deze bewerking uit te voeren in Resourcebeheer. |
| correlationId | Meestal een GUID in de tekenreeksindeling. Gebeurtenissen die een correlationId delen, behoren tot dezelfde uber-actie. |
| description | Dit veld is leeg voor beleidsgebeurtenissen. |
| eventDataId | Unieke id van een gebeurtenis. |
| gebeurtenisNaam | "BeginRequest" of "EndRequest". "BeginRequest" wordt gebruikt voor vertraagde auditIfNotExists en deployIfNotExists evaluaties en wanneer een deployIfNotExists effect een template implementatie start. Alle andere bewerkingen retourneren "EndRequest". |
| category | Verklaart de gebeurtenis activiteitenlogboek als behorend tot "Beleid". |
| eventTimestamp | Tijdstempel wanneer de gebeurtenis is gegenereerd door de Azure-service die de aanvraag verwerkt die overeenkomt met de gebeurtenis. |
| Id | Unieke id van de gebeurtenis op de specifieke resource. |
| niveau | Niveau van het evenement. Bij Audit wordt 'Waarschuwing' en Weigeren gebruikt als 'Fout'. Een fout van auditIfNotExists of deployIfNotExists kan 'Waarschuwing' of 'Fout' genereren, afhankelijk van de ernst. Alle andere beleidsgebeurtenissen gebruiken "Informatief". |
| operationId | Een GUID die wordt gedeeld tussen de gebeurtenissen die overeenkomen met één bewerking. |
| operationName | Naam van de bewerking en correleert rechtstreeks met het effect Beleid. |
| resourceGroupName | Naam van de resourcegroep voor de geëvalueerde resource. |
| resourceProviderName | Naam van de resourceprovider voor de geëvalueerde resource. |
| resourceType | Voor nieuwe resources is dit het type dat wordt geëvalueerd. Als u voor bestaande bronnen retourneert, retourneert u 'Microsoft.Resources/checkPolicyCompliance'. |
| resourceId | Resource-id van de geëvalueerde resource. |
| status | Tekenreeks die de status van het resultaat van de beleidsevaluatie beschrijft. De meeste beleidsevaluaties retourneren 'Geslaagd', maar een fout weigeren geeft als resultaat 'Mislukt'. Fouten in auditIfNotExists of deployIfNotExists keren ook 'Mislukt' terug. |
| subStatus | Veld is leeg voor beleidsgebeurtenissen. |
| indieningTijdstempel | Tijdstempel toen de gebeurtenis beschikbaar kwam voor query's. |
| subscriptionId | Azure-abonnements-ID. |
| properties.isComplianceCheck | Retourneert 'False' wanneer een nieuwe resource wordt geïmplementeerd of de eigenschappen resourcebeheer van een bestaande resource worden bijgewerkt. Alle andere [evaluatietriggers](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) resulteren in "True". |
| properties.resourceLocatie | Het Azure-gebied van de resource die wordt geëvalueerd. |
| properties.ancestors | Een door komma's gescheiden lijst van oudermanagementgroepen die van directe ouder tot verste grootouder werden besteld. |
| eigenschappen.beleid | Bevat details over de beleidsdefinitie, toewijzing, effect en parameters waarvan deze beleidsevaluatie het resultaat is. |
| gerelateerdeEvenementen | Dit veld is leeg voor beleidsgebeurtenissen. |


## <a name="schema-from-storage-account-and-event-hubs"></a>Schema van opslagaccount- en gebeurtenishubs
Wanneer u het Azure Activity-logboek streamt naar een opslagaccount of gebeurtenishub, volgen de gegevens het [schema voor bronlogboeken](diagnostic-logs-schema.md). De onderstaande tabel biedt een toewijzing van eigenschappen uit het bovenstaande schema aan het schema voor resourcelogboeken.

> [!IMPORTANT]
> De indeling van activiteitslogboekgegevens die zijn geschreven naar een opslagaccount, is op 1 november 2018 gewijzigd in JSON Lines. Zie [Voorbereiden op formaatwijziging in Azure Monitor-bronlogboeken die zijn gearchiveerd naar een opslagaccount](diagnostic-logs-append-blobs.md) voor meer informatie over deze indelingswijziging.


| Resourcelogboekenschema, eigenschap | ActiviteitslogboekAPI-schema, eigenschap | Opmerkingen |
| --- | --- | --- |
| tijd | eventTimestamp |  |
| resourceId | resourceId | abonnementId, resourceType, resourceGroupName zijn allemaal afgeleid van de resourceId. |
| operationName | operationName.value |  |
| category | Deel van de naam van de bewerking | Uitbraak van het bewerkingstype - "Schrijven"/"Verwijderen"/"Actie" |
| resultType | status.waarde | |
| resultSignature | substatus.waarde | |
| resultDescription | description |  |
| durationMs | N.v.t. | Altijd 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identity | claims en autorisatie-eigenschappen |  |
| Niveau | Niveau |  |
| location | N.v.t. | Locatie van de plaats waar het evenement is verwerkt. *Dit is niet de locatie van de resource, maar waar de gebeurtenis is verwerkt. Deze eigenschap wordt verwijderd in een toekomstige update.* |
| Eigenschappen | properties.eventEigenschappen |  |
| properties.eventCategorie | category | Als properties.eventCategory niet aanwezig is, is categorie 'Administratief' |
| properties.eventName | gebeurtenisNaam |  |
| properties.operationId | operationId |  |
| properties.eventEigenschappen | properties |  |

Hieronder volgt een voorbeeld van een gebeurtenis die dit schema gebruikt..

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```





## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het activiteitenlogboek](platform-logs-overview.md)
* [Een diagnostische instelling maken om activiteitslogboek en logboekanalysewerkruimte, Azure-opslag of gebeurtenishubs te verzenden](diagnostic-settings.md)

