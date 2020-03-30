---
title: Azure Service Fabric CLI- sfctl sa-cluster
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten voor het beheren van zelfstandige clusters.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 233148c04fb1a9c6cf1d6c7042c12c54eebd0205
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904918"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Beheer zelfstandige Service Fabric-clusters.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| configuratie | Download de zelfstandige clusterconfiguratie van Service Fabric. |
| config-upgrade | Begin met het upgraden van de configuratie van een zelfstandig cluster van ServiceFabric. |
| upgradestatus | De upgradestatus van een zelfstandige cluster voor clusterconfiguratie op. |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa-cluster config
Download de zelfstandige clusterconfiguratie van Service Fabric.

De clusterconfiguratie bevat eigenschappen van het cluster die verschillende knooppunttypen op het cluster bevatten, beveiligingsconfiguraties, fout- en upgradedomeintopologieën, enz.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --configuration-api-version [Vereist] | De API-versie van de zelfstandige clusterjson-configuratie. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster config-upgrade
Begin met het upgraden van de configuratie van een zelfstandig cluster van ServiceFabric.

Valideer de opgegeven configuratie-upgradeparameters en begin met het upgraden van de clusterconfiguratie als de parameters geldig zijn.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --cluster-config [Vereist] | De clusterconfiguratie. |
| --application-health-policies | JSON gecodeerd woordenboek van paren van de naam van het toepassingstype en het maximumpercentage ongezond alvorens fout op te halen. |
| --delta-ongezonde knooppunten | Het maximaal toegestane percentage van delta gezondheid degradatie tijdens de upgrade. Toegestane waarden zijn gehele waarden van nul tot 100. |
| --health-check-retry | De tijdsduur tussen pogingen om statuscontroles uit te voeren als de toepassing of het cluster niet in orde is.  Standaard\: PT0H0M0S. |
| --health-check-stable | De hoeveelheid tijd dat de toepassing of het cluster gezond moet blijven voordat de upgrade doorgaat naar het volgende upgradedomein.  Standaard\: PT0H0M0S. <br><br> Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. |
| --health-check-wait | De tijdsduur om te wachten na het voltooien van een upgradedomein voordat u het proces voor statuscontroles start.  Standaard\: PT0H0M0S. |
| --time-out -t | Standaard\: 60. |
| --ongezonde toepassingen | Het maximaal toegestane percentage ongezonde toepassingen tijdens de upgrade. Toegestane waarden zijn gehele waarden van nul tot 100. |
| --ongezonde knooppunten | Het maximaal toegestane percentage ongezonde knooppunten tijdens de upgrade. Toegestane waarden zijn gehele waarden van nul tot 100. |
| --upgrade-domein-delta-ongezonde knooppunten | Het maximaal toegestane percentage van upgrade domein delta gezondheid degradatie tijdens de upgrade. Toegestane waarden zijn gehele waarden van nul tot 100. |
| --upgrade-domein-time-out | De hoeveelheid tijd die elk upgradedomein moet voltooien voordat FailureAction wordt uitgevoerd.  Standaard\: PT0H0M0S. <br><br> Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. |
| --upgrade-time-out | De hoeveelheid tijd die de algehele upgrade moet voltooien voordat FailureAction wordt uitgevoerd.  Standaard\: PT0H0M0S. <br><br> Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

### <a name="examples"></a>Voorbeelden

Een clusterconfiguratie-update starten
```
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-cluster upgrade-status
De upgradestatus van een zelfstandige cluster voor clusterconfiguratie op.

Download de statusstatusgegevens van een zelfstandige cluster van de clusterconfiguratievan een servicestructuur.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |


## <a name="next-steps"></a>Volgende stappen
- Stel de SERVICE Fabric CLI [in.](service-fabric-cli.md)
- Meer informatie over het gebruik van de CLI van de ServiceFabric met behulp van de [voorbeeldscripts](/azure/service-fabric/scripts/sfctl-upgrade-application).