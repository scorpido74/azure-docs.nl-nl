---
title: Toegang tot gegevens met Azure Security Center for IoT| Microsoft Documenten
description: Meer informatie over hoe u toegang krijgt tot uw beveiligingswaarschuwing en aanbevelingsgegevens wanneer u Azure Security Center voor IoT gebruikt.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3ddd9b2c8373746a65cd78f0a81b60d097cd9f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68597176"
---
# <a name="access-your-security-data"></a>Toegang tot uw beveiligingsgegevens 

Azure Security Center for IoT slaat beveiligingswaarschuwingen, aanbevelingen en onbewerkte beveiligingsgegevens (als u ervoor kiest deze op te slaan) op in uw Log Analytics-werkruimte.

## <a name="log-analytics"></a>Log Analytics

Ga als u configureren welke Log Analytics-werkruimte wordt gebruikt:

1. Open je IoT-hub.
1. Klik op het **blad Overzicht** onder de sectie **Beveiliging**
2. Klik **op Instellingen**en wijzig de configuratie van de Log Analytics-werkruimte.

Ga als volgt te werk om na configuratie toegang te krijgen tot uw waarschuwingen en aanbevelingen in uw Log Analytics-werkruimte:

1. Kies een waarschuwing of aanbeveling in Azure Security Center for IoT. 
2. Klik **op verder onderzoek**en klik vervolgens op Om te zien welke apparaten deze waarschuwing **hebben, klik hier en bekijk de kolom DeviceId**.

Zie [Aan de slag met query's in Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries)voor meer informatie over het opvragen van gegevens uit Log Analytics.

## <a name="security-alerts"></a>Beveiligingswaarschuwingen

Beveiligingswaarschuwingen worden opgeslagen in de tabel _AzureSecurityOfThings.SecurityAlert_ in de log Analytics-werkruimte die is geconfigureerd voor de Azure Security Center for IoT-oplossing.

We hebben een aantal nuttige vragen beschikbaar gesteld om u op weg te helpen bij het verkennen van beveiligingswaarschuwingen.

### <a name="sample-records"></a>Voorbeeldrecords

Selecteer een paar willekeurige records

```
// Select a few random records
//
SecurityAlert
| project 
    TimeGenerated, 
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity, 
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IotHubId                                                                                                       | DeviceId      | WaarschuwingErnst | DisplayName                           | Beschrijving                                             | ExtendedProperties ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /abonnementen/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hoog          | Brute kracht aanval geslaagd           | Een brute kracht aanval op het apparaat was succesvol        |    { "Full Source\"Address": "[ 10.165.12.18:\"]",\"\""Gebruikersnamen": "[ ]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /abonnementen/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hoog          | Succesvol lokaal inloggen op apparaat      | Een succesvolle lokale aanmelding op het apparaat is gedetecteerd     | { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "28207", "Gebruikersnaam": "aanvaller", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /abonnementen/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hoog          | Mislukte lokale aanmeldingspoging op apparaat  | Er is een mislukte lokale aanmeldingspoging bij het apparaat gedetecteerd |  { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "22644", "Gebruikersnaam": "aanvaller", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>Apparaatoverzicht

Ontvang het aantal afzonderlijke beveiligingswaarschuwingen dat in de afgelopen week is gedetecteerd, gegroepeerd op IoT Hub, apparaat, waarschuwingsernst, waarschuwingstype.

```
// Get the number of distinct security alerts detected in the last week, grouped by 
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IotHubId                                                                                                       | DeviceId      | WaarschuwingErnst | DisplayName                           | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /abonnementen/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hoog          | Brute kracht aanval geslaagd           | 9   |   
| /abonnementen/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Middelgroot        | Mislukte lokale aanmeldingspoging op apparaat  | 242 |    
| /abonnementen/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hoog          | Succesvol lokaal inloggen op apparaat      | 31  |
| /abonnementen/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Middelgroot        | Crypto Munt Mijnwerker                     | 4   |

### <a name="iot-hub-summary"></a>IoT-huboverzicht

Selecteer een aantal verschillende apparaten die in de afgelopen week waarschuwingen hebben gehad, op IoT Hub, waarschuwingsernst, waarschuwingstype

```
// Select number of distinct devices which had alerts in the last week, by 
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId, 
    AlertSeverity,
    DisplayName
```

| IotHubId                                                                                                       | WaarschuwingErnst | DisplayName                           | CntDevices CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /abonnementen/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Hoog          | Brute kracht aanval geslaagd           | 1          |    
| /abonnementen/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Middelgroot        | Mislukte lokale aanmeldingspoging op apparaat  | 1          | 
| /abonnementen/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Hoog          | Succesvol lokaal inloggen op apparaat      | 1          |
| /abonnementen/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Middelgroot        | Crypto Munt Mijnwerker                     | 1          |

## <a name="security-recommendations"></a>Aanbevelingen voor beveiliging

Beveiligingsaanbevelingen worden opgeslagen in _azuresecurityofthings.De_ tabel SecurityAanbeveling in de werkruimte Log Analytics die is geconfigureerd voor de Azure Security Center for IoT-oplossing.

We hebben een aantal nuttige vragen beschikbaar gesteld om u te helpen bij het verkennen van beveiligingsaanbevelingen.

### <a name="sample-records"></a>Voorbeeldrecords

Selecteer een paar willekeurige records

```
// Select a few random records
//
SecurityRecommendation
| project 
    TimeGenerated, 
    IoTHubId=AssessedResourceId, 
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```
    
| TimeGenerated | IotHubId | DeviceId | AanbevelingErnst | AanbevelingStaat | AanbevelingDisplayName | Beschrijving | AanbevelingAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /abonnementen/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Middelgroot | Actief | Tolerante firewallregel in de invoerketen is gevonden | Er is een regel in de firewall gevonden die een tolerant patroon bevat voor een breed scala aan IP-adressen of poorten | {"Regels":"[{\"\"SourceAddress\"\"\":\",\"\"\"SourcePort\"\"\":\",\"\"DestinationAddress :\", DestinationPort : 1337 }]"} |
| 2019-03-22T10:50:27.237 | /abonnementen/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Middelgroot | Actief | Tolerante firewallregel in de invoerketen is gevonden | Er is een regel in de firewall gevonden die een tolerant patroon bevat voor een breed scala aan IP-adressen of poorten | {"Regels":"[{\"\"SourceAddress\"\"\":\",\"\"\"SourcePort\"\"\":\",\"\"DestinationAddress :\", DestinationPort : 1337 }]"} |

### <a name="device-summary"></a>Apparaatoverzicht

Krijg het aantal verschillende actieve beveiligingsaanbevelingen, gegroepeerd op IoT Hub, apparaat, aanbevelingsernst en type.

```
// Get the number of distinct active security recommendations, grouped by by 
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IotHubId                                                                                                       | DeviceId      | AanbevelingErnst | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /abonnementen/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hoog          | 2   |    
| /abonnementen/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Middelgroot        | 1 |  
| /abonnementen/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hoog          | 1  |
| /abonnementen/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Middelgroot        | 4   |


## <a name="next-steps"></a>Volgende stappen

- Lees het Azure Security Center for [IoT-overzicht](overview.md)
- Meer informatie over Azure Security Center voor [IoT-architectuur](architecture.md)
- Azure [Security Center voor IoT-waarschuwingen](concept-security-alerts.md) begrijpen en verkennen
- [Azure Security Center voor IoT-aanbeveling begrijpen](concept-recommendations.md) en verkennen
