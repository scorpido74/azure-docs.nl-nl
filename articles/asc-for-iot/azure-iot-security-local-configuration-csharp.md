---
title: Informatie over Azure Security Center voor het lokale configuratie bestand van de C# IOT-beveiligings agent voor | Microsoft Docs
description: Meer informatie over de Azure Security Center voor de IoT-beveiligings service, het lokale configuratie bestand C#van de beveiligings agent voor.
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
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74664187"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Informatie over het lokale configuratie bestandC# (agent)


In de Azure Security Center voor IoT-beveiligings agent worden configuraties van een lokaal configuratie bestand gebruikt.

De beveiligings agent leest het configuratie bestand eenmaal wanneer de agent wordt gestart. Configuraties die in het lokale configuratie bestand worden gevonden, bevatten zowel de verificatie configuratie als andere aan de agent gerelateerde configuraties.

De C# beveiligings agent maakt gebruik van meerdere configuratie bestanden:

- **Algemeen. config** -gerelateerde configuraties van agent.
- **Verificatie. config** -gerelateerde configuratie van authenticatie (inclusief verificatie gegevens).
- Configuratie van **SecurityIotInterface. config** -gerelateerde IOT.

De configuratie bestanden bevatten de standaard configuratie. Verificatie configuratie wordt ingevuld tijdens de installatie van de agent en wijzigingen in het configuratie bestand worden gemaakt wanneer de agent opnieuw wordt gestart. 

## <a name="configuration-file-location"></a>Locatie van configuratie bestand
Voor Linux:
- Configuratie bestanden voor het besturings systeem bevinden zich in `/var/ASCIoTAgent`.

Voor Windows:
- Configuratie bestanden voor het besturings systeem bevinden zich in de map van de beveiligings agent. 

### <a name="generalconfig-configurations"></a>Algemene configuratie configuraties

| Configuratie naam | Mogelijke waarden | Details | 
|:-----------|:---------------|:--------|
| agentId | GPT | Unieke id van agent |
| readRemoteConfigurationTimeout | Duur | De tijds periode voor het ophalen van de externe configuratie van IoT Hub. Als de agent de configuratie niet binnen de opgegeven tijd kan ophalen, wordt er een time-out van de bewerking uitgevoerd.|
| schedulerInterval | Duur | Intern scheduler-interval. |
| producerInterval | Duur | Werk interval van de gebeurtenis producent. |
| consumerInterval | Duur | Interval van gebeurtenis verbruiker werk nemer. |
| highPriorityQueueSizePercentage | 0 < getal < 1 | Het gedeelte van het totale cache geheugen dat is toegewezen voor berichten met een hoge prioriteit. |
| LogLevel | "Off", "onherstelbare", "fout", "waarschuwing", "informatie", "fouten opsporen"  | Logboek berichten die gelijk zijn aan deze Ernst, worden vastgelegd in de console fout opsporing (syslog in Linux). |
| fileLogLevel |  "Off", "onherstelbare", "fout", "waarschuwing", "informatie", "fouten opsporen"| Logboek berichten die gelijk zijn aan deze Ernst, worden geregistreerd in een bestand (syslog in Linux). |
| diagnosticVerbosityLevel | "Geen", "enkele", "alle", | Uitgebreidheids niveau van diagnostische gebeurtenissen. Geen: er worden geen diagnostische gebeurtenissen verzonden, alleen bepaalde diagnostische gebeurtenissen met hoge urgentie worden verzonden, alle-alle logboeken worden ook als diagnostische gebeurtenissen verzonden. |
| logFilePath | Pad naar bestand | Als fileLogLevel > uit, worden logboeken naar dit bestand geschreven. |
| defaultEventPriority | ' Hoog ', ' laag ', ' uit ' | Standaard prioriteit van gebeurtenis. |

### <a name="generalconfig-example"></a>Algemeen. config-voor beeld
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

### <a name="authenticationconfig"></a>Verificatie. config

| Configuratie naam | Mogelijke waarden | Details | 
|:-----------|:---------------|:--------|
| moduleName | string | De naam van de identiteit van de beveiligings module. Deze naam moet overeenkomen met de naam van de module-id in het apparaat. |
| deviceId | string | ID van het apparaat (zoals geregistreerd in azure IoT Hub). || schedulerInterval | Time span-teken reeks | Intern scheduler-interval. |
| gatewayHostname | string | De hostnaam van de Azure IOT hub. Doorgaans < mijn hub >. Azure-devices.net |
| Bestandspad | teken reeks-pad naar bestand | Het pad naar het bestand dat het verificatie geheim bevat.|
| type | "SymmetricKey", "SelfSignedCertificate" | Het gebruikers geheim voor authenticatie. Kies *SymmetricKey* als het gebruikers geheim een symmetrische sleutel is, kies het *zelfondertekende certificaat* als het geheim een zelf ondertekend certificaat is. |
| identiteit | ' DPS ', ' module ', ' apparaat ' | Verificatie-identiteit: DPS als verificatie wordt uitgevoerd via DPS, module als verificatie wordt uitgevoerd met behulp van de module referenties of het apparaat als verificatie wordt uitgevoerd met behulp van de referenties van het apparaat.
| certificateLocationKind |  "LocalFile", "Store" | LocalFile als het certificaat wordt opgeslagen in een bestand, opslaan als het certificaat zich in een certificaat archief bevindt. |
| idScope | string | ID-bereik van DPS |
| Registratie | string  | Registratie-ID van DPS-apparaat. |
|

### <a name="authenticationconfig-example"></a>Verificatie. config-voor beeld
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
### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface. config

| Configuratie naam | Mogelijke waarden | Details | 
|:-----------|:---------------|:--------|
| Transport type | "Ampq" "Mqtt" | Transport type IoT Hub. |
|

### <a name="securityiotinterfaceconfig-example"></a>Voor beeld van SecurityIotInterface. config
```XML
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Volgende stappen
- Lees het [overzicht](overview.md) van de Azure Security Center voor IOT-service
- Meer informatie over Azure Security Center voor IoT- [architectuur](architecture.md)
- De Azure Security Center inschakelen voor IoT- [service](quickstart-onboard-iot-hub.md)
- Lees de [Veelgestelde vragen over](resources-frequently-asked-questions.md) de Azure Security Center voor IOT-service
- Meer informatie over het openen van [onbewerkte beveiligings gegevens](how-to-security-data-access.md)
- Meer informatie over [aanbevelingen](concept-recommendations.md)
- Beveiligings [waarschuwingen](concept-security-alerts.md) begrijpen