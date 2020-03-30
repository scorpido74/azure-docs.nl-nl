---
title: bestand opnemen
description: bestand opnemen
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 0b6864c3304b86e80549297fc073a2e387000d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272845"
---
```json
{
  "VendorName": "Microsoft",
  "AlertType": "SUSPECT_SVCHOST",
  "StartTimeUtc": "2016-12-20T13:38:00.000Z",
  "EndTimeUtc": "2019-12-20T13:40:01.733Z",
  "ProcessingEndTime": "2019-09-16T12:10:19.5673533Z",
  "TimeGenerated": "2016-12-20T13:38:03.000Z",
  "IsIncident": false,
  "Severity": "High",
  "Status": "New",
  "ProductName": "Azure Security Center",
  "SystemAlertId": "2342409243234234_F2BFED55-5997-4FEA-95BD-BB7C6DDCD061",
  "CompromisedEntity": "WebSrv1",
  "Intent": "Execution",
  "AlertDisplayName": "Suspicious process detected",
  "Description": "Suspicious process named 'SVCHOST.EXE' was running from path: %{Process Path}",
  "RemediationSteps": ["contact your security information team"],
  "ExtendedProperties": {
    "Process Path": "c:\\temp\\svchost.exe",
    "Account": "Contoso\\administrator",
    "PID": 944,
    "ActionTaken": "Detected"
  },
  "Entities": [],
  "ResourceIdentifiers": [
        {
            Type: "AzureResource",
            AzureResourceId: "/subscriptions/86057C9F-3CDD-484E-83B1-7BF1C17A9FF8/resourceGroups/backend-srv/providers/Microsoft.Compute/WebSrv1"
        },
        {
            Type: "LogAnalytics",
            WorkspaceId: "077BA6B7-8759-4F41-9F97-017EB7D3E0A8",
            WorkspaceSubscriptionId: "86057C9F-3CDD-484E-83B1-7BF1C17A9FF8",
            WorkspaceResourceGroup: "omsrg",
            AgentId: "5A651129-98E6-4E6C-B2CE-AB89BD815616",
        }
  ]
}
```

### <a name="the-data-model-of-the-schema"></a>Het gegevensmodel van het schema

|Veld|Gegevenstype|Beschrijving|
|----|----|----|
|**AlertDisplayName**|Tekenreeks|De weergavenaam van de waarschuwing.|
|**Waarschuwingstype**|Tekenreeks|Het type waarschuwing. Waarschuwingen van hetzelfde type moeten dezelfde waarde hebben. Dit veld is een toetstekenreeks die het type waarschuwing weergeeft en niet van een waarschuwingsinstantie. Alle waarschuwingsinstanties van dezelfde detectielogica/analytische moeten dezelfde waarde hebben voor waarschuwingstype.|
|**Gecompromitteerde entiteit**|Tekenreeks|De weergavenaam van de bron die het meest gerelateerd is aan deze waarschuwing.|
|**Beschrijving**|Tekenreeks|Beschrijving van de waarschuwing.|
|**EndTimeUtc**|DateTime|De tijd van de laatste gebeurtenis of activiteit die in de waarschuwing is opgenomen.  Het veld moet een tekenreeks zijn die voldoet aan de ISO8601-indeling, inclusief UTC-tijdzone-informatie.|
|**Entiteiten**|IEnumerable (IEntity)|Een lijst met entiteiten die verband houden met de waarschuwing. Deze lijst kan een mengsel van entiteiten van verschillende soorten bevatten. Het type entiteiten kan een van de typen zijn die zijn gedefinieerd in de sectie Entiteiten. Entiteiten die niet in de onderstaande lijst staan, kunnen ook worden verzonden, maar het is niet gegarandeerd dat ze worden verwerkt (de waarschuwing mislukt niet met de validatie met nieuwe typen entiteiten).|
|**ExtendedProperties ExtendedProperties**|Woordenboek (tekenreeks, tekenreeks)|Providers kunnen (optioneel) hier aangepaste velden opnemen.|
|**Intentie**|Enum|De moordketen gerelateerde intentie achter de waarschuwing. Zie Intenties voor een lijst met ondersteunde waarden en uitleg over de ondersteunde [intenties](../articles/security-center/alerts-reference.md#intentions)van Azure Security Center.<br/>Dit veld kan meerdere waarden hebben (gescheiden door komma's).|
|**IsIncident IsIncident**|Booleaanse waarde|Dit veld bepaalt of de waarschuwing een incident is (een samengestelde groepering van meerdere waarschuwingen) of één waarschuwing. Standaardwaarde voor het veld is 'false' (wat betekent dat het een enkele waarschuwing is).|
|**Verwerkingseindtijd**|DateTime|De tijd dat de waarschuwing toegankelijk was voor de eindgebruiker in het oorspronkelijke product dat de waarschuwing vasthoudt.|
|**ProductName**|Tekenreeks|De naam van het product dat deze waarschuwing heeft gepubliceerd (Azure Security Center, Azure ATP, Microsoft Defender ATP, O365 ATP, MCAS, enzovoort).|
|**Herstelstappen**|Lijst<String>|Handmatige actie-items die u moet uitvoeren om de waarschuwing te herstellen.|
|**Resource-id's**|Lijst (resource-id's)|De resource-id's voor deze waarschuwing die kunnen worden gebruikt om de waarschuwing naar de juiste groep productblootstelling te leiden (tenant, werkruimte, abonnement, enz.). Er kunnen meerdere id's van verschillend type per waarschuwing zijn.|
|**Ernst**|Enum|De ernst van de waarschuwing zoals gerapporteerd door de provider. Mogelijke waarden: informatief, laag, gemiddeld en hoog.|
|**StartTimeUtc**|DateTime|Het tijdstip van de eerste gebeurtenis of activiteit die in de waarschuwing is opgenomen. Het veld moet een tekenreeks zijn die voldoet aan de ISO8601-indeling, inclusief UTC-tijdzone-informatie.|
|**Status**|Enum|De levenscyclusstatus van de waarschuwing.<br/>Ondersteunde statussen zijn: Nieuw, Opgelost, Ontslagen, Onbekend.<br/>Aan een waarschuwing die een andere waarde opgeeft dan de ondersteunde opties, wordt de status 'Onbekend' toegewezen.<br/>Aan een waarschuwing die geen waarde opgeeft, wordt de status 'Nieuw' toegewezen.|
|**SystemAlertId**|Tekenreeks|De waarschuwings-id.|
|**TimeGenerated**|DateTime|De tijd dat de waarschuwing is gegenereerd door de waarschuwingsprovider. Als een product niet wordt gerapporteerd door interne waarschuwingsproviders, kan het ervoor kiezen om de tijd toe te wijzen die is ontvangen voor verwerking door het product.  Het veld moet een tekenreeks zijn die voldoet aan de ISO8601-indeling, inclusief UTC-tijdzone-informatie.|
|**Naam van leverancier**|Tekenreeks|De naam van de leverancier die de waarschuwing verhoogt.|
|||
