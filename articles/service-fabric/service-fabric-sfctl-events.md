---
title: Azure Service Fabric CLI- sfctl-gebeurtenissen
description: Beschrijft de gebeurtenissenopdrachten voor de servicestructuur CLI sfctl.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 635bddef1a98d6ed2b112662d8c9c44fe65e9e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906102"
---
# <a name="sfctl-events"></a>sfctl evenementen
Gebeurtenissen ophalen uit de evenementenwinkel (als de EventStore-service al is geïnstalleerd).

De EventStore-systeemservice kan worden toegevoegd via een config-upgrade naar elk SFRP-cluster met >=6.4. Controleer de volgende\: \:url https -docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| lijst met alle toepassingen | Hiermee worden alle toepassingengerelateerde gebeurtenissen opje klaarstaan. |
| lijst met alle knooppunten | Hiermee worden alle knooppuntengerelateerde gebeurtenissen ophaalt. |
| alle partities-lijst | Hiermee worden alle partitiesgerelateerde gebeurtenissen opje verkrijgt. |
| all-services-lijst | Hiermee worden alle services-gerelateerde gebeurtenissen opje klaarstaan. |
| toepassingslijst | Hiermee wordt een toepassingsgerelateerde gebeurtenis op je klaargemaakt. |
| clusterlijst | Hiermee worden alle clustergerelateerde gebeurtenissen ophaalt. |
| knooppuntlijst | Krijgt een node-gerelateerde gebeurtenissen. |
| partitie-all-replica's-lijst | Hiermee worden alle replica's-gerelateerde gebeurtenissen voor een partitie ophaalt. |
| partitielijst | Hiermee wordt een partitiegerelateerde gebeurtenis op de plaats gemaakt. |
| partitiereplica-lijst | Hiermee wordt een partitiereplica-gerelateerde gebeurtenissen opje verkrijgt. |
| servicelijst | Hiermee wordt een servicegerelateerde gebeurtenis op je klaarstaan. |

## <a name="sfctl-events-all-applications-list"></a>sfctl events all-applications-list
Hiermee worden alle toepassingengerelateerde gebeurtenissen opje klaarstaan.

Het antwoord is een lijst met ApplicationEvent-objecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --end-time-utc [Vereist] | De eindtijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Vereist] | De begintijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Dit is een door komma gescheiden tekenreeks die de typen FabricEvents opgeeft die alleen in het antwoord moeten worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Deze param schakelt het ophalen van AnalysisEvents uit als true wordt doorgegeven. |
| --skip-correlatie-lookup | Deze param schakelt het zoeken naar CorrelatedEvents informatie uit als true wordt doorgegeven. anders wordt het veld CorrelationEvents verwerkt en hascorrelatedevents in elke FabricEvent ingevuld. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-events-all-nodes-list"></a>sfctl gebeurtenissen all-nodes-lijst
Hiermee worden alle knooppuntengerelateerde gebeurtenissen ophaalt.

Het antwoord is een lijst met objecten NodeEvent.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --end-time-utc [Vereist] | De eindtijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Vereist] | De begintijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Dit is een door komma gescheiden tekenreeks die de typen FabricEvents opgeeft die alleen in het antwoord moeten worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Deze param schakelt het ophalen van AnalysisEvents uit als true wordt doorgegeven. |
| --skip-correlatie-lookup | Deze param schakelt het zoeken naar CorrelatedEvents informatie uit als true wordt doorgegeven. anders wordt het veld CorrelationEvents verwerkt en hascorrelatedevents in elke FabricEvent ingevuld. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-events-all-partitions-list"></a>sfctl events all-partitions-list
Hiermee worden alle partitiesgerelateerde gebeurtenissen opje verkrijgt.

Het antwoord is een lijst met partitionevent-objecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --end-time-utc [Vereist] | De eindtijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Vereist] | De begintijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Dit is een door komma gescheiden tekenreeks die de typen FabricEvents opgeeft die alleen in het antwoord moeten worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Deze param schakelt het ophalen van AnalysisEvents uit als true wordt doorgegeven. |
| --skip-correlatie-lookup | Deze param schakelt het zoeken naar CorrelatedEvents informatie uit als true wordt doorgegeven. anders wordt het veld CorrelationEvents verwerkt en hascorrelatedevents in elke FabricEvent ingevuld. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-events-all-services-list"></a>sfctl evenementen all-services-lijst
Hiermee worden alle services-gerelateerde gebeurtenissen opje klaarstaan.

Het antwoord is een lijst met ServiceEvent-objecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --end-time-utc [Vereist] | De eindtijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Vereist] | De begintijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Dit is een door komma gescheiden tekenreeks die de typen FabricEvents opgeeft die alleen in het antwoord moeten worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Deze param schakelt het ophalen van AnalysisEvents uit als true wordt doorgegeven. |
| --skip-correlatie-lookup | Deze param schakelt het zoeken naar CorrelatedEvents informatie uit als true wordt doorgegeven. anders wordt het veld CorrelationEvents verwerkt en hascorrelatedevents in elke FabricEvent ingevuld. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-events-application-list"></a>sfctl evenementen applicatielijst
Hiermee wordt een toepassingsgerelateerde gebeurtenis op je klaargemaakt.

Het antwoord is een lijst met ApplicationEvent-objecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --end-time-utc [Vereist] | De eindtijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Vereist] | De begintijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Dit is een door komma gescheiden tekenreeks die de typen FabricEvents opgeeft die alleen in het antwoord moeten worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Deze param schakelt het ophalen van AnalysisEvents uit als true wordt doorgegeven. |
| --skip-correlatie-lookup | Deze param schakelt het zoeken naar CorrelatedEvents informatie uit als true wordt doorgegeven. anders wordt het veld CorrelationEvents verwerkt en hascorrelatedevents in elke FabricEvent ingevuld. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-events-cluster-list"></a>clusterlijst sfctl-gebeurtenissen
Hiermee worden alle clustergerelateerde gebeurtenissen ophaalt.

Het antwoord is een lijst met clustereventobjecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --end-time-utc [Vereist] | De eindtijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Vereist] | De begintijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Dit is een door komma gescheiden tekenreeks die de typen FabricEvents opgeeft die alleen in het antwoord moeten worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Deze param schakelt het ophalen van AnalysisEvents uit als true wordt doorgegeven. |
| --skip-correlatie-lookup | Deze param schakelt het zoeken naar CorrelatedEvents informatie uit als true wordt doorgegeven. anders wordt het veld CorrelationEvents verwerkt en hascorrelatedevents in elke FabricEvent ingevuld. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-events-node-list"></a>sfctl evenementen node-lijst
Krijgt een node-gerelateerde gebeurtenissen.

Het antwoord is een lijst met objecten NodeEvent.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --end-time-utc [Vereist] | De eindtijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --node-name [Vereist] | De naam van het knooppunt. |
| --start-time-utc [Vereist] | De begintijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Dit is een door komma gescheiden tekenreeks die de typen FabricEvents opgeeft die alleen in het antwoord moeten worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Deze param schakelt het ophalen van AnalysisEvents uit als true wordt doorgegeven. |
| --skip-correlatie-lookup | Deze param schakelt het zoeken naar CorrelatedEvents informatie uit als true wordt doorgegeven. anders wordt het veld CorrelationEvents verwerkt en hascorrelatedevents in elke FabricEvent ingevuld. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl-gebeurtenissen partitie-all-replica's-lijst
Hiermee worden alle replica's-gerelateerde gebeurtenissen voor een partitie ophaalt.

Het antwoord is een lijst met replicagebeurtenisobjecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --end-time-utc [Vereist] | De eindtijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --start-time-utc [Vereist] | De begintijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Dit is een door komma gescheiden tekenreeks die de typen FabricEvents opgeeft die alleen in het antwoord moeten worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Deze param schakelt het ophalen van AnalysisEvents uit als true wordt doorgegeven. |
| --skip-correlatie-lookup | Deze param schakelt het zoeken naar CorrelatedEvents informatie uit als true wordt doorgegeven. anders wordt het veld CorrelationEvents verwerkt en hascorrelatedevents in elke FabricEvent ingevuld. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-events-partition-list"></a>sfctl-verdelingslijst voor gebeurtenissen
Hiermee wordt een partitiegerelateerde gebeurtenis op de plaats gemaakt.

Het antwoord is een lijst met partitionevent-objecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --end-time-utc [Vereist] | De eindtijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --start-time-utc [Vereist] | De begintijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Dit is een door komma gescheiden tekenreeks die de typen FabricEvents opgeeft die alleen in het antwoord moeten worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Deze param schakelt het ophalen van AnalysisEvents uit als true wordt doorgegeven. |
| --skip-correlatie-lookup | Deze param schakelt het zoeken naar CorrelatedEvents informatie uit als true wordt doorgegeven. anders wordt het veld CorrelationEvents verwerkt en hascorrelatedevents in elke FabricEvent ingevuld. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-events-partition-replica-list"></a>sfctl-gebeurtenissen partitie-replica-lijst
Hiermee wordt een partitiereplica-gerelateerde gebeurtenissen opje verkrijgt.

Het antwoord is een lijst met replicagebeurtenisobjecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --end-time-utc [Vereist] | De eindtijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --replica-id [Vereist] | De id van de replica. |
| --start-time-utc [Vereist] | De begintijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Dit is een door komma gescheiden tekenreeks die de typen FabricEvents opgeeft die alleen in het antwoord moeten worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Deze param schakelt het ophalen van AnalysisEvents uit als true wordt doorgegeven. |
| --skip-correlatie-lookup | Deze param schakelt het zoeken naar CorrelatedEvents informatie uit als true wordt doorgegeven. anders wordt het veld CorrelationEvents verwerkt en hascorrelatedevents in elke FabricEvent ingevuld. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-events-service-list"></a>sfctl evenementen service-lijst
Hiermee wordt een servicegerelateerde gebeurtenis op je klaarstaan.

Het antwoord is een lijst met ServiceEvent-objecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --end-time-utc [Vereist] | De eindtijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --service-id [Vereist] | De identiteit van de dienst. Deze ID is meestal de volledige naam van\:de dienst zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de servicenaam bijvoorbeeld "fabric\:/myapp/app1/svc1" is, is\~de\~service-identiteit "myapp app1 svc1" in 6.0+ en "myapp/app1/svc1" in eerdere versies. |
| --start-time-utc [Vereist] | De begintijd van een opzoekquery in ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Dit is een door komma gescheiden tekenreeks die de typen FabricEvents opgeeft die alleen in het antwoord moeten worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Deze param schakelt het ophalen van AnalysisEvents uit als true wordt doorgegeven. |
| --skip-correlatie-lookup | Deze param schakelt het zoeken naar CorrelatedEvents informatie uit als true wordt doorgegeven. anders wordt het veld CorrelationEvents verwerkt en hascorrelatedevents in elke FabricEvent ingevuld. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

