---
title: Azure Service Fabric CLI- sfctl-cluster
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten voor het beheren van clusters.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 007ad6f59f0ce304db579f4faa1bb95611a93a37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906149"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Selecteer, beheer en beheer Service Fabric-clusters.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| codeversies | Hier krijgt u een lijst met fabriccodeversies die zijn ingericht in een cluster van Servicefabric. |
| config-versies | Hier krijgt u een lijst met fabric config-versies die zijn ingericht in een cluster van Service Fabric. |
| Gezondheid | Krijgt de status van een Service Fabric-cluster. |
| manifest | Download het clustermanifest Service Fabric. |
| operatie annuleren | Hiermee annuleert u een door de gebruiker veroorzaakte foutbewerking. |
| operatielijst | Krijgt een lijst met door de gebruiker geïnduceerde foutbewerkingen gefilterd op de opgegeven invoer. |
| Bepaling | Instel de code- of configuratiepakketten van een Service Fabric-cluster. |
| herstelsysteem | Geeft aan het cluster Servicefabric aan dat het moet proberen de systeemservices te herstellen die momenteel vastzitten in quorumverlies. |
| rapport-gezondheid | Hiermee verzendt u een statusrapport op het cluster Servicefabric. |
| Selecteer | Maakt verbinding met een eindpunt van een clustervan Service Fabric. |
| show-verbinding | Laat zien met welk Service Fabric-cluster deze sfctl-instantie is verbonden. |
| ontstellen | De inrichten van de code- of configuratiepakketten van een Service Fabric-cluster ongedaan maken. |
| upgrade | Begin met het upgraden van de code of configuratieversie van een Service Fabric-cluster. |
| upgrade-cv | Laat de clusterupgrade doornaar het volgende upgradedomein gaan. |
| upgrade-rollback | De upgrade van een Service Fabric-cluster terugdraaien. |
| upgradestatus | Wordt de voortgang van de huidige clusterupgrade opandere plaats gedeeld. |
| upgrade-update | Werk de upgradeparameters van een clusterupgrade van Service Fabric bij. |

## <a name="sfctl-cluster-code-versions"></a>sfctl-clustercodeversies
Hier krijgt u een lijst met fabriccodeversies die zijn ingericht in een cluster van Servicefabric.

Hier krijgt u een lijst met informatie over fabriccodeversies die in het cluster zijn ingericht. De parameter CodeVersion kan worden gebruikt om de uitvoer optioneel te filteren op alleen die specifieke versie.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --code-versie | De productversie van Service Fabric. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-cluster-config-versions"></a>sfctl cluster config-versies
Hier krijgt u een lijst met fabric config-versies die zijn ingericht in een cluster van Service Fabric.

Hier krijgt u een lijst met informatie over fabric config-versies die in het cluster zijn ingericht. De parameter ConfigVersion kan worden gebruikt om de uitvoer optioneel te filteren op alleen die specifieke versie.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --config-versie | De config versie van Service Fabric. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-cluster-health"></a>sfctl-clusterstatus
Krijgt de status van een Service Fabric-cluster.

Gebruik EventsHealthStateFilter om de verzameling statusgebeurtenissen die op het cluster worden gerapporteerd op basis van de status te filteren. Gebruik ook NodesHealthStateFilter en ApplicationsHealthStateFilter om het verzamelen van knooppunten en toepassingen die zijn geretourneerd te filteren op basis van hun geaggregeerde status.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --applications-health-state-filter | Hiermee u het filteren van de statusobjecten van de toepassing die zijn geretourneerd in het resultaat van clusterstatusquery's op basis van hun status. De mogelijke waarden voor deze parameter omvatten gehele waarde verkregen van leden of bitwise bewerkingen op leden van HealthStateFilter opsomming. Alleen toepassingen die overeenkomen met het filter worden geretourneerd. Alle toepassingen worden gebruikt om de geaggregeerde status te evalueren. Als dit niet is opgegeven, worden alle items geretourneerd. De statuswaarden zijn op vlag gebaseerde opsomming, dus de waarde kan een combinatie zijn van deze waarden die zijn verkregen met bitwise 'OR'-operator. Als de opgegeven waarde bijvoorbeeld 6 is, wordt de status van toepassingen met healthstate-waarde van OK (2) en Waarschuwing (4) geretourneerd.  <br> - Standaard - Standaardwaarde. Komt overeen met elke HealthState. De waarde is nul.  <br> - Geen - Filter dat niet overeenkomt met een HealthState-waarde. Gebruikt om geen resultaten op een bepaalde verzameling van staten terug te keren. De waarde is 1.  <br> - Ok - Filter dat overeenkomt met invoer met HealthState-waarde Ok. De waarde is 2.  <br> - Waarschuwing - Filter dat overeenkomt met invoer met Waarschuwing voor de waarde HealthState. De waarde is 4.  <br> - Fout - Filter dat overeenkomt met invoer met Statuswaardefout. De waarde is 8.  <br> - Alles - Filter dat overeenkomt met de invoer met een healthstate-waarde. De waarde is 65535. |
| --events-health-state-filter | Hiermee u de verzameling HealthEvent-objecten filteren die zijn geretourneerd op basis van de status. De mogelijke waarden voor deze parameter omvatten de gehele waarde van een van de volgende statussen. Alleen gebeurtenissen die overeenkomen met het filter worden geretourneerd. Alle gebeurtenissen worden gebruikt om de geaggregeerde status te evalueren. Als dit niet is opgegeven, worden alle items geretourneerd. De statuswaarden zijn op vlag gebaseerde opsomming, dus de waarde kan een combinatie zijn van deze waarden, verkregen met behulp van de bitwise 'OR'-operator. Als de opgegeven waarde bijvoorbeeld 6 is, worden alle gebeurtenissen met de statuswaarde Van OK (2) en Waarschuwing (4) geretourneerd.  <br> - Standaard - Standaardwaarde. Komt overeen met elke HealthState. De waarde is nul.  <br> - Geen - Filter dat niet overeenkomt met een HealthState-waarde. Gebruikt om geen resultaten op een bepaalde verzameling van staten terug te keren. De waarde is 1.  <br> - Ok - Filter dat overeenkomt met invoer met HealthState-waarde Ok. De waarde is 2.  <br> - Waarschuwing - Filter dat overeenkomt met invoer met Waarschuwing voor de waarde HealthState. De waarde is 4.  <br> - Fout - Filter dat overeenkomt met invoer met Statuswaardefout. De waarde is 8.  <br> - Alles - Filter dat overeenkomt met de invoer met een healthstate-waarde. De waarde is 65535. |
| --exclude-health-statistics | Geeft aan of de statusstatistieken moeten worden geretourneerd als onderdeel van het queryresultaat. Standaard onwaar. De statistieken tonen het aantal kinderen entiteiten in de gezondheidstoestand Ok, Waarschuwing en Fout. |
| --include-system-application-health-statistics | Geeft aan of de gezondheidsstatistieken de statistieken voor de gezondheid van de stof\:/Systeemtoepassing moeten bevatten. Standaard onwaar. Als IncludeSystemApplicationHealthStatistics is ingesteld op true, bevatten de gezondheidsstatistieken\:de entiteiten die behoren tot de fabric /System-toepassing. Anders bevat het queryresultaat alleen gezondheidsstatistieken voor gebruikerstoepassingen. De statusstatistieken moeten worden opgenomen in het queryresultaat voor het toepassen van deze parameter. |
| --nodes-health-state-filter | Hiermee u filteren op de objecten van de status van het knooppunt die zijn geretourneerd in het resultaat van clusterstatusquery's op basis van hun status. De mogelijke waarden voor deze parameter omvatten de gehele waarde van een van de volgende statussen. Alleen knooppunten die overeenkomen met het filter worden geretourneerd. Alle knooppunten worden gebruikt om de geaggregeerde status te evalueren. Als dit niet is opgegeven, worden alle items geretourneerd. De statuswaarden zijn op vlag gebaseerde opsomming, dus de waarde kan een combinatie zijn van deze waarden die zijn verkregen met bitwise 'OR'-operator. Als de opgegeven waarde bijvoorbeeld 6 is, wordt de status van knooppunten met statusstatus van OK (2) en Waarschuwing (4) geretourneerd.  <br> - Standaard - Standaardwaarde. Komt overeen met elke HealthState. De waarde is nul.  <br> - Geen - Filter dat niet overeenkomt met een HealthState-waarde. Gebruikt om geen resultaten op een bepaalde verzameling van staten terug te keren. De waarde is 1.  <br> - Ok - Filter dat overeenkomt met invoer met HealthState-waarde Ok. De waarde is 2.  <br> - Waarschuwing - Filter dat overeenkomt met invoer met Waarschuwing voor de waarde HealthState. De waarde is 4.  <br> - Fout - Filter dat overeenkomt met invoer met Statuswaardefout. De waarde is 8.  <br> - Alles - Filter dat overeenkomt met de invoer met een healthstate-waarde. De waarde is 65535. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-cluster-manifest"></a>sfctl-clustermanifest
Download het clustermanifest Service Fabric.

Download het clustermanifest Service Fabric. Het clustermanifest bevat eigenschappen van het cluster die verschillende knooppunttypen op het cluster bevatten, beveiligingsconfiguraties, fout- en upgradedomeintopologieën, enz. Deze eigenschappen worden opgegeven als onderdeel van het clusterConfig.JSON-bestand terwijl een zelfstandige cluster wordt geïmplementeerd. De meeste informatie in het clustermanifest wordt echter intern gegenereerd door servicefabric tijdens de implementatie van het cluster in andere implementatiescenario's (bijvoorbeeld bij het gebruik van Azure-portal). De inhoud van het clustermanifest is alleen voor informatieve doeleinden en van gebruikers wordt niet verwacht dat ze afhankelijk zijn van de indeling van de bestandsinhoud of de interpretatie ervan.

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

## <a name="sfctl-cluster-operation-cancel"></a>sfctl cluster operation-cancel
Hiermee annuleert u een door de gebruiker veroorzaakte foutbewerking.

De volgende API's starten foutbewerkingen die\: kunnen worden geannuleerd met CancelOperation StartDataLoss, StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Als kracht vals is, wordt de opgegeven door de gebruiker geïnduceerde bewerking op een elegante manier gestopt en opgeschoond.  Als de kracht waar is, wordt de opdracht afgebroken en kan een interne status worden achtergelaten.  Het specificeren van kracht als waar moet met zorg worden gebruikt. Het aanroepen van deze API met force set to true is niet toegestaan totdat deze API al is aangeroepen op dezelfde testopdracht met force set op false first, of tenzij de testopdracht al een OperationState of OperationState.RollingBack heeft. Verduidelijking\: OperationState.RollingBack betekent dat het systeem de interne systeemstatus wordt/opruimt die wordt veroorzaakt door het uitvoeren van de opdracht.  Het herstelt geen gegevens als de testopdracht gegevensverlies zou veroorzaken.  Als u bijvoorbeeld StartDataLoss aanroept en deze API aanroept, wordt de interne status alleen opgeschoond door de opdracht uit te voeren. Het herstelt de gegevens van de doelpartitie niet als de opdracht ver genoeg is gevorderd om gegevensverlies te veroorzaken. Belangrijke\: opmerking als deze API wordt aangeroepen met force==true, kan de interne status worden achtergelaten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --operation-id [Vereist] | Een GUID die een aanroep van deze API identificeert.  Dit wordt doorgegeven aan de bijbehorende GetProgress API. |
| --kracht | Hiermee geeft u aan of u de interne systeemstatus die is gewijzigd door de door de gebruiker veroorzaakte bewerking afzonderlijk terug te draaien en op te schonen. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-cluster-operation-list"></a>sfctl-clusterbewerkingslijst
Krijgt een lijst met door de gebruiker geïnduceerde foutbewerkingen gefilterd op de opgegeven invoer.

Hier wordt de lijst met door de gebruiker geïnduceerde foutbewerkingen gefilterd op de opgegeven invoer.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --state-filter | Wordt gebruikt om te filteren op OperationState's voor door de gebruiker geïnduceerde bewerkingen. - 65535 - selecteer Alles - 1 - selecteer Lopend - 2 - selecteer RollingBack - 8 - selecteer Voltooid - 16 - selecteer Fout - 32 - selecteer Geannuleerd - 64 - selecteer ForceCancelled.  Standaard\: 65535. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |
| -type-filter | Wordt gebruikt om te filteren op OperationType voor door de gebruiker geïnduceerde bewerkingen. - 65535 - selecteer alles - 1 - selecteer PartitionDataLoss. - 2 - selecteer PartitionQuorumLoss. - 4 - selecteer PartitionRestart. - 8 - selecteer NodeTransition.  Standaard\: 65535. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-cluster-provision"></a>sfctl-clusterbepaling
Instel de code- of configuratiepakketten van een Service Fabric-cluster.

Valideer en instel de code- of configuratiepakketten van een Service Fabric-cluster.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --cluster-manifest-file-path | Het clustermanifestbestandspad. |
| --code-file-path | Het bestandspad van het clustercodepakket. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-cluster-recover-system"></a>sfctl cluster recover-systeem
Geeft aan het cluster Servicefabric aan dat het moet proberen de systeemservices te herstellen die momenteel vastzitten in quorumverlies.

Geeft aan het cluster Servicefabric aan dat het moet proberen de systeemservices te herstellen die momenteel vastzitten in quorumverlies. Deze bewerking mag alleen worden uitgevoerd als bekend is dat de replica's die zijn uitgeschakeld niet kunnen worden hersteld. Onjuist gebruik van deze API kan leiden tot mogelijk gegevensverlies.

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

## <a name="sfctl-cluster-report-health"></a>sfctl cluster rapport-status
Hiermee verzendt u een statusrapport op het cluster Servicefabric.

Hiermee verzendt u een statusrapport op een cluster van servicefabric. Het rapport moet de informatie bevatten over de bron van het gezondheidsrapport en de eigenschap waarop het wordt gerapporteerd. Het rapport wordt verzonden naar een Service Fabric-gatewayknooppunt, dat doorstuurt naar de gezondheidswinkel. Het rapport kan worden geaccepteerd door de gateway, maar afgewezen door het gezondheidsarchief na extra validatie. Het statusarchief kan het rapport bijvoorbeeld afwijzen vanwege een ongeldige parameter, zoals een verouderd volgnummer. Voer GetClusterHealth uit om te zien of het rapport is toegepast in het gezondheidsarchief en controleert of het rapport wordt weergegeven in de sectie HealthEvents.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --health-property [Vereist] | De eigenschap van de gezondheidsinformatie. <br><br> Een entiteit kan statusrapporten voor verschillende eigenschappen hebben. De eigenschap is een tekenreeks en geen vaste opsomming om de verslaggever flexibiliteit om de toestand voorwaarde die het rapport triggers categoriseren. Een verslaggever met SourceId "LocalWatchdog" kan bijvoorbeeld de status van de beschikbare schijf op een knooppunt controleren, zodat het de eigenschap 'Beschikbare schijf' op dat knooppunt kan rapporteren. Dezelfde verslaggever kan de verbinding met het knooppunt controleren, zodat hij een eigenschap "Connectiviteit" op hetzelfde knooppunt kan melden. In het gezondheidsarchief worden deze rapporten behandeld als afzonderlijke statusgebeurtenissen voor het opgegeven knooppunt. Samen met de SourceId identificeert het pand op unieke wijze de gezondheidsinformatie. |
| --gezondheidstoestand [Vereist] | Mogelijke waarden\: zijn 'Ongeldig', 'Ok', 'Waarschuwing', 'Fout', 'Onbekend'. |
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

## <a name="sfctl-cluster-select"></a>sfctl-cluster selecteren
Maakt verbinding met een eindpunt van een clustervan Service Fabric.

Als u verbinding maakt met een beveiligd cluster, geeft u een absoluut pad op naar een cert (.crt) en sleutelbestand (.key) of een enkel bestand met beide (.pem). Geef beide niet op. Als u verbinding maakt met een beveiligd cluster, geeft u optioneel ook een absoluut pad op naar een CA-bundelbestand of een map met vertrouwde CA-certs.  Er is geen verbinding met een cluster zonder deze opdracht eerst uit te voeren, inclusief een verbinding met localhost. Er is echter geen expliciet eindpunt vereist voor het maken van verbinding met een lokaal cluster.  Als u een zelfondertekend cert of een ander certificaat gebruikt dat niet is ondertekend door een bekende CA, geeft u de parameter -ca in om ervoor te zorgen dat de validatie wordt doorgegeven. Als niet op een productiecluster, om clientside validatie te omzeilen (handig voor zelf ondertekend of niet bekend CA ondertekend), gebruik dan de optie --geen-verifiëren. Hoewel het mogelijk is, wordt het niet aanbevolen voor productieclusters. Een fout bij de verificatie van certificaten kan anders het gevolg zijn.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --aad | Gebruik Azure Active Directory voor verificatie. |
| --ca | Absolute path to CA certs directory to treat as valid or CA bundle file. Als u een map met `c_rehash <directory>` CA-cert's gebruikt, moet de door OpenSSL geleverde gegevens eerst worden uitgevoerd om de certificaathashes te berekenen en de juiste symbolicskoppelingen te maken. Dit wordt gebruikt om te controleren of het certificaat dat door het cluster is geretourneerd, geldig is. |
| --cert | Absoluut pad naar een clientcertificaatbestand. |
| -Eindpunt | Clustereindpunt-URL, inclusief poort en HTTP- of HTTPS-voorvoegsel. Meestal ziet het eindpunt er ongeveer\:uit als https \:-<uw url>19080. Als er geen eindpunt wordt gegeven,\:wordt\:dit standaard weergegeven op http -localhost 19080.  Standaard\: \:http /localhost\:19080. |
| --toets | Absoluut pad naar clientcertificaatsleutelbestand. |
| --nee-verifiëren | Schakel de verificatie voor certificaten\: uit bij het gebruik van HTTPS, let op dat dit een onveilige optie is en mag niet worden gebruikt voor productieomgevingen. |
| --pem | Absolute weg naar clientcertificaat, als een .pem-bestand. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-cluster-show-connection"></a>sfctl cluster show-verbinding
Laat zien met welk Service Fabric-cluster deze sfctl-instantie is verbonden.

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-cluster-unprovision"></a>sfctl-cluster ontviand
De inrichten van de code- of configuratiepakketten van een Service Fabric-cluster ongedaan maken.

Het wordt ondersteund om code en configuratie afzonderlijk te ontstellen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --code-versie | De versie van het clustercodepakket. |
| --config-versie | De clustermanifestversie. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-cluster-upgrade"></a>sfctl-clusterupgrade
Begin met het upgraden van de code of configuratieversie van een Service Fabric-cluster.

Valideer de meegeleverde upgradeparameters en begin met het upgraden van de code of configuratieversie van een Service Fabric-cluster als de parameters geldig zijn.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-health-map | JSON gecodeerd woordenboek van paren van de naam van de toepassing en het maximale percentage ongezond voordat het verhogen van de fout. |
| --app-type-health-map | JSON gecodeerd woordenboek van paren van de naam van het toepassingstype en het maximumpercentage ongezond alvorens fout op te halen. |
| --code-versie | De versie van de clustercode. |
| --config-versie | De versie van de clusterconfiguratie. |
| --delta-health-evaluatie | Hiermee wordt delta-gezondheidsevaluatie mogelijk gemaakt in plaats van een absolute gezondheidsevaluatie na voltooiing van elk upgradedomein. |
| --delta-ongezonde knooppunten | Het maximaal toegestane percentage van de statusdegradatie van knooppunten is toegestaan tijdens clusterupgrades.  Standaard\: 10. <br><br> De delta wordt gemeten tussen de status van de knooppunten aan het begin van de upgrade en de status van de knooppunten op het moment van de gezondheidsevaluatie. De controle wordt uitgevoerd na elke voltooiing van de upgrade van het upgradedomein om ervoor te zorgen dat de algemene status van het cluster binnen de getolereerde limieten valt. |
| --failure-action | Mogelijke waarden\: zijn 'Ongeldig', 'Rollback', 'Manual'. |
| --force-herstart | Processen worden met kracht opnieuw gestart tijdens de upgrade, zelfs als de codeversie niet is gewijzigd. <br><br> De upgrade wijzigt alleen de configuratie of gegevens. |
| --health-check-retry | De tijdsduur tussen pogingen om statuscontroles uit te voeren als de toepassing of het cluster niet in orde is. |
| --health-check-stable | De hoeveelheid tijd dat de toepassing of het cluster gezond moet blijven voordat de upgrade doorgaat naar het volgende upgradedomein. <br><br> Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. |
| --health-check-wait | De tijdsduur om te wachten na het voltooien van een upgradedomein voordat u het proces voor statuscontroles start. |
| --replica-set-check-time-out | De maximale tijd om de verwerking van een upgradedomein te blokkeren en verlies van beschikbaarheid te voorkomen wanneer er onverwachte problemen zijn. <br><br> Wanneer deze time-out verloopt, verloopt de verwerking van het upgradedomein ongeacht problemen met het verlies van beschikbaarheid. De time-out wordt aan het begin van elk upgradedomein opnieuw ingesteld. Geldige waarden liggen tussen 0 en 42949672925. |
| --rolling-upgrade-modus | Mogelijke waarden\: zijn 'Invalid', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitored'.  Standaard\: Niet-gecontroleerdAuto. |
| --time-out -t | Standaard\: 60. |
| --ongezonde toepassingen | Het maximaal toegestane percentage ongezonde toepassingen voordat een fout wordt gemeld. <br><br> Bijvoorbeeld, om 10% van de toepassingen ongezond te maken, zou deze waarde 10 zijn. Het percentage vertegenwoordigt het maximale getolereerde percentage toepassingen dat ongezond kan zijn voordat het cluster als fout wordt beschouwd. Als het percentage wordt gerespecteerd, maar er ten minste één ongezonde toepassing is, wordt de status geëvalueerd als Waarschuwing. Dit wordt berekend door het aantal ongezonde toepassingen te verdelen over het totale aantal toepassingsexemplaren in het cluster, met uitzondering van toepassingen van toepassingstypen die zijn opgenomen in de ApplicationTypeHealthPolicyMap. De berekening rondt af om één fout op kleine aantallen toepassingen te tolereren. |
| --ongezonde knooppunten | Het maximaal toegestane percentage ongezonde knooppunten voordat een fout wordt gemeld. <br><br> Als u bijvoorbeeld wilt toestaan dat 10% van de knooppunten ongezond is, is deze waarde 10. Het percentage vertegenwoordigt het maximale getolereerde percentage knooppunten dat ongezond kan zijn voordat het cluster als fout wordt beschouwd. Als het percentage wordt gerespecteerd, maar er ten minste één ongezond knooppunt is, wordt de status geëvalueerd als Waarschuwing. Het percentage wordt berekend door het aantal ongezonde knooppunten te verdelen over het totale aantal knooppunten in het cluster. De berekening wordt afgerond om één fout op kleine aantallen knooppunten te tolereren. In grote clusters zijn sommige knooppunten altijd down of out voor reparaties, dus dit percentage moet worden geconfigureerd om dat te tolereren. |
| --upgrade-domein-delta-ongezonde knooppunten | Het maximaal toegestane percentage van upgrade domeinknooppunten statusdegradatie toegestaan tijdens cluster upgrades.  Standaard\: 15. <br><br> De delta wordt gemeten tussen de status van de upgradedomeinknooppunten aan het begin van de upgrade en de status van de upgradedomeinknooppunten op het moment van de statusbeoordeling. De controle wordt uitgevoerd na elke upgrade domein upgrade voltooiing voor alle voltooide upgrade domeinen om ervoor te zorgen dat de status van de upgrade domeinen is binnen getolereerd grenzen. |
| --upgrade-domein-time-out | De hoeveelheid tijd die elk upgradedomein moet voltooien voordat FailureAction wordt uitgevoerd. <br><br> Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. |
| --upgrade-time-out | De hoeveelheid tijd die de algehele upgrade moet voltooien voordat FailureAction wordt uitgevoerd. <br><br> Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. |
| --waarschuwing-als-fout | Geeft aan of waarschuwingen met dezelfde ernst worden behandeld als fouten. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-cluster-upgrade-resume"></a>sfctl cluster upgrade-hervatten
Laat de clusterupgrade doornaar het volgende upgradedomein gaan.

Zorg ervoor dat de clustercode of configuratie-upgrade zo nodig doorgaat naar het volgende upgradedomein.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --upgrade-domein [Vereist] | Het volgende upgradedomein voor deze clusterupgrade. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-cluster-upgrade-rollback"></a>sfctl cluster upgrade-rollback
De upgrade van een Service Fabric-cluster terugdraaien.

De code of configuratie-upgrade van een Service Fabric-cluster terugdraaien.

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

## <a name="sfctl-cluster-upgrade-status"></a>sfctl-clusterupgradestatus
Wordt de voortgang van de huidige clusterupgrade opandere plaats gedeeld.

Krijgt de huidige voortgang van de lopende clusterupgrade. Als er momenteel geen upgrade wordt uitgevoerd, krijgt u de laatste status van de vorige clusterupgrade.

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

## <a name="sfctl-cluster-upgrade-update"></a>sfctl cluster upgrade-update
Werk de upgradeparameters van een clusterupgrade van Service Fabric bij.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-health-map | JSON gecodeerd woordenboek van paren van de naam van de toepassing en het maximale percentage ongezond voordat het verhogen van de fout. |
| --app-type-health-map | JSON gecodeerd woordenboek van paren van de naam van het toepassingstype en het maximumpercentage ongezond alvorens fout op te halen. |
| --delta-health-evaluatie | Hiermee wordt delta-gezondheidsevaluatie mogelijk gemaakt in plaats van een absolute gezondheidsevaluatie na voltooiing van elk upgradedomein. |
| --delta-ongezonde knooppunten | Het maximaal toegestane percentage van de statusdegradatie van knooppunten is toegestaan tijdens clusterupgrades.  Standaard\: 10. <br><br> De delta wordt gemeten tussen de status van de knooppunten aan het begin van de upgrade en de status van de knooppunten op het moment van de gezondheidsevaluatie. De controle wordt uitgevoerd na elke voltooiing van de upgrade van het upgradedomein om ervoor te zorgen dat de algemene status van het cluster binnen de getolereerde limieten valt. |
| --failure-action | Mogelijke waarden\: zijn 'Ongeldig', 'Rollback', 'Manual'. |
| --force-herstart | Processen worden met kracht opnieuw gestart tijdens de upgrade, zelfs als de codeversie niet is gewijzigd. <br><br> De upgrade wijzigt alleen de configuratie of gegevens. |
| --health-check-retry | De tijdsduur tussen pogingen om statuscontroles uit te voeren als de toepassing of het cluster niet in orde is. |
| --health-check-stable | De hoeveelheid tijd dat de toepassing of het cluster gezond moet blijven voordat de upgrade doorgaat naar het volgende upgradedomein. <br><br> Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. |
| --health-check-wait | De tijdsduur om te wachten na het voltooien van een upgradedomein voordat u het proces voor statuscontroles start. |
| --replica-set-check-time-out | De maximale tijd om de verwerking van een upgradedomein te blokkeren en verlies van beschikbaarheid te voorkomen wanneer er onverwachte problemen zijn. <br><br> Wanneer deze time-out verloopt, verloopt de verwerking van het upgradedomein ongeacht problemen met het verlies van beschikbaarheid. De time-out wordt aan het begin van elk upgradedomein opnieuw ingesteld. Geldige waarden liggen tussen 0 en 42949672925. |
| --rolling-upgrade-modus | Mogelijke waarden\: zijn 'Invalid', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitored'.  Standaard\: Niet-gecontroleerdAuto. |
| --time-out -t | Standaard\: 60. |
| --ongezonde toepassingen | Het maximaal toegestane percentage ongezonde toepassingen voordat een fout wordt gemeld. <br><br> Bijvoorbeeld, om 10% van de toepassingen ongezond te maken, zou deze waarde 10 zijn. Het percentage vertegenwoordigt het maximale getolereerde percentage toepassingen dat ongezond kan zijn voordat het cluster als fout wordt beschouwd. Als het percentage wordt gerespecteerd, maar er ten minste één ongezonde toepassing is, wordt de status geëvalueerd als Waarschuwing. Dit wordt berekend door het aantal ongezonde toepassingen te verdelen over het totale aantal toepassingsexemplaren in het cluster, met uitzondering van toepassingen van toepassingstypen die zijn opgenomen in de ApplicationTypeHealthPolicyMap. De berekening rondt af om één fout op kleine aantallen toepassingen te tolereren. |
| --ongezonde knooppunten | Het maximaal toegestane percentage ongezonde knooppunten voordat een fout wordt gemeld. <br><br> Als u bijvoorbeeld wilt toestaan dat 10% van de knooppunten ongezond is, is deze waarde 10. Het percentage vertegenwoordigt het maximale getolereerde percentage knooppunten dat ongezond kan zijn voordat het cluster als fout wordt beschouwd. Als het percentage wordt gerespecteerd, maar er ten minste één ongezond knooppunt is, wordt de status geëvalueerd als Waarschuwing. Het percentage wordt berekend door het aantal ongezonde knooppunten te verdelen over het totale aantal knooppunten in het cluster. De berekening wordt afgerond om één fout op kleine aantallen knooppunten te tolereren. In grote clusters zijn sommige knooppunten altijd down of out voor reparaties, dus dit percentage moet worden geconfigureerd om dat te tolereren. |
| --upgrade-domein-delta-ongezonde knooppunten | Het maximaal toegestane percentage van upgrade domeinknooppunten statusdegradatie toegestaan tijdens cluster upgrades.  Standaard\: 15. <br><br> De delta wordt gemeten tussen de status van de upgradedomeinknooppunten aan het begin van de upgrade en de status van de upgradedomeinknooppunten op het moment van de statusbeoordeling. De controle wordt uitgevoerd na elke upgrade domein upgrade voltooiing voor alle voltooide upgrade domeinen om ervoor te zorgen dat de status van de upgrade domeinen is binnen getolereerd grenzen. |
| --upgrade-domein-time-out | De hoeveelheid tijd die elk upgradedomein moet voltooien voordat FailureAction wordt uitgevoerd. <br><br> Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. |
| --upgrade-soort | Mogelijke waarden\: zijn 'Ongeldig', 'Rolling', 'Rolling_ForceRestart'.  Standaard\: rollen. |
| --upgrade-time-out | De hoeveelheid tijd die de algehele upgrade moet voltooien voordat FailureAction wordt uitgevoerd. <br><br> Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. |
| --waarschuwing-als-fout | Geeft aan of waarschuwingen met dezelfde ernst worden behandeld als fouten. |

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