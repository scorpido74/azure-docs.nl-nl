---
title: Azure Service Fabric CLI- sfctl-replica
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten voor het beheren van replica's.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f6ad0b4c08ac8d710340fe654a068d0a3804e58f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905804"
---
# <a name="sfctl-replica"></a>sfctl replica
Beheer de replica's die behoren tot servicepartities.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| Ingezet | Krijgt de details van replica geïmplementeerd op een Service Fabric-knooppunt. |
| geïmplementeerde lijst | Hier wordt de lijst met replica's weergegeven die zijn geïmplementeerd op een servicestructuurknooppunt. |
| Gezondheid | Krijgt de status van een servicefabric-statusreplica of stateless service-instantie. |
| Info | Hier krijgt u de informatie over een replica van een servicefabricpartitie. |
| list | Hier krijgt u de informatie over replica's van een Service Fabric-servicepartitie. |
| verwijderen | Hiermee verwijdert u een servicereplica die op een knooppunt wordt uitgevoerd. |
| rapport-gezondheid | Hiermee verzendt u een statusrapport over de replica Service Fabric. |
| restart | Start opnieuw een servicereplica van een nog niet gebruikte service die op een knooppunt wordt uitgevoerd. |

## <a name="sfctl-replica-deployed"></a>sfctl replica ingezet
Krijgt de details van replica geïmplementeerd op een Service Fabric-knooppunt.

Krijgt de details van de replica geïmplementeerd op een Service Fabric-knooppunt. De informatie omvat servicesoort, servicenaam, huidige servicebewerking, begindatum van de huidige servicebewerking, partitie-id, replica-/instantie-id, gerapporteerde belasting en andere informatie.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [Vereist] | De naam van het knooppunt. |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --replica-id [Vereist] | De id van de replica. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-replica-deployed-list"></a>sfctl replica ingezet-lijst
Hier wordt de lijst met replica's weergegeven die zijn geïmplementeerd op een servicestructuurknooppunt.

Hier wordt de lijst weergegeven met de informatie over replica's die zijn geïmplementeerd op een servicefabricknooppunt. De informatie omvat partitie-id, replica-id, status van de replica, naam van de service, naam van het servicetype en andere informatie. Gebruik queryparameters partitionid of ServiceManifestName om informatie over de geïmplementeerde replica's terug te sturen die overeenkomen met de opgegeven waarden voor die parameters.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --node-name [Vereist] | De naam van het knooppunt. |
| --partitie-id | De identiteit van de partitie. |
| --service-manifest-naam | De naam van een servicemanifest dat is geregistreerd als onderdeel van een toepassingstype in een cluster servicestructuur. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-replica-health"></a>sfctl replica gezondheid
Krijgt de status van een servicefabric-statusreplica of stateless service-instantie.

Krijgt de status van een Service Fabric replica. Gebruik EventsHealthStateFilter om de verzameling statusgebeurtenissen die op de replica zijn gerapporteerd op basis van de status te filteren.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --replica-id [Vereist] | De id van de replica. |
| --events-health-state-filter | Hiermee u de verzameling HealthEvent-objecten filteren die zijn geretourneerd op basis van de status. De mogelijke waarden voor deze parameter omvatten de gehele waarde van een van de volgende statussen. Alleen gebeurtenissen die overeenkomen met het filter worden geretourneerd. Alle gebeurtenissen worden gebruikt om de geaggregeerde status te evalueren. Als dit niet is opgegeven, worden alle items geretourneerd. De statuswaarden zijn op vlag gebaseerde opsomming, dus de waarde kan een combinatie zijn van deze waarden, verkregen met behulp van de bitwise 'OR'-operator. Als de opgegeven waarde bijvoorbeeld 6 is, worden alle gebeurtenissen met de statuswaarde Van OK (2) en Waarschuwing (4) geretourneerd.  <br> - Standaard - Standaardwaarde. Komt overeen met elke HealthState. De waarde is nul.  <br> - Geen - Filter dat niet overeenkomt met een HealthState-waarde. Gebruikt om geen resultaten op een bepaalde verzameling van staten terug te keren. De waarde is 1.  <br> - Ok - Filter dat overeenkomt met invoer met HealthState-waarde Ok. De waarde is 2.  <br> - Waarschuwing - Filter dat overeenkomt met invoer met Waarschuwing voor de waarde HealthState. De waarde is 4.  <br> - Fout - Filter dat overeenkomt met invoer met Statuswaardefout. De waarde is 8.  <br> - Alles - Filter dat overeenkomt met de invoer met een healthstate-waarde. De waarde is 65535. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-replica-info"></a>sfctl replica info
Hier krijgt u de informatie over een replica van een servicefabricpartitie.

Het antwoord omvat de id, rol, status, status, knooppuntnaam, uptime en andere details over de replica.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --replica-id [Vereist] | De id van de replica. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-replica-list"></a>sfctl replica lijst
Hier krijgt u de informatie over replica's van een Service Fabric-servicepartitie.

Het eindpunt GetReplicas retourneert informatie over de replica's van de opgegeven partitie. Het antwoord omvat de id, rol, status, status, knooppuntnaam, uptime en andere details over de replica.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --partitie-id [Vereist] | De identiteit van de partitie. |
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

## <a name="sfctl-replica-remove"></a>sfctl replica verwijderen
Hiermee verwijdert u een servicereplica die op een knooppunt wordt uitgevoerd.

Deze API simuleert een replicafout van servicefabric door een replica uit een cluster van servicefabric te verwijderen. De verwijdering sluit de replica, schakelt de replica over naar de rol Geen en verwijdert vervolgens alle statusgegevens van de replica uit het cluster. Deze API test het verwijderingspad voor replicastatus en simuleert het permanente pad van de rapportfout via client-API's. Waarschuwing : er worden geen veiligheidscontroles uitgevoerd wanneer deze API wordt gebruikt. Onjuist gebruik van deze API kan leiden tot gegevensverlies voor stateful services. Bovendien heeft de vlag forceRemove gevolgen voor alle andere replica's die in hetzelfde proces worden gehost.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [Vereist] | De naam van het knooppunt. |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --replica-id [Vereist] | De id van de replica. |
| --kracht-verwijderen | Verwijder een Service Fabric-toepassing of -service krachtig zonder de sierlijke afsluitvolgorde te doorlopen. Deze parameter kan worden gebruikt om een toepassing of service waarvoor verwijderen is timing uit als gevolg van problemen in de servicecode die sierlijke sluiten van replica's voorkomt, krachtig verwijderen. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-replica-report-health"></a>sfctl replica report-health
Hiermee verzendt u een statusrapport over de replica Service Fabric.

Rapporteert de status van de opgegeven replica van de servicestructuur. Het rapport moet de informatie bevatten over de bron van het gezondheidsrapport en de eigenschap waarop het wordt gerapporteerd. Het rapport wordt verzonden naar een Service Fabric-gateway replica, die doorstuurt naar de statuswinkel. Het rapport kan worden geaccepteerd door de gateway, maar afgewezen door het gezondheidsarchief na extra validatie. Het statusarchief kan het rapport bijvoorbeeld afwijzen vanwege een ongeldige parameter, zoals een verouderd volgnummer. Als u wilt zien of het rapport is toegepast in het gezondheidsarchief, voert u de status Replica op en controleert u of het rapport wordt weergegeven in de sectie HealthEvents.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --health-property [Vereist] | De eigenschap van de gezondheidsinformatie. <br><br> Een entiteit kan statusrapporten voor verschillende eigenschappen hebben. De eigenschap is een tekenreeks en geen vaste opsomming om de verslaggever flexibiliteit om de toestand voorwaarde die het rapport triggers categoriseren. Een verslaggever met SourceId "LocalWatchdog" kan bijvoorbeeld de status van de beschikbare schijf op een knooppunt controleren, zodat het de eigenschap 'Beschikbare schijf' op dat knooppunt kan rapporteren. Dezelfde verslaggever kan de verbinding met het knooppunt controleren, zodat hij een eigenschap "Connectiviteit" op hetzelfde knooppunt kan melden. In het gezondheidsarchief worden deze rapporten behandeld als afzonderlijke statusgebeurtenissen voor het opgegeven knooppunt. Samen met de SourceId identificeert het pand op unieke wijze de gezondheidsinformatie. |
| --gezondheidstoestand [Vereist] | Mogelijke waarden\: zijn 'Ongeldig', 'Ok', 'Waarschuwing', 'Fout', 'Onbekend'. |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --replica-id [Vereist] | De identiteit van de partitie. |
| --source-id [Vereist] | De bronnaam die de client/waakhond/systeemcomponent identificeert die de gezondheidsinformatie heeft gegenereerd. |
| --beschrijving | De beschrijving van de gezondheidsinformatie. <br><br> Het vertegenwoordigt gratis tekst die wordt gebruikt om menselijke leesbare informatie over het rapport toe te voegen. De maximale tekenreekslengte voor de beschrijving is 4096 tekens. Als de meegeleverde tekenreeks langer is, wordt deze automatisch afgekapt. Wanneer afgekapt, bevatten de laatste tekens van de beschrijving een markering "[Afgekapt]", en de totale tekenreeksgrootte is 4096 tekens. De aanwezigheid van de markering geeft aan gebruikers aan dat er een afsplitsing is opgetreden. Houd er rekening mee dat wanneer deze wordt afgekapt, de beschrijving minder dan 4096 tekens uit de oorspronkelijke tekenreeks bevat. |
| --onmiddellijke | Een vlag die aangeeft of het rapport onmiddellijk moet worden verzonden. <br><br> Er wordt een gezondheidsrapport verzonden naar een Service Fabric-gatewaytoepassing, die doorstuurt naar de statuswinkel. Als Onmiddellijk is ingesteld op true, wordt het rapport onmiddellijk vanuit HTTP Gateway naar het gezondheidsarchief verzonden, ongeacht de fabricclientinstellingen die de HTTP-gatewaytoepassing gebruikt. Dit is handig voor kritieke rapporten die zo snel mogelijk moeten worden verzonden. Afhankelijk van de timing en andere voorwaarden kan het verzenden van het rapport nog steeds mislukken, bijvoorbeeld als de HTTP-gateway is gesloten of het bericht de gateway niet bereikt. Als Onmiddellijk is ingesteld op false, wordt het rapport verzonden op basis van de statusclientinstellingen van de HTTP-gateway. Daarom wordt het batched volgens de HealthReportSendInterval-configuratie. Dit is de aanbevolen instelling omdat de statusclient hiermee statusrapportageberichten naar het gezondheidsarchief en de verwerking van gezondheidsrapporten kan optimaliseren. Rapporten worden standaard niet onmiddellijk verzonden. |
| --verwijderen-wanneer het is verlopen | Waarde die aangeeft of het rapport wordt verwijderd uit het gezondheidsarchief wanneer het verloopt. <br><br> Als het rapport is ingesteld op true, wordt het rapport uit het gezondheidsarchief verwijderd nadat het is verlopen. Als het rapport is ingesteld op false, wordt het als een fout behandeld wanneer het is verlopen. De waarde van deze eigenschap is standaard onjuist. Wanneer clients periodiek rapporteren, moeten ze RemoveWhenExpired false (standaard) instellen. Op deze manier, is de verslaggever heeft problemen (bijvoorbeeld impasse) en kan niet melden, de entiteit wordt geëvalueerd op fout wanneer het gezondheidsrapport verloopt. Hiermee wordt de entiteit als foutstatus aandeind. |
| --sequence-nummer | Het volgnummer voor dit gezondheidsrapport als een numerieke tekenreeks. <br><br> Het rapportreeksnummer wordt door het statusarchief gebruikt om verouderde rapporten te detecteren. Als dit niet is opgegeven, wordt een volgnummer automatisch gegenereerd door de statusclient wanneer een rapport wordt toegevoegd. |
| --service-vriendelijk | Het soort service replica (stateless of stateful) waarvoor de gezondheid wordt gemeld. Hieronder volgen de\: mogelijke waarden 'Stateless', 'Stateful'.  Standaard\: Stateful. |
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

## <a name="sfctl-replica-restart"></a>sfctl replica opnieuw opstarten
Start opnieuw een servicereplica van een nog niet gebruikte service die op een knooppunt wordt uitgevoerd.

Start opnieuw een servicereplica van een nog niet gebruikte service die op een knooppunt wordt uitgevoerd. Waarschuwing : er worden geen veiligheidscontroles uitgevoerd wanneer deze API wordt gebruikt. Onjuist gebruik van deze API kan leiden tot verlies van beschikbaarheid voor stateful services.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [Vereist] | De naam van het knooppunt. |
| --partitie-id [Vereist] | De identiteit van de partitie. |
| --replica-id [Vereist] | De id van de replica. |
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
