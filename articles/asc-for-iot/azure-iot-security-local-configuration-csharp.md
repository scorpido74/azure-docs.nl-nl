---
title: Informatie over het lokale configuratiebestand van Azure Security Center voor IoT-beveiligingsagent voor C# | Microsoft Documenten
description: Meer informatie over de Azure Security Center for IoT-beveiligingsservice, het lokale configuratiebestand van beveiligingsagenten voor C#.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 0172ada68ffa652fb0c301c89238beca4f4ce2f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74664187"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Inzicht in het lokale configuratiebestand (C#-agent)


De Azure Security Center for IoT-beveiligingsagent gebruikt configuraties van een lokaal configuratiebestand.

De beveiligingsagent leest het configuratiebestand eenmaal wanneer de agent wordt opgestart. Configuraties in het lokale configuratiebestand bevatten zowel verificatieconfiguratie als andere agentgerelateerde configuraties.

De C#-beveiligingsagent gebruikt meerdere configuratiebestanden:

- **General.config** - Agent gerelateerde configuraties.
- **Authentication.config** - Verificatiegerelateerde configuratie (inclusief verificatiegegevens).
- **SecurityIotInterface.config** - IoT gerelateerde configuraties.

De configuratiebestanden bevatten de standaardconfiguratie. Verificatieconfiguratie wordt ingevuld tijdens de installatie van de agent en wijzigingen in het configuratiebestand worden aangebracht wanneer de agent opnieuw wordt opgestart. 

## <a name="configuration-file-location"></a>Locatie van configuratiebestand
Voor Linux:
- Configuratiebestanden van het `/var/ASCIoTAgent`besturingssysteem bevinden zich in .

Voor Windows:
- Configuratiebestanden van het besturingssysteem bevinden zich in de map van de beveiligingsagent. 

### <a name="generalconfig-configurations"></a>Algemene configuraties van config

| Configuratienaam | Mogelijke waarden | Details | 
|:-----------|:---------------|:--------|
| agentId | GUID | Unieke agent-id |
| readRemoteConfigurationTimeout lezen | TimeSpan | Tijdsperiode voor het ophalen van externe configuratie van IoT Hub. Als de agent de configuratie niet binnen de opgegeven tijd kan ophalen, wordt er een time-out van de bewerking.|
| schedulerInterval | TimeSpan | Interval van interne planner. |
| producentInterval | TimeSpan | Interval van de producent van gebeurtenissen. |
| consumentInterval | TimeSpan | Interval van de consument van de gebeurtenis. |
| highPriorityQueueSizePercentage | 0 < nummer < 1 | Het gedeelte van de totale cache dat is toegewezen aan berichten met een hoge prioriteit. |
| Loglevel | "Uit", "Fatal", "Error", "Warning", "Information", "Debug"  | Log berichten gelijk en boven deze ernst worden geregistreerd op debug console (Syslog in Linux). |
| fileLogLevel |  "Uit", "Fatal", "Error", "Warning", "Information", "Debug"| Log berichten gelijk en boven deze ernst worden geregistreerd om bestand (Syslog in Linux). |
| diagnosticVerbosityLevel | "Geen", "Sommige", "Alle", | Verbosity niveau van diagnostische gebeurtenissen. Geen - diagnostische gebeurtenissen worden niet verzonden, Sommige - Alleen diagnostische gebeurtenissen met een hoog belang worden verzonden, Alle - alle logboeken worden ook verzonden als diagnostische gebeurtenissen. |
| logFilePath | Pad naar bestand | Als fileLogLevel > uit, worden logboeken naar dit bestand geschreven. |
| defaultEventPriority defaultEventPriority defaultEventPriority defaultEvent | "Hoog", "Laag", "Uit" | Standaardgebeurtenisprioriteit. |

### <a name="generalconfig-example"></a>Voorbeeld van General.config
```XML
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Authentication.config

| Configuratienaam | Mogelijke waarden | Details | 
|:-----------|:---------------|:--------|
| moduleNaam | tekenreeks | Naam van de identiteit van de beveiligingsmodule. Deze naam moet overeenkomen met de naam van de moduleidentiteit in het apparaat. |
| deviceId | tekenreeks | ID van het apparaat (zoals geregistreerd in Azure IoT Hub). || schedulerInterval | Tijdspantekenreeks | Interval van interne planner. |
| gatewayHostname | tekenreeks | Hostnaam van de Azure Iot Hub. Meestal <mijn-hub>.azure-devices.net |
| Filepath | tekenreeks - pad naar bestand | Pad naar het bestand dat het verificatiegeheim bevat.|
| type | "SymmetricKey", "SelfSignedCertificate" | Het gebruikersgeheim voor verificatie. Kies *SymmetricKey* als het gebruikersgeheim een symmetrische sleutel is, kies *zelfondertekend certificaat* als het geheim een zelfondertekend certificaat is. |
| identity | "DPS", "Module", "Apparaat" | Verificatie-identiteit - DPS als verificatie wordt uitgevoerd via DPS, Module als verificatie wordt gemaakt met behulp van modulereferenties of als verificatie wordt uitgevoerd met behulp van apparaatreferenties.
| certificaatLocationKind |  "LocalFile", "Store" | LocalFile als het certificaat is opgeslagen in een bestand, slaat u op als het certificaat zich in een certificaatarchief bevindt. |
| idScope | tekenreeks | ID-bereik van DPS |
| registratieId | tekenreeks  | DPS-apparaatregistratie-ID. |
|

### <a name="authenticationconfig-example"></a>Voorbeeld van Authentication.config
```XML
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```
### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| Configuratienaam | Mogelijke waarden | Details | 
|:-----------|:---------------|:--------|
| transportType | "Ampq" "Mqtt" | IoT Hub-transporttype. |
|

### <a name="securityiotinterfaceconfig-example"></a>Voorbeeld van SecurityIotInterface.config
```XML
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Volgende stappen
- Lees het [overzicht](overview.md) van de Azure Security Center for IoT-service
- Meer informatie over Azure Security Center voor [IoT-architectuur](architecture.md)
- De Azure Security Center for IoT-service [inschakelen](quickstart-onboard-iot-hub.md)
- Lees de veelgestelde vragen over azure security center voor [IoT-service](resources-frequently-asked-questions.md)
- Meer informatie over hoe u toegang krijgt tot [ruwe beveiligingsgegevens](how-to-security-data-access.md)
- Aanbevelingen [begrijpen](concept-recommendations.md)
- [Beveiligingswaarschuwingen begrijpen](concept-security-alerts.md)