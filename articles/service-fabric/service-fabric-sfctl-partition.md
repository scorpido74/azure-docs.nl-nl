---
title: Azure Service Fabric CLI- sfctl-partitie
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten voor het beheren van partities voor een service.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: c038ef3266a727bf6984a5bd88ca540a589380db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905851"
---
# <a name="sfctl-partition"></a>sfctl partition
Partities voor elke service opvragen en beheren.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| gegevensverlies | Deze API zal leiden tot gegevensverlies voor de opgegeven partitie. |
| status van gegevensverlies | Hiermee wordt de voortgang van een bewerking voor het verlies van partitiegegevens gestart met de StartDataLoss API. |
| Gezondheid | Krijgt de status van de opgegeven Service Fabric-partitie. |
| Info | Krijgt de informatie over een Service Fabric-partitie. |
| list | Hier wordt de lijst met partities van een Service Fabric-service weergegeven. |
| laden | Krijgt de belastingsinformatie van de opgegeven Service Fabric-partitie. |
| load-reset | Hiermee wordt de huidige belasting van een Service Fabric-partitie opnieuw ingesteld. |
| quorumverlies | Leidt tot quorumverlies voor een bepaalde stateful servicepartitie. |
| quorumverlies-status | Hiermee wordt de voortgang van een quorumverliesbewerking op een partitie gestart met de API StartQuorumLoss. |
| herstellen | Geeft aan het cluster Servicefabric aan dat het moet proberen een specifieke partitie te herstellen die momenteel vastzit in quorumverlies. |
| herstellen-all | Geeft aan het cluster Servicefabric aan dat het moet proberen alle services (inclusief systeemservices) te herstellen die momenteel vastzitten in quorumverlies. |
| rapport-gezondheid | Hiermee verzendt u een statusrapport op de partitie Servicefabric. |
| restart | Deze API start sommige of alle replica's of exemplaren van de opgegeven partitie opnieuw. |
| herstartstatus | Hiermee wordt de voortgang van een partitiebewerkinggestart gestart met StartPartitionRestart. |
| svc-naam | Krijgt de naam van de Service Fabric-service voor een partitie. |

## <a name="sfctl-partition-data-loss"></a>sfctl partitie verlies
Deze API zal leiden tot gegevensverlies voor de opgegeven partitie.

Het zal leiden tot een aanroep naar de OnDataLossAsync API van de partitie.  Deze API zal leiden tot gegevensverlies voor de opgegeven partitie. Het zal leiden tot een aanroep naar de OnDataLoss API van de partitie. Werkelijke gegevensverlies is afhankelijk van de opgegeven DataLossMode.
- PartialDataLoss: Alleen een quorum van replica's wordt verwijderd en OnDataLoss wordt geactiveerd voor de partitie, maar het werkelijke gegevensverlies is afhankelijk van de aanwezigheid van replicatie tijdens de vlucht.  
- FullDataLoss: Alle replica's worden verwijderd, dus alle gegevens gaan verloren en OnDataLoss wordt geactiveerd. Deze API mag alleen worden aangeroepen met een stateful service als doel. Het aanroepen van deze API met een systeemservice als doel wordt afgeraden.

> [!NOTE]   
> Zodra deze API is aangeroepen, kan deze niet meer worden teruggedraaid. Als u CancelOperation aanroept, wordt de uitvoering alleen gestopt en wordt de interne systeemstatus opgeschoond. Het herstelt geen gegevens als de opdracht ver genoeg is gevorderd om gegevensverlies te veroorzaken. Bel de GetDataLossProgress API met dezelfde OperationId om informatie over de bewerking die met deze API is gestart, terug te sturen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --data-loss-modus [Vereist] | Dit enum wordt doorgegeven aan de StartDataLoss API om aan te geven welk type gegevensverlies moet worden veroorzaakt. |
| --operation-id [Vereist] | Een GUID die een aanroep van deze API identificeert.  Dit wordt doorgegeven aan de bijbehorende GetProgress API. |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --service-id [Vereist] | De identiteit van de dienst. Deze ID is meestal de volledige naam van\:de dienst zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de servicenaam bijvoorbeeld "fabric\:/myapp/app1/svc1" is, is\~de\~service-identiteit "myapp app1 svc1" in 6.0+ en "myapp/app1/svc1" in eerdere versies. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl partitie data-loss-status
Hiermee wordt de voortgang van een bewerking voor het verlies van partitiegegevens gestart met de StartDataLoss API.

Hiermee wordt de voortgang van een gegevensverliesbewerking gestart met StartDataLoss met behulp van de OperationId.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --operation-id [Vereist] | Een GUID die een aanroep van deze API identificeert.  Dit wordt doorgegeven aan de bijbehorende GetProgress API. |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --service-id [Vereist] | De identiteit van de dienst. Deze ID is meestal de volledige naam van\:de dienst zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de servicenaam bijvoorbeeld "fabric\:/myapp/app1/svc1" is, is\~de\~service-identiteit "myapp app1 svc1" in 6.0+ en "myapp/app1/svc1" in eerdere versies. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-partition-health"></a>sfctl partitie status
Krijgt de status van de opgegeven Service Fabric-partitie.

Gebruik EventsHealthStateFilter om het verzamelen van statusgebeurtenissen die op de service zijn gerapporteerd op basis van de status te filteren. Gebruik Replica'sHealthStateFilter om de verzameling replicahealthstate-objecten op de partitie te filteren. Als u een partitie opgeeft die niet in het statusarchief bestaat, wordt in deze aanvraag een fout geretourneerd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --events-health-state-filter | Hiermee u de verzameling HealthEvent-objecten filteren die zijn geretourneerd op basis van de status. De mogelijke waarden voor deze parameter omvatten de gehele waarde van een van de volgende statussen. Alleen gebeurtenissen die overeenkomen met het filter worden geretourneerd. Alle gebeurtenissen worden gebruikt om de geaggregeerde status te evalueren. Als dit niet is opgegeven, worden alle items geretourneerd. De statuswaarden zijn op vlag gebaseerde opsomming, dus de waarde kan een combinatie zijn van deze waarden, verkregen met behulp van de bitwise 'OR'-operator. Als de opgegeven waarde bijvoorbeeld 6 is, worden alle gebeurtenissen met de statuswaarde Van OK (2) en Waarschuwing (4) geretourneerd.  <br> - Standaard - Standaardwaarde. Komt overeen met elke HealthState. De waarde is nul.  <br> - Geen - Filter dat niet overeenkomt met een HealthState-waarde. Gebruikt om geen resultaten op een bepaalde verzameling van staten terug te keren. De waarde is 1.  <br> - Ok - Filter dat overeenkomt met invoer met HealthState-waarde Ok. De waarde is 2.  <br> - Waarschuwing - Filter dat overeenkomt met invoer met Waarschuwing voor de waarde HealthState. De waarde is 4.  <br> - Fout - Filter dat overeenkomt met invoer met Statuswaardefout. De waarde is 8.  <br> - Alles - Filter dat overeenkomt met de invoer met een healthstate-waarde. De waarde is 65535. |
| --exclude-health-statistics | Geeft aan of de statusstatistieken moeten worden geretourneerd als onderdeel van het queryresultaat. Standaard onwaar. De statistieken tonen het aantal kinderen entiteiten in de gezondheidstoestand Ok, Waarschuwing en Fout. |
| --replica's-health-state-filter | Hiermee u de verzameling ReplicaHealthState-objecten op de partitie filteren. De waarde kan worden verkregen van leden of bitwise operaties op leden van HealthStateFilter. Alleen replica's die overeenkomen met het filter worden geretourneerd. Alle replica's worden gebruikt om de geaggregeerde status te evalueren. Als dit niet is opgegeven, worden alle inzendingen geretourneerd. De statuswaarden zijn op vlag gebaseerde opsomming, dus de waarde kan een combinatie zijn van deze waarden die zijn verkregen met bitwise 'OR'-operator. Als de opgegeven waarde bijvoorbeeld 6 is, worden alle gebeurtenissen met de statuswaarde Van OK (2) en Waarschuwing (4) geretourneerd. De mogelijke waarden voor deze parameter omvatten de gehele waarde van een van de volgende statussen.  <br> - Standaard - Standaardwaarde. Komt overeen met elke HealthState. De waarde is nul.  <br> - Geen - Filter dat niet overeenkomt met een HealthState-waarde. Gebruikt om geen resultaten op een bepaalde verzameling van staten terug te keren. De waarde is 1.  <br> - Ok - Filter dat overeenkomt met invoer met HealthState-waarde Ok. De waarde is 2.  <br> - Waarschuwing - Filter dat overeenkomt met invoer met Waarschuwing voor de waarde HealthState. De waarde is 4.  <br> - Fout - Filter dat overeenkomt met invoer met Statuswaardefout. De waarde is 8.  <br> - Alles - Filter dat overeenkomt met de invoer met een healthstate-waarde. De waarde is 65535. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-partition-info"></a>sfctl partitie info
Krijgt de informatie over een Service Fabric-partitie.

Hier wordt de informatie over de opgegeven partitie opgevraagd. Het antwoord omvat de partitie-id, partitieschema-informatie, sleutels die worden ondersteund door de partitie, status, status en andere details over de partitie.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-partition-list"></a>sfctl-partitielijst
Hier wordt de lijst met partities van een Service Fabric-service weergegeven.

Het antwoord omvat de partitie-id, partitieschema-informatie, sleutels die worden ondersteund door de partitie, status, status en andere details over de partitie.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --service-id [Vereist] | De identiteit van de dienst. Deze ID is meestal de volledige naam van\:de dienst zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de servicenaam bijvoorbeeld "fabric\:/myapp/app1/svc1" is, is\~de\~service-identiteit "myapp app1 svc1" in 6.0+ en "myapp/app1/svc1" in eerdere versies. |
| --continuation-token | De parameter vervolgtoken wordt gebruikt om volgende set resultaten te verkrijgen. Een vervolgtoken met een niet-lege waarde wordt opgenomen in de reactie van de API wanneer de resultaten van het systeem niet in één reactie passen. Wanneer deze waarde wordt doorgegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolgtoken geen waarde. De waarde van deze parameter mag niet worden gecodeerd met url's. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-partition-load"></a>sfctl partitiebelasting
Krijgt de belastingsinformatie van de opgegeven Service Fabric-partitie.

Retourneert informatie over de belasting van een opgegeven partitie. Het antwoord bevat een lijst met belastingsrapporten voor een Service Fabric-partitie. Elk rapport bevat de naam, waarde en de laatst gerapporteerde tijd in UTC.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-partition-load-reset"></a>sfctl-partitie load-reset
Hiermee wordt de huidige belasting van een Service Fabric-partitie opnieuw ingesteld.

Hiermee wordt de huidige belasting van een Service Fabric-partitie gereset naar de standaardbelasting voor de service.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-partition-quorum-loss"></a>sfctl partitie quorum-verlies
Leidt tot quorumverlies voor een bepaalde stateful servicepartitie.

Deze API is handig voor een tijdelijke situatie van quorumverlies op uw service. Roep de GetQuorumLossProgress API met dezelfde OperationId aan om informatie over de bewerking die met deze API is gestart, terug te sturen. Dit kan alleen worden aangeroepen op stateful persisted (HasPersistedState==true) diensten.  Gebruik deze API niet op stateless services of stateful in-memory only services.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --operation-id [Vereist] | Een GUID die een aanroep van deze API identificeert.  Dit wordt doorgegeven aan de bijbehorende GetProgress API. |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --quorum-verlies-duur [Vereist] | De hoeveelheid tijd waarvoor de partitie in quorumverlies wordt bewaard.  Dit moet in enkele seconden worden opgegeven. |
| --quorum-loss-modus [Vereist] | Dit enum wordt doorgegeven aan de StartQuorumLoss API om aan te geven welk type quorumverlies moet worden veroorzaakt. |
| --service-id [Vereist] | De identiteit van de dienst. Deze ID is meestal de volledige naam van\:de dienst zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de servicenaam bijvoorbeeld "fabric\:/myapp/app1/svc1" is, is\~de\~service-identiteit "myapp app1 svc1" in 6.0+ en "myapp/app1/svc1" in eerdere versies. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl partitie quorum-verlies-status
Hiermee wordt de voortgang van een quorumverliesbewerking op een partitie gestart met de API StartQuorumLoss.

Hiermee wordt de voortgang van een quorumverliesbewerking gestart met StartQuorumLoss met behulp van de meegeleverde OperationId.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --operation-id [Vereist] | Een GUID die een aanroep van deze API identificeert.  Dit wordt doorgegeven aan de bijbehorende GetProgress API. |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --service-id [Vereist] | De identiteit van de dienst. Deze ID is meestal de volledige naam van\:de dienst zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de servicenaam bijvoorbeeld "fabric\:/myapp/app1/svc1" is, is\~de\~service-identiteit "myapp app1 svc1" in 6.0+ en "myapp/app1/svc1" in eerdere versies. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-partition-recover"></a>sfctl-partitie herstellen
Geeft aan het cluster Servicefabric aan dat het moet proberen een specifieke partitie te herstellen die momenteel vastzit in quorumverlies.

Deze bewerking mag alleen worden uitgevoerd als bekend is dat de replica's die zijn uitgeschakeld niet kunnen worden hersteld. Onjuist gebruik van deze API kan leiden tot mogelijk gegevensverlies.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-partition-recover-all"></a>sfctl partitie recover-all
Geeft aan het cluster Servicefabric aan dat het moet proberen alle services (inclusief systeemservices) te herstellen die momenteel vastzitten in quorumverlies.

Deze bewerking mag alleen worden uitgevoerd als bekend is dat de replica's die zijn uitgeschakeld niet kunnen worden hersteld. Onjuist gebruik van deze API kan leiden tot mogelijk gegevensverlies.

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

## <a name="sfctl-partition-report-health"></a>sfctl partitie rapport-health
Hiermee verzendt u een statusrapport op de partitie Servicefabric.

Rapporteert de status van de opgegeven servicestructuurpartitie. Het rapport moet de informatie bevatten over de bron van het gezondheidsrapport en de eigenschap waarop het wordt gerapporteerd. Het rapport wordt verzonden naar een Service Fabric-gatewaypartitie, die doorstuurt naar de statusopslag. Het rapport kan worden geaccepteerd door de gateway, maar afgewezen door het gezondheidsarchief na extra validatie. Het statusarchief kan het rapport bijvoorbeeld afwijzen vanwege een ongeldige parameter, zoals een verouderd volgnummer. Als u wilt zien of het rapport is toegepast in het gezondheidsarchief, controleert u of het rapport wordt weergegeven in de sectie Gebeurtenissen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --health-property [Vereist] | De eigenschap van de gezondheidsinformatie. <br><br> Een entiteit kan statusrapporten voor verschillende eigenschappen hebben. De eigenschap is een tekenreeks en geen vaste opsomming om de verslaggever flexibiliteit om de toestand voorwaarde die het rapport triggers categoriseren. Een verslaggever met SourceId "LocalWatchdog" kan bijvoorbeeld de status van de beschikbare schijf op een knooppunt controleren, zodat het de eigenschap 'Beschikbare schijf' op dat knooppunt kan rapporteren. Dezelfde verslaggever kan de verbinding met het knooppunt controleren, zodat hij een eigenschap "Connectiviteit" op hetzelfde knooppunt kan melden. In het gezondheidsarchief worden deze rapporten behandeld als afzonderlijke statusgebeurtenissen voor het opgegeven knooppunt. Samen met de SourceId identificeert het pand op unieke wijze de gezondheidsinformatie. |
| --gezondheidstoestand [Vereist] | Mogelijke waarden\: zijn 'Ongeldig', 'Ok', 'Waarschuwing', 'Fout', 'Onbekend'. |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --source-id [Vereist] | De bronnaam die de client/waakhond/systeemcomponent identificeert die de gezondheidsinformatie heeft gegenereerd. |
| --beschrijving | De beschrijving van de gezondheidsinformatie. <br><br> Het vertegenwoordigt gratis tekst die wordt gebruikt om menselijke leesbare informatie over het rapport toe te voegen. De maximale tekenreekslengte voor de beschrijving is 4096 tekens. Als de meegeleverde tekenreeks langer is, wordt deze automatisch afgekapt. Wanneer afgekapt, bevatten de laatste tekens van de beschrijving een markering "[Afgekapt]", en de totale tekenreeksgrootte is 4096 tekens. De aanwezigheid van de markering geeft aan gebruikers aan dat er een afsplitsing is opgetreden. Houd er rekening mee dat wanneer deze wordt afgekapt, de beschrijving minder dan 4096 tekens uit de oorspronkelijke tekenreeks bevat. |
| --onmiddellijke | Een vlag die aangeeft of het rapport onmiddellijk moet worden verzonden. <br><br> Er wordt een gezondheidsrapport verzonden naar een Service Fabric-gatewaytoepassing, die doorstuurt naar de statuswinkel. Als Onmiddellijk is ingesteld op true, wordt het rapport onmiddellijk vanuit HTTP Gateway naar het gezondheidsarchief verzonden, ongeacht de fabricclientinstellingen die de HTTP-gatewaytoepassing gebruikt. Dit is handig voor kritieke rapporten die zo snel mogelijk moeten worden verzonden. Afhankelijk van de timing en andere voorwaarden kan het verzenden van het rapport nog steeds mislukken, bijvoorbeeld als de HTTP-gateway is gesloten of het bericht de gateway niet bereikt. Als Onmiddellijk is ingesteld op false, wordt het rapport verzonden op basis van de statusclientinstellingen van de HTTP-gateway. Daarom wordt het batched volgens de HealthReportSendInterval-configuratie. Dit is de aanbevolen instelling omdat de statusclient hiermee statusrapportageberichten naar het gezondheidsarchief en de verwerking van gezondheidsrapporten kan optimaliseren. Rapporten worden standaard niet onmiddellijk verzonden. |
| --verwijderen-wanneer het is verlopen | Waarde die aangeeft of het rapport wordt verwijderd uit het gezondheidsarchief wanneer het verloopt. <br><br> Als het rapport is ingesteld op true, wordt het rapport uit het gezondheidsarchief verwijderd nadat het is verlopen. Als het rapport is ingesteld op false, wordt het als een fout behandeld wanneer het is verlopen. De waarde van deze eigenschap is standaard onjuist. Wanneer clients periodiek rapporteren, moeten ze RemoveWhenExpired false (standaard) instellen. Op deze manier, is de verslaggever heeft problemen (bijvoorbeeld impasse) en kan niet melden, de entiteit wordt geëvalueerd op fout wanneer het gezondheidsrapport verloopt. Hiermee wordt de entiteit als foutstatus aandeind. |
| --sequence-nummer | Het volgnummer voor dit gezondheidsrapport als een numerieke tekenreeks. <br><br> Het rapportreeksnummer wordt door het statusarchief gebruikt om verouderde rapporten te detecteren. Als dit niet is opgegeven, wordt een volgnummer automatisch gegenereerd door de statusclient wanneer een rapport wordt toegevoegd. |
| --time-out -t | Standaard\: 60. |
| --ttl | De duur waarvoor dit gezondheidsrapport geldig is. In dit veld wordt de ISO8601-indeling gebruikt voor het opgeven van de duur. <br><br> Wanneer klanten periodiek rapporteren, moeten ze rapporten verzenden met een hogere frequentie dan de tijd om te leven. Als klanten rapporteren over de overgang, kunnen ze de tijd instellen om te leven op oneindig. Wanneer de tijd om te leven verloopt, wordt de statusgebeurtenis die de statusgegevens bevat, verwijderd uit het gezondheidsarchief, als RemoveWhenExpired true is of geëvalueerd bij een fout, als RemoveWhenExpired false. Als dit niet is opgegeven, is de tijd om standaard te leven in een oneindige waarde. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-partition-restart"></a>sfctl-partitie opnieuw opstarten
Deze API start sommige of alle replica's of exemplaren van de opgegeven partitie opnieuw.

Deze API is handig voor het testen van failover. Als deze wordt gebruikt om een statusloze servicepartitie te targeten, moet RestartPartitionMode AllReplicasOrInstances zijn. Bel de GetPartitionRestartProgress API met dezelfde OperationId om de voortgang te krijgen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --operation-id [Vereist] | Een GUID die een aanroep van deze API identificeert.  Dit wordt doorgegeven aan de bijbehorende GetProgress API. |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --restart-partition-mode [Vereist] | Beschrijf welke partities u opnieuw wilt starten. |
| --service-id [Vereist] | De identiteit van de dienst. Deze ID is meestal de volledige naam van\:de dienst zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de servicenaam bijvoorbeeld "fabric\:/myapp/app1/svc1" is, is\~de\~service-identiteit "myapp app1 svc1" in 6.0+ en "myapp/app1/svc1" in eerdere versies. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-partition-restart-status"></a>sfctl partitie herstart-status
Hiermee wordt de voortgang van een partitiebewerkinggestart gestart met StartPartitionRestart.

Hiermee wordt de voortgang van een PartitionRestart gestart met StartPartitionRestart met behulp van de meegeleverde OperationId.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --operation-id [Vereist] | Een GUID die een aanroep van deze API identificeert.  Dit wordt doorgegeven aan de bijbehorende GetProgress API. |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --service-id [Vereist] | De identiteit van de dienst. Deze ID is meestal de volledige naam van\:de dienst zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de servicenaam bijvoorbeeld "fabric\:/myapp/app1/svc1" is, is\~de\~service-identiteit "myapp app1 svc1" in 6.0+ en "myapp/app1/svc1" in eerdere versies. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-partition-svc-name"></a>sfctl partitie svc-naam
Krijgt de naam van de Service Fabric-service voor een partitie.

Krijgt de naam van de service voor de opgegeven partitie. Er wordt een fout van 404 geretourneerd als de partitie-id niet in het cluster bestaat.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --partitie-id [Vereist] | De identiteit van de partitie. |
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
- [Stel](service-fabric-cli.md) de SERVICE Fabric CLI in.
- Meer informatie over het gebruik van de CLI van de ServiceFabric met behulp van de [voorbeeldscripts](/azure/service-fabric/scripts/sfctl-upgrade-application).
