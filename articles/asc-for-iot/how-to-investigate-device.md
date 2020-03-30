---
title: Azure Security Center voor IoT-apparaatonderzoekshandleiding| Microsoft Documenten
description: In deze handleiding wordt uitgelegd hoe u Azure Security Center for IoT gebruiken om een verdacht IoT-apparaat te onderzoeken met behulp van Log Analytics.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 8d2fe8d63c7ece6f3b3426d8fc5a3454a61826f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596250"
---
# <a name="investigate-a-suspicious-iot-device"></a>Een verdacht IoT-apparaat onderzoeken

Azure Security Center for IoT-servicewaarschuwingen geven duidelijke aanwijzingen wanneer IoT-apparaten worden verdacht van betrokkenheid bij verdachte activiteiten of wanneer er aanwijzingen zijn dat een apparaat is gecompromitteerd. 

Gebruik in deze handleiding de onderzoekssuggesties die worden verstrekt om de potentiële risico's voor uw organisatie te bepalen, te beslissen hoe u moet saneren en ontdek de beste manieren om vergelijkbare aanvallen in de toekomst te voorkomen.  

> [!div class="checklist"]
> * Uw apparaatgegevens zoeken
> * Onderzoeken met kql-query's


## <a name="how-can-i-access-my-data"></a>Hoe heb ik toegang tot mijn gegevens?

Azure Security Center for IoT slaat standaard uw beveiligingswaarschuwingen en aanbevelingen op in uw Log Analytics-werkruimte. U er ook voor kiezen om uw ruwe beveiligingsgegevens op te slaan.

Ga als u uw Log Analytics-werkruimte zoeken naar gegevensopslag:

1. Open uw IoT-hub, 
1. Klik **onder Beveiliging**op **Overzicht**en selecteer **Instellingen**.
1. Wijzig de configuratiegegevens van uw Log Analytics-werkruimte. 
1. Klik op **Opslaan**. 

Ga als volgt te werk om toegang te krijgen tot gegevens die zijn opgeslagen in uw Log Analytics-werkruimte:

1. Selecteer en klik op een Azure Security Center for IoT-waarschuwing in uw IoT-hub. 
1. Klik **op Verder onderzoek**. 
1. Selecteer **Om te zien welke apparaten deze waarschuwing hebben, klik hier en bekijk de kolom DeviceId**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Onderzoeksstappen voor verdachte IoT-apparaten

Als u inzichten en ruwe gegevens over uw IoT-apparaten wilt bekijken, gaat u naar uw Log Analytics-werkruimte [om toegang te krijgen tot uw gegevens.](#how-can-i-access-my-data)

Bekijk het voorbeeld van kql-query's hieronder om aan de slag te gaan met het onderzoeken van waarschuwingen en activiteiten op uw apparaat.

### <a name="related-alerts"></a>Gerelateerde waarschuwingen

Ga als volgt te werk om erachter te komen of andere waarschuwingen rond dezelfde tijd zijn geactiveerd, gebruikt u de volgende kql-query:

  ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ```

### <a name="users-with-access"></a>Gebruikers met toegang

Als u wilt weten welke gebruikers toegang hebben tot dit apparaat, gebruikt u de volgende kql-query: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
 ```
Gebruik deze gegevens om te ontdekken: 
- Welke gebruikers hebben toegang tot het apparaat?
- Hebben de gebruikers met toegang de verwachte machtigingsniveaus?

### <a name="open-ports"></a>Poorten openen

Als u wilt weten welke poorten in het apparaat momenteel in gebruik zijn of zijn gebruikt, gebruikt u de volgende kql-query: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
 ```

Gebruik deze gegevens om te ontdekken:
- Welke luisteraansluitingen zijn momenteel actief op het apparaat?
- Moeten de luisteraansluitingen die momenteel actief zijn, worden toegestaan?
- Zijn er verdachte externe adressen verbonden met het apparaat?

### <a name="user-logins"></a>Gebruikersaanmeldingen

Als u gebruikers wilt vinden die zijn aangemeld bij het apparaat, gebruikt u de volgende kql-query: 
 
 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
 ```

Gebruik de queryresultaten om te ontdekken:
- Welke gebruikers hebben zich aangemeld bij het apparaat?
- Moeten de gebruikers die zijn ingelogd, inloggen?
- Hebben de gebruikers die ingelogd zijn verbinding gemaakt vanaf verwachte of onverwachte IP-adressen?
  
### <a name="process-list"></a>Proceslijst

Ga als volgt te werk om erachter te komen of de proceslijst is zoals verwacht, de volgende kql-query: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
```

Gebruik de queryresultaten om te ontdekken:

- Waren er verdachte processen die op het apparaat worden uitgevoerd?
- Zijn processen uitgevoerd door geschikte gebruikers?
- Bevatten uitvoeringen van opdrachtregeluitvoeringen de juiste en verwachte argumenten?

## <a name="next-steps"></a>Volgende stappen

Nadat u een apparaat hebt onderzocht en uw risico's beter hebt begrepen, u overwegen [aangepaste waarschuwingen](quickstart-create-custom-alerts.md) te configureren om de beveiligingshouding van uw IoT-oplossing te verbeteren. Als u nog geen apparaatagent hebt, u overwegen [een beveiligingsagent te implementeren](how-to-deploy-agent.md) of de configuratie van een bestaande [apparaatagent](how-to-agent-configuration.md) te wijzigen om uw resultaten te verbeteren. 
