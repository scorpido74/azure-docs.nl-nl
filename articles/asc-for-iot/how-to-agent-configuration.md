---
title: Azure Security Center configureren voor IoT-agent| Microsoft Documenten
description: Meer informatie over het configureren van Azure Security Center voor IoT-beveiligingsagents voor gebruik met de Azure Security Center for IoT-beveiligingsservice.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: 70396cdcaf8b6e2ac66619290eea35a7b260cd9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461246"
---
# <a name="tutorial-configure-security-agents"></a>Zelfstudie: Beveiligingsagents configureren

In dit artikel wordt uitgelegd in Azure Security Center voor IoT-beveiligingsagents en wordt uitgelegd hoe u deze wijzigen en configureren. 

> [!div class="checklist"]
> * Beveiligingsagents configureren
> * Agentgedrag wijzigen door dubbele eigenschappen te bewerken
> * Standaardconfiguratie ontdekken

## <a name="agents"></a>Agents

Azure Security Center voor IoT-beveiligingsagents verzamelen gegevens van IoT-apparaten en voeren beveiligingsacties uit om de gedetecteerde kwetsbaarheden te beperken. Configuratie van beveiligingsagentis controleerbaar met behulp van een set van module twin eigenschappen die u aanpassen. Over het algemeen komen secundaire updates voor deze eigenschappen zelden voor.  

Azure Security Center for IoT's security agent twin configuration object is een JSON-indelingsobject. Het configuratieobject is een set controleerbare eigenschappen die u definiëren om het gedrag van de agent te bepalen. 

Met deze configuraties u de agent aanpassen voor elk scenario dat nodig is. Het is bijvoorbeeld mogelijk om bepaalde gebeurtenissen automatisch uit te sluiten of het stroomverbruik tot een minimum te beperken door deze eigenschappen te configureren.  

Gebruik het [configuratieschema](https://aka.ms/iot-security-github-module-schema) van azure security center voor IoT-beveiligingsagent om wijzigingen aan te brengen.  

## <a name="configuration-objects"></a>Configuratieobjecten 

Eigenschappen die verband houden met elke Azure Security Center voor IoT-beveiligingsagent bevinden zich in het configuratieobject van de agent, binnen de gewenste eigenschappensectie, van de **azureiotsecurity-module.** 

Als u de configuratie wilt wijzigen, maakt en wijzigt u dit object in de dubbele identiteit van de **azureiotsecurity-module.** 

Als het agentconfiguratieobject niet bestaat in de **azureiotsecuritymoduletwin,** zijn alle eigenschapswaarden van beveiligingsagent ingesteld op standaard. 

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  } 
}
```

## <a name="configuration-schema-and-validation"></a>Configuratieschema en validatie 

Zorg ervoor dat u de configuratie van uw agent valideert aan de hand van dit [schema.](https://aka.ms/iot-security-github-module-schema) Een agent wordt niet gestart als het configuratieobject niet overeenkomt met het schema.

 
Als het configuratieobject tijdens het uitvoeren van de agent wordt gewijzigd in een niet-geldige configuratie (de configuratie komt niet overeen met het schema), negeert de agent de ongeldige configuratie en blijft de huidige configuratie gebruiken. 

### <a name="configuration-validation"></a>Configuratievalidatie

Azure Security Center for IoT security agent rapporteert de huidige configuratie binnen het gedeelte gerapporteerde eigenschappen van de dubbele identiteit van de **azureiotsecurity** module.
De agent rapporteert alle beschikbare eigenschappen, als een eigenschap niet is ingesteld door de gebruiker, rapporteert de agent de standaardconfiguratie.

Als u uw configuratie wilt valideren, vergelijkt u de waarden die op de gewenste sectie zijn ingesteld met de waarden die in de gerapporteerde sectie worden gerapporteerd.

Als er een mismatch is tussen de gewenste en de gerapporteerde eigenschappen, kon de agent de configuratie niet ontleeden.

Valideer uw gewenste eigenschappen aan de hand van het [schema,](https://aka.ms/iot-security-github-module-schema)repareer de fouten en stel uw gewenste eigenschappen opnieuw in!

> [!NOTE]
> Er wordt een configuratiefoutwaarschuwing van de agent geactiveerd in het geval dat de agent de gewenste configuratie niet kon ontleeden.
> Vergelijk de gerapporteerde en gewenste sectie om te begrijpen of de waarschuwing nog steeds van toepassing is

## <a name="editing-a-property"></a>Een eigenschap bewerken 

Alle aangepaste eigenschappen moeten worden ingesteld in het configuratieobject van de agent binnen de **azureiotsecurity-moduletwee.**
Als u een standaardeigenschapswaarde wilt gebruiken, verwijdert u de eigenschap uit het configuratieobject.

### <a name="setting-a-property"></a>Een eigenschap instellen

1. Zoek en selecteer in uw IoT-hub het apparaat dat u wilt wijzigen.

1. Klik op uw apparaat en vervolgens op **de azureiotsecuritymodule.**

1. Klik op **Module Identity Twin**.

1. Bewerk de eigenschappen die u wilt wijzigen in de beveiligingsmodule.
   
   Als u bijvoorbeeld verbindingsgebeurtenissen als hoge prioriteit wilt configureren en gebeurtenissen met hoge prioriteit elke 7 minuten wilt verzamelen, gebruikt u de volgende configuratie.
   
    ```json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
            "highPriorityMessageFrequency": {
                "value": "PT7M"
            },
            "eventPriorityConnectionCreate": {
                "value": "High"
            }
        }
    }
    ```

1. Klik op **Opslaan**.

### <a name="using-a-default-value"></a>Een standaardwaarde gebruiken

Als u een standaardeigenschapswaarde wilt gebruiken, verwijdert u de eigenschap uit het configuratieobject.

## <a name="default-properties"></a>Standaardeigenschappen 

De volgende tabel bevat de controleerbare eigenschappen van Azure Security Center voor IoT-beveiligingsagents.

Standaardwaarden zijn beschikbaar in het juiste schema in [GitHub](https\://aka.ms/iot-security-module-default).

| Name| Status | Geldige waarden| Standaardwaarden| Beschrijving |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Vereist: false: false: |Geldige waarden: duur in ISO 8601-notatie |Standaardwaarde: PT7M |Maximaal tijdsinterval voordat berichten met hoge prioriteit worden verzonden.|
|lowPriorityMessageFrequency lowPriorityMessageFrequency lowPriorityMessageFrequency lowPriority |Vereist: false: false:|Geldige waarden: duur in ISO 8601-notatie |Standaardwaarde: PT5H |Maximale tijd voordat berichten met lage prioriteit worden verzonden.| 
|momentopnameFrequentie |Vereisen: false|Geldige waarden: duur in ISO 8601-notatie |Standaardwaarde PT13H |Tijdsinterval voor het maken van momentopnamen voor apparaatstatus.| 
|maxLocalCacheSizeInBytes |Vereist: false: false: |Geldige waarden: |Standaardwaarde: 2560000, groter dan 8192 | Maximale opslag (in bytes) toegestaan voor de berichtencache van een agent. Maximale hoeveelheid ruimte toegestaan om berichten op het apparaat op te slaan, voordat berichten worden verzonden.| 
|maxMessageSizeInbytes |Vereist: false: false: |Geldige waarden: Een positief getal, groter dan 8192, minder dan 262144 |Standaardwaarde: 204800 |Maximaal toegestane grootte van een agent om berichten te vertroebelen. Met deze instelling bepaalt u de hoeveelheid maximale gegevens die in elk bericht worden verzonden. |
|gebeurtenisPrioriteit${EventName} |Vereist: false: false: |Geldige waarden: Hoog, Laag, Uit |Standaardwaarden: |Prioriteit van elke door agenten gegenereerde gebeurtenis | 

### <a name="supported-security-events"></a>Ondersteunde beveiligingsgebeurtenissen

|Gebeurtenisnaam| PropertyName | Standaardwaarde| Momentopnamegebeurtenis| Details Status  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Diagnostische gebeurtenis|gebeurtenisPrioriteitDiagnos| Uit| False| Agent gerelateerde diagnostische gebeurtenissen. Gebruik deze gebeurtenis voor verbose logging.| 
|Configuratiefout |gebeurtenisPriorityConfigurationError |Laag |False |Agent kan de configuratie niet ontzien. Controleer de configuratie op basis van het schema.| 
|Gedaalde gebeurtenissenstatistieken |gebeurtenisPriorityDroppedEventsStatistieken |Laag |True|Agent gerelateerde gebeurtenisstatistieken. |
|Verbonden hardware|gebeurtenisPriorityConnectedHardware |Laag |True |Momentopname van alle hardware die op het apparaat is aangesloten.|
|Luisterpoorten|eventPriorityListeningPorts |Hoog |True |Momentopname van alle geopende luisterpoorten op het apparaat.|
|Procesmaken |eventPriorityProcessCreate |Laag |False |Audits proces creatie op het apparaat.|
|Proces beëindigd|eventPriorityProcessTerminate |Laag |False |Audits proces beëindiging op het apparaat.| 
|Systeeminformatie |eventPrioritySystemInformatie |Laag |True |Een momentopname van systeeminformatie (bijvoorbeeld: OS of CPU).| 
|Lokale gebruikers| eventPriorityLocalUsers |Hoog |True|Een momentopname van de geregistreerde lokale gebruikers binnen het systeem. |
|Aanmelden|  eventPriorityLogin |Hoog|False|Controleer de inloggebeurtenissen op het apparaat (lokale en externe aanmeldingen).|
|Verbinding maken |eventPriorityConnectionCreate|Laag|False|Controleert TCP-verbindingen die van en naar het apparaat zijn gemaakt. |
|Firewallconfiguratie| gebeurtenisPriorityFirewallConfiguratie|Laag|True|Momentopname van de configuratie van de apparaatfirewall (firewallregels). |
|OS-basislijn| gebeurtenisPriorityOSBaseline| Laag|True|Momentopname van de basislijncontrole van apparaatbesturingssysteem.|
|
 

## <a name="next-steps"></a>Volgende stappen

- [Inzicht in Azure Security Center voor IoT-aanbevelingen](concept-recommendations.md)
- [Azure Security Center voor IoT-waarschuwingen verkennen](concept-security-alerts.md)
- [Toegang tot onbewerkte beveiligingsgegevens](how-to-security-data-access.md)
