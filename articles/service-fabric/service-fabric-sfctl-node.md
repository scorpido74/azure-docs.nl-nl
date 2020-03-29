---
title: Azure Service Fabric CLI- sfctl-knooppunt
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten voor het beheren van clusterknooppunten.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 5881e6485003abd4fd23a7f6d06a428e768c00fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905876"
---
# <a name="sfctl-node"></a>sfctl node
Beheer de knooppunten die een cluster vormen.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| add-configuration-parameter-overrides | Hiermee voegt u de lijst met configuratieoverschrijvingen toe aan het opgegeven knooppunt. |
| uitschakelen | Deactiveer een clusterknooppunt servicestructuur met de opgegeven deactiveringsintentie. |
| inschakelen | Activeer een clusterknooppunt servicestructuur dat momenteel is gedeactiveerd. |
| get-configuration-overrides | Hier wordt de lijst met configuratieoverschrijvingen op het opgegeven knooppunt weergegeven. |
| Gezondheid | Krijgt de gezondheid van een Service Fabric-knooppunt. |
| Info | Hier vindt u informatie over een specifiek knooppunt in het cluster Servicefabric. |
| list | Hier wordt de lijst met knooppunten weergegeven in het cluster Servicefabric. |
| laden | Krijgt de laadinformatie van een Service Fabric-knooppunt. |
| verwijderen-configuratie-overschrijvingen | Hiermee verwijdert u configuratieoverschrijvingen op het opgegeven knooppunt. |
| verwijderstatus | Hiermee waarschuwt Service Fabric dat de aanhoudende status op een knooppunt permanent is verwijderd of verloren is gegaan. |
| rapport-gezondheid | Hiermee stuurt u een gezondheidsrapport op het servicestructuurknooppunt. |
| restart | Start opnieuw een clusterknooppunt servicestructuur opnieuw. |
| Overgang | Hiermee start of stopt u een clusterknooppunt. |
| overgangsstatus | Hiermee wordt de voortgang van een bewerking gestart met StartNodeTransition. |

## <a name="sfctl-node-add-configuration-parameter-overrides"></a>sfctl node add-configuration-parameter-overrides sfctl node add-configuration-parameter-overrides sfctl node add-configuration-parameter-overrides sfc
Hiermee voegt u de lijst met configuratieoverschrijvingen toe aan het opgegeven knooppunt.

Deze api maakt het toevoegen van alle bestaande configuratieoverschrijvingen op het opgegeven knooppunt mogelijk.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --config-parameter-override-list [Vereist] | Beschrijving voor het toevoegen van lijst met configuratieoverschrijvingen. |
| --node-name [Vereist] | De naam van het knooppunt. |
| --kracht | Het toevoegen van configuratieoverschrijvingen op opgegeven knooppunten forceren. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-node-disable"></a>sfctl-knooppunt uitschakelen
Deactiveer een clusterknooppunt servicestructuur met de opgegeven deactiveringsintentie.

Deactiveer een clusterknooppunt servicestructuur met de opgegeven deactiveringsintentie. Zodra de deactivering aan de gang is, kan de deactiveringsintentie worden verhoogd, maar niet worden verminderd (bijvoorbeeld een knooppunt dat wordt gedeactiveerd met de intentie Onderbreken, kan verder worden gedeactiveerd met Opnieuw starten, maar niet andersom. Knooppunten kunnen worden gereactiveerd met behulp van de bewerking Een knooppunt activeren wanneer ze zijn gedeactiveerd. Als de deactivering niet is voltooid, wordt de deactivering geannuleerd. Een knooppunt dat naar beneden gaat en weer omhoog komt terwijl gedeactiveerd, moet nog steeds opnieuw worden geactiveerd voordat services op dat knooppunt worden geplaatst.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [Vereist] | De naam van het knooppunt. |
| --deactivering-intent | Beschrijft de intentie of reden voor het deactiveren van het knooppunt. De mogelijke waarden volgen. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-node-enable"></a>sfctl node enable
Activeer een clusterknooppunt servicestructuur dat momenteel is gedeactiveerd.

Hiermee activeert u een clusterknooppunt servicestructuur dat momenteel is gedeactiveerd. Eenmaal geactiveerd, zal het knooppunt opnieuw een levensvatbaar doelwit voor het plaatsen van nieuwe replica's, en alle gedeactiveerde replica's die nog op het knooppunt zal worden gereactiveerd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [Vereist] | De naam van het knooppunt. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-node-get-configuration-overrides"></a>sfctl node get-configuration-overrides sfctl node get-configuration-overrides sfctl node get-configuration-overrides sfc
Hier wordt de lijst met configuratieoverschrijvingen op het opgegeven knooppunt weergegeven.

Deze api maakt het mogelijk om alle bestaande configuratieoverschrijvingen op het opgegeven knooppunt te krijgen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [Vereist] | De naam van het knooppunt. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-node-health"></a>sfctl node gezondheid
Krijgt de gezondheid van een Service Fabric-knooppunt.

Krijgt de gezondheid van een Service Fabric-knooppunt. Gebruik EventsHealthStateFilter om het verzamelen van statusgebeurtenissen die op het knooppunt worden gerapporteerd op basis van de status te filteren. Als het knooppunt dat u opgeeft op naam niet bestaat in het statusarchief, wordt een fout geretourneerd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [Vereist] | De naam van het knooppunt. |
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

## <a name="sfctl-node-info"></a>sfctl node info
Hier vindt u informatie over een specifiek knooppunt in het cluster Servicefabric.

Het antwoord bevat de naam, status, id, status, uptime en andere details over het knooppunt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [Vereist] | De naam van het knooppunt. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-node-list"></a>sfctl node lijst
Hier wordt de lijst met knooppunten weergegeven in het cluster Servicefabric.

Het antwoord bevat de naam, status, id, status, uptime en andere details over de knooppunten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --continuation-token | De parameter vervolgtoken wordt gebruikt om volgende set resultaten te verkrijgen. Een vervolgtoken met een niet-lege waarde wordt opgenomen in de reactie van de API wanneer de resultaten van het systeem niet in één reactie passen. Wanneer deze waarde wordt doorgegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolgtoken geen waarde. De waarde van deze parameter mag niet worden gecodeerd met url's. |
| --max-resultaten | Het maximum aantal resultaten dat moet worden geretourneerd als onderdeel van de paginaquery's. Deze parameter definieert de bovengrens op het aantal geretourneerde resultaten. De geretourneerde resultaten kunnen lager zijn dan de opgegeven maximale resultaten als ze niet in het bericht passen volgens de maximale beperkingen voor de grootte van het bericht die in de configuratie zijn gedefinieerd. Als deze parameter nul is of niet is opgegeven, bevat de paginaquery zoveel mogelijk resultaten die in het retourbericht passen. |
| --node-status-filter | Hiermee u de knooppunten filteren op basis van de NodeStatus. Alleen de knooppunten die overeenkomen met de opgegeven filterwaarde worden geretourneerd. De filterwaarde kan een van de volgende zijn.  Standaardstandaard.\: |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-node-load"></a>sfctl node belasting
Krijgt de laadinformatie van een Service Fabric-knooppunt.

Hiermee haalt u de laadgegevens van een servicefabricknooppunt op voor alle statistieken waarvoor belasting of capaciteit is gedefinieerd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [Vereist] | De naam van het knooppunt. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-node-remove-configuration-overrides"></a>sfctl node remove-configuration-overrides sfctl node remove-configuration-overrides sfctl node remove-configuration-overrides sfc
Hiermee verwijdert u configuratieoverschrijvingen op het opgegeven knooppunt.

Deze api maakt het verwijderen van alle bestaande configuratieoverschrijvingen op opgegeven knooppunt mogelijk.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [Vereist] | De naam van het knooppunt. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-node-remove-state"></a>sfctl-knooppunt verwijderen
Hiermee waarschuwt Service Fabric dat de aanhoudende status op een knooppunt permanent is verwijderd of verloren is gegaan.

Dit houdt in dat het niet mogelijk is om de aanhoudende toestand van dat knooppunt te herstellen. Dit gebeurt meestal als een harde schijf is schoongeveegd, of als een harde schijf crasht. Het knooppunt moet naar beneden zijn om deze bewerking succesvol te laten zijn. Met deze bewerking weet Service Fabric dat de replica's op dat knooppunt niet meer bestaan en dat Service Fabric moet stoppen met wachten tot die replica's weer omhoog komen. Voer deze cmdlet niet uit als de toestand op het knooppunt niet is verwijderd en het knooppunt weer intact kan komen met zijn toestand. Als u begint met Service Fabric 6.5, wijzigt u, om deze API voor seednodes te gebruiken, de seednodes in gewone (niet-seed)-knooppunten en u deze API aanroepen om de knooppuntstatus te verwijderen. Als het cluster op Azure wordt uitgevoerd, probeert Service Fabric het automatisch te wijzigen in een niet-zaadknooppunt nadat het startknooppunt is uitgeschakeld. Om dit mogelijk te maken, moet u ervoor zorgen dat het aantal niet-zaadknooppunten in het primaire knooppunttype niet lager is dan het aantal downseedknooppunten. Voeg indien nodig meer knooppunten toe aan het primaire knooppunttype om dit te bereiken. Als het start-en seed-knooppunt Down naar verwachting niet weer wordt opgehaald met de status intact, verwijdert u het knooppunt uit het cluster, zie https\:-docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [Vereist] | De naam van het knooppunt. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-node-report-health"></a>sfctl node report-health
Hiermee stuurt u een gezondheidsrapport op het servicestructuurknooppunt.

Rapporteert de status van het opgegeven servicestructuurknooppunt. Het rapport moet de informatie bevatten over de bron van het gezondheidsrapport en de eigenschap waarop het wordt gerapporteerd. Het rapport wordt verzonden naar een Service Fabric-gatewayknooppunt, dat doorstuurt naar de gezondheidswinkel. Het rapport kan worden geaccepteerd door de gateway, maar afgewezen door het gezondheidsarchief na extra validatie. Het statusarchief kan het rapport bijvoorbeeld afwijzen vanwege een ongeldige parameter, zoals een verouderd volgnummer. Als u wilt zien of het rapport is toegepast in het gezondheidsarchief, controleert u of het rapport wordt weergegeven in de sectie HealthEvents.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --health-property [Vereist] | De eigenschap van de gezondheidsinformatie. <br><br> Een entiteit kan statusrapporten voor verschillende eigenschappen hebben. De eigenschap is een tekenreeks en geen vaste opsomming om de verslaggever flexibiliteit om de toestand voorwaarde die het rapport triggers categoriseren. Een verslaggever met SourceId "LocalWatchdog" kan bijvoorbeeld de status van de beschikbare schijf op een knooppunt controleren, zodat het de eigenschap 'Beschikbare schijf' op dat knooppunt kan rapporteren. Dezelfde verslaggever kan de verbinding met het knooppunt controleren, zodat hij een eigenschap "Connectiviteit" op hetzelfde knooppunt kan melden. In het gezondheidsarchief worden deze rapporten behandeld als afzonderlijke statusgebeurtenissen voor het opgegeven knooppunt. Samen met de SourceId identificeert het pand op unieke wijze de gezondheidsinformatie. |
| --gezondheidstoestand [Vereist] | Mogelijke waarden\: zijn 'Ongeldig', 'Ok', 'Waarschuwing', 'Fout', 'Onbekend'. |
| --node-name [Vereist] | De naam van het knooppunt. |
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

## <a name="sfctl-node-restart"></a>sfctl node herstart
Start opnieuw een clusterknooppunt servicestructuur opnieuw.

Hiermee start u een clusterknooppunt servicestructuur opnieuw dat al is gestart.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [Vereist] | De naam van het knooppunt. |
| --create-fabric-dump | Geef True op om een dump van het fabricnodeproces te maken. Deze is hoofdlettergevoelig.  Standaard\: false. |
| --node-instance-id | De instantie-id van het doelknooppunt. Als instantie-id is opgegeven, wordt het knooppunt alleen opnieuw gestart als deze overeenkomt met de huidige instantie van het knooppunt. Een standaardwaarde van "0" komt overeen met een instantie-id. De instantie-ID kan worden verkregen met behulp van knooppuntquery's.  Standaard\: 0. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-node-transition"></a>sfctl node overgang
Hiermee start of stopt u een clusterknooppunt.

Hiermee start of stopt u een clusterknooppunt.  Een clusterknooppunt is een proces, niet de os-instantie zelf.  Als u een knooppunt wilt starten, geeft u het in 'Start' door voor de parameter NodeTransitionType. Als u een knooppunt wilt stoppen, geeft u de opdracht in 'Stoppen' voor de parameter NodeTransitionType. Deze API start de bewerking - wanneer de API wordt geretourneerd, is het mogelijk dat het knooppunt nog niet klaar is met de overgang. Bel GetNodeTransitionProgress met dezelfde OperationId om de voortgang van de bewerking te krijgen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-instance-id [Vereist] | De knooppuntinstantie-id van het doelknooppunt. Dit kan worden bepaald via de GetNodeInfo API. |
| --node-name [Vereist] | De naam van het knooppunt. |
| --node-transition-type [Vereist] | Geeft het type overgang aan dat moet worden uitgevoerd.  NodeTransitionType.Start start een gestopt knooppunt. NodeTransitionType.Stop stopt een knooppunt dat omhoog is. |
| --operation-id [Vereist] | Een GUID die een aanroep van deze API identificeert.  Dit wordt doorgegeven aan de bijbehorende GetProgress API. |
| --stop-duration-in-seconden [Vereist] | De duur, in seconden, om het knooppunt gestopt te houden.  De minimumwaarde is 600, het maximum is 14400.  Na afloop van deze tijd komt het knooppunt automatisch weer omhoog. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-node-transition-status"></a>sfctl node transition-status
Hiermee wordt de voortgang van een bewerking gestart met StartNodeTransition.

Hiermee wordt de voortgang van een bewerking gestart met StartNodeTransition met behulp van de meegeleverde OperationId.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [Vereist] | De naam van het knooppunt. |
| --operation-id [Vereist] | Een GUID die een aanroep van deze API identificeert.  Dit wordt doorgegeven aan de bijbehorende GetProgress API. |
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