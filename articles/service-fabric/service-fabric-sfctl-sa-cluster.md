---
title: Azure Service Fabric CLI-sfctl sa-cluster | Microsoft Docs
description: Hierin worden de zelfstandige sfctl-opdrachten voor de Service Fabric CLI-cluster beschreven.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: ecdd288d7cb320b91ab4c69697d334f8d9459e62
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901019"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Zelfstandige Service Fabric-clusters beheren.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| configuraties | Haal de configuratie van de zelfstandige Cluster Service Fabric. |
| configuratie-upgrade | Het bijwerken van de configuratie van een zelfstandige Service Fabric-cluster starten. |
| upgrade-status | De upgrade status van de cluster configuratie ophalen van een zelfstandige Service Fabric-cluster. |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa-cluster configuratie
Haal de configuratie van de zelfstandige Cluster Service Fabric.

De cluster configuratie bevat eigenschappen van het cluster die verschillende knooppunt typen bevatten op het cluster, de beveiligings configuraties, de fout en de topologieën van het upgrade domein, enzovoort.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --configuratie-API-versie [vereist] | De API-versie van de JSON-configuratie van de zelfstandige cluster. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --fouten opsporen | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster configuratie-upgrade
Het bijwerken van de configuratie van een zelfstandige Service Fabric-cluster starten.

Valideer de opgegeven configuratie-upgrade parameters en begin met het upgraden van de cluster configuratie als de para meters geldig zijn.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --cluster-config [vereist] | De cluster configuratie. |
| --toepassings status-beleids regels | JSON-gecodeerde woorden lijst met paren van de naam van het toepassings type en het maximum percentage is slecht voordat er een fout optreedt. |
| --Delta-onjuiste knoop punten | Het Maxi maal toegestane percentage van de Delta status vermindering tijdens de upgrade. Toegestane waarden zijn gehele waarden van nul tot en met 100. |
| --status-controle-opnieuw | De tijds duur tussen pogingen om status controles uit te voeren als de toepassing of het cluster niet in orde is.  Standaard\: PT0H0M0S. |
| --status-controle stabiel | De hoeveelheid tijd die de toepassing of het cluster in orde moet blijven voordat de upgrade wordt voortgezet naar het volgende upgrade domein.  Standaard\: PT0H0M0S. <br><br> Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. |
| --status-check-wait | De tijds duur die moet worden gewacht na het volt ooien van een upgrade domein voordat het proces status controles wordt gestart.  Standaard\: PT0H0M0S. |
| --time-out-t | Standaard\: 60. |
| --toepassingen met een slechte status | Het Maxi maal toegestane percentage van beschadigde toepassingen tijdens de upgrade. Toegestane waarden zijn gehele waarden van nul tot en met 100. |
| --onjuiste knoop punten | Het Maxi maal toegestane percentage van beschadigde knoop punten tijdens de upgrade. Toegestane waarden zijn gehele waarden van nul tot en met 100. |
| --upgrade-Domain-Delta-slechte statussen: knoop punten | Het Maxi maal toegestane percentage van de upgrade van de Delta status van het domein tijdens de upgrade. Toegestane waarden zijn gehele waarden van nul tot en met 100. |
| --upgrade-time-out van domein | De hoeveelheid tijd die elk upgrade domein moet volt ooien voordat FailureAction wordt uitgevoerd.  Standaard\: PT0H0M0S. <br><br> Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. |
| --upgrade-time-out | De hoeveelheid tijd die de algehele upgrade moet volt ooien voordat FailureAction wordt uitgevoerd.  Standaard\: PT0H0M0S. <br><br> Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --fouten opsporen | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

### <a name="examples"></a>Voorbeelden

Een update voor een cluster configuratie starten
``` 
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-    
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"   
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-cluster upgrade-status
De upgrade status van de cluster configuratie ophalen van een zelfstandige Service Fabric-cluster.

De details van de upgrade status van de cluster configuratie van een zelfstandige Service Fabric-cluster ophalen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --fouten opsporen | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |


## <a name="next-steps"></a>Volgende stappen
- [Stel](service-fabric-cli.md) de service Fabric cli in.
- Meer informatie over het gebruik van de Service Fabric CLI met behulp van de [voorbeeld scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).