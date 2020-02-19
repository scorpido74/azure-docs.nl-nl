---
title: Azure Security Center configureren voor IoT-agent | Microsoft Docs
description: Meer informatie over het configureren van Azure Security Center voor IoT-beveiligings agenten voor gebruik met de Azure Security Center voor IoT-beveiligings service.
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
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461246"
---
# <a name="tutorial-configure-security-agents"></a>Zelf studie: beveiligings agenten configureren

In dit artikel worden Azure Security Center voor IoT-beveiligings agenten beschreven en wordt uitgelegd hoe u deze kunt wijzigen en configureren. 

> [!div class="checklist"]
> * Beveiligingsagents configureren
> * Gedrag van agent wijzigen door dubbele eigenschappen te bewerken
> * Standaard configuratie detecteren

## <a name="agents"></a>Agents

Azure Security Center voor IoT-beveiligings agenten gegevens verzamelen uit IoT-apparaten en beveiligings acties uitvoeren om de gedetecteerde beveiligings problemen te verhelpen. De configuratie van de beveiligings agent kan worden ingesteld met behulp van een set van module-dubbele eigenschappen die u kunt aanpassen. In het algemeen zijn secundaire updates van deze eigenschappen niet vaak.  

Azure Security Center voor een configuratie object van een IoT-beveiligings agent is een JSON-indelings object. Het configuratie object is een verzameling eigenschappen die u kunt definiëren om het gedrag van de agent te bepalen. 

Deze configuraties helpen u bij het aanpassen van de agent voor elk vereist scenario. Als u bijvoorbeeld een aantal gebeurtenissen uitsluit of het energie verbruik tot een minimum niveau wilt houden, kunt u deze eigenschappen configureren.  

Gebruik het Azure Security Center voor IoT-beveiligings agent configuratie [schema](https://aka.ms/iot-security-github-module-schema) om wijzigingen aan te brengen.  

## <a name="configuration-objects"></a>Configuratie objecten 

Eigenschappen die betrekking hebben op elke Azure Security Center voor IoT-beveiligings agent bevinden zich in het configuratie object voor de agent, binnen de gewenste eigenschappen sectie van de module **azureiotsecurity** . 

Als u de configuratie wilt wijzigen, maakt en wijzigt u dit object in de **azureiotsecurity** -module dubbele identiteit. 

Als het configuratie object van de agent niet bestaat in de **azureiotsecurity** -module dubbele, worden alle waarden van de eigenschappen van de beveiligings agent ingesteld op standaard. 

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  } 
}
```

## <a name="configuration-schema-and-validation"></a>Configuratie schema en validatie 

Zorg ervoor dat u de agent configuratie op basis van dit [schema](https://aka.ms/iot-security-github-module-schema)valideert. Een agent wordt niet gestart als het configuratie object niet overeenkomt met het schema.

 
Als, terwijl de agent wordt uitgevoerd, het configuratie object wordt gewijzigd in een niet-geldige configuratie (de configuratie komt niet overeen met het schema), wordt de ongeldige configuratie door de agent genegeerd en blijft de huidige configuratie worden gebruikt. 

### <a name="configuration-validation"></a>Configuratie validatie

Azure Security Center voor IoT-beveiligings agent rapporteert de huidige configuratie in de sectie gerapporteerde eigenschappen van de **azureiotsecurity** -module dubbele identiteit.
De agent rapporteert alle beschik bare eigenschappen, als er geen eigenschap is ingesteld door de gebruiker, de agent rapporteert de standaard configuratie.

Als u de configuratie wilt valideren, vergelijkt u de waarden die zijn ingesteld voor de gewenste sectie met de waarden die zijn gerapporteerd in de gerapporteerde sectie.

Als de gewenste en de gerapporteerde eigenschappen niet overeenkomen, kan de configuratie niet door de agent worden geparseerd.

Valideer de gewenste eigenschappen voor het [schema](https://aka.ms/iot-security-github-module-schema), Los de fouten op en stel de gewenste eigenschappen opnieuw in.

> [!NOTE]
> Er wordt een configuratie fout waarschuwing van de agent geactiveerd wanneer de agent de gewenste configuratie niet kan parseren.
> De gerapporteerde en gewenste sectie vergelijken om te begrijpen of de waarschuwing nog steeds van toepassing is

## <a name="editing-a-property"></a>Een eigenschap bewerken 

Alle aangepaste eigenschappen moeten worden ingesteld binnen het configuratie object van de agent in de **azureiotsecurity** -module.
Als u een standaard waarde voor een eigenschap wilt gebruiken, verwijdert u de eigenschap van het configuratie object.

### <a name="setting-a-property"></a>Een eigenschap instellen

1. Zoek en selecteer het apparaat dat u wilt wijzigen in de IoT Hub.

1. Klik op uw apparaat en vervolgens op de module **azureiotsecurity** .

1. Klik op **module-identiteit, twee**.

1. Bewerk de eigenschappen die u wilt wijzigen in de beveiligings module.
   
   Gebruik bijvoorbeeld de volgende configuratie om verbindings gebeurtenissen te configureren als hoge prioriteit en elke 7 minuten gebeurtenissen met een hoge prioriteit te verzamelen.
   
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

### <a name="using-a-default-value"></a>Een standaard waarde gebruiken

Als u een standaard waarde voor een eigenschap wilt gebruiken, verwijdert u de eigenschap van het configuratie object.

## <a name="default-properties"></a>Standaard eigenschappen 

De volgende tabel bevat de eigenschappen van Azure Security Center voor IoT-beveiligings agenten.

Standaard waarden zijn beschikbaar in het juiste schema in [github](https\://aka.ms/iot-security-module-default).

| Name| Status | Geldige waarden| Standaardwaarden| Beschrijving |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Vereist: onwaar |Geldige waarden: duration in ISO 8601-indeling |Standaard waarde: PT7M |Het maximale tijds interval voor berichten met een hoge prioriteit worden verzonden.|
|lowPriorityMessageFrequency |Vereist: onwaar|Geldige waarden: duration in ISO 8601-indeling |Standaard waarde: PT5H |Maximale tijd waarna berichten met een lage prioriteit worden verzonden.| 
|snapshotFrequency |Vereist: False|Geldige waarden: duration in ISO 8601-indeling |Standaard waarde PT13H |Tijds interval voor het maken van moment opnamen van de Apparaatstatus.| 
|maxLocalCacheSizeInBytes |Vereist: onwaar |Geldige waarden: |Standaard waarde: 2560000, groter dan 8192 | Maximale opslag (in bytes) die is toegestaan voor de berichten cache van een agent. Maximale hoeveelheid ruimte die is toegestaan voor het opslaan van berichten op het apparaat voordat berichten worden verzonden.| 
|maxMessageSizeInBytes |Vereist: onwaar |Geldige waarden: een positief getal groter dan 8192, kleiner dan 262144 |Standaard waarde: 204800 |Maxi maal toegestane grootte van een agent naar een Cloud bericht. Met deze instelling bepaalt u de hoeveelheid gegevens die in elk bericht wordt verzonden. |
|eventPriority${EventName} |Vereist: onwaar |Geldige waarden: hoog, laag, uit |Standaard waarden: |Prioriteit van elke door een agent gegenereerde gebeurtenis | 

### <a name="supported-security-events"></a>Ondersteunde beveiligings gebeurtenissen

|Gebeurtenisnaam| propertyName | Standaardwaarde| Momentopname gebeurtenis| Status Details  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Diagnostische gebeurtenis|eventPriorityDiagnostic| Uit| False| Aan een agent gerelateerde diagnostische gebeurtenissen. Gebruik deze gebeurtenis voor uitgebreide logboek registratie.| 
|Configuratie fout |eventPriorityConfigurationError |Laag |False |Agent kan de configuratie niet parseren. Controleer de configuratie op basis van het schema.| 
|Statistieken voor verwijderde gebeurtenissen |eventPriorityDroppedEventsStatistics |Laag |True|Gerelateerde gebeurtenis statistieken voor de agent. |
|Verbonden hardware|eventPriorityConnectedHardware |Laag |True |Moment opname van alle hardware die is aangesloten op het apparaat.|
|Luisterende poorten|eventPriorityListeningPorts |Hoog |True |Moment opname van alle open luisterende poorten op het apparaat.|
|Proces maken |eventPriorityProcessCreate |Laag |False |Het proces voor het maken van controles op het apparaat.|
|Proces beëindigen|eventPriorityProcessTerminate |Laag |False |Controleert het proces op het apparaat.| 
|Systeem gegevens |eventPrioritySystemInformation |Laag |True |Een moment opname van systeem informatie (bijvoorbeeld: besturings systeem of CPU).| 
|Lokale gebruikers| eventPriorityLocalUsers |Hoog |True|Een moment opname van de geregistreerde lokale gebruikers in het systeem. |
|Aanmelden|  eventPriorityLogin |Hoog|False|Controleer de aanmeldings gebeurtenissen op het apparaat (lokale en externe aanmeldingen).|
|Verbinding maken |eventPriorityConnectionCreate|Laag|False|Controleert TCP-verbindingen die zijn gemaakt met en van het apparaat. |
|Firewall configuratie| eventPriorityFirewallConfiguration|Laag|True|Moment opname van de firewall configuratie van het apparaat (firewall regels). |
|Basis lijn van besturings systeem| eventPriorityOSBaseline| Laag|True|Moment opname van de basis controle van het besturings systeem van het apparaat.|
|
 

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over Azure Security Center voor IoT-aanbevelingen](concept-recommendations.md)
- [Azure Security Center verkennen voor IoT-waarschuwingen](concept-security-alerts.md)
- [Toegang tot onbewerkte beveiligings gegevens](how-to-security-data-access.md)
